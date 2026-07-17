# Zelda RISC-V Assembly Game 🛡️⚔️

An action-adventure game heavily inspired by *The Legend of Zelda*, built entirely from scratch in **RISC-V Assembly (RV32IMF)**. The project was developed as part of the Computer Architecture coursework at the University of Brasília (UnB). It showcases low-level system design, direct bitmap memory mapping, double-buffered visual rendering, and parallel MIDI multi-track audio playback.

---

## ⚙️ How the Project Works

* **Graphics & Anti-Ghosting (Rastro):** The game utilizes double buffering (swapping between Frame 0 and Frame 1) to prevent screen tearing. To completely eliminate visual artifacts and trailing pixels ("ghosting"), the system maintains separate historical coordinates for the player and active projectiles (`LAST_POS_FRAME_0` and `LAST_POS_FRAME_1`). This ensures that as objects move, their exact previous positions are cleanly erased with background tiles on the correct frame buffer before drawing their new positions.
* **Movement & Grid-Based Collisions:** Real-time keyboard input is processed through memory-mapped I/O (polling the keyboard controller). Link's coordinates are scaled down and compared against a 20x15 grid layout matrix (`mapa1_colisao`, `mapa2_colisao`, etc.) to verify terrain walkability before updating the player's coordinate. Stepping on specific threshold cells triggers map transition pointers via a custom `PORTAL_TABLE` configuration.
* **Dynamic Dual-Track MIDI Audio:** The game features a full background soundtrack playing *Zelda's Lullaby*. It operates with independent real-time timers (`PROXIMA_NOTA_HARPA_TEMPO` and `PROXIMA_NOTA_OCARINA_TEMPO`). This allows the MIDI controller to dynamically process and play the main melody (Ocarina, instrument 79) and the accompaniment (Harp, instrument 46) concurrently without blocking the game's execution loops.
* **Combat, Shops, & Puzzles:** 
  * **Attacking:** Pressing the attack key displays an active sword sprite oriented to Link's direction for 15 frames, during which player movement is locked.
  * **Dungeon Shop:** Players can purchase consumable items (Hearts, Shield, Key) using collected rupees, triggering audio feedback and updating the HUD's 7-segment custom numeric display.
  * **Interactive Puzzle:** A locked passage can be cleared by physically striking a crystal switch (`CRISTAL_POS`), which alters the underlying map collision matrices dynamically.
* **Enemy AI & Projectile Physics:**
  * **Octorok:** Randomly roams around Map 1, automatically reversing its direction vectors upon hitting a wall or obstacle.
  * **Moblin:** A stationary enemy that tracks the player's position, dynamically computing a 2D distance vector to shoot stone projectiles targeting Link's coordinates.
* **Hidden Developer Cheats:** The codebase features built-in cheat switches mapped directly to keyboard triggers for debugging purposes (`p` for infinite rupees, `h` for full health, and `k` to spawn the key).

---

## 🎮 Controls

* **`W` `A` `S` `D`:** Move Link (Up, Left, Down, Right).
* **`J`:** Attack with Sword (only works after picking up the sword).
* **`ENTER`:** Retry/Restart the game on Game Over screen.
* **`ESC`:** Exit the game.
* **`H` `P` `K` (Cheats):** Give Max Health, Max Rupees, and Key.

---

## 🚀 How to Run

1. **Download a RISC-V Simulator** that supports Keyboard and Display MMIO (such as **RARS**, **MARS** or **Venus**).
2. **Clone this repository** and ensure all files under `/sprites` are kept in the same directory structure:
   ```bash
   git clone [https://github.com/Wahrenha/your-repository-name.git](https://github.com/Wahrenha/your-repository-name.git)
   ```
3. **Open** `Main.asm` inside your simulator.
4. **Configure the bitmap display** tool with the following specifications:
 * Unit Unit Width in Pixels: 1
 * Unit Height in Pixels: 1
 * Display Width in Pixels: 320
 * Display Height in Pixels: 240
 * Base Address: 0x10008000 (heap)
5. **Connect both the Bitmap Display** and the **Keyboard and Display MMIO Simulator** to the program.
6. Assemble(compile) and Run.
