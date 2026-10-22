# AI Opponents: how Tricorne AI makes decisions

[← Return to the Commands & Colors field guide](userguide.md)

Commands & Colors: Tricorne offers two computer-controlled play modes: play against one computer opponent, or watch two AI commanders play each other. The AI runs on the game server. It selects from actions the server says are legal, compares their tactical value with a bounded search, and submits its choice through the same authoritative action flow used by players.

AI games are unranked. The AI uses the same Tricorne rules as human commanders; difficulty changes the size and depth of its search, not dice, unit capabilities, or combat outcomes.

## Choose a mode

| New Commission option | What happens |
| --- | --- |
| **Play against computer** | Choose British, Continental, or Random. You command one side and the AI commands the opposing side. |
| **Watch two AI commanders** | The server assigns an AI commander to each side. Open the game to spectate. |

Choose an **AI Difficulty** before deployment. In watch mode the selected difficulty applies to both AI commanders. AI settings are currently part of Tricorne's New Commission flow.

<figure>
  <img src="images/userguide-ai-opponent.png" alt="Dark-mode New Commission dialog with Play against computer selected, Hard difficulty, and the Bunker Hill force and objective preview" width="100%">
  <figcaption>Choose a human-versus-AI engagement and set the computer's difficulty before deployment.</figcaption>
</figure>

<figure>
  <img src="images/userguide-ai-vs-ai.png" alt="Dark-mode New Commission dialog with Watch two AI commanders selected and Brutal difficulty" width="100%">
  <figcaption>Watch mode lets you follow an AI-controlled engagement from both sides.</figcaption>
</figure>

## What difficulty changes

The shared AI planner uses these search profiles:

| Difficulty | Look-ahead depth | Beam width | Candidate limit per node | Future-score discount |
| --- | ---: | ---: | ---: | ---: |
| **Easy** | 1 step | 3 | 9 | 0.80 |
| **Medium** | 2 steps | 5 | 15 | 0.82 |
| **Hard** | 3 steps | 8 | 24 | 0.86 |
| **Brutal** | 3 steps | 8 | 30 | 0.90 |

**Depth** is the number of hypothetical actions the planner can look ahead. **Beam width** is how many of the strongest partial plans it keeps at each step. The **candidate limit** caps how many options it retains from each decision point, and the **discount** reduces the influence of later steps in a plan. The server also caps planning work at 2,048 expanded nodes and 5 seconds per decision; a search can stop earlier when it has enough information or reaches a limit.

Brutal is not a four-step search: it has the same depth and beam width as Hard, but can keep more candidates and gives later actions more weight. Hard and Brutal are more thorough within the current decision; neither searches every possible line through the entire battle.

## How a decision is made

1. **Read the current server state.** The Tricorne adapter builds a versioned view for the AI's controller. It keeps the opponent's private information and hidden random state out of the planner's observation.
2. **Collect legal candidates.** The adapter uses the server's current legal actions and variant rules to create typed choices. Candidate identifiers are opaque to the shared planner.
3. **Describe each choice with features.** The adapter supplies numeric evidence for relevant factors such as objective progress, expected damage and losses, retaliation, flags, survival, card use and cost, position, follow-up value, tactical value, and risk.
4. **Compare short plans.** The planner scores the candidates and uses cloned, hypothetical states to project selected order and movement actions. It keeps the strongest branches within the difficulty profile and work limits.
5. **Submit one choice and evaluate again.** The adapter resolves the selected candidate back to a typed Tricorne intent. The server validates it against the current authoritative state and version, applies it through the normal game transaction, and plans again from the updated state.

The AI's feature scores are estimates used to compare choices. For example, it can value progress toward an objective, weigh the expected damage and retaliation of an attack, and consider whether a combat card is worth spending. The feature values depend on the current board and legal action; the planner does not award a fixed bonus for every feature on every turn.

The search is deterministic for identical candidates and feature evidence: candidates are ordered by score with an opaque identifier used to break ties. It does not add random score noise to choose a weaker near-best option.

## The AI does not resolve its own combat

The AI proposes a legal intent; it does not author dice, hits, flags, casualties, retreats, or a final combat result. The authoritative Tricorne rules and server-generated random results resolve those outcomes. Required decisions and follow-ups remain explicit game state, and the AI plans from the state the server accepts.

This means the AI can estimate that an attack or position looks promising and still lose the exchange when the actual server-resolved result is poor. Higher difficulty improves the search within its limits; it does not guarantee a win or change the rules.

## Scope and limits

- Search depth covers a short sequence in the current decision context. The AI replans after each accepted action instead of committing to a whole battle plan.
- The planner is bounded and heuristic. It examines only a limited set of legal candidates and projected states; it is not exhaustive game-tree analysis and does not model every future opponent response.
- The decision features and action meanings are supplied by the Tricorne adapter. The shared policy ranks opaque candidates without owning faction rules or interpreting card identities.
- If a choice becomes stale before the server applies it, the server's current state and validation rules take precedence.

Use **Easy** for a lighter opponent, **Medium** for a balanced challenge, and **Hard** or **Brutal** when you want the planner to spend more of its bounded search on candidate comparison. These are play preferences, not promises of a particular result.
