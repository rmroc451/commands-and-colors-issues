<!-- BEGIN PUBLIC PLAYER GUIDES -->
## Player Guides

- [Commands & Colors User Guide](docs/userguide.md)
- [AI Opponents Guide](docs/ai-guide.md)
<!-- END PUBLIC PLAYER GUIDES -->

# Commands & Colors Tactical Portal: Architecture Overview

This document outlines the architecture, technology, and functional design of the Commands & Colors Tactical Portal. The current playable implementation is Tricorne: Angular presents the game, while the Python server owns authoritative game rules and state. Variant-specific rules stay behind their owning server adapters.

---

## 0. Legal & Attribution: The Commands & Colors System

*Please Note: I am taking absolutely no credit for the underlying game mechanics, rules, or the incredible system this project is based upon. This is entirely a fan project, and the true credit goes to the brilliant minds who originally created and published these games.*

The **Commands & Colors** game system is a masterclass in wargame design, striking a perfect balance between accessibility, historical flavor, and deep tactical gameplay. All rights, concepts, and core mechanics belong solely to the original designer and the respective publishers.

### The Designer

The Commands & Colors system was invented and designed by [**Richard Borg**](https://en.wikipedia.org/wiki/Richard_Borg), an incredibly prolific game designer. His amazing work has spawned one of the most successful, adaptable, and universally beloved families of board games in the tabletop hobby. Borg's innovative system has successfully modeled warfare from the ancient world through to science fiction.

### Support the Publishers (Buy the Games!)

If you enjoy this system or find this project useful, I highly encourage you to purchase the physical board games. They feature fantastic tactile components, beautiful maps, and countless expansions that significantly deepen the experience. You can find and support the publishers directly at the links below:

* [**GMT Games**](https://www.gmtgames.com/)
  *GMT is the powerhouse behind the core historical titles in the series. They are the publishers of:*

  * *Commands & Colors: Ancients (Focusing on the Classical world, Punic Wars, etc.)*

  * *Commands & Colors: Napoleonics*

  * *Commands & Colors: Medieval*

  * *Commands & Colors: Samurai Battles*

* [**Days of Wonder**](https://www.daysofwonder.com/)
  *Publishers of the massively popular World War II classic, **Memoir '44**. This game features extensive expansions covering the Eastern Front, Pacific Theater, and the Mediterranean.*

* [**Compass Games**](https://www.compassgames.com/)
  *Publishers of **Commands & Colors: Tricorne - The American Revolution**, focusing on the linear tactics of the 18th century.*

* [**Plastic Soldier Company (PSC Games)**](https://www.theplasticsoldiercompany.co.uk/)
  *Publishers of **The Great War**, which successfully adapts the system to the trench warfare and emergence of tanks in World War I, and **Red Alert: Space Fleet Warfare**, which takes the system into science fiction combat.*

* [**Avalon Hill / Hasbro**](https://avalonhill.hasbro.com/)
  *Publishers of **Battle Cry** (The American Civil War). This is the landmark game that originally launched the entire Commands & Colors system back in 2000.*


## 1. Technology Stack

| Component | Technology | Purpose |
| :--- | :--- | :--- |
| **Framework** | Angular | Browser application structure, components, and UI. |
| **Language** | TypeScript | Client-side presentation, requests, and state projections. |
| **Game authority** | Python backend | Validates typed game actions and owns Tricorne rules, state transitions, and outcomes. |
| **Live communication** | WebSockets and Redis-backed room transport | Carries authenticated game requests and server updates to connected clients. |
| **Browser testing** | Playwright and Karma / ChromeHeadless | Covers browser workflows and Angular unit tests. |
| **Deployment** | Docker Compose and Nginx | Runs the application and its supporting services in development and deployment environments. |

### Backend & Infrastructure Analysis

The live game uses a server-authoritative client/server flow. Angular renders the board, cards, decisions, and accepted game state. It sends typed player intent with an action ID and the expected state version; it does not submit dice, hits, casualties, retreats, game results, or document patches as gameplay truth.

The Python Tricorne implementation validates the controller, action, version, and any pending decision before it changes the game. It owns movement and phase legality, command and combat deck transactions, combat rolls, follow-up decisions, and victory outcomes. Accepted transactions retain version and audit evidence; stale or rejected actions do not alter the live game.

WebSocket and Redis-backed room transport distribute committed updates through recipient-aware projections. Private hands, draw-pile order, and private random-number state are not exposed to other players. Redis supports live room transport and coordination; it is not described here as the sole authoritative game database.

Docker Compose and Nginx provide the containerized development and deployment setup. The browser application and Python game server have separate responsibilities, with variant-specific rules kept in the Python variant boundary.

---

## 2. Core Data Models

The live game document and its rule-bearing data are owned by the Python server and the selected game variant. Angular receives projections for display and submits typed requests; client-side interfaces are not the authoritative rules or game state.

The principal game concepts include:

* **Game state**: Tracks the active player, phase, board, orders, decks, victory progress, and any serialized decision awaiting a controller.
* **Hex**: Represents a board location, including coordinates and variant-provided terrain or elevation data.
* **Unit**: Represents a formation and its current board state. Unit capabilities and faction presentation come from the selected variant's server-owned data.
* **Cards**: Command and combat cards have server-owned identities, physical instances, availability, and effects. The client displays the permitted projection.

---

## 3. Game Flow: The State Machine

The Python server owns the Tricorne phase machine and validates every live transition. The browser requests an action and renders the accepted state and any decision that remains open.

1. **Play Command Card**: The controlling player selects a card from their permitted hand projection.
2. **Order**: The server checks which units the active card allows the player to order.
3. **Movement**: The server validates the requested path, movement capability, terrain, occupancy, and stacking.
4. **Combat and follow-ups**: The server resolves combat and serializes any required retreat, rally, reaction, leader, battle-back, advance, or cavalry decision.
5. **End phase and turn**: The server permits phase advancement only when required decisions are complete and selects the next active player.

Pending decisions block unrelated actions that could bypass the required sequence. The browser can preview and animate actions, but only server-accepted transitions change the live game.

---

## 4. Logic Services (The Game Engine)

Tricorne rules are implemented behind the Python variant boundary. Shared client and server code may handle common transport or rendering, but it does not infer Tricorne faction rules from class names or raw faction identifiers.

### A. Orders and Movement

The server checks order scope and validates each movement step against the unit's chart capabilities, terrain, occupancy, stacking, and pass-through rules. It verifies the complete path and cumulative movement before accepting the move. Client-provided traits, paths, or movement limits cannot widen legal movement.

### B. Combat Resolution

The Tricorne server combat resolver owns dice generation, hit and flag resolution, casualties, board changes, pending follow-ups, and replay evidence. Each action is tied to an idempotency key and expected state version. Angular can show legal previews and animate the resulting evidence, but it cannot supply the roll or commit a calculated result.

### C. Retreats, Rallying, and Leaders

Retreat, rally, leader casualty, escape, and related follow-up choices are explicit server decisions with a controller and decision version. The server validates each response and resolves its consequences. The client renders the serialized decision rather than reconstructing the rule locally.

---

## 5. Command & Combat Card Architecture

Card identity and transactions belong to the variant-owned Python deck implementations. In Tricorne, sprite position and physical card instance identity are stable data; titles, image URLs, or reordered artwork are not used to infer card identity.

* **Command Cards**: The server controls the playable manifest, hands, draw and discard transactions, order limits, and card effects. British and Continental players own command decks; French and Hessian forces are supporting members controlled by the appropriate deck-owning player.
* **Combat Cards**: The server checks card eligibility and timing, owns reactions and card-scoped effects, and records accepted use with the combat or action that triggered it.
* **Client display**: Angular shows only the authorized hand and public card information. It does not expose hidden hands or draw-pile order, consume cards, or author card outcomes.

---

## 🚀 Variant Boundaries and Extensibility

The current implementation is centered on Commands & Colors: Tricorne. Variant rules, faction identifiers, sprite identities, unit capabilities, decks, and scenario behavior belong to their owning variant definitions or adapters. Shared code treats variant identifiers as opaque values and obtains names and presentation metadata from the selected variant.

Adding another variant requires an authoritative rules adapter and its own manifests, validation, and parity evidence, along with client projections and focused browser coverage. A unit configuration or card class alone does not make a new Commands & Colors variant playable.

The AI uses the same server-owned legal actions and action resolution as human players. Its planner compares bounded candidate plans and submits typed intent; the server remains responsible for validating and resolving each choice. See the [AI Opponents Guide](docs/ai-guide.md) for its difficulty profiles and decision process.
