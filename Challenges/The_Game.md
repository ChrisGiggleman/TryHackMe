# TryHackMe Writeup: The Game

**Difficulty:** Easy  
**Category:** Game Hacking  

---

## Description
Cipher has gone dark, hiding secrets inside a Tetris-style game.  
The goal is to hack the game and reveal the encrypted data buried in its code.  
This challenge was originally part of the Hackfinity Battle 2025 CTF.

---

## Steps Taken

### Step 1: Grab Your Tools
Before starting, we need the following tools:  

- **Godot Engine** (to open and edit the game): [Download here](https://godotengine.org/download/windows/)  
- **GDRE Decompiler** (to unpack `.pck` files and recover source): [GDRETools](https://github.com/GDRETools/gdsdecomp/releases/tag/v1.00-beta.3)  

> **Note:** Windows is recommended for this room. Using Linux makes the unpacking process extremely slow.

---

### Step 2: Reverse the Game
1. Use **GDRE Decompiler** to unpack the game file.  
2. If done correctly, this produces the full Godot project folder with all the `.gd` script files.  

*Screenshot Placeholder: GDRE Decompiler output showing unpacked project folder*

---

### Step 3: Edit the Code
1. Open the recovered project in **Godot Engine**.  
2. Navigate to `gui.gd` (you can search for this file in the FileSystem panel).  
3. Locate the function controlling score logic:

```gdscript
func _on_Board_update_score(score, lines):
    $ContainerScore / ScoreBackground / ScoreValue.text = str(score)
    $ContainerLines / LinesBackground / LinesCount.text = str(lines)
    if score >= 999999:
        $ButtonContainer / T.show()
This function only shows the flag if score >= 999999.

Edit it to bypass the check:

gdscript
Copy code
if score >= 0:
    $ButtonContainer / T.show()
Screenshot Placeholder: Edited code in Godot showing bypassed score check

Step 4: Trigger the Flag
Run the game inside Godot.

The modified logic immediately triggers the flag display.

Screenshot Placeholder: Game running showing the flag

Flag
Copy code
THM{I_CAN_READ_IT_ALL}
