# Commands & Colors: Tricorne

## A field guide to the digital War Room

**Choose a battlefield. Take command. Watch the front come alive.**

Commands & Colors: Tricorne brings scenario selection, command cards, hex-by-hex maneuvers, combat resolution, and a community of commanders together in one browser-based table. Play against another commander, challenge a computer opponent, or sit back and spectate an engagement already under way.

> **The short version:** Browse historical engagements in the **War Room**, deploy a scenario, then use the battlefield HUD to follow cards, orders, movement, combat, and banners.

> **Found a bug? Please report it.** Use **Report a Bug** in CnC Portal, then see [how to write a useful report and add a screenshot or details later](#8-report-a-bug-and-help-improve-cnc-portal).

<figure>
  <img src="images/userguide-war-room.png" alt="The War Room with navigation, filters, commander presence, and active engagement cards" width="100%">
  <figcaption><strong>The War Room</strong> — find a front to join, inspect a battle, or commission a new scenario.</figcaption>
</figure>

*Screenshots in this guide were captured with Playwright in dark mode using a local demo fixture. Names and sample engagements are illustrative; live games, scores, and available actions vary with server data and account permissions.*

---

## 1. Muster in the War Room

After signing in, the **War Room** is your operations board. The **Your Battles First** area brings your engagements to the top, followed by other fronts in the selected theater. Engagement cards show the scenario, status, commanders, faction markers, and whether the battle is ranked or unranked. Use the controls to make a busy front easier to scan:

- **Grid** gives each engagement its own card; **Split View** pairs a scrollable list with selected-game details.
- Filter by status, including **Recruiting**, **Engaged**, **Awaiting Deployment**, and **Finished**.
- Search by commander or lobby name.
- Open a recruiting battle with **Enlist Now**, enter a battle you are playing, or choose **Spectate** when you are observing.
- The **Active Commanders** panel shows recent commanders and their presence.

In Split View, selecting an engagement reveals its current phase, active faction, banner count, commander matchup, and tactical board preview when that state is available.

## 2. Commission a scenario

Select **New Commission** to open the scenario setup. The preview helps you understand the engagement before deployment: review the forces on each side, see the objectives, or switch from **Terrain Summary** to **Full Layout** for a hex-map view.

<figure>
  <img src="images/userguide-new-commission.png" alt="New Commission dialog showing scenario selection, faction choice, play modes, armies, and objectives" width="100%">
  <figcaption>Compare the scenario’s forces and objectives before you deploy.</figcaption>
</figure>

<figure>
  <img src="images/userguide-scenario-map.png" alt="Full Layout preview of the Bunker Hill scenario, with terrain hexes and unit chits" width="100%">
  <figcaption><strong>Full Layout</strong> previews the terrain, map labels, and starting unit positions.</figcaption>
</figure>

Your setup choices include:

| Choice | What it does |
| --- | --- |
| **Scenario** | Select an available Tricorne engagement. Examples in the scenario list include Bunker Hill, Long Island, Freeman’s Farm, Bemis Heights, Monmouth, and Cowpens. |
| **Lobby name** | Give the engagement a name commanders can recognize in the War Room. |
| **Your faction** | Choose British, Continental, or Random. |
| **AI opponent** | For Tricorne, choose **Play against computer** to command one side or **Watch two AI commanders** to spectate both sides. Choose **AI Difficulty** before deployment. AI games are unranked. See the [AI opponents guide](ai-guide.md) for how the server AI chooses actions. |
| **Ranked or unranked** | Ranked play is for eligible human multiplayer. AI battles are unranked; draws are available only in unranked games. |

When the preview looks right, select **Deploy Scenario**. In multiplayer, other commanders can enlist in a recruiting engagement. Once both sides are present, players can enter the battlefield; other commanders can spectate.

### Ranked, unranked, and ending an engagement

Ranked battles are for human-versus-human play; a ranked lobby waits for a second commander rather than allowing a ranked self-match. Computer-opponent and AI-versus-AI games are unranked. A draw is only available in an unranked two-human match.

The in-game early-end controls distinguish a result from simply leaving the board:

- **Forfeit** is an explicit concession. Confirming it ends the active two-human match, records the forfeiting commander as the loser, and awards the win to the opponent.
- **Propose Draw** is offered only in an unranked two-human match. The game records a draw with no winner.
- **Return to War Room** only navigates away from the board; it does not send a forfeit or draw result. The current live-match controls do not expose a separate **Withdraw** result. Separately, a lobby host can decommission an engagement from the War Room; that is lobby management, not a forfeit result.

Completed forfeits and draws are game results. Results marked incomplete, abandoned, or cancelled are excluded from completed-game analytics.

For a deeper explanation of Easy through Brutal, the AI’s search depth, and how it scores cards, targets, objectives, risk, and follow-up actions, see the standalone [AI opponents guide](ai-guide.md).

## 3. Read the battlefield

The battlefield is organized around the map, with controls in the header, faction dashboards, phase ribbon, map tools, and bottom navigation. The header provides a return to the War Room, the battle title and scenario overview, notifications, the battle menu, and **What Can I Do?** The faction dashboards show each side’s banner progress and command/combat card counts. The phase ribbon shows **Card**, **Order**, **Move**, and **Combat**; the active phase and side are highlighted.

<figure>
  <img src="images/userguide-battlefield-desktop.jpg" alt="Dark-mode Tricorne battlefield with the scenario header, What Can I Do preview button, faction dashboards, phase ribbon, range tools, terrain inspector, and unit chits" width="100%">
  <figcaption>The current desktop table puts the turn, faction status, map controls, terrain inspection, and battlefield on one screen.</figcaption>
</figure>

The **What Can I Do?** control opens a preview of actions the server considers available in the current game state. It helps explain the current options; it does not resolve a move or combat. The server still validates each action when you submit it. The exact choices depend on your role, the active phase, open decisions, and the selected unit.

The battle header also opens the scenario overview. It shows the historical setup, victory goal, and scenario conditions before play.

<figure>
  <img src="images/userguide-battle-overview.jpg" alt="Dark-mode scenario overview for Freeman’s Farm showing its historical briefing, seven-banner victory goal, attrition condition, and Ready to Play control" width="100%">
  <figcaption>Open the scenario overview from the battle header to revisit the briefing and victory conditions.</figcaption>
</figure>

These screenshots use a local Freeman’s Farm demo fixture. The available controls in a live battle depend on your role, phase, selected unit, and the server-provided options.

### Follow the turn

The turn ribbon guides players through **Card**, **Order**, **Move**, and **Combat**; **Move First** can appear when the scenario calls for it. The active side is shown beside the board. The faction dashboards track each side’s banner score and card piles, while the victory tracker shows progress toward the scenario’s requirement.

### Play cards, then command units

Open **Hand** to browse your permitted cards, then play a legal card for the current phase. Command cards shape which units or sections can be ordered; combat cards and scenario abilities can matter during an engagement. The hand supports scrolling and card selection. A contextual menu can make card actions available on touch devices where applicable. **Card Advisor** suggestions appear when supported by the current card and battle context.

### Inspect units, terrain, and actions

Select a unit or stack to inspect its classification, remaining blocks, movement and combat values, traits, and current status. Selecting a hex shows its terrain and coordinates; the terrain inspector can also show path cost, whether movement stops there, and whether it is impassable. When access is available, the inspector can link to **Review Terrain Chart**. As you plan a route, the board shows its movement path.

The **What Can I Do?** preview lists server-generated legal options for the current state. It is useful for checking available actions before you commit. Previewing an option does not order a unit, move it, or begin combat.

#### Map tools

The desktop map toolbar currently offers **Move Range**, **Attack Range**, and **Clear**. **Move Range** previews destinations for the focused unit; **Attack Range** previews its available targets. **Clear** removes the current map preview. Separate **Hide Bars** and **Dice** controls are also available. These visual previews help with planning; the server remains the authority for action legality and results.

**Fire Lanes** is conditional: it appears only when the current server preview supplies lane data. It is not available in every game state and is not shown in the current desktop screenshot.

On a tablet-sized layout, the map controls collapse behind **Map Tools** instead of showing the desktop toolbar. Unit and hex inspection and the bottom navigation remain available.

For more context, map settings can show coordinates, map-point labels, terrain effects, unit identifiers, line-of-sight highlights, and faction outlines. A terrain legend and action-highlighting key explain the map’s visual cues. Sound and dice animation are optional.

### Resolve combat together

During an engagement, the combat panel identifies the attacker and defender and shows the dice breakdown and relevant modifiers. The server resolves the roll and records the result. The interface presents it and guides players through required decisions such as a defender’s **Retire and Rally** choice, leader casualty checks, retreats, or follow-up actions. The board updates after the required steps are complete. Spectators can follow the engagement without taking the players’ actions.

## 4. Use the battlefield panels and history

The bottom navigation keeps **Hand**, **Chat**, **History**, and **Audio** available beside the map. Open **Hand** for your permitted cards, **Chat** for the game conversation, and **History** to review recorded events and any replay or battle-review controls available for that game. Recorded replays use saved results; they do not reroll combat. The **Classic** control switches to the alternate battlefield presentation.

The War Room has lobby chat and separate channels for games you are part of. Unread badges help you spot new messages. In a chat, commanders can send messages, edit their own messages, review edit history, and pop the conversation out. Browser notifications can be enabled when supported. Game chat can stay beside the battlefield while you play.

## 5. Follow commanders and scenarios between battles

The left navigation connects the battle browser to the broader commander community:

- **Service Record** summarizes wins, losses, draws, games played, faction results, military rank, XP, TrueSkill, conflict totals, and battle history. Search for another commander’s profile from this view.
- **Headquarters** groups available scenarios and shows active and completed engagements. Where results exist, it also presents side win rates and historical games for a scenario.
- **Social** opens the application’s social hub for friends, regiments, and tournament organization.
- **Active Commanders** gives a quick view of recent commanders and presence.

### Friends, regiments, and tournaments

The **Friends** panel lets you search commanders by username, send friend requests, accept or decline incoming requests, and remove friends. **Regiments** let commanders create a named group with an optional description, join or leave groups, and transfer command to another member. Regiment participation can also be used when organizing a tournament.

**Tournaments are a work in progress.** The current Social hub includes the foundations for single-elimination events with either individual players or regiments: create an event, register a player or one of your regiments, let the organizer start it once there are enough participants, review rounds and byes, and verify a match by linking its completed game ID. The bracket advances from the verified result and can display a champion. Treat this as an evolving organizer feature rather than a finished tournament service.

Presence, lobby chat, and game-specific chat make it easier to find and coordinate with other commanders before and during a battle.

<figure>
  <img src="images/userguide-social-hub.png" alt="Social and Competition hub with Friends, Regiments, and Tournaments panels and a single-elimination bracket" width="100%">
  <figcaption>Search for commanders, build a regiment, or explore the tournament tools as they continue to evolve.</figcaption>
</figure>

<figure>
  <img src="images/userguide-headquarters.png" alt="Headquarters scenario operations page grouped by Tricorne theater, with active and completed game counts" width="100%">
  <figcaption><strong>Headquarters</strong> — scan scenario activity and open historical results when they are available.</figcaption>
</figure>

<figure>
  <img src="images/userguide-service-record.png" alt="Service Record page with commander search, personal results, rank progression, and conflict overview" width="100%">
  <figcaption>The Service Record brings personal results and wider conflict progress together.</figcaption>
</figure>

Open a commander’s full profile for an expandable dossier: personal contributions, faction results, rank progression, TrueSkill history, conflict overview, and active or completed battles. A profile can also provide commander preferences for the next engagement and local visual choices for the board.

<figure>
  <img src="images/userguide-commander-profile.png" alt="Expandable commander profile showing settings, personal contributions, progression, conflict overview, and battle history" width="100%">
  <figcaption>Expand only the profile sections you want to review.</figcaption>
</figure>

### Make the board your own

Visual extras make the map easier to read while adding a little battlefield atmosphere:

- **Fallen-unit overlays** place casualty markers on hexes where units have taken losses. Toggle them on or off in commander settings.
- **Block-by-block sprite wear**: the default multi-row unit sheets select damage artwork according to a unit’s remaining blocks, so a weakened formation looks different from a fresh one. The block indicator beside the chit and the unit inspector give you the numbers too. Choose **Legacy — one-row appearance** if you prefer the older, uniform look.
- **Action overlays** animate battlefield activity such as marching, firing, retreating, or taking fire. Turn them off if you prefer a quieter map.
- **Faction unit sheets** can be set independently for British, Continental, Hessian, and French units. Use the bundled damage-variation sheet, the legacy sheet, or a custom sheet you upload/build.
- **Terrain variations** switch between the default and classic sheets.

<figure>
  <img src="images/userguide-visual-settings.png" alt="Commander profile appearance settings for fallen-unit overlays, action animations, faction unit sheets, and terrain variations" width="100%">
  <figcaption>Board appearance options live in the commander profile and can be previewed without changing the shared game state.</figcaption>
</figure>

Custom unit sheets are PNGs stored in this browser; the builder supports the faction sheet layout, and the profile accepts one-row or four-row sheets. These appearance choices are local to the browser, not sent to the game server. Per-game overrides can temporarily show or hide fallen-unit markers and action animations for one battle without changing your profile defaults.

## 6. TrueSkill and competitive integrity

### A rating that includes uncertainty

The Service Record and commander profile display **TrueSkill** as a competitive skill estimate. The profile shows the mean rating (**μ**) and uncertainty (**σ**), plus a conservative score of **μ − 3σ**. That conservative estimate is used in the displayed military-rank progression alongside earned XP; rating and rank are related, but they are not the same number.

Ratings are based on completed, eligible human-versus-human ranked results. Computer games and self-play do not count as rated matches. Team outcomes and draws are accounted for, and the profile can chart rating history over selectable time windows. XP progression separately reflects match contributions such as units taken, victory points, objective points, and a win bonus.

### Rating-integrity review, not an automatic verdict

The application includes a **rating-integrity review** for patterns that could indicate competitive rating manipulation. It looks across recent rated results for combinations of signals—for example:

- the same opponents meeting repeatedly, with results strongly favoring one account;
- repeated forfeits that transfer results to the same opponent;
- unusually imbalanced accepted game-state activity;
- repeated low-scoring losses compared with that commander’s history in comparable scenarios and factions; or
- several accounts repeatedly transferring wins to one recipient through forfeits or very low-scoring losses.

A review flag is a reason for an administrator to look more closely, **not proof of cheating**. A single surprising result does not by itself establish a pattern. The review queue is restricted to administrators, and the interface describes the process as review-only: eligible results remain rated while a flag is being reviewed. Administrators can dismiss a flag, mark a recurring matchup as expected, or exclude/restore a result’s rating with a recorded reason. The underlying game result remains part of history.

This is a safeguard for competitive rating integrity, not an automatic gameplay ban system.

## 7. Play comfortably on a smaller screen

The game includes tablet mode, touch-oriented map tools, inspection panels, and a hand designed for swipe or scroll navigation. On touch layouts, select a unit or hex to open contextual actions such as inspect, edit, select a unit or stack, and attack when available. The **Install** control appears when the browser offers app installation.
<figure>
  <img src="images/userguide-battlefield-tablet.jpg" alt="Dark-mode tablet battlefield with compressed faction dashboards, phase ribbon, Map Tools button, hex board, and Hand, Chat, History, and Audio navigation" width="100%">
  <figcaption>On tablet layouts, map controls collapse behind **Map Tools** while the main battlefield navigation stays in reach.</figcaption>
</figure>

For a clearer view on any screen, open **Map Settings** and enable only the map details you need. The tablet-mode control and **Hide bars** option provide additional space for the battlefield.

## 8. Report a bug and help improve CnC Portal

Bug reports help improve the rules, interface, and reliability of the portal. If something behaves unexpectedly, use **Report a Bug** in CnC Portal. A useful report lets another person understand what happened and, when possible, reproduce it.

1. Give the report a short, specific **Title** and describe the problem.
2. Add the steps that led to it. Include the scenario, game phase, relevant units or cards, and what you clicked or selected when those details matter.
3. Describe what you expected and what happened instead. Choose the closest **Severity**.
4. Leave **Try to attach a screenshot** selected if a picture would help, then select **Submit Report**.

The portal may include available game context, recent history, browser details, and synchronization diagnostics with the report. Screenshot capture and automatic attachment during issue creation are a **work in progress**: the portal will try, but a screenshot may not be captured or appear on the GitHub issue. The report can still be submitted without one.

After submission, select **Open issue** to view the public GitHub issue. If you do not see that link, use **View Bugs** in the report panel and look for the issue by its title. You can also [open the public issue tracker](https://github.com/rmroc451/commands-and-colors-issues/issues) directly. If the portal cannot submit your report, use GitHub’s [bug report form](https://github.com/rmroc451/commands-and-colors-issues/issues/new?template=bug_report.md).

### Add details or a missing screenshot to an existing issue

You do not need to create a duplicate issue to add evidence later:

1. Open the issue using **Open issue** or find it in the issue tracker.
2. Sign in to GitHub if prompted, then go to the comment box at the bottom of the issue.
3. Add any extra reproduction details. To add a screenshot, drag the image into the comment box or paste it there, wait for GitHub to finish uploading it, and then post the comment.

Issues in this tracker are public. Before submitting, check that your description and screenshot do not expose passwords, private messages, or personal information.

## A quick first sortie

1. Sign in and open **Active Fronts**.
2. Browse recruiting battles, or select **New Commission** to choose a scenario.
3. Compare the armies and objectives; open **Full Layout** to study the starting map.
4. Choose a faction and game mode, then deploy.
5. Follow the turn ribbon, play a card, order units, and use the map previews to plan movement and attacks.
6. Review combat results, required decisions, and the History panel as the engagement unfolds.

> **Availability note:** Actions depend on your role in the engagement. Players receive command controls for their side; spectators can observe. Scenario statistics and commander records grow as completed results are recorded.

---

*A digital tabletop for Commands & Colors fans: historical scenarios, clear tactical feedback, shared combat resolution, and a War Room that keeps the community connected.*
