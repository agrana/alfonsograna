---
layout: post
title: "Zoom Before You Merge"
date: 2026-07-13 00:00:00 -0000
categories: [ai, software-engineering]
tags: [ai-coding, coding-agents, code-review, software-engineering, generative-ai]
excerpt: "An AI editor removed the crowd from a photograph of my girlfriend and me in Berlin. The result looked perfect until I zoomed in and discovered that our faces were gone. AI-generated code and documents deserve the same closer look."
author: "Alfonso Grana"
---

# Zoom Before You Merge

I recently used an AI photo editor on a picture of my girlfriend and me in Berlin.

We were standing in front of a beautiful monument, but the place was crowded. There were people all around us, distracting from what should have been a simple photograph of the two of us.

So I gave the editor a straightforward instruction: remove everyone else.

The result looked great.

The monument was clear. The crowd had disappeared. The composition felt calmer, almost like we had managed to visit Berlin at exactly the right moment.

Then I zoomed in.

The two people in the centre had our clothes and occupied our place in the photograph. From a distance, they looked like us.

They did not have our faces.

The model had successfully cleaned up the background while replacing the two most important people in the image. It produced a convincing version of the photograph, but it failed to preserve the reason the photograph existed.

I have seen the same thing happen with AI-generated code.

## A plausible result is only the first layer

AI-generated work often makes a strong first impression.

The code has sensible filenames. The functions have professional names. The implementation follows a recognizable pattern. The accompanying explanation is confident and easy to read.

At that level, reviewing it can feel like looking at the edited photograph on a phone screen. The crowd is gone. The task appears complete.

The problems become visible when you zoom in.

An authentication check moved to the wrong boundary. An error path disappeared. A library behaves differently from what the model assumed. A configuration value was invented. A migration works for a new database but breaks an existing one. A test confirms the implementation rather than the requirement.

The code still resembles the requested feature. It may compile. It may even pass the tests that were generated alongside it.

But the faces are wrong.

## Every change has something it must preserve

The essential requirement in my Berlin photograph was not simply “remove people.” It was:

> Remove the other people while preserving us.

That second part was implicit to me and negotiable to the model.

Software tasks contain the same kind of implicit requirements.

“Add authentication” also means preserving existing clients, keeping authorization decisions at the correct boundary, and producing useful failure behaviour.

“Refactor this service” also means preserving its public contract, operational characteristics, logs, metrics and edge cases.

“Package this Python tool” also means preserving its configuration model, keeping credentials outside the package, and ensuring it works somewhere other than the developer's checkout.

These are the invariants: the properties that must remain true while the requested transformation happens.

A model can produce an elegant transformation without understanding which invariants carry the identity of the system. That understanding still belongs to the engineer.

## Documents have faces too

The same effect appears in AI-generated documents.

A generated proposal can have a clean structure, polished language and convincing recommendations. Zooming in may reveal that a date is wrong, a source does not support the claim, a product capability was assumed, or a tentative discussion became a firm decision.

The document looks like the thing we asked for. Its shape is familiar and its tone sounds authoritative.

Its identity comes from fidelity to the real people, facts, constraints and decisions behind it.

For code, the face might be a security boundary or API contract. For a document, it might be an exact commitment, a citation, or the distinction between what we know and what we infer.

Fluency helps us create. Fidelity is what makes the result usable.

## The zoom test

I now think of reviewing AI-generated work as a zoom test.

Before accepting the output, ask five questions.

### 1. What must remain unchanged?

Identify the faces before asking the model to edit the picture.

For code, this might include:

- public interfaces
- authorization boundaries
- data integrity
- backward compatibility
- failure behaviour
- performance expectations
- logs, metrics and traces

Writing these constraints into the task improves the generation. Keeping them beside the diff improves the review.

### 2. Where can plausibility hide an error?

Generated code is particularly convincing in familiar shapes: configuration, adapters, API clients, tests and error handling.

These areas deserve attention precisely because they look ordinary. A plausible method call can still target an API that does not exist. A clean test can still assert the model's interpretation rather than the user's requirement.

### 3. What can be checked mechanically?

Use the tools that do not care how polished the output looks:

- tests
- type checking
- linters
- schema validation
- security scanning
- clean-environment installation
- migration tests
- API contract tests

Automation does not replace review. It gives the review sharper eyesight.

### 4. What evidence supports the claim?

When an agent says the implementation works, look for the command and its real output.

When a document makes a factual statement, follow the citation.

When a dependency supposedly supports a capability, read the relevant documentation or exercise the API.

Confidence is part of the interface. Evidence is part of the result.

### 5. Would I recognize this system afterward?

This is the human question.

Does the change still fit the architecture? Can the team operate it? Is the code something we want to debug in six months? Did the generated document preserve the actual decision and the voice of the people making it?

The answer requires context and judgment. Those remain central engineering skills.

## Review is where generation becomes engineering

AI has made exploration remarkably cheap.

I can ask for several implementations, compare approaches and discard weak directions before investing heavily in one. I can draft documentation earlier. I can move from an idea to something testable much faster.

That speed changes the shape of the work.

More of my attention now goes into defining the transformation, naming the invariants, inspecting the result and deciding what deserves to remain in the system.

The model helps create the candidate. Review turns the candidate into a decision.

A merge is a commitment. It says that we understand the change well enough to let other code, other engineers and future decisions depend on it.

That deserves more than a glance at the composition.

## Zoom before you merge

The edited Berlin photograph taught me something useful because its failure was so visible.

At first glance, the model had done exactly what I asked. It removed the crowd and produced a cleaner image.

At full size, it had also removed us.

Generated code and documents can fail in quieter ways. Their structure remains convincing while an important property drifts. The most consequential mistake may be hiding inside the most polished result.

AI gives us powerful ways to transform what already exists. Our responsibility is to know what the transformation must preserve, verify the high-risk details, and keep evidence close to every claim.

Enjoy the speed. Use the leverage. Then zoom in before you merge.
