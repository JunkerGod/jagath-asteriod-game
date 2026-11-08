## IPO Table:

| Input                                      | Process                                                                                                 | Output                                                    |
| ------------------------------------------ | ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| Game start                                 | `pygame.init()` starts pygame, `set_mode()` opens the window and the groups, player and field are made | 1280 x 720 window with the player in the middle           |
| Window close event                         | `pygame.event.get()` checks if the event type is `pygame.QUIT`                                          | Game loop ends and the program exits                      |
| Keys held (W, A, S, D, space) and `dt`     | `Player.update()` rotates, moves or shoots depending on the key                                         | New player rotation/position and new `Shot` objects      |
| `dt`                                       | `AsteroidField.update()` adds `dt` to a timer and spawns an asteroid once it passes 0.8 seconds          | New `Asteroid` at a random edge of the screen             |
| Asteroid and player                        | `collides_with()` checks if the distance is less than or equal to the two radii added together          | `player_hit` logged, "Game over!" printed and exit        |
| Asteroid and shot                          | `collides_with()` checks the distance, then `shot.kill()` and `asteroid.split()` are called              | `asteroid_shot` logged, shot removed, smaller asteroids   |
| Asteroid that was hit                      | `split()` kills the asteroid and makes two smaller ones if it is bigger than the minimum radius          | Two smaller, faster asteroids (or nothing if it was small)|
| All objects in `drawable`                  | Screen is filled black, then `draw()` is called on each object and `display.flip()` refreshes the screen | Frame shown on the screen                                 |
| 60 passed to `clock.tick()`                | `clock.tick(60)` waits until the frame is done and returns the time passed                               | New `dt` in seconds for the next frame                    |
|                                            |                                                                                                         |                                                           |

## Flow Diagram & Class Hierarchy Diagram:
![[Flow Diagram and Class Hierarchy Diagram.png]]

## Design decisions and justifications

| Decision                                         | Justification                                                                               |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------- |
| Treat every object as a circle for collisions    | checking a triangle against a circle is hard, circles only need the distance and the radii  |
| `collides_with()` is in `CircleShape`            | the player, asteroids and shots all inherit it so the code is only written once             |
| Player, Asteroid and Shot inherit `CircleShape`  | they all share a position, velocity and radius                                              |
| `AsteroidField` inherits `Sprite` only           | it doesn't have a shape, it just spawns asteroids                                           |
| Separate files for each class                    | keeps the code organised and `main.py` only has the game loop                               |
| Constants stored in `constants.py`               | speeds and sizes can be changed in one place instead of searching through the code          |
| Using groups (`updatable`, `drawable`, etc.)     | one call updates or draws everything, so the game loop doesn't get longer for every object  |
| Setting `containers` on each class               | new objects add themselves to the right groups when they are created                        |
| Multiplying movement by `dt`                     | the game moves at the same speed on a fast or slow computer                                 |
| `clock.tick(60)`                                 | limits the game to 60 FPS so it doesn't use more CPU than it needs                          |
| `shoot_timer` with a 0.3 second cooldown         | stops the player from firing a shot every frame when holding space                          |
| Negative `dt` for A and S                        | turns the ship the other way / moves backward without writing new methods                   |
| Splitting using `radius - ASTEROID_MIN_RADIUS`   | large becomes medium, medium becomes small and small disappears                             |
| Rotating the velocity by a random 20 to 50       | makes the two new asteroids go in different directions                                      |
| Multiplying the new velocity by 1.2              | smaller asteroids are faster                                                                |

## Test Cases

| Input                                                                     | Expected                             | Output                                    |
| ------------------------------------------------------------------------- | ------------------------------------ | ----------------------------------------- |
| Ran `collides_with()` with two radius 5 circles, centres 8 apart         | True                                 | True                                      |
| Ran `collides_with()` with two radius 5 circles, centres 10 apart        | True (touching)                      | True                                      |
| Ran `collides_with()` with two radius 5 circles, centres 20 apart        | False                                | False                                     |
| Ran `rotate(0.1)` on a new player                                         | rotation of 30                       | 30.0                                      |
| Ran `rotate(-0.1)` after that                                             | rotation of 0                        | 0.0                                       |
| Ran `move(0.5)` on a player at (0, 0)                                     | position (0, 100)                    | (0, 100)                                  |
| Ran `move(-0.5)` after that                                               | position (0, 0)                      | (0, 0)                                    |
| Ran `shoot()` with the player rotated 90                                  | shot velocity (-500, 0)              | (-500, 0)                                 |
| Ran `shoot()` twice straight away                                         | only 1 shot                          | 1 shot                                    |
| Held shoot for 1 second at 60 FPS                                         | 4 shots                              | 4 shots                                   |
| Ran `update(2)` on an asteroid with velocity (10, 0)                      | position (20, 0)                     | (20, 0)                                   |
| Ran `split()` on an asteroid with radius 60                               | 2 asteroids of radius 40             | [40, 40]                                  |
| Checked the speed of the new asteroids (original speed 100)               | 120                                  | [120, 120]                                |
| Ran `split()` on both medium asteroids                                    | 4 asteroids of radius 20             | [20, 20, 20, 20]                          |
| Ran `split()` on all the small asteroids                                  | 0 asteroids                          | 0 asteroids                               |
| Ran `AsteroidField.update(0.5)`                                           | no asteroid yet                      | 0 asteroids                               |
| Ran `AsteroidField.update(0.4)` after that (0.9 seconds total)            | 1 asteroid                           | 1 asteroid                                |
| Ran `uv run main.py` and didn't press any keys until an asteroid hit      | "Game over!" and the program exits   | Game over! printed, program exited        |
|                                                                           |                                      |                                           |

## Reflection on Challenges and Solutions

### Failed Test From the Log File:
One of the BootDev tests failed because `game_state.jsonl` showed the player's rotation as 0 every time. The log is rewritten every time the game runs, and I had run the game without pressing any keys, so it never changed. I fixed it by running the game properly, holding the keys down for a few seconds and then submitting without running it again.

### Stopping Too Many Shots:
When I held space the ship fired a shot every frame, so there were around 60 shots a second. I added `shoot_timer` which counts down by `dt` and `shoot()` returns straight away if the timer is above 0. After a shot it is set to `PLAYER_SHOOT_COOLDOWN_SECONDS`.

### Using Groups:
Originally I called `player.update(dt)` and `player.draw(screen)` directly. This would have gotten longer for every new object. It was confusing at first that setting `containers` on the class adds the object to the groups when it is created, but after that I only needed `updatable.update(dt)` and a loop over `drawable`.

### Limitations:
Asteroids are never removed when they leave the screen and there is no score or lives, so one hit ends the game. If I had more time I would add these.
