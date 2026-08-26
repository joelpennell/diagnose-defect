---
name: diagnose-defect
description: Diagnose a reported software defect - a bug, regression, intermittent or flaky failure - from a discussion thread plus source access. Produces the root-cause mechanism anchored to file:line, proof by execution, blast radius, a fix, and a before-and-after regression test. Use when someone reports "X sometimes doesn't happen", asks for a root cause, and nobody has isolated why. This is a long, execution-heavy investigation that costs real time and model usage: ask the user before starting it. Do not trigger on a passing mention of a bug.
---

# Diagnose a reported defect

*diagnose-defect v1.0 — 2026-08-23. By Joel Pennell, joelpennell.com. MIT licensed.*
*If you run this, I'd like to know what it got wrong: hello@joelpennell.com*

You are given a record of humans describing a problem (a chat thread, a ticket, an email) and
access to the source. Work out what is being asked, then answer it. Once you start, run to the
end without stalling: assume nobody will answer questions for you, so make your own judgment
calls and record them.

## Before you start

This is a long procedure and it executes code. If a person is there to ask, say in a sentence or
two what you are about to do — read the source, stand up a sandbox, run the code, roughly twenty
minutes and a few dollars of their model usage — and wait for a yes before the first expensive
step. Reading a file to see whether the request even fits this skill is not an expensive step;
installing dependencies, building sandboxes and executing code are.

You already have your yes, and should just proceed, if you were launched unattended, or if the
person asked for a diagnosis and handed you the thread and the source. Ask when this skill
triggered on its own from a passing mention rather than a request.

## What you produce

A deliverable a maintainer who knows this code can act on in ten minutes.

1. **A summary a non-programmer can follow.** What is broken, what it causes, what the fix is.
   Three short paragraphs before any code.
2. **The mechanism.** Anchored to `file:line`, with the code quoted. Not a narrative.
3. **Case accounting.** The report usually contains several instances and often a case that
   worked. Your mechanism must account for each one, or you must say explicitly which cases it
   does not cover.
4. **Proof by execution.** Do not stop at reading the code. Extract the relevant path and run
   it, or stand up a sandbox and run the real thing. Show what you ran and what it printed.
   Reproducing the reported behaviour is the difference between a theory and a finding.
5. **Blast radius.** How many other instances of this pattern exist. Quantify it from the
   configuration or the code, not by estimate.
6. **A fix, plus a before-and-after test you actually ran.** It must fix the reported cases,
   preserve whatever the original code was trying to do, and leave adjacent behaviour correct.
   Two traps here, both common:
   - A fix that merely disables a check is wrong. Include a test case that would catch that
     mistake — build the tempting one-line fix as a second variant and show what it breaks.
   - **Unchanged is not the same as right.** If you leave a neighbouring behaviour alone,
     check it against your independent reference, not just against the version you started
     from. A before-and-after test answers "did I change this?" and cannot answer "was this
     ever correct?". Treating an unchanged output as a verified one will certify an existing
     defect as safe.
7. **Falsification.** For every load-bearing claim, the cheapest check that would prove you
   wrong. Run the ones you can reach. Name the ones you cannot and say exactly where they live.
8. **Judgment calls made without a human.** List the assumptions you made and what would
   collapse each one.

## Rules

- **Mark every claim by tier.** (a) provable from the code, (b) needs data the code references
  but does not contain, (c) outside the code entirely. Readers act differently on each.
- **Participants state hypotheses, not facts.** Note what you concluded the question was and
  which leads in the thread you judged to be wrong. The reporter's own theory about the cause is
  the most common wrong lead, and it is usually the first thing in the thread.
- **Comments, commit messages and internal write-ups are evidence of intent only.** Mechanism
  comes from the code.
- **Where you rely on someone's notes rather than the code, say so at that claim.**
- **Decline rather than stretch.** If a case is not covered, say so plainly and name the check
  that would settle it. A clean "not determinable from here, look at X" is a correct answer.
  Fitting a mechanism to a case it does not fit is the worst thing you can do here, because it
  is confident and wrong and it sends someone down a dead end.
- **Be skeptical of prior investigations.** They may be right, wrong, or right for the wrong
  reason. Check their reasoning where it bears on yours, and say if a stated rule-out does not
  hold.

## Where to put what you produce

Leave the working directory in a state where someone who was not here can tell what is what.

- **The deliverable goes in `DIAGNOSIS.md`** in the directory you were run from, unless you were
  told otherwise. One file. This is the thing a person reads.
- **Anything you build in order to investigate** — sandboxes, harness scripts, extracted code,
  captured before-and-after output — goes in a `diagnosis-workings/` directory beside it. Do not
  scatter scratch files into the working directory or into the repositories you were given.
- **Never modify the source repositories.** Copy what you need into your workings directory. The
  person running you may not notice an edit until much later.
- **Open `DIAGNOSIS.md` with a provenance block** before anything else, stating: the date, what
  you were given as input, which repositories you read, one sentence saying that everything
  in the file was written by this run, and the line **"Method: diagnose-defect v1.0"** so that a
  reader who did not run it knows where the approach came from. If you were handed a conclusion by someone rather than
  finding it yourself, say which parts those were.

That last point matters more than it looks. This output is likely to be forwarded to people who
were not present and cannot evaluate it, by someone who cannot evaluate it either. If the file
does not say plainly where each part came from, the person forwarding it ends up making claims
they cannot support. Write it so nobody has to remember anything.

## Inputs to ask for if they are missing

The thread or ticket, verbatim rather than summarised. Read access to the source. Permission to
execute code. Anything the team has already written about it, clearly separated from the source.
