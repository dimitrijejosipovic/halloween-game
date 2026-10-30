# Functional Specification: Halloween Edition

- **Topic:** A themed overlay on the finished Dino Runner, active automatically during the week of October 31 and optionally forced on or off from the address bar. It swaps the look to a night scene with pumpkins, moon and drifting clouds; adds bats as a second obstacle; introduces a witch's hat that unlocks candies, which fill a small row of hearts that soften a crash; and adds small spooky flourishes on jump, landing, death and score milestones. The core run-and-jump loop, controls and fairness of the original are preserved.
- **Status:** Draft
- **Author:** Dimitrije Josipovic

---

## 1. Overview and Rationale (The "Why")

Marketing wants a Halloween edition for the week of October 31 that is recognisably the same game — same controls, same reflexes, same restart loop — dressed and tuned for the season. Players should open the page during the campaign week, see a night scene with pumpkins and a moon, and keep playing exactly as they always have, with a few new things to react to: bats to jump, a witch's hat worth chasing, candies worth catching, and small spooks on the way.

Two goals shape every decision below. First, the game must stay **fair**: the pace, jump height, cactus-equivalent spacing and crash rules are the ones from the core game. A player who can clear a tight group of cacti should be able to clear a mixed group of pumpkins and bats at the same pace. Second, the game must stay **testable**: every new piece of randomness (bat timing, hat appearance, candy appearance, which milestone brings a scare vs. a treat) is driven by the same seed system as the core course, so a seeded page replays the same Halloween run from start to finish.

To keep the look coherent, the Halloween Edition commits to a **single visual style**: _night pixel-art — near-black sky, off-white silhouettes, with pumpkin-orange as the one accent colour._ The game-over puddle stays red, consistent with the core game's one-accent discipline.

Success looks like this: during the campaign week, a first-time player opens the page, sees the night scene, and starts a run within 5 seconds. They play at least three runs in their first minute without ever being blocked by an unfair obstacle or a confusing new rule. A learner who already knows the core game can describe each new behaviour (bats, hat, candies, hearts, milestone events, night-darkening) in one sentence after one playthrough.

---

## 2. Functional Requirements (The "What")

### 2.1 When the Halloween Edition is active

- The Halloween Edition is a themed layer over the core game. When it is active, the game looks and behaves as described in sections 2.2 to 2.8. When it is not active, the game looks and behaves exactly as the core game. There is one game, one set of controls, one score, one high score.
  - **Acceptance Criteria:**
    - [ ] Given the player opens the page any time from the start of October 25 to the end of November 1 in their own time zone, when the page finishes loading, then the Halloween Edition is active (night sky, pumpkins instead of cacti, moon visible).
    - [ ] Given the player opens the page outside that week, when the page finishes loading, then the core game is shown (daylight look, cacti, no moon).
    - [ ] Given the player adds `?halloween=1` to the address, when the page finishes loading, then the Halloween Edition is active regardless of the date.
    - [ ] Given the player adds `?halloween=0` to the address, when the page finishes loading, then the core game is shown regardless of the date.
    - [ ] When the Halloween Edition is active, then every control from the core game still works the same way (SPACE or UP to start, jump, and restart; no other key does anything).
    - [ ] When the Halloween Edition is active, then the high score shown on the screen is the same `HI` value the player has from the core game (there is one high score, shared across both looks).

### 2.2 Visual swap: night sky, pumpkins, moon and drifting clouds

- The daylight scene becomes a night scene. Cacti are redrawn as pumpkins. A moon hangs in the sky and clouds drift slowly across it.
  - **Acceptance Criteria:**
    - [ ] When the Halloween Edition is active, then the background is near-black and the ground is a dark silhouette (not the near-white and dark-grey of the core game).
    - [ ] When the Halloween Edition is active, then every obstacle that would be a cactus in the core game is drawn as a pumpkin instead, in the same two sizes (small and large), standing on the ground in the same positions and arriving at the same times as the core game would place them for the same seed.
    - [ ] When the Halloween Edition is active, then a round moon is visible in the upper part of the sky, in pumpkin-orange, and it does not move with the ground.
    - [ ] When the Halloween Edition is active, then at least one cloud is visible drifting slowly from right to left across the sky, in a lighter shade than the sky, slower than the ground scrolls.
    - [ ] When the Halloween Edition is active, then every drawn element uses only the three colours of the Halloween palette (near-black background, off-white silhouettes, pumpkin-orange accents); the red puddle after a crash is the only other colour.
    - [ ] When the player opens the page with the same `?seed=` number twice in the Halloween Edition, then the clouds appear at the same positions and move at the same speeds on both runs.

### 2.3 Bats as a second obstacle

