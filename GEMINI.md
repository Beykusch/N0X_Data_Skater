# N0X Data Skater - Cube Environment Project

## Project Concept
A character (NPC MertNigga) runs on a cube with 6 unique faces. The player rotates the cube to keep the character from falling. The goal is to capture a photo on each face.

## Current Progress (2026-05-17)

### Completed:
- **Project Structure**: Workspace established in `Content/Ekin`.
- **UI System**:
    - `WBP_PolaroidSlot`: Individual slot with frame (100x125) and photo (90x90). Includes `UpdatePhoto` function.
    - `WBP_PhotoHUD`: Main HUD with 6 slots and a `Flying_Photo` instance.
    - **Animation Logic**: Smooth "Fly-to-Slot" animation using `Event Tick`, `IsMoving` boolean, and `Vector2D Interp To`.
    - **Slot Management**: Automatic indexing via `SlotArray` and `PhotosTakenCount`.

### Pending Tasks:
1. **Scene Capture Setup**:
    - Create `RT_PhotoCapture` (Render Target).
    - Attach `SceneCaptureComponent2D` to the target view/camera.
2. **Trigger Logic**:
    - Bind a key (e.g., 'F') or event to trigger `Capture Scene`.
    - Pass the resulting Texture to `StartPhotoFlyAnimation` in `WBP_PhotoHUD`.
3. **Win Condition**:
    - Detect when all 6 photos are collected.

## Score System Integration Guide (GameInstance-based)
Since the game requires keeping track of the score across level resets or game cycles, the score is managed within a custom GameInstance.

### 1. GameInstance Configuration (`GI_DataSkater`)
We registered the default GameInstance as `/Game/Ekin/Blueprints/GI_DataSkater.GI_DataSkater_C` in `Config/DefaultEngine.ini`.

**Steps to create inside Unreal Editor:**
- Navigate to `Content/Ekin/Blueprints/`.
- Right-click -> **Blueprint Class** -> search/select **GameInstance** as parent. Name it `GI_DataSkater`.
- **Variables to add:**
  - `CurrentScore` (Integer, Default: `0`): Tracks the active score.
  - `HighScore` (Integer, Default: `0`): Tracks the highest score achieved.
- **Event Dispatcher to add:**
  - `OnScoreChanged` (Parameters: `NewScore` [Integer], `NewHighScore` [Integer]): To dynamically update UI.
- **Functions to add:**
  - `AddScore` (Input: `Amount` [Integer]):
    - Formula: `CurrentScore = CurrentScore + Amount`.
    - Check if `CurrentScore > HighScore`. If true, set `HighScore = CurrentScore`.
    - Call Event Dispatcher `OnScoreChanged` passing `CurrentScore` and `HighScore`.
  - `ResetScore`:
    - Set `CurrentScore = 0`.
    - Call Event Dispatcher `OnScoreChanged` passing `0` and `HighScore`.

### 2. Triggering Surface Transition Score (Yüzey Geçiş Skoru)
Whenever the player successfully rotates the cube and transitions to a new surface (e.g., keeping MertNigga from falling):
- Locate the transition logic in your cube/rotation actor (e.g., `BP_WorldCube_Ekin` or character blueprint).
- In the transition success event flow:
  - Call **Get Game Instance**.
  - **Cast to GI_DataSkater**.
  - Call **AddScore** with your preferred transition points (e.g., `100` points).

### 3. Displaying Score on UI (`WBP_PhotoHUD`)
- Open `Content/Ekin/UI/WBP_PhotoHUD`.
- In the **Designer** tab, add a vertical or horizontal layout for scores in the top-right:
  - Add `TX_Score` (Text Block, e.g. "SCORE: 000000"). Check `Is Variable`.
  - Add `TX_HighScore` (Text Block, e.g. "HIGH: 000000"). Check `Is Variable`.
- In the **Graph** tab:
  - On **Event Construct**:
    - Call **Get Game Instance** -> **Cast to GI_DataSkater**.
    - Save the casted reference as a variable `GI_Reference`.
    - Drag out from `GI_Reference` and call **Bind Event to OnScoreChanged**.
    - Create a custom event (e.g., `UpdateScoreUI`) linked to the bind.
  - In `UpdateScoreUI` (which has `NewScore` and `NewHighScore` inputs):
    - Drag `TX_Score` -> **Set Text**. Use **Format Text** with format string `SCORE: {Score}` (formatted with leading zeros if desired, or just standard integer representation).
    - Drag `TX_HighScore` -> **Set Text**. Use **Format Text** with format string `HIGH: {HighScore}`.
  - (Optional) Call `UpdateScoreUI` manually on Construct using the initial values of `CurrentScore` and `HighScore` from `GI_Reference` so the UI initializes correctly before the first score change.

## Folder Structure
- `Content/Ekin/UI`: Widget Blueprints.
- `Content/Ekin/Blueprints`: Game logic.
- `Content/Ekin/Materials`: Render Targets and Materials.
