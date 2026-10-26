# D&D Map Planning Workbook: Requirements, Evidence, Review, and Handoff

A useful encounter map is more than an attractive image. It is a working model of space that helps a game master answer practical questions: where can characters move, what can they see, which routes create meaningful choices, and how does the environment change during play? A dependable map-making process therefore starts with encounter requirements and ends with a table-ready export that another person can understand.

This is an independent technical planning guide. DnD Map Maker is the AI-assisted project associated with the article; generated drafts require human review. The examples below are synthetic and demonstrate a portable metadata contract, not an API or native export provided by the product.

This guide presents a repeatable workflow for designing dungeon rooms, wilderness sites, buildings, and battle maps. It works whether the final image is drawn manually, assembled from assets, or drafted with an AI-assisted tool such as [dnd map generator](https://dndmapmaker.com/). The important principle is to keep the design decisions reviewable. Generated imagery can accelerate exploration, but the game master remains responsible for scale, access, clarity, tone, and the final playable result.


## Represent the brief as a small data contract

A structured brief makes assumptions inspectable before any image is generated. This JSON is intentionally tool-neutral. It is not a claim about a native export format.

```json
{
  "mapId": "flooded-observatory",
  "grid": { "type": "square", "cellFeet": 5, "columns": 28, "rows": 20 },
  "requiredZones": ["south-entry", "central-dais", "north-gallery"],
  "routes": [
    { "from": "south-entry", "to": "central-dais", "widthCells": 2 },
    { "from": "central-dais", "to": "north-gallery", "widthCells": 1 }
  ],
  "hazards": [
    { "zone": "central-dais", "kind": "rising-water", "telegraphed": true }
  ]
}
```

A lightweight validator can reject incomplete briefs before a person spends time reviewing a visual draft:

```javascript
function validateMapBrief(brief) {
  const errors = [];
  if (!brief.mapId) errors.push("mapId is required");
  if (!Number.isInteger(brief.grid?.cellFeet) || brief.grid.cellFeet <= 0) {
    errors.push("grid.cellFeet must be a positive integer");
  }
  if (!Array.isArray(brief.requiredZones) || brief.requiredZones.length < 2) {
    errors.push("at least two required zones are needed");
  }
  const zones = new Set(brief.requiredZones || []);
  for (const route of brief.routes || []) {
    if (!zones.has(route.from) || !zones.has(route.to)) {
      errors.push(`unknown route endpoint: ${route.from} -> ${route.to}`);
    }
    if (!Number.isInteger(route.widthCells) || route.widthCells < 1) {
      errors.push("route widthCells must be at least 1");
    }
  }
  return errors;
}

const errors = validateMapBrief(exampleBrief);
if (errors.length) throw new Error(errors.join("\n"));
```

The contract does not prove that a map is playable. It only turns several important requirements into explicit, testable values. Visual inspection and tabletop judgment remain necessary.

## 1. Write a short encounter brief

Before drawing walls, summarize the scene in five or six lines. Record the purpose of the location, the expected party size and level, the likely opposition, the desired mood, and the main decision the map should create. A ruined observatory might ask the party to choose between a visible central staircase and two slower side routes. A smugglers' cave might revolve around tides, narrow ledges, and boats that can be moved.

The brief prevents decorative details from taking control of the design. It also gives you a stable reference when revising a generated image. If a beautiful draft does not support the intended choice, change the draft rather than rewriting the encounter around an accidental feature.

## 2. Establish scale and the playable boundary

Choose the grid scale early. Five feet per square is common for character-scale combat, but larger outdoor scenes may need ten-foot squares or a gridless measurement system. Write the choice in the map notes. Then mark the playable boundary and distinguish it from background scenery.

Check whether doors, corridors, stairs, bridges, and furniture fit the creatures expected in the scene. A corridor that looks wide in an image may represent only one square after export. Large creatures need turning space, while ranged encounters need enough distance for movement and cover to matter. If the location will be used online, test the exported image in the target virtual tabletop before finalizing details.

## 3. Design routes before decoration

Sketch a simple route diagram using boxes and lines. Each box is a meaningful area; each line is a connection. Most tactical locations benefit from at least two routes between important areas. Alternate routes may involve risk, time, noise, locked doors, vertical movement, or resource costs.

Avoid making every room a dead end unless the story calls for it. Loops let players flank, retreat, investigate, and form plans. Choke points are useful when they are intentional, but a map composed only of narrow choke points can make every encounter feel similar. Combine open areas, protected positions, and transitional spaces.

At this stage, label entrances and exits clearly. Include routes opponents might use even if the characters do not initially know about them. Secret passages should still make physical sense within the building or terrain.

## 4. Add elevation and sight lines

Elevation often creates more tactical variety than additional floor clutter. Platforms, balconies, pits, ramps, staircases, and ledges can change movement and visibility without making the map hard to read. Note the height of each level and how characters move between levels.

Trace several likely lines of sight from entrances and important positions. Decide which obstacles provide full cover, partial cover, concealment, or no meaningful protection. Tall objects should cast readable shadows or receive clear labels so that players do not mistake them for flat floor textures.

For complicated multi-level locations, produce separate floor images or a numbered diagram. Trying to show every level in one top-down image can create ambiguity. A concise legend is better than relying on visual style alone.

## 5. Place interactive terrain

Interactive features turn scenery into decisions. Examples include doors that can be barred, chandeliers that can fall, unstable bridges, flood controls, movable carts, spreading fire, ritual circles, fragile windows, and machinery with visible controls. Each feature should have a plausible effect and a clear location.

Record the rule or ruling separately from the image. The map may show a cracked pillar, but the encounter notes should state what happens if it collapses. This keeps the art reusable and prevents hidden mechanics from being lost when the image is shared with another game master.

Use restraint. Three memorable interactions are often more useful than fifteen tiny props. Dense decoration can hide movement boundaries and make tokens difficult to see.

## 6. Create or generate the visual draft

Once the functional plan is stable, create the visual draft. If using an AI-assisted map generator, describe the viewpoint, environment, room arrangement, lighting, material palette, and required open spaces. Request a top-down composition and avoid instructions that depend on exact text appearing inside the image, because generated labels may be unreliable.

Treat the first result as a draft, not as evidence that every requirement has been met. Compare it against the route diagram and encounter brief. Check entrances, room connections, scale cues, and unwanted obstructions. Revise selected areas when possible instead of repeatedly regenerating the entire map, because local revision preserves parts that already work.

Do not assume a generated feature has a specific game effect. Decide the mechanics yourself and write them in the encounter notes. Also review the result for accidental symbols, malformed objects, inconsistent stairs, impossible shadows, or doors that open into solid walls.

## 7. Run a movement audit

Simulate at least three turns of movement from each entrance. Move representative tokens through doors, around corners, and across elevation changes. Confirm that the intended routes are actually usable at the chosen scale.

Ask practical questions. Can a token stand on both sides of a door? Is there space to pass an ally? Can a large creature leave its starting area? Does a bridge connect cleanly to walkable ground? Are diagonal gaps truly passable? If difficult terrain exists, can players identify its boundary?

A movement audit catches problems that are easy to miss in a static image. It also reveals whether the map is too large for the expected encounter duration. Long empty approaches can consume table time without adding decisions.

## 8. Review information clarity

Separate what players can see from what only the game master knows. The player map should not reveal secret doors, trap triggers, hidden creatures, or private labels. Keep a second annotated version or a lightweight key containing numbered locations.

Check contrast at the actual display size. Tokens should remain visible over floors, shadows, vegetation, and furniture. Doorways and walls need recognizable boundaries. If the map uses a grid, confirm that grid lines are visible without dominating the art. A gridless version can also be useful for systems with free measurement.

Include a small legend only when symbols are not self-explanatory. North arrows, scale bars, elevation labels, and numbered markers are more useful than decorative titles covering playable space.

## 9. Perform a table test

The fastest quality check is a short mock encounter. Place the expected number of heroes and opponents, then play through the opening round. Test one melee route, one ranged position, and one environmental interaction. You do not need to resolve the whole battle.

Observe whether the map creates the choice described in the original brief. If every sensible move leads to the same square, add another route or objective. If one position dominates without a counter, adjust cover, access, or elevation. If players would spend several turns simply walking, compress the layout or add meaningful events along the way.

For exploration maps, test navigation instead of combat. Can a player describe where they are? Do landmarks distinguish similar corridors? Are clues placed where they can reasonably be noticed?

## 10. Export with a handoff note

Export a high-resolution master and a practical table version. Use a common format such as PNG or WebP, and keep the editable source if the tool provides one. Name files consistently, for example `observatory-player-v1.png`, `observatory-gm-v1.png`, and `observatory-notes-v1.md`.

The handoff note should state the grid scale, image dimensions, intended virtual tabletop size, numbered locations, elevation rules, and any environmental mechanics. Mention whether the map is final or still under review. If an AI-assisted draft was used, say so plainly when that information matters to collaborators or the publication context.

Finally, open the exported file rather than trusting the export dialog. Confirm that it is not cropped, blank, unexpectedly compressed, or missing the latest revision. Import it into the intended tabletop software and verify grid alignment there.


## Requirements matrix

| Requirement | Evidence to record | Review question |
|---|---|---|
| Playable boundary | Width, height, and scale | Can players understand where movement is allowed? |
| Required zones | Named room or area list | Is every encounter function represented? |
| Routes | Connected zone pairs | Are primary and alternate paths readable? |
| Hazards | Location and telegraphing note | Can the game master explain the risk fairly? |

## Connection review table

| From | To | Width | Purpose |
|---|---|---:|---|
| South entry | Central dais | 2 cells | Main approach and group movement |
| Central dais | North gallery | 1 cell | Constrained route and tactical choice |
| South entry | Side chamber | 1 cell | Optional exploration path |

## Handoff checklist

| Item | Owner | Complete when |
|---|---|---|
| Grid and scale | Map reviewer | Tokens align with expected movement |
| Route clarity | Encounter designer | Entrances and connections need no guesswork |
| Export check | Table operator | File opens at the intended size and format |
| Privacy review | Project owner | No private campaign or identifiable data remains |

## Compact checklist

Before using the map, confirm the following:

- The encounter purpose and main decision are written down.
- Scale, entrances, exits, and playable boundaries are clear.
- Important areas have intentional routes and usable connections.
- Elevation and sight-blocking objects are understandable.
- Interactive terrain has separate, explicit rules.
- Generated or drawn visuals match the functional plan.
- Representative tokens can move through the location.
- Player and game-master information are separated.
- Contrast and token visibility work at actual play size.
- Exported files open correctly and align in the target tabletop.

A map that passes these checks does not need to be visually elaborate. Its value comes from supporting clear choices, understandable movement, and a smooth handoff from preparation to play.
