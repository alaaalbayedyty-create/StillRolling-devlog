# Dev log

## Pre-jam setup (declared, before the contest period)

Nov 11, 2026. No game content exists yet. Only the following was prepared:

- Freed disk space for Unreal Engine 5.9 MegaJam Preview.
- `.mcp.json`: Claude Code connection to the Unreal MCP server in the editor.
- `CLAUDE.md`: jam rules, deadlines and working notes for the coding agent.
- `AGENTS.md`: how several coding agents share the project (roles, file ownership, editor lock).
- `.gitignore` for Unreal projects and an empty git repository.
- A scheduled Claude Code task that starts the work at the official contest start.
- `Tools/unreal_mcp.py`: a small command-line client for the editor's MCP server (a script, no game content).
- Installed Unreal Engine 5.9.0 Preview and tested the MCP connection on an empty scratch project
  kept outside this folder (`Documents\Unreal Projects\MCPTest`). Nothing from it is used in the game.

- `Tools/build.ps1`, `start_editor.ps1`, `package.ps1`, `editor_lock.ps1`, `common.ps1`: build,
  launch, packaging and locking scripts. The scratch project was used to test that C++ compiles
  (144 s) and that a Windows package builds and runs (5.3 min, 447 MB zipped while empty).

- Nov 12, 01:25 to 01:50 Jordan time (before the start): re-read the Official Rules (unchanged
  since Nov 5) and the jam page, and confirmed the theme, "Director's Cut", from the official
  kickoff stream. Wrote the theme, the rule findings and a few lines of concept direction (ideas
  only) into `CLAUDE.md`, and switched the packaging script's default to a Development build as
  the jam page asks. No project, code, level, art or audio was created.

Tools planned: Unreal Engine 5.9 MegaJam Preview, Unreal MCP, Claude Code, Tripo, Meshy.

## Jam log

Entries start on Nov 12, 08:00 Jordan time.

### Day 1, Nov 12 (Jordan time)

- 08:01. The scheduled Claude Code session (model: Claude Fable 5.2) started by itself as the
  **lead** agent. Checked the clock, re-read the jam page and the change log of the Official Rules
  PDF in the built-in browser (still "Last updated: November 5, 2026", nothing changed).
- 08:04. First commit: the declared pre-jam files only.
- 08:07. Lead wrote the project skeleton by hand as text (uproject, targets, module, config) and
  the one-page `DESIGN.md`. Game: **Still Rolling**. Theme use: you assemble the Director's cut
  yourself (more reels than slots, every spliced scene comes alive on the set), the stage runs on
  his "Action" and "Cut" calls, and the cut you chose decides the ending.
- 08:10. First compile of the empty module with Unreal 5.9: OK in 134 s.
- 08:12. Lead started two background agents (Claude Code subagents, same model), each with its own
  folders from `AGENTS.md`: **CORE** (C++ gameplay contracts, then the solo loop) and **WORLD**
  (blockout of Soundstage 9 through the Unreal MCP server). One editor and one compiler are shared
  through a lock file.
- 08:25 to 08:45. Lead queued six text-to-3D models in **Tripo Studio** (H3.1, 55 credits each, 8K
  texture off; balance 3000 -> 2670), driving the site through the built-in browser. Our own
  AI-generated work, prompts written by the lead:
  | Asset | Tripo task id |
  |---|---|
  | The Director (coat, film-light head) | 87fddaf2-66bd-406d-9f8b-2f297804976d |
  | Vintage film camera on tripod | 21361808-5bca-4d00-a3ed-d5489c30f1b1 |
  | Film reel canister (the collectible) | 06e828fa-8a6d-4a15-83c0-92ede39b22b4 |
  | Faceless mannequin (the Extras) | 733e13d9-ea92-4f18-901a-00d11e4e9743 |
  | Fresnel stage light on stand | 61ed10ac-c73b-4e46-aaf1-aff5f266d07a |
  | Film editing table (the splicer) | 0fad6e78-7edc-4a57-9b36-f17d4c8a9bb3 |
- 08:24. WORLD finished the Stage9 blockout in about 15 minutes by writing a re-runnable editor
  Python script (`Tools/world/stage9_build.py`) and running it through the MCP `execute_python` tool:
  141 pieces, 93 markers, navmesh. Lead checked its top-down capture and committed it.
- 08:28. CORE wrote `INTERFACES READY`: the whole gameplay contract compiles (game state and mode,
  noise subsystem, first-person character with Enhanced Input built in C++, reels, splicer, doors,
  hiding spots, scene-reactive and redress components). Lead committed it and started the third
  agent, **CREATURE** (the Director's AI, the playback system, set pieces).
- 08:20 to 08:35. The Director in Tripo, by the lead: retopology to 18,306 triangles (35 credits),
  auto-rig on the UE5 mannequin skeleton (20 credits), five preset motions retargeted for free
  (idle, walk, run, look_around, slash), exported as one GLB with in-place animation. The props came
  out at about 2 million triangles each, so the lead wrote `Tools/lead/decimate_glb.py` and decimates
  them with headless Blender 5.2 before import.
- 08:34. WORLD's first lighting pass committed: practical lights, haze, grain, vignette, striped
  wallpaper on the set faces, bare timber on the backs of the flats.
- 08:40. Second Tripo batch (55 credits each): rotary telephone 6438479d-8849-4eae-9b50-928f321db25d,
  wardrobe locker 350edc04-b7b6-486c-a26e-9192cf0ff7ce, PA horn speaker
  aeeeb4f4-1dce-4ce5-b63a-d8775679a5a7, director's chair 555280c3-884f-4fe4-a2a7-5e12da136ed5,
  crew member (co-op body) dd3dd3c9-a12a-438a-bcea-e50a6ec976bb.
- 08:55. CREATURE: the Director hunts on Stage9 in a headless scripted run (20 of 20 checks): he hears a knock and
  investigates, hears a sprint and calls Action, chases, catches, calls Cut, resets, searches, and drags a
  player out of a locker he watched them enter. Playback speakers, stage lights, phones and mannequins
  are written (11 of 12 checks). CORE: the solo loop passes 52 checks with real key input.
- 08:55. AUDIO (fourth agent, started when WORLD went idle) synthesized 120 sounds with numpy, no downloads:
  room tone, projector, footsteps, doors, stingers, thunder, and processed placeholder PA and crew voices.
- 09:02. Lead imported the second Tripo batch. Lesson of the hour: all agents share one C++ module, so a
  half-written file in one folder (a header without its .cpp) blocked everybody's build twice.
- 09:05. Plan usage: 17% of the weekly limit after one hour with three agents. Lead narrowed every agent to
  its current step and wrote `NEXT.md`. Not done yet: nobody has seen the game rendered, and the day-one
  test package is still to do.
- 09:07. AUDIO done: 120 SoundWaves imported and a `USRAudioDirector` subsystem plays them from game events
  (checked by log lines only: 120 loaded, 0 missing; no agent can hear, so the owner has to listen).
- 09:15. WORLD dressed Stage9 with the generated props and placed CREATURE's speakers, phones, stage lights
  and mannequins on the markers.
- 09:22. FRONT (fifth agent): slate HUD, ACTION / CUT / BACK TO ONE cards with a real blackout overlay, end
  screens, pause and main menu, all Slate in C++. It proved them with a scripted tour that saves a
  screenshot and a widget dump of each screen, and fixed white text lost in the light after reading its own PNGs.
- 09:25. First frames from the player's eyes. Lead saw the camera light clip the whole cone to white; CORE
  changed the falloff and intensity and re-shot. Lead also saw the generated props render speckled; WORLD
  is on it.
- 09:33. CORE: 52 checks pass on Stage9 itself, and a listen server plus one client on 127.0.0.1 pass 7
  replication checks (two-player scaling row, client pickup and splice through server RPCs, phases, door,
  the redress seed matching on both machines).
- 09:41. **Day-one package**: `Tools/package.ps1` built the Windows Development package in 7.1 minutes,
  `Alaa_StillRolling.zip` is 505 MB. The packaged exe ran the UI tour to the end: menu, Stage9, 120 sounds
  loaded, the Director's mesh and five animations loaded, exit code 0.
- Agents and tools so far: lead, CORE, WORLD, CREATURE, AUDIO, FRONT are all Claude Code agents on Claude
  Fable 5.2. Unreal MCP (editor Python through `execute_python`), Tripo Studio (models, retopology, rig,
  motions), Blender 5.2 (decimation), numpy (sound synthesis), Windows offline voices (placeholder lines).
- Failed or unverified at this point: no human has played or listened; mouse input, host and join by IP on
  two real machines, the mannequin turn-to-face check, door swing directions, frame time on the RTX 4050.
- 09:50. WORLD traced the speckled props to the lead's Blender decimation (damaged UVs and winding), made a
  two-sided prop material that removes the holes, and applied CORE's level fixes. The flecks remain; the
  plan is to redo the low-poly meshes with Tripo's retopology, which kept the Director clean. End of the
  kickoff session: five agents ran, everything is committed, `NEXT.md` has the exact state.
- 09:52. The owner told the first chat session to carry on; it took over as **lead** (same model). It
  looked at the game's own screenshots for the first time: flat grey surfaces, a hard white disc for the
  camera light, speckled props. Visual quality became the job of the hour.
- 09:58. Lead started one agent, **LOOK** (lighting, camera light, fog, post, surfaces, the Director on
  screen, frame time), and worked in the browser itself in parallel.
- 09:58 to 10:08. Lead redid all nine props with **Tripo's own retopology** (Triangle, 20,000 target,
  35 credits each, 315 in total; balance 2,340 -> 2,025) and exported them as GLB with 2K textures into
  `SourceArt/Retopo/`. Results: 17k to 20k triangles each instead of about 2 million, UVs intact. The
  Blender decimation step is dropped. New helper `Tools/lead/collect_downloads.ps1` copies exports out of
  Downloads by the name stored inside the file and checks the GLB length.
- 10:05. Lead began PBR surface materials in **PBRgen** (text prompt -> start texture -> 2K PBR set),
  first the soundstage floor.
- 12:04 to 12:40, Nov 12. **INTEG** (Claude Code agent, Claude Fable 5.2): checks green, one visible bug, new
  package, packaged build compared with the editor frames.
  - AutoRun was 49 of 52 because the test stood the player inside the west wall next to the PropStore reel.
    `FSRAutoRun::Build` now picks a side of the reel where a player's capsule is free and the eye sees the
    reel (`FindReelStandPoint` in `SRDebug.cpp`; it chose the east side, 0 cm push-out) and aims in a later
    step from the real eye position. The three checks themselves are unchanged.
  - The reel label read mirrored from one side. `ASRReel` now turns its label to the local camera every frame
    on each machine (absolute rotation on the component, nothing replicated), text size 9 -> 6, a darker
    paper colour, fades from 3.5 m and is hidden from 6.5 m. Frames: `Saved/LookShots/integ_label_*.png`.
    Under the camera light at 2 m the text still burns to white like every pale surface there.
  - `-SRSetPieceTest` failed 11 of 12 on a new line: Stage9 now has eight speakers of its own and the server
    correctly used one of those (7.8 m from the player, 18.4 m from the Director), while the test demanded
    its own spawned speaker. The test now judges the speaker the server really used.
  - Checks on the final code: AutoRun on Stage9 **52 of 52** (CheckLevel 0 problems), set pieces **12 of 12**,
    net test **7 of 7** (two processes, CoreTest map), Director test **20 of 20** twice and **18 of 20** once.
    The failed run: placed 8.5 m in front of a lit player he stood 2.6 s and walked off without seeing them
    (`Saved/Logs/integ_director_fail_1223.log`); the sight line is clear in the level, and his listening
    sweep (60 degrees each way, 24 degree sight cone) can keep the cone off the player for about 2.6 s. Not
    changed; the cause is likely, not proven.
  - Package: 3.3 minutes for build, cook and stage (incremental), `Alaa_StillRolling.zip` **490 MB**
    (796 MB unpacked). Packaged exe: UI tour exit code 0 in 54 s.
  - Look tour inside the packaged exe at 1920x1080 (`Saved/LookShots/packaged_*.png`): Stage to the Parlor
    set, Backstage W and the Edit Bay match the editor frames (the mean colour of each frame differs by at
    most 0.2 on the 0 to 255 scale; haze, practicals, textures, props and the projector gate are all there). The Director, his coat, lamp head,
    beam shaft and the beam's light function are there too; his pose differs from the editor frame because
    he is AI-driven (in one frame he looks at the wall, in the other he has seen the player, called Action
    and is running at the camera). Nothing was missing in these five frames; other rooms were not compared.
  - Packaged frame rate, RTX 4050 Laptop, 1920x1080 exclusive fullscreen: Stage with the camera light on
    **63.5 FPS** (worst frame 22.6 ms), Action with the stage lights 61.5 FPS, Director in view 65.4 FPS.
  - Seen on the way, not fixed: with `-nosound` the audio code logs "loop ... started (no audio device)"
    several times per frame (25 MB of log per minute of test); the packaged game logs 17 engine warnings
    about MCP toolset manifests at start (plugins enabled in the project).

- **Nov 12, 12:45 to 13:20, DRESS agent (cut short by the lead: the level is to be rebuilt as a house).** Nine furniture
  props (sofa, armchair, bed, kitchen table, coffee table, road case, crate, projector, generator; Tripo retopology,
  our own work) imported into `Environment/Props` and listed in `Docs/agents/ART_manifest.md`; none placed. The
  Director now notices a player whose camera light is on anywhere in a 120 degree arc in front of him within 14 m
  after 0.7 s of clear view (`SRDirectorAIController`); a player in the dark keeps the old narrow rule. Director test
  **20 of 20 in six runs out of six** (was 18 of 20 about one run in three). Stage lights at Action: cone 44 -> 30
  degrees, 650 -> 300 cd, more haze, so the floor stays mid-grey in separate pools (`Saved/LookShots/dress_action_*.png`).
  The `-nosound` audio log line is written once per loop (a test log went from 26.7 MB to 0.2 MB). Checks on the final
  build: AutoRun on Stage9 52 of 52 (CheckLevel 0 problems), set pieces 12 of 12, net test 7 of 7. No package made.
  Details: `Docs/agents/DRESS_progress.md`.

- **Nov 12, 13:00 to 14:30, HOUSE agent: the level rebuilt as an abandoned house (owner's request after playing the
  first package).** New map `House9`, generated by `Tools/world/house_build.py`, `house_gameplay.py` and
  `house_dress.py` from coordinate tables (2,049 actors); the soundstage map `Stage9` is untouched and no longer the
  default. A two-storey house, 16 x 12 m, with a cellar, in a fenced yard at night: walls built as two skins so each
  room has its own wallpaper, ceilings, skirting, cornices, door casings, window frames with glazing bars, two
  staircases with banisters, a porch, a gable roof. The player starts at the garden gate (the exit), walks the path
  to the open front door; a side path leads to the kitchen door and to an open-air stair down to the cellar's outside
  door (the FireExit scene unlocks it). The cellar is the screening room with the splicer, the projector and the
  generator. All 29 generated props are placed (eleven new ones from Tripo imported this session: wardrobe, dining
  table and chair, fireplace, clock, bookshelf, bathtub, stove, piano, rocking horse, dressing table); the film crew's
  cameras, lights and chairs stand in the yard and the rooms. Light: a real directional moon through the windows, one
  or two warm lamps per room, small cool fill lights inside the windows; grain and chromatic aberration lowered.
  Checks on House9: AutoRun **52 of 52**, CheckLevel **0 problems**, Director test **20 of 20**, set pieces **12 of 12**,
  net test 7 of 7 (on its own map), navmesh check 0 failures over three levels and four stairs. The AutoRun's
  "stand next to it" helper now stands on the target's own floor (it assumed one floor). Frame rate, uncooked,
  1920x1080: 55 FPS in the worst room (the Hall), 56 during Action there, 72 in the yard. Frames:
  `Saved/LookShots/house_sheet_on.png`, `house_sheet_off.png`, `house_front.png`, `house_action.png`.
  Tried and dropped: haze inside the house only, through a volume material (its extinction was ignored in the 5.9
  Preview). Details: `Docs/agents/HOUSE_progress.md`, plan `Docs/agents/HOUSE_layout.md`.