- A new obstacle appears during a run: a bat flying low across the game area. Bats are jumpable the same way pumpkins are — the player clears them with the standard jump. Bats never require a new control.
  - **Acceptance Criteria:**
    - [ ] When a run has lasted at least 10 seconds in the Halloween Edition, then at some point during that run a bat appears on the right edge of the game area and moves left toward the dinosaur.
    - [ ] When a bat appears, then its wings visibly flap (at least two drawn frames alternating), so it reads as a flying bat rather than a floating shape.
    - [ ] When a bat is on the course, then it flies at a height that a standard jump clears cleanly, with a reaction margin of at least half a second at the current pace (the same safety margin the core game uses between obstacles).
    - [ ] When the dinosaur jumps over a bat, then the bat passes under the dinosaur without ending the run, exactly like a pumpkin.
    - [ ] Given any part of the dinosaur's body touches any part of a bat, when the collision occurs, then the run ends the same way it would end for a pumpkin collision (see section 2.5).
    - [ ] When bats and pumpkins both appear in the same run, then the total obstacle rate (pumpkins + bats combined) is close to the obstacle rate of the core game at the same pace, so the run does not feel more crowded than the original. Over a run of at least 60 seconds, roughly 1 in 4 obstacles is a bat and the remaining 3 in 4 are pumpkins.
    - [ ] When two consecutive obstacles appear (whether pumpkin, bat or one of each), then the gap between them is never shorter than the gap the core game would allow between two cacti at the current pace.
    - [ ] When the player opens the page with the same `?seed=` number twice in the Halloween Edition, then the bats appear at the same ticks, in the same positions, on both runs.

### 2.4 Witch's hat, candies and hearts

- Rarely during a run, a witch's hat floats across the game area at a height the dinosaur can reach by jumping. Catching one unlocks candies: from that moment on, candies occasionally appear in the same way. Catching a candy adds a heart to a small row of hearts shown near the score. Each heart absorbs one otherwise-fatal collision.
  - **Acceptance Criteria:**
    - [ ] Given a run has just started in the Halloween Edition, when the player looks at the screen, then no hearts are shown next to the score (the heart row is empty).
    - [ ] When a witch's hat appears during a run, then it floats from right to left at a height the dinosaur reaches at the top of a standard jump, in a shape clearly recognisable as a pointed hat.
    - [ ] Given a run has started in the Halloween Edition, when the run reaches its first 60 seconds, then exactly one witch's hat appears during that window; no further witch's hat appears later in the same run, whether or not the player caught it.
    - [ ] When the dinosaur touches a witch's hat, then the hat disappears with a small puff and from that moment on candies may appear during this run.
    - [ ] Given the player has caught a witch's hat in the current run, when the run continues, then candies occasionally appear on the course, floating at the same reachable height as the hat did (clearly recognisable as wrapped candies in pumpkin-orange).
    - [ ] Given the player has NOT caught a witch's hat in the current run, when the run continues, then no candies appear.
    - [ ] When the dinosaur touches a candy, then the candy disappears with a small puff and one heart is added to the heart row next to the score (up to the cap of 3 hearts; a candy caught at 3 hearts disappears with the same puff but adds nothing).
    - [ ] Given the player has at least one heart, when any part of the dinosaur touches any part of a pumpkin or bat, then one heart is removed from the row, the obstacle disappears with a small puff, the dinosaur flashes briefly for about 1 second during which no further collision ends the run, and the run continues.
    - [ ] Given the player has zero hearts, when any part of the dinosaur touches any part of a pumpkin or bat, then the run ends the same way the core game ends on a cactus collision (section 2.5 of the core game spec).
    - [ ] When a run ends, then the heart row and the "candies unlocked" state reset: the next run starts with zero hearts and no candies, until a new witch's hat is caught.
    - [ ] When the player opens the page with the same `?seed=` number twice in the Halloween Edition and plays identically on both runs, then the witch's hat and candies appear at the same moments in both runs.
    - [ ] When the heart row is shown, then it appears to the left of the score counter, with each heart drawn in pumpkin-orange, and the number of hearts shown always matches the number collected.

### 2.5 Night deepens and gets stranger the longer you survive

- The scene is already dark at the start of a run. The longer the player survives, the darker and stranger it gets, in clearly perceivable steps, up to a maximum.
  - **Acceptance Criteria:**
    - [ ] When a run starts in the Halloween Edition, then the sky is dark but the moon and clouds are clearly visible against it.
    - [ ] When a run has lasted about 30 seconds, then the sky is visibly darker than at the start, and silhouettes stand out more sharply against it.
    - [ ] When a run has lasted about 60 seconds, then a low band of off-white fog has drifted in across the ground and continues to drift from right to left, so the scene feels dimmer and stranger than at 30 seconds.
    - [ ] When a run has lasted about 90 seconds or more, then the night has reached its darkest state and does not get darker any further, no matter how long the run continues.
    - [ ] When the pace increases, then the darkening keeps step with the pace milestones from the core game (it reads as "the same clock" rather than a separate timer).
    - [ ] Given a run has been dark for a while, when a new run starts after a crash, then the night resets to its starting state (the same as the first run).
    - [ ] When the night darkens, then neither obstacles nor the dinosaur become harder to see — they remain clearly outlined against the darkening sky.

### 2.6 Small spooky flourishes on jump, landing and death

