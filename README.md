# UX Competitive Benchmark Skill

A Claude Code skill for conducting rigorous, evidence-based UX competitive benchmarks for travel and experience booking platforms (Klook, GetYourGuide, KKday, Viator, Headout, Pelago, Airbnb Experiences, etc.).

## Features

- **Structured 4-phase benchmark workflow** — Tool verification → Screenshot capture → Heuristic evaluation → HTML report
- **8-dimension scoring framework** — CTA clarity, step clarity, trust signals, form design, progress indicators, error handling, mobile-friendliness, speed
- **Live browser automation** — Uses `agent-browser` CLI for screenshot capture with compact element refs
- **Self-contained HTML reports** — All screenshots embedded as base64, no external dependencies
- **Fallback mode** — Generates knowledge-based benchmarks when browser automation is unavailable
- **Mobile viewport support** — Captures both desktop (1280x800) and mobile (375x812) views

## Requirements

### Essential

- **Claude Code** — This skill requires Claude Code CLI
- **agent-browser CLI** — Browser automation tool for screenshot capture
  - Install: `npm install -g agent-browser && agent-browser install`
  - Verify: `agent-browser --version`

### Optional (for fallback mode)

If browser automation is unavailable, the skill will generate a knowledge-based benchmark without live screenshots.

## Installation

1. **Copy the skill to your Claude skills directory:**
   ```bash
   mkdir -p ~/.claude/skills/
   cp -r ux-benchmark/ ~/.claude/skills/
   ```

2. **Or install the `.skill` package:**
   ```bash
   # Claude Code will recognize the skill from the skills directory
   ```

3. **Verify installation:**
   ```bash
   ls ~/.claude/skills/ux-benchmark/SKILL.md
   ```

## Usage

### Basic Command

Invoke the skill from Claude Code:

```
/ux-benchmark {flow_type} {competitors} {our_product}
```

### Examples

```bash
# Benchmark checkout flow across Klook, GetYourGuide, KKday
/ux-benchmark checkout Klook,GetYourGuide,KKday MyProduct

# Benchmark product detail pages for Bali activities
/ux-benchmark pdp Klook,Viator,Headout SatuSatu --focus "Bali activities"

# Benchmark mobile booking experience
/ux-benchmark mobile GetYourGuide,KKday TripPal --viewport 375x812
```

### Flow Types

| Flow Type | Description |
|-----------|-------------|
| `pdp` | Product Detail Page — browsing, information architecture, trust signals |
| `checkout` | Cart → guest info → payment → confirmation |
| `signin-signout` | Authentication flows (sign in, sign out, session management) |
| `search-to-book` | Search → results → PDP → cart → checkout |
| `cart` | Add to cart → cart review → proceed to checkout |

### Competitor Options

- Klook (`https://www.klook.com`)
- GetYourGuide (`https://www.getyourguide.com`)
- KKday (`https://www.kkday.com`)
- Viator (`https://www.viator.com`)
- Headout (`https://www.headout.com`)
- Pelago (`https://www.pelago.co`)
- Airbnb Experiences (`https://www.airbnb.com/experiences`)

## Step-by-Step Workflow

The skill executes four phases automatically:

### Phase 0: Verify Tooling

Checks if `agent-browser` is installed and accessible. Falls back to knowledge-based mode if unavailable.

### Phase 1: Screenshot Capture

For each competitor:
1. Opens a named browser session
2. Navigates to the relevant flow/URL
3. Captures screenshots at each step
4. Captures mobile viewport (375x812)
5. Closes sessions

### Phase 2: Heuristic Evaluation

Scores each competitor on 8 dimensions (1-5 scale):
- CTA Clarity & Visual Hierarchy (20%)
- Step Clarity & Information Architecture (15%)
- Trust Signals & Social Proof (15%)
- Form Design & Cognitive Load (15%)
- Progress Indicators & Orientation (10%)
- Error Handling & Validation (10%)
- Mobile-Friendliness Signals (10%)
- Speed & Performance Signals (5%)

### Phase 3: Pattern Extraction

