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

## Folder Structure
- `Content/Ekin/UI`: Widget Blueprints.
- `Content/Ekin/Blueprints`: Game logic.
- `Content/Ekin/Materials`: Render Targets and Materials.
