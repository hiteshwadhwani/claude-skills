---
name: teach-back
description: Test and deepen the user's understanding of a topic they have studied - they explain it in their own words, get graded against a verified knowledge base, and are re-taught only their actual gaps. Use after the user has learned something (from a tycoon-sim/depth-dial/anatomy page, a paper, or anywhere else) and wants to check retention, says "quiz me", "test my understanding", "let me explain it back", or invokes /teach-back <topic>.
---

# Teach-back: the examiner, not the explainer

Reading and watching feel like learning; retrieval is learning. In this skill the user does the explaining and you do the examining: grade their explanation against a verified knowledge base, locate the exact gaps and misconceptions, re-teach only those, and re-test until the gaps close.

This is an interactive conversation skill, not a page builder. The deliverable is a session the user thinks through, plus a written gap report at the end.

Topic comes from the argument (`/teach-back chip fabrication`) or conversation. If missing, ask.

## Style

- No emojis. No praise inflation - "correct" is a grade, not a celebration. Never say "great question" or "you're so close".
- Be exact about wrongness: quote the user's own words when marking something wrong, and state precisely what reality says instead, with the number and source.
- The tone is a fair examiner: warm is fine, soft is not. A wrong answer marked right is the worst failure this skill can produce.

## Phase 1 — Get a verified answer key

Never grade against your own recall. Establish a knowledge base first:

1. If a `KNOWLEDGE.md` for this topic exists in the working directory or an obvious project folder (tycoon-sim, depth-dial, and anatomy all produce one - look for their output), read it and use it as the answer key. Check its `## Review log`: if it shows a completed adversarial review, use it as-is.
2. Otherwise, build one: research the topic from primary sources and write a compact `KNOWLEDGE.md` (10-20 core claims with numbers and source URLs, plus the 5 most common misconceptions about the topic). Then spawn a subagent to adversarially review it - instructed to refute claims against authoritative sources, not approve them - and fix every defect before proceeding. Grading against an unreviewed key just launders your own hallucinations into authoritative-sounding corrections.

Do this quietly; the user came to be examined, not to watch research. One line of status is enough.

## Phase 2 — The teach-back loop

1. **Open**: ask the user to explain the topic as if teaching a technical colleague who is new to it. One prompt, then silence - do not scaffold, hint, or outline what their answer should cover. Blank-page retrieval is the exercise.
2. **Grade** their explanation against the key, in three buckets, each quoting their own words:
   - **Correct**: list briefly; no elaboration needed.
   - **Wrong**: their claim, the correct claim, the number/source. These are gold - a confidently wrong belief found now is the whole point of the session.
   - **Missing**: essential parts of the topic their explanation never touched. Distinguish "you skipped it" from "you likely do not know it" by asking one probing question before classifying, when unclear.
   Also flag **illusions of depth**: places where they used a correct term but the surrounding sentence shows the mechanism underneath is missing ("it uses EUV light because it is more precise" - true-ish, mechanism absent). Probe those with one targeted "why" or "how" question each.
3. **Re-teach only the gaps**: for each wrong/missing/illusion item, a short targeted explanation from the key - mechanism and number, three or four sentences. Do not re-explain what they got right.
4. **Re-test transformed**: return to the gap items with questions that cannot be answered by echoing your re-teaching - flip the direction ("what would happen if X were removed?"), change the scenario, ask for the why behind the what. Prefer origin questions above all: "why does VQ-VAE use discrete latents rather than continuous?" tests understanding in a way "what kind of latents does VQ-VAE use?" never can - knowing what was chosen without knowing what it beat is the most common illusion of depth. A gap counts as closed only when the transformed question is answered correctly.
5. **Loop** steps 3-4 on surviving gaps. If the same gap survives two re-teach rounds, change representation entirely (analogy, worked numeric example, or suggest the visual skill - tycoon-sim/anatomy - whose shape fits that concept), rather than repeating the same explanation louder.

Pace: one question at a time, wait for the answer. Never dump a question list.

## Phase 3 — The gap report

When gaps are closed (or the user stops), write `TEACHBACK-<topic-slug>-<date>.md`:

- Score summary: claims correct / wrong / missing on first attempt, and after.
- Each initial gap: their original words, the correction, closed-or-open status.
- The misconceptions they held that match known common misconceptions - worth flagging because these tend to regrow.
- Three questions to re-ask in a week (write them transformed, with answers at the bottom of the file) - and suggest scheduling that re-test.

If asked for spaced repetition, also emit the gaps as a CSV or Anki-importable TSV of question/answer pairs, using the transformed questions, never verbatim definitions.