- **Nov 12, 14:35 to 17:00, FEAR agent: three things that hunt, doors that slam, a Director who remembers (owner's
  request: "smart, very frightening, not the same every time, each with its own ability", and doors that slam by
  themselves).** Design: `Docs/CREATURES.md`, now merged into `DESIGN.md`.
  - **Your cut decides what hunts you.** The Director is always in the house. Splicing SC. 31 THE EXTRAS promotes one of
    the eight placed mannequins, a different one each run: **the Extra** moves only while no player looks at it with a lit
    camera light (35 degree cone, 18 m, clear line), slides with no animation and a dry scrape that is cut dead when it
    freezes, stops 2 m short and waits, and a touch is a catch. It cannot open doors: it finds the shortest way on which
    every door stands open (a graph of way points on both sides of the 16 doors, built once in 7 ms) and otherwise stands
    at the door. Splicing SC. 8 (the reel is now labelled SC. 8 THE UNDERSTUDY) brings in **the Understudy**: blind and deaf,
    it feels lit camera lights on its floor (20 m alone, no line of sight needed within 8 m), comes at 323 cm/s, and when
    the light goes out it stops 2 m short of the last lit place, sweeps its arms for 6 s and shuffles off. Light freezes
    one and calls the other; running from either tells the Director. Each release shows a card with the rule.
  - **The Understudy's model** was made earlier today in Tripo (logged by the MONSTER agent); this session imported it
    (`Tools/world/import_understudy.py`: skeletal mesh, own skeleton on the UE5 mannequin bone names, five animations),
    measured its strides (`Tools/look/anim_speed.py`: 166 and 320 cm per loop in mesh units, 16 percent longer than the
    Director's) and anchored it to its capsule the way the Director is. Our own work.
  - **Doors slam by themselves**: the leaf shuts in 0.13 s, bangs, rattles in the frame, the room's lamps stutter for a
    second. A player walking through or past an open door rolls for it (front door 72 percent), and a slow timer takes a
    door on your floor. A slam is a loud noise nobody made, so the Director comes to look. At most six per run alone,
    never during Action, never the only exit of a room while a creature is within 10 m of the player in it.
  - **The Director remembers**: the last three hiding spots where he dragged somebody out or was fooled (he checks them
    first), the place where the crew was loudest (his patrol goes back), and after a failed search he sometimes walks
    off without calling Cut, waits in the dark and comes back on soft feet. Three cards teach it the first time. His
    speed, hearing and takes per player count are unchanged.
  - **Sound**, all synthesized with numpy (`Tools/audio/gen_sfx.py Fear`, our own work): door bang and rattle, the Extra's
    scrape loop and stop knock, the Understudy's breath and film tick. Nobody has listened to them yet.
  - **What the frames showed and what was changed because of them** (`Saved/LookShots/fear_sheet.png`): every mannequin
    stood in profile to whoever it faced (the mesh faces +Y; fixed for all eight); the Understudy's wrapped head read as a
    pale bandage, so a material with a head mask taken from the reference pose turns everything above the chin into
    near-black film with a wet sheen and darkens the figure. Honest judgement: as a black shape against the moonlit window
    at 3 m it is frightening; fully lit and standing still at 3 m it is a stylised puppet with big hands and flat feet,
    which the game's own rule (do not light it) mostly keeps off the screen. The Extra is frightening for where it
    suddenly stands, not for how it is modelled (a blank mannequin with its arms out).
  - **Tests** (headless, House9, final code): AutoRun 52 of 52 with CheckLevel 0 problems; Director test 29 of 29 in four
    runs out of four (20 old checks plus 9 for his memory); set pieces 12 of 12 in three runs; the two-process net test now
    runs on House9, 13 of 13 on the host, with the client itself confirming the Extra frozen, the Extra moving and the
    Understudy's place; the three new tests: doors 16 of 16, the Extra 17 of 17, the Understudy 19 of 19; navmesh check 0
    failures. Runs that failed on the way and why are in the progress file.
    Frame rate, uncooked, 1920x1080, the Hall with all three creatures in view: 54.7 FPS (57.1 empty; worst reading 54.4).
  - Found on the way, old faults: the navmesh continues through the shut garden gate into the exit pocket, so a "random
    reachable point" could lie behind the fence. The Director's blackout move had put him out there, behind a gate he
    cannot pass (seen in a test log), and test bots too; all random points are now filtered. The level check counted a
    player standing on the respawn point as a blocked respawn point. Three test steps placed their actors by chance in
    places where the step could not work (no clear 9 m in a small room, a phone behind the cellar wall): they now pick a
    place that is what the step says.
  - Not done: nobody has played it by hand, so the balance of three creatures for a first-time solo player is a guess
    written down in numbers; not packaged. Details: `Docs/agents/FEAR_progress.md`.

- **Nov 12, 17:00 to 17:30, PACK agent (Claude Code, Opus): the House9 game packaged and checked.** Windows Development build
  of commit 681b0cd, built, cooked and staged in 3.4 minutes (953 packages, 0 errors). `Alaa_StillRolling.zip` is **574 MB**
  (limit 1000), 871 MB unpacked, no debug symbols. The packaged exe loads House9 by default; the UI tour, the 20-room house
  tour and the creature tour all exit cleanly, and their frames match the editor's (mean colour within 0.2 of 255 except
  where FEAR changed the mannequins after the editor frames, and doors caught mid-swing). AutoRun in the packaged game,
  rendered: **52 of 52**, level check 0 problems (headless it gets 50 of 52 because the cooked game runs above 2000 FPS and
  its clock outruns the test's real-time waits). Frame rate at 1920x1080 with the camera light on: rooms 56 to 72 FPS, the
  Hall the worst at 54 to 56, the Hall with all three creatures 52.5 to 52.8, Action in the Hall 55.9. Seen, not fixed: the
  Development build prints a yellow "[VSM] One Pass Projection max lights overflow" engine message on screen as soon as play
  starts in the yard (too many overlapping shadowed lights at the front of the house; the editor runs logged it too). A copy
  to play is on the owner's Desktop in `StillRolling_Play`. Details: `Docs/agents/PACK_progress.md`.

- **Nov 12, 17:50 to 18:15, CONCEPT agent (Claude Code, Opus): Cutter concept images, made with Tripo Studio's image
  tool.** Asked for three concept designs of the new pursuer Edgar Vane, the Cutter: A sack hood (huge man, burlap sack
  sewn shut, one torn eye hole, rope, leather apron), B no face (very tall gaunt man in a rotted 1970s suit, blank
  stitched face), C porcelain mask (heavy man in a wool overcoat, cracked doll mask strapped over a dark hood); each with
  large tailor's shears, each as a full-body front A-pose sheet on mid-grey for image-to-3D (`body`) and a still at the
  end of a dark hallway in the viewer's torch light (`mood`). Teen limits: no gore, wounds or nudity; two arms, two legs,
  coats and aprons no lower than the knee, no camera, no text.
  - Grok was tried first: its image tool answered "Image limit reached" with an Upgrade button, so nothing came from it.
  - Tool used: Tripo Studio, Generate Image, model **Nano Banana** on its free quota (0 of 1000 used at the start, 12
    after), 1 image, 3:4, 4K, cost shown 0; account credits stayed at 115, no 3D generation. 12 generations for 6 kept
    images (A body 1st try; A mood 2nd, the 1st had no shirt and came out square; B body 3rd, the 1st was turned and
    clean-suited, the 2nd had red specks by the shears; B mood 3rd, the 1st was barefoot and render-like, the 2nd gave
    him an eye-holed mask; C body 1st; C mood 2nd, the 1st came out square and pasted-looking). Files fetched from the
    result's signed link with curl, size checked against Content-Length, converted to PNG (2976x4096).
  - Output: `SourceArt/Concepts/cutter_{A,B,C}_{body,mood}.png` and `cutter_sheet.png` (1920x1762, A B C columns, body
    over mood, lettered). The owner chooses the concept. Details: `Docs/agents/CONCEPT_progress.md`.

- **Nov 12, 17:47 to 18:40, ASSETS agent (Claude Code, Opus): exterior and story materials and props, generated in
  PBRgen and Meshy through the built-in browser.** Our own AI-generated work, prompts written by the agent; nothing
  imported yet (sources only).
  - **PBRgen**, 12 seamless 2K PBR sets (COL, SPEC, ROUGH, NRM, DISP, plus .mtlx/.usda) in `SourceArt/PBR/`:
    `stone_wall`, `stone_dressed`, `roof_slate`, `brick_old`, `wood_weathered`, `ground_dirt`, `grass_dead`,
    `flagstone_path`, `plaster_damp`, `leaf_litter`, and for the street `asphalt_cracked`, `siding_old`. Each: text
    prompt -> 2 start tiles (Nano Banana 2 Lite, 8 credits) -> better tile picked in the tiled 2D view -> "Create
    material from texture", seams inpainted (10 credits). 216 credits, balance 812 -> 596. All maps 2048x2048; colour
    map edges wrap as cleanly as the inside of the image.
  - **Meshy** (first use of it on this project), 16 props as GLB in `SourceArt/Retopo/`: `sr_deadtree`,
    `sr_deadtree2`, `sr_bush`, `sr_well`, `sr_lantern`, `sr_swing`, `sr_streetlamp`, `sr_car`, `sr_powerpole`,
    `sr_bin`, `sr_doll`, `sr_wheelchair`, `sr_tv`, `sr_mirror`, `sr_cradle`, `sr_candelabra`. Each: text -> 2 reference
    images (Meshy Image, Nano Banana 2, 12 credits) -> image to 3D with Smart Topology (Meshy T2.5, 20,000 target,
    5 credits) and texture (Meshy 7 PBR at 2K, 10 credits; Meshy N1 base colour for the big tree). Result: 18.5k to
    21.4k triangles each, one mesh, 2K textures, 4.4 to 9.9 MB; every file checked for the `glTF` magic and a header
    length equal to its size. Two redone: the big tree at 10k came out with paper-like flaps on the twigs (20k fixed
    it), and the first dead bush was a ball of shards (a sparser "vase of stems" prompt fixed it). Task costs 474;
    balance 1,400 -> 1,076 (Meshy added 150 bonus credits by itself), above the 600 kept for the creature.
  - Tripo was not used (lead's order, credits kept for the creature). Weak spots, honestly: bricks too large per tile,
    flagstones small and asphalt light (fix with tiling and tint), the plaster crackle is fine and even, the lantern
    glass and mirror glass are opaque painted textures, the swing seat is part of the frame mesh, the pole is thick.
    Meshy normalises every model to about 1 unit, so each needs scaling on import (heights listed in
    `Docs/agents/ASSETS_progress.md`).

- **Nov 12, 18:20 to 18:40, CUTTER-ART agent (Claude Code, Opus): the Cutter as a rigged, animated GLB, made in Tripo
  Studio from the owner's chosen concept A (the sack hood).** Built-in browser, the Tripo tab only; nothing imported.
  - Image to 3D: the concept image was already in the account's Generate Image history, so its own "Generate 3D" ->
    High Detail Model button loaded it (no upload). HD Model, H3.1, Geometry & Texture set to Ultra Mesh off, Texture
    2K, PBR off, Remove Lighting on (the panel had gone back to its defaults, price 55; with these settings 25). First
    model kept: 972,804 faces, task id `4903dfcd-4080-4723-94ed-c0aa70da6eab`.
  - Retopology, Triangle, 20,000, Smart Mesh on: 18,801 triangles, texture kept (35). Exported unrigged for free as
    `SourceArt/Retopo/sr_cutter.glb` (2.4 MB). Rig: Humanoid, UE5 Mannequin preset (20). Animate: the free presets
    idle, walk, run, look_around, slash (no zombie or heavy walk exists among the presets, so plain walk). Export GLB,
    2K, skeleton, 5 of 5 animations, in place: `SourceArt/Tripo/sr_cutter_rigged.glb` (3.75 MB).
  - Checked from its JSON chunk: 18,801 triangles, 80 joints with the same UE5 mannequin names as the Understudy, the
    five clips with the same names and lengths (idle 15.33 s, walk 2.33 s, run 1.25 s, look_around 15.58 s, slash
    6.58 s), base colour and normal 2048x2048, no roughness/metal map (PBR off), 1.0 unit tall.
  - How he looks: one heavy upright figure, sack hood intact with the dark hole and the rope, green shirt, knee-length
    leather apron; the apron was continued round the back as a closed skirt. Arms hang 15 to 20 degrees out with a gap
    to the body; the right fist and the open rusted shears are one piece, blades to knee height in front.
  - Faults found: the auto-rig gave the apron's front hem finger weights (251 of 343 hem vertices mainly on
    `index_03_l/r`), so in the slash the apron front is pulled towards his hands; the missing roughness map means the
    Unreal material must not inherit another model's. Walk and run move the pelvis like the other two (1.45 and 2.84
    per loop). Credits **115 -> 35** (25 + 35 + 20; motions and exports free). Details:
    `Docs/agents/CUTTER_ART_progress.md`.

- **Nov 12, evening, CUTTER-FIX agent (Claude Code, Opus): the Cutter's apron no longer follows his hands.** No
  Unreal, no browser. Python (numpy) edited the GLB's weight bytes directly; headless Blender 5.2 only for the look.
  - What was wrong: Tripo's auto-rig had put finger-tip weight (`index_03_l/r`, `thumb_03_l/r`, `middle_03_l`,
    `pinky_03_r`; 0.26 median, up to 0.74) on 1,547 vertices of the apron/skirt front, from the knee hem up to the big
    pocket. In the slash the apron front tore open and flew up to his hands, and it shredded in run.
  - How it was repaired: the forearms, hands and the shears (fused to the right fist) were found as closed mesh islands
    below the elbow and left untouched. On the 1,547 vertices the arm weight went to a thigh_l / thigh_r blend (smooth
    across the centre line) plus pelvis towards the waist. The two faces of the leather sheet were synced (Tripo had
    often weighted only the inner face to the fingers) and the result lightly smoothed. Max 4 influences, renormalised.
    2,206 rows written; only the JOINTS_0 / WEIGHTS_0 bytes of the file changed (same joints, clips, textures,
    material, size 3,753,216). Original kept as `SourceArt/Tripo/sr_cutter_rigged_orig.glb`.
  - Checked: `Saved/LookShots/cutterfix_sheet.png` (original vs repaired, slash, run, walk, look_around, idle; front,
    side, back). The apron now hangs from the waist and swings with the legs; hands and shears move exactly as before.
    Left as is: the back skirt's centre seam (Tripo's hard left/right split) stretches in run, the hidden inside of the
    skirt (pale texture) shows from the side in run, and there is upper-arm weight on the shoulder blades.
  - For the importer: 0.998 units tall, faces +Z in the file (-Y in Blender), pelvis travels +Z 1.452 per walk loop
    and 2.835 per run loop; run's last key is about one frame short of a closed loop. Details:
    `Docs/agents/CUTTER_FIX_progress.md`.

