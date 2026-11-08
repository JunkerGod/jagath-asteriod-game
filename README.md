# asteroids

Asteroids is my second [Boot.dev](https://www.boot.dev) project!

Asteroids is a version of the classic arcade game, built in Python with pygame. Fly your ship, shoot the asteroids and don't get hit. Big asteroids split into two smaller, faster ones when shot.

Run the game with the command `uv run main.py` in the CLI (requires Python 3.13 and [uv](https://docs.astral.sh/uv/)).

Controls:
 - A / D - turn left / right
 - W / S - move forward / backward
 - Space - shoot

If an asteroid touches your ship, the game prints `Game over!` and exits.

Project files include:
 - main.py (entry point and game loop)
 - player.py (ship movement and shooting)
 - asteroid.py (asteroid movement and splitting)
 - shot.py (bullets)
 - asteroidfield.py (spawns new asteroids)
 - circleshape.py (base class with collision checks)
 - constants.py (screen size, speeds, sizes)
 - logger.py (course-provided logging for the tests)

By Jagath Kiran
