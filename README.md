# Zelda RISC-V Assembly Game 🛡️⚔️

An action-adventure game heavily inspired by *The Legend of Zelda*, built entirely from scratch in **RISC-V Assembly (32-bit)**. The project features graphic rendering through direct bitmap memory mapping displaying four different scenarios, full 2D movement with collision detection implemented using MMIO (Memory-Mapped I/O) keyboard polling, two different enemies with unique attack mechanics, store and inventory systems, a Head-up Display (HUD) showing player stats, an interactive puzzle, and a parallel MIDI multi-track soundtrack with sound effects. 

The gameplay showing all features (except audio) can be watched in this [Project Demo Video](https://youtu.be/O_kw-_hm8m8).

---

## 👥 Collaborators
This project was developed in collaboration with [Ph-567](https://github.com/Ph-567) and [vitor11-a](https://github.com/vitor11-a).

---

## ⚙️ How the Project Works

* **Graphics & Anti-Ghosting:** The game utilizes double buffering (swapping between Frame 0 and Frame 1) to prevent screen tearing. To completely eliminate visual artifacts and trailing pixels ("ghosting"), the system maintains separate historical coordinates for the player and active projectiles (`LAST_POS_FRAME_0` and `LAST_POS_FRAME_1`). This ensures that as objects move, their exact previous positions are cleanly erased with background tiles on the correct frame buffer before drawing their new positions.
* **Movement & Grid-Based Collisions:** Real-time keyboard input is processed through memory-mapped I/O (polling the keyboard controller). Link's coordinates are scaled down and compared against a 20x15 grid layout matrix (`mapa1_colisao`, `mapa2_colisao`, etc.) to verify terrain walkability and potential obstacles before updating the player's position. Stepping on specific transition tiles triggers map changes via a custom `PORTAL_TABLE` configuration. This table functions as a structural database containing portal information, such as connected maps, spawning coordinates, and which background tile to use for the anti-ghosting system.
* **Dynamic Dual-Track MIDI Audio:** The game features a full background soundtrack playing *Zelda's Lullaby*. It operates with independent real-time timers (`PROXIMA_NOTA_HARPA_TEMPO` and `PROXIMA_NOTA_OCARINA_TEMPO`). This allows the MIDI controller to dynamically process and play the main melody (Ocarina, instrument 79) and the accompaniment (Harp, instrument 46) concurrently without blocking the game's main execution loop.
* **Inventory & HUD:** The system tracks player items through state flags indicating acquisition (`HAS_SWORD`, `HAS_KEY`, and `HAS_SHIELD`), along with quantities for **Rupees** and **Hearts**. The inventory status and player health are rendered continuously onto the Head-up Display (HUD) filling the upper section of the screen.
* **Combat, Shops, & Puzzles:** 
  * **Attacking:** At the start of the game, Link cannot attack without a weapon. A sword is available in the starting scenario, and upon approaching it, Link picks it up. After acquiring the sword, pressing the attack key `J` displays an active sword sprite oriented to Link's facing direction for 15 frames, during which player movement is locked. Link's attack is a single-hit kill for both enemies.
  * **Shield:** The player can buy a shield in the Dungeon Shop. The shield acts as an extra life, protecting the player from a single enemy attack before breaking and disappearing.
  * **Dungeon Shop:** The player can purchase consumable items (Hearts, Shield, Key) using collected rupees, triggering sound effects and updating the HUD's custom 7-segment numeric display. Hearts heal the player's health by one, and the key is required to access the end-game portal. 
  * **Interactive Puzzle:** A locked passage can be cleared by attacking a crystal switch (`CRISTAL_POS`), which dynamically alters the underlying map collision matrices, granting access to the portal to the final scenario.
* **Enemy AI & Projectile Physics:**
  * **Octorok:** Randomly roams around Map 1, automatically reversing its movement direction vectors upon colliding with an obstacle. The Octorok damages the player directly upon contact with Link's hitbox.
  * **Moblin:** A stationary enemy that tracks the player's position, dynamically computing a 2D distance vector to shoot stone projectiles targeting Link's coordinates.
* **Win Condition:** To win the game, the player must complete the crystal puzzle to access the final area, and defeat both enemies to earn enough rupees to buy the key, which unlocks the portal to the final screen.
* **Hidden Developer Cheats:** The codebase features built-in cheat switches mapped directly to keyboard triggers for debugging purposes (`p` for infinite rupees, `h` for full health, and `k` to spawn the key).

---

## 🎮 Controls

* **`W` `A` `S` `D`:** Move Link (Up, Left, Down, Right).
* **`J`:** Attack with Sword (only works after picking up the sword).
* **`ENTER`:** Retry/Restart the game on Game Over screen.
* **`ESC`:** Exit the game.
* **`H` `P` `K` (Cheats):** Give Max Health, Max Rupees, and Key.

---

## 🚀 How to Play

1. **Download a RISC-V Simulator** that supports Keyboard and Display MMIO (such as **RARS**, **MARS**, or **Venus**).
2. **Clone this repository** and ensure all files under `/sprites` are kept in the same directory structure:
   ```bash
   git clone [https://github.com/Wahrenha/ISC-Zelda-Assembly.git](https://github.com/Wahrenha/ISC-Zelda-Assembly.git)
   ```
3. **Open** `Main.asm` inside your simulator.
4. **Configure the Bitmap Display** tool with the following specifications:
   * **Unit Width in Pixels:** 1
   * **Unit Height in Pixels:** 1
   * **Display Width in Pixels:** 320
   * **Display Height in Pixels:** 240
   * **Base Address:** `0x10008000` (heap)
5. **Connect both the Bitmap Display** and the **Keyboard and Display MMIO Simulator** to the program.
6. **Assemble (compile)** and **Have fun!**.
