---
layout: post
title: "Findings in agentic coding"
date: 2026-06-16 00:00:00 -0000
categories: [ai, software-engineering]
tags: [cursor, ai-coding, coding-agents, software-engineering, workflow]
excerpt: "After starting 3.5K Cursor agents and using 1.15B tokens, my main lesson is not that AI replaces engineering judgment. It makes writing clearly, reading carefully, and choosing what to keep more important."
author: "Alfonso Grana"
---


I started 3.5K agents and consumed 1.15 billion tokens in a couple of months ending 2025.

It made me think about how much my workflow has changed.

After reflectinb aout it a few things stand out.

## Writing clearly matters more than ever

If the problem statement is vague, the output gets long.

When the input is ambiguous, the model will try to cover cases that you don't need. It adds explanations, branches, abstractions, configuration, fallback behavior, that you don't need.

The work starts before the prompt is sent.

It is worth spending time writing specs that are unambiguous and straight to the point. What should change? What should stay the same? What constraints matter? What should the agent avoid touching?

At the end of the day, dealing with ambiguity is a big part of what makes an engineer good anyway.

## Be ready to read a lot

Reading AI generated output is not the most fun part of the job, but it is necessary.

Agents can generate a lot of code quickly. Some of it is useful. Some of it is unnecessary. Some of it is old thinking left behind after you changed direction halfway through.

That last part happens more often than I expected.

You start with one approach, realize it is not quite right, and ask the agent to move in another direction. Unless you explicitly ask it to remove the previous attempt, it may leave pieces behind. Extra helpers. Unused types. A half-relevant abstraction. Tests for behavior that no longer exists.

Agent rules help, but they are not enough on their own.

You still have to read. You still have to check whether the code is brief enough, boring enough, and maintainable enough for the next person to understand. Or, increasingly, for the next agent to work with.

The strange thing is that throughput goes up, but so does the amount of code you need to review.

Before, the bottleneck was often typing or searching or remembering some syntax. Now the bottleneck is more often judgment. Is this the right shape? Is this too much? Did we leave anything behind? Would I want to debug this in three months?

AI makes it easier to create code. But most of the code it produces is less worth keeping in general.

## Exploration is cheaper now

This is the part I like most.

Exploring options is much cheaper than it used to be.

You can ask for three different approaches. You can try the smallest viable version of each. You can see one working, dislike the shape of another, and throw it away without feeling like you wasted half a day.

That changes how I think about early implementation work.

Before, I would sometimes overthink the first approach because changing direction later felt expensive. Now I am more willing to test a path quickly. If it feels wrong, I can start again. By then I understand the problem better, and I know what to ask for.

That does not mean every prototype should become production code. Actually, the opposite.

The cheaper exploration gets, the more important it is to separate exploration from commitment. Trying something is cheap. Keeping it is the decision that matters.

## The work moved

Using more AI moved the work into different places.

- Taks definitions. Prompting
- Reviewing AI generated output code, documents. 
- Verification that the specification is implmented correctly. 