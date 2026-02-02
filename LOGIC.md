# .sjr -> .sb3

> [!NOTE]
> **Work in progress!** I'm figuring out how to go about writing the steps. At the moment, this is unfinished, horribly explained, and riddled with inaccuracies. :>

These are the steps for super accurately converting an .sjr file to .sb3! 🥳

## Notes

- These steps are expected to be completed by a computer program. Performing them manually would be cumbersome.
- Arrays are assumed to be 0-indexed, as is the case in most programming languages in which one would implement this procedure.
- Some steps are there to account for obscure edge cases and are largely unnecessary if you only want to support ordinary ScratchJr projects. However, they are needed if complete accuracy to ScratchJr is desired. These steps are marked as (optional), but if possible, do them!

## Retrieving Files

1. Unzip the .sjr project file.

   - a. If the file fails to unzip, it is an invalid or corrupted project file.
     Throw an error.

   - b. If there is no file at the path `project/data.json` within the
     decompressed file directory, the project is missing necessary data and
     cannot be converted. Throw an error.

   - c. If the contents of the file at the path `project/data.json` fail to be
     parsed, or otherwise is not an object after being parsed, throw an error.

   - d. Let `data` be an object and the contents of the file at the path
     `project/data.json` inside the .sjr. (Note that `data.json` in later steps
     will access the `json` property of `data`, and does **not** refer to the file
     named `data.json`.)

## Converting the JSON

2. Let `project` be an object representing the contents of the resulting `project.json`.

   - a. Initialize `project` with the following value:

     ```json
     {
       "targets": [
         {
           "isStage": true,
           "name": "Stage",
           "variables": {},
           "lists": {},
           "broadcasts": {
             "a": "🟠 Orange",
             "b": "🔴 Red",
             "c": "🟡 Yellow",
             "d": "🟢 Green",
             "e": "🔵 Blue",
             "f": "🟣 Purple"
           },
           "blocks": {},
           "comments": {
             "a": {
               "blockId": null,
               "x": 50,
               "y": 50,
               "width": 350,
               "height": 170,
               "minimized": false,
               "text": "Configuration for https://turbowarp.org/\nYou can move, resize, and minimize this comment, but don't edit it by hand. This comment can be deleted to remove the stored settings.\n{\"interpolation\":true} // _twconfig_"
             }
           },
           "currentCostume": 0,
           "costumes": [],
           "sounds": [],
           "volume": 100,
           "layerOrder": 0,
           "tempo": 60,
           "videoTransparency": 50,
           "videoState": "on",
           "textToSpeechLanguage": null
         }
       ],
       "monitors": [],
       "extensions": [],
       "meta": {
         "semver": "3.0.0",
         "vm": "0.2.0",
         "agent": "",
         "sjr": null
       }
     }
     ```

   - b. Let `stage` be the first object of the `project.targets` array to have
     the `isStage` property set to `true` (i.e. let `stage` be the object
     representing the Stage).

3. If any of the following values are missing or of the wrong type, throw
     an error:

     - `data.json` is an object

     - `data.json.pages` is a string array that is not empty

4. For each `name` in the `data.json.pages` array:

   - a. Let `page` be the object at `data.json.pages[name]`. If it does
     not exist, then let `page` instead be the following value:
     ```json
     {
       "textstartat" :36,
       "sprites": ["Cat 1"],
       "num": 1,
       "lastSprite": "Cat 1",
       "Cat 1": {
         "shown": true,
         "type": "sprite",
         "md5": "Cat.svg",
         "id": "Cat 1",
         "flip": false,
         "name": "Cat",
         "angle": 0,
         "scale": 0.5,
         "speed": 2,
         "defaultScale": 0.5,
         "sounds": ["pop.mp3"],
         "xcoor": 240,
         "ycoor":180,
         "cx": 83,
         "cy": 116,
         "w": 166,
         "h": 232,
         "homex": 240,
         "homey": 180,
         "homescale": 0.5,
         "homeshown": true,
         "homeflip": false,
         "scripts":[]
     }
     ```

   - b. Add a backdrop for the page to the Stage.

     - If the value of `page.md5` exists:

       - Append the following object to `stage.costumes`:

         ```json
         {
             "name": name,
             "dataFormat": fmt,
             "assetId": hash,
             "md5ext": page.md5,
             "rotationCenterX": 240,
             "rotationCenterY": 180
         }
         ```

     - Otherwise:
       - Append the following object to `stage.costumes`:

       ```json
       {
           "name": name,
           "dataFormat": "svg",
           "assetId": "cd21514d0531fdffb22204e0ec5ed84a",
           "md5ext": "cd21514d0531fdffb22204e0ec5ed84a.svg",
           "rotationCenterX": 240,
           "rotationCenterY": 180
       }
       ```

       - Ensure the following image file will be saved under the name
         `cd21514d0531fdffb22204e0ec5ed84a.svg` alongside the other assets in
         the resulting `.sb3`:

       ```svg
       <svg version="1.1" width="2" height="2" viewBox="-1 -1 2 2" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
       <!-- Exported by Scratch - http://scratch.mit.edu/ -->
       </svg>
       ```