Identifies:
- **Table Stakes** — Features present on 3+ competitors
- **Differentiators** — Unique to 1-2 competitors
- **Anti-patterns** — Negative UX to avoid

### Phase 4: HTML Report

Generates a self-contained HTML report with:
- Executive summary with overall winner
- Methodology notes
- Per-competitor analysis with embedded screenshots
- Comparative scoring matrix
- Design recommendations tagged HIGH/MEDIUM/LOW

## Output

The skill generates:

1. **HTML Report** — Saved to output directory with embedded screenshots
2. **Chat Summary** — 5-bullet overview:
   - 🏆 Overall winner + weighted score
   - 📐 Top 3 design recommendations
   - 🚫 Top 2 anti-patterns to avoid
   - 📱 Most notable mobile UX gap
   - 📝 Live vs. knowledge-based sourcing notes

## Configuration

### Output Directory

Default: `/mnt/user-data/outputs/`

Modify in `SKILL.md` if needed:
```markdown
#### Save Location
```
/custom/path/ux-benchmark-{FLOW_TYPE}-{YYYY-MM-DD}.html
```
```

### Competitor URLs

Default URLs are defined in `SKILL.md` under "Common Competitor URLs". Add or modify as needed:

```markdown
| Competitor | Base URL |
|------------|----------|
| YourCompetitor | https://www.yourcompetitor.com |
```

## Troubleshooting

### "agent-browser: command not found"

Install agent-browser:
```bash
npm install -g agent-browser
agent-browser install
```

### "Permission denied" errors

The skill requires bash permissions for:
- Running `agent-browser` commands
- Navigating to websites
- Capturing screenshots
- Encoding images to base64

Ensure your Claude Code permissions allow bash access.

### Bot Protection (DataDome, etc.)

Some travel sites use bot protection. The skill will:
1. Try direct navigation first
2. Fall back to alternative URLs if needed
3. Use knowledge-based analysis for blocked content

### No screenshots in report

If browser automation fails, the skill generates a knowledge-based benchmark with:
- CSS wireframe placeholders
- Clear labeling: "Knowledge-based evaluation — not from live capture"
- Recommendations to verify with live screenshots

## MCP Server Requirements

This skill uses the `agent-browser` CLI tool, which is a separate MCP (Model Context Protocol) server.

### Installing agent-browser

```bash
# Install globally via npm
npm install -g agent-browser

# Install browser dependencies
agent-browser install
```

### Verifying Installation

```bash
agent-browser --version
# Expected output: agent-browser 0.x.x
```

### Alternative: Playwright MCP

If `agent-browser` is unavailable, the skill can adapt to use Playwright MCP tools:
- `browser_navigate` — Navigate to URLs
- `browser_snapshot` — Get page elements
- `browser_take_screenshot` — Capture screenshots
- `browser_click` — Interact with elements
- `browser_resize` — Change viewport size

## Skill Structure

```
ux-benchmark/
├── SKILL.md              # Main skill definition
└── evals/
    └── evals.json        # Test cases with assertions
```

## Development

### Running Tests

```bash
# From the skill directory
cd ux-benchmark

# Tests are defined in evals/evals.json
# Run via Claude Code skill-creator or manually
```

### Test Cases

| ID | Name | Description |
|----|------|-------------|
| 1 | checkout-flow-benchmark | Checkout flow across Klook, GYG, KKday |
| 2 | pdp-benchmark-bali-activities | PDP analysis for Bali activities |
| 3 | mobile-ux-benchmark | Mobile date picker and ticket selection |

## Contributing

To improve this skill:

1. Edit `SKILL.md` with your changes
2. Update `evals/evals.json` with new test cases
3. Test with Claude Code to verify functionality
4. Submit a pull request

## License

This skill is part of the uiux-benchmark project.

## Changelog

### v1.0.0 (2026-03-27)
- Initial release
- 8-dimension scoring framework
- Support for signin/signout, checkout, pdp, and more flow types
- Fallback mode for environments without browser automation
- Self-contained HTML report generation
