# PRD #5: KubeCon Demo Polish — Slides, Dashboards, QR Codes

**Issue**: [#5](https://github.com/wiggitywhitney/kubecon-2026-gitops/issues/5)
**Status**: In Progress
**Priority**: High
**Created**: 2026-03-20

## Problem

Thomas rewrote the demo app in Java/Spring Boot with PostgreSQL persistence, switched from Anthropic to Mistral AI (with Anthropic big coming for the expensive variant), and restructured the GitOps repo. The existing slides, dashboards, and demo assets reference outdated architecture:

- Model names wrong (slides say Sonnet 4 / Haiku 4.5 / Opus 4.6)
- Architecture diagrams show two separate Knative Services per round — now it's one service with Flagger managing the canary
- Metrics use `story_name` label, not `service_name`
- OTel instrumentation shows Node.js injection — Java app handles its own OTel
- Datadog dashboard lacks Flagger and Knative traffic split metrics
- No QR codes for the live app URLs
- Talk structure agreed (premise → demo 1 → how it works → demo 2 → end credits) needs slide reordering
- "Journey of a thumbs up" sequence diagram may not match Java app's telemetry behavior

## Solution

Update all presentation assets to match current architecture and agreed talk flow. Create dashboards that support each talk moment. Generate QR codes. Investigate telemetry differences.

## Design Notes

- Live app URLs: `https://story-app-1.apps.platform.thomasvitale.dev/` and `https://story-app-2.apps.platform.thomasvitale.dev/`
- Speaker split: Whitney = OTel/Metrics (~7 min), Thomas = Flagger/Traffic Splitting (~7 min)
- Each demo ~5 min, ~15 min slides total
- Dashboard should be visible during live demos showing real-time rollout
- Knative metrics available for traffic split visualization
- Flagger metrics now scraped by the OTel Collector (Prometheus receiver)
- Thomas will change models to Anthropic big vs Mistral small (not done yet — use placeholder names until confirmed)

## Milestones

### M1: Slide Corrections ✅
- [x] Fix model names throughout (remove Sonnet 4, Haiku 4.5, Opus 4.6 references; use current model names or placeholders)
- [x] Fix architecture diagrams — one Knative Service per story with Flagger canary, not two separate services
- [x] Update metric label references from `service_name` to `story_name`
- [x] Remove Node.js OTel injection annotation references — Java app handles its own OTel
- [x] Update Collector YAML examples to match current config (v0.146.0, `story.name` dimension)
- [x] Fix container image references (ghcr.io/thomasvitale, not wiggitywhitney Docker Hub)

### M2: Slide Restructuring
- [x] Reorder slides to match agreed flow: premise → demo 1 → app explanation → how it works → demo 2 → end credits
- [ ] Add speaker introductions at end (end credits slide)
- [x] ~~Complete Flagger canary logic slides~~ — Thomas owns new slides; leave TODO in deck for him
- [x] Update "The Reveal" tables with correct variant descriptions
- [ ] Update speaker notes and presenter cues to reflect new flow (WHITNEY/THOMAS PRESENTS markers)

### M3: Dashboard Design ✅
- [x] Map which dashboards appear at which talk moment:
  - Both demos: combined dashboard with Flagger traffic weight + votes + canary status
  - Whitney's OTel section: APM trace link (already in slides)
  - Thomas's Flagger section: same combined dashboard
- [x] Identify which Flagger and Knative metrics are available in Datadog
- [x] Decide: update existing dashboard (`68y-xeg-j6s`) — single combined dashboard for all moments

### M4: Dashboard Implementation
- [x] Create/update Datadog dashboards per the design from M3
- [x] Add Flagger canary progression widgets (traffic weight over time, promotion status table, weight big numbers)
- [x] Add Knative traffic split visualization (primary vs canary weight timeseries + area charts)
- [x] Add real-time rollout timeline widget for demo moments
- [ ] Verify dashboards populate correctly with live cluster data (requires Thomas's cluster)

### M5: QR Code Integration ✅
- [x] Generate QR codes for story-app-1 and story-app-2 live URLs
- [x] Integrate QR codes into demo slides (replacing "generate QR code in Chrome" presenter cue)
- [x] Verify QR codes scan correctly on mobile devices

### M6: Journey of a Thumbs Up Investigation
- [ ] Review sequence diagram against Java/Spring Boot OTel behavior
- [ ] Hypothesize what's different (spanContext propagation, span event attachment, auto-instrumentation differences)
- [x] Get error details from Thomas and diagnose
- [ ] Update sequence diagram slides if the telemetry flow has changed

**Thomas's rendering error (diagnosed 2026-03-21):**
The "Journey of a 👍" sequence diagram slides (pages 29-40) render as raw Mermaid CSS instead of SVG diagrams on Thomas's machine. Participant labels appear but no diagram content. Thomas is on macOS, installed `chrome-headless-shell` extension (not `chromium`). Reproduced in Brave, Vivaldi, Firefox, and Safari — all show the same broken output. The issue is server-side Mermaid SVG rendering (`mermaid-format: svg`), not the viewing browser. **Fix**: Thomas needs `quarto install chromium` (full Chromium, not chrome-headless-shell), or switch to `mermaid-format: js` (renders in browser instead of at build time).

## Dependencies

- **Thomas's model change**: M1 slide corrections for model names depend on Thomas confirming final model choices (Anthropic big vs Mistral small)
- **Thomas's cluster**: M4 dashboard verification requires live data flowing through the cluster
- **Thomas's Chromium**: M6 fix requires Thomas to install `quarto install chromium` or switch `mermaid-format: js`

## Success Criteria

- Slides accurately reflect current architecture and demo flow
- Dashboards show meaningful real-time data during both live demos
- QR codes work on mobile devices at conference scale
- Presenter can walk through the full talk without referencing outdated information
