SC Controller profile file specification
----------------------------------------

Profile file contains json-encoded dictonary with specific keys. Missing keys are substituted with defaults, unknown keys are ignored. See [Desktop.sccprofile](../default_profiles/Desktop.sccprofile) for example.

Root dictonary has to contain following keys:
- `buttons`			- contains subkey for controller buttons. See [buttons](#buttons).
- `pad_left`		- sets action executed when finger is moved on left touchpad.
- `pad_right`		- ... when finger is moved on right touchpad.
- `stick`			- ... when stick angle is changed.
- `trigger_left`	- ... when left trigger value is changed.
- `trigger_right`	- ... when right trigger value is changed.
- `gyro`			- ... when gyroscope reading changes. Gyroscope in is activated only if this key is set to something else than `NoAction`
- `menus`			- stores menus saved in profile. See [menus](#menus).
- `version`			- profile file version. Current version is _1_. See If not pressent, _0_ is assumed. If profile file version is lower than expected, automatic conversion may happen. This conversion is in-memory only, but changing and saving such profile in GUI will save converted data.

See [actions.md](actions.md) file for list of possible actions.


## <A name="Action_definition"></a>Action definition
Action definition is dictionary containing `action` key and optional `name` key. Value assigned to `action` describes action to be executed.

`action` key can describe entire action, but for better readability, it is also possible to specify additional properties using [additional keys](#Additional_keys).

For example,

	{
	  "trigger_left": {
	    "action": "axis(Axes.ABS_Z)",
	    "name": "Aim",
	}}

assigns `axis` action with *Axes.ABS_Z* parameter to left trigger.

## <a name="Additional_keys"></a>Additional keys in action definition

#### `X` and `Y`
Turns action into `XYAction`, allowing to specify different action for each pad
or stick axis. If either of keys is specified, `action` key is ignored.

Example:

	"stick" : {
	  "X": { "action": "axis(Axes.ABS_RX)" },
	  "Y": { "action": "raxis(Axes.ABS_RY)" }
	},


is same as
`"stick" : { "action" : "XY(axis(Axes.ABS_RX), raxis(Axes.ABS_RY))" }`


#### `levels`
Turns action into `TriggerAction`, allowing to specify lower and upper trigger
levels among which is action executed.

Example:

	"trigger_left": {
	  "action": "button(BTN_LEFT)",
	  "levels": [127, 255]
	},

Sets action that presses left mouse button, but only if trigger
is roughly half-pressed.


#### `dpad`
Turns action into `DPadAction`, allowing to assign different action for each
side of pad or stick alignment.

Example:

	"dpad" : [
	  { "action": "button(Keys.KEY_UP)" },
	  { "action": "button(Keys.KEY_DOWN)" },
	  { "action": "button(Keys.KEY_LEFT)" },
	  { "action": "button(Keys.KEY_RIGHT)" }
	],


#### `ring`
Defines outer and inner ring bindings. Expects keys with 'inner' and 'outer'
actions and 'radius' as float value, but all keys are optional.

Example:

	"pad_left": {
	  "ring": {
	    "inner": { "action": "XY(axis(Axes.ABS_X), raxis(Axes.ABS_Y))" },
	    "outer": { "action": "XY(axis(Axes.ABS_RX), raxis(Axes.ABS_RY))" },
	    "radius": 0.4
	  }
	},

defines inner ring binding controlling left stick and outer ring right stick
of emulated gamepad.

#### `tilt`
Turns action into `TiltAction`, allowing to assign different action for tilting
dpad. Works pretty-much as `dpad` on gyro.

Example:

	"tilt" : [
	  { "action": "button(Keys.KEY_UP)" },
	  { "action": "button(Keys.KEY_DOWN)" },
	  { "action": "button(Keys.KEY_LEFT)" },
	  { "action": "button(Keys.KEY_RIGHT)" }
	],

#### `deadzone`
Specifies deadzone. Allows for `lower` and `upper` subkeys defaulting to
*0* and *32767* and `mode` subkey defaulting to 'CUT'.
See see [deadzone modifier](actions.md#deadzone) for list of modes.

Example:

	"trigger_left": {
		"mode" : "linear",
 	  "action": "axis(Axes.ABS_Z)",
 	  "deadzone": {
	    "lower": 100,
	    "upper": 200
  	}},

#### `curve`
Specifies curve. Allows for `strength` subkey defaulting to
*0*.

Example:

	"trigger_left": {
 	  "action": "axis(Axes.ABS_Z)",
 	  "curve": {
	    "strength": -25,
  	}},


#### `sensitivity`
Specifies input sensitivity. Value is list with one or two values for sensitivity
over X and Y axis (or one value for sensitivity of trigger.)
Default sensitivity is 1.0

Example:

	"stick" : {
	  "action": "trackball()",
	  "sensitivity": [2.0, 0.5]
	},

doubles sensitivity over X and halves over Y axis.


#### `rotate`
Rotates input pad or stick input by given angle.

Example:

	"stick" : {
	  "action": "trackball()",
	  "rotate": 15
	},



#### `feedback`
Enables haptic feedback for action. Value is list with one to three values
specifying feedback position (*'LEFT'*, *'RIGHT'* or *'BOTH'*),
amplitude and frequency.

Example:

	"pad_left": {
	  "action": "trackball()",
	  "feedback": ["LEFT", 512.0, 5.0]
	},

specifies feedback with amplitude of 512 (default vlaue)
and frequency of 5 generated by left motor.


#### `smooth`
Enables input smoothing (see [smooth modifier](actions.md#smooth))

Example:

	"pad_left": {
	  "action": "trackball()",
	  "smooth": [ 8, 0.7, 2.0 ]
	},

enables smoothing with buffer of 8 and modifier set to 0.7.


#### `osd`
If set to True, enables OSD for action.

Example:

	"X": {
	  "action": "button(Keys.BTN_EAST)",
	  "osd": true
	},

enables OSD feedback for X button.

#### `click`
If set to True, enables 'click' modifier, making action executed only when
pad or stick is pressed.

Example:

	"pad_left": {
	  "action": "mouse()",
	  "click": True
	},


#### `ball`
If set to value, enables trackball mode. Value is list with zero to two values
specifying friction and mass of virtual 'spinning ball'.
See [ball modifier](actions.md#ball) for more info.

Example:

	"pad_left": {
	  "action": "mouse()",
	  "ball": [ 10.0 ]
	},


#### `circular`
Designed to controls scroll wheel by scrolling finger around pad, but can
be used with any axis.

Example:

	"pad_left": {
	  "action": "mouse(Rels.REL_WHEEL)",
	  "circular": true
	},


#### `circularabs`
Works as to `circular`, but instead of counting with finger movements,
translates exact position on dpad to axis value.

Example:

	"pad_left": {
	  "action": "circularabs(Rels.REL_WHEEL)",
	  "circular": true
	},


#### `modes`
Defines mode shifting (see [mode modifier](actions.md#mode)).

Value is dict with physical key names (A, B, X, Y...) as keys and actions
for each mode as values. Action on same level as `mode` is used as default
action.

Example:

	"modes": {
	  "A": { "action": "mouse()" },
	  "B": { "action": "XY( axis(Axes.ABS_X), raxis(Axes.ABS_Y) )" }
	},

defines pad or stick that controls mouse while button A is pressed
and left virtual stick while button B is pressed.


#### `gestures`
Enables gesture cognition on pad (see [gestures modifier](actions.md#gestures)).

Value is dict with encoded gestures (see [description in actions.md](actions.md#gesture_format)) and actions
for each gesture as values.

Example:

	"gestures": {
	  "UD": { "action": "button(Key.R)" }
	},

enables gesture recognition with single gesture activated when user does short stroke up followed by short stroke down.

#### `doubleclick`
Defines action that is executed when user double-clicks with button.
Optional `time` key can be used on same level as `doubleclick` to modify
double-click time.

Example:

	"buttons": {
	  "A": {
	    "action": "button(KEY_X)",
	    "doubleclick": { "action": "button(KEY_Z)" },
		"time": 5
	  }
	}

defines button that emulates pressing X key when pressed normally and
pressing Z key when doubleclicked.


#### `hold`
Defines action that is executed when user holds button for short time.
Optional `time` key can be used on same level as `hold` to modify
double-click time.

Example:

	"buttons": {
	  "A": {
	    "action": "button(KEY_X)",
	    "hold": { "action": "button(KEY_Z)" },
		"time": 5
	  }
	}

defines button that emulates pressing X key when pressed normally and
pressing Z key when held for 5 seconds.


## <a name="buttons"></a>Buttons
`buttons` is dictionary with keys for each gamepad button.
Possible keys are:

- `X`, `Y`, `A` and `B` for colored buttons
- `C` for Steam button in center
- `SELECT` and `START` for small "( &lt; )" and "( &gt; )" buttons
- `LB` and `RB` for left and right bumper
- `LPAD`, `RPAD` and `STICK` for presing pads or stick.

All keys are optional. Value for each key is [action definition](#Action_definition)

Example:

	"buttons": {
	  "A":    { "action": "button(Keys.BTN_WEST)",  },
	  "B":    { "action": "osd('Hello world!')" },
	  "BACK": { "action": "button(Keys.KEY_LEFTCTRL) and button(Keys.KEY_A)" },
	}


## <a name="menus"></a>Menus
`menus` is dictionary with menus stored along with profile. Keys are IDs of
menus; Menu ID can contain any characters but dots (".") and slashes ("/").

Value for each key is same as root list in [menu file](menu-file.md)
