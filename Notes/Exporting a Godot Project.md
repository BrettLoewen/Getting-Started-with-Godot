#Godot 

## Exporting Steps

Exporting to Windows Desktop:
- Create an image that you want to use as the file and taskbar icon for the game
- Create a .ico file for that image
	- Open the image in an editing software such as Gimp
	- Resize the image to be 256x256
	- Create a new layer with a size of 128x128, copy the image into it, and resize the image to 128x128
	- Repeat this process until you have a layer and image for all sizes in the following list:
		- 256, 128, 64, 48, 32, 16
	- Make sure the layers are ordered descending (256 at the top and 16 at the bottom)
	- Export the image as a .ico file
- Setup your icon:
	- Go to Project > Project Settings > Application > Config, enable advanced settings, and then find the option for Windows Native Icon. Select your ico file for this option
	- Download rcedit which can be found [here](https://github.com/electron/rcedit/releases)
	- Go to Editor > Editor Settings > Export > Windows and find the rcedit property. Select the rcedit file that you downloaded
- Open Project > Export and click the add button to create a new Windows Desktop preset
- Go to the Resources section of the preset and select the files you want in your export
- Select the option to embed the .pck file in the exectuable
- Select the icon and input the name for your game in the application section of the preset
- Export the game using this preset
- And the game should be runnable using the executable file created

Exporting to Web:
- Switch to compatibility mode (which may mess with graphical stuff) but may be necessary for the game to run smoothly
- Open Project > Export and click the add button to create a new Web preset
- Go to the Resources section of the preset, switch to the scenes and dependencies option, and select the scenes you want in your export
- Export the game using this preset
- Rename the .html file created to `index.html`
- Create a zip folder with all the files created by the export
- Create a new project on Itch.io and upload that zip folder
	- Make sure to check the box for shared array buffer support
	- Fill out the rest of the information as needed
- And the game should now be runnable in Itch.io

## Notes

The rest of this note is a recording of the process of building the 2d and 3d games I made while learning Godot to both desktop and web.

Opening the project and clicking the project button at the top left reveals a menu with a Export option. Clicking that opens an empty meny for export presets. I clicked the add button at the top and it opened a dropdown where I could select the export platform like windows, android, web, etc. I selected windows and it added a new preset for windows. However, I still couldn't build the project since there were a bunch of error messages at the bottom talking about missing export templates.

I looked up the Godot docs for exporting a project. They can be found [here](https://docs.godotengine.org/en/stable/tutorials/export/exporting_projects.html).

Export templates need to be downloaded from the download page of Godot's website found [here](https://godotengine.org/download/windows/). I downloaded the export templates. However, it downloaded the one for 4.2.1 but I'm using Godot 4.2. I opened the Editor > Manage Export Templates window and pressed the option to download and install the export template I needed. After it finished I went back to the export menu and now those error messages were gone. There was a new warning message about rcedit and application icons, but I figured I would see what I could while ignoring that.

I went into the Resources section of the export preset and set it to only include the 3d game folder since that contains everything I need for the 3d game which is what I want to export first. I took a screenshot from the game, cropped it, and set it as the icon using the Options > Application > Icon setting. I also setup the game's name and company in the application section. Earlier I had setup the export path to be "/First 3D Game.exe". I pressed export and a menu opened to choose where to save the build to. I made a builds folder and selected it. The file already had the name I chose earlier in the export path (and after exporting the export path value updated to include the builds folder, so I guess it's a preset path thing). It almost instantly said it was done and told me it couldn't modify the icon because of the rcedit thing.

I opened the builds folder and found the executable file along with a pck file. Running the executable ran the game, so success. I copied it to my desktop to see if it could run on its own without the other files, and I got an error popup saying it needed the pck file. I copied the pck file to my desktop, and now I could run the game as expected. Good to know I need both files.

The exectuable is currently just using the Godot logo and I would like to change that. I did set the icon, but I guess the rcedit thing is preventing it from working. I googled it, and found a reddit post that links to a Godot doc [here](https://docs.godotengine.org/en/stable/tutorials/export/changing_application_icon_for_windows.html), so I guess I'll look at that and see what it has to say.

One thing I learned from the doc is that there is an export option to embed the pck. I wonder if that would remove the necessity of the pck file in a build. I tested this by making another build with that option enabled. Indeed, it only created the executable and it could be run anywhere without needing an accompanying file. Awesome.

Another note, I also changed the debug console wrapper option from debug only to no. When I made the build with this change, the .console file that originally accompanied the executable was no longer present, which is what I wanted. So I should do this in the future. Admittedly, I don't know what this file did, so maybe I'll learn it was valuable in the future, but for now I don't need it.

So I read through the doc and it says that I need to convert my icon png file to an ICO file using something like Gimp and then I can use that to setup my icons. I'll also need to install rcedit from a link it provides and set that up.

To create the ico file, the doc links to [this](https://youtu.be/uqV3UfM-n5Y?si=phyYAxqjMDbZ-JnL) video which goes through that process. Essentially, open your image in gimp and use the Image > Scale Image option to make your image 256x256. Then name the layer 256 (layer names are optional). Then create a new layer that is 128x128. Copy and paste the image from the first layer into this one. Press `shift + s` to resize the image to 128 and position it within the layer. Repeat this process for all sizes: 256, 128, 64, 28, 32, 16. Then make sure the layers are ordered with 256 at the top all the way to 16 at the bottom. Finally you can export it and select the .ico file extension. When you click export a menu will appear showing the different sizes and there is a 'compressed' option you need to uncheck. And with that, you now have your ico file.

The taskbar and file icons are set differently.

To set the taskbar icon, go to Project > Project Settings > Application > Config, enable advanced settings, and then find the option for Windows Native Icon. Click on the folder icon for it and select the ico file you created. (For mac, use the macos option here, and for any other platform use the generic icon option.)

To setup the file icon, first we need to download rcedit which can be found [here](https://github.com/electron/rcedit/releases). I downloaded the executable and put it in a folder with some Godot files. Then I went to Editor > Editor Settings > Export > Windows and found the rcedit property. I clicked the folder icon, found the rcedit file I downloaded, and selected it. Now that rcedit is setup, I can go open the export menu and find the Application > Icon option for my windows preset and select my ico file.

Now all the icons needed for windows should be setup.

I made another build, and it's now using the correct icon. I got an error message when exporting saying that the 128 size was missing. I tested this by modifying the view properties of my file explorer and indeed some sizes used the godot icon instead of my icon. I think this is because the 128 layer in gimp wanted to be 129x128 for whatever reason. I'm going to try to fix this. I went into gimp and unlinked the xy scales for that layer so I could force it to 128x128 since for whatever reason it wanted to stay 1 pixel off when linked. I re-exported the file and made another build. This one does not have the same issues as before, so that's good and this all seems to be working.

Now for a web build.

I made a new export preset for web. I was met with an error message saying that the .net version of Godot 4 can't build to web and that I should switch to version 3 or to a non-.net version of the editor. I switched to the non-.net version and that error message went away. I guess I should switch to using this version all the time.

Now there are other error messages about export templates like what I had before for the desktop version. It seems that there are different export templates between the .net and non-.net versions of Godot, so I guess I'll download and install the correct templates. I did that and now the errors are gone.

I found a Godot doc [here](https://docs.godotengine.org/en/stable/tutorials/export/exporting_for_web.html) about exporting to the web. One thing I noticed in it is that Godot 4's web exports apparently don't work on mac. This isn't a big deal for me since I use windows but maybe something to keep in mind.

I tried exporting and running the html, but I got an error about some features missing in order for Godot to run in the web.

I googled exporting Godot to Itch.io and found [this](https://youtu.be/RiQcnVgBhFE?si=u3keZIhOPLDHK5Qz) youtube video. The only change it makes from what I was doing is switching to compatibility mode rendering instead of forward+. I tried that and still got the error. But the video nevers runs the html file on it's own and just puts it into a zip folder along with the other exported files, puts that folder into itch.io, and it works. I tried that, and it also works.

So, to export Godot to the web: Switch to compatibility mode (which may mess with graphical stuff), export to web, put all the files created by the export into a zip folder, put that zip folder on itch.io, and you're done.

That was admittedly easier than I thought it was going to be.

Now I just need to repeat these steps for the 2D game.

I followed the procedure described above for creating the icon, setting it up in Godot, and creating the export preset. The only differences were using the 2D game folder in the export preset's resources section, disabling the music player scene from autoloading, and setting the 2d game scene as the main scene.

I exported the windows executable and tested it and it all seems to be working properly.

I followed the above steps for exporting to web and publishing on itch.io. The only thing I did differently was going into the resources section and choosing to only export the scenes and dependencies for the 2d game which I didn't bother doing for the 3d game.

I setup the game on itch.io and it seems to be working properly.

Note: in order to push the exported .exe files to github since they were too large for git, I needed to setup git lfs for the project. It was as simple as followed the link provided in the terminal message, download and run the git lfs installer, and then follow the steps on the website to set it up in my project. I normally wouldn't upload builds of a project to source control since they're big, but I'm making an exception for this project since I think having the exported files will be useful for future reference if I ever need to review the export process.

I made github releases for both games so that the exectuables can be more easily found and downloaded.

I worked on the itch.io pages for both games and published them so anyone can play the games.