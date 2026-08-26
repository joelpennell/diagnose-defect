# diagnose-defect

A tool for turning "this sometimes doesn't work and nobody knows why" into a diagnosis with a
tested fix.

You give it the discussion where people described the problem, plus access to the code. It
works out what is actually being asked, finds the mechanism, proves it by running code rather
than by reasoning about it, counts how many other cases are affected, proposes a fix, and runs a
before-and-after test showing the fix works and does not break what the original code intended.

It runs once, start to finish, with nobody answering questions for it.

## If it helps you, tell me what it missed

That is the only thing asked in return, and it is genuinely the useful part. Two questions:

1. **What did it get wrong or miss?** A confident wrong answer is the failure mode that matters,
   and the only way to find the next one is for someone who knows the system to say so.
2. **Would you want this running all the time, or only when something breaks?** Nobody outside
   the people who built it has answered that yet.

Either answer, however short, to **hello@joelpennell.com**. "It missed X" with no explanation is
more useful than nothing.

## What you need

- **Claude Code**, with permission to read the repositories and run commands in them.
- **A frontier-class model.** It has only been run on Claude Opus. Cheaper models are untested
  here, and on comparable analysis work they produced confidently wrong statements — including
  misreadings of code that was sitting in front of them. A confident wrong answer is the
  expensive failure with this kind of tool, so it is not the place to economise.
- **Read access to the source.** Clone or check out whatever is relevant, including any
  replacement system or internal notes if they exist. More context is better; it is told to
  treat other people's conclusions as claims to check rather than facts.
- **A language runtime for the code in question**, if you want it to execute rather than only
  read. It will do considerably better with one.

## Before you run it

It runs unattended for around twenty minutes and spends model usage on **your own** account. It
will execute code and stand up sandboxes as part of investigating, so run it somewhere you are
comfortable with that. It is told to say what it is about to do and wait for a yes before the
expensive steps, but that is an instruction to the model, not an enforced limit — if you run
Claude Code with permission prompts turned off, nothing will stop it.

## How to run it

1. Install the skill — see `INSTALL.md`. It is one `cp` into `~/.claude/skills/`.
2. Put the repositories somewhere together, for example `~/work/thing/`.
3. Open Claude Code in that directory.
4. Paste the thread and say what you want:

   > Use the diagnose-defect skill. Here is the thread describing the problem:
   >
   > [paste the whole thing, verbatim, including the parts that look irrelevant]
   >
   > The code is in ./repo-a and ./repo-b. Write the result to DIAGNOSIS.md.

Paste the discussion **verbatim**. Do not summarise it and do not strip the parts that look like
noise or like wrong turns. Working out which leads are wrong is part of the job, and a
participant's own theory about the cause is often the most misleading thing in the thread.

## What it costs

One measured run on a real defect in an unfamiliar codebase took 21 minutes and $6.59 of model
usage, including standing up a sandbox and running the code. Cost scales with how much source it
has to read.

## What it is good at

Defects where several instances have been observed and at least one comparable case worked.
That contrast is what it uses to isolate the mechanism. "Three of these failed and one didn't"
is close to an ideal input.

## What it is not

It is not a replacement for someone who knows the system. It produces a claim with the evidence
attached and the checks that would disprove it, so that a maintainer can confirm or kill it in a
few minutes. Every claim is labelled by whether it is provable from the code, needs runtime data
the code refers to but does not contain, or is outside the code entirely.

It will tell you when it cannot explain something rather than inventing a mechanism that fits.
Treat a decline as a real answer, and read the check it names.

It can still be confidently wrong. It has missed a real defect before — in a field it left alone
and reported as unaffected. Read the "judgment calls made without a human" section at the end
before you act on anything.

## What it leaves behind

It is told to leave two things in the folder you ran it from:

    DIAGNOSIS.md          <- the answer. This is the file you read and forward.
    diagnosis-workings/   <- everything it built to get there: sandboxes, scripts,
                             captured before-and-after output. Keep it, ignore it.

`DIAGNOSIS.md` is told to open with a block saying what it was given, which repositories it read,
and that everything in the file was written by that run. That block is there so that if you pass the file
to someone, you never have to remember or explain where any of it came from. Point at the block.

It is told not to modify the repositories you point it at, and to copy anything it needs to change.

## Reading the output

Start with the plain-English summary, then the mechanism, then the before-and-after test. The
"judgment calls made without a human" section at the end is worth reading before you act on
anything: it lists what the run assumed and what would collapse each assumption.
