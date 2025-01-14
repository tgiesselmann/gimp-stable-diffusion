# gimp-stable-diffusion-horde

This repository includes a GIMP plugin for communication with [stablehorde](https://stablehorde.net). Stablehorde is a cluster of stable-diffusion servers run by volunteers. You can create stable-diffusion images for free without running a colab notebook or a local server. Please check the section "Limitations" to better understand where the limits are.

Please check HISTORY.md for the latest changes. 

## Installation
### Download files

To download the files of this repository click on "Code" and select "Download ZIP". In the ZIP you will find the file "gimp-stable-diffusion-horde.py" in the subfolder "stablehorde". This is the code for the GIMP plugin. You don't need the other files in the ZIP.

### GIMP

The plugin is tested in GIMP 2.10 and runs most likely in all 2.* releases. Excluded is 2.99, because it's already Python 3 based.

1. Start GIMP and open the preferences dialog via "edit/preferences" and scroll down to "folders". Expand "folders" and click on "plug-ins". Select the folder which includes your username and copy the path. 

2. Open the file explorer, navigate to this directory and copy the file "gimp-stable-diffusion.py" from the repository into this directory. If you are on MacOS or Linux, change the file permissions to 755.

3. Restart GIMP. You should now see the new menu "AI". If you don't see this, something went wrong. Please check in this case "Troubleshooting/GIMP" for possible solutions. The menu has one item "Stablehorde". This item can't currently be selected. This only works, when you opened an image before. More about this below.

## Generate images
Now we are ready for generating images.

1. Start GIMP and create/open an image with a size between 512x512 and 1024x1024. The generated image will have the size of the opened image or is a bit smaller. Check below for an explanation.
   - Stable diffusion only generates image sizes which are a multiple of 64. This means, if your opened image has a size of 650x512, the generated image will have a size of 640x512.
   - The larger the image, the longer you have to wait for generation. The reason is, that all servers in the cluster support 512x512, but not all larger sizes.

2. Select the new AI/Stablehorde menu item. A dialog will open, where you can enter the details for the image generation.

   - **Generation Mode:** 
      - **Text -> Image:** Generate an image based on your prompt.
      - **Image -> Image:** Generate an image based on an init image and on your prompt.
      - **Inpainting:** Erase a part of an image and generate a new image which has the erased part filled. The erased part is filled based on your prompt. Please read the section "Inpainting" below for an explanation how inpainting works.

   - **Init Strength:** How much the AI should take the init image into account. The higher the value, the more will the generated image look like the init image. 0.3 is a good value to use.

   - **Prompt Strength:** How much the AI should follow the prompt. The higher the value, the more the AI will generate an image which looks like your prompt. 7.5 is a good value to use.

   - **Steps:** How many steps the AI should use to generate the image. The higher the value, the more the AI will work on details. But it also means, the longer the generation takes and the more the GPU is used. 50 is a good value to use.

   - **Seed:** This parameter is optional. If it is empty, a random seed will be generated on the server. If you use a seed, the same image is generated again in the case the same parameters for init strength, steps, etc. are used. A slightly different image will be generated, if the parameters are modified. You find the seed in an additional layer at the top left. 

   - **NSFW:** If you want to send a prompt, which is excplicitly NSFW (Not Safe For Work). 
      - If you flag your request as NSFW, only servers, which accept NSFW prompts, work on the request. It's very likely, that it takes then longer than usual to generate the image. If you don't flag the prompt, but it is NSFW, you will receive a black image.
      - If you didn't flag your request as NSFW and don't prompt NSFW, you will receive in some cases a black image, although it's not NSFW (false positive). Just rerun the generation in that case.

   - **Prompt:** How the generated image should look like.

   - **API key:** This parameter is optional. If you don't enter an API key, you run the image generation as anonymous. The downside is, that you will have then the lowest priority in the generation queue. For that reason it is recommended registering for free on [stablehorde](https://stablehorde.net) and getting an API key.

   - **Max Wait:** The maximum time in minutes you want to wait until image generation is finished. When the max time is reached, a timeout happens and the generation request is stopped.

3. Click on the OK button. The values you inserted into the dialog will be transmitted to the server, which dispatches the request now to one of the stable-diffusion servers in the cluster. Your generation request is added to queue. You can see the queue position and the remaining wait time in the status bar of the dialog. When the image has been generated successfully, it will be shown as a new image in GIMP. The used seed is shown at the top left in an additional layer.

## Inpainting
Inpainting means replacing a part of an existing image. For example if you don't like the face on an image, you can replace it. **Inpainting is currently still in experimental stage. So, please don't expect perfect results.** The experimental stage is caused by the server side and not by GIMP.

For inpainting it's necessary to prepare the input image because the AI needs to know which part you want to replace. For this purpose you replace this image part by transparency. To do so, open the init image in GIMP and select "Layer/Transparency/Add alpha channel". Select now the part of the image which should be replaced and delete it. You can also use the eraser tool. 

For the prompt you use now a description of the new image. For example the image shows currently "a little girl running over a meadow with a balloon" and you want to replace the balloon by a parachute. You just write now "a little girl running over a meadow with a parachute".

## Limitations
   - **Stability:** Stablehorde is still pretty new and under heavy development. So, it's not unlikely, that the servers are not available for some time or unexpected errors occur.

   - **Generation speed:** Stablehorde is a cluster of stable-diffusion servers run by volunteers. The generation speed depends on how many servers are in the cluster, which hardware they use and how many others want to generate with stablehorde. The upside is, that stablehorde is free to use, the downside that the generation speed is unpredictable.

   - **Privacy:** The privacy stablehorde offers is similar to generating in a public discord channel. So, please assume, that neither your prompts nor your generated images are private.
   
   - **Features:** Currently text2img, img2img and inpainting are supported. As soon as stablehorde supports outpainting, this will be available in the plugin too.

## Troubleshooting
### GIMP
#### AI menu is not shown
##### Linux
   - If you get this error ```gimp: LibGimpBase-WARNING: gimp: gimp_wire_read(): error```, it's very likely, that you have a GIMP version installed, which doesn't include Python. Check, if you have got the menu "Filters > Python-Fu > Console". If it is missing, please install GIMP from here: https://flathub.org/apps/details/org.gimp.GIMP.
  
  - Please try https://flathub.org/apps/details/org.gimp.GIMP if you have got any other problems.

##### macOS
   - Please double check, if the permissions of the plugin py file are set to 755. It seems, that changing permissions doesn't work via the file manager. Please open a terminal, cd to the plugins directory and run "chmod ugo+x *py".
   
##### macOS/Linux
   - Open a terminal an try to run the plugin py file manually via ```python <path-to-plugin-folder>/gimp-stable-diffusion.py```. You should see the error message, that "gimpfu" is unknown. Make sure, that you are running Python 2, as this version is used by GIMP. If other errors occur, please reinstall GIMP.

## FAQ
**Why is the generated image smaller than the opened image?** Stable-diffusion only generates image sizes which are a multiple of 64. This means, if your opened image has a size of 650x512, the generated image will have a size of 640x512.

**Will GIMP 3 be supported?** Yes, the plugin will be ported to GIMP 3.

**Will outpainting be supported?** Pretty likely outpainting will be supported. This depends on which features the stablehorde cluster supports.

**How do I report an error or request a new feature?** Please open a new issue in this repository.


