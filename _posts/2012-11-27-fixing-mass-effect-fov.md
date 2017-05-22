---
title: "How to actually fix FOV in Mass Effect"
redirect_from:
    - /blog/2012-11-27-mass-effect-fov/
    - /post/fixing-mass-effect-fov/
---

The viewing angle (FOV) in Mass Effect games is too small, PC players are upset, but developers never cared. By the time of this writing the only solution to be found on gaming forums is clumsy and messes up cutscenes. So here is the actually working fix, for each of three Mass Effect games.

> What is widely reposted between forums as FOV fix (by binding several FOV commands to keypad) is a bad way to go. 
> That command is a global override; each game mode requires a different FOV but that command forces the same one on them all, 
> consequently messing those which are not designed for different FOV (like conversations and cutscenes). 
> That is why they also need to bind a key to “FOV 0″ to temporarily turn off this “fix” every time a cutscene appears.
> When a cutscene ends, they have to press a key again; and so the player has frequently re-fix during play.
> 
> The fix in this article changes FOV permanently, does not mess up cutscenes and does not require pressing keypad buttons all the time.

## Mass Effect 3

> Note: you might be banned from multiplayer for changing `Coalesced.bin`.

0. Use [Gibbed Mass Effect 3 Coalesced.bin tool](http://blog.gib.me/2012/02/16/mass-effect-3-coalesced-bin-tool/) to unpack `Coalesced.bin` into separate `.json`. files.

0. Open `06_bioinput.json` in text editor and find the `sfxgame.sfxgamemodedefault.bindings` section.

0. Append a comma after the last element (the one that binds Tab key), and add a binding for Period key as follows:

        "sfxgame.sfxgamemodedefault": {
         "bindings": [
         "( Name=\"LeftControl\", Command=\"Walking\" )",
        .............................................................
         "( Name=\"Tab\", Command=\"PC_PushToTalk\" )",
         "( Name=\"Period\", Command=\"set SFXGame.SFXCameraMode FOV 100\" )"
         ],

0. If you've been using the bad FOV fix previously, revert `SharedAim` and `RightMouseButton` to their original values.

0. Pack `.json` files back into new `Coalesced.bin` and replace the old one.

0. See the end of this article about how to use it (*hint: press Period `.` key*).

Save a copy — in case game auto-update rewrites the file back to original one.

## Mass Effect 2

0. Get Notepad++ editor. *Notepad, Wordpad, Word will not work, they will corrupt the file*.

0. Save a backup of `MassEffect2\BioGame\Config\PC\Cooked\Coalesced.ini`. Do it, because if you accidentally screw it up, the game will stop working.

0. Open `Coalesced.ini` with Notepad++ and find `[SFXGame.SFXGameModeDefault]` section.

0. Add binding for the Period key somewhere in that section:

        [SFXGame.SFXGameModeDefault]
        InputDelayStormExit=0.23
        InputDelayTightAimExit=0.1
        RadarMapDisplayTime=1.0
        Bindings=( Name="LeftControl", Command="Walking" )
        ......................................................................
        Bindings=( Name="LeftShift", Command="PC_EnterCommandMenu" )
        Bindings=( Name="Period", Command="set SFXGame.SFXCameraMode FOV 100" )

0. (Optional) Also fix mouse acceleration. You'd be surprised how better will the aiming be. 
    Search for these values in the file and make sure they are `false`:

        bEnableMouseSmoothing=false
        bUseMouseDampening=false

    That will require to lower sensitivity somewhat. I'm using this value:

        MouseSensitivity=0.15

0. If you've been using the bad FOV fix previously, revert `SharedAim` and `RightMouseButton` to their original values.

0. Download ME2IniFixer into the same folder and run it to fix the file. **If the game started crashing on start, it means you have used a bad text editor, or did not apply ME2IniFixer. Restore `Coalesced.ini` from backup and retry.**

0. See the end of this article about how to use it (*hint: press Period `.` key*).

Save a copy of `Coalesced.ini` — in case game auto-update rewrites the file back to original one.

## Mass Effect 1

> For some reason I never wanted to fix FOV in ME1, probably because big camera distance kind of compensates low FOV. But people asked so here it is.

0. Open `Documents\BioWare\Mass Effect\Config\BIOInput.ini` with any text editor.

0. Locate `[BIOC_Base.BioPlayerInput]` section and add the Period binding into it:

        [BIOC_Base.BioPlayerInput]
        Bindings=(Name="Period",Command="set BioCameraBehaviorFollow CAMERA_FOV 100 | set BioCameraBehaviorZoom CAMERA_FOV 40")

0. See the next chapter about how to use it (*hint: press Period `.` key*).

## How to use it

Get to a stage when your Shepard can run around, and hit Period `.` key. You only need to do it once, then it works until you exit the game.

This fix sets FOV to `100`. You can try other values, the good ones are in the range `80..120`.

> Theoretically the Unreal engine allows to set object properties on load using a config file and then no key binding would be needed. However it does not work with Mass Effect games. Probably because developers rewrite camera's FOV with the hardcoded value `75` **after** the config has been read.

