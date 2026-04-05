# Silksong Tutorial

A Godot 4.6 (C#) 2D platformer tutorial project inspired by Hollow Knight: Silksong, focused on implementing a fluid player controller with Silksong-style movement mechanics.

## Features

- State machine player controller with the following states:
  - Floor, Fall, Jump, Double Jump, Float, Ledge Climb, Ledge Jump, Wall Slide, Wall Jump, Wall Climb, Dash
- Coyote time
- Float mechanic with cooldown
- Ledge grab and climb with mid-climb jump
- Wall slide, wall jump, and wall climb
- Dash with cooldown and dash-jump buffer
- Pixel art sprite animations

## Controls

| Action | Key |
|--------|-----|
| Move Left | Left Arrow |
| Move Right | Right Arrow |
| Jump / Float | Space |
| Dash / Wall Climb | Shift |

## Requirements

- [Godot 4.6](https://godotengine.org/) with .NET support
- Jolt Physics (bundled with Godot 4.6)

## Getting Started

1. Clone the repo
2. Open `project.godot` in Godot 4.6
3. Run the `pixel_level` scene

## State Machine

```mermaid
stateDiagram-v2
	[*] --> FALL

	FALL --> FLOOR : is_on_floor()
	FALL --> JUMP : jump pressed & coyote time
	FALL --> DOUBLE_JUMP : jump pressed & can_double_jump
	FALL --> FLOAT : jump pressed (no jumps left)
	FALL --> LEDGE_CLIMB : toward wall + ledge detected + space above
	FALL --> WALL_SLIDE : toward wall + can_wall_slide()
	FALL --> DASH : sprint pressed & can_dash

	FLOOR --> FALL : left floor
	FLOOR --> JUMP : jump pressed
	FLOOR --> DASH : sprint pressed

	JUMP --> FALL : jump released / velocity.y > 0
	JUMP --> DOUBLE_JUMP : jump pressed
	JUMP --> DASH : sprint pressed & can_dash

	DOUBLE_JUMP --> FALL : jump released / velocity.y > 0
	DOUBLE_JUMP --> DASH : sprint pressed & can_dash

	FLOAT --> FLOOR : is_on_floor()
	FLOAT --> FALL : jump released (starts float cooldown)
	FLOAT --> LEDGE_CLIMB : toward wall + ledge + space above
	FLOAT --> WALL_SLIDE : toward wall + can_wall_slide()
	FLOAT --> DASH : sprint pressed & can_dash

	LEDGE_CLIMB --> FLOOR : animation finished
	LEDGE_CLIMB --> LEDGE_JUMP : jump pressed mid-climb

	LEDGE_JUMP --> FALL : jump released / velocity.y > 0
	LEDGE_JUMP --> DOUBLE_JUMP : jump pressed
	LEDGE_JUMP --> DASH : sprint pressed & can_dash

	WALL_SLIDE --> FLOOR : is_on_floor()
	WALL_SLIDE --> LEDGE_CLIMB : ledge detected + space above
	WALL_SLIDE --> FALL : lost wall contact
	WALL_SLIDE --> WALL_JUMP : jump pressed
	WALL_SLIDE --> WALL_CLIMB : sprint pressed & toward wall
	WALL_SLIDE --> DASH : sprint pressed & away from wall

	WALL_JUMP --> JUMP : travel distance reached / wall contact
	WALL_JUMP --> FALL : jump released / velocity.y > 0
	WALL_JUMP --> DASH : sprint pressed & can_dash

	WALL_CLIMB --> WALL_SLIDE : distance reached / hit ceiling
	WALL_CLIMB --> LEDGE_JUMP : ledge detected

	DASH --> JUMP : dash-jump buffer & coyote time
	DASH --> FLOOR : dash ended & on floor
	DASH --> FALL : dash ended & in air
	DASH --> LEDGE_JUMP : ledge detected + space above
	DASH --> WALL_SLIDE : can_wall_slide()
```

## Project Structure

```
assets/
  art/2d/          # Sprite sheets
  prefabs/player/  # Player scene
  scenes/levels/   # Level scenes
  scripts/player/  # Player controller GDScript
  resources/       # Tilesets and other resources
```
