# Third-party notices

This plugin includes the third-party components below. Each license text ships
next to the files it covers. Pinned commits and the list of changes are in
[NOTICE](NOTICE).

| Component | Path | Copyright notice upstream | License | License text |
| :- | :- | :- | :- | :- |
| frontend-design, from [anthropics/skills](https://github.com/anthropics/skills) | `skills/frontend-design/` | None stated; published by Anthropic | Apache-2.0 | [LICENSE.txt](skills/frontend-design/LICENSE.txt) |
| modern-web-guidance, from [GoogleChrome/modern-web-guidance](https://github.com/GoogleChrome/modern-web-guidance) | `skills/modern-web-guidance/` | None stated; published by Google Chrome | Apache-2.0 | [LICENSE](skills/modern-web-guidance/LICENSE) |
| security-audit, from [cloudflare/security-audit-skill](https://github.com/cloudflare/security-audit-skill) | `skills/security-audit/` | Copyright (c) 2025-2026 Cloudflare, Inc. | MIT | [LICENSE](skills/security-audit/LICENSE) |

## Fetched at runtime, not shipped

The skills run the `modern-web-guidance` npm package, pinned to version 1.0.1,
through `npx`. The package is not part of this repository. Its code is licensed
under Apache-2.0, and the guide content it serves is licensed under
[CC-BY 4.0](https://creativecommons.org/licenses/by/4.0/).
