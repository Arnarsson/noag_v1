# NoAg — Forward Plan

A static prototype today. Path to a real local-first chief-of-staff that runs the company.

## Where we are (v1, this commit)

- `index.html` — single-file React app (Babel UMD). Loads in any browser, no build step.
- 3 amoebas (HEKLA · Seven · DAD), 21 teams, full flow boards, Touchpoints/Touchdowns calendar, Speak Up.
- HEKLA amoeba: **Prospects / Clients toggle** above the canvas.
- Flow board: **New ticket** + per-stage **Add** buttons land tickets via `AgentOps`.
- **Amoeba Agent** (bottom-right FAB) — chat surface that mutates the board:
  - Tier 1 (default): local rule-based parser. No setup.
  - Tier 2 (opt-in): paste an Anthropic API key in the gear menu → free-form English via Claude.
- All edits persist to `localStorage` (`noag_v1_state`).
- Speak Up moved to the sidebar.

## Roadmap

### Near-term (this week → next sprint)

1. **Drag-reorder tickets** across stages. Currently agent-only. Use HTML5 drag/drop, emit `ticket.move`.
2. **"Pull into Goals"** signal action. Wire the button on each signal to `AgentOps.apply([{op:'ticket.add', lane:'goals', ...}])`.
3. **Multi-week calendar nav.** Today the touch grid is one fixed week. Add `<` `>` buttons that shift `data.touches[].day` by ±7 with a date-aware label.
4. **Agent: undo last op.** Capture pre-state in `Store.update`; expose `Store.undo()`. Add an "Undo" button in the agent's ops log.
5. **Agent: streaming.** Switch the Anthropic call to streaming and update the message as tokens arrive.
6. **Mobile.** Sidebar collapses to a sheet, agent FAB stays. Touch grid → day list view < 768px.

### Mid-term (next 2–6 weeks)

7. **Persistence beyond localStorage.** Move state to Supabase (or Tauri SQLite if going local). Schema mirrors `APP_DATA`.
8. **Auth + multi-user.** One Sven today; eventually Hjalti, Christopher, ET members. Roles map cleanly to NoAg PO/TM/CT/ET/NP.
9. **Real Touchpoint integration.** Touchpoints become real calendar events (Google/Microsoft). Two-way sync.
10. **Inbox → Signals.** A daemon (n8n, or local agent) ingests Gmail/Outlook, summarises, posts as `signal.add`.
11. **Whiteboard photo → DAD board.** OCR + LLM → reconstruct the Bring/Hente/AC/Sven/Fælles rota from a photo.
12. **WhatsApp agent.** Sven texts the board. Sven gets reminders. Same `AgentOps` contract, different transport.
13. **Speak Up routing.** Today the modal is decorative — wire it to actually email/WhatsApp the relevant role-holder.

### Longer-term (quarter+)

14. **Tauri packaging.** Match the HEKLA local-first stack. Ship NoAg as part of the HEKLA desktop app, with the Mac Mini as the persistence layer.
15. **Sprint cadence automations.** Auto-roll daily → done at end of day. Auto-create next sprint from carryover.
16. **Agent gets tools, not just ops.** Right now the agent emits JSON ops. Next: give it tool access — read email, search Vault, draft replies, schedule calendar slots.
17. **Vault integration.** Tickets reference Vault entries (PRDs, transcripts, contracts). Click a ticket → see the underlying material.
18. **Org-wide rollout.** The amoeba model isn't just for Sven — any HEKLA pilot can use it. White-label it.

## Architecture decisions to make

- **State shape:** keep current flat `{amoebas, flowByTeam, touches, signals}` or normalise into separate Supabase tables? Recommendation: normalise once we hit real persistence; keep the in-memory shape for the React layer.
- **Agent runtime location:** browser today. Eventually move to the HEKLA local agent runtime (Ollama + Anthropic escalation). The `AgentOps` contract stays the same — only the transport changes.
- **Conflict resolution:** last-write-wins for now; revisit when multi-user lands.
- **Operation log as audit trail:** `AgentOps.apply` already returns a log array. Persist these per-session and you have a "what changed in NoAg this week" digest for free.

## Operating principles (don't drift)

- **Ship working code over perfect plans.** The prototype lives in a single file because that's the fastest path to feedback.
- **Local-first.** localStorage now, SQLite via Tauri later, Supabase only if/when multi-user demands it.
- **Agent as glue, not as oracle.** The agent translates Sven's reality into board mutations. It's not the source of truth — the boards are.
- **Match HEKLA's brand.** Background `#131110`, accent `#D4643A`, cream text. Already aligned in dark mode.
