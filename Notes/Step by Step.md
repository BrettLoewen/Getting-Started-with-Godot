#Godot 

I'm going to be following the step by step beginners guide in Godot's docs to learn the basics of Godot.

[Step by step guide](https://docs.godotengine.org/en/stable/getting_started/step_by_step/index.html)

## Nodes and Scenes

The first section, Nodes and Scenes, explained the basics of the scene structure in Godot. *Everything is a node*. Nodes have scripts, either built-in ones or user written ones.

Nodes and their children can be saved and turned into scenes, which act similarly to prefabs from Unity. However, the game has a root scene as well. So prefabs and scenes are combined into the same thing.

I created a node for text, called a Label. I set its text value to "Hello World" and positioned it in the world. There is a purple outline indicating the canvas size. To setup font size, I had to add a label settings asset which has values for things like size, color, font, etc. This label settings asset can be saved as a file.

I ran the game and it opened a new window where the game was running. It took a few seconds to start up the first time, and it was very fast after that.

## Creating Instances

A scene is a file. An instance is an instance of that scene in the current game scene.

I downloaded the example ball project and moved the files into my getting started project. The guide wants me to just open the example project itself, but I want everything in one project, so I won't do that.

The guide goes through the process of opening an existing godot project. It's basically open godot, press the import button, and go through the steps. It looks pretty straightforward.

I opened the Main.tscn scene like the guide wanted me to. I had to move the Main.gd file out of the Ball Example folder I made to resolve a dependency warning and then moved it back into the folder and saved the scene to fix this. I switched to a different scene and then switched back and no error, yay.

The main scene is a lot of wall nodes with sprite2d and collisionshape2d nodes under them to make a 2d environment for the balls.

Sidenote: the walls are centered within the camera in the guide's screenshots but not in my project. I tried to adjust the camera, but realized there isn't a camera in the scene. I guess I'll need to learn how the camera works at some point.

To create an instance of the ball scene, I pressed the instantiate child scene button. Unity has an add object button which opens a context menu where you can select from a bunch of different options. But if you want to add a prefab into the scene, you need to drag it from the project window. Godot has an add child node button which works like unity's add gameobject button, so you can still create "gameobjects" in the same way, and you can drag scenes from the file system window into the active scene, so that option still exists. But the additional button to add "prefabs" to the active scene via a popup menu is a nice feature.

You can also duplicate nodes in the scene by pressing ctrl+d just like in Unity.

The guide is making me open the ball scene to edit it. While doing this, I learned the power of Godot's scene window: it has tabs! You can have multiple scenes open at the same in different tabs. No more reopening and closing scenes and prefabs just to check something or make a small change. In Godot, you could have everything open at the same time and switch between them by simply moving to a different tab. Amazing.

The guide had me change the bounce value of the ball's rigidbody2d's physics material. After changing it, running the game revealed that every ball's bounciness had changed to match the value.

The guide then had me change the gravity scale of one of the ball instances to show that the change would only affect that instance and not any of the other balls. I made the change, and indeed, the change only affected that one ball.

The guide also told me that making a change to a value of an instance causes a restart-looking icon to appear next to that value. This icon is the revert button. It shows that the value no longer matches the saved scene version and pressing the button causes the value to revert back to the value in the saved scene file.

The guide explained how games made in Godot should be made using a scene-based architecture. Unique elements of the game should be their own scene, and scenes should encapsulate other scenes as needed. The scenes should all come together, either through scene setup or spawning via code, to create the complete game.

Here's the summary of this section verbatim from the guide:
```
Instancing, the process of producing an object from a blueprint, has many handy uses. With scenes, it gives you:
- The ability to divide your game into reusable components.
- A tool to structure and encapsulate complex systems. 
- A language to think about your game project's structure in a natural way.
```

## Scripting Languages

"**Scripts attach to a node and extend its behavior**. This means that scripts inherit all functions and properties of the node they attach to."

There are 4 official scripting languages offered by Godot:
- GDScript
- C#
- C++
- C

There are other languages that are supported by the community, but these are the official ones.

You can use multiple different languages in the same project to fit your needs.

C# and C++ are apparently faster, but need to be compiled. Also, most functions call internal code written in C++, so the performance shouldn't vary much between languages.

If you use C#, you can't build to the web in Godot 4.2. If you want to build to the web and use C#, you have to use Godot 3.

## Creating your First Script

I created a new folder to hold this section. I made a new scene by pressing the + button in the scene tab list and then selecting other node for the root node and chose sprite2d. I also opened the project in file explorer to copy/paste the godot icon svg file into the folder for this section.

I set the icon as the texture for the root sprite.

I pressed the attach script button in the top bar of the scene window to open a menu to assign a new script to this node. I could also have right-clicked the node and selected attach script from the list of options. I selected the object: empty option for the scripts template and clicked create.

Now the script window is open, which replaces the scene view. I can still switch between scenes using the tabs and such, but instead of seeing the scene, I see the script editor. Using the buttons at the top of the editor, I can switch between the script view, the 2d view, the 3d view, and the asset library.

The empty object template has created a script with nothing in it except for one line that says this script extends the sprite 2d script.

In Godot, every script is a class. The extends keyword means it inherits from that class. This means that they get access to all of the properties and methods of their parent class(es) just like you'd expect. Essentially, when you attach a script to a node, you are replacing the node's type with your own class that inherits from that original class but with a little more functionality which you provide.

I wrote the following code according to the guide:
```
extends Sprite2D

func _init():
	print("Hello World!")
```

`_init` is a built-in function like Start and Update in Unity. It is called whenever the object/node is created in memory.

I ran the scene, and hello world was printed in the output window at the bottom of the editor.

Sidenote: I added a semi-colon at the end of the print line, and the code still ran without error. So if I accidentally add semi-colons by habit or copy code with them, it won't cause problems.

Sidenote: I tried editing the code and then closing the scene without saving anything. I reopened the scene, and the script was still edited. There isn't anything indicating unsaved work, unlike work in a scene which makes the scene show a (\*) in it's name. You can run the scene without saving changes in a script, and the changes affect the game. I'm not sure if I like this, but it's good to know about.

I created 2 variables, one for speed and one for angular speed.

Apparently angles in Godot work in radians, but there are methods to convert between radians and degrees.

Godot agrees with me that variables should go at the top of a script after "extend" lines but before functions.

I wrote some more code the guide told me to write. The code now looks like this:
```
extends Sprite2D

var speed = 400
var angular_speed = PI

func _process(delta):
	rotation += angular_speed * delta
```

`_process` is the same as Unity's Update method. It is called by the editor every frame. `delta` is like Unity's `Time.deltaTime`.

`rotation` is the sprite's rotation value. Modifying it will cause the sprite to spin.

Running the game now results in the godot icon sprite spinning in place around it's central pivot point.

Modifying the code some more, I now have this:
```
extends Sprite2D

var speed = 400
var angular_speed = PI

func _process(delta):
	rotation += angular_speed * delta
	
	var velocity = Vector2.UP.rotated(rotation) * speed
	position += velocity * delta
```

It causes the sprite to move around in a circle.

The guide provides a GDScript to C# comparison. The C# version definitely has a lot more boilerplate than the GDScript does. I think some of that boilerplate is useful, but it is nice to have simple code that looks like simple code.

## Listening to Player Input

Now it's time for input!

The guide had me reaplce the rotation calculation happening in `_process_` with the following block of code:
```
var direction = 0
if Input.is_action_pressed("ui_left"):
	direction = -1
if Input.is_action_pressed("ui_right"):
	direction = 1

rotation += angular_speed * direction * delta
```

Using the `Input` singleton and it's action pressed function, we get a true/false value for whether or not the left and right arrow keys are pressed down. If they are, we set a direction variable accordingly. We use the direction value to modify the rotation.

Playing the game now has the godot sprite moving forward constantly, but it can be steered using the the left and right arrow keys.

Godot provides several built-in input options and you can create more by opening the project settings and going to the input map tab.

The guide made a similar change to the velocity calculation so the sprite only moves forward while the player is holding down the up arrow. The new velocity code looks like this:
```
var velocity = Vector2.ZERO
if Input.is_action_pressed("ui_up"):
	velocity = Vector2.UP.rotated(rotation) * speed

position += velocity * delta
```

The guide mentions an alternate way to gather input using `_unhandled_input()` which is a built-in function, but the guide doesn't explain how to use it.

Here's the full script:
```
extends Sprite2D

var speed = 400
var angular_speed = PI

func _process(delta):
	var direction = 0
	if Input.is_action_pressed("ui_left"):
		direction = -1
	if Input.is_action_pressed("ui_right"):
		direction = 1
	
	rotation += angular_speed * direction * delta
	
	var velocity = Vector2.ZERO
	if Input.is_action_pressed("ui_up"):
		velocity = Vector2.UP.rotated(rotation) * speed
	
	position += velocity * delta
```

## Using Signals

Signals are like events in Unity.

The guide started by making me make a new 2d scene and dragging my script-learning scene into it. They also had me make a button as a sibling of my sprite. Currently the button is clickable when I run the game, but it doesn't do anything.

Using the Node tab of the inspector editor window shows all of the signals that node has. Double-clicking a signal opens a signal assign window where you can select a node from the scene. You can select the name of the function it will call (and create if needed) (the convention is `_on_node_name_signal_name`). There's also an advanced toggle that opens up more options for additional arguments that will be passed to the function. Clicking the connect button creates the callback within the script.

The guide had me do this for the button's `pressed` signal and my sprite 2d. It created the following function:
```
func _on_button_pressed():
	pass # Replace with function body.
```

There is an icon beside this function signature. Clicking it opens a window listing the signal connections to this function. Apparently it only shows signals that are setup in the editor, so presummably there are ways to connect signals via code and those wouldn't appear in this list. Regardless, this is a nice feature and something Unity would benefit from. You can also see a list of functions connected to the signal in the node editor window.

The guide had me replace the code in the signal method so now it looks like this:
```
func _on_button_pressed():
	set_process(not is_processing())
```

This code will prevent `_process` from running, preventing the sprite from moving.

I ran the game to test this. I could control the sprite before pressing the button. I pressed the button, and now I couldn't control the sprite. I pressed the button again, and now I could control the sprite.

Now it's time to learn about connecting signals via code.

The guide had me create a Timer node as a child of the sprite and set its autostart property to true.

The guide had me add the following code in order to get a reference to the timer:
```
func _ready():
	var timer = get_node("Timer")
```

`_ready()` is equivalent to Unity's Start method. `get_node` is similar to Unity's get component, however get node expects the name of the node as a string instead of the type ofthe component you want.

Next, the guide had me write a function to be called by the signal and the code to connect the signal to that function. The code for that looks like this:
```
func _ready():
	var timer = get_node("Timer")
	timer.timeout.connect(_on_timer_timeout)

func _on_timer_timeout():
	visible = not visible
```

The default value for the timer node is a timer of 1 second, so now the sprite toggles whether or not it is visible every second. `visible` is a variable from Node that controls whether or not the node is visible.

Here's some additional code the guide provides on how to create custom signals:
```
extends Node2D

signal health_depleted

var health = 10

func take_damage(amount):
	health -= amount
	if health <= 0:
		health_depleted.emit()
```

```
extends Node

signal health_changed(old_value, new_value)

var health = 10

func take_damage(amount):
	var old_health = health
	health -= amount
	health_changed.emit(old_health, health)
```

That's it for signals.

## End

That's it for the Step by Step guide.