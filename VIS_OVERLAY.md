Visualizing the state of your AI
================================

The `vis_overlay` branch adds support for visualizing your AI state in the game visualizer.

Installation
------------

* Clone the repository to your machine: `git clone git://github.com/j-h-a/aichallenge.git ./aichallenge`
* Change directory to the newly cloned repository: `cd aichallenge`
* Switch to the `vis_overlay` branch: `git checkout vis_overlay`
* Initialize the submodules: `git submodule init; git submodule update`

This installs all you need to use the browser-based AI visualizer, it can be installed
alongside (or even instead of) the tools directory from the main site.
Don't try to mix-and-match files between this repository and your tools directory,
use one or the other (or swap between using the two).

### Running a Game

Create or modify your game-execution script (outside the cloned repository) to
use this version of the game. It should look something like this:

`python aichallenge/ants/playgame.py "$BOT0" "$BOT1" "$BOT2" "$BOT3" --map_file $MAP --log_dir game_logs --turns $TURNS --player_seed $SEED --engine_seed $ESEED --verbose -e --turntime $TURNTIME`

*(The BOTn and MAP can point to any bots/maps in any directory, the main thing is to use the correct path for playgame.py)*

### Stand-alone real-time streaming visualizer

If you want to use the stand-alone real-time visualizer you will need to do a few more steps:

* Make sure you're in the repository directory: `cd aichallenge`
* Build the Java visualizer: `cd ants/visualizer; ant jar`
* Test that the build worked: `ant run-visualizer`

You should see the Java visualizer pop up with a sample game replay.
Now create a new version of your game-execution script to look something like this:

`python aichallenge/ants/playgame.py "$BOT0" "$BOT1" "$BOT2" "$BOT3" --map_file $MAP --log_dir game_logs --turns $TURNS --player_seed $SEED --engine_seed $ESEED --verbose -e --turntime $TURNTIME --nolaunch -So | java -jar aichallenge/ants/visualizer/build/deploy/visualizer.jar`

Visualizing AI State
--------------------

From your bot, in addition to the `o row col direction` commands you
output to control your ants' movement, you can also send the following
(case-sensitive) visualization commands:

Setting state:

* `v setFillColor r g b a`
* `v setLayer layer`
* `v setLineColor r g b a`
* `v setLineWidth w`

Drawing:

* `v arrow row1 col1 row2 col2`
* `v circle row col radius fill`
* `v line row1 col1 row2 col2`
* `v rect row col width height fill`
* `v routePlan row col plan-string`
* `v star row col inner_radius outer_radius points fill`
* `v tile row col`
* `v tileBorder row col subtile`
* `v tileSubtile row col subtile`
* `i row col rest-of-line-is-any-string-you-like`

### Aliases:

The draw commands (second word, after a `v` command) have short aliases that
you can use if preferred, to save space in the output/replay.
The aliases are made by taking the first letter of the command,
or first letter plus any capitalized letters for multi-word commands.
So the aliases are:

`sfc sl slc slw a c l r rp s t tb ts`

### Notes:

* `setLayer` sets the current visualization layer. Layer 0 is drawn on top of the map, behind the ants. Layer 1 is drawn on top of the ants and behind the fog. Layer 2 is drawn on top of the fog. The default is 1.
* `setLineWidth` sets the with for all line-drawing commands. The line width `w` is in screen-units (not map-units).
* `setLineColour` and `setFillColour` set the colour values for all line-drawing and filled area drawing commands. Colours are specified as integers for `r`, `g`, and `b` (from 0-255) and the alpha value `a` is a float (from 0.0-1.0).
* 2D positions and dimensions (`row`, `col`, `radius`, `width`, `height`, etc.) are all floating point values in map-units. The row and column values are measured from the centre of each map square so if you want to draw shapes that line up with the map square boundaries (rather than centres) you have to add/subtract 0.5 from your numbers.
* The `fill` parameter used in some commands is a boolean and should be `true` or `false` (case-sensitive).
* `line` and `arrow` will draw the shortest path between the specified points, taking map-wrapping at the edges into account.
* `arrow` draws a line with an arrow-head at the end. The arrow-head length will be the smaller of two map-squares or half the line length.
* `star` draws a star centered at (`row`, `col`) with `points` points. `inner_radius` and `outer_radius` control the size of the star and points.
* `tile` draws a filled rectangle over the specified map-square. The same effect can be achieved with rect, but this is much easier if you want to exactly cover one square. Be sure to use a transparent fill-colour unless you want to completely block out the actual game square.
* `tileBorder` and `tileSubTile`: Imagine each tile (map-square) is divided into nine sub-tiles like a naughts-and-crosses board, the `subtile` parameter is a combination of Top-Middle-Bottom and Left-Middle-Right to define which of the nine sub-tiles you want to draw. It should be one of: `TL`, `TM`, `TR`, `ML`, `MM`, `MR`, `BL`, `BM`, `BR`. `tileSubTile` fills the specified sub-tile while `tileBorder` draws a line around the edge of the tile at the specified sub-tile location, or around the whole tile if `subtile` is `MM`.
* The `i` command adds map-information to a specific tile on the current turn. In the visualizer move your mouse over the tile to see the string you specified. If you specify this command more than once for the same row and column on any turn, the additional strings will be appended on a new line.
* The `plan-string` for the `routePlan` command is a case-insensitive sequence of direction characters, for example `NNEENNWWWWSS`. It draws a line using the current line width and colour from the starting row and column along the planned route.

When you output these commands from your bot and run it under this version
of the game, you can see the AI-state visualizations for each bot by pressing
the ai-state toggle-buttons (next to the fog-of-war buttons in the visualizer).

