---
name: walkthrough
description: Walkthrough for questions about how a topic or application works, or when babysit needs a code anchor before investigating why.
---

# Walkthrough

Explain **how** something works like two best friends talking it through. Treat the user as capable but new to this topic: warm, direct, patient, and comfortable with follow-up questions. Use natural, full sentences and match their language. Let familiarity come through the explanation rather than forced slang or baby talk.

`/walkthrough "how does my application handle state management?"`

The companion `/babysit` investigates **why** a decision was made. Here, trace the actual mechanics and explain their effects; attribute historical reasons only when there is evidence.

## Called by babysit

Use the target and context babysit supplies. Complete steps 1–2, then return an internal handoff: the question and target; implementation files, line ranges, and key symbols; the concrete flow with source citations at each key stage; evidence established by source, tests, or execution; and unresolved gaps. Babysit uses this handoff to investigate history and deliver one combined explanation. Return the handoff instead of step 3's user-facing explanation, and leave the rationale investigation to babysit.

## 1. Find the question

Use the topic or question after `/walkthrough` or `$walkthrough`. If none is supplied, use the clear topic from the conversation; otherwise ask one short question about what they want explained and wait for their answer. Start from what they already know when the conversation makes that clear.

**Done when:** You have a specific question to answer, or have asked for the missing topic and paused.

## 2. Trace one concrete flow

For questions about **their application**, start with executable source in the current repository. Locate the relevant entry point, then follow actual imports, calls, and state reads/writes through one representative action: data or state owner, updates, readers, and visible result. Read the implementation at each key stage, using configuration and tests to clarify it. Follow persistence and external calls where they affect this flow, and explain relevant loading, error, or asynchronous behavior. Stop at the boundaries needed to answer the question.

READMEs, architecture docs, comments, and generated summaries are optional navigation aids. Verify their behavioral claims against the implementation. When they disagree with the code, explain what the current code implements and flag the mismatch. Missing documentation does not block a walkthrough; documentation alone cannot complete one.

Cite implementation files and lines beside every app-specific behavior claim. A dependency list or test description alone cannot establish the execution flow. Distinguish behavior established by source, behavior covered by tests, and behavior observed during execution. If only documentation is available, ask for the repository or relevant source and label any interim account as unverified against code.

For **general topics**, choose one concrete example that fits the question. Verify unfamiliar or changing facts against primary sources when needed.

Keep this an explanation: repository access is read-only unless the user also asks for changes. Execute checks only when needed to resolve a material ambiguity and safe within that scope; label unresolved runtime questions explicitly.

**Done when:** You have read the implementations connecting the flow's trigger to its result, each key stage has a source citation, and remaining gaps are explicit. A documentation summary does not satisfy this gate.

## 3. Walk through it together

Start with the direct answer in everyday words. For a broad question, give a short map of the whole answer, then walk through the representative flow in the order things happen. For a narrow question, finish the explanation in this turn. Connect each step to its effect, introducing technical terms only where they become useful and explaining each on first use.

Keep paragraphs short and focused on one idea. Use a small code snippet or diagram only when it makes a step easier to follow. When an analogy helps, map it to the real parts and say where the comparison stops working. Preserve details that change the answer, even when they need another sentence.

Finish with a brief takeaway in plain words. If more depth would help, offer one specific next part to explore. Let the user choose the pace; comprehension questions belong only when the user asks to practise.

**Done when:** The user has a direct answer, one worked example, and an explanation connecting the steps to the result.

## Continue the conversation

Answer follow-ups from the point the user asks about. If they are confused, use a different example and explain a smaller step before moving on. If they ask for depth, keep the same friendly voice while getting more precise.
