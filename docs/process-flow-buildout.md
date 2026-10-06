# Process flow buildout (Miro via Claude MCP)

This build opened with a **process flow** before any Salesforce configuration, so the whole
system could be explained on one page: a partner referral moving across Marketing, Sales, and
Delivery, and who owns each step. The diagram was built with **Claude connected to Miro over MCP**
(the Miro Model Context Protocol server), not drawn by hand.

**Board:** https://miro.com/app/board/uXjVHiXb-zU=/

## Why a flow first
The assignment is graded on business acumen and communication. Leading with a cross-functional
swimlane does two things: it plays the business process back to the client in their own language,
and it gives the live Salesforce demo a spine to follow. The diagram is the first thing shown in
the walkthrough; every later step maps to a node on it.

## The MCP toolchain
Claude's Miro MCP exposes a canvas composer that turns a constrained **SVG dialect** into real Miro
widgets (frames, shapes, connectors, tables). The relevant tools:

| Tool | Used for |
|------|----------|
| `board_create` | Create the board |
| `canvas_get_canvas_composer_skill` | Load the authoring workflow + the SVG DSL spec (design step, then DSL step) |
| `canvas_load_format_skill` (diagramming / free_form) | Palette, shapes-by-role, and connector rules for a hand-assembled diagram |
| `canvas_create_from_svg` | First render of the composition |
| `canvas_update_from_svg` | Iterate in place (matched on widget ids returned by the previous call) |
| `board_show` | Surface the finished board |

## Build steps
1. **Create the board**, then load the composer skill (design → DSL) and the diagramming format
   guidance so the SVG uses the correct palette, node shapes, and connector routing.
2. **First pass — a linear flow.** Nodes for Referral Lead → Qualify → Convert → Work the
   Opportunity → `Closed Won?` decision → Won (Project + Client) / Lost, authored as one SVG and
   sent with `canvas_create_from_svg`. Connectors are created *before* the node shapes so the shapes
   sit on top of the lines.
3. **Rework into swimlanes.** The first version had no owners. It was reworked into three
   owner lanes down the left — **Marketing / Referral Partner**, **Sales**, **Delivery** — with the
   flow visibly handing off lane to lane. Nodes were recolored by owner, and the capture node was
   relabeled from a generic "marketing lead" to a **referral lead** (it is a partner-referral
   process, not a cold-marketing one).
4. **Close the open paths.** Added a **Nurture & Recycle** state fed by both "not qualified" leads
   and "re-engage" lost deals, and a **Project Complete** end state, so no branch dead-ends.
5. **Fold the Salesforce specifics into the flow:** the "Work the Opportunity" node notes the guided
   **Path**, and the Won node shows the automation's dual effect — *Project created + Account becomes
   Client*.

## Integration: how the flow maps to the Salesforce build
The diagram is not decoration; every node is a real object or automation.

| Flow node | Salesforce |
|-----------|------------|
| Referral Lead (web form) | standard **Lead** created by a Web-to-Lead form; `Lead Source = Referral Partner` |
| Qualify Lead | set **Referred By (Partner)** lookup to the real partner Account |
| Convert Lead | standard **Lead Convert** → Account + Contact + Opportunity |
| Work the Opportunity | custom **Sales Process** + **Path** (key fields + guidance per stage) |
| `Closed Won?` | the Opportunity **Stage** |
| Won → Project created + Account → Client | one **Record-Triggered Flow** on the Opportunity |
| Delivery / Project Complete | the custom **Project** object + its **Status** |
| Nurture & Recycle | the lead/opportunity lifecycle (disqualify and lost re-engagement) |

## Gotchas worth keeping
- **Z-order is creation order.** Author connectors before the shapes they connect, or the lines
  render on top of the boxes.
- **Connectors reference widget ids, not coordinates.** On an update, reference the existing nodes
  by the ids returned in the previous `result_svg` (or restate them as stubs) or the endpoints come
  back "unresolved."
- **Escape XML entities** (`&amp;`) in labels, and don't double-escape — `&amp;amp;` prints a literal
  `&amp;`.
- A **filled swimlane band added on an update renders on top** of existing nodes (new widgets are
  created last = in front). Lane headers in the left margin plus thin divider strips avoid covering
  the flow.

---
Built by [Jibril Sulaiman](https://github.com/carljibrilsulaimanii).
