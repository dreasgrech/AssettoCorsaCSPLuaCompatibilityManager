`CSPLuaCompatibilityManager` is a lua module for Assetto Corsa Custom Shader Patch (CSP) apps used for informing a user with a modal dialog if he's running an older version of CSP which doesn't include the function your app needs.

The point of this is so that if a user of your app is running a version of CSP where some CSP function you are using didn't exist in yet, you can inform the user about it and be in control of whether you want to allow the app to continue running or not.  

**Informing the user about an outdated CSP version could be the difference between the user succesfuly updating CSP to run your app versus rating your app negatively on whichever platform you hosted it on.**

<p align="center">
    <img width="809" height="305" alt="image" src="https://github.com/user-attachments/assets/9dced4ed-c31b-476b-865f-20e4329e20f5" />
</p>

<img width="2560" height="1440" alt="Screenshot 1_27_2026 3_07_44 PM" src="https://github.com/user-attachments/assets/14c4cbd9-a1b8-419f-89a1-7cfa123010da" />

## How to use
### Download and Extract
Download and extract the `AssettoCorsaCSPLuaCompatibilityManager` directory to the directory of your Assetto Corsa CSP lua app.

### Including the CSPCompatibilityManager to your code
Add the `CSPCompatibilityManager` module to your app ideally in the very first line:
```lua
local CSPCompatibilityManager = require("AssettoCorsaCSPLuaCompatibilityManager.CSPCompatibilityManager")
```
If you put the `AssettoCorsaCSPLuaCompatibilityManager` directory in a subdirectory, such as `lib`, you will need to include it like this:
```lua
local CSPCompatibilityManager = require("lib.AssettoCorsaCSPLuaCompatibilityManager.CSPCompatibilityManager")
```
### Adding your CSP functions
Next we need to add a reference to all the CSP functions that you are using in your app which have the potential of not working on outdated CSP versions (or ideally just add all the CSP functions you are using here to really make sure your app doesn't break).
Currently there are two functions you can use: `CSPCompatibilityManager.addFunction` and `CSPCompatibilityManager.addSimStateFunction`.

These functions accept two formal arguments.  The first is a function which returns a reference to the CSP function you want testing, and the second is a string representing the name of the function which is shown in the error message if it doesn't exist.

#### `CSPCompatibilityManager.addFunction`: Used for adding a reference to any type of CSP function:
```lua
CSPCompatibilityManager.addFunction(function() return ac.setWindowOpen end, "ac.setWindowOpen")
CSPCompatibilityManager.addFunction(function() return ui.pushItemWidth end, "ui.pushItemWidth")
CSPCompatibilityManager.addFunction(function() return physics.preventAIFromRetiring end, "physics.preventAIFromRetiring")
```

#### `CSPCompatibilityManager.addSimStateFunction`: Used for adding a reference to a CSP function from the `ac.StateSim` table from `ac.getSim()`:
```lua
CSPCompatibilityManager.addSimStateFunction(function(sim) return sim.trackLengthM end, "ac.getSim().trackLengthM")
CSPCompatibilityManager.addSimStateFunction(function(sim) return sim.raceSessionType end, "ac.getSim().raceSessionType")
```

**Both these functions provide a safe way of accessing the CSP functions without actually throwing any errors which cause your lua app to break.**

### Call `checkForMissingElements()` to check if all the needed CSP functions are available.
The `CSPCompatibilityManager.checkForMissingElements` function requires the name and version of your app (these are used for the error message shown to the user) and returns a `boolean` indicating whether all the functions exist in the player's current CSP version.
```lua
local everythingOK = CSPCompatibilityManager.checkForMissingElements("My incredible app", "v0.9.5")
```

### Clear the references to the functions added before
Call `CSPCompatibilityManager.freeMemory()` to get rid of the memory used by the metadata we added previously.
```lua
CSPCompatibilityManager.freeMemory()
```

## Sample usage
```lua
-- import the CSPCompatibilityManager module ideally in the first line of your app
local CSPCompatibilityManager = require("AssettoCorsaCSPLuaCompatibilityManager.CSPCompatibilityManager")

-- add all the csp functions that your app uses which you want to check for existence in the CSP version running on the user's app
CSPCompatibilityManager.addFunction(function() return ac.log end, "ac.log")
CSPCompatibilityManager.addFunction(function() return ac.warn end, "ac.warn")
CSPCompatibilityManager.addFunction(function() return ac.error end, "ac.error")
CSPCompatibilityManager.addFunction(function() return ac.getSim end, "ac.getSim")
CSPCompatibilityManager.addFunction(function() return ac.storage end, "ac.storage")
CSPCompatibilityManager.addFunction(function() return ac.setWindowOpen end, "ac.setWindowOpen")

CSPCompatibilityManager.addFunction(function() return ui.button end, "ui.button")
CSPCompatibilityManager.addFunction(function() return ui.newLine end, "ui.newLine")
CSPCompatibilityManager.addFunction(function() return ui.text end, "ui.text")
CSPCompatibilityManager.addFunction(function() return ui.pushItemWidth end, "ui.pushItemWidth")
CSPCompatibilityManager.addFunction(function() return ui.popItemWidth end, "ui.popItemWidth")
CSPCompatibilityManager.addFunction(function() return ui.itemHovered end, "ui.itemHovered")
CSPCompatibilityManager.addFunction(function() return ui.setTooltip end, "ui.setTooltip")
CSPCompatibilityManager.addFunction(function() return ui.pushDisabled end, "ui.pushDisabled")
CSPCompatibilityManager.addFunction(function() return ui.popDisabled end, "ui.popDisabled")
CSPCompatibilityManager.addFunction(function() return ui.columns end, "ui.columns")
CSPCompatibilityManager.addFunction(function() return ui.ButtonFlags end, "ui.buttonFlags")
CSPCompatibilityManager.addFunction(function() return ui.pushStyleColor end, "ui.pushStyleColor")
CSPCompatibilityManager.addFunction(function() return ui.popStyleColor end, "ui.popStyleColor")
CSPCompatibilityManager.addFunction(function() return ui.textColored end, "ui.textColored")
CSPCompatibilityManager.addFunction(function() return ui.setColumnWidth end, "ui.setColumnWidth")
CSPCompatibilityManager.addFunction(function() return ui.separator end, "ui.separator")
CSPCompatibilityManager.addFunction(function() return ui.nextColumn end, "ui.nextColumn")
CSPCompatibilityManager.addFunction(function() return ui.pushID end, "ui.pushID")
CSPCompatibilityManager.addFunction(function() return ui.popID end, "ui.popID")
CSPCompatibilityManager.addFunction(function() return ui.itemClicked end, "ui.itemClicked")
CSPCompatibilityManager.addFunction(function() return ui.sameLine end, "ui.sameLine")
CSPCompatibilityManager.addFunction(function() return ui.slider end, "ui.slider")
CSPCompatibilityManager.addFunction(function() return ui.dwriteText end, "ui.dwriteText")
CSPCompatibilityManager.addFunction(function() return ui.checkbox end, "ui.checkbox")
CSPCompatibilityManager.addFunction(function() return ui.MouseButton end, "ui.MouseButton")
CSPCompatibilityManager.addFunction(function() return ui.mouseClicked end, "ui.mouseClicked")
CSPCompatibilityManager.addFunction(function() return ui.alignTextToFramePadding end, "ui.alignTextToFramePadding")
CSPCompatibilityManager.addFunction(function() return ui.setMouseCursor end, "ui.setMouseCursor")
CSPCompatibilityManager.addFunction(function() return ui.styleColor end, "ui.styleColor")
CSPCompatibilityManager.addFunction(function() return ui.StyleColor end, "ui.StyleColor")

-- add the functions that we want to check from ac.getSim()
CSPCompatibilityManager.addSimStateFunction(function(sim) return sim.trackLengthM end, "ac.getSim().trackLengthM")
CSPCompatibilityManager.addSimStateFunction(function(sim) return sim.raceSessionType end, "ac.getSim().raceSessionType")

-- make sure all the functions exist and show the modal dialog if any are missing
local APP_NAME = 'My App'
local APP_VERSION = 'v0.95'
local everythingOK = CSPCompatibilityManager.checkForMissingElements(APP_NAME, APP_VERSION)
CSPCompatibilityManager.freeMemory() -- call to get rid of the memory used by the metadata we added previously

-- if any of the functions are missing, we can output an error here and possibly even halt the app from running any further.
if not everythingOK then
    ac.error(string.format("%s v%s is missing required Custom Shaders Patch elements and will not run as expected.", APP_NAME, APP_VERSION))
    return false -- if we don't want the app to continue running.
end
```