- **Nov 12, 18:20 to 19:30, STORY agent (Claude Code, Opus): the story written on the walls of House9, every mark drawn by
  code.** The owner asked for writing on the walls, blood and a story inside; at 18:30 he changed the story to a father's
  home movie, "OUR HAPPY HOME" (`Docs/HOUSE_LOOK.md`), before any text was drawn, so only the new texts exist.
  - **What is in the house now** (124 actors, tag `HGen_story`): Father's tally of takes in fives round the Hall window that
    faces the front door, 212 marks then `TAKE 212`; `CROUCH. HE HEARS HEELS.` at the foot of the stairs; `FINISH HIS FILM. IT
    IS THE ONLY WAY OUT.` with an arrow down on the panel under the stairs beside the cellar door; a dried drag smear from the
    stairs into the cellar doorway and drops down the cellar stair; `ONE MORE TAKE` in eight rows over the Study's north wall,
    over an old picture too, the rows growing from 13 to 38 cm and rougher; the schedule board `DAY 1` to `DAY 61` crossed off,
    `DAY 62` to `70` empty; the slate `OUR HAPPY HOME  TAKE 213` (ROLL 9, SCENE 61, DIRECTOR and CAMERA `E. VANE`); the Dining,
    Parlor, Kitchen, Storage, Landing (`AGAIN.`, 75 cm letters over the stairwell), Bedroom, ChildRoom, mirror and Cellar texts
    of the table; two pairs of hands dragged down beside the Dining and Bedroom doors; a spray behind a Storage mannequin; the
    porch board `QUIET IN THE HOUSE` / `he means it`. Wear in every room: damp with tide rings and mould at ceiling corners,
    water runs under windows, soot over the fireplace and the stove, wallpaper torn to the plaster, clean rectangles where
    pictures hung, nail scratches by doors, cracks, grime at thresholds, 20 cobwebs, 18 sheets of paper, 5 framed
    photographs with the faces cut out as neat holes (the tall man's head seen from behind).
  - **How the writing was made** (`Tools/look/gen_decals.py`, numpy and Pillow; no downloads, no font file for the hands): a
    stroke font drawn by hand as polylines and arcs (26 capitals, 10 digits, punctuation, the 8 lower-case letters of "he
    means it"). A "hand" lays a line out with jitter per letter (size, turn, baseline, slant), a slow noise wobble along
    every stroke and a pressure curve; each stroke is then rasterised segment by segment as a distance field with a brush
    model. Chalk: a ragged edge from a fine "tooth" noise and a grain threshold that rises where the pressure drops. Father's
    brush: a load of paint that runs dry along the stroke into bristle streaks (a 1D noise across the stroke against a
    rising dryness), darker where the paint is thick, and runs that drip from the low ends of strokes with a bead at the end.
    The kept ones' white paint: a thinner brush with runs. Crayon: paper tooth plus wax streaks along the stroke. A finger in
    dust: a soft edge with ridge lines. Scratches: thin broken double passes. Pencil for the shot lists. A "rough" value grows
    row by row in the Study so the rows get larger and wilder, and wear (low-frequency rubbing, sleeve-like scuffs) takes
    some of every text off. The typed pages are Courier New from Windows, struck character by character with uneven ink.
  - **Stains**: each handprint is a palm, four finger capsules and a thumb as a distance field, printed hardest at the finger
    pads and the heel of the palm, then slid down the wall in 2-pixel steps where the fingertips keep contact longest (four
    streaks) and the palm runs out; a darker rim and flakes make it dry. The drag smear is a Catmull-Rom path swept with a
    film that comes and goes and streaks dragged through it. Damp is a noise field pulled toward the corner, cut at four
    contour levels for tide rings, with mould speckle. Torn wallpaper has a plaster fill, a pale curled rim and a shadow
    line. Cobwebs are threads from the ceiling edge and both wall edges to a knot high in the middle, with a dust veil.
    Teen: everything is dried and brown-red; no body, no wound, no pool.
  - **In the engine** (`Tools/world/house_story.py`, re-runnable): `M_SR_Decal` (deferred decal). It fades on faces that
    turn away from its plane, using the face normal from the screen derivatives of the world position, so writing stays
    on the wall and not on door casings, floors or furniture edges. `M_SR_StoryCard` (masked, two-sided, a screen-space
    dither for cobwebs). One instance per texture. Measured, not assumed: a test texture showed that in this engine a
    decal's texture U runs along the box's local Z, so the box size is (depth, height, width) and wall decals are rolled 90
    degrees. Every frame was looked at in the running game with the camera light (three tours of 41 frames). That found and
    fixed: chalk invisible on the cream wallpaper (charcoal there now); the floor smear lost on the dark boards; a handprint
    hidden under the open front door's leaf, then behind the cine camera; the spray hidden behind crates; cobweb threads
    vanishing in the mipmaps, and my own dither formula letting a tenth of the transparent pixels through as a grey
    rectangle; soot that showed on a picture but not on the wall; a mirror that read as a paper sign; a single handprint too
    quiet to notice.
  - **Numbers**: 43 textures (46.8 Mpx, about 62 MB as DXT5 with mips; not packaged). Frame rate at 1920x1080 with the camera
    light, same tour on the committed map and on the new one: Hall from the door 55.5 to 56.3, Hall along the stairs 54.4 to
    53.3 (the lowest), Study 75.0 to 73.5, Landing 62.0 to 61.7. Checks after the pass: AutoRun 52 of 52 with CheckLevel 0
    problems, Director 29 of 29 twice (one earlier run was 28 of 29, its known timing check "back within 10 s" at 12.0 s),
    set pieces 12 of 12, door slams 16 of 16, Extra 17 of 17, Understudy 19 of 19, navmesh check 0 failures.
  - **Still weak, honestly**: the drag smear is a dark, slightly glossy band rather than plainly red; the handprints are true
    to size and so small from across a room; the mirror reads as dirty glass only up close; the small Bedroom line and the top
    Study rows need the lamp on them; the photographs are flat silhouettes. Frames: `Saved/LookShots/story_sheet.png`,
    `story_sheet2.png`. Details: `Docs/agents/STORY_progress.md`.
- **Nov 12, 19:40 to Nov 13, 00:30, EXTERIOR agent (Claude Code, Opus): the outside of House9 rebuilt as a stone house on a dead-end
  street, every piece placed by script.** The owner wanted the house stunning and realistic from outside, in stone, full of
  detail, standing in a neighbourhood rather than a void (`Docs/HOUSE_LOOK.md`, "Outside", "The street"). Nothing inside the
  house was moved: interior wall faces, collision, doors, reels, spawns and the navmesh are as they were.
  - **Sources used** (generated earlier by the ASSETS agent, imported here by `Tools/world/house_exterior_import.py`): the
    PBRgen sets `stone_wall` (house walls, gables, trench walls), `stone_dressed` (quoins, lintels, sills, jamb stones,
    string course, cornice, plinth, kerbs, pavement slabs), `roof_slate` (house and neighbours' roofs, porch roof),
    `brick_old` (chimneys, gate pillars, the dwarf wall, two neighbours, front-garden walls), `wood_weathered` (porch,
    fence, boards, bargeboards, fascia, window timber, logs), `siding_old` (three neighbours, and the shutters: its peeling
    paint, turned upright), `plaster_damp` (two rendered neighbours), `ground_dirt` + `grass_dead` + `leaf_litter` (one
    blended ground), `flagstone_path` (the path), `asphalt_cracked` (the lane, darkened to a third), `leaf_litter` (leaf
    drifts, hedges). Meshy models: `sr_deadtree` and `sr_deadtree2` (four trees in and round the yard, nine along the
    lane), `sr_well`, `sr_swing`, `sr_bush` (15 dead bushes), `sr_lantern` (the porch lantern, its milky panes glowing),
    `sr_streetlamp` (five; the one by the gate flickers), `sr_powerpole` (five, wires between them), `sr_car`, `sr_bin`.
    The Meshy models are about 95 cm long as exported; they get their real size from the actor scale (the 5.9 Preview
    stores a mesh build scale set from Python but does not rebuild the mesh, so a later rebuild would have changed every
    size: the build scale stays 1). Each got four LODs (100, 45, 18, 7 percent). Textures capped at 2048 on the wall,
    roof and ground sets, the trees and the car, 1024 on the small props.
  - **Drawn by code** (`Tools/look/gen_exterior_textures.py`, numpy and Pillow, Windows fonts rasterised): the `DEAD END`
    plate (enamel, rust blooms at the edges and bolt holes, a dent, pellet strikes), the `CONDEMNED` notice (red bar, typed
    lines, damp tide marks, a torn corner, rusty staples), two `MISSING` posters whose photograph is a washed-out head with
    no face and whose handwriting the rain has run until nothing can be read (no names anywhere), the enamel number 9,
    two dead grass tufts (blades as curved strokes, straw to grey), the upper window's lamp behind a curtain with a
    mannequin's silhouette, three window-glass grime and crack maps, and the moon.
  - **How the house was built** (`Tools/world/house_exterior.py`, re-runnable by parts): a 20 cm stone skin outside the
    old outer faces, cut round every opening, so every window and door has a 30 cm reveal; dressed quoins at the corners
    (long and short in turn), a dark plinth, a string course between the floors, a cornice; a lintel, a sill and block-and-
    start jamb stones at every opening; stone gables (a prism mesh built from a mesh description: the engine has no wedge)
    with a louvred vent; a 45 degree slate roof with verges, bargeboards, fascia and soffits, a ridge, missing and slipped
    slates, a rusted gutter that has dropped at one end, down pipes (one broken off), two brick chimneys with pots; a
    lean-to slate porch roof with brackets; shutters made of three pieces each, some open, some ajar, some hanging off one
    hinge; glass in every window (one-sided, facing out, cracked or broken in places); two windows boarded from inside and
    the glowing window (both one-sided cards facing out: from the rooms nothing is there); dead creeper up the front-left
    corner. The skin, its reveal stones and the shutters cast no shadows, so the moon patches in the rooms are exactly what
    they were (the old walls behind still cast the house's shadow). New materials: `M_SR_Ext` (a projection plane per
    face, so rotated boxes such as roof slabs and leaning boards are mapped square; a second rotated sample breaks up
    tiling; per-instance brightness), `M_SR_ExtGround` (dirt, dead grass and leaves blended by world noise), `M_SR_Sky`,
    `M_SR_GroundFog`, `M_SR_Glass`, `M_SR_XBars`, `M_SR_XCard`, `M_SR_PropGlow`, `M_SR_StreetFlickerFn`.
  - **The yard**: the film crew's gear is gone (camera, chair, lamp and the tall stand of the yard's stage light, road
    case, crate, cables; the crates on the porch and in the back yard keep their invisible collision so the navmesh did
    not change, and firewood, a bucket and a chopping block stand there now; the loudspeaker horns outside stop drawing);
    blended ground, 940 grass tufts, leaf drifts, a board fence on three sides with leaning sections, missing and broken
    boards (the old fence boxes still hold the collision), a low brick wall with iron railings at the front and the gate
    drawn as iron bars (a masked material on the gate's slab), so the street shows from the spawn. Low fog: local fog
    volumes draw only beyond 10 m in this engine (`r.LocalFogVolume.GlobalStartDistance`), so the yard's fog is three
    stacked translucent sheets with drifting noise, soft where they meet the ground and faded near the camera.
  - **How the street was built**: Marrow Lane runs west from the gate for 120 m into the fog: asphalt with patches,
    kerb blocks laid one by one with small turns and drops, slab pavements, front gardens with hedges, low walls or picket
    fences; ten neighbours' houses from one builder (walls, plinth, gabled roofs side-on or end-on, chimneys, framed
    windows of dark glass, broken or boarded, doors, two porches): two flank the yard, six line the lane, two close the view
    beyond the back fence; one window far down the lane is lit. Five street lamps (the one by the gate flickers through a
    light function, its glass through the same function in its material); five power poles with three sagging wires a
    span and a drop to the house's gable; the car at the kerb, bins (one knocked over), the `DEAD END` sign on its post
    (back-to-back plates), `CONDEMNED` notices on a neighbour's door and side window, `MISSING` posters on the pole by the
    gate; and beyond, black in the fog against the sky, rooftops all round and a church tower with a spire. All of it is
    instanced (one component per material and per 16 m cell, so walls hide what is behind them) and none of it has
    collision.
  - **Air and light**: a sky dome past the fog's cut-off (2 km) with a gradient, a glow on the horizon, slow clouds and a
    large pale moon beside the left slope of the roof as seen from the gate, its disc crossed by the twigs of a tree just
    over the north fence. The moonlight itself was not moved (it still comes from behind the player at the gate, so the
    rooms keep their patches): the visible moon is a cinematic cheat. Looking west, toward the real light, the haze glows
    pale: the moon's light scattered forward. Shortening the volumetric haze to 7 m halved that glow and left the rooms'
    look alone, but it packed the whole froxel grid into the room the player stands in: A/B runs against a copy of the
    committed map (four each) showed every room 2 to 3 FPS slower, so the haze stays at 45 m and the west glow stays as it
    was before this pass. Local fog volumes were dropped for the same reason (and they draw only beyond 10 m here). The yard's stage light and the
    warm gate lamp are gone, the back door lamp is a cold spot aimed away from the wall (no shadow, so it cannot reach the
    kitchen), and a small unshadowed light lets the lantern's colour reach the porch board. The engine's VSM warning "One
    Pass Projection max lights overflow", logged at the front in the baseline, no longer appears.
  - **Looked at, not assumed**: every change was judged on frames from the running game (`Tools/look/tour_exterior.txt`,
    camera light off; six rounds). They found: the grass, glass and glow cards drawing the engine's grey checker in the game
    (their masters lacked the "used with instanced meshes" flag, which the editor sets on the fly and the game does not);
    a metallic, wet-looking trunk (the tree came without a metal-rough map and the master read its grunge mask instead);
    trees far too fat and pale and standing in front of three cameras; the moon hidden by branches; the board fence hiding
    the street from the spawn; the yard's ground fog invisible (local fog volumes draw only beyond 10 m in this engine);
    two shutter entries lost inside a comment by one of my own edits; the porch board unreadable; the window bars pink.
  - **Frame rate** (1920x1080 fullscreen, camera light on; before = the committed map): yard gate view 75.2 -> 64.9, porch
    64.8 -> 60.8, left 74.5 -> 65.7, far corner 83.1 -> 73.3; rooms Hall 55.0 -> 53.8, Hall along the stairs 53.8 -> 53.1,
    Dining 58.5 -> 57.9, Parlor 59.5 -> 59.2, Landing 59.9 -> 60.7. The first version cost the rooms 2 to 4 FPS: A/B runs
    against a copy of the committed map and a GPU profile put most of it on the 7 m haze range and the local fog volumes;
    also taken out: outside geometry from Lumen's and ray tracing's scenes, instances split into 16 m cells so walls hide
    them, grass culled beyond 35 m, LODs on the models. Interior frames with the light off, mean brightness before -> after:
    Hall 32.5 -> 32.6, Dining 78.1 -> 78.0, Parlor 11.5 -> 11.7.
  - **Size**: about 102 MB of new textures as cooked (DXT with mips: PBR sets 56, model textures 43, drawn textures 3), with
    meshes, LODs, level data and shaders about 130 to 140 MB cooked, roughly 100 to 110 MB more in the zip. Not packaged.
  - **Checks** (headless, final level): AutoRun 52 of 52 with CheckLevel 0 problems, Director 29 of 29, set pieces 12 of 12,
    door slams 16 of 16, Extra 17 of 17, Understudy 19 of 19, net test 13 of 13 (host, on House9), `house_nav_check.py`
    0 failures (farthest room 11 s from the gate, as before).
  - **Still weak, honestly**: the visible moon is on the wrong side of the real light (shadows fall toward it); looking
    west the haze glows pale, more like dusk fog than night; the neighbours are boxes and prisms with flat dark windows and
    no gutters; the swing does not sway (seat and frame are one mesh); grass tufts are crossed cards that show as flat up
    close; the stone's dark patches repeat on the big gable; from the spawn the roof is only its verge edge; the gate view
    from the spawn is mostly the gate itself. Frames: `Saved/LookShots/ext_sheet.png`. Details:
    `Docs/agents/EXTERIOR_progress.md`. Rebuild order: `house_build.py`, `house_gameplay.py`, `house_dress.py`,
    `house_story.py`, then `house_exterior_import.py` (once) and `house_exterior.py parts=meshes,clean,shell,yard,street,sky,save`
    (re-run `parts=clean` after any re-run of `house_build.py` or `house_dress.py`: it restyles and hides their outside pieces).

- **Nov 12, 23:10 to Nov 13, 00:45, CHASE agent (Claude Code, Fable 5.2): the pursuer is the Cutter, heard before seen; the chase,
  the catch, the camera in the player's hands, the way out.** Owner's direction of Nov 12, 17:50 (no film-set man with a
  camera; a far more frightening figure). Everything below was made in this session; nothing downloaded, nothing bought.
  - **Tools used**: Claude Code (C++ and scripts), Unreal Engine 5.9 editor through MCP (`Tools/unreal_mcp.py`,
    `Tools/world/run_in_editor.py`), Python with numpy for the sound synthesis and PIL for one drawn texture, the offline
    Windows voice (System.Speech) for the raw voice lines. The model is CUTTER-ART's Tripo GLB with CUTTER-FIX's weights.
  - **The Cutter's body**: `Tools/world/import_cutter.py` imports `SourceArt/Tripo/sr_cutter_rigged.glb` (18,801 triangles,
    idle, walk, run, look_around, slash) into `/Game/StillRolling/Creature/Cutter/`; the GLB has no roughness map, so a 16 px
    flat mask (roughness 0.8, metal 0; `make_cutter_mask.py`) stands in. Material on `M_SR_Figure`, darkened
    (brightness 0.36, the hood 0.52). He stands 2.2 m. `ASRDirector` lost its spot light, lamp cone and light function;
    what the code calls his beam is now only his gaze. Walk and run play at ground speed / 62.2 and 226.8 cm/s (mesh units).
  - **Sounds, all synthesized** (`Tools/audio/gen_chase.py`, numpy noise and sines like `gen_sfx.py`; 28 files, 6.7 MB of WAV):
    `fs_cutter_01..06` (boots on boards), `cutter_scrape_loop` (shears dragged on wall and floor), `cutter_snip_01..03`,
    `cutter_breath_loop`, `amb_cutter_drone_loop`, `amb_panic_breath_loop` (the player in a chase), `door_burst_01..02`,
    `cutter_grab`, `gate_chain_fall`, `sting_cutter_seen`, `sting_reel_out`, `amb_screening_music_loop` (a music box on the
    film's sound stripe: an original eight-bar tune written as note numbers in the script). **Voice, placeholder as before**:
    `Tools/audio/tts_father.ps1` renders "Quiet.", "Action.", "Cut.", "Every family needs a happy ending.", "One more take."
    with the offline Windows voice; `gen_chase.py voice` turns them and three of the old raw lines into the father's voice
    (slowed to 0.70 to 0.74, a chest under it, dry, a breath in front; no PA horn): `father_quiet`, `father_action_01/02`,
    `father_cut`, `father_again`, `father_thats_a_wrap`, `father_happy_ending`, `father_one_more_take`. Nobody has listened to
    any of them: they are judged by their numbers only (peak, RMS, loop seams).
  - **Hearing him**: the scrape rides on him while he walks (louder and 1.3 times faster in a chase, gone when he stands, one
    snip as he stops to listen), breath within about 6 m, a drone that rises with his nearness, boots on his real stride
    (160 and 312 cm). Two kinds of quiet in `USRAudioDirector::UpdateCutterSense`: he is within 9 m with a clear line and
    has not seen you, or you sit in a hiding spot with him within 8 m: room tone, dread, drone and heartbeat go, his breath stays.
  - **The chase**: closed doors in his way are thrown open in 0.15 s (`ASRDoor::Burst`, replicated like the slam), a door
    shut in his face holds him 1 s, the picture of players within 6 m jolts; heartbeat by distance, panic breath, shake and
    a wider view while sprinting, dark edges within 4 m (`ASRCharacter::UpdateLocalView`). At a hiding spot he stands 1.6 s
    before he opens it. **The catch** is first person: 1.3 s with his hand on the player, the view turned to his hood, the
    slash clip entered at 0.68 s and played at 0.8 so the shears come up, "Cut.", a snip, and a cut to black; the cost of
    a catch is what it was.
  - **The camera**: the HUD is a viewfinder (corner brackets, REC, time code, battery bars, mode). Night vision on `V`:
    a post-process material (`M_SR_NightVision`, built by `Tools/world/chase_assets.py`) and a weak light only the owner's
    machine shows. It is not light for the game: the Extra walks in it, the Understudy does not feel it; it drains the
    battery 1.6 times as fast; lamp and night vision switch each other off; the mode replicates.
  - **The way out**: the gate is drawn chained (plain links and a padlock on `ASRStageDoor`). When the cut is complete
    `ASRGameMode::StartScreening` runs a 16 s reel: the cellar projector's light function becomes the film (`M_SR_FilmFn`
    with a title card drawn by `Tools/look/gen_film.py`), the chain drops, the gate rolls aside, and he walks down to
    watch (state `Watch`: deaf, blind, no obstacle). Reel out: with The Ending he stays (`PICTURE WRAP`), without it he
    comes at 1.12 times his hunting speed and knows where everybody is (`TO BE CONTINUED` at the gate).
  - **Wording**: menu, HUD, cards, prompts, reel labels and end screens moved from a film crew on a set to the father's
    home movie (list in `Docs/agents/CHASE_progress.md`). `DESIGN.md`: pitch, theme, player, the Cutter, the way out, sound.
  - **Checks** (headless, final code): AutoRun 52 of 52 with CheckLevel 0 problems, Director 29 of 29, set pieces 12 of 12,
    door slams 16 of 16, Extra 19 of 19 (two night-vision checks added), Understudy 21 of 21 (two added), new `-SRChaseTest`
    18 of 18, net test 15 of 15 on the host and 4 answers on the client (night vision added), `house_nav_check.py` 0 failures.
  - **Frame rate** (uncooked, 1920x1080 fullscreen, Hall from the front door, lamp on): empty 55.8, the Cutter at 9 m 54.9,
    at 3 m 53.2, the same in night vision 54.8, the cellar with the film and him 60.9. A chase down the Hall averages 48.7
    over 2.2 s (the Action lamps alone, as before this pass, are 52.4).
  - **Weak, honestly**: no sound has been heard by anyone; the voice is still the Windows voice; the run clip's loop is one
    frame short and is not blended; the gate slides aside instead of swinging (its bar material only draws on faces that
    look along X); a catch after the reel ran out sends the player back to the gate, which is open; the film on the wall is
    one still title card. Frames: `Saved/LookShots/chase_sheet.png`. Details: `Docs/agents/CHASE_progress.md`.
- **Nov 13, 00:48 to 02:10, ROOMS agent (Claude Code, Opus): the film studio out of the rooms; the house's own lamps at
  "Action!"; a dirty Cutter; night vision; a new package.** The story is a family's home movie, but the rooms were still
  dressed like a film set. All of it is made by a new re-runnable editor script, `Tools/world/house_rooms.py` (tag
  `HGen_rooms`; `parts=import,materials,place,lamps,nightvision,save`; run it after any re-run of `house_dress.py` or
  `house_gameplay.py`).
  - **Out**: two cine cameras on tripods, the director's chair, four studio lamps on stands, four road cases, five director's
    chairs used as cinema seats, eight crew cables, five loudspeaker horns. What had collision is hidden with its collision
    kept, so the navmesh and every path are as they were; the rest is deleted (their collision posts stay).
  - **In**: a coat stand with a coat and a hat by the front door, a plain hall chair (the same actor, still moved in a Cut), a
    standing lamp, a cabinet under a dust sheet, a television on a stand facing the sofa, the standing lamp that was left on
    in the Parlor (its practical light moved into the shade), a wheelchair alone at the end of the Landing facing the stairs,
    a crate of household goods, stacked chairs, a trunk, kitchen chairs and his armchair in front of the cellar screen,
    wooden wall speakers instead of horns, and his own small camera on a tripod in the Study, aimed at the door. Six models
    made by the ASSETS agent with Meshy (`SourceArt/Retopo`) and not used until now were imported: doll, wheelchair, television,
    standing mirror, cradle, candelabra (about 20k triangles each, four LODs, 1K textures, no Nanite).
  - **Action lamps**: the seven `ASRStageLight` keep their place, Radius and rule (on at "Action!" within 7 m, out in the
    Cut). What the player sees is now the house's ceiling lights: milk-glass globes, fabric drum shades, a glass kitchen shade
    and a bare cellar bulb on cords, which glow while the light is on (`ASRStageLight::HousingGlow`, a few lines of look-only
    C++ setting the material's `Glow`). Warmer light, no haze shafts, attenuation 11 m -> 5.6 m, one shadowed lamp per room,
    and shadows of the house only (no movable casters) from these lamps and the Hall's two table and wall lamps.
  - **Frame rate** (1920x1080, uncooked): a chase down the Hall **49.5 -> 53.4 / 52.8 FPS** (two runs; limit 50, target 52).
    Found by A/B runs with the tour's `set` command: the cost was the Cutter's moving shadow re-rendered in every shadowed lamp
    he ran through, most of all the console lamp. Every room is as fast or faster (Hall 55.6 -> 56.5, Kitchen 62.2 -> 68.4,
    Bedroom 57.2 -> 62.4, Hall at Action 50.4 -> 55.5). The price: the Cutter casts no shadow from those lamps; his shadow from
    the players' camera lamps and the moon is unchanged.
  - **The Cutter**: `Tools/world/make_cutter_mask.py dirt` draws a dirty base colour and a roughness map from his own GLB
    (PIL and numpy, fixed seed): the pale sack, apron and shirt darker and greyer, grime in the folds from the normal map,
    sweat shadows, dried brown stains kept inside the UV islands, a darker band round the eye holes and the stitching.
    `import_cutter.py material=only dirt=1` puts them on his material; the shared figure master is untouched.
  - **Night vision**: grey-green instead of flat neon green, coarser grain, darker with distance, a soft vignette (the custom
    node's HLSL rewritten in place; rules unchanged).
  - **Wording**: menu, HUD, cards, pause and the three end screens dumped as text and checked: nothing left from the film crew.
  - **Checks**: AutoRun 52 of 52 with CheckLevel 0 problems, Director 29 of 29, set pieces 12 of 12, door slams 16 of 16,
    Extra 19 of 19, Understudy 21 of 21, Chase 18 of 18 (one earlier run 7 of 18: the test's teleport onto the navmesh landed
    a floor too low; the same happens on HEAD's map, see `ROOMS_progress.md`), net test 15 of 15 and 4 on the client,
    `house_nav_check.py` 0 failures.
  - **Package**: `package.ps1` (Development) OK in 3.8 min, 0 cook errors; `Alaa_StillRolling.zip` **694 MB**.
    The packaged exe: rendered AutoRun 52 of 52 with CheckLevel 0 problems; a look tour (gate, Hall, the Cutter in a doorway,
    Action, the screening) matches the uncooked frames (mean difference 0.2 to 1.5 of 255, no checker material, no missing
    mesh); 144 of 144 sounds load. `Desktop\StillRolling_Play` replaced with it.
  - Frames: `Saved/LookShots/rooms_sheet.png` (and `rooms_*.png`, `pkg_*.png`). Details: `Docs/agents/ROOMS_progress.md`.
- **Nov 13, 02:20 to about 04:00, SCARES agent (Claude Code, Opus): the house does things when nothing is chasing you.**
  Short scripted moments, a different handful in every run (`DESIGN.md`, "Scripted moments"). No gore, nothing that blocks
  a path, everything a moment spawns is gone when it ends.
  - **Two repairs first.** `ASRDirectorAIController::DebugTeleport` searched the navmesh in a box 8 m tall and once put the
    Cutter in the Cellar under the Dining room (`-SRChaseTest` 7 of 18): it now searches close first and never more than
    1.2 m up or down (Chase 18 of 18 three times). The household models had no collision: the standing mirror, the cradle,
    the television's stand and the wheelchair got hidden boxes (the wheelchair's rides on it, it moves); the coat stand and the
    sheeted cabinet were already inside solid hidden posts. Nav check 0 failures; a new `Tools/world/house_reach_check.py`
    finds a path from the gate to all 12 reel places, 10 hiding spots and both sides of 16 doors (0 of 54 failing).
  - **The system** (new C++: `USRScareSubsystem`, `ASRScareSite`, `SRScareDebug.cpp`): at the start of a run the server picks
    5 of 9 moments (6 with two to five players) from a seed; each fires once, by its own trigger. Not in the first 25 s, 40 s
    apart, never in a chase, a grab, the 15 s after a catch, or the screening; a moment that shows a figure only while the
    real Cutter is over 12 m away and cannot see the player. Placed sites replicate what the server chose; every machine
    plays it. `-SRNoScares`, `SR.Scare.Fire <Name>`, `-SRScares=`, `-SRScareSeed=`. Every existing test runs without them.
  - **The nine moments**: a dark shape crossing between open doorways at the back of the Hall (or the Landing) with one boot
    step; a child's ball bouncing down the main stairs as you start up them; the Parlor television snapping on behind you
    (static, hiss he comes to look at, cold light; dies at 1.5 m or 7 s); the wheelchair rolling 1.2 m toward the stairs
    while nobody looks; a pale sack hood outside a Dining or Kitchen window when the lamp finds it, dropping below the sill
    0.4 s later; three knocks from inside an empty wardrobe that stop when you face it; a closed door creaking a third open
    by itself (`ASRDoor::CreepOpen`, no noise for him); a mannequin at the far end of the Landing as you come out of a
    room, gone once your lamp has left it; the cradle rocking until you step toward it.
  - **Level and assets**: `Tools/world/house_scares.py` (tag `HGen_scares`: collision, 12 sites, `M_SR_TVStatic` an unlit
    snow material made of a Custom node). Sounds made in code, `Tools/audio/gen_scares.py` (numpy noise and sines, nothing
    downloaded): 16 files (ball bounces and roll, TV on/static/off, wheelchair, knocks, slow door creak, cradle creaks).
    Nobody has listened to them yet.
  - **Checks**: new `-SRScareTest` 41 of 41 (four runs): the pick, every rule, the figure rule, each moment by its own
    trigger once, nothing left behind. AutoRun 52 of 52 with CheckLevel 0, Director 29 of 29, set pieces 12 of 12, door slams
    16 of 16, Extra 19 of 19, Understudy 21 of 21, Chase 18 of 18, net test 16 of 16 on the host (one new: the client sees a
    forced television) and 5 on the client, nav check 0.
  - **Frame rate** (1920x1080, uncooked): Hall 55.3 quiet, 54.8 with the ball, 55.4 with the figure; Parlor 58.8 quiet,
    56.7 with the television on. Frames: `Saved/LookShots/scare_sheet.png`. Details: `Docs/agents/SCARES_progress.md`.

- **Nov 13, 03:30 to 05:20, POLISH agent (Claude Code, Opus): the owner's last notes on the build he liked.**
  - **The night outside**: the moon's light now comes from the left of the gate view (it came from behind the player on
    the right, so shadows pointed at the moon in the sky); the stone of the front is lit at a raking angle and the right
    side falls dark. A post-process volume round the yard and lane darkens and cools the outside (exposure -0.45, more
    contrast, less blue); a second one round the house writes back the old values, so no room changed (lamp-off frames,
    mean brightness against a copy of the committed map: Hall 41.5 -> 50.9, Dining 85.2 -> 85.2, Parlor 30.4 -> 31.0).
    Darker sky, warmer porch lantern, the near neighbour no longer shades the lit side, half the moon haze. The big gable's
    repeating dark patches came from the stone texture's own blotches: the material now takes the texture's low
    frequencies out and lets its world-space noise vary it.
  - **Doors**: all 16 are six-panel doors (stiles, rails, sunk panels with raised fields, a knob and a keyhole plate on
    both sides), dark wood or worn paint by room, made from a mesh description by `Tools/world/house_polish.py` and hung on
    each door's hinge; the old slab still does the collision and the code is untouched, so slams, bursts and creeping
    doors move them.
  - **Story marks**: the Dining, Bedroom and Cellar writing larger and opaque; Father's paint a dark red-brown with solid
    strokes and runs (it read pink); the child's drawing redrawn bolder and moved clear of the cradle; four dragged
    handprints at each of the two doors; the Hall drag a matte, broken red-brown; the Bathroom mirror a real mirror in a
    gilt frame under the dust; the family photographs in moulded frames behind glass, two more hung crooked.
  - **The film in the cellar** (`Tools/look/gen_film_reel.py`, `M_SR_FilmReelFn`, `USRScreeningSubsystem`): a countdown
    leader, OUR HAPPY HOME, A FILM BY FATHER, one home-movie card per reel in the cut (a dinner with one place cut out of
    the film, a child with a cake and the tall man behind, the house from the street, a mannequin in a chair, the woman
    with film over her eyes, the cellar door), the slate TAKE 213, then THE END if The Ending is in the cut, or the frame
    burning through to white if not. Stretched to the reel's length (16 s alone).
  - **The gate** swings open on its hinges; it is a real iron gate now (bars, spear tops, rings) with a chain of real
    links and a padlock. The old leaf turned into a solid plate when rotated because its material chose the bar faces by
    the world normal.
  - **Creatures**: the Understudy got the Cutter's dirt pass (stained, darker dress, grime in the folds); the mannequins
    stand with their arms down and the head a little turned (`Tools/world/pose_mannequin.py` moves the vertices of the
    static mesh; collision unchanged). The Cutter's run clip loops without its skip (`Tools/world/close_run_loop.py`), and
    in the catch the view follows the shears so they are open in the middle of the last picture before the black.
  - **Lamps and props**: the Hall and Storage standing lamps come on at "Action!" with the ceiling lamps; the coat, the
    dust sheet and the trunk are proper meshes now (cloth with folds, straps and iron corners).
  - Not done: the street and back-yard additions (item 9), the wall speakers and the firewood.
  - **Checks** on the final level: AutoRun 52 of 52 with CheckLevel 0, Director 29 of 29, set pieces 12 of 12, door slams
    16 of 16, Extra 19 of 19 (18 of 19 on the first run: the Extra did not come through an opened door within 5 s; the
    rerun passed), Understudy 21 of 21, Chase 18 of 18, Scare 41 of 41, net test 16 of 16 on the host and 5 on the client,
    nav check 0, reach check 0 of 54.
  - **Frame rate** (1920x1080, uncooked): rooms 55.5 (Hall) to 71.2; Hall at Action 55.1; the Hall chase 53.3; the yard
    from the gate 65.2. Zip 701 MB (was 695). Frames: `Saved/LookShots/polish_sheet.png`. Details:
    `Docs/agents/POLISH_progress.md`.

- **Nov 13, about 05:25 to 06:10, AUDIOQA agent (Claude Code, Claude Opus 5.5): every sound checked by numbers, and the
  submission papers.** No editor, no compiler, no listening (an agent cannot hear; the owner listens later).
  - **Measured** all 173 game sounds and the 26 raw voice lines with a new `Tools/audio/measure_audio.py` (loudness, true
    peak, clipping, DC, silence, spectrum, loop seams), then rebuilt the mix from the code's own numbers with
    `Tools/audio/mix_table.py` (volumes and attenuation transcribed from `SRAudioDirector.cpp`, the engine's falloff and
    low-pass formulas read from the UE 5.9 source) and compared each of the Cutter's tells with the bed band by band.
  - **Found**: no clipping, no clicking or pumping loop, no silent file. The problems are mix values in the code: his breath
    carries about 4 m, not the 6 m of the design; after the Storm reel the rain bed masks the scrape and the breath; the
    boots, the heartbeat, the hand of the catch, the drone and the dread are almost all below 80 Hz and mask each other;
    "Cut." in the catch is buried under the chase music (speech index 0.43); the ending line lands on the peak of the
    escape sting; a 2.3 s soundstage reverb from the Stage9 days is on every sound in the house. Each has a suggested value
    with file and line in `Docs/AUDIO_REPORT.md`; the code was not changed.
  - **Fixed in the generators** (`Tools/audio/regen_check.py` showed the other 159 files bit-identical): 60 ms of silence
    at the head of the "he saw you" sting; DC offset in the 11 concrete footsteps and the 2 landings. These 14 wavs still
    need a re-import (one command in the report). An optional A/B set for the owner's ears (boots, heartbeat, indoor rain,
    a brighter father voice, the catch hits) is in `SourceArt/Audio/_qa_ab/`, not imported.
  - **The father's voice** (still the Windows voice) was measured, not replaced: after the pitch drop its consonants are
    10 to 17 dB weaker than the raw line; likely understood in the quiet moments where he speaks, helped by the HUD cards.
  - **Paperwork**: `ASSETS.md` rewritten from what is really in the project (sourced: the engine's Roboto font and basic
    shapes; flagged for the owner: Windows fonts rasterised into the sign, poster, page and film textures, the Windows
    text-to-speech voices, Tripo's preset motions). `Docs/SUBMISSION.md`: the itch.io text, the form answers (NVIDIA:
    none enabled; OpenAI: none), a 52-second video shot list, the upload-day checklist, dev log notes with a correction
    entry for the lead, git notes. Found on the way: the repository has no remote, so this dev log is not public yet;
    LOOK, MATS, PROPS and MONSTER (Nov 12) have no entry here although their work is committed.
  - Not done: nothing heard, nothing re-imported, no code touched. Details: `Docs/agents/AUDIOQA_progress.md`.

- **Nov 13, about 06:10 to 06:20, AUDIOQA agent (Claude Code, Claude Opus 5.5): the lead adopted the A/B sounds.** The lead's
  decision, after reading the report: take all five candidate sets into the game, in the generators. Still no listening.
  - **In the generators now**: the Cutter's boots get a floorboard under the heel and the sole (`gen_chase.py board_layer`,
    its own random seeds, so the snips and door bursts made from the same stream stay bit-identical), driven together with
    the old weight; the heartbeat a thump at 150 Hz; the rain low-passed as heard from inside; the sub below 35 Hz taken out
    of four hits (`synth.sub_clean`); the father's voice brighter at 2.8 kHz. The boots replace the A/B's EQ: I tried
    levels in a scratch copy until the step was audible through a wall at 3 m and at 5 m in a hunt while the sub bands
    stayed within 2 dB of the old file. `regen_check.py`: exactly the 20 adopted files changed, the other 153 identical;
    a second full regeneration reproduces all 173.
  - **Numbers with POLISH2's code values** (`mix_table.py code=new`): boots through a wall at 3 m -3.8 -> +10.0 dB over the
    bed, at 5 m in a hunt -2.6 -> +6.9; his breath at 6 m in a quiet room -6.5 -> +15.6; after the Storm reel the scrape at
    3 m through a wall -16.3 -> +2.0 and the breath -13.1 -> +8.7; the heartbeat in a close hunt -9.1 -> +1.6; the ending line
    0.80 -> 0.92 (now after the sting's peak). Two code values were not enough once the new files were in, and the report
    says so with corrected values: "Cut." in the catch only 0.43 -> 0.48, because his breath (louder now at arm's length)
    and the player's panic breath are not covered by the hush (both pulled down in the grab: 0.68); the heartbeat needs the
    dread ducked like the drone (+3.9 dB).
  - **Final import list**: 33 files (the 14 fixes and 19 adopted), one command in `Docs/AUDIO_REPORT.md`; `IMPORT LIST FINAL`
    written in the progress file for POLISH2 after the last file was written. `ASSETS.md` and the listening checklist updated.

- **Nov 13, about 05:30 to 08:30, POLISH2 agent (Claude Code, Claude Opus 5.5): doors, the dead street, the moon, the
  screens, the last box props, the audio fixes.** Everything by script (`Tools/world/house_polish.py`, new parts) and C++.
  - **Found on the way:** the mesh builder POLISH wrote wound every triangle inward (a probe built a box and a tube and read
    the normals back: 0 of 24 pointed out). The panelled doors, the trunk, the frames, the gate and the padlock were drawn
    inside out; the "dark slits" across the front door were the rails' top faces seen through the culled front. Winding
    fixed in the builder, every mesh rebuilt.
  - **Doors:** a new door material (`M_SR_DoorPaint`): dark paint worn where hands and feet wear a door (both edges, the
    top, round the knob, the bottom), grime low and at the knob, dark wood under the paint so the wear is brown, not pale.
    New watertight six-panel mesh. Front door near-black oxblood with a brass letter plate, the Parlor bottle green, the
    rest dark wood or dull mud, slate, nicotine and dirty grey-white. Under the camera lamp at 2 m everything sits in the
    tone curve's shoulder, so the paints are very dark (albedo 0.005 to 0.05).
  - **Marrow Lane:** the neighbours got fascia boards, gutters and downpipes, barge boards, sills and heads at every window,
    a second step and a door hood, a second chimney with pots, television aerials; leaning poles with sagging wires and one
    wire down on the road, bins (two knocked over), boarded fences, litter along the kerbs, three large CONDEMNED notices
    (two read over the fence from the path), a mailbox with a 9. Back yard: the hiding locker is drawn as a tool shed (same
    box), a washing line with sheets and a child's dresses, a tricycle on its side; the swing's seat sways (a world
    position offset in its material, no code). A wind material for the washing compiled but drew nothing: dropped.
  - **Moon:** the drawn moon and the moonlight were 75 degrees apart; now 11 (light from behind the house on the left of the
    gate view, the disc behind the dead tree). The backlit front keeps its read with more sky light and a smaller outside
    exposure cut; the Hall's window fill went up (it lost its moon patch).
  - **Screens:** the menu, pause and end screens wear the viewfinder's frame (STANDBY, STOP and the time code where REC is);
    a CONTROLS table and the way out on the menu and beside the pause menu; the first-minute card lists every key; the IP
    box fits; the catch's CUT card keeps the frame. Shot at 1920x1080 and 1366x768 by `-SRUITour` (now also the join row,
    the catch, PICTURE WRAP, TO BE CONTINUED).
  - **Props:** the playback speakers are a radio, a wall clock and a reel-to-reel on shelves (the speakers still play);
    firewood is split logs; the crate is off the trunk; the Hall's drag smear is a deep maroon, satin-matte.
  - **Audio (the lead's list from AUDIOQA's report):** his breath carries about 6 m, rain halved, heartbeat up with the
    drone and dread giving way to it, a 1.6 s hush after the grab so "Cut." is heard, a house-sized reverb, the ending line
    after the sting, landings only after real falls, beds pause with the game, thunder in its own slot, log off in the
    package; 33 regenerated sound files imported. Still nobody has listened.
  - **Checks:** all green on the final level. AutoRun failed "Shift + W sprints" twice: the run had spawned on the start
    nearest the porch and sprinted into its post inside the 0.6 s window; the test now checks after 0.3 s (52/52). Frame
    rate: rooms 55.9 to 72.5, Hall chase 52.8, gate 65.8. Details, rebuild order and the weak points:
    `Docs/agents/POLISH2_progress.md`; frames: `Saved/LookShots/polish2_sheet.png`.
  - **Package:** 702 MB zip; the packaged exe passes AutoRun 52/52 with all 160 sounds loaded; co-op on the package: the
    net test passes (host 16/16, client 5/5) and a normal host and client meet in House9, where each sees the other as
    the placeholder cylinder (no teammate model yet, noted for a later agent).

- **Nov 13, 07:20, lead: corrections and entries that were missing.** Written after the fact from the progress files
  and the commits, at the AUDIOQA agent's suggestion after it read this log as a judge would. Nothing here rewrites an
  entry above.
  - Missing from Nov 12 (all committed at the time; details in the progress files named):
    - 09:58 to 12:00, LOOK agent (Claude Code): a look tour that saves frames from the running game, the camera light,
      haze, grade and practicals, textured surfaces from PBRgen sets, the nine retopology props re-imported, the Director
      anchored to his capsule, frame-time settings (commit 7b97339; `Docs/agents/LOOK_progress.md`).
    - Morning, MATS agent: seven PBRgen sets (floorboards, plywood, stage_wall, black_drape, wallpaper_green,
      wallpaper_cream, wallpaper_rose), 170 credits, balance 982 -> 812 (`Docs/agents/MATS_progress.md`).
    - Late morning to early afternoon, PROPS agent: twenty furniture and house props in Tripo Studio (H3.1, retopology to
      20,000 triangles, 2K textures), round 1 nine props for 810 credits (2,025 -> 1,215), round 2 eleven for 990
      (1,215 -> 225); prompts and task ids in `Docs/agents/PROPS_progress.md` (commits ddf24f6, 6b5dc32).
    - Early afternoon, MONSTER agent: the Understudy in Tripo Studio (text to 3D, retopology, UE5-mannequin rig, five
      preset motions), 110 credits (225 -> 115); prompt and task id in `Docs/agents/MONSTER_progress.md` (commit d1d164b).
  - Decisions by the owner, in one place: Nov 11, the direction (horror, as frightening as possible, alone or up to
    five); Nov 12 01:40, approved the theme concept; Nov 12 afternoon, after playing the first build, an ordinary
    abandoned house instead of a film studio; 14:35, Claude Fable 5.2 for the lead and hard work, Claude Opus for simple
    work; about 17:50, the father's home movie and a far more frightening pursuer instead of the film-set Director, and
    a real street round the house; that evening, concept A (the sack hood) of the three Cutter concepts; Nov 13 night,
    after seeing the frames of the new build, "continue with the remaining notes", which POLISH and POLISH2 worked through.
  - Tools actually used: Unreal Engine 5.9 MegaJam Preview and its MCP server, Claude Code (Claude Fable 5.2, Claude
    Opus), Tripo Studio, Meshy, PBRgen, Python with numpy and Pillow, the offline Windows voices, Blender 5.2 (headless),
    Visual Studio 2022 Build Tools, Git. Tried, nothing used: Grok. Opened, nothing used: the We Love Indies sound library.
  - Corrections: the Cutter's breath carried about 4 m, not the 6 m the CHASE entry says, until this morning's mix
    change (AUDIOQA measured it, POLISH2 changed the attenuation; see `Docs/AUDIO_REPORT.md`); the gate, the run loop and
    the film card listed as weak by CHASE were fixed by POLISH; "SC." labels and "camera light" in older entries are now
    "REEL" and "lamp"; Stage9, the Director and the crew in older entries are the first version of the game, replaced on
    Nov 12. The entries from the ROOMS pass onward were made on Nov 13 (day 2), although they sit under the day 1 heading.
  - Still true at this point: no person has listened to the game's sound; the mix was set by measurement only.

- **Nov 13, 07:30 to 09:50, PLAYER agent (Claude Code, Opus): a body for teammates in co-op.** In a group game each
  player saw the others as the engine's checker cylinder. The plan was a character from Meshy, but the built-in browser
  was signed out of Meshy and of Tripo this morning (both pages showed "Log In"), and signing in needs the owner, so
  **no credits were spent** and the body was made by script instead (the brief's fallback, done with a real skeleton):
  - Blender 5.2, headless (`Tools/art/teammate/bl_build_teammate.py`): 87 metaballs placed on the bones of the
    Understudy's Tripo rig make a young urban explorer: short dark hair, a plain face (nose, brow, ears; eyes, brows and
    mouth painted in vertex colour), a jacket with the hood down as a roll round the neck and a zipped collar, jeans,
    boots with flat soles, mitten hands, a small backpack with straps. Decimated to 20,000 triangles, skinned with
    Blender's automatic weights (hands, hair, pack and hood made rigid), ambient occlusion baked into vertex colour.
  - numpy (`make_teammate_glb.py`): the GLB keeps the rig's 80 joints and its idle, walk and run (Tripo preset motions,
    made in place; the run started 0.59 units off its root and skipped 19.5 degrees at its loop, both fixed), adds two
    crouch clips derived from idle and walk (hips down 29 cm, knees bent, back leaning, head level), a camera with a lamp
    on a chest harness, and two attachment joints (the lamp, the left palm). No texture; the jacket's colour is a
    per-player tint (charcoal, olive, navy, oxblood, ochre).
  - In the game (`Player/SRCharacter`): the character's own skeletal mesh, never drawn or animated on its owner's
    machine, no collision (the capsule stays the body for every trace and every creature), a shadow for the others;
    idle, walk, run, crouch idle and crouch walk chosen by the replicated speed, crouch and sprint and played at the speed
    the player really moves; gone inside a hiding spot; cowering while the Cutter's hand is on them. The lamp's light,
    for the others, comes from the chest camera, whose face glows while the lamp is on; a carried reel hangs from the
    left hand. Gameplay unchanged: the creatures and the scares still use the eye and the aim.
  - Debug tools for co-op (not in Shipping): `SR.Debug.Body` (a stand-in teammate, poses, bring the other player, the
    Cutter at them, use a door or a reel or the splicer through the real server calls) and `-SRBodyScript`, a script
    runner that also works on a client, so a host and a client on one PC play one timeline and both take frames.
  - Co-op pass, host and client on this PC, uncooked and packaged: the other player stands, walks, runs, crouches, with
    the lamp on and off, at 9 m down the Hall; doors, a reel picked up and dropped, splicing, hiding, Action, night
    vision, the Cutter catching the client and the take lost, and the end screen were all seen correctly on both sides.
  - Checks: AutoRun 52/52, Director 29/29, SetPiece 12/12, DoorSlam 16/16, Extra 19/19, Understudy 21/21, Chase 18/18,
    Scare 41/41, NetTest host 16/16 and client 5 (uncooked and packaged), nav 0 failures, reach 0 of 54 (on a last
    full rerun Director and Extra each missed one known timing check, 28/29 and 18/19, and passed when rerun). Solo frame rate
    unchanged (A/B with and without the body in one sitting: within the 1 to 2 fps run-to-run spread; Hall 56.9). The
    host's Hall with a teammate in view: 49.5 fps (50.0 with the teammate out of sight; both processes on one laptop).
    Package 702 MB zipped (+1.4 MB); the packaged AutoRun 52/52 with 160 of 160 sounds.
  - Weak, honestly: it is a scripted figure, not a sculpted one: blobby folds, mitten hands, a doll-like face up close
    under the lamp; the Understudy's skeleton gives a slightly forward head; at the game's 3 m/s walk the walk clip runs
    at its 2.1x cap and the feet slide a little; the crouch clips are made from the walk, not captured. Frames:
    `Saved/LookShots/player_sheet.png`.

