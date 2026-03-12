# Journal
# 3/10/26

Working with KeyboardtoPads GUI.



Raspberry PI 3B+ / 43-dev-ae21997681 2026/02/11 16:43

I've been playing around with the new keyboardToPads GUI in v43. Very cool!  I just noticed a couple things:  In the `UltimarcMiniPAC.vd209.p0440.yml` config file, both c and 1 are mapped to Start and v and 5 are mapped to Select).   I'm not sure that either c or v are the right mapping because looking at a picture of the physical controller and the diagram in the RR Masters controller manual, they probably are suppossed to map to L2/R2.  Happy to submit a PR fixing this if there not another reason for it to be this way.   This topic led me to another discovery:  When I went to fix it in the keyboardToPads GUI, there is only one entry visible in the GUI for Select and Start.  I tried changing them to 1 and 5, but when I go back in, the GUI reverts to c and v again.  I'm guessing this is because it just reads the first row in the config file for each. (which is c and v).  I was able to remediate this by creating a keyboardToPads folder in configs, copying the system file and manually editing.  Then the GUI displays properly. (Because there is now only one entry of each).   After fixing that, for some reason, [Start]+[Select] is not exiting the emulator, but I'm still troubleshooting that and not ready to report that as a potential issue yet.    





I didn't want to overload my previous post so here is another complexity I've observed about keyboardToPads: it "thinks" any Ultimarc MiniPAC/IPAC2/IPAC4 is a Rec Room Masters controller, because that is what is in the RR Master's controller.  But in the example of the above, I'm using the MiniPAC in my own build.  Based on what I've seen, there is probably no way to tell if a board is actually physically in a RR Master's controller or in a DIY build because they are standard parts.  One way to deal with this could be to genericize/change the name attribute in the config files.  Another way is to just put this caveat about this in the wiki. I'm happy to do either of these things if there is a strong opinion one way or the other.  

RR Master controller manual:

https://recroommasters.com/wp-content/uploads/2023/05/Xtension-EE-Product-Manual-2023.pdf

Created a custom config and removed c and v. 

Went into map a controller and mapped.  L2 and R2 didnt' work bc they need a mapping neither did hot key. 

Didn't work.  Need to compare to the RR Room masters functionality.  Hotkey + B for example.  Also check the hotkey mapping.  

Re did map a contorller and now the hotkyes work EXCEPT For Start+Select to exit.

Saw this in the documentation:

```
# Xtension 2 Player Plus
target_devices:
  - name: Xtension 2P Player 1
    type: joystick
    mapping:
      "key:up":        "abs:hat0y:-1"
      "key:down":      "abs:hat0y:1"
      "key:left":      "abs:hat0x:-1"
      "key:right":     "abs:hat0x:1"
      "key:leftshift": "btn:south"
      "key:enter":     "btn:south"
      "key:z":         "btn:east"
      "key:leftctrl":  "btn:west"
      "key:leftalt":   "btn:north"
      "key:space":     "btn:tl"
      "key:x":         "btn:tr"
      "key:c":         "btn:start"
      "key:1":         "btn:start"
      "key:v":         "btn:select"
      "key:5":         "btn:select"
  - name: Xtension 2P Player 2
    type: joystick
    mapping:
      "key:r": "abs:hat0y:-1"
      "key:f": "abs:hat0y:1"
      "key:d": "abs:hat0x:-1"
      "key:g": "abs:hat0x:1"
      "key:w": "btn:south"
      "key:i": "btn:east"
      "key:a": "btn:west"
      "key:s": "btn:north"
      "key:q": "btn:tl"
      "key:k": "btn:tr"
      "key:j": "btn:start"
      "key:2": "btn:start"
      "key:1": "btn:select"
      "key:6": "btn:select"
  - name: Xtension hotkeys
    type: hotkeys
    mapping:
      "key:tab": "key:menu"
      "key:p":   "key:pause"
      "key:esc": "key:exit"
      ["key:1", "key:v"]: "key:exit" # exit from combination player 1 select + player 2 select, require batocera 43
      ["key:c", "key:space"]: "key:1" # player 2 select from combination player 1 start+tl, require batocera 43
```



And, specifically, this: 

["key:1", "key:v"]: "key:exit" # exit from combination player 1 select + player 2 select, require batocera 43



button mapping across different files:

