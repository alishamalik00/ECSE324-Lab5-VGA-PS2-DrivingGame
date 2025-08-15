# ECSE 324 — Lab 5: Escape from Assembly (VGA & PS/2 in C)

This lab transitions from ARMv7 assembly to C programming on the DE1-SoC board, focusing on advanced I/O through the VGA display and PS/2 keyboard.  
You will write memory-mapped I/O drivers in C, test them individually, and integrate them into a fully playable driving game.

---

## Task 1 — VGA Driver

**Description:**  
Implements low-level VGA graphics and text control using pixel and character buffers:

- `VGA_draw_point(x, y, c)` → Draw a pixel at `(x, y)` with 16-bit RGB color.
- `VGA_clear_pixelbuff()` → Clear the entire pixel buffer to black.
- `VGA_write_char(x, y, c)` → Draw an ASCII character at `(x, y)` in the 80×60 character buffer.
- `VGA_clear_charbuff()` → Clear the entire character buffer.

**Key Details:**  
- Pixel buffer: base `0xC8000000`, 320×240 pixels, 16-bit color (5R–6G–5B).  
- Character buffer: base `0xC9000000`, 80×60 grid, byte-sized ASCII values.  
- Uses byte/halfword memory access to control specific elements.

**Example:**  
Clearing the pixel buffer and writing "HELLO" to the top-left corner in the character buffer.

---

## Task 2 — PS/2 Keyboard Driver

**Description:**  
Reads keyboard scan codes from the PS/2 data register at `0xFF200100` and outputs them to the VGA character buffer.

- `read_PS2_data(char *data)` → Reads a byte from the PS/2 device if valid.  
  Returns `1` if RVALID is set, otherwise `0`.

**Key Details:**  
- RVALID: bit 15 of PS/2 data register.  
- Low byte contains the scan code.  
- Detects both make (press) and break (release) codes.  
- Works with typematic delay/rate for held keys.

**Example:**  
Typing `A` outputs its make code `1C` and break code `F0 1C` to the VGA character display.

---

## Task 3 — Driving Game

**Description:**  
Integrates VGA and PS/2 drivers to create a lane-based driving game:

- Player controls a car (30×48 px) at the bottom of the screen in left or right lane.
- Incoming traffic cars spawn randomly in lanes and move down.
- Score increases by 1 per successfully dodged car; speed increases by 1% each dodge.
- Game ends on collision; shows “Game Over” and final score.
- Press `S` to start or restart.

**Features:**  
- Road drawn to fixed dimensions.
- Smooth left/right movement via PS/2 keyboard input.
- Max two visible traffic cars at a time (full or partial).
- Uses ARM private timer for frame timing (`timer_expired()`).

**Example:**  
Player starts in left lane, dodges 3 cars, score = 3, game speed increased accordingly.
---

## References

- DE1-SoC Computer Manual: Sections 4.2.1, 4.2.4 (VGA), Section 4.5 (PS/2)
- ARM private timer (Section 9.1, Listing 1)
