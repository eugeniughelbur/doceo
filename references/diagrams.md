# Diagrams

Each lesson gets one of 5 diagram kinds. Write it once in Mermaid, then show it in the note and on the page.

## Pick the kind from the topic's shape

When unsure, use a flow.

- **Flow** (`flowchart LR`): steps in order, or data moving through parts. Webhooks, a build pipeline, how an LLM picks the next word.
- **Sequence** (`sequenceDiagram`): two to four parties talking over time. OAuth, a TCP handshake, a payment API call.
- **State** (`stateDiagram-v2`): one thing that changes status. An order, a pull request, a circuit breaker.
- **Map** (`flowchart TB`): where one piece sits in a bigger system. A folder in a codebase, one service in an architecture. Mark one node "you are here".
- **Compare** (`flowchart LR` with two `subgraph` blocks): two options side by side. Webhooks vs polling, SQL vs NoSQL.

A domain map in `maps/<domain>.md` is always a map.

## Write the Mermaid first

1. Write the diagram as Mermaid.
2. Paste it unchanged into the vault note's `## Picture` section.
3. Draw the HTML page's inline SVG from the same nodes, labels and arrows. Only the look changes. Never add or drop a node on one side.

## Keep it small

- 4 to 9 nodes. A sequence gets 2 to 4 participants and at most 8 messages.
- Label every arrow with a verb: "sends token", "retries", "reads".
- Node labels stay under 5 words, in the learner's words. Gloss jargon the same way the prose does.

## Mermaid that renders everywhere

Obsidian, GitHub and Notion all render Mermaid, but they break on the same few things.

- Quote any label that holds `()`, `[]`, `{}`, `/`, `<`, `>` or `#`: `A["POST /hook"]`.
- In a flowchart, never name a node `end` in lowercase. It closes the block. Write `End` or `done`.
- Break long labels with `<br/>`, not a newline.
- In a sequence, keep `;` and `#` out of message text.
- For "you are here" on a map, add `classDef here stroke-width:3px` and tag the node with `:::here`.

## Show the source on the page

Under the picture on the HTML page, add a closed `<details>` with the summary "Diagram as Mermaid". Inside it, put the exact source in `<pre><code>`, HTML-escaped. The learner can paste it into GitHub, Notion or their own notes. It is plain text, so nothing loads from the network.
