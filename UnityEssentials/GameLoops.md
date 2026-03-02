# 📜 Game Loops
> By: Akram Taghavi-Burris | © 2026

Before touching a line of code or a visual script, we must understand the "heartbeat" of a game. While the interaction loop covers the broad player journey, the game loop is the continuous, repetitive sequence that keeps the experience alive.

It follows a three-step cycle:

-   **Decision:** The player makes a choice based on the game state (e.g., "I will try to open this door").
-   **Game Reaction:** The engine processes that choice (e.g., "Does the player have the 'Gold Key' in their inventory?").
-   **Feedback:** The game communicates the result (e.g., playing a 'locked' sound or a 'door swinging open' animation).

This **loop repeats indefinitely**, ensuring actions and responses flow seamlessly until a new stage or end-state is reached.

