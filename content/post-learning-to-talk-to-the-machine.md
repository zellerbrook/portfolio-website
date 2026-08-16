---
title: Learning to talk to the machine
description: I told the AI to make me explain every line before it shipped. It did exactly that, and the two things that deserved the scrutiny got buried in the noise.
url: /writing/learning-to-talk-to-the-machine
date: TBD
status: draft
---

# Learning to talk to the machine

Before I wrote any code for the SSH detector, I gave Claude a rule. Explain everything, don't race ahead, and if I can't explain a line, it doesn't ship.

I still think that was the right instinct. I've read enough about people assembling a working project out of pasted answers and coming away unable to say what they built. I didn't want a repo I couldn't defend.

What I got was an interrogation.

Every line came with a question. The lines where something was actually decided, sure. Also the line that checks whether a string contains a space, and the line that imports the thing the next line obviously needs. I'd answer, it would confirm, we'd move down a row.

A few sessions in I worked out what that was costing me. When everything gets a question, nothing gets a question.

Two things in that project genuinely deserved to be picked apart. The log rotation bug, where the tool quietly stopped reading anything and went right on looking healthy. And my own assumptions about the log format, which were wrong in a way I'd have caught sooner if I'd been made to say them out loud.

Both of those showed up in the same tone as "and what does this line do?" They didn't stand out, because by then nothing stood out.

So I changed the instructions. Narrate the mechanics directly and skip the quiz. Save the hard questions for the design decisions and the debugging, which are the parts that are actually mine.

The original rule still earns its keep, and I can point at where. My editor inserted an import I never wrote and couldn't account for. A week earlier I'd have been answering questions on autopilot and might have let it ride. Instead it stood out immediately, because it was the only thing on screen I couldn't explain. I deleted it.

Where that leaves me is worth being plain about. Claude writes a good deal of the code and leaves comments explaining what it did. I read it, I follow it, and I can tell you why the file is shaped the way it is. I can't always tell you I would have produced that exact line by myself.

Whether that reads as resourceful or as a problem, I'll find out.

My working answer, for now. What's worth defending is the shape of the file. What it reads, what it throws away, and what it does when the thing it's watching disappears out from under it. Most lines aren't decisions. The ones that are, I want to have made.
