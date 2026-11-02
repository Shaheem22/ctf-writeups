# CTF Writeups

Writeups from the CTFs and blue-team challenges I play, with a focus on SOC, DFIR and threat hunting. Each CTF has its own folder with an event README and one file per scenario.

## CTFs

| CTF | Platform | Focus | Folder |
|---|---|---|---|
| Holmes CTF 2026: The Reichenbach Directive | Hack The Box | Sherlock-format blue-team incident response (team of two) | [holmes-ctf-2026](./holmes-ctf-2026) |
| Cymbal Investments Cold Room (Oct 2026) | Google Cloud Security Community x Hack The Box | SIEM investigation, threat hunting, AI misuse (Google SecOps) | [cymbal-cold-room-2026](./cymbal-cold-room-2026) |

## Conventions

- **Spoiler-free:** flag values and environment-specific answers are replaced with `REDACTED`. Writeups describe tools, reasoning and technique, not the literal strings submitted.
- **Template:** HTB events follow the HTB writeup layout (banner, byline, Scenario, Artifacts Provided, Initial Analysis, Questions). Other platforms use a layout that fits their format.
- **Naming:** one folder per CTF (`event-name-year`), numbered scenario files (`01-scenario-name.md`), and platform banners in the root `assets/` folder (e.g. `htb-banner.png`), referenced as `../assets/<platform>-banner.png`.

## Adding a new CTF

1. Create a folder named `event-name-year`.
2. Add a `README.md` with the event overview and a scenario table.
3. Add one `NN-scenario-name.md` per scenario. Use the platform's banner from the root `assets/` if the template has one.
4. Add a row to the table above.

## Author

Muhammad Shaheem (HTB: feanor) · [GitHub](https://github.com/Shaheem22) · [LinkedIn](https://linkedin.com/in/muhammad-shaheem--)
 