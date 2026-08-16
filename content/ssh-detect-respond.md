---
title: ssh-detect-respond
description: A tool that reads SSH authentication logs and watches them for brute-force activity. Milestones 1 and 2 done, with the bugs written down.
url: /work/ssh-detect-respond
updated: 2026-08-15
status: publish
repo: https://github.com/zellerbrook/ssh-detect-respond
---

# ssh-detect-respond

A tool that reads SSH authentication logs and watches them for brute-force activity.

`fail2ban` already does this, and does it better than I will. I'm building it to understand how it works, and someday to put mine next to it and see how far off I am.

Status: milestones 1 and 2 done. Milestone 3 picks back up once this site is finished.

[Source on GitHub](https://github.com/zellerbrook/ssh-detect-respond)

## What it reads

Ubuntu writes a line to `/var/log/auth.log` every time someone tries to log in over SSH. Plain text, one line per attempt:

```
Nov 12 09:15:22 target-vm sshd[2145]: Failed password for admin from 192.168.56.101 port 52344 ssh2
```

Four things in there matter: when it happened, what IP it came from, what account was being tried, and whether it worked. The rest is noise, and most of the file is noise anyway. sudo, cron, every other service with something to say.

The first thing I got wrong was assuming my own machine had that file. It doesn't. I run CachyOS, which is Arch-based, and Arch logs to the systemd journal. `auth.log` is a Debian and Ubuntu convention. So before writing a line of code I had to work out that the machine I write on and the machine this runs on don't keep records the same way. I built against a saved sample instead.

## What it does

Two pieces so far.

**Milestone 1, the parser.** Takes one line of text, decides whether it's a login attempt, and pulls those four fields into a small record. Anything else gets thrown away. That's the whole job. It writes down facts, one line at a time, and it doesn't form an opinion about them.

**Milestone 2, the tail.** Rather than reading the file once and quitting, it sits and watches the file grow, the way `tail -f` does. A new line shows up about a second later, no restart. It starts at the end of the file, so restarting the tool doesn't make it re-report last week's traffic as if it were happening now.

Opinions are milestone 3. That's where it starts counting failures and deciding something is an attack.

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

The fix is checking whether the file sitting at that path is still the same file I opened, and reopening if it isn't. There's a second flavor, where the file gets emptied in place instead of renamed, and that needs its own check.

### A red test that wasn't the code's fault

A test failed and I assumed I'd broken something. I hadn't. My expectation was wrong. I'd written down what I thought the answer should be, and the code was right.

Small thing. But the reflex I want on the job is to work out which side is wrong before changing anything. Don't reflexively fix working code. Don't delete the test.

### The unglamorous ones

I spent several minutes debugging output that made no sense before noticing I hadn't saved the file. I typed `test/` instead of `tests/`. I worked on this across 8 sessions before noticing the repo had no remote, so every commit lived on one desktop and nowhere else.

## Still broken

I'd rather list these than sound finished.

- `parse_line` assumes every pattern matches. A line shaped differently than expected raises `AttributeError` instead of getting skipped. The username bug found this. The fix isn't in yet.
- The timestamp pattern misses single-digit days. Syslog pads them with two spaces (`Nov  2`) and my pattern expects one.
- The parser has never seen a log from a real internet-facing server. Everything so far is a saved sample.

## What's next

Milestone 3: counting failures inside a time window and deciding when that count is an attack. Then deciding what to do about it.
