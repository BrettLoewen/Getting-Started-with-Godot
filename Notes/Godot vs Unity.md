#Godot 

As I learn Godot, I want to write down any differences between it and Unity.

Something I noticed is that the sizing of things is different. The default editor layout generally has the different window tabs of the editor smaller than I would expect and the text within those windows is generally larger. This leads to everything feeling rather cramped in my opinion.

There's the obvious difference of nodes vs gameobjects, but I don't know enough about nodes to go into detail about this yet.

UI works similarly but a little bit differently.
- Anchors still exist and work the same, at least so far
- Text looks good, but seems lackluster compared to text mesh pro
	- In order to control things like font size, fonts, and color, you need to create a label settings asset. This doesn't need to be saved as a file, but it can be. This settings asset holds that text data. This feels weird, but I understand the benefits it could bring.
	- I don't see an option for auto-sizing text
	- The text node is called a Label

There are multiple run buttons.
- There is a Run Game button which runs the game from the starting scene
	- The first time you do this, you must select the starting scene. Later you can change this from the project settings.
- There is a Run Current Scene button which runs the game in the currently open scene
- There is a Run Specific Scene button which opens a menu to select the scene to run

When you run the game, instead of there being a game in-editor window tab, a new detached window opens that is dedicated to running the game.

There's also a movie maker button that makes the game run at a stable framerate and records audio/visual data to a video file. Not sure how it works yet, but it sounds like a great addition.

Instead of the project window and the assets folder, you have the file system window and the res:// folder which is an in-editor abstraction of the project's root folder.

When you create a project, there is an initialize with git option. However, I think all it does is add .gitignore and .gitattributes files, which don't have a ton in them. The only thing ignored is the .godot folder and the attributes file seems to just standardize text file EOL characters. If you want to actually make this a git repo, run `git init` from a terminal.

When you create a project, it asks you to pick a renderer from 3 options. It seems that these determine what build options you have and what graphic quality it will default to. You can change this later from a dropdown in the main editor view.

In Unity, you can hold the alt key while left clicking the expand/collapse arrow for things in the project and hierarchy windows. In Godot, this is done by holding the shift key instead of the alt key.

The file system (project) window tab has a favourites "folder" at the top. You can right click on any asset and select Add to Favourites to make it appear under the favourites folder as well as in its file location. This is a cool idea for having quick access to files that you work with often. You can mark folders as favourites, but they can;t be opened and just exist so you can click on them to bring you to the actual folder. It seems that you can't create folders within favourites, which limits how helpful it can be in my opinion. Actually, I looked at Unity's project window more, and it does have a favourites section, it just only appears in the 2 column layout which I don't use which is why I didn't know it existed.

It seems that deselecting things doesn't cause the inspector to stop displaying them. This could be a good thing, but it seems there isn't any way to make the inspector stop displaying them, which I don't love.

Godot's scene window has an advantage over Unity's: it has tabs! You can have multiple scenes open at the same in different tabs. No more reopening and closing scenes and prefabs just to check something or make a small change. In Godot, you could have everything open at the same time and switch between them by simply moving to a different tab. Amazing.

Making a change to a value of a scene instance causes a restart-looking icon to appear next to that value. This icon is the revert button. It shows that the value no longer matches the saved scene version and pressing the button causes the value to revert back to the value in the saved scene file.

Unity has scenes and prefabs. Godot combines these things into 1 thing. Godot's scenes have all the features of Unity's scenes and the features of Unity's prefabs.

Each node can only have 1 script in Godot whereas a gameobject in Unity can have as many components as you want. Granted, that 1 script can inherit from any number of parent classes, but it's still one script. I believe this is meant to force the node-based architecture that Godot encourages. It also encourages composition through child nodes. However, multiple different scripts on 1 gameobject is also composition, and 1 script per node could lead to denser scripts and less composition. Ultimately, it's still in the hands of the developer, but I do prefer the option to have multiple components per gameobject.

Since every script inherits from all other node scripts that it sits on top of, it also means that every script has access to their variables, like `position` and `rotation`. So essentially, every script is a transform, so no more `transform.rotation` or anything. Also, rotation can be directly assigned to, which either can't be done in Unity, or is weird to do, I don't remember the details.

Input is done using the `Input` singleton much like Unity's old input system. However, you define those inputs in the input map tab of the project settings which looks to be a similar process to creating input actions in Unity's new input system. There's also the option to use `_unhandled_input()` for input, but I haven't learned how to do that yet.

Godot's equivalent to Unity's events are signals. They are fairly similar, but signals have a lot less boilerplate and some nice additional features like a button in the IDE that brings up a menu of all the signals that connect to the given function.

Timers are something that have been a bit awkward in Unity. Not that the code itself is awkward, but it felt like there should be a better way than to constantly make variables just to decrease them by delta time and whatever else you need. Godot answers this with its Timer node, a dedicated node for timers. I'm not sure how helpful it will be, but it seems like it could make this kind of thing simpler.