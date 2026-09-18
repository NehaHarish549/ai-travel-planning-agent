# AI Travel Planning Agent

An AI-powered travel-planning agent built with Claude Code, designed around a simple principle borrowed from enterprise software QA: **an agent's output is only as trustworthy as its handling of ambiguity, missing data, and its own assumptions.**

This project isn't about generating itineraries — plenty of tools do that. It's about stress-testing the failure modes that make AI agents unreliable in practice, and hardening against them with explicit, testable rules.

## Core Design Principles

The agent operates under a set of hardened rules (see `CLAUDE.md`), refined through iterative testing:

1. **Never assume — ask.** Destination, exact travel dates, and budget style are all treated as required inputs. If any is ambiguous or unstated, the agent stops and asks rather than guessing or defaulting to a "reasonable middle."
2. **Currency validation.** Every price search explicitly sets and confirms its currency rather than relying on a tool's default — a real bug caught in early testing (a search silently defaulted to the wrong currency for a Pakistan-based query).
3. **Cross-city contamination checks.** When a city name has same-name counterparts elsewhere in the world (e.g., Hyderabad, India vs. Hyderabad, Sindh, Pakistan), search results are actively checked for bleed-through from the wrong city, rather than passed through silently.
4. **Budget-consistency checks.** The agent validates that recommended hotels, tours, and estimated daily costs actually align with the user's stated budget style — and explicitly flags it when a destination's realistic costs exceed what that style typically implies (e.g., "budget" in Reykjavik still running $116–164/person/day due to structurally high food and tour costs).
5. **Explicit source-tagging.** Every figure in a generated document is labeled `Sourced` (pulled live from a named tool/site) or `Estimated` (a planning range from cited guides, not a live quote) — nothing is presented as fact unless it's traceable.
6. **Honest gap reporting.** When data genuinely doesn't exist for a destination (e.g., no organized walking tours, no curated vegetarian restaurant scene), the agent says so plainly instead of fabricating plausible-sounding content.
7. **"Last verified" transparency.** Every generated document leads with a visible verification timestamp and a reminder to re-check prices if the travel date is far out.

## What Was Tested

| Test | Scenario | Result |
|---|---|---|
| **Multi-way disambiguation** | Requested "Santiago" with no country specified | Correctly surfaced all 4 real destinations (Chile, Spain, Dominican Republic, Cuba) and asked the user to choose, rather than defaulting to the most famous one |
| **Budget-mismatch detection** | "Budget/Backpacker" trip to Reykjavik, Iceland | Found genuinely budget-tier accommodation, but correctly flagged that food and tour costs push the realistic total well above what "backpacker" typically implies elsewhere |
| **Budget-match confirmation** | "Mid-range" trip to Lisbon, Portugal | Correctly found *no* mismatch and stated so explicitly — validating that the check doesn't just raise alarms, it can also confidently confirm a fit |
| **Real-world rule validation** | A genuine (non-test) Lisbon trip request with no stated budget style | The agent stopped and asked for budget style before starting any research — proving the rule generalizes beyond scripted test cases |
| **Data contamination judgment** | A hotel result ("Hotel 3K Madrid") in a Lisbon search | Investigated the actual evidence (address, review count) rather than pattern-matching on the name alone, and made a disclosed, reasoned judgment call |

## Example Outputs

Sample generated itineraries are included in this repo, demonstrating the rules in action across different destinations, budget styles, and edge cases.

## Tech Notes

Built using Claude Code, with live web search/browsing tools for real-time hotel, tour, weather, and pricing data. PDF generation via a Python build script.

## Why This Project

I bring 13+ years of enterprise QA and test leadership experience (SAP S/4HANA, Tricentis TOSCA). This project was a way to apply that same rigor — risk-based thinking, refusing to let assumptions compound, treating gaps as findings rather than hiding them — to a new domain: agentic AI. The goal wasn't a polished product, but a demonstration of how quality engineering judgment transfers directly to building (and testing) AI systems.

---

*Built and tested September 2026.*
