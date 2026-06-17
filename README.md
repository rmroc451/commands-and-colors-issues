# Commands & Colors Tactical Portal: Architecture Overview

This document outlines the architecture, technology stack, and functional design of the Commands & Colors Tactical Portal. The system is engineered to be highly modular and service-oriented, ensuring that the core game logic is decoupled from the UI, making it easily extensible to any new commands and color variants.

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
| **Framework** | Angular | Component structure, dependency injection, and UI management. |
| **Language** | TypeScript | Provides strong typing for all game models and logic. |
| **State Management** | NgRx | Centralized, predictable state management for complex, relational game data. |
| **UI/Grid** | CSS Grid / HTML5 Canvas | Rendering the Hex Grid efficiently. |
| **Interactivity** | Angular CDK Drag and Drop | Handling user interactions, such as card placement and movement. |
| **Backend API** | FastAPI (Python) | High-performance API layer handling authentication, REST endpoints, and WebSocket connections. |
| **Real-time State** | Redis | Distributed, persistent storage for game state, user presence, and Pub/Sub messaging for low-latency updates. |
| **Deployment** | Docker & Docker Compose | Containerization for consistent, scalable deployment across development and production environments. |

### Backend & Infrastructure Analysis

The backend is designed for high concurrency and low latency, leveraging a microservice approach:

*   **FastAPI & WebSockets:** FastAPI provides a fast, asynchronous foundation, crucial for handling numerous simultaneous WebSocket connections required for real-time game updates. This decouples the game logic from traditional HTTP request/response cycles.
*   **Redis as the Source of Truth:** Redis is the core persistence layer. It is used not just for simple key-value storage (game state, user data) but also for Pub/Sub (`cnc_room_updates`, `cnc_global_updates`), which enables efficient, broadcast-based communication between the server and all connected clients. This pattern ensures that state changes are immediately propagated to all relevant players without requiring constant polling.
*   **Data Synchronization (JSON Patch):** The system utilizes JSON Patch operations for state updates (`handle_patch_data`). This allows clients to send granular, small updates to the server, minimizing bandwidth usage and ensuring that the server can validate and apply changes safely against the current state version, which is critical for maintaining data integrity in a distributed environment.
*   **Scalable Deployment (Docker Compose):** The infrastructure is containerized using Docker and orchestrated via `docker-compose.prod.yaml`. This ensures that the game server, Redis, and Nginx frontend run in a consistent, reproducible environment, simplifying scaling and deployment across various hosting platforms. The multi-stage Dockerfile optimizes the final production image size by separating build dependencies from runtime requirements.
*   **Background Processing:** Asynchronous tasks (like `prune_orphaned_data_task` and `pending_offers_reaper_task`) are run as background tasks, preventing long-running maintenance operations from blocking the main WebSocket event loop, thus maintaining responsiveness for active players.

---

## 2. Core Data Models

The foundation of the game state relies on strongly typed TypeScript interfaces:

*   **`GameState`**: The central store tracking the current turn, active player, card hands, combat status, and victory conditions.
*   **`Hex`**: Represents a single board tile, storing coordinates (`q, r, s`), terrain type, and elevation.
*   **`Unit`**: Defines block stacks, tracking faction, unit type, current status, and leader attachment status.
*   **`Card`**: The abstract structure for both **Command Cards** (tactics) and **Combat Cards**.

---

## 3. Game Flow: The State Machine

The game loop is managed by a state machine within an Angular Service, enforcing a strict sequence of phases:

1.  **Play Command Card Phase:** Player selects and plays a Command Card.
2.  **Order Phase:** Player selects valid units/leaders based on card restrictions.
3.  **Movement Phase:** Ordered units are moved, validated against terrain and unit movement limits.
4.  **Combat Phase:** Units attack, resolving Line of Sight (LoS), dice calculations, damage, and leader casualty checks.
5.  **End Turn Phase:** Drawing replacement cards and checking for victory conditions.

---

## 4. Logic Services (The Game Engine)

Complex rules are encapsulated within dedicated services, ensuring clean separation of concerns.

### A. `MovementService`
*   **Functionality:** Handles pathfinding and movement validation.
*   **Key Rules Implemented:**
    *   **Terrain Costs:** Logic to halt movement or consume movement points upon entering specific terrain (Woods, Towns, Hills).
    *   **Pass-through Rules:** Enforcing restrictions on movement through friendly vs. enemy units.
    *   **Retreat Logic:** Separate validation logic to ensure units move towards their baseline for retreats.

### B. `CombatService`
This is the core resolution engine, utilizing pure functions for deterministic results.
*   **Line of Sight (LoS):** Implements a hex-grid line-drawing algorithm to determine if ranged attacks are valid.
*   **Dice Modifiers (Pre-Roll):** Calculates modifiers based on:
    *   Unit Type and Range.
    *   Movement penalties (e.g., -1 die if the unit moved and fired).
    *   Terrain penalties (e.g., -1 die for firing into Woods/Towns).
    *   Leader Bonuses (+1 die if an attached leader is present).
    *   Active Card Modifiers (e.g., Bayonet Charge bonuses).
*   **Dice Resolution (The Roll):** Rolls dice and resolves hits based on unit type and defensive modifiers.
*   **Leader Casualty Checks:** Dedicated logic to roll multiple dice to determine if an attached leader is eliminated during combat.

### C. `MoraleService` (Tricorne Mechanic Focus)
This service handles the crucial Morale, Retreats, and Rallying mechanics.
*   **Flag Resolution:** Calculates the retreat distance for every un-ignored Flag based on unit type.
*   **Flag Ignoring:** Logic to determine if a Flag is ignored (e.g., supported by adjacent friendly units, or if a Leader is attached).
*   **Rally Check:** After a unit retreats, this mechanic rolls dice (based on remaining blocks and leader bonuses) to determine if the unit survives the rout or is removed from the board.

---

## 5. Command & Combat Card Architecture

The card system uses the **Strategy Pattern** to define card behavior:

*   **Section Cards:** Logic filters valid targets by checking hex coordinates against the current section's rules (e.g., Left/Center/Right mapping).
*   **Tactic Cards:** These cards modify global `GameState` flags (e.g., setting a global melee modifier) for the duration of the turn.
*   **Combat Cards:** These act as event listeners, triggering specific actions within the `CombatService` at defined points (e.g., `onBeforeDiceRolled`, `onAfterHitsCalculated`).

---

## 🚀 Extensibility for Any Commands & Colors Game

The architecture is inherently designed for rapid adaptation:

1.  **Unit Customization:** New unit archetypes are added by simply defining new configuration objects that map directly to the existing statistical matrix (Move, Dice, Defense).
2.  **Rule Modification:** New game rules (e.g., different retreat distances, unique morale checks) are isolated and implemented by modifying the specific logic within the dedicated **`MovementService`**, **`CombatService`**, or **`MoraleService`**.
3.  **Card Definition:** New commands and tactics are added by creating new classes that implement the `Card` interface and define their specific effects, which interact with the global `GameState` or trigger service functions.

By keeping the state management centralized (NgRx) and the rule logic decentralized (Services), the system remains robust, scalable, and perfectly suited to evolve into any new Commands & Colors variant.
