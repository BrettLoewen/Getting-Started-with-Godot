# Getting Started with Godot

## What is this?

This repo is a collection of the work I did while following some Godot beginner tutorials to learn the basics of the engine. It includes a 3D game, a 2D game, and some miscellaneous additional learning projects.

The guides can be found here:

-   [Step by step](https://docs.godotengine.org/en/stable/getting_started/step_by_step/index.html)
-   [2D Game](https://docs.godotengine.org/en/stable/getting_started/first_2d_game/index.html)
-   [3D Game](https://docs.godotengine.org/en/stable/getting_started/first_3d_game/index.html)

## Running

-   Download Godot 4.2 [here](https://godotengine.org/download/windows/)
    -   If you want to work with C#, download the .NET version. Otherwise download the regular version. This project does not use C#.
-   Extract the downloaded zip folder
    -   The file you care about is `Godot_v4.2-stable_win64`. This is the Godot executable. Feel free to rename it
    -   If you downloaded the .NET version, you need to keep the executable in the same folder as the GodotSharp folder
    -   Create a shortcut to the executable or pin it to the Start menu and/or taskbar if you want
-   Run Godot
-   Select the import option and follow the prompts to find this project
-   Once imported, open the project
-   Once opened, run the game by pressing the run button in the top right corner of the editor
    -   Note: This will currently just run the 3D game. This is talked about more below.

## Running the 3D Game

If you want to play the 3D game, you have a few options:

-   Open the project in Godot and hit the 'Run Project' button at the top right of the editor or press `F5`
-   You can download the executable for it from the releases section of the repo
-   The game is available on Itch.io [here](https://supremetorian-studios.itch.io/3d-godot-game)

Note: If you previously played the 2D game in editor, you'll need to reverse the autoloading music scene and main scene steps you followed

## Running the 2D Game

If you want to play the 2D game, you have the same options:

-   Open the project in Godot and hit the 'Run Project' button at the top right of the editor or press `F5`, but you'll need to do some work first:
    -   Find the `First 2D Game/Main.tscn` file in the editor, right click it, and select the option 'Set as Main Scene'
    -   Go to Project > Project Settings > Autoload and disable the music player scene so it won't be loaded into the game
    -   Now playing the game will work properly
-   You can download the executable for it from the releases section of the repo
-   The game is available on Itch.io [here](https://supremetorian-studios.itch.io/2d-godot-game)

## Notes

Additional notes that I took while following the guides and publishing these games can be found in the Notes folder. They include a walkthrough of the steps I followed, observations made about differences between the Godot and Unity game engines, and the process of exporting the games to Itch.io.
