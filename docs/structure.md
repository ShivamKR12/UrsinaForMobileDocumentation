# Understanding the Structure

This is the structure of a default UfM (Ursina for Mobile) game:

```text
|- assets/
|  |- game_assets/
|  |  |- ...
|  |- ursina_assets/
|  |  |- ...
|  |- logo.png
|
|- project/
|  |- builder/
|  |  |- ... [.py]
|  |- wheels/
|  |  |- ... [.whl]
|  |- settings.toml
|
|- src/
|  |- ... [.py]
|- ...
|- setup.py
```

In this section, I will go through each directory, explaining what they do and what you need to do in them.

!!! warning "Important"
    This chapter is one of the most important ones. It will teach you how to create your basic game. Please read it carefully and entirely.

## `assets` folder
The `assets` folder contains two subfolders: 

- `game_assets`: The folder you'll modify to include your own assets. Put your assets here. Thanks to the `setup_ursina_android` script, you can load your assets exactly like you normally would on desktop! No special pathing is required.
- `ursina_assets`: The folder that contains Ursina's base assets. Normally, you won't modify this folder.

## `project` folder
You generally won't modify this folder much after the initial setup. It contains the following subfolders and files:

- `builder`: You won't normally edit this folder; it contains modules that handle the build process.
- `wheels`: This folder includes basic Python wheels, like Panda3D and Ursina. If you need to include extra custom wheels, put them in this folder. 
- `settings.toml`: This is the most important file in the folder. This is where you configure your game's build settings.

### Using `project/settings.toml` 
This is the default `settings.toml`: 

```toml
[android]
id          = "your.company.app.name" # Identifier used by android. Cannot be changed once uploaded to the Play Store!
version     = "0.0.0"                 # Version of your game
name        = "My ursina game"        # Name displayed in the settings page
icon        = "assets/logo.png"       # Icon of the application
classifiers = ["Topic :: Games/Entertainment"]

[application]
name        = "MyUrsinaGame"          # Name displayed on the launcher
startfile   = "src/__main__.py"       # Main python file of the game

[build]
vercode     = 0                       # Android version code. Must be incremented by 1 for every new Play Store upload!
platforms   = ["android"]             # Platforms supported by your app
includes    = [
    "assets/game_assets/**",    # Game assets
    "assets/ursina_assets/**",  # Ursina assets, don't remove this.
    "assets/assets.gen",        # Generated list of assets
    "**/*.png",
    "**/*.jpg",
    "**/*.egg",
]

[assets]
ursina_dir  = "assets/ursina_assets"  # Ursina assets dir
game_dir    = "assets/game_assets"    # Game assets dir
```

## `src` folder
This folder includes your application source code, with two main files:
- `__main__.py`: The main application entry point. (You can also just name this `main.py`).
- `setup_ursina_android.py`: This file sets up Ursina for Android. Modify it only if you know what you're doing.

!!! info "Script Placement"
    **All** of your game's Python scripts must always only be inside this `src` folder!

!!! warning "Modifying `__main__.py`"
    When modifying the main file, make sure to include these two lines at the very beginning to ensure Android compatibility: 
    ```python
    from setup_ursina_android import setup_ursina_android
    setup_ursina_android()
    ```