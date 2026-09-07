---
title: Why I'm writing in public
description: Every piece of advice I've gotten about learning security came with an equal and opposite piece of advice. This is me asking to be corrected out loud.
url: /writing/why-im-writing-in-public
date: 2026-08-15
status: publish
---

# Why I'm writing in public

Every piece of advice I've gotten about learning security came with an equal and opposite piece of advice.

Build a website so people can find you. Don't build a website, nobody reads them, ship projects instead.

Work through the tutorials until the fundamentals stick. Stop doing tutorials, they're a trap, go build something broken and fix it.

Write your Python from scratch or you'll never really learn it. Use the AI and spend your time understanding the system instead of typing it out.

All of that came off Reddit, Discord, and whatever the algorithm decided I should see that week. Plenty of it from people who clearly know more than I do. None of it from anyone who has seen a line of my code.

Security is a community. Everybody says so, and from what I can see they're right. I've been standing in the online version of it, where the advice is constant and aimed at nobody in particular. The in-person kind meets at hours I don't have. I start around 5am, which is when nobody is meeting and everyone worth asking is asleep like a reasonable adult.

So this is me knocking.

If the work sits somewhere visible, somebody can tell me I'm wrong about it. That's the whole idea. I can't sort this much information on my own, and I've stopped pretending the reading list is going to sort itself.

The first real thing here is a writeup of a tool I'm building that reads SSH logs and watches for brute-force attempts. `fail2ban` already does that job, and does it better than I will. I'm building it to understand how it works. The writeup includes the bug I'm least proud of, where the tool quietly stopped seeing anything at all and kept looking perfectly healthy while it did. That's exactly the kind of thing I'd want a stranger to catch before I trusted it with something that mattered.

I'd rather be publicly wrong about that now than privately wrong about it in an interview.

Most of what's in that writeup I worked out at the keyboard and looked up the proper name for afterward. The bug taught me the shape and a search taught me the vocabulary, and I'd sooner put that order on the record myself.

There's one more thing I want to settle here, and it's the one I'm least sure about. I've been leaning on AI to write code I couldn't have written as quickly on my own, and I'm still working out what that's buying me and what it's costing.

I have opinions forming. That one gets its own post.
