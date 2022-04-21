# Overview

A repo to record learnings along side LiveOverflow's Minecraft Hacking Youtube Series
- [Series Playlist](https://www.youtube.com/playlist?list=PLhixgUqwRTjwvBI-hmbZ2rpkAl4lutnJG)
- [Series Intro Video](https://www.youtube.com/watch?v=Ekcseve-mOg)

# History

This section consists of write-ups in choronological order, organised by sub-sections.

## Find the seed used in this series
- First idea comes to mind is to understand better on how minecraft seed actually works
    - [This SO answer](https://gaming.stackexchange.com/questions/22474/how-do-level-seeds-work) explain some basics on how a seed works
    - [This minecraft fandom wiki](https://minecraft.fandom.com/wiki/Seed_(level_generation)) goes into much more detail about how it works. 
- The result of my research seems to suggest that there's no direct way of inferring by understanding how minecraft seed works and by looking for clues from the video.
    - Along the way of this investigation, found [this helpful website (mcseeder)](https://www.mcseeder.com/?seed=-372327949&version=18) that renders minecraft map from any seed.
- However, got another idea to undo the mosaic effect of the seed shown in the video. 
    - Know that the mosaic blur or even some of the method of covering text using block of colour is not secure.
    - Knew about this a while back after stumbling across an article. Unfortunately can't find that article anymore. A quick search returns this [SO questions which contains some good explanation](https://security.stackexchange.com/questions/161436/is-it-possible-for-someone-to-see-under-the-blacked-out-part-of-this-image-se).
- Found a [github project called Depix](https://github.com/beurtschipper/Depix) that looks like able to get the job done easily
    - command used
        ```batch
        python Depix\\depix.py -p seed\\pixelize-seed-only-invert-colour.png -s Depix\\images\\searchimages\\debruinseq_notepad_Windows10_closeAndSpaced.png -o seed\\uncensored.png
        ```
    - Halfway when running the unpixelize, found out that for best effect the font size, colour and typesetting should be as close to the `searchimages` for best effect.
        - Invert the colour of the cropped image and run the adjusted command again.
        - Third try: The image must only be cropped to contain the pixelize portion only
- Fail to unpixelize the image using Depix, try another tool called [DepixHMM](https://github.com/JonasSchatz/DepixHMM.git), which aims to be a more accurate version of Depix.
    - Further reading reveals a more recent tool called [unredactor](https://github.com/BishopFox/unredacter). Using this instead as it more recent and looks more polished.
    - download ttf file for minecraft font from [here](https://fonts2u.com/minecraft-regular.font)
    - modify the code according to the readme
        - particular effort is spent on making sure the font size, text shadow, font color, etc matches the original secret.png as much as possible.
    - Not able to redact the image, spending more time on understanding the code
        - Read about how [Jimp scan function works](https://github.com/oliver-moran/jimp/tree/master/packages/jimp).
        - Turn out that need to wait for the browser windows [to finish loading](https://github.com/electron/electron/issues/6798). Currently wait for 5 seconds before saving screenshot.
        - use `await <variable of type any>` to [wait for imageData](https://stackoverflow.com/questions/22125865/wait-until-flag-true) to be populated.
        - last checkpoint, `cropped_redacted_image.bitmap.width` is smaller than the width that it needs to be cropped into.

### Launching Offline MC
- Use ATLauncher by building it from source
    - before building, modify `Instance.java` so that no online account is required to play offline
        - comment out or modify any use/check of `account` object
        - add `mclaucher.launch()` for offiline mode
    - also need to modify `MCLauncher.java` 
        - to remove any references to account in `replaceArgument()`
        - to comment out any use of account in `censorArguments()`
    - run `gradlew.bat build -x test` to build the source but skip UT
- Unzip lib/ folder in build/distribution/ATLauncher-x.x.x.zip into a folder of your choice
    - %USERPROFILE%\Downloads\Games\Minecraft\launcher\ATLauncher-mod\lib
    - This folder act as the release for ATLauncher
- TODO:
    - All modifications are now source control except for the changes in `ATLauncher.bat`
    - The following is added into the bat file so that the files downloaded are nicely put into a folder called data/
        ```batch
        @rem ...
        if exist %APP_HOME%\data\ (
        echo %APP_HOME%\data\ exist
        ) else (
        mkdir %APP_HOME%\data\
        )
        cd %APP_HOME%\data\
        @rem ...
        ```

### Alternative to launching offline MC
- By watching [this LiveOverflow video](https://www.youtube.com/watch?v=Hmmr1oLt-V8), it seems like offline MC can also be easily launched by using [the Fabric example Mod](https://github.com/FabricMC/fabric-example-mod/tree/1.18).
- Reorganise this readme to be in a new repo that will be consisted of other projects in the form of git submodules. This allows:
    - version control each project individually, while working on all of them in a unified place
    - putting this readme as a parent readme to all the projects. This is important as this readme records all the writeups in this journey 🚗.
- Encountered an unexpected error when trying to build ATLauncher from the git submodule folder
    - It appeared that the build output in the distribution folder will be different if `-x test` is added to the `gradlew.bat build` command.
- Stashing the reference links of all the tools that originally thought of needing to download for launching offline MC
    - https://www.minecraft.net/en-us/download
    - https://www.minecraft.net/en-us/download/server
    - https://github.com/QPCrummer/TaterLauncher/commits/dev?before=a2be09b74fb1863da85c228ad13a58e933aef828+35&branch=dev
    - https://github.com/search?o=desc&p=2&q=minecraft+launcher&s=updated&type=Repositories
    - https://github.com/Corona-Studio/ProjBobcat


## References / To-read
- [A wiki site that reverse engineer minecraft](https://wiki.vg/Main_Page)
- [Article explaining about unpixelize text from image](https://www.linkedin.com/pulse/recovering-passwords-from-pixelized-screenshots-sipke-mellema/)
- $HOME/Downloads/Games/Minecraft/ATLauncher
    - https://github.com/ATLauncher/ATLauncher

