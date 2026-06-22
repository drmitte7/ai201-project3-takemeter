# TakeMeter — Planning Document

---

## Community

I chose r/soccer with a focus on World Cup discussion posts. This community is a strong fit for a classification task because the discourse varies enormously — the same match can generate detailed tactical breakdowns, bold controversial opinions, and pure emotional outbursts all within minutes of each other. The World Cup focus keeps the topic consistent while providing a large volume of varied posts to annotate.

---

## Labels

### analysis
A post that makes a structured argument using statistics, tactical observation, historical comparison, or specific match details. The evidence is specific and verifiable, and the post is genuinely reasoning from it rather than just asserting a conclusion.

Examples:
- "Morocco's low block was incredibly effective — they allowed only 0.8 xG per game in the group stage, the lowest of any team in the tournament. Their defensive shape neutralizes wide attacks completely."
- "Argentina's pressing stats in the final were elite — 47 PPDA, meaning they allowed opponents only 4.7 passes per defensive action. That's what won them the midfield battle."

### hot_take
A bold, confident opinion stated without supporting evidence. The claim might be true but the post asserts rather than argues. Often provocative or deliberately controversial.

Examples:
- "Messi is not the GOAT. He never performed at World Cups until he got lucky with an aging squad. Ronaldo would have won this tournament."
- "France is the most overrated team in World Cup history. They always underperform relative to their talent."

### reaction
An immediate emotional response to a specific goal, match, or moment. Little to no argument — the post is expressing a feeling in the moment.

Examples:
- "MBAPPÉ WHAT A GOAL I CANNOT BREATHE THIS MAN IS INSANE "
- "I am shaking. I cannot believe what just happened. That penalty shootout was the most stressful thing I have ever watched."

---

## Hard Edge Cases

**Ambiguous case:** A post that mixes emotion with one or two stats:
"France deserved to lose. They had 34% possession and only 3 shots on target — terrible performance."

**Decision rule:** If the post is primarily expressing an emotional judgment and the stats are decoration to justify the feeling, label it `reaction`. If the post is genuinely reasoning from the stats to build an argument, label it `analysis`. Frustration-driven posts that cite one or two stats to vent = `reaction`. A post needs at least 2-3 specific pieces of evidence and a structured argument to qualify as `analysis`.

**Second ambiguous case:** A bold opinion that cites one cherry-picked stat:
"LeBron of soccer? Please. Messi has a 45% win rate in knockout games — he's overrated."

**Decision rule:** One cherry-picked stat with accusatory framing = `hot_take`. The stat is decorative, not genuinely reasoned from.

---

## Data Collection Plan

**Source:** Reddit r/soccer, filtered to World Cup 2022 and 2026 posts and comments. I will collect posts and top-level comments manually by browsing match threads, post-match discussions, and general World Cup discussion threads.

**Target distribution:**
- analysis: 70 examples
- hot_take: 70 examples
- reaction: 70 examples
- Total: 210 examples

**If a label is underrepresented:** If any label falls below 60 examples after collecting 200 total, I will specifically search for posts that fit that label before moving on to annotation.

**Format:** Saved as a CSV file with columns: text, label, notes.

---

## Evaluation Metrics

I will use the following metrics:

- **Overall accuracy** — fraction of test examples classified correctly for both models
- **Per-class F1 score** — harmonic mean of precision and recall for each label. This matters because the classes may not be perfectly balanced and accuracy alone could be misleading if the model learns to predict one class most of the time
- **Confusion matrix** — to see which label pairs are being confused and in which direction

F1 is the right metric here because both false positives and false negatives matter equally — mislabeling a `reaction` as `analysis` is just as bad as missing a real `analysis` post.

---

## Definition of Success

The fine-tuned model will be considered successful if:
- Overall accuracy exceeds 70% on the test set
- No single class has an F1 score below 0.60
- The fine-tuned model outperforms the zero-shot baseline by at least 10 percentage points overall

These thresholds would make the classifier genuinely useful as a moderation or discourse-quality tool in a real community.

---

## AI Tool Plan

**Label stress-testing:** I will give Claude my three label definitions and edge case descriptions and ask it to generate 10 posts that sit at the boundary between two labels. If it produces posts I cannot classify cleanly, I will tighten my definitions before annotating 200 examples.

**Annotation assistance:** I will use Claude to pre-label batches of 20-30 examples at a time by providing my label definitions and asking it to assign one label per post. I will review and correct every pre-assigned label before accepting it. I will track which examples were pre-labeled in my notes column.

**Failure analysis:** After fine-tuning, I will paste my list of wrong predictions into Claude and ask it to identify common patterns — similar post length, sarcasm, specific label pairs being confused, or short low-information posts. I will verify the patterns myself by re-reading the examples before writing up my evaluation report.