# Installing this skill

A Claude Code "skill" is just a folder with a `SKILL.md` inside it. Installing one means putting
that folder in the right place. There is nothing to compile and no account to connect.

## Step 1 — check you have Claude Code

In a terminal:

    claude --version

If that prints a version, you are set. If it says command not found, this skill needs Claude
Code and won't work without it. (There is a weaker fallback at the bottom of this file.)

## Step 2 — install the skill

From inside the unzipped or cloned folder (the one containing this file), copy the inner
`diagnose-defect` folder into your personal skills directory:

    mkdir -p ~/.claude/skills
    cp -r diagnose-defect ~/.claude/skills/

That makes it available in every project on your machine. To check it landed:

    ls ~/.claude/skills/diagnose-defect/SKILL.md

## Step 3 — put the code somewhere together

Make a folder and clone or copy in every repository that might be involved, including any
replacement system and any internal notes or reports. More is better.

    mkdir -p ~/work/investigation
    cd ~/work/investigation
    # copy or clone the relevant repos in here

## Step 4 — run it

From inside that folder:

    claude

Then type a message like this, pasting the discussion in full:

    Use the diagnose-defect skill.

    Here is the thread describing the problem:

    [paste the entire thread here, verbatim]

    The code is in ./repo-name. Write the result to DIAGNOSIS.md.

It will ask permission to run commands. Say yes: running the code is where most of the value
comes from. It will take about twenty minutes and will work without further input.

## If you don't have Claude Code

You can paste `SKILL.md` and the thread into Claude on the web, with the repositories attached
as project files. It will still reason about the code, but it cannot execute anything, which is
where a large part of the value came from in testing. Treat that as a weaker version.
