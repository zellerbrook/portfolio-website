---
title: ssh-detect-respond
description: A tool that reads SSH authentication logs and watches them for brute-force activity. Complete as scoped, with the default threshold derived from 34 days of real traffic.
url: /work/ssh-detect-respond
updated: 2026-09-04
status: publish
repo: https://github.com/zellerbrook/ssh-detect-respond
---

# ssh-detect-respond

A tool that reads SSH authentication logs and watches them for brute-force activity.

`fail2ban` already does this and does it better than I will. I built it to understand how it works, and to find out what the questions look like from the inside.

Status: complete as scoped. Milestones 1 through 3 are done and tested, 20 tests. Milestone 9, the threshold justification, is done as analysis. Milestones 4 through 8 are cut on purpose, and `SCOPE.md` in the repo says why.

[Source on GitHub](https://github.com/zellerbrook/ssh-detect-respond)

## What it reads

Ubuntu writes a line to `/var/log/auth.log` every time someone tries to log in over SSH. Plain text, one line per attempt:

```
Nov 12 09:15:22 target-vm sshd[2145]: Failed password for admin from 192.168.56.101 port 52344 ssh2
```

Four things in there matter: when it happened, what IP it came from, what account was being tried, and whether it worked. The rest is noise, and most of the file is noise anyway. sudo, cron, every other service with something to say.

The first thing I got wrong was assuming my own machine had that file. It doesn't. I run CachyOS, which is Arch-based, and Arch logs to the systemd journal. `auth.log` is a Debian and Ubuntu convention. So before writing a line of code I had to work out that the machine I write on and the machine this runs on don't keep records the same way. I built against a saved sample instead.

## What it does

**Milestone 1, the parser.** Takes one line of text, decides whether it's a login attempt, and pulls those four fields into a small record. Anything else gets thrown away. It writes down facts, one line at a time, and it doesn't form an opinion about them.

**Milestone 2, the tail.** Watches the file grow the way `tail -f` does, starting at the end so a restart doesn't re-report last week's traffic as if it were happening now. It survives log rotation, which is the part that broke first and is written up below.

**Milestone 3, the detector.** Counts failed passwords per source IP inside a sliding time window and reports a detection the first time an IP crosses the threshold. It evicts old events against the timestamp on the event it's processing rather than the clock, so replaying a saved log gives the same answer as watching a live one, and the tool stays correct if it falls behind on a busy machine.

It counts failed passwords only, though the parser recognizes 5 kinds of sshd event. Feeding all 5 into the threshold would roughly triple the totals and the number would stop meaning anything.

## Where 3 in 600 came from

The default is 3 failed passwords from one address inside 600 seconds. I could have picked that out of the air. Instead I took 34 days of auth logs off a VPS I ran, 1,233,435 lines, and replayed the whole capture through the real detector at 20 settings: thresholds of 2, 3, 5, 10 and 20, against windows of 60, 300, 600 and 3600 seconds.

1,914 distinct source addresses in there. 1,878 of them failed a password at least once. At 3 in 600, 1,481 of those would have been blocked, and 397 never crossed the line at all. That last number is the one I'd have to answer for in a review. It's the traffic this control misses, and now it has a figure behind it instead of a shrug.

Then the caveat that matters more than any of the above. That host recorded exactly 1 successful SSH login in 34 days, because in practice it was key-only. So every false-positive number in the sweep is 1 divided by something large. Those are counts, and I won't dress them up as rates.

The single false positive is me.

```
08-07 01:46:59  Failed password for invalid user zach from 172.58.x.x
08-07 01:47:10  Failed password for invalid user zach from 172.58.x.x
08-07 01:48:07  Failed password for invalid user zach from 172.58.x.x
   (+2 more, second session, through 01:48:54)
08-08 00:46:40  Accepted publickey for root from 172.58.x.x  (ED25519)
```

Wrong username, 5 failures in about 2 minutes from a T-Mobile carrier address, then a correct key login from the same IP the next day. At 3 in 600 the detector blocks that address, and it's right to. Five failures from one source in two minutes is the brute-force pattern. My behavior was indistinguishable from an attacker's, and the detector reads behavior and has no access to intent.

So the fix is an exception list. Raising the threshold to 10 would have spared me and handed every real attacker 7 more guesses.

That's the whole argument for milestone 4, and I got it from measurement rather than assumption. Milestone 4 is still cut.

## What the capture doesn't get published

The 34 days of logs are not in the repo and never have been. They contain 1,914 real source addresses, attempted usernames, a key fingerprint, and one of my own carrier IPs. Findings get published, the evidence stays local.

Regenerating the traffic synthetically would have been easy and would have made the one claim worth making false.

## What broke

### A username with a space in it

Doesn't sound like much until you sit with how literal a program is. When someone tries to log into an account that doesn't exist, the log says `for invalid user admin from` where I expected `for admin from`. My pattern was written to grab one word, so it matched nothing. The code assumed a match every time, so what I got back was:

```
AttributeError: 'NoneType' object has no attribute 'group'
```

Which means "your pattern found nothing, and then you tried to use the nothing."

### Log rotation, which is the good one

Linux doesn't let log files grow forever. Once a day it renames `auth.log` out of the way and starts a fresh empty one. My tool kept reading the old renamed file, because it doesn't follow the name. It follows the actual file underneath.

No crash. No error. No warning. It quietly stopped seeing anything, forever, while looking completely fine.

That one stuck with me. A tool that crashes tells you it's broken. A tool that goes blind lets you keep trusting it. A security tool that goes blind is worse than no tool at all, because you assume you're covered while the attempts pile up unseen.

The fix is checking whether the file sitting at that path is still the same file I opened, and reopening if it isn't. There's a second flavor, where the file gets emptied in place instead of renamed, and that has its own check and its own test.

### A red test that wasn't the code's fault

A test failed and I assumed I'd broken something. I hadn't. My expectation was wrong. I'd written down what I thought the answer should be, and the code was right.

Small thing. But the reflex I want on the job is to work out which side is wrong before changing anything. Don't reflexively fix working code. Don't delete the test.

### The unglamorous ones

I spent several minutes debugging output that made no sense before noticing I hadn't saved the file. I typed `test/` instead of `tests/`. I worked on this across 8 sessions before noticing the repo had no remote, so every commit lived on one desktop and nowhere else.

## Why I stopped

Milestones 4 through 8 were the allowlist, the firewall response, escalation and auto-expiry, alerting, and JSON output. That's the part fail2ban and CrowdSec already do, and do better. The problems I hadn't seen before were all in the first 3: what happens to a tailer when the log rotates underneath it, and how you evict events from a sliding window so the answer holds up when the process falls behind. By the end of milestone 3 I was past those, and everything after was plumbing I'd have written worse than the tools that already exist.

The second reason is the useful one, and it's why there's a `SCOPE.md` instead of a repo that just stopped getting commits.

I built this in 30 to 60 minute sessions around a full-time job and 4 kids. Every milestone was several sessions of work with nothing finished in between, so there was never a good place to start and never a good place to stop. It got put off for weeks at a time, and while it sat at the top of my list it stopped everything behind it moving too.

The rule I took out of it: the unit of work has to match the unit of output. If a session can't end with something finished and committed, the scope is wrong, however good the idea is.

That's a scoping mistake rather than a discipline one, and I'd rather write it down than let the repo sit there implying I lost interest.

## Still broken

I'd rather list these than sound finished.

- `parse_line` assumes every pattern matches. A line shaped differently than expected raises `AttributeError` instead of getting skipped. The username bug found this. The fix isn't in.
- The timestamp pattern misses single-digit days. Syslog pads them with two spaces (`Nov  2`) and my pattern expects one.
- The parser has never tailed a live internet-facing server. The 34-day capture was replayed off disk.
- The detector keeps state in memory only. Restart it and it forgets every address it was tracking, so an attacker patient enough to wait one out starts from zero.
- The threshold comes from a host that's since been decommissioned. The machine this would deploy to has a different exposure profile and no comparable capture, so that sweep would need running again after 30 days of data there.

One more, and it's a limit rather than a bug. The repo is called ssh-detect-respond and it doesn't respond. Cutting milestones 4 through 8 left a detective control: it reads logs, recognizes a pattern, and reports it. On a host that's effectively key-only, what it delivers is log hygiene and evidence.