- **Nov 13, 10:00 to 11:10, VIDEO agent (Claude Code, Opus): the submission video, made from the game by one
  command.** How it is made, plainly: it is not a screen recording of someone playing. Every shot is filmed from the
  running game (uncooked, `-game`, 1920x1080) by a scripted camera at a fixed time step, and the sound is not recorded
  live: the game runs with `-nosound`, and a script lays the game's own sound files on the timeline afterwards.
  - The camera (`Source/StillRolling/UI/SRVideo.cpp`, debug only: compiled out of Shipping, and it does nothing without
    `-SRVideo=<script>`). The real player character is walked along a path through key positions with its own
    movement component, so footsteps, doors, the lamp and the creatures react as they do to a player; a slight handheld
    drift and a step bob are added to the view. The picture is the real first-person view with the real viewfinder HUD
    (REC, time code, battery), the real lamp and night vision. The engine advances exactly 1/30 s of game time per frame
    however slowly the laptop draws, and each frame is read back with the HUD and saved as a JPEG by the game itself.
    One shot is one game run (about 45 s). The staging uses the debug commands the other agents built (the Extra and
    the Understudy placed in front of the camera, the television scare fired, the Cutter teleported, turned, set
    hunting, his catch called, the cellar film started on its title card).
  - One file holds the whole cut (`Tools/video/video_script.txt`: per shot the camera keys, timed commands and sound
    lines). `Tools/video/make_video.ps1` films every shot (or only some: `-Shots 3,7`), draws the title card (Pillow,
    with the Roboto font that ships with the engine), builds the soundtrack, encodes, reads the file back to check it and
    writes a contact sheet decoded out of the MP4 (`Saved/LookShots/video_sheet.png`). A full run takes 13.4 minutes.
  - Sound (`Tools/video/make_soundtrack.py`, numpy): the game's source wavs at the game's own code volumes, each placed
    where the game logged the event in that shot (the reel picked up, the Cutter's hand on the player, then the snip and
    the father's "Cut." on the black), the footsteps from the distance the camera really walked, his boots and breath
    from his real speed and distance. Measured with `Tools/audio/measure_audio.py`: 51.7 s, 48 kHz stereo, true peak
    -1.9 dBFS, -18.8 LUFS integrated (momentary max -11.5), no clipping. Nobody has listened to it yet.
  - Encoder: nothing was downloaded or installed. Windows' own Media Foundation H.264 and AAC encoders, driven by a
    small C++ program (`Tools/video/mfencode.cpp`) built with the MSVC Build Tools already on this PC; `mfprobe.cpp`
    reads the MP4 back frame by frame. (Windows' MediaComposition API also wrote an MP4, but every frame came out twice
    and the file claimed 40 fps, so it is not used.)
  - First cut, `Saved/Video/StillRolling_video.mp4`: 51.7 s, 1920x1080, 30 fps, H.264 about 15 Mbit/s + AAC 192 kbit/s,
    92 MB. Fourteen shots: the chained gate on Marrow Lane, the walk to the porch, the front door slamming behind,
    ONE MORE TAKE in the lamp, the Extras reel picked up under HE SAT THEM IN OUR CHAIRS, the television, the mannequin
    nearer after the lamp was off, the Understudy turning to the lamp, the Cutter at the end of the Hall, Action and the
    run, hiding in the dark Parlor as he stops outside, the grab to black and CUT, OUR HAPPY HOME on the cellar wall,
    and the title card STILL ROLLING, "Alone, or with up to five."
  - Gameplay unchanged (the new file only acts with `-SRVideo`). Checks after the last code change, uncooked headless:
    AutoRun 52/52 with CheckLevel 0 problems, Chase 18/18, Scare 41/41.
- Nov 13, about 10:00 to 11:20. TEAMMATE (Opus): **the co-op body is now a Meshy model**, replacing this morning's
  scripted figure on the same asset paths, with no C++ change.
  - Generated in Meshy (the owner had signed the built-in browser back in): two reference images with Nano Banana Pro
    (task `01a1d3b6-...`, 18 credits) from this prompt: "Full-body character reference of an ordinary young adult urban
    explorer, present day, realistic and believable, not a hero or soldier. Short dark brown hair, plain calm face,
    neutral expression. Plain mid-grey zip-up hooded jacket with the hood down, plain blue jeans, worn brown leather
    boots. Small dark olive-brown backpack with dark straps over both shoulders. Compact black camera with a small lamp
    on a black chest harness strap. Realistic proportions, real hands with five relaxed fingers, natural fabric folds.
    Neutral A-pose, arms slightly away from the body, facing the viewer, front view, whole body in frame. Plain light
    grey background, soft even studio lighting, no cast shadow, realistic photographic style. No logos, no text, no
    patterns or prints on clothing." The second image (camera on a real chest harness) went to 3D twice: Smart Topology
    (15 credits, a smeared mouth, not used) and High Detail Meshy 7.1 with Meshy 7 textures (30 credits, 546k
    triangles, a clean face); Meshy's own Remesh took that to 26,667 triangles and re-baked its 2K textures; Meshy's
    auto-rig (free) and four motions from its library (free): Short Breathe and Look Around, Walking, Running, Cautious
    Crouch Walk Forward. Exported once as GLB on Meshy's "UE5 Mannequin" skeleton template, so the bones already have
    the names the game looks for. **63 Meshy credits** (balance 1,076 before, 1,113 shown after: Meshy added 100 share
    credits by itself). The "crew member never imported" turned out to be a Tripo task (Nov 12) and was left alone.
  - By script (`Tools/art/teammate/make_teammate_meshy_glb.py`): the five clips the game plays (the crouch walk made in
    place with its loop closed and its outstretched arms eased; a crouch idle made from a held crouch-walk frame with
    breathing); the model written at the size where the clips' true ground speeds equal the speeds the C++ already
    has, so the feet keep up at 3, 5.6 and 1.5 m/s without touching code; a root bone and the lamp and reel joints;
    a small lamp on the chest camera that glows for the others while the lamp is on; the jacket's grey texels drawn
    into a mask in the base colour's alpha, so each player's jacket is tinted (charcoal, olive, navy, oxblood, ochre,
    now boosted so they tell apart under the lamp); UV gutters padded. `Tools/world/import_teammate.py` gained the
    textured material path (the scripted body is kept as `SourceArt/Player/sr_teammate_scripted.glb` to go back).
  - Kept from the morning's body: the asset paths and clip names, the C++ that drives it (`Player/SRCharacter`), the
    attachment-joint names, the three material slots and the per-player tint parameter, and the look and co-op tools,
    which were rerun unchanged.
  - Checks, uncooked: AutoRun 52/52 with CheckLevel 0 problems, NetTest host 16/16 and client 5 PASS, Chase 18/18; the
    co-op walk-through (doors, a reel carried and spliced, the Cutter's catch, the ending) seen from both sides. Frame
    rate: solo Hall 56.6 (56.9 this morning); co-op host Hall with the teammate in view 49.3 and 50.3 on two runs (49.5
    this morning). Frames: `Saved/LookShots/teammate_sheet.png` (old against new).
  - Weak, honestly: the walk is an ordinary walk played at twice its pace for the game's 3 m/s (no jog in the library
    near that speed); up close under one's own lamp the face burns white like every pale surface; the fingers are
    modelled close together; the crouch walk is a theatrical sneak; one LOD only.
- **Nov 13, 12:50 to about 17:30, FIX1 agent (Claude Code, Claude Opus 5.5): what the video's camera showed was wrong
  with the fear, fixed in the game.** The VIDEO agent's first cut showed four things. (1) Inside a hiding spot the picture
  was black, even in night vision: the camera sat behind the wardrobe's solid front, so the moment these games are built
  on, watching him walk past while you hold your breath, could not happen. (2) In the Hall, lit by the house's own lamps,
  the mannequin (the Extra) slid across the floor in plain view with the camera lamp off: it kept its rule (only a lit
  camera holds it) but read as a gliding statue. (3) The Understudy reached the camera 1.1 s after the lamp came on from
  3.7 m: no time to learn what she is. (4) From 3 m the television was a small white rectangle that hardly lit the room.
  - Hiding: the picture (only the picture, on the hidden player's own machine) is now taken just outside the front of
    whatever is drawn round the spot, measured from the meshes there (wardrobes 48 cm, cellar lockers 43, the shed
    58); the capsule and the eye the creatures ask about stay inside, so no rule changed. The HUD draws the inside of the
    spot round the gap: the crack between two wardrobe doors, a locker's vent slots, the boards of the shed. The view
    turns 25 degrees each way and the frame slides a little with it; night vision's light comes from the gap. Checked
    from four spots with the Cutter walking or put in front of the gap, normal and night vision.
  - The Extra is never seen moving: while anybody has it on screen and can see it (the house's lamps light it, measured
    on the server with shadows, or that player is in night vision) it holds still even with no lamp on it; every 3 s or
    so the lamps round it stutter dark, the watcher's picture dips with them (a night picture drops to grain) and it is
    1.5 m nearer when the light comes back. The moment nobody sees it, it glides again. A lit camera lamp still freezes
    it outright. Written up in `Docs/CREATURES.md`. All lamp stutters (door slams, the Extra, the felt lamp, the
    television) now go through one place that remembers each lamp's own intensity, so two at once cannot leave a lamp
    dimmed.
  - The Understudy now stands about 0.9 s when she first feels a light: her head goes this way and that, her breath
    catches, the film in her gate races, and the felt lamp stutters in its owner's hand; then she comes at 306 cm/s
    alone (was 323), just over a walk, well under a run. The debug hold now stops her even while she comes.
  - The television: a new screen material (coarse snow that survives the anti-aliasing, torn lines, a rolling bar, the
    film's title card for two frames before it cuts off), its light moved out of the set's own front and made stronger
    with crisp shadows, and the Parlor's electric lamps die with a stutter while it is on (their bulbs and lit shade go
    dark too), so its cold flicker is the room's light.
  - Smaller: the reel "picked up from 3.09 m" was the video tool calling the server's range check directly (it allows
    150 cm for latency); a player can only use what the 2.6 m trace finds, and the video tool is now held to that. The
    lamp's near-dimming now sees a teammate in front, so a friend at a metre is no longer burnt white.
  - Checks on the final code (uncooked): AutoRun 54/54 (two new hiding checks) with CheckLevel 0, Director 29/29,
    SetPiece 12/12, Slam 16/16, Extra 24/24 (rewritten: dark and lit runs, night vision, the jump cut, the look-away;
    one run missed its old "the door is opened: it comes through" check and passed on the rerun), Understudy 27/27
    (wind-up, speed, hold), Chase 18/18, Scare 41/41, net test host 24/24 and client 8 PASS (new: the client's own hiding
    view, the Extra held by the client's eyes alone and its jump cut seen on the client, the Understudy's wind-up on the
    client). Frame rate at 1080p: every room 57 to 72, the Parlor with the television on 64, inside the Hall wardrobe 70.
    Package: zip 710 MB (744,170,077 bytes); packaged AutoRun 54/54 with 160/160 sounds, packaged net test host 24/24 and
    client 8 PASS. Video re-filmed by `make_video.ps1`: 53.3 s, 93.2 MB, shot 11 now a real hide in the Hall wardrobe
    with him passing the crack, shot 6 the television's light and title card, shots 7 and 8 the new Extra and the
    Understudy's wind-up. Sheet: `Saved/LookShots/fix1_sheet.png`.
  - Weak, honestly: the hiding frame is a drawn overlay, not geometry (it does not match each wardrobe's own doors); the
    video's hide is dark and he reads as a silhouette; the Parlor's ceiling dome keeps glowing while its lamps are out;
    the Understudy is still only 6 cm/s faster than a walk, so careless walking loses slowly, not at once.
- **Nov 13, 14:50 to about 17:45, OPENING agent (Claude Code, Claude Fable 5.2): the first five minutes of a first run,
  measured and then staged.** Every system of the game existed and nobody had decided in what order a new player meets
  them. So the first job was to find out what a first run is, without a person: the game was given a first-time player
  of its own (`-SROpeningProbe`, debug builds only): the real character walked with the navmesh the obvious way (gate,
  porch, front door, the reel it sees, the cellar, then the rooms), opening doors with the use key, never crouching,
  running away when chased, logging every event with the run time; headless and at a fixed time step it plays a run in
  about half a minute. Twenty-three runs on the unchanged game said the same thing: no line of story before the gate;
  the first reel 3.4 m inside the front door; the Cutter's first round or the first splice brought him into the Hall;
  **the first catch came 28 to 33 s into the run in 22 of 23 runs**, the first sight of him was that catch, the first
  splice had let the mannequin loose 3 s earlier, that wardrobes hide you was never said, and every runaway run lost
  all three takes within 73 to 264 s. Details and tables: `Docs/OPENING.md`.
  - The opening, as built (`Core/SROpeningSubsystem`, decided on the server, sent to every machine): three cards on
    black before the gate (VANE HOUSE, 9 MARROW LANE; 1974, the film, no ending; you came to film what is left), 8 s,
    skippable, once per session. The Cutter is "staged" from his first second: deaf, blind, unable to catch, kept out
    of sight, until the first splice (or 150 s after the first player is inside). The porch lantern stutters on the
    path; two steps inside the front door slams, always on a first run. The reel in the Hall is THE STORM on a first run
    (THE EXTRAS lies upstairs), so the first splice brings rain and thunder, not a second creature. Picking it up is
    answered: a board overhead, "Quiet.", the lamps stutter, and his boots cross the Landing to the head of the stairs,
    where he stands looking down, 8 m away and sixteen steps up, until he has been looked at for 2.4 s; then he walks
    away and is gone. One line at the foot of the screen says, when it is needed, that wardrobes and lockers hide you,
    where the cellar door is, and "hide" when the first chase begins. That first chase is the first splice: he starts
    31.7 m of path away by a way that needs no locked door, comes all the way to the table, and a locker stands 3.4 m
    from it. The first catch of a first run alone is a rehearsal and costs no take. The ACTION card now says whether he
    heard you or saw your lamp. Later runs of a session drop the cards, the forced slam, the Storm and the rehearsal
    and vary the rest. In a group the beats are for whoever is first, and the sight is left out while anybody is upstairs.
  - Also in this pass, asked for by the owner through the lead: two start-up cards before the main menu, once per
    launch, text only in the viewfinder's lettering (MADE WITH UNREAL ENGINE; MADE FOR THE 2026 EPIC AI MEGAJAM / THEME:
    DIRECTOR'S CUT), never in any automated mode.
  - Found by the walker and fixed: a chase could end without a Cut and leave the house in "Action" for the rest of the
    run (the fire-exit alarm pulled him out of his search); it now ends with a Cut. Found and not fixed (older than this
    pass, written up for the lead): from the front of the house his way to the splicing table goes round the outside
    and through the locked fire exit; his search picks its points on the wrong floor; an open door's leaf makes a
    pocket beside its hinge that a straight walk ends in.
  - After, same walker, same seeds, thirteen runs: nothing hunts before the first splice (39 to 50 s); he is first seen
    at 16 s, from 8.5 m, at the head of the stairs; the first catch of the walker that runs away (47 to 57 s) is the
    rehearsal; that walker now escapes in 8 of 9 runs (108 to 208 s; five of them with one take left, so the rehearsal is
    what saved them), the walker that hides in 3 of 4 (it was 1 of 4).
  - Checks on the final code: `-SROpeningTest` (new: the walker plays a first run and every beat is asked for, in order,
    inside its window, over five seeds straight, one by the rooms, and one player who ignores the path) 144 of 144 in 7
    runs; AutoRun 54/54 with CheckLevel 0, Director 29/29 (28 in the pass that ran beside another game of mine: the
    fake-leave timing check), SetPiece 12/12, Slam 16/16, Extra 24/24, Understudy 27/27 (26 once in the first pass: "she
    shuffles away"), Chase 18/18, Scare 41/41, net test host 24/24 and client 8 PASS. Two machines with the opening on:
    the cues, the four lines and the phases reach the client. Frame rate: no valid exclusive-fullscreen numbers this
    evening (the owner was working on the PC and the game was not the front window); windowed, the Hall with him at the
    head of the stairs is 1.6 frames a second under the same Hall without him.
  - Film: one continuous shot of the first minute and a half of a first run at 960x540 by the video tool with its own
    script (the submission video and its script untouched); contact sheet `Saved/LookShots/opening_sheet.png` (the two
    start-up cards, then one frame per beat with its run time).
  - Weak, honestly: nobody has played it or heard it, and its best beat is sound; he is dark at the head of the stairs
    (it reads at 1080p) and hard to see from the cellar locker; the walker is a lower bound in time and no judge of
    fear; the rehearsal and the Storm-first rule are design decisions the owner has not seen.
- **Nov 13, about 13:40 to 17:10, AUDIOLIB agent (Claude Code, Opus): real recordings for the most exposed sounds.**
  Every sound in the game so far was synthesised by our Python scripts, and nobody had listened to them. Footsteps, doors,
  breath and rain are where synthesis is most likely to sound fake on headphones, so an agent replaced the most exposed
  62 of them with real recordings.
  - First stop: We Love Indies, which the owner had opened. Its free licence asks for "our intro" (their logo video) in
    non-commercial releases, and the jam allows no logos but Unreal's, so the agent stopped before downloading anything and
    the owner chose Freesound instead, Creative Commons 0 sounds only.
  - 85 Freesound pages were read and every licence checked on the sound's own page (all CC0). 29 recordings were chosen
    and downloaded as the uploaders' original files from the owner's signed-in account. They became 62 game files:
    footsteps on wood, on the yard and cellar floor, landings and the Cutter's boots (30), doors and the wardrobe (15),
    room tone, rain and thunder (6), the player's, the Cutter's and the blind woman's breath (4), a heartbeat, the shears'
    snip and wall scrape, the television static and the film projector.
  - Tools made for it (no installs): a small Media Foundation decoder (`Tools/audio/mfdecode.cpp`), a page and licence
    checker (`lib_fetch.py`), and a numpy processor with one recipe per file (`lib_process.py`, `lib_recipes.py`): it cuts
    step sequences into single steps, trims, filters, makes seamless loops and matches each file to the loudness of the
    file it replaces, so the mix in code stays as it is.
  - **These were chosen by measurement, not by ear.** Nobody has listened to them yet. Several are clearly louder on
    headphones than the synthesised sounds at the same LUFS (the old ones hid energy in sub-bass), and a few choices are
    weak: the shears' snip, the wall scrape, the landings and the gate chain. The swap is one script and one import, with
    a script to put the synthesised set back.
  - Sourced assets: every recording is listed with its page, id, uploader and licence (CC0, no attribution required).
  - **Later the same day: batch 2.**
  - **Headphone loudness.** The first batch was matched on integrated loudness (LUFS), and the agent's own measurements
    showed several real recordings 4 to 11 dB louder on an A-weighted scale (the yard steps, door rattles, the scripted
    creak, the projector, the slams), because the synthesised files hid their energy in sub-bass that LUFS counts and
    headphones barely play. The processor got an A-weighted cap: no replacement may be more than 3 dB louder than the
    file it replaces on that scale, and quieter ones may come up by 3 dB at most. 31 files were capped; the uncapped
    versions are kept for the owner to compare by ear.
  - **What was left over, and three weak choices redone:** the house's settling creaks, the cradle, the television
    switching on and off, the reel pickup and the splicer motor are now recordings too; the shears' snip got a blade
    ring under the click so it carries, the wall scrape is now real metal dragged on concrete, the landing is a recorded
    jump, and the gate's chain is followed by a rusty gate swinging.
  - **New sounds for the mansion (44, not yet in the game):** a draught through long corridors, the room tone of a high
    hall, a grandfather clock ticking and striking, a chandelier stirring, a low piano note and a piano cluster, dust
    sheets, doors closing far away, footsteps on carpet runners and on stone, a heavy double door, an old lift, wings and
    pigeons in the attic, a rat in the walls. Each is levelled against the nearest existing sound and comes with the
    exact code lines to hook it.
  - Still chosen by measurement only: 118 files from 56 CC0 recordings, nobody has listened to them yet.
- **Nov 13, 14:30 to 16:00, VOICE agent (Claude Code, Opus): every spoken line remade with Gemini's speech generation,
  chosen by measurement, not yet heard by a person.** All 31 voice files were the offline Windows voice ("Microsoft
  David Desktop", "Microsoft Zira Desktop") processed by script: a placeholder that sounded like a computer, with
  unclear terms for shipping. The owner asked for Gemini's speech generation.
  - Tool use: Google AI Studio, Playground, speech generation, model "Gemini 3.8 Flash TTS" (`gemini-3.8-flash-tts`),
    in the owner's signed-in browser tab, no API key. 36 requests: 27 one line at a time on his first account until
    its daily limit ("quota exceeded: you have reached the daily limit of requests for this model"; nothing was
    clicked in the upgrade dialog), then, after he switched the tab to his second account, 9 requests holding 3 to 6
    lines each, separated by pauses and split by script. 61 takes measured for 31 files. Voices as the page lists them:
    Algenib ("Gravelly · Lower pitch") for the father, chosen over Charon, Enceladus and Bodi in an audition on "Quiet."
    and "Every family needs a happy ending." (roughest voice, flattest pitch, falling ends, no stray breath); Leda for
    the young woman whose calls and whispers come from the set speakers; Puck for the young man. Style instructions,
    for example the father's: "A big American man in his fifties, a film director on his own set. Low, dry, gravelly
    voice, very close to the microphone, slightly muffled as if through cloth. Calm, flat, patient, tired, absolutely
    certain. Quiet and unhurried." Every style and take is in `ASSETS.md` and `Docs/VOICE_REPORT.md`.
  - Chosen by numbers because nobody could listen: length, silence at the head, extra sound segments (a breath, a sob,
    a second word), pitch level, spread and the direction of the line's end (single words are where a generator adds a
    rising end), a roughness measure, consonant energy, loudness and clipping (`Tools/audio/voice_takes.py`).
  - Processed by `Tools/audio/voice_process.py` (numpy only), far lighter than the old chain because the voice is
    performed: no pitch shift, no doubling; the father gets a gentle low-pass for the sack hood, a little body and a small
    wooden room; the lures keep a light tape-and-small-speaker sound. Same names, format (48 kHz 16-bit mono) and
    loudness as the old files (loudest 400 ms within 0.01 dB, true peak at most -1.3 dBFS), and the father's words start
    at the same moment as before, so no code changes. "Cut." in the catch, AUDIOQA's weakest line: speech index 0.68 ->
    0.94 with POLISH2's code (the K and T now carry 6 % of the energy instead of 0.3 %).
  - Staged in `SourceArt/AudioLib/VoiceFather|VoiceCrew|VoicePA/`; `Tools/audio/voice_swap.ps1` puts them in place
    (backup in `SourceArt/Audio_synth_backup/`), `voice_restore.ps1` undoes it. Least sure, to be heard first: the one
    raised "Action!", the very flat ending line, "Cut." pitched higher than his other lines, and the young man's voice
    running high when frightened.
- **Nov 13, afternoon. FILMART agent (Opus): the cellar film becomes a real home movie; the family's photographs.**
  The payoff of the game, the film the projector throws on the cellar wall while he comes down to watch, was 16 flat
  silhouette cards drawn by script. Every card is now a frame of the family's 1974 Super 8 home movie, made with
  Meshy's image generator (model Nano Banana Pro) and finished by a new script, `Tools/look/film_frames.py`.
  - Tool use: Meshy Image, Nano Banana Pro, 24 images, **216 credits** (prompts and task ids in `ASSETS.md`). One
    family snapshot was generated first (mother, a boy of about ten, a girl of about seven, in front of a fieldstone
    house like ours) and attached as Meshy's image reference to every later picture, so the same three people appear
    in every frame; the father is never seen, only his shadow on the lawn and on the birthday wallpaper, and his hand on
    the slate. The mannequin dinner was made from the dinner frame itself, so the mannequins sit in the very chairs of
    the earlier shot; the empty swing at dusk was made from the sunny swing frame. Four images were rejected (the
    father visible in three cake frames, a slate where the hand does not hold the board).
  - The arc on the wall: countdown, the children holding up a card painted OUR HAPPY HOME, the father's long shadow on
    the lawn under A FILM BY FATHER 1974, then the reels in splice order: dinner with fixed smiles and the mother's
    place cut out of the film (pure light), the birthday with the cameraman's shadow looming on the wall, the house
    from the street with the family waving, the same dinner with mannequins in the family's clothes, the woman in
    Mother's dress with cine film wound over her eyes, the cellar stairs down to a door with light under it, the
    offcuts (the cut-out mother spliced to the next frame), then the slate in chalk (OUR HAPPY HOME, TAKE 213, DAY 61,
    DIR. FATHER, NIGHT) and THE END over the empty swing at dusk. The two spare cards hold the children on the swing and
    the family frozen on the parlor sofa beside an empty armchair. Nothing violent anywhere: the unease is stillness,
    held smiles and what is missing.
  - The script: one grade for every card (the source's own cast neutralised, S-curve, warm faded blacks and cream
    highlights, lower saturation, red-magenta drift), halation, light leaks, grain, dust, scratches, a hair in the gate
    on some cards, gate weave, rounded frame corners; every letter drawn by script in the engine's Roboto (painted on
    the children's card, chalk warped onto the slate's corners, white title letters), so no Windows font is left in the
    reel film. The projector is a light function and reads one channel, so the atlas is grey: luminance, levels and an
    exposure gamma per card that keeps the wall at one brightness through the reel (a wall simulation from the packaged
    frame's response was used to set it). Same file format as before: `SourceArt/Film/film_atlas_v2.png`, 2048 x 1536,
    8-bit grey, 4 x 4 cards of 512 x 384 in the order of `SRScreeningSubsystem`'s Card namespace. The colour frames
    (1024 x 768) are kept in `SourceArt/Film/frames/`.
  - Eleven family photographs for the walls (`SourceArt/Photos/`, prints with yellowed white borders): the family
    portrait, the same portrait with the boy's face scratched out of the emulsion, the same pose with a mannequin where
    the boy stood, the mother alone with a strip of tape across her face, the house front when it was cared for, the
    children on the swing, the birthday, the father's empty director's chair with a megaphone on it, the family on the
    lawn, the frozen parlor portrait, the stand-in Mother. Scratches and tape are drawn by script on the print.
    Placement proposal (Hall, Study, Dining, Parlor, Landing, Bedroom, ChildRoom): `Docs/agents/FILMART_placement.md`.
  - Not done here (FIX1 held the editor): the import. Contact sheet, old card / new card / colour frame for all 16 and
    the photographs: `Saved/LookShots/filmart_sheet.png`.
  - Weak, honestly: the family is the same three people from frame to frame but not identical (the girl's hair runs
    from blonde to strawberry blonde, the mother's face shifts a little between the dinner and the birthday); the slate's
    smaller chalk lines (TAKE, DAY, DIR. FATHER, NIGHT) are only just legible at the card's 512 px; the leader cards are
    still drawn, not filmed; the wall image is grey because the projector is a light function, so the warm colour lives
    only in the frames on disk.
- **Nov 13, evening. FILMART agent (Opus): the Vane family's paintings for the mansion.** The house is becoming a large
  mansion with long corridors, so it needs the paintings an old family leaves on its walls. Eighteen framed paintings,
  `SourceArt/Paintings/` (RGBA, frame included, 1024 px; the big one 2048; real sizes in `paintings.json`).
  - Tool use: Meshy Image, Nano Banana Pro, on the owner's paid Meshy plan, in FILMART's own browser tab: 14 images,
    **126 credits** (prompts in `ASSETS.md`). Four generations of the family in oils: a stern grandfather (1885), the
    grandmother (1892), Edgar's father (1925) and mother (1927), the wife's wedding portrait (1962, painted from the
    mother photograph so it is the same woman), the two children painted formally (1972, from the family snapshot),
    an empty chair with a pair of gloves on its seat; a dead garden, a hunting still life with no animals, a storm at
    sea; and one very large picture, the family posed in a drawing room with the shop mannequins in their clothes and
    places and a black gap in the curtains where the father would stand (made from the family snapshot and the
    mannequin dinner frame). Edgar himself is in none of them. Two big-painting images were rejected (the generator
    drew a canvas in perspective); two others came as a photographed canvas and were flattened or cropped by script.
  - `Tools/look/paintings.py` (numpy + Pillow): darkened amber varnish, craquelure (two crack networks), canvas weave,
    grime toward the edges, dust; the damage, all by script: two parallel shears cuts across the grandfather's face,
    faces cut out as neat ovals (holes with alpha, so the wall shows) in three portraits, water stains with tide-lines and
    drips, the wedding portrait's face lost to damp and mould, two canvases turned to the wall (linen, stretcher, keys,
    tacks); the frames: gilt or dark wood, mitred, shaded from their section profile under one light, beading, gilding
    rubbed through to the red bole on the high points, dust in the hollows. Clean and damaged versions of each portrait,
    so the damage can grow toward the Master suite. Sheet: `Saved/LookShots/paintings_sheet.png`; placement proposal:
    `Docs/agents/FILMART_paintings_placement.md`.
  - Weak, honestly: the frames are flat images (no real depth; they need a thin box behind them in the level); the gilt
    ornament is simple beading, not carved leaves; the big painting is upscaled 1.4x and is softer than the portraits up
    close; Edgar's father's portrait was flattened from a perspective view and has about 640 px of real detail across.
- **Nov 13, about 14:40 to 15:45, PROPS3D agent (Claude Code, Opus): sculpted props for the box-built furniture (Meshy).**
  The look sheets showed what a judge stares at under the camera lamp: the Tripo / Meshy furniture of Nov 12 holds up, but
  the pieces the dress scripts had built from engine cubes, cylinders, cones and spheres do not: the coat stand by the front
  door (a box hat and a red box scarf), the Hall console table and its cone lamp, every standing lamp (a cone on a stick),
  the Study desk, the dining sideboard, the plates (flat discs in the quilt material), the bathroom basin (a box on a
  cylinder). And the splicing table at the centre of the game was a green studio Moviola that reads as a filing cabinet.
  The agent ranked twelve props by how often and how close the player sees them and made nine in Meshy.
  - How: Meshy's Image tool (Nano Banana 2) made two product-photo references per prop (plain grey background, three-quarter
    view, late-1960s, worn, no text, no logos); the better one went to image-to-3D. Two Smart Topology tries failed (a fin on
    the hall table's leg; the floor lamp's pleated shade came out as slats with gaps), so the rest went through High Detail
    (Meshy 7.1) with Meshy 7 PBR textures and Meshy's free Remesh down to 5k to 12k triangles. Every model was checked in
    headless Blender renders before it was kept. The splicing bench came with flat "membranes" between its legs (in the
    generation itself: two different remeshes both had them); a script deleted those 143 faces. Another script put each
    model at real size, front on +X, pivot on the floor, with clean asset names. 438 Meshy credits; nothing bought.
  - Not in the game yet: `Tools/world/import_props2.py` imports them and swaps them in (old box parts hidden, their collision
    kept, so navigation and every check are unchanged; four lamp lights moved into the new shades; the spliced reels moved onto
    the new bench through the splicer's own editable slot offset, no C++ change). The editor agent runs it when the editor
    is free; `Docs/agents/PROPS3D_placement.md` lists every swap. Comparison: `Saved/LookShots/props3d_sheet.png`.
  - Tools: Meshy (images, image to 3D, texture, remesh), Blender 5.2 headless (look renders, scale / pivot / facing, the
    membrane clean-up), Python + Pillow (the sheet). Task ids and full prompts: `Docs/agents/PROPS3D_assets_rows.md`.
- **Nov 13, about 16:50 to 17:45, PROPS3D agent, second round (Claude Code, Opus): on the owner's new Meshy subscription.**
  The owner bought a Meshy subscription, so the lead sent PROPS3D back for what the first credits had not reached, with a new
  cap of 700 credits. Made: the kitchen's sink unit, counter and phone cabinet (one cabinet model widened or narrowed per
  place), the night tables (Bedroom twice, ChildRoom), a fork and knife on a folded napkin beside each of the six dining
  plates, and ten more props ranked by how often and how close the player meets them: the child's toy chest (until now a
  film-crew crate) and a heap of wooden blocks, the cellar's film-can shelving, workbench and steamer trunk, the bathroom
  linen cabinet, the kitchen's green enamel pendant lamp, the radio on the Hall and Landing shelves, the tricycle in the back
  yard and the porch bucket; plus the Parlor TV stand. Every one: two reference images, High Detail image to 3D with Meshy 7
  textures, Meshy's free remesh to 3k to 12k triangles, checked in Blender renders. Two needed a script after Meshy: the
  film-can shelving had flat sheets stretched across the shelves (21 faces deleted) and the TV stand came with two extra
  legs (cut off). 685 credits, nothing rejected.
  - The sofa and the table under dust sheets in the Storage cost no credits: Blender's cloth simulation drops a sheet over a
    hidden stand-in of the furniture and keeps only the cloth, which the game draws with its own dust-sheet material.
  - The lamp that "was left on" in the Parlor keeps its glow: a second prop material adds an emissive masked to the shade's
    texels (the mask is drawn from the lamp model's own geometry). The always-on lamps get a faint glow, and the two standing
    lamps that come on at "Action!" now use the new floor-lamp model as the housing the game lights up, so their shades glow on
    cue as the old cones did. No C++ change.
  - Not in the game yet: `Tools/world/import_props2.py` gained the parts `place2` and `glow` (the first nine's `place` is
    unchanged); every swap is listed in `Docs/agents/PROPS3D_placement.md`; comparison sheet
    `Saved/LookShots/props3d_sheet2.png`. Tools: Meshy (images, image to 3D, textures, remesh), Blender 5.2 headless (renders,
    scale and pivot, cloth simulation, glow masks, clean-up), Python + Pillow (sheets, masks).
- **Nov 13, afternoon. ICON agent (Opus): the game gets its own icon, the Cutter's hood; itch.io cover and banner.**
  The packaged game still carried Unreal's default icon. It now has one made from the real in-game Cutter, no
  generator and no credits: the Tripo model `sr_cutter_rigged.glb` rendered head-on in headless Blender 5.2 (Cycles on
  the CPU, so the editor kept the GPU), finished in Pillow and packed by our own small ICO writer.
  - Sources compared: the Blender render of the model, the grab frames of the catch (`Saved/Video/frames/12/`) and the
    owner's chosen concept A. The game frames were too small and too bright on the hood (the house lamps flatten it)
    to make an icon; the concept is not the game's own picture. The render won: it is the character the player meets,
    and the camera and light could be set for a square.
  - The picture: the hood three-quarter from his left, one hard key light from the right so the far side of the sack
    falls into black, a faint cool rim to keep its edge, a black world. The burlap is the model's own texture with the
    in-game dirty map multiplied in at 30% and a sparse dark-brown stain noise: old, dry marks only, no red (Teen).
    The concept and the model have **one** torn eye hole, so the icon has one too. A small red REC dot, the game's
    viewfinder motif, sits in the corner of the large sizes.
  - Small sizes are drawn, not shrunk: a second, nearly frontal render with flat light and a third render that is a
    white matte of the hood alone; from those the script builds a pale sack on black with soft form shading and the
    eye hole enlarged a little (by 12% at 32 px up to 30% at 16 px), then shrinks it in two steps and sharpens. At 16 px it is
    a pale hood about 14 pixels tall and 10 wide with a dark hole of about 3 by 3 pixels. The first try at three-quarter view read as a pale lump
    with a beak at 32 px; the frontal sack reads as a hood.
  - A first render with a subdivision modifier painted black star-shaped patches over the hood: the Tripo mesh is
    triangulated and its atlas has black gaps between islands, and smoothing pulled them in. Rendered without it.
  - How the icon reaches the exe: UnrealBuildTool compiles `Build/Windows/Application.ico` into the game exe's icon
    resource when the file exists (`UEBuildWindows.cs`, `VCToolChain.cs`), and the window and taskbar button load that
    resource; AutomationTool writes the same file into the small launcher exe at the package root
    (`WinPlatform.Automation.cs`). No project setting is needed. `Tools/art/icon/check_exe_icon.py` reads the icon
    groups out of both packaged exes and compares them byte for byte with the .ico (checked against today's package:
    both still match the engine's default icon, as expected before the next package).
  - Store art: a 630 x 500 cover and a 1920 x 1080 banner, the hood on black with STILL ROLLING in the engine's
    Roboto Bold Condensed, viewfinder corners and a REC mark. English only, no logos, no Windows fonts.
  - Files: `Build/Windows/Application.ico`, `SourceArt/Icon/` (masters, sizes, itch art, the three renders),
    `Tools/art/icon/` (`bl_render_cutter_head.py`, `make_icon.py`, `ico.py`, `check_exe_icon.py`, `render.ps1`),
    check sheet `Saved/LookShots/icon_sheet.png`.
- **Nov 13, 17:27 to 19:05, INTEGRATE agent (Claude Code, Claude Opus 5.5): the five offline passes brought into the
  game, looked at in the game, checked, packaged and filmed.** Five agents had worked without the editor that afternoon;
  none of their material had been seen or heard inside the game. This pass held the editor and put it all in.
  - Sound: `lib_swap.ps1` put the 118 Freesound CC0 files in place (74 replacements, 44 new sounds for the mansion) and
    the import gave 118 of 118 SoundWaves, 12 looping; `voice_swap.ps1` and its import gave the 31 Gemini voice lines,
    31 of 31. The synthesised set and the Windows voices are kept in `SourceArt/Audio_synth_backup/` (not shipped). The
    game still asks for 160 sounds by name and loads 160 (the 44 new ones are cooked, but nothing plays them yet).
    **Nobody has heard any of it.**
  - Two lines of code (`Audio/SRAudioDirector.cpp`): real recorded breath sounds wrong pitched far, so the Cutter's
    breath in a hunt (was 1.4) and the player's panic breath (was up to 1.35) are capped at 1.12; volumes unchanged.
  - The cellar film: FILMART's atlas replaced the silhouette cards (`T_SR_FilmReel`; the old cards are kept beside it).
    On the wall every card reads (the dinner with the mother cut out, the birthday with the cameraman's shadow, the
    mannequins, the stand-in Mother), so the lighter gamma the agent left as an option was not used.
  - The family photographs: a new re-runnable script, `Tools/world/house_photos.py` (`clean` undoes it), imports the
    eleven prints and frames, hangs or pins them; seven take over the story pass's photograph slots. Looking at each one
    under the lamp found three things a placement note could not know: the Hall's slot is behind an older canvas (which
    had hidden the old print as well), the Landing's was half under a door casing, and the Bedroom's print stood behind
    the telephone. All three moved. Also found: two of the story pass's old prints had a ground-floor height on
    upper-floor furniture and floated in the Hall doorway and the Parlor; they are hidden now.
  - Props: `import_props2.py` brought in both PROPS3D rounds, 26 models (24 from Meshy, 2 cloth-simulated dust sheets)
    in place of the furniture built from boxes, with the lamps' lights moved into the new shades and the lit-shade glow.
    Every one was looked at from 1.5 to 3 m with the lamp on, before and after: none floats, sinks or shows inside-out
    faces, none was dropped. The two "Action!" lamps stay dark until Action and light on cue; the Parlor lamp left on
    glows. Three reels spliced through the video tool lie on the new bench and the prompt still works.
  - The game exe now carries "(c) 2026 Alaa. Made for the 2026 Epic AI MegaJam." and ICON's hood icon (both exes match
    the .ico byte for byte). `ASSETS.md`, this log and `Docs/SUBMISSION.md` brought up to date (every Freesound recording
    with its uploader, Gemini speech, Meshy on the owner's paid plan, the Windows placeholder voice gone).
  - Checks, all on the first run: AutoRun 54/54 with CheckLevel 0 problems, Director 29/29, SetPiece 12/12, Slam 16/16,
    Extra 24/24, Understudy 27/27, Chase 18/18, Scare 41/41, net test host 24/24 and client 8 PASS, Opening 144/144 in
    7 runs, navigation 0 failures, reach 0 of 54. No check or game rule depends on a sound's length.
  - Frame rates at 1080p fullscreen with the game in front the whole time: Hall 56.4 (was 57.3), Study 70.8, Parlor
    60.5, Dining 57.1, Kitchen 64.2, Landing 61.5, Bedroom 60.8, Bathroom 63.2, Storage 69.8, ChildRoom 63.1, Cellar
    59.9, Hall at Action 55.5; the opening's Hall two steps in 54.5, the stairs with him at the top 53.3, the cellar with
    him 56.5. One to three frames a second lower everywhere; no room under 50.
  - Package: 779 MB zip (was 710), packaged AutoRun 54/54 with 160 of 160 sounds, packaged net test host 24/24 and
    client 8 PASS. Video re-filmed: 53.3 s, its soundtrack now built from the recorded sounds and the new voices, the new
    film card on the cellar wall in shot 13.
  - Disk: the drive ran short because Visual Studio's own background updater was downloading an update into `%TEMP%`
    (3.4 GB while this pass ran); its download folder was cleared twice and the old package and video frames removed so
    the package could start; free space fell to 11.7 GB during packaging.
  - Weak, honestly: nobody has listened to the sounds or the voices; the reels' floating name labels overlap on the
    bench at 22 cm spacing (as on the old one); with three to five players the second row of spliced reels would stand
    off the bench's front edge (not tried); the Hall floor lamp's shade shows a thin dark seam; the 44 mansion sounds
    are in the package without being played.
  - Sheet: `Saved/LookShots/integrate_sheet.png` (the film before and after on the wall, six photographs, every prop
    before and after, the splice, the Action lamp, the icon).

- **Nov 13, afternoon and evening. MANSION agent (Claude Fable 5.2), phase 1 without the editor: the house becomes a
  mansion on paper.**
  - The owner played the build and said the house is small: he wants a big abandoned mansion, and, when asked again, one he
    can walk in, with corridors. The lead (Claude) gave the job to one agent in two phases, because the editor was busy with
    the opening: phase 1 designs and writes everything without the editor, phase 2 builds and tests.
  - What the agent did in phase 1, and how:
  - Read the level's build scripts instead of guessing. That changed the plan: the two walls a wing would naturally open
    from, the end of the Hall and the Study's north wall, carry the father's writing (`TAKE 212`, `ONE MORE TAKE`), and the
    end of the Hall is the first image of the game. The agent's first plan therefore kept the wing off the Hall, behind the
    kitchen's back door. The lead (Claude) sent it back: a juror who never opens that door never learns the house is big.
    The second plan takes the window at the end of the Hall down to the floor as a glazed door, keeps the tally round it
    and moves `TAKE 212` over it, so that from the front door the player now sees the writing and, through the glass, a lit
    passage and a long gallery with a window at its far end: one sight line of 43 m from the gate. Upstairs a covered
    bridge leaves from the head of the back stair.
  - Designed an East Wing of 14 rooms and five corridors on two floors (a ballroom turned private cinema where shop
    mannequins sit as the audience, a library, a winter garden, a music room, a billiard room, servants' hall and pantry;
    upstairs a master bedroom and bathroom, the room of the woman who played Mother, a schoolroom, a sewing room with his
    shears, a nursery and a guest room whose bed is made), 67 m of corridor, a ring on the ground floor and a second ring
    through both floors for chases. 25 rooms with the old eleven.
  - Worked out from the plan why that wing cannot show from the gate (the house front fills 60 degrees of the view from
    11 m; everything behind it is hidden) and so widened the front with closed shells instead: a south wing built round a
    neighbour's house with a tower, a low orangery under the moon, dormers, a dry fountain.
  - Wrote the wing as data (`Tools/world/mansion_plan.py`: rooms, walls with openings, doors, lights, furniture) and a
    self-check in plain Python that proves, before any editor run, that rooms do not overlap, every room is closed, every
    door has its opening and its open leaf hits no furniture, the stair's rise and run are right, and nothing new enters
    an existing room. The first run of the check found eight real mistakes (a door opening into a seat row, a reel under a
    chair); they were fixed in the table. The same file draws the plan for the owner.
  - Wrote the editor scripts (`mansion_build.py`, `mansion_gameplay.py`, `mansion_dress.py`, `mansion_exterior.py`,
    `mansion_check.py`, `import_mansion_props.py`) in the style of the existing ones, additive and removable
    (`parts=clean`). None has run yet.
  - Kept a first solo run as short as it is: by the game's own code a solo run's six reels always lie on places reserved in
    the old house, so the wing's reel places only ever hold the extra reels of a team. A check proves it on the level.
  - A helper agent (Claude Opus) drove Meshy in the owner's browser for the furniture that boxes cannot be: seated
    mannequins, cinema chairs, a fountain, bookcases, a chandelier, beds and more: 19 models in an hour and a half, 905
    credits on the paid plan the owner bought that afternoon, each one fixed and looked at in Blender renders (log with
    every prompt and task id: `Docs/agents/MANSION_meshy_log.md`).
  - Tested the scripts without the editor by writing a small fake of Unreal's Python module and running the real level
    scripts and the new ones against it (`Tools/world/mansion_smoke.py`): the Python runs end to end, the two new doorways
    come out clear, and removing the wing puts everything back. It cannot see the engine, and the entry says so.
  - What a human decided: the owner asked for the mansion and then made clear it must be walkable; the lead set the stages,
    the credit cap and the rule that nothing existing moves. What the AI decided: where the wing goes and why, the room
    programme, the reel rule, the frame-rate design, the cut order.
  - Not done yet and said so: nothing is built, no frame exists, the frame rate is a design, not a measurement.

- **Nov 13, evening. STORY2 agent (Opus): the East Wing's story layer, ready to drop in.** The mansion's builder had the
  wing's wall texts first on its list of things to cut (11 to 13 hours of editor time in one night); without them the
  fourteen new rooms would be dressed but silent while the old house tells its story on every wall. This pass made the
  whole story layer without the editor (another agent held it), so it costs the builder one command per stage.
  - The story (`Docs/MANSION_STORY.md`): after Day 61 the film never stopped; the wing became his studio, each room a
    department of OUR HAPPY HOME: the servants' hall his office (tonight's call sheet: DAY 18,994, VISITORS, AS THEY
    ARRIVE), the schoolroom the children's lines (GOODNIGHT, FATHER, the girl's DADDY struck out three times), the sewing
    room his wardrobe (the family's heights, the new Mother TOO TALL, the hood's paper pattern: THE CAMERA MUST NEVER SEE
    ME, the shears' empty outline), Mother's room the make-up room (his list in her lipstick; in the dust on the
    mirror's sheet her own I AM NOT HER.), the ballroom his cinema (mannequins seated in the names of the family, the
    neighbours, the doctor, the attendance officer, the constable; the empty seats taped VISITOR; NO ONE LEAVES BEFORE THE
    END.), his bedroom the schedule (DAY 62 to DAY 18,993 crossed, 18,994 circled, STILL ROLLING.). Two voices as in the
    old house (his red-brown directions high, the family's chalk answers low and pointing the other way: NOT THAT WAY.,
    OUT), the girl's crayon, the understudy's finger. Three rooms are left almost bare on purpose. One thread for a
    careful player: twelve little crayon birds where the girl hid, each facing the next, across both floors to a real
    hiding spot: HE NEVER FOUND ME HERE.
  - Textures (`Tools/look/gen_mansion_decals.py`): 34 decals and 10 papers, numpy and Pillow, no download, no AI tool,
    no Windows font. It loads the old `gen_decals.py` as a library and uses its own stroke font and brushes, so the wing's
    walls are in the same hands as the old ones; printed and typed letters are the engine's Roboto. Powers of two, at most
    1024 px; 12.1 MB of PNG, about 17.8 MB compressed in the game. Contact sheet `Saved/LookShots/mansion_story_sheet.png`.
  - Placement (`Tools/world/mansion_story.py`): 83 decals and paper cards, every one given against a room and a wall of
    `mansion_plan.py` (or a door, an arch, the screen, an Extra's place, a seat, a piece of furniture), so a moved wall
    only changes the plan. `parts=import,place,save`, `clean`, by floor. Offline: `check` resolves every placement
    against the plan's walls, openings, paintings, pictures, sconces, lights, furniture and the dressing's hand-placed
    pieces (FAILURES=0); `smoke` runs the editor path against the mansion's fake `unreal` module (0 failures); `tour`
    writes a 66-frame look tour. Not run in the editor yet.
  - Found on the way and reported: `mansion_plan.py`'s `ROOM_MAT` reads each room's description instead of its
    wallpaper, so the wing's walls would have asked for materials that do not exist (the offline smoke test could not see it).
  - Weak, honestly: nothing is seen in the engine yet; the flat papers' orientation and three furniture heights are
    unverified; the bird thread is subtle by design and may go unnoticed by most players.

- **Nov 13, evening and night. MANSION agent (Claude Fable 5.2), phase 2 with the editor: the East Wing is built, and
  what it cost.** Fourteen rooms and nine corridors and links on two floors now stand behind the old house, reached from
  the Hall through a glazed door and from the back landing over a covered bridge: 25 rooms with the old eleven, 70 m of
  new corridor, 27 new doors, a private cinema with eleven shop mannequins seated as its audience.
  - How it was built: in the stages agreed with the lead, each ending with every scripted check green (A the front, B1
    the glazed door alone, B2 the ground floor, C the upper floor). Everything is placed by re-runnable Python scripts
    from one table (`Tools/world/mansion_plan.py`); the agent looked at every room in frames taken in the running game
    (`Saved/LookShots/mlg_*.png`, `mlu_*.png`, `wingstory_*.png`) and fixed what it saw: jars floating in the Gallery,
    bookcases where the pantry wanted shelves, a billiard lamp three times too bright, vines of the winter garden coming
    down through the schoolroom above it, a clothes line at head height inside the sewing room's door.
  - The lead's rule for the glazed door was a measurement, not a taste: build the door, the link and the Gallery first
    and measure the Hall during a chase; under 52 frames a second it becomes a solid door. It measured 52.2 and stayed.
  - The whole ground floor then cost the OLD views 4 to 8 frames a second (the porch 60 to 52) although walls hid all
    of it. The agent found the cause by switching one thing off at a time and measuring with the game window in front
    (a window behind another one is throttled and reads low, so a helper script keeps it in front and counts it):
    not the lamps' haze (no change), a little the lamps, mostly the NUMBER of small actors. 1,172 skirtings, cornices,
    casings and glazing bars alone cost the gate 1.6 frames. Walls, trim and small dressing became instanced meshes
    (about 1,800 boxes in some 100 components), the wing was taken out of Lumen and ray tracing, its lamps got draw
    distances. The first try drew the default grey checker on every instanced piece in the game while the editor showed
    them right: the master material lacked the usage flag for instanced meshes, which the editor sets in memory only.
  - What the checks caught that looking did not: three doors whose open leaf stood in geometry (the glazed door hung
    inside a 40 cm stone wall; it now hangs at the wall's outer face); a hiding spot shut in by a piano; a 20 cm rut
    with no navmesh across the new doorway to the bridge; and the opening's first chase arriving 0.3 s too soon, because
    with the wing the Cutter's starting marks in the back yard had a new short way in. He now starts at the far end of
    the Gallery, 32 m away, and comes through the glass door that the player's first splice has just unlocked.
  - What the agent did not do or got wrong first: its first front (closed shells and a tower beside the cottage) read
    to the lead as "a house with something dark beside it"; a second pass was approved, and the owner then asked for a
    properly wide mansion front, which a separate agent (FACADE) is designing; this agent only made its front pieces
    removable one by one for that. One mansion sound (`chandelier_stir`) has a row in the sound table and nothing
    plays it. The wing's reel places hold only a team's extra reels, by design: a solo run's six reels stay in the old
    house, so a first run is as short as before. Nobody has played the wing; the frames and the checks are the evidence.
  - Where it stands, with the evidence: every scripted check passed first time on the finished wing (54 of 54 in the
    automatic playthrough, 0 level problems, the creatures' and scares' tests 29, 12, 16, 24, 27, 18 and 41, the
    two-machine co-op test 24 and 8, the opening's 151 of 151; in the editor every new room, door, hiding spot, battery
    and reel place is reached on the navmesh). Frame rate at 1080p on the RTX 4050 with the ground floor finished and
    the PC quiet: Hall chase 52.5 (52.2 before the wing), Hall 57.2, gate 63.0, porch 61.0, the wing's rooms 67 to 80.
    With the upper floor in, the same tour read about 8 % lower in every view, the cellar included, which cannot see the
    wing: other agents were working on the PC by then. Measured against the cellar as a control the Hall chase had not
    moved, but that is an inference; the lead recorded the stage C frame rates as unconfirmed and gave the confirming
    measurement, with the recipe and tonight's numbers per view, to the agent who holds the PC alone next.
  - The story layer of the wing (the STORY2 agent's 83 texts, marks and papers) was placed with it and looked at frame
    by frame: papers that burned out to blank white cards under the camera lamp were made aged and matt, a paper lying
    3 cm inside a mattress was lifted to the measured bed height, and the girl's crayon bird on two cupboards, which
    could not be made out, was redrawn larger and paler on a deeper decal (the cupboard's front is bowed).
  - Disk: during the night the drive fell from 11.9 to 6.1 GB free; the cause was not the project but Visual Studio's
    background updater downloading into `%TEMP%` again. The agent reported it instead of deleting a folder a running
    process was writing; the lead stopped the updater with the owner's permission.
  - Human decisions: the owner asked for the mansion, bought the Meshy plan, approved stopping the updater and asked for
    the wider front. The lead (Claude) set the stages, the frame-rate rule and the cut order. The agent decided the room
    programme, where things stand, the instancing, the door rehanging and the new starting marks, and told the lead each.

- **Nov 13, afternoon and night. ROOMS agent (Claude Code, Claude Opus 5.5): rooms instead of joining by IP.** The owner
  asked for joining to be easy, "like rooms instead of joining by IP, and rooms with a password". The menu's HOST A GROUP
  and JOIN BY IP box are now HOST A ROOM (a name, an optional password, 2 to 5 players), FIND A ROOM (a live list of the
  rooms on the home network: a drawn padlock, players 2/5, LAN, FULL or ENDING) and JOIN BY ADDRESS as the manual way.
  - How it was done: in two phases agreed with the lead. Phase 1 (afternoon) read and decided without the editor, while
    OPENING held the compiler, and staged the code in `Tools/rooms_staging/` with an exact patch list; phase 2 (night,
    with the editor lock) applied it, built it and tested it. The design is `Docs/ROOMS.md`.
  - Decisions, each from the engine source on this PC rather than memory: discovery is our own small UDP "room beacon"
    (port 47777), not the engine's LAN sessions, because their host answers by broadcast to a port the searcher must
    also open, advertises one OS-chosen address and reports the time since the search began as "ping". Our host answers
    by unicast to whoever asked (Windows lets that through without a rule), the finder also asks 127.0.0.1, and the
    address to join is where the answer came from. The password is checked on the host in `ASRGameMode::PreLogin`; the
    client sends a salted hash as a travel option; the host refuses with a code (wrong password, full, run ending, other
    version) that the client turns into a plain line and the password prompt opens again. The password is never
    replicated and never shown in the list. A join first "knocks" on the room, so a closed, full or ending room says so
    at once; a join that gets no answer gives up after 15 s with a line instead of the engine's silent 60 s.
  - What the agent got wrong first: the first two-PC-in-one test (`-SRRoomsTest`) found that the engine reports the
    menu world as a client as soon as a connection is pending, so a join counted as done before the host's refusal came
    back (the line was right, the prompt did not reopen); the frames showed a padlock drawn over the room name, a
    password box stretched across the column, tiny "-"/"+" buttons, and a "ping" that measured game frames. All fixed
    and filmed again; every text a player reads in the rooms screens is now at the menu buttons' size.
  - Where it stands, with the evidence: the two-copies-on-one-PC rooms test passes 8 of 8 on the host (the room opens, its beacon answers, a wrong password is refused, the right one is let in, the list says 2/2 FULL, a third player would be refused, and after a retake the client is back in the locked room) and 8 of 8 on the client (it finds the room in 0.1 s, is asked for the password, is refused with the right line and the prompt reopens after the menu reloads, joins with the password typed in capitals, sees 2 players, sees the room close and leave the list when the host quits). Sixteen 1080p frames of every screen and line are in `Saved/LookShots/rooms_*.png` with a sheet. Every other check passed first time on the final code: the automatic playthrough 54 of 54 with 0 level problems, the creatures' and scares' tests 29, 12, 16, 24, 27, 18 and 41, the two-machine co-op test 24 and 8 (its direct join now goes through the room's door, which is what JOIN BY ADDRESS does), the opening's 151 of 151, and the UI tour shows PLAY ALONE going straight into the run as before. Not tested: two real PCs on a home network (only two copies on one PC), three to five players. The packaged build passed the same way: a Development package, zip 865 MB (907,221,535 bytes; under the 900 MB stop line, 779 MB before the wing), its rendered automatic playthrough 54 of 54 with 0 level problems and all 181 sounds of the table loaded, the co-op test 24 and 8, the rooms test 8 and 8, and both exes carry the game's icon.
  - Online rooms without an address (Layer 2, Epic Online Services lobbies and peer-to-peer with relay) are designed and
    staged but not built: the owner made the Developer Portal product, a Peer2Peer client policy and a client himself;
    the lead decides when it goes in.
  - Human decisions: the owner asked for rooms and passwords and set up the portal; the lead (Claude) set the two
    phases, the order of the night's work and the readability rule; the agent chose the beacon over the engine's LAN
    sessions, the hashed password option, the knock and the failure lines, and told the lead each.

- **Nov 13, night, to Nov 14, 02:10. FACADE agent (Claude Fable 5.2): the front becomes a mansion, one sculpted model
  cut to fit a house that cannot move.** The owner looked at the game's front after two passes and said it was an
  ordinary house; he showed a picture he had made himself with ChatGPT (a wide gothic-Victorian mansion, a tall gabled
  centre, a columned portico, wings with arched windows, a balustrade and dormers, round towers at the ends), asked why
  the front was not a Meshy model, and told us to write a better prompt if we could.
  - Phase 1, with no editor (another agent held it). Read the constraints before drawing anything: the house is a gable
    end 12.4 m wide, 10 m from the gate; its roof overhangs the front wall; the player's capsule touches the wall 20 cm
    behind the stone; the real door, six windows, the porch's collision, a story board on the wall, the side path and a
    neighbour 7 m away all had to stay. `Tools/world/facade_plan.py` holds the whole front as data and checks 57 such
    conditions offline.
  - Wrote its own reference: instead of the owner's moody three-quarter night picture, Meshy's image tool (Nano Banana
    Pro) was asked for the same mansion as a straight-on, evenly lit architectural model, which is what image-to-3D needs.
    His picture stayed the direction and was not uploaded anywhere.
  - Generated the whole building once (Meshy image-to-3D, highest quality, 4K texture, 3.6 million faces, remeshed by
    Meshy to 100 thousand) and an end tower separately, and cut them by script to fit the real house
    (`Tools/art/facade/bl_cut_modules.py`, headless Blender): scaled so the first window bay lands on the real windows on
    both floors, sunk so the storeys match, flattened to a 58 cm relief so the old roof never pierces its mansard, outer
    bays and towers removed, holes cut where the real door, windows and the side path are. The portico, the one piece the
    player walks through, was too coarse when cut from the big model and got its own model (17 thousand triangles after
    the cut, its pedestals beside the step, not in it). A Blender mock-up from the same data the editor script uses was
    judged before any editor time was spent; a first design of game-stone boxes and a modelled kit was dropped as the body
    when the owner asked for a model, and its arches, gates and balustrades stay where real openings must be met exactly.
  - Found how the account's credits work: Meshy showed one number but it was two pools, the tools' and the "3D Agent"
    chat's. One real request showed the Agent pays from its own pool and makes textured models too; its image step
    defaults to an OpenAI image model, which was switched to Nano Banana Pro before approving, every time.
  - Phase 2, with the editor (Nov 14, 00:30 to 02:10): the import ran clean first time; the body alone was measured
    before anything else (Hall chase 51.7 against 52.5 before); then the portico, towers, the south range, details and 13
    unshadowed lamps; the lead's notes on the frames were followed (a plain stone ceiling under the portico, warm lamps
    behind three windows); the moon's disc was moved above the north wing; hidden collision went into the two places a
    player could stand inside a model, with the navigation and reach checks green after it; the first view of a run got
    10 degrees of pitch in code so the portico and the tall window are in the first picture. Every check passed on the
    built front, and the lowest frame rate of the whole build, the Hall in a chase, read 51.4 (the rule is 50).
  - Human decisions: the owner chose the look and asked for a model; the lead (Claude) set the limits, approved the
    mock-up and decided the moon, the collision and the pitch; the agent chose the reference, the cut and the layout.

- **Nov 14, from 01:30. FINISH agent (Claude Code, Claude Opus 5.5): the front's last list, then a package.**
  The FACADE agent's session ended with the front built and measured; this agent took the rest of its list.
  - Looked at the front windows from inside the Parlor, the Dining room, the Bedroom and the child's room, and the
    Landing window: the yard, the gate and the street show through every one; nothing of the new shell's back shows (the
    model's one-sided material and its holes work). One old hanging shutter of the Bedroom window, which the front's
    clean-up did not take, still hangs across half that window, as it did before the front.
  - Fetched the five grounds models that the FACADE agent had generated with Meshy's 3D Agent and left on Meshy (a
    railing on a stone plinth, two dead trees, a stone lion, a sundial), nothing generated and no credit spent; checked
    each in a Blender render. The railing came with flat dark triangles stretched between its bars; a small script
    (`Tools/art/facade/bl_clean_railing.py`) took out the upright, broad faces above the plinth and kept the bars. Scale and
    pivot fixed, imported, and placed: the railing replaces the board fences between the forecourt and the wings (the
    fences' invisible collision stays exactly as it was, so the paths and checks see the same yard), the two trees
    stand outside the fences where they frame the front from the street and the gate's side views without touching the
    moon or the tall window, the sundial stands on the south lawn with a hidden collision box. The gate pier with its
    lantern was left out: round the brick gate pillars it would reach into the gate leaf's opening and clash with the
    stone balls and the iron arch on top, and at the portico steps it would stand in front of the columns' lanterns.
  - Measured on a quiet PC with the game in front: the kit costs nothing measurable (Hall chase 51.6 and 51.9, gate 60.3,
    porch 60.0, Hall from the front door 55.0).
  - What the checks caught: the sundial's first place closed the gap between it and the old well, and the opening's
    test walker, who ignores the path and goes round the house, was pushed round it into the area where the porch
    lantern stutters for a player walking up the path: the opening test failed one check (150 of 151). The sundial moved
    two metres, and the front's own offline check now knows that area and the walker's route. Two later runs went wrong
    in the upper floor on a level saved the instant the navigation rebuild was asked for; saved again after the rebuild
    had finished, every check passed first time: the automatic playthrough 54 of 54 with 0 level problems, the
    creatures' and scares' tests 29, 12, 16, 24, 27, 18 and 41, the co-op test 24 and 8, the opening 151 of 151, the
    rooms test 8 and 8, and the navigation, reach and mansion checks with 0 failures.
  - Packaged (Development): zip 901 MB (945,075,631 bytes; 865 MB before the front), under the jam's 1 GB with no
    texture trimmed; the packaged build passed its rendered automatic playthrough 54 of 54 with all 181 sounds loaded,
    the co-op test 24 and 8 and the rooms test 8 and 8, and both exes carry the icon.
  - The video was filmed again, 53.3 s: it now opens on the mansion through the chained gate from the street, the walk
    up the path tilts across the new front, and the Cutter's first appearance moved from the Hall to the far end of the
    East Wing's Long Gallery. A sheet sets the front as it was two nights ago, the mock-up and the game now side by side,
    with the front windows seen from inside; and one picture of the whole front with no lighting at all, the textures'
    own colours, for an artist to redraw the family's old photographs of the house from.
  - Human decisions: the owner chose the mansion; the lead (Claude) set the order, the time box for the kit and the
    frame-rate bar; the agent chose where each piece stands and what to leave out, and said why.

- **Nov 13, night. FILMART agent (Opus): the film and the photographs now show the mansion the player stands in.** The
  house became a large mansion with a final front, so every picture that showed the old cottage was made again on it.
  - Tool use: Meshy Image, Nano Banana Pro, owner's paid plan: 17 images, 153 credits (two rejected). The reference was
    our own game: the night frame from inside the gate, brightened by script and its porch board painted out, from
    which Meshy made the same mansion in 1974 daylight (fountain running, lawns kept, the tree in leaf). That picture
    then stood in for the house in every redo, next to the original picture of the family, so the people stay the same
    and only the building changes: the central fieldstone gable and the pedimented porch, the wing with its square
    spired tower on the right, the glazed orangery and a second tower on the left, the fountain, the iron gate.
  - Redone: film cards 4 (the father's shadow on the path, A FILM BY FATHER), 7 (from the open gate, the family waving
    on the porch), 10 (THE END over the empty swing at dusk) and 14 (the children on the swing in the front garden);
    photographs 01 and 02 (the family portrait, and with the boy's face scratched out), 03 (the mannequin for the boy),
    04 (the mother taped over, now by the new porch), 05 (the house front), 06 (the swing), 08 (the director's chair),
    09 (the family on the lawn); new painting for the Gallery, the estate in its good years (160 x 120 cm, gilt).
    Kept: the interiors (dinner, cake, mannequins, parlor, title, slate, the stand-in, cellar), the offcuts card, the
    birthday, parlor and stand-in prints. Old versions kept (`film_atlas_v2_oldhouse.png`, `frames/_oldhouse/`,
    `Photos/_oldhouse/`); sheet `Saved/LookShots/filmart_house_sheet.png`: old picture, new picture, the game's front.
  - Weak, honestly: in the two family portraits (01-03) the building behind the family is the mansion's stone and its
    tower and wing, but seen from the side: the front gable and pediment porch are not in that view; the generated house
    has neat white sash windows and fresh stone where the game's is dark and broken, as it should be fifty years
    earlier, but the right wing reads a little lower than the game's; the second, far tower appears only in the estate
    painting and the 1974 front view.

- **Nov 14, small hours. FILMART2 agent (Opus): the film and the photographs show the gothic front.** The front changed
  for the last time (a wide gothic mansion: central gable with the tall traceried window, columned portico with a
  pediment and lanterns, arched wings with a balustrade and dormers, a round tower with a cone at each end), so every
  picture of the house's outside was made a third time.
  - Tool use: Meshy Image, Nano Banana Pro, owner's paid plan: 12 images, 108 General credits (one rejected); no Agent
    credits. The reference was again our own game: the front in its textures' own colours, its night sky and moon
    repainted as day by script, lettering and the figure in the window painted out; Meshy made that building in 1974
    daylight, and that picture stood in for the house in every redo, next to the original picture of the family.
  - Redone, same meaning, framing and file names: film cards 4 (the father's shadow, A FILM BY FATHER), 7 (from the gate
    between the ball-capped piers, the family waving on the portico steps), 10 (THE END over the empty swing at dusk) and
    14 (the children on the swing); photographs 01-03 (the portrait under the portico with a tower behind, the boy's face
    scratched out, the mannequin), 04 (the mother taped over, now under the portico by a column and a lantern), 05 (the
    front), 06, 08, 09; the Gallery's estate painting. Checked in grey as the projector throws them: the gable and the
    towers' cones read on all four cards (THE END needed a darker band behind it over the pale stone). Sheet:
    `Saved/LookShots/filmart_house_sheet2.png` (previous picture, new picture, the game's front).
  - Weak, honestly: the 1974 house is pale cream stone everywhere where the game's has a dark rubble ground storey; its
    ground floor has arched windows instead of the game's carriage arches; in the photographs the wings are shorter and
    the towers closer than in the game; the fountain stands on the axis in the front view and the chair photo (left of
    the path in the game, and on the cards).

**Nov 14, 03:40, lead: a person has now heard the game.** Until tonight every sound in the game had been chosen and mixed by measurement only, and each entry above says so. The owner played the Desktop build that carries the Freesound recordings and the Gemini voices (the Nov 13 evening builds) with the sound on and said the sounds are good as they are; he named no single sound or line. So the replaced sounds and the voices stay as imported. Still unheard by anyone: the 21 `kill_*` sounds staged for the kill sequence, which is not in the game yet. Also decided tonight: the team name on the form and in the zip name is `SoloTake` (the owner's choice); packages up to now were named `Alaa_...` and the final one will use the new name. The jam page was read again for the size rule: "keep it under 1 GB. For bigger builds, use butler", so 1 GB is the web upload's guideline; tonight's zip is 945,075,631 bytes and the plan is to stay under 1,000,000,000.

- **Nov 13, evening. KILL agent (Claude Fable 5.2), phase 1 without the editor: the catch becomes a kill, and the camera
  keeps rolling.** The owner played the newest build for the first time and asked for one thing: when the man with the
  shears catches you he should kill you, bloodily, "horror in full, 18+ if it must be". The jam requires a Teen rating,
  so the lead told him that and gave the agent a two-phase brief, as ROOMS had: phase 1 designs, writes code into a
  staging folder and draws textures by script while another agent holds the editor and the compiler for the night; phase
  2 builds it, tunes it and tests it.
  - The idea is the one death only this game can have. The player is a person holding a camera and the game is called
    Still Rolling, so he kills the player and **the camera goes on filming**: the seize (the view wrenched to his hood),
    a hold in which only his breath is heard and the blades rise into the lamp, the strike at the lens (the picture tears
    like a struck camera, blood across the glass), a second blow heard and not seen, the camera falling and landing on
    its side on the boards with a cracked lens while REC and the time code go on counting, his boots crossing the
    picture, "Cut.", black.
  - **The Teen line is drawn at the wound.** Blood is only ever liquid on a surface (the lens, the floor, his apron),
    dark, lit by the camera's own lamp. No blade enters a body, no wound, no anatomy, no lingering on a body, no scream:
    one breath cut off and a heartbeat that stops. The victim's own body is never on their screen. One setting,
    `SR.Kill.Intensity`, gives 0 = the old catch exactly, 1 = this (shipped), 2 = more blood and a longer hold for the
    owner to compare, which the agent said plainly is past what it would defend as Teen and is not for the jam build.
  - **Nothing is replicated frame by frame.** The server decides the kill (which way he strikes, where the dropped camera
    comes to rest, which side it lies on) and sends it once; every machine plays the same sequence from the same numbers
    for as long as the victim is "seized", a state that already replicated. The stains and how many he has killed are
    replicated state, so a late joiner sees them.
  - A team mate sees his slash land on the strike, the body go down, the victim's camera on the floor with its lamp
    still on, and a stain that stays for the rest of the run. The body's fall is worked out from numbers every machine
    has rather than a ragdoll: the teammate's physics asset came from an automatic rig and has never been simulated.
  - After a kill the take is lost as before, but the next take opens just inside the front door, which slams behind
    you, instead of 30 m away at the gate (a walk an earlier agent had flagged as a reason jurors stop); and the reel
    you carried lies where you fell, next to your own stain. Alone, the first catch of a first run is still a rehearsal
    that costs nothing: the same seize and hold, the blades raised, and then he lowers them: "Again."
  - Made without the engine: the design (`Docs/KILL.md`), about 3,350 lines of C++ in a staging folder with an exact
    patch list for the ten existing files it touches (written to stay clear of two other agents' edits to the same
    files), fifteen textures drawn by a Python script, a sheet and a storyboard built from real frames so the lead and
    the owner could judge the idea before it ran, and the list of recorded sounds the kill needs, handed to the agent
    fetching sounds that same night.

- **Nov 14, morning. KILL agent (Claude Fable 5.2), phase 2 with the editor: the kill is in the game.**
  - **It built on the second try.** The 3,350 lines written blind had one compile error (a pointer type used in a header
    where it was only declared). Every patch anchor was checked against the tree first, because rooms and the mansion
    had gone in overnight; all were still there.
  - **The two things nobody could know without the engine both held**: the picture lies on its side when the camera is
    rolled 84 degrees through its additive offset, and the Cutter's slash clip follows when it is held by position, so
    the stroke lands on the strike on every machine. The first full run matched the storyboard closely enough to be set
    beside it (`Saved/LookShots/kill_game_sheet.png`: storyboard above, the game below).
  - **Tuned from frames, not from the plan.** A debug command slows the world to a quarter so each beat can be
    photographed. What the frames changed: the lens read as covered on the floor, so the sprays were thinned (blood now
    covers 5.5 % of the picture at the strike and 10.5 % with the runs and the second spatter; it was 8 and 14); his walk
    past the lens was one frame of boot, so he walks slower and farther out; his cloth, made "wetter" with each kill,
    shone like bronze by the fourth, so it barely changes roughness now; the apron spatter read as red paint under the
    lamp at arm's length, so its colour was halved; the shadow and the sideways runs were on the wrong side of the fallen
    picture.
  - **The last take was redesigned from a frame.** "He bends down and looks into the lens" showed his shins: from the
    boards the picture only reaches his knees. Now the point of the blades tips the camera up at him first, and the
    picture turns up into his hood as he bends over it, lit from below by the camera's own lamp. Three poses of his one
    slash clip were photographed and the one in which the hood looks straight down the lens was kept.
  - **What the checks found that was the agent's own doing.** The new check (`-SRKillTest`, 16 steps: a kill runs to its
    end, the take is lost exactly once, the reel and the stain are left where the player fell, the next take starts in
    the Hall, a rehearsal costs nothing, intensity 0 is the old catch, the last take ends the run) passed first time, and
    so did its two-machine version (the second player sees the body go down, the lamp on the floor, the stain, his darker
    apron). But two old tests failed, and the cause was the nice touch of the front door slamming behind a player who
    starts again in the Hall: one test looks through that doorway from the gate, and the Cutter's own test places him in
    the yard and has him hear a player who used to come back out there. The first now opens the doors again; the second
    keeps the old start at the gate for its length and says so. Every changed expectation is listed in the patch notes.
  - Checks on the final code: every one passed in a single run: the kill's own 16 of 16 and, on two
    machines, 6 and 7; the automatic playthrough 54 of 54 with 0 level problems and every sound loaded (215 of 215 table
    entries: 202 files, 13 of them listed twice for team mates); the opening 151 of 151 over seven runs (the rehearsal
    still costs no take); the Cutter 29, the set pieces 12, the slamming doors 16, the Extra 24, the Understudy 27, the
    chase 18, the scares 41; co-op 24 and 8; rooms 8 and 8; navigation, reach and mansion checks 0 failures. Two old
    steps missed once and passed on a rerun with nothing changed, as other agents have seen: the set pieces' phone and the
    Extra's door.
  - Frame rate in the tightest place, a chase down the Hall, on a quiet PC with the game in front: the chase
    itself is unchanged at 51.6 and 51.7 frames per second, and the same with three kills' stains in view and his apron
    three steps darker, so the marks cost nothing. During a kill the picture runs at 44 to 45 while he has the player and
    41 to 45 through the strike and the fall, then 48 to 53 from the floor. Measuring the old 1.3 s catch the same way
    gave 45.0: the dip belongs to his body filling the lens in the lamp, which every catch has always done; the kill
    makes it last three seconds instead of one. It is under the build's 50 bar and is said so in the known issues.
  - The lead's addition, done in the same session: the picture agent's redrawn film cards, photographs and painting
    (the ones that show the house, now with its gothic front) were imported.
  - Sounds: the ten recorded kinds (21 files, Freesound CC0, fetched by AUDIOLIB the night before) are in at the levels
    AUDIOLIB matched them to. **Nobody has listened to the kill**: it was built and judged from frames and logs only.
  - The video's shot of being caught is now the kill: the strike and the fall, cut before he walks off, his "Cut." over
    the cut. The gate's own prompt, which sat small in the middle of the first shot, is hidden for that shot by a
    new console variable. Re-filmed: 54.3 s, 1920x1080, 96 MB.
  - Package: `SoloTake_StillRolling.zip` (the team name the owner chose), Development build, 946,315,079 bytes
    (902 MB), under the jam's 1 GB with nothing trimmed; it was 945,075,631 before the kill. The packaged game passed its
    rendered automatic playthrough 54 of 54 with every sound loaded, the kill's check 16 of 16 and its two-machine check
    6 and 7, the co-op test 24 and 8, the rooms test 8 and 8, and both exes carry the icon.
  - Weak, honestly: the body's fall for other players is a stiff tip-over; in a tight corner the camera drops at the
    victim's feet; the falling drop and the body's shadow are barely seen; from behind, the floor picture is dark where
    the house has no lamp; nobody has heard it.
  - Human decisions: the owner asked for the kill and, told about the Teen rule, kept it inside the rating; the lead
    (Claude) set the brief, the Teen line's wording and the order of work; the agent chose the idea's execution, the
    numbers, what to thin and what to leave out, and said why.

- **Nov 14, 06:20 to 10:30. FINAL agent (Claude Code, Claude Fable 5.2): the last pass on the known weak points.** The
  build of 06:20 could be submitted as it was; this pass took the short list of things that were known to be weak, one at
  a time, each one looked at in frames or measured before and after. Before / after sheet: `Saved/LookShots/final_sheet.png`.
  - **Frame rate of the kill: tried, measured, left.** Profiled at a tenth of its speed (CSV and GPU profiles): the
    frame is bound by the graphics card all the way, the blood on the lens costs nothing, and the one cost that moves is
    the shadow of the camera's own lamp, which is drawn afresh every frame while the lamp is thrown about. So the lever
    the kill's builder had left untried was tried: no shadow from the lamp from the strike until the camera lies on the
    boards. Six frames of that second, with and without, look the same. But the numbers did not hold: measured both ways
    round in one run each, the strike and the fall read 43.8 and 44.4 frames per second with the shadow and 46.0 and
    46.6 without; measured again the same way two hours later, 45.2 and 45.1 with it and 41.4 and 41.2 without, with a
    54 ms frame where the shadow is switched. One kill differs from the next by more than the lever gives. It was
    taken out again, whole. The kill reads what it read: about 45 while he has the player and through the strike and
    the fall (40 in one run in five), 48 to 53 from the floor.
  - **The picture from the floor in bad places.** Three causes, each measured (the debug dump now logs every camera
    lamp: where it is, how strong, what is in front of it). The dropped lamp was a fifth of whatever the *standing*
    player's view had turned it down to, two centimetres above the lens: it grazed the boards, and from behind, where he
    stands two and a half metres from the lens, he was a shape in the dark. In the Storage the camera came to rest inside
    the model of the sheeted sofa, which is wider than the collision box of the furniture it replaced: the lens looked
    out through the model's back faces, the lamp was shut in. And a lamp that was switched off (a hunted player puts it
    out) stayed off: black from the first frame to the last. Now the lamp at rest is a hand's breadth above the lens at
    its own strength (three tenths of the full lamp; eight tenths from behind and wherever the lens looks down a long
    room), a lamp that was off is jarred alight when the camera lands, the camera's shadowless night-vision light is
    borrowed until the black as what the lamp throws about the boards, and the rest place needs room round it and is the
    one whose lens sees farthest. Frames before and after in the Storage (lamp on, lamp off), halfway up the main stairs
    and in a corner of the Bathroom: he and the boards are lit in all of them.
  - **Reading it the first time.** The lines at the foot of the screen that teach the first minutes were 14 point: 19
    now, the two rows of keys 15. On the splicing bench the cans lay in a row 22 cm apart, though a can is 32 across, with
    their names in one heap, and the second row a team of three to five needs stood off the front edge of the bench: now
    three stacks side by side, and each can's name (the scene's alone: "THE STORM") stands on its own line above its
    stack. Looked at with four cans (alone) and with all twelve reels of a five-player run laid by a debug command; not
    with five real players.
  - **The Bedroom window.** One board of an old shutter hung across it, left behind when the front became a mansion
    (it hangs at an angle and reached out of the box the front's builder cleared). Taken out the same way as the other
    31 pieces, so the old front can still be put back whole. Looked at from the room, from the path and from the gate.
  - **His routes.** From the front of the house and from upstairs the navmesh's shortest way to the cellar is round the
    outside and in by the cellar's outside door, which is locked until its reel is spliced: in a chase he burst it,
    otherwise he unlocked it, and it stood open for the rest of the run. He goes round by the cellar stair now (a way
    point of the door graph from which the way on is clear of every door a reel has yet to unlock), and no longer opens
    such a door in passing. A tour with the player at the table shows both: with the switch on, through the Hall and
    down the stair in five seconds; with it off, round the south side and through the door, as before. The message
    "no way to cellar" in every wander run of the opening's check is the test walker's own rule (it will not walk
    through a locked door and does not look for the way round): harmless, and the message now says why.
  - **Two things found on the way, both where the film is spliced.** A hunt for a player who stood at the splicing
    table went to the Dining room straight above and stood there: his goal was projected onto the navmesh with a box
    three metres up and down, and the engine takes the nearest polygon in two dimensions. The destination's own storey
    is asked first now. And a player standing in front of the bench, between it and the cinema seat, could not be caught
    at all: his navmesh ends 135 cm away and his reach was 115, so he stood and looked at them for as long as they
    stayed. Where his way ends short of a player in plain sight he reaches now (190 cm, after standing 0.7 s).
  - **The check that sometimes "gave up".** The opening's wander run (a walker who ignores the natural path) passed
    on the first full run of the checks and gave up at 600 s on the next two. The cause was in the walker, not the
    game: with a reel in hand it planned again in every frame, was sent to the same room each time, and each time its
    "no progress" clock started afresh, so when a door slammed in front of it or an open leaf stood in its way it stood
    there for the rest of the run without one "stuck" event. With the clock kept, what held it shows: the pocket beside
    an open door's leaf, twice on the upper floor (Bedroom to Bathroom, Bathroom to Landing). The walker now shuts the
    nearest open door (it shut whichever came last in the level's list: in the Bathroom, the wrong one) and does so
    again when a second leaf is in the way. One expectation changed with that, in the open: "the walker was never
    stuck for good" counted every stuck event (at most 3) and passed only because the events never showed; it counts
    the ones that shutting the door did not clear (still at most 3) and prints both numbers. Five wander runs in a
    row: five passes, every one with a reel spliced.
  - **Left, with the reason.** The cellar is still a dead end for a hunted player until the reel of the cellar door is
    spliced (it is the design: two lockers stand by the table). The pocket beside an open door's leaf stays in the game
    (a person steps back or shuts the door, and the test walker does now; changing the leaves' collision touches every
    door). The seize and the hold of the kill
    stay at 45 frames per second (see above). His search still picks its points in two dimensions. The interaction prompt is still
    14 point. Nobody has heard the kill, and nobody has played the build since this pass: every change was judged from
    frames, logs and the checks.
  - **Checks on the final code and level.** Three full runs were made, because the code changed twice after the first
    (the frame-rate lever came out; the test walker was mended). The last one, each check a single run: the kill 16 of
    16 and, on two machines, 6 and 7; the automatic playthrough 54 of 54 with 0 level problems and 215 of 215 sound
    entries loaded; the opening 151 of 151 over seven runs; the set pieces 12, the slamming doors 16, the Extra 24, the
    Understudy 27, the chase 18, the scares 41; co-op 24 and 8; rooms 8 and 8; navigation, reach and mansion checks 0
    failures. The Cutter's own check read 28 of 29 in that run and 29 of 29 on its one rerun and in the two runs
    before: its "he only pretends to leave" step measures a walk whose start is random, and this time he had 12 m to
    come back instead of 7 to 9. The Extra's known door step missed once in the second run and passed on its rerun.
  - **Frame rates, and a baseline the same hour.** The Hall chase, the build's tightest view, read 49.9, 50.8, 50.7
    and 51.3 frames per second on the final code (quiet PC, the game in front, the same tour as always), where the
    record from the night was 51.6 and 51.7. So the code as it was committed before this pass was built again and
    measured in the same hour: 51.2, 50.6 and 51.1, and eight more chases in one run between 50.3 and 51.4. The
    machine read about 0.7 lower that morning; the pass cost nothing. Hall from the front door 53.8, gate 59.3, porch
    59.6, Long Gallery 67.3, cellar 60.5.
  - **Package.** `SoloTake_StillRolling.zip`, Development build, 946,326,578 bytes (902 MB; it was 946,315,079), under
    the jam's 1 GB with nothing trimmed, the exe at the top, no symbols, no Saved folder. The packaged game passed its
    rendered automatic playthrough 54 of 54 with 0 level problems and 215 of 215 sounds, the kill's check 16 of 16 and
    its two-machine check 6 and 7, the co-op test 24 and 8 (escaped, 5 of 5 spliced), the rooms test 8 and 8, and both
    exes carry the icon. The video was not re-filmed: none of its shots shows the bench, a hint line or the Bedroom
    window's board, and its kill is the Hall kill from above, whose picture from the floor changed only in where the
    lamp lies.
  - Human decisions: the lead (Claude) wrote the list and its order and set the rule that a fix that is not solid is
    worse than the weak point; the agent chose the fixes, measured them, and kept or left each one for the reasons above.

- **Nov 14, 10:33 to 12:20. ROOMS agent (Claude Code, Claude Opus 5.5): online rooms, found and joined over the
  internet with no ports and no account.** The owner asked on Nov 13 for "rooms instead of joining by IP"; the rooms on
  the home network went in that night. Online rooms were designed and staged then (phase 1), re-checked offline this
  morning while FINAL held the editor (phase A), and built and tested now (phase B), after the owner said he can test
  with a friend on another network.
  - What it is: on HOST A ROOM a WHERE row switches a room between THIS NETWORK and ONLINE. An online room is listed
    with Epic Online Services (the owner made the product, a peer-to-peer client policy and a client in Epic's developer
    portal on Nov 13; Epic Account Services is not used); anyone with the game sees it under FIND A ROOM marked ONLINE and
    joins it with the same password prompt and the same five-player limit, over a peer-to-peer connection that crosses
    home routers or goes through Epic's relays. Each PC signs in anonymously with a device id, in the background, the
    first time HOST A ROOM or FIND A ROOM is opened; PLAY ALONE never goes online and nothing asks anyone to sign in.
  - How: the engine's EOSShared and SocketSubsystemEOS plugins and the SDK called directly from our code, because
    neither of the engine's online-subsystem plugins can sign in with a device id (the reasons, with file and line, are in
    `Docs/ROOMS.md`). The game's network driver is pointed at the peer-to-peer driver only while an online room is hosted
    or joined. One switch turns it all off: `SR.Rooms.Online=0` under `[SystemSettings]` in `Config/DefaultEngine.ini`
    (or the same two lines in `StillRolling\Saved\Config\Windows\Engine.ini` next to an unzipped game).
  - What the first real test found, and the fixes: everything online worked first time against the live service
    (sign-in, the listing, the search, the peer-to-peer join, the password refused and accepted), but two of nineteen
    checks failed. A retake makes every player reconnect; on peer-to-peer the reconnect reached the host's old
    connection, which closed while the host loaded the next take, and the engine's socket then refused the host for good,
    so the friend fell back to the menu. The client now remembers the online room it is in and, when such a reconnect is
    refused, joins it again from the menu three seconds later ("THE HOST CALLED A RETAKE. JOINING ... AGAIN"); back in
    about 16 s. And a host who quit left its room listed, because the game ended before the service heard the room was
    gone; it now waits for the answer, at most two seconds. Also found: the online library writes the product's ids into
    every log; one config line keeps them out of the logs players may send.
  - Evidence (two copies of the game on one PC, two anonymous players, the live service): online, host 10 of 10 and
    client 9 of 9, four times in the editor build and once in the package (found online only, a locked room asks for
    its password, a wrong one refused over peer-to-peer, joined over peer-to-peer and not over IP, 2 players, 2/2 FULL,
    a third refused, the retake rejoined, the room gone from the list 20 s after the host quit). With the sign-in made to
    fail on purpose: the menu says "ONLINE: NOT AVAILABLE NOW. ROOMS ON THIS NETWORK STILL WORK." within 2 to 7 s and the
    home-network rooms pass 9 and 9 untouched. Switched off: 9 and 9, no online row anywhere. The home-network rooms 8
    and 8 as before; PLAY ALONE starts as fast as before (1.74 s after the menu in the UI tour, 1.81 s before). Every
    other check on the final code, one run each: the kill 16 and on two machines 6 and 7, the automatic playthrough 54 of
    54 with 0 level problems and 215 of 215 sounds, the opening 151, the Cutter 29, set pieces 12, doors 16, the Extra
    24, the Understudy 27, the chase 18, the scares 41, co-op 24 and 8. Frames of every online screen:
    `Saved/LookShots/rooms_online_sheet.png`.
  - Package: `SoloTake_StillRolling.zip`, 954,901,562 bytes (911 MB; the online library adds 8.5 MB), built with online
    rooms on; the packaged game passed the same checks (54 of 54 and 215 of 215 sounds, 16, 6 and 7, 24 and 8, rooms 8
    and 8, online 10 and 9, fail-safe and switch 9 and 9) and both exes carry the icon. The zip without online rooms
    from FINAL's pass is kept as the fallback.
  - Not proven: a room between two homes (only one PC here; on one PC the connection is direct, not through a router or
    a relay), Windows' firewall prompt on a fresh PC, three to five players online, lag over the internet. The owner's
    test with a friend, `Docs/ROOMS_REMOTE_TEST.md`, is what proves those.
  - Human decisions: the owner asked for rooms and passwords, set up the developer portal himself and agreed to test
    with a friend; the lead (Claude) split the work into phases, set the two-hour cap on the online test and the rule
    that the home-network rooms must not change; the agent chose the plugin set, the switch, the retake rejoin and the
    wait on quit.
**Nov 14, 13:40, lead: this dev log is public.** The owner chose where: a separate public repository,
`https://github.com/alaaalbayedyty-create/StillRolling-devlog`, holding `DEVLOG.md`, `ASSETS.md`, a short README and
the cover picture, and no game code. The game's own repository stays private. Before publishing, both files were
scanned for the online service's ids, e-mail addresses, keys and paths on the owner's PC: none. The final versions of
both files are pushed there again before the entry is uploaded.

**Nov 14, 14:35, lead: the owner's two-home test, ONLINE by default, the submission build.**

- **The owner's test.** He sent the 11:56 zip to a friend on another internet connection and they played through the
  online rooms following `Docs/ROOMS_REMOTE_TEST.md`. His report: it works very well. No step-by-step record or logs
  were kept, and three to five players online at once is still untried.
- **His decision after it: a new room is ONLINE by default.** The lead made this change itself (no agent). `bDraftOnline`
  is now the player's wish and starts on; a new `GetDraftWhere()` says what OPEN THE ROOM will really do. Signed in: the
  room opens online. First sign-in still on its way: WHERE says `ONLINE    (SIGNING IN ...)` and OPEN THE ROOM answers
  `STILL SIGNING IN ONLINE. WAIT A MOMENT, OR CLICK WHERE TO HOST ON THIS NETWORK.` instead of quietly opening a room
  nobody outside can find. Service not answering, or online switched off: WHERE falls back to this network by itself
  (`THIS NETWORK    (ONLINE IS NOT AVAILABLE NOW)`). A room that fell back does not turn the wish off for the next one.
- **A fault the tests showed, fixed.** The first online run after the rebuild failed: the game stalled for 47 s while
  starting, and its first tick declared the sign-in dead ("no answer within 15 s") before the answer could be read.
  The 15 s now counts only time in which the game is ticking (`FSREosRooms::Tick`). A second failure in the same series
  (the fail-safe run: the second player did not come back after a retake) was the PC, not the code: the host's map
  load took 16.5 s instead of about 4 and the client's 20 s connection timeout ran out; the same run passed on the
  rebuilt code. A host whose map load takes longer than 20 s would lose its same-network players at a retake; this is
  not changed.
- **Checks on the final code** (editor build): same-network rooms 8 and 8, online 10 and 9 (the host's first check now
  also requires that WHERE stood on ONLINE untouched), broken sign-in 9 and 9 (now also: WHERE fell back to this
  network), switched off 9 and 9 (run on the build before the stall fix, which does not touch that path). **From the
  packaged game:** AutoRun 54/54 with 0 problems and 215 of 215 sounds, same-network rooms 8 and 8, online 10 and 9,
  broken sign-in 9 and 9, switched off 9 and 9, both exe icons match. Only the rooms code changed, so the other
  suites (Opening, Director, SetPiece, Slam, Extra, Understudy, Chase, Scare, KillTest, Net) were not run again; their
  last full run is ROOMS's of 12:00 on the code before this change.
- **The submission zip:** `SoloTake_StillRolling.zip`, 954,903,604 bytes.
- **The video** is on YouTube (uploaded by the owner): `https://youtu.be/IGEPdkHIBxM`, checked signed out: it plays,
  54.4 s. It was not uploaded by an agent: uploads through the YouTube API from an unaudited project are locked to
  private, and the browser tool's upload limit is 10 MB.
- **The itch.io page** is filled in by the owner from texts the lead prepared (cover, five screenshots taken from the
  video's own frames, description, AI statement). The owner uploads the zip and submits by hand.
