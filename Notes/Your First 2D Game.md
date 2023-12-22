#Godot 

You can find the full guide [here](https://docs.godotengine.org/en/stable/getting_started/first_2d_game/index.html).

The idea for this guide is to create a 2D game using Godot where you control a character to avoid enemies for as long as possible.

## Setting up

I downloaded the assets the game wanted me to download and I added them to the project under a 'First 2D Game' folder.

The guide wants me to change the viewport ratio of the game to make it portrait mode. It sasys this can be done from the General > Display > Window section of the project settings. I'm going to try to avoid doing this, since I want to have the step by step guide, the 2d game, and the 3d game all in one project. So for now I'm ignoring this step.

It's asking me to go to the same settings section to make canvas items scale correctly across different sized screens. This can be down by setting the Stretch mode to canvas_items and the aspect to keep.

## Creating the Player

The player will be its own scene.

For the root node, the guide had me select 'Other Node' and then select Area2D from the list of nodes. Apparently this node will allow for object overlap detection.

There's a warning icon in the scene tab for this node, but the guide says to ignore it for now and that it will be addressed later.

The guide had me click a button on the toolbar after selecting the player node. The button was next to the lock icon and its tooltip says "Make selected node's children not selectable."

The guide had me add a new node as a child of the player node. This node is an AnimatedSprite2D node and will be how the appearance and animation of the player is handled. It also has a warning icon. The guide made me click on the sprite frames property for this node in the inspector and select the option "New SpriteFrames". I did, and now there's a sprite frame value for this property and the warning icon on this node is gone. Clicking the value opened the animation tab at the bottom of the editor.

In the animation tab, the guide had me rename the default animation to 'walk' and create a new animation called 'up'. It had me drag in the sprites from the assets it provided for both animations. It had me adjust the scale value for this node in the inspector. Now the player has an appearance.

Next, the guide had me add a CollisionShape2D node as a child of the player node. It had me select the shape property and select "New CapsuleShape2D" which added a collider. It had me use the handles in the scene view to scale the capsule collider to fit the player. Adding this node made the warning icon on the player node go away.

Now we have the player setup and ready to be coded.

## Coding the Player

The guide had me attach a new script to the player node. In this new script, it had me create speed and screen size variables. The speed variable has the keyword `@export` in front which makes it appear in the inspector. The inspector updated to show this variable (not immediately, but pretty quickly). The screen size variable is set in `_ready` by calling `get_viewport_width().size`.

Now to setup input. The guide had me open up the input map tab of the project settings. It had me type in 'move_right' in the 'Add New Action' field and click the 'Add' button. This created a new input action in the list below. Clicking the plus button for this action opened a popup where I could select an input binding. I could put my cursor in an input field and press a key, and the editor listened for that input and highligted that option from the list of bindings. I selected the one for the right arrow key and now I had a new input action bound to the right arrow key. The guide had me repeat these steps for the up, down, and left arrow keys as well.

The next step was to use these inputs in `_process` to set a velocity vector variable. If the variable's length was greater than 0, then the player is inputing something, so normalize the velocity and multiply it by the speed variable. Depending on whether or not the player should be moving, tell the animated sprite to play or to stop.

Here is the code so far:
```
extends Area2D

@export var speed = 400 # How fast the player will move (pixels/sec).
var screen_size # Size of the game window.

# Called when the node enters the scene tree for the first time.
func _ready():
	screen_size = get_viewport_rect().size


# Called every frame. 'delta' is the elapsed time since the previous frame.
func _process(delta):
	var velocity = Vector2.ZERO
	
	if Input.is_action_pressed("move_right"):
		velocity.x += 1
	if Input.is_action_pressed("move_left"):
		velocity.x -= 1
	if Input.is_action_pressed("move_down"):
		velocity.y += 1
	if Input.is_action_pressed("move_up"):
		velocity.y -= 1
	
	if velocity.length() > 0:
		velocity = velocity.normalized() * speed
		$AnimatedSprite2D.play()
	else:
		$AnimatedSprite2D.stop()
```

The `$` is a shortcut for `get_node` and grabs the nodes that are children of this node. Godot's IDE automatically detected the animated sprite and collision shape as options when I typed the `$`.

A weird thing about Godot is that the up direction is negative, so the y value for up is -1 and 1 for down.

It had me add this code to move the player according to the velocity:
```
position += velocity * delta
position = position.clamp(Vector2.ZERO, screen_size)
```

I ran the scene and was able to move the player around with the arrow keys. While the player moves, it plays the walk animation and when the player stops moving, it doesn't play any animation. More specifically, it seems to sit at the first frame of the walk animation.

Next, the guide had me add some code to the end of `_process` to control the animations of the player. Here is the code that does that:
```
if velocity.x != 0:
	$AnimatedSprite2D.animation = "walk"
	$AnimatedSprite2D.flip_v = false
	$AnimatedSprite2D.flip_h = velocity.x < 0
elif velocity.y != 0:
	$AnimatedSprite2D.animation = "up"
	$AnimatedSprite2D.flip_v = velocity.y > 0
```

The flip variables are used to invert the animations so it looks like there are animations for all 4 directions when in reality there are only 2 animations.

Running the game now shows that the player's animations play correctly depending on the movement of the player.

Then the game had me add the line `hide()` to `_ready` to make the player invisible at the start of the game. Now when you run the game, you cannot see the player.

Next up is detecting collisions. The guide had me add this line `signal hit` at the top of the script right below the extends line to create a new signal. Going to the node tab shows this new signal under the section for the player script.

The guide had me click on the body_entered signal defined by the area 2D node to connect it to the player script and create a new function:
```
func _on_body_entered(body):
	pass # Replace with function body.
```

It had me fill in this method with code to hide the player, emit the hit signal, and turn off the collider. The collider needs to be turned off using a deferral method that it has since apparently in Godot modifying a physics component during physics processing can cause an error.

Then the guide had me create a `start` method so that the player could be reset.

The code looks like this now:
```
extends Area2D

signal hit

@export var speed = 400 # How fast the player will move (pixels/sec).
var screen_size # Size of the game window.

# Called when the node enters the scene tree for the first time.
func _ready():
	screen_size = get_viewport_rect().size
	hide()

# Called every frame. 'delta' is the elapsed time since the previous frame.
func _process(delta):
	var velocity = Vector2.ZERO
	
	if Input.is_action_pressed("move_right"):
		velocity.x += 1
	if Input.is_action_pressed("move_left"):
		velocity.x -= 1
	if Input.is_action_pressed("move_down"):
		velocity.y += 1
	if Input.is_action_pressed("move_up"):
		velocity.y -= 1
	
	if velocity.length() > 0:
		velocity = velocity.normalized() * speed
		$AnimatedSprite2D.play()
	else:
		$AnimatedSprite2D.stop()
	
	if velocity.x != 0:
		$AnimatedSprite2D.animation = "walk"
		$AnimatedSprite2D.flip_v = false
		$AnimatedSprite2D.flip_h = velocity.x < 0
	elif velocity.y != 0:
		$AnimatedSprite2D.animation = "up"
		$AnimatedSprite2D.flip_v = velocity.y > 0
	
	position += velocity * delta
	position = position.clamp(Vector2.ZERO, screen_size)


func _on_body_entered(body):
	hide() # Player disappears after being hit
	hit.emit()
	
	# Must be deferred as we can't change physical properties on a physics callback
	$CollisionShape2D.set_deferred("disabled", true)

func start(pos):
	position = pos
	show()
	$CollisionShape2D.disabled = false
```

## Creating the Enemy

Now it's time to create enemies for the game. They will be simple enemies that spawn and move across the screen in a random direction.

Time to create a new scene. This one will have a rigidbody 2d node for its root node and this node and scene will be named Mob.

I had to create child nodes for the mob consisting of an animated sprite 2d node, a collision shape 2d node, and a visible on screen notifier 2d node. I set the rigidbody's gravity scale property to 0 since we don't these enemies to be affected by gravity in any way. I also unchecked the 1 box inside the mask section of the collision section of the collision object 2d section of the rigidbody node's inspector since this will ensure that the mobs do not colllide with each other.

Next the guide had me setup the animations for the enemy. 3 animations: fly, walk, and swim. Each 2 frames with the fps set to 3. It also had me set the scale of the node's node 2d to 0.75.

Finally, I setup the collision shape. A capsule just like the player's, but with the transform of this node 2d set to 90 so that it aligns with the horizontal nature of the sprites.

Now it's time to code the enemies. I attached a script to the root node and added this code in `_ready`:
```
# Called when the node enters the scene tree for the first time.
func _ready():
	var mob_types = $AnimatedSprite2D.sprite_frames.get_animation_names()
	$AnimatedSprite2D.play(mob_types[randi() % mob_types.size()])
```

This code gets the list of animation names from the animation node and play a random one.

Next it had me connect the screen exited signal of the screen notifier node to this script and fill in the function like so:
```
func _on_visible_on_screen_notifier_2d_screen_exited():
	queue_free()
```

This completes the mob scene. The final script looks like this:
```
extends RigidBody2D

# Called when the node enters the scene tree for the first time.
func _ready():
	var mob_types = $AnimatedSprite2D.sprite_frames.get_animation_names()
	$AnimatedSprite2D.play(mob_types[randi() % mob_types.size()])


# Called every frame. 'delta' is the elapsed time since the previous frame.
func _process(delta):
	pass


func _on_visible_on_screen_notifier_2d_screen_exited():
	queue_free()
```

## Main Game Scene

First things first, new scene with a generic root node of type Node. Then spawn an instance of the player node as a child.

Next was adding 3 timers (mob, score, and start) and a Marker2D node called start position.

The timers got times of .5, 1, and 2 respectively, the start timer got its one shot property set to true, and the position marker was moved to the center of the screen.

Next up is adding a Path2D node as a child of the main node and creating a path around the edge of the screen using the add point button on the top bar to enter add mode and then clicking on the corners of the screen in a clockwise order.

Now it's time to make the script. I attached a script to the main node and setup a variable for the score and one for the enemy scene.

Apparently this code forces a type of variable so the inspector knows what it will be.
```
@export var mob_scene: PackedScene
```

Next up was connecting some signals to the main script. First has the player's hit signal made earlier so that it calls the game over function. We made a new game function to restart the game. We also hooked up all the timers so they call methods on the main script. Here's the code:
```
extends Node

@export var mob_scene: PackedScene
var score

func game_over():
	$ScoreTimer.stop()
	$MobTimer.stop()
	
func new_game():
	score = 0
	$Player.start($StartPosition.position)
	$StartTimer.start()

func _on_mob_timer_timeout():
	pass # Replace with function body.


func _on_score_timer_timeout():
	score += 1


func _on_start_timer_timeout():
	$MobTimer.start()
	$ScoreTimer.start()
```

Next is setting up the mob timer function so it will spawn mobs. Here's the code for that.
```
func _on_mob_timer_timeout():
	# Create a new instance of the Mob scene.
	var mob = mob_scene.instantiate()

	# Choose a random location on Path2D.
	var mob_spawn_location = get_node("MobPath/MobSpawnLocation")
	mob_spawn_location.progress_ratio = randf()

	# Set the mob's direction perpendicular to the path direction.
	var direction = mob_spawn_location.rotation + PI / 2

	# Set the mob's position to a random location.
	mob.position = mob_spawn_location.position

	# Add some randomness to the direction.
	direction += randf_range(-PI / 4, PI / 4)
	mob.rotation = direction

	# Choose the velocity for the mob.
	var velocity = Vector2(randf_range(150.0, 250.0), 0.0)
	mob.linear_velocity = velocity.rotated(direction)

	# Spawn the mob by adding it to the Main scene.
	add_child(mob)
```

After adding code to call the new game function in `_ready`, the game could now be tested. It seems to be working. There's currently no way to restart if you die and no way to see your score, but it does work.

In the next section UI will be added. For now, the guide says to remove the new game function call. I guess it will be called from UI later.

## Heads Up Display

Time to create the game's UI.

First the guide had me make a new hud scene with the canvas layer root node. Next was creating a score label, a message label, a start button, and a message timer.

It had me change the font and font size for the labels and button by going into their control node and adjusting some theme overrides.

It had me position them and setup their texts. It also had me give the timer a length of 2 and make it one shot.

Now comes the script. I mostly just copy/pasted my way through it. There was a method to show text on the message label. There was a message to show a game over message and pause the game before bringing back the start button so the player can restart. There was an update score method. There were 2 signal methods, one for the start button and one for the message timer. The start button one starts the game and the timer method hides the message after the timer expires.

Here's the code for the HUD:
```
extends CanvasLayer

# Notifies `Main` node that the button has been pressed
signal start_game

func show_message(text):
	$Message.text = text
	$Message.show()
	$MessageTimer.start()

func show_game_over():
	show_message("Game Over")
	# Wait until the MessageTimer has counted down.
	await $MessageTimer.timeout

	$Message.text = "Dodge the Creeps!"
	$Message.show()
	# Make a one-shot timer and wait for it to finish.
	await get_tree().create_timer(1.0).timeout
	$StartButton.show()

func update_score(score):
	$ScoreLabel.text = str(score)

func _on_start_button_pressed():
	$StartButton.hide()
	start_game.emit()

func _on_message_timer_timeout():
	$Message.hide()
```

Now this scene is done and can be added to the main scene. I did that.

It had me hook up the start button signal to the new game method and had me add some code in the main script methods to control the UI.

The game can now be played.

There is an issue though. If the player starts a new game as quickly as possible, old enemies will still be on screen. We should fix this.

I went into the mob scene, and opened the group section of the node window. There I defined a new group called mobs on the root node of that scene. Back in the main script, I added a line that deletes every instance of the mobs groups when a new game starts.

This is what the main script looks like now:
```
extends Node

@export var mob_scene: PackedScene
var score

func game_over():
	$ScoreTimer.stop()
	$MobTimer.stop()
	$HUD.show_game_over()

func new_game():
	score = 0
	$Player.start($StartPosition.position)
	$StartTimer.start()
	$HUD.update_score(score)
	$HUD.show_message("Get Ready")
	get_tree().call_group("mobs", "queue_free")

func _on_score_timer_timeout():
	score += 1
	$HUD.update_score(score)

func _on_start_timer_timeout():
	$MobTimer.start()
	$ScoreTimer.start()

func _on_mob_timer_timeout():
	# Create a new instance of the Mob scene.
	var mob = mob_scene.instantiate()

	# Choose a random location on Path2D.
	var mob_spawn_location = get_node("MobPath/MobSpawnLocation")
	mob_spawn_location.progress_ratio = randf()

	# Set the mob's direction perpendicular to the path direction.
	var direction = mob_spawn_location.rotation + PI / 2

	# Set the mob's position to a random location.
	mob.position = mob_spawn_location.position

	# Add some randomness to the direction.
	direction += randf_range(-PI / 4, PI / 4)
	mob.rotation = direction

	# Choose the velocity for the mob.
	var velocity = Vector2(randf_range(150.0, 250.0), 0.0)
	mob.linear_velocity = velocity.rotated(direction)

	# Spawn the mob by adding it to the Main scene.
	add_child(mob)
```

## Finishing Up

Time for some finishing touches.

I added a background color using a color rect node.

Next I added some audio stream player nodes to play a death sound and music. To make the music loop I had to select the option in the dropdown to make the sound unique and then I could open it and set its loop property to true. I played these from the main script.

Then the guide told me to create a keyboard shortcut for the start button. I created a new keybind for the enter key named start_game. Then in the start button in the hud scene, I went to the shortcut section of the base button node and made a new shortcut resource. In it, I added a new element to the array, created a new input event action, and entered the name of my input in its action property.

And that's the end of the 2D game.