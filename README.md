# Runaway Tab

**When should a usage dashboard flag a coding-agent session as a runaway spender, and should the flag just alert or also pause the session?** An importance-sampling Monte Carlo prototype picks the default threshold.

- **Live demo:** https://shriyashekatkar25-byte.github.io/runaway-tab/
- **Gallery:** [APM Lab](https://shriyashekatkar25-byte.github.io/apm-lab/)
- By Shriya Shekatkar · UCLA · Stats 102C Monte Carlo Methods

## The launch that inspired it

On October 27, 2026 Databricks released **Unity Gateway Usage Dashboard v1.0**. The release note says it covers coding-agent adoption, harness usage and estimated session spend, with more model and user spend analysis and tag-based spend attribution.
Source: [Databricks platform release notes, October 2026](https://docs.databricks.com/aws/en/release-notes/product/2026/october) · [Usage tracking docs](https://docs.databricks.com/aws/en/ai-gateway/usage-tracking)

Once spend is visible per session, the next question is what to do about a session that runs away (an agent stuck retrying the same failing step). This prototype imagines I'm the PM deciding that follow-up.

## The PM decision

Ship a default "runaway session" rule: when one session's estimated spend crosses **$T**, either **alert** the owner or **alert + pause** the session.

- **Success metric:** share of runaway-session spend above T that the rule stops, per 1,000 developer-months
- **Guardrails:** false alerts on normal sessions ≤ 120 / 1k dev-months · runaway sessions caught ≥ 85% · normal sessions paused ≤ 40 / 1k dev-months · tail-estimate relative error ≤ 10%
- **Stakeholders:** platform admins / FinOps, developers, gateway and agent-harness engineering, field teams, security

With the default simulated assumptions the memo recommends **$65 with alert + pause**. Alert-only could go as low as $50, but it stops less of the overrun.

## What the prototype shows about product judgment

| APM skill | Where it shows up |
|---|---|
| Customer problem framing | Starts from the admin's pain ("I found out from the invoice"), not from the feature |
| Prototyping | A working simulation with sliders, a seeded RNG, animated sample stickers and live KPI medals |
| SQL / Python | Illustrative Databricks-style SQL to build per-session spend, plus Python importance sampling to sweep thresholds |
| Data-driven decisions | The threshold comes out of a guardrail sweep, and the director memo rewrites itself when you move a slider |
| Clear writing | Decision, evidence, risks, rollout, next step, and a one-sentence VP summary |

## Methods

| Method (Stats 102C) | Used for |
|---|---|
| Ch6 Importance sampling | The rare false-alert tail P(normal spend > T), using a defensive mixture proposal g = ½f + ½N(ln T, σ²) and weights w = f/g |
| Ch6 Running estimate ± 1.96·SE | Live convergence chart comparing plain MC and importance sampling against the closed form |
| Ch6 Weight diagnostics | Mean weight ≈ 1 and effective sample size (Σw)²/Σw² |
| Ch5 Mixtures / Box-Muller | Session spend is a mixture of normal and runaway log-normals; normal draws come from Box-Muller |
| Ch1 Plain Monte Carlo | Runaway catch rate and dollars stopped |

All numbers are synthetic: normal sessions have a log-normal spend with a $2.40 median, runaways are 0.5% of sessions with a $130 median, and there are 140 sessions per developer per month.

## Design baseline

Four pins from Pinterest served as design input. Everything was redrawn in SVG, CSS and canvas, and no pin images are used.

- Hero: [Gallery of Indian Stamps - 2003](https://www.pinterest.com/pin/475692779372822750/). I took the perforated stamp edge, cut-paper coral and wave shapes, white polka dots, the cobalt/sunflower/pink/teal palette, the vertical greeting text and the corner denomination (here it shows the live threshold).
- Cards and buttons: ["You are the {css} to my <html>" by 333](https://www.pinterest.com/pin/821203313340069725/). I took the chunky slab-serif type on soft grey, red curly braces and blue angle brackets as heading and card motifs.
- KPI badges: ["Born To Win" by ·˚ ༘Aliza](https://www.pinterest.com/pin/1085156472744154784/). I took the gold medals on red/white/navy striped ribbons and the tall condensed type.
- Simulation room: [Sticker sheet pin on "Packaging" by - Bull](https://www.pinterest.com/pin/532972937161147107/). I took the purple sheet backing, the holographic rainbow header, the star strip and the grid of white die-cut stickers (each sticker is one simulated session, with my own original faces and fuse shapes).

---

Unofficial concept prototype by Shriya Shekatkar. All data is simulated. Not affiliated with any company.
