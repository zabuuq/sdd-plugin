# SDD Plugin

This is the Spec-Driven Development plugin for Claude Code.

When any `/sdd:*` command is invoked, load the shared behavior core at `skills/sdd-guide/SKILL.md` before executing the command skill. The sdd-guide core defines tone, interaction rules, guard rails, and the command chain.

All behavioral detail lives in sdd-guide and the individual command skills. This file is a loader, not a behavior definition.
