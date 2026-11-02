<img src="../assets/htb-banner.png" style="max-width: 100%;" align=left />

# Cymbal Investments Cold Room (October 2026)

Hosted by the Google Cloud Security Community in partnership with Hack The Box. A solo Jeopardy event run entirely inside **Google Security Operations (SecOps)**: you play a certified reconstructor working in a frozen SIEM environment, following evidence across identity, endpoint, cloud, mail, build-system and data logs, and separating attacker activity from legitimate maintenance.

Three incidents unfolded in the same environment over about a week. Each scenario came with a threat-intelligence report as seed material.

## Scenarios

| # | Scenario | Category | Difficulty | Writeup |
|---|---|---|---|---|
| 1 | Deceptive Click | Investigation | Very Easy | [01-deceptive-click.md](./01-deceptive-click.md) |
| 2 | OAuth Heist | Threat Hunting | Easy | [02-oauth-heist.md](./02-oauth-heist.md) |
| 3 | Rogue Synthesis | AI Misuse | Medium | [03-rogue-synthesis.md](./03-rogue-synthesis.md) |

## Result

Played solo as team **Cyber_Star**: 3 of 3 scenarios and 18 of 18 flags solved, 1975 points.

## Tooling

- Google SecOps: Cases, Emerging Threats, SIEM Search (UDM and raw log search), Entity Explorer
- Case-level Gemini summaries (used as leads, verified against raw events)
- Google Threat Intelligence reports supplied with each scenario

## Notes

Flag values and environment-specific answers are intentionally redacted throughout. Each writeup covers the approach and reasoning, not the literal strings submitted.