- Three moments in a run get a small visible flourish. None of them affect physics, scoring, or fairness.
  - **Acceptance Criteria:**
    - [ ] When the dinosaur leaves the ground on a jump, then a small puff of orange dust appears briefly under its feet and fades within about a quarter of a second.
    - [ ] When the dinosaur lands back on the ground after a jump, then a slightly larger puff of orange dust appears briefly under its feet and fades within about a quarter of a second.
    - [ ] When a run ends on a collision in the Halloween Edition, then a small ghost silhouette rises from the dinosaur over about 1 second and fades out, in off-white, floating upward (every death, regardless of candies caught).
    - [ ] When the jump, landing or death flourish is drawn, then the dinosaur's jump height, jump duration, running pose and collision behaviour are unchanged from the core game.

### 2.7 Milestone scares and treats

- Every score milestone during a run brings a small moment that feels either like a scare or like a treat. Scares and treats alternate so the player sees both.
  - **Acceptance Criteria:**
    - [ ] When the score reaches 500 points, then a short "scare" moment plays: the whole screen flashes a dark frame once, a bat silhouette crosses the sky, and the game continues without pause.
    - [ ] When the score reaches 1000 points, then a short "treat" moment plays: a small shower of candy sprites falls once across the top of the sky over about 1 second and fades out, and the game continues without pause.
    - [ ] When the score reaches each further multiple of 500, then the next milestone moment alternates (1500: scare, 2000: treat, 2500: scare, and so on), so a run that reaches many milestones has both.
    - [ ] When a milestone moment plays, then neither the pace nor the obstacles pause, the dinosaur keeps running, and jumping still works normally.
    - [ ] When a milestone moment plays, then nothing in it can cause a collision — the bat silhouette in the scare is only in the sky, the candies in the treat shower cannot be caught.
    - [ ] When a run ends between milestones, then the next run starts counting toward 500 again (milestones are per run, not cumulative).
    - [ ] When the player opens the page with the same `?seed=` number twice in the Halloween Edition and plays identically on both runs, then each milestone that occurs plays with the same visuals in both runs.

### 2.8 Fairness and feel

- The Halloween Edition must stay recognisably the same game. The following constraints apply across sections 2.2 to 2.7.
  - **Acceptance Criteria:**
    - [ ] When the Halloween Edition is active, then the dinosaur's jump height and jump duration are unchanged from the core game.
    - [ ] When the Halloween Edition is active, then the pace curve (starting pace, speed at 30 seconds, cap at 90 seconds) is unchanged from the core game.
    - [ ] When the Halloween Edition is active, then the score climbs at the same rate as in the core game at every pace, so the same play produces the same score to within a few points.
    - [ ] When the Halloween Edition is active, then the gap rule (never shorter than one full jump plus half a second of reaction margin at the current pace) applies to every pair of consecutive obstacles, including bats.
    - [ ] When the Halloween Edition is active with `?seed=N`, then two runs on that page present the same pumpkins, bats, witch's hat, candies, milestone outcomes, clouds and night-darkening timeline in both runs.

---

## 3. Scope and Boundaries

### In-Scope

- Automatic activation of the Halloween Edition from October 25 to November 1 inclusive (player's time zone), with `?halloween=1` and `?halloween=0` as explicit overrides.
- Visual swap: night sky background, pumpkin obstacles replacing cacti in both sizes, moon in the sky, at least one slowly drifting cloud.
- Night-darkening over the course of a run, in perceivable steps, with low drifting fog arriving around 60 seconds and the night reaching its darkest around 90 seconds and holding; reset on restart.
- Bats as a new obstacle, jumpable with the standard jump, mixed with pumpkins at a rate that keeps the overall density close to the core game.
- Witch's hat as a rare collectible; from the first hat caught in a run, candies occasionally appear; catching a candy fills one heart in a row of up to three; hearts absorb one otherwise-fatal collision each, with a short invulnerability flash after each save; everything resets on restart.
- Three small flourishes: orange dust puff on jump, orange dust puff on landing, off-white ghost on death.
- Milestone events every 500 points, alternating scare and treat; they are purely visual and cannot cause a collision.
- Full seeded reproducibility: every new piece of randomness (bats, hat, candies, milestone visuals, clouds, night-strangeness) is driven by the same seed system as the core game, so `?seed=N` replays the same Halloween run.
- The single high score, shared with the core game.

### Out-of-Scope

- Any new control. SPACE and UP remain the only controls; there is no duck button, no second jump, no action key.
- Sound and music.
- Difficulty settings, lives counters beyond the three-heart row, player-chosen power-ups.
- Visual theming for anything outside the game area (page background, favicon, title bar).
- Localisation of any new on-screen text.
- Any mobile, touch, mouse or gamepad input.
- Persisting Halloween-specific state across sessions (hearts and candies-unlocked state do not survive a crash, let alone a page reload).
- Online leaderboards, sharing, accounts, network features.
- Any change that would make a core-game run score differently from a Halloween-edition run of the same length (score formula, pace curve and jump physics are frozen).

---

## Change Log

_Dated amendments made after the spec was first written — typically by `/awos:spec` in Update Mode when a bug fix changed documented behavior. Each entry records the date, the source reference (bug id or fix description), and what behavior changed and why. Leave empty until the first amendment._