| Physical control         | Batocera `es_input.cfg` / evmapy trigger | keyboardToPads                                  | Linux input name                   | RetroArch / RetroPad concept                 |
| ------------------------ | ---------------------------------------- | ----------------------------------------------- | ---------------------------------- | -------------------------------------------- |
| D-pad Up                 | `up`                                     | usually `abs:h0up` or equivalent mapping choice | `BTN_DPAD_UP` or `ABS_HAT0Y=-1`    | D-pad Up                                     |
| D-pad Down               | `down`                                   | usually `abs:h0down`                            | `BTN_DPAD_DOWN` or `ABS_HAT0Y=+1`  | D-pad Down                                   |
| D-pad Left               | `left`                                   | usually `abs:h0left`                            | `BTN_DPAD_LEFT` or `ABS_HAT0X=-1`  | D-pad Left                                   |
| D-pad Right              | `right`                                  | usually `abs:h0right`                           | `BTN_DPAD_RIGHT` or `ABS_HAT0X=+1` | D-pad Right                                  |
| South face button        | `b`                                      | `btn:south`                                     | `BTN_SOUTH`                        | B / Cross-style south button                 |
| East face button         | `a`                                      | `btn:east`                                      | `BTN_EAST`                         | A / Circle-style east button                 |
| West face button         | `y`                                      | `btn:west`                                      | `BTN_WEST`                         | Y / Square-style west button                 |
| North face button        | `x`                                      | `btn:north`                                     | `BTN_NORTH`                        | X / Triangle-style north button              |
| Left shoulder            | `pageup`                                 | `btn:tl`                                        | `BTN_TL`                           | L1                                           |
| Right shoulder           | `pagedown`                               | `btn:tr`                                        | `BTN_TR`                           | R1                                           |
| Left trigger             | `l2`                                     | `btn:tl2`                                       | `BTN_TL2`                          | L2                                           |
| Right trigger            | `r2`                                     | `btn:tr2`                                       | `BTN_TR2`                          | R2                                           |
| Left stick click         | `l3`                                     | `btn:thumbl`                                    | `BTN_THUMBL`                       | L3                                           |
| Right stick click        | `r3`                                     | `btn:thumbr`                                    | `BTN_THUMBR`                       | R3                                           |
| Select / Back            | `select`                                 | `btn:select`                                    | `BTN_SELECT`                       | Select / Back                                |
| Start                    | `start`                                  | `btn:start`                                     | `BTN_START`                        | Start                                        |
| Guide / PS / Xbox / Home | `hotkey` in Batocera mapping context     | often `btn:mode` if exposed                     | `BTN_MODE`                         | Guide / Home / Hotkey role depends on config |
| Left stick X/Y           | `joystick1left/right/up/down`            | `abs:leftx`, `abs:lefty` style mappings         | `ABS_X`, `ABS_Y`                   | Left analog stick                            |
| Right stick X/Y          | `joystick2left/right/up/down`            | `abs:rightx`, `abs:righty` style mappings       | `ABS_RX`, `ABS_RY`                 | Right analog stick                           |

A few notes that make this easier to decode:

- In **`es_input.cfg`**, the names are the **Batocera virtual pad names**, not the physical Linux event names. That is why **L1/R1 appear as `pageup`/`pagedown`**. 
- In **keyboardToPads**, names like `btn:tl` and `btn:tr` are basically friendly forms of Linux’s **`BTN_TL`** and **`BTN_TR`**. 
- In the Linux gamepad spec, **upper triggers** are `BTN_TL` / `BTN_TR`, and **lower triggers** are `BTN_TL2` / `BTN_TR2`. 
- Batocera’s general controller model follows the **RetroPad-style abstraction**, so RetroArch usually thinks in terms of **L1/R1/L2/R2/L3/R3** rather than `pageup` or `btn:tl`. 

For your specific shoulder-button example, the same control shows up like this:

| Meaning | `es_input.cfg` | keyboardToPads | Linux     |
| ------- | -------------- | -------------- | --------- |
| L1      | `pageup`       | `btn:tl`       | `BTN_TL`  |
| R1      | `pagedown`     | `btn:tr`       | `BTN_TR`  |
| L2      | `l2`           | `btn:tl2`      | `BTN_TL2` |
| R2      | `r2`           | `btn:tr2`      | `BTN_TR2` |



## 3/7/26

Changed controller to keyboard mode to see how it worked with Batocera's new keyboardToPads GUI.

Didn't work, realized the board is not mapped to any keyboard keys.  

Noticed discrepancy with button mapping. I thought it would be:
Y, X, L

B, A, R

But on on both my RRmasters and the XGaming, it appears to be:
A, B, X

Y, L, R

This video does a good job mapping this and it shows like my current config.  I think it is more becasue of the numbers than then button names.  I.e rather than:

ABX

YLR

it is really:

123

456
