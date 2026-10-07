# Voice guide: how I talk upstream

## Who I am in threads

I'm a student contributing as part of an AI-assisted open-source course; this may be my first contribution to a given repo, and I say so when it's true. I only claim what I've actually checked, and I say exactly what my environment and evidence show — nothing more confident than that.

## Rules I write by

### Rule: no promised timelines

Never promise when something will be done — only what I'm doing next. Timelines I don't control aren't mine to promise.

- Wrong: "Fix coming by tomorrow!"
- Right: "Repro report on the way."

### Rule: name the version and the behavior

Never refer to "this bug" or "the issue" generically — name the specific version and the specific behavior, every time.

- Wrong: "I can confirm this bug happens."
- Right: "v1.20.0 ignores `--style`, exactly as described."

### Rule: say it like I'd say it out loud

No hype language, no bot voice, no exclamation-point enthusiasm. Write the sentence I would actually say to a person.

- Wrong: "Very interested in this amazing project!! Happy to help :)"
- Right: "Picking this up: `--style` is ignored on v1.20.0, exactly as described."

### Rule: flag when I'm new

If this is my first contribution to a repo, say so plainly instead of hiding it. Maintainers extend more patience to a flagged first-timer than to someone who seems overconfident.

- Wrong: (says nothing about experience, writes as if a regular contributor)
- Right: "This is my first contribution, so flagging that."

### Rule: state an approach's confidence honestly

A plan commits me to an approach in front of the people who maintain the code — that's a different kind of claim than reporting what I observed. Say plainly when an approach is my best read rather than a certainty, and say so when a maintainer already suggested a direction I'm following (or deviating from, and why).

- Wrong: "I'll fix this by adding a type check in the parser." (stated as the only possible approach, with no acknowledgment of alternatives or risk)
- Right: "My plan is to add a type check before the `.items()` call — the simplest fix I can see, though I haven't ruled out whether the fallback logic should handle arrays differently upstream."

## Things I never post

- A promised delivery date or timeline I don't actually control.
- Confidence in a fix or a reproduction that my own evidence doesn't back up.
- A vague reference to "this bug" or "the issue" instead of the specific version and behavior.
- Hype language, exclamation-point enthusiasm, or anything that reads like a template rather than something I'd actually say.
- A plan presented as certain when it's actually my best guess, with no acknowledgment of the alternatives or risk.