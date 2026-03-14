# cub3D

A lightweight Wolfenstein-like raycasting engine written in C using MiniLibX.

This project parses a `.cub` configuration file (textures, colors, and map), validates it, then renders a textured 3D scene in real time.

## Features

- Raycasting-based first-person rendering
- Wall texturing from `.xpm` files (`NO`, `SO`, `WE`, `EA`)
- Configurable floor and ceiling RGB colors
- `.cub` parsing and validation (identifiers, map content, borders, colors)
- Keyboard movement and camera rotation

## Requirements

- Linux
- `cc` (with `-Wall -Wextra -Werror` support)
- X11 development libraries
- MiniLibX (`mlx`)
- Math library (`libm`)

The project links with:

- `-lmlx -lXext -lX11 -lm`

## Build

```bash
make
```

Extra targets:

```bash
make clean
make fclean
make re
```

Binary output:

- `./cub3D`

## Run

```bash
./cub3D map.cub
```

The program expects exactly one argument and the file must end with `.cub`.

## Controls

- `W` / `S`: move forward / backward
- `A` / `D`: strafe left / right
- `←` / `→`: rotate camera left / right
- `ESC`: quit

## `.cub` File Format

The file must include:

1. Four texture paths:
	- `NO path_to_north_texture.xpm`
	- `SO path_to_south_texture.xpm`
	- `WE path_to_west_texture.xpm`
	- `EA path_to_east_texture.xpm`
2. Two color lines:
	- `F R,G,B` for floor
	- `C R,G,B` for ceiling
3. A map using:
	- `1` wall
	- `0` empty space
	- `N`, `S`, `E`, `W` single player start/orientation

Validation enforced by the parser includes:

- Required identifiers present and non-duplicated
- Color triplets with exactly 3 values in range `0..255`
- Existing `.xpm` texture files
- Exactly one player start
- Valid map characters only
- Closed map borders (walkable/player cells cannot leak outside)

## Example

```cub
NO ./textures/colorstone.xpm
SO ./textures/greystone.xpm
WE ./textures/mossy.xpm
EA ./textures/wood.xpm

F 164,0,20
C 153,204,255

111111111111111111111
101000001000000000001
101010101011111110001
101010111011111110011
1W0000000000100000001
111111111111111111111
```

## Project Structure

- `main.c`, `mlx_tools.c`: startup, window/image lifecycle, input hooks
- `dda.c`, `dda2.c`, `texturing.c`: raycasting + textured wall rendering
- `utils_*.c`, `map_parse_1.c`, `colors_check.c`, `cub3D.c`: parsing and validation
- `get_next_line*.c`: file reading utilities
- `textures/`: texture assets

## Common Issues

- **Black window / crash on startup**: verify all texture paths in `.cub` exist and point to valid `.xpm` files.
- **Immediate `Error` on launch**: check `.cub` extension, required identifiers, and map closure.
- **Build/link errors for `mlx` or X11**: ensure MiniLibX and X11 dev packages are installed for your distro.