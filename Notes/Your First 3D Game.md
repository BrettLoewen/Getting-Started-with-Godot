#Godot 

You can find the full guide [here](https://docs.godotengine.org/en/stable/getting_started/first_3d_game/index.html).

This game is similar to the 3d game but modified to take advantage of 3D. Enemies will still be spawned around the outside of the map and move from one side to the other randomly, but now the player increases their score by jumping on mobs to kill them instead of just surviving.
## Setting up the game area

I imported the assets for the project and placed them in a new folder for this game (within the same project as the other tutorials).

The guide had me create a new scene with a default node for the root. It had me save this as the main scene.

It had me add a static body 3d to create the ground. I needed to add to child nodes to it: a collision shape 3d and a mesh instance 3d. I created new shape and mesh resources for them and used those to control the size of the ground. The collision shape will handle collision and the mesh instance handles visuals.

Next it had me add a directional light 3d node the scene and set it up to light up the ground.

That's it for this section.

## Player scene and input actions

I created a new 3d scene whose root node was the character body 3d node (it seems like this might be an equivalent to Unity's character controller component). It had me add a node 3d named pivot as a child and add the player model scene provided by the assets as a child of the pivot. It also had me create a collision shape 3d which uses a sphere shape to be the player's collider.

Next the guide had me create input actions. Since I'm using the same project as the 2d game, I'm just going to re-use it's actions for moving. I created a new action called jump which uses the space bar. The guide includes directions for setting up joystick controls, but I don't feel like doing that, so I won't.

## Moving the player with code

The guide had me create variables to define the player's movement.

It had me use the `_physics_process` built in function to do the calculations. This appears to work the same way as Unity's fixed update method.

The calculations included getting the player's input, normalizing it, rotating the player pivot child to face the direction of movement, calculating the target velocity from the input, checking if the player is on the floor or not (using a function of the character body node), and finally moving the player (using a function of the character body node).

Here's the code for it:
```
extends CharacterBody3D

# How fast the player moves in meters per second.
@export var speed = 14
# The downward acceleration when in the air, in meters per second squared.
@export var fall_acceleration = 75

var target_velocity = Vector3.ZERO


func _physics_process(delta):
	# We create a local variable to store the input direction.
	var direction = Vector3.ZERO

	# We check for each move input and update the direction accordingly.
	if Input.is_action_pressed("move_right"):
		direction.x += 1
	if Input.is_action_pressed("move_left"):
		direction.x -= 1
	if Input.is_action_pressed("move_down"):
		# Notice how we are working with the vector's x and z axes.
		# In 3D, the XZ plane is the ground plane.
		direction.z += 1
	if Input.is_action_pressed("move_up"):
		direction.z -= 1
	
	if direction != Vector3.ZERO:
		direction = direction.normalized()
		$Pivot.look_at(position + direction, Vector3.UP)
	
	# Ground Velocity
	target_velocity.x = direction.x * speed
	target_velocity.z = direction.z * speed

	# Vertical Velocity
	if not is_on_floor(): # If in the air, fall towards the floor. Literally gravity
		target_velocity.y = target_velocity.y - (fall_acceleration * delta)

	# Moving the Character
	velocity = target_velocity
	move_and_slide()
```

Next was adding it to the main scene so we could test the movement.

2D scenes apparently have a default camera or something that allows them to work without a camera but 3D scenes require a camera. The guide had me create a marker 3d node for the camera pivot and created a camera 3d node as a child of it.

So, I originally thought that Godot didn't have any kind of game window like Unity. At least, that's what I had been told. However, that made me think there might not be any way to see what the camera sees except by running the game. This, thankfully, turns out to be false. The guide had me toggle on the camera preview (button that appeared in the top left corner of the scene view) and now I could see what the camera saw. Furthermore, the guide had me select the view option on the scene's top bar which allowed me to split the scene view into 2 sections. I could make 1 preview the camera and one the normal edit mode camera. This is something I wasn't sure Godot had, but it's awesome to learn that it does have it.

It had me move the camera out on the z axis and then rotate the camera pivot node to get the camera looking down on the scene.

I ran the game, and I could move the player around on the ground.

The guide had me switch to an orthogonal camera and made me adjust the size value for it so that a good amount of the game could actually be seen. And that's it for this section.

## Designing the mob scene

The guide had me create a new mob scene for the enemy which is setup almost identically to the player scene. It uses a box shape collider instead of a sphere and has a visibility notifier node to send a signal when the mob leaves the camera's view.

The guide had a sidenote explaining that object pooling can be useful in languages like C# where garbage collecting memory freezes can happen but that because GDScript doesn't use that kind of memory management system object pooling wouldn't be useful. So I think I should just never bother with object pooling in Godot. Good to know.

Next it had me create the mob script. It includes code to pick a random position based on where the player is and then move in that direction. It also has a function connected to the visible notifier node's screen exited signal so the mob will destroy itself when after exiting the camera's view. Here's the code for this:
```
extends CharacterBody3D

# Minimum speed of the mob in meters per second.
@export var min_speed = 10
# Maximum speed of the mob in meters per second.
@export var max_speed = 18

func _physics_process(_delta):
	move_and_slide()

# This function will be called from the Main scene.
func initialize(start_position, player_position):
	# We position the mob by placing it at start_position
	# and rotate it towards player_position, so it looks at the player.
	look_at_from_position(start_position, player_position, Vector3.UP)
	# Rotate this mob randomly within range of -90 and +90 degrees,
	# so that it doesn't move directly towards the player.
	rotate_y(randf_range(-PI / 4, PI / 4))

	# We calculate a random speed (integer)
	var random_speed = randi_range(min_speed, max_speed)
	# We calculate a forward velocity that represents the speed.
	velocity = Vector3.FORWARD * random_speed
	# We then rotate the velocity vector based on the mob's Y rotation
	# in order to move in the direction the mob is looking.
	velocity = velocity.rotated(Vector3.UP, rotation.y)

func _on_visible_on_screen_notifier_3d_screen_exited():
	queue_free()
```

That's it for the mob scene.

## Spawning monsters

Now back to the main scene.

The guide mentions changing the camera's viewport, but I can't really do that since I have multiple games in this project. So I'll see if I can get away with not changing anything.

The guide had me create placeholder cylinder meshes so I could tell where the corners of the camera's view are in 3D space. Then I created a path 3d node that goes from cylinder to cylinder around the edge of the camera's view. I added a path follow node as a child of the path so it can be used to sample on the path. I hid the cylinders using the eye icon in the scene tab and it worked both in the editor and for what the camera sees.

Now it's time for code. The guide had me attach a script to the root node of the main scene and add a variable to referene the scene file for the mob.

It had me create a timer node that autostarts and connect it's timeout signal to the main script. In that function it had me add code to spawn and initialize a mob. The game can now be played and mobs will spawn, but they collider with each other and the player and stop moving. That will be fixed in the next section.

Here's the main scene code so far:
```
extends Node

@export var mob_scene: PackedScene


func _on_mob_timer_timeout():
	# Create a new instance of the Mob scene.
	var mob = mob_scene.instantiate()

	# Choose a random location on the SpawnPath.
	# We store the reference to the SpawnLocation node.
	var mob_spawn_location = get_node("SpawnPath/SpawnLocation")
	# And give it a random offset.
	mob_spawn_location.progress_ratio = randf()

	var player_position = $Player.position
	mob.initialize(mob_spawn_location.position, player_position)

	# Spawn the mob by adding it to the Main scene.
	add_child(mob)
```

## Jumping and squashing monsters

We're going to use physics layers to fix the collision issues that currently exist in the game.

In Godot, collision is controlled by layers and masks. Layers are assigned to collision nodes and say what this node is (a node can have multiple layers). Masks say what this node can collide with. If a node has no masks, then it won't collide with anything regardless of what layer it is.

It had me go into the project setings to the general -> layer names -> 3d physics section and name the first 3 layers player, enemies, and world. It then had me go through the scenes and update some collision settings. I made the main scene's ground use the world layer and have no masks. I made the player use the player layer and have masks for world and enemies. I made the mob use the enemies layer and have no masks.

So now the player interacts with the ground and enemies, and nothing else interacts with anything.

The guide had me add some additional code to the player script so it can jump. It had me add a new variable and some code near the end of the physics process function. Here's the code:
```
# Vertical impulse applied to the character upon jumping in meters per second.
@export var jump_impulse = 20

# Jumping
if is_on_floor() and Input.is_action_just_pressed("jump"):
	target_velocity.y = jump_impulse
```

Now it's time for killing mobs.

The guide had me go to the mob scene and make it part of a mob group.

After that it had me add some additional code to the player script to handle collision detection and bouncing on enemies:
```
# Vertical impulse applied to the character upon bouncing over a mob in
# meters per second.
@export var bounce_impulse = 16

func _physics_process(delta):
	#...

	# Iterate through all collisions that occurred this frame
	for index in range(get_slide_collision_count()):
		# We get one of the collisions with the player
		var collision = get_slide_collision(index)

		# If the collision is with ground
		if collision.get_collider() == null:
			continue

		# If the collider is with a mob
		if collision.get_collider().is_in_group("mob"):
			var mob = collision.get_collider()
			# we check that we are hitting it from above.
			if Vector3.UP.dot(collision.get_normal()) > 0.1:
				# If so, we squash it and bounce.
				mob.squash()
				target_velocity.y = bounce_impulse
				# Prevent further duplicate calls.
				break
```

It then had me add some additional code to the mob script:
```
# Emitted when the player jumped on the mob.
signal squashed

# ...

func squash():
	squashed.emit()
	queue_free()
```

Now when the player lands on an enemy, the player bounces, that enemy dies, and the enemy sends a signal which will be used later to increase the score.

I tested the game, and enemies were dying from just running into me instead of only when I jump on them. I tweaked the colliders of the players and enemies and now enemies push the player around when they run into the player. Player death will be added in the next section.

## Killing the player

The guide had me add an area node to the player scene and give it a cylinder collision shape. This collider was setup to be short and a bit above the floor so it wouldn't be able to touch a mob when the player was jumping on them. The area was also setup to have its monitorable property disabled, no layer, and only a mask for the enemies layer.

Some additional code was added to the player script to receive the area's body entered signal and kill the player.
```
# Emitted when the player was hit by a mob.
# Put this at the top of the script.
signal hit

# And this function at the bottom.
func die():
	hit.emit()
	queue_free()

func _on_mob_detector_body_entered(body):
	die()
```

Playing the game now shows that the player can die, but it causes an error since the player is referenced in the main scene's script since the game doesn't stop playing when the player dies.

To fix this issue, the guide had me connect the player's hit signal to the main script in the main scene to the following code which stops the timer.
```
func _on_player_hit():
	$MobTimer.stop()
```

Now the game doesn't break if the player dies. With that, the game could be considered playable. There's still more work to do, but this is a good milestone.

## Score and replay

Now it's time to add a score display.

The guide had me create a control node child of the main node and a label node under it. It had me position the label, set its color to black, and set the theme of the control node to control the font and font size.

Next it had me add a script to the label to control it's display. This also required adding a line to the mob spawning code to connect its death signal to the UI.
```
// Mob.gd
# We connect the mob to the score label to update the score upon squashing one.
mob.squashed.connect($UserInterface/ScoreLabel._on_mob_squashed.bind())

// ScoreLabel.gd
extends Label

var score = 0

func _on_mob_squashed():
	score += 1
	text = "Score: %s" % score
```

Now the score increases and the player can see their score.

Now for replaying the game. The guide had me add a color rect node and another label node for a death screen. The text says "Press Enter to Retry". I added code to the main script to hide this UI at the start of the game and reveal it when the player dies. I added some code to restart the game if the player presses enter at this point.

Here's the new code:
```
func _ready():
	$UserInterface/Retry.hide()

func _on_player_hit():
	#...
	$UserInterface/Retry.show()

func _unhandled_input(event):
	if event.is_action_pressed("ui_accept") and $UserInterface/Retry.visible:
		# This restarts the current scene.
		get_tree().reload_current_scene()
```

I played the game, and it all seems to be working as expected.

Finally the guide wanted to add music to the game. This was done by creating a new music player scene and making the root node an audio stream player node. The music was set and the autoplay property was set to true.

However, this scene should be handled differently than the others so that the music doesn't restart when the scene is reloaded when the player retries the game. This is done by making the scene autoload. In project settings there is an autoload tab where scenes can be added to load when the game starts. These scenes will be siblings with the current scene (the main scene in our case). I did this and player the game and it is working as expected.

Sidenote: since this project contains multiple games, I'll need to remember to disable this autoload if I get around to making builds for the other games and might need to mention it in the readme as well.

## Character animation

The guide had me create an animation for the player's character node called float. This animation just changes the position and rotation a bit to give the player some wave motion. The guide also had me adjust the interpolation easing curve by selecting the keyframes and editing the curve in the inspector.

Now that the player animates, the guide says it's time to control the animation through code. The animation player was setup earlier to autoplay and loop, but we can control the speed of the animation based on the player's movement speed.

It had me add the following code to the player script to change the speed of the animation and to rotate the pivot node while the player is jumping.
```
func _physics_process(delta):
	#...
	if direction != Vector3.ZERO:
		#...
		$AnimationPlayer.speed_scale = 4
	else:
		$AnimationPlayer.speed_scale = 1
	
	$Pivot.rotation.x = PI / 6 * velocity.y / jump_impulse
```

Now it's time to animate the mobs.

I pressed the edit button on the player's animation and copied the tracks. I then unpinned the animation tab. I went to the mob scene, added an animation player node, made a new animation, and pasted the tracks. I made sure that autoplay was enabled, looping was enabled, and that the duration was correct.

I added the following code to the mob script's initialization function to give the animation a random speed:
```
$AnimationPlayer.speed_scale = random_speed / min_speed
```

## Conclusion

And with that, the game is finished.

Here's the full code for the game:

`Main`
```
extends Node

@export var mob_scene: PackedScene


func _ready():
	$UserInterface/Retry.hide()


func _unhandled_input(event):
	if event.is_action_pressed("ui_accept") and $UserInterface/Retry.visible:
		# This restarts the current scene.
		get_tree().reload_current_scene()


func _on_mob_timer_timeout():
	# Create a new instance of the Mob scene.
	var mob = mob_scene.instantiate()

	# Choose a random location on the SpawnPath.
	# We store the reference to the SpawnLocation node.
	var mob_spawn_location = get_node("SpawnPath/SpawnLocation")
	# And give it a random offset.
	mob_spawn_location.progress_ratio = randf()

	var player_position = $Player.position
	mob.initialize(mob_spawn_location.position, player_position)

	# Spawn the mob by adding it to the Main scene.
	add_child(mob)
	
	# We connect the mob to the score label to update the score upon squashing one.
	mob.squashed.connect($UserInterface/ScoreLabel._on_mob_squashed.bind())


func _on_player_hit():
	$MobTimer.stop()
	$UserInterface/Retry.show()
```

`Player`
```
extends CharacterBody3D

# Emitted when the player was hit by a mob.
# Put this at the top of the script.
signal hit

# How fast the player moves in meters per second.
@export var speed = 14
# The downward acceleration when in the air, in meters per second squared.
@export var fall_acceleration = 75
# Vertical impulse applied to the character upon jumping in meters per second.
@export var jump_impulse = 20
# Vertical impulse applied to the character upon bouncing over a mob in
# meters per second.
@export var bounce_impulse = 16

var target_velocity = Vector3.ZERO


func _physics_process(delta):
	# We create a local variable to store the input direction.
	var direction = Vector3.ZERO
	
	# We check for each move input and update the direction accordingly.
	if Input.is_action_pressed("move_right"):
		direction.x += 1
	if Input.is_action_pressed("move_left"):
		direction.x -= 1
	if Input.is_action_pressed("move_down"):
		# Notice how we are working with the vector's x and z axes.
		# In 3D, the XZ plane is the ground plane.
		direction.z += 1
	if Input.is_action_pressed("move_up"):
		direction.z -= 1
	
	if direction != Vector3.ZERO:
		direction = direction.normalized()
		$Pivot.look_at(position + direction, Vector3.UP)
		$AnimationPlayer.speed_scale = 4
	else:
		$AnimationPlayer.speed_scale = 1
	
	# Ground Velocity
	target_velocity.x = direction.x * speed
	target_velocity.z = direction.z * speed
	
	# Vertical Velocity
	if not is_on_floor(): # If in the air, fall towards the floor. Literally gravity
		target_velocity.y = target_velocity.y - (fall_acceleration * delta)
	
	# Jumping
	if is_on_floor() and Input.is_action_just_pressed("jump"):
		target_velocity.y = jump_impulse
	
	$Pivot.rotation.x = PI / 6 * velocity.y / jump_impulse
	
	# Iterate through all collisions that occurred this frame
	for index in range(get_slide_collision_count()):
		# We get one of the collisions with the player
		var collision = get_slide_collision(index)
		
		# If the collision is with ground
		if collision.get_collider() == null:
			continue
		
		# If the collider is with a mob
		if collision.get_collider().is_in_group("mob"):
			var mob = collision.get_collider()
			# we check that we are hitting it from above.
			if Vector3.UP.dot(collision.get_normal()) > 0.1:
				# If so, we squash it and bounce.
				mob.squash()
				target_velocity.y = bounce_impulse
				# Prevent further duplicate calls.
				break
	
	# Moving the Character
	velocity = target_velocity
	move_and_slide()


# And this function at the bottom.
func die():
	hit.emit()
	queue_free()


func _on_mob_detector_body_entered(body):
	die()
```

`Mob`
```
extends CharacterBody3D

# Emitted when the player jumped on the mob.
signal squashed

# Minimum speed of the mob in meters per second.
@export var min_speed = 10
# Maximum speed of the mob in meters per second.
@export var max_speed = 18


func _physics_process(_delta):
	move_and_slide()

# This function will be called from the Main scene.
func initialize(start_position, player_position):
	# We position the mob by placing it at start_position
	# and rotate it towards player_position, so it looks at the player.
	look_at_from_position(start_position, player_position, Vector3.UP)
	# Rotate this mob randomly within range of -45 and +45 degrees,
	# so that it doesn't move directly towards the player.
	rotate_y(randf_range(-PI / 4, PI / 4))
	
	# We calculate a random speed (integer)
	var random_speed = randi_range(min_speed, max_speed)
	# We calculate a forward velocity that represents the speed.
	velocity = Vector3.FORWARD * random_speed
	# We then rotate the velocity vector based on the mob's Y rotation
	# in order to move in the direction the mob is looking.
	velocity = velocity.rotated(Vector3.UP, rotation.y)
	
	$AnimationPlayer.speed_scale = random_speed / min_speed

func _on_visible_on_screen_notifier_3d_screen_exited():
	queue_free()

func squash():
	squashed.emit()
	queue_free()
```

`ScoreLabel`
```
extends Label

var score = 0

func _on_mob_squashed():
	score += 1
	text = "Score: %s" % score
```