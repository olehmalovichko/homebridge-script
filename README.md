homebridge-script
==============

Execute custom scripts via homekit apps.
Core of the code written by Oleh Malovichko https://github.com/xxcombat.
Original plugin https://github.com/xxcombat/homebridge-script.
I've updated it to allow for executing a state script or work by checking for the existance of a file.
This fork depends on file-exists however there is no need to install it seperately for this fork.


## Installation

1. Install homebridge using: `npm install -g homebridge`
2. Install this plugin using: `npm install -g pponce/homebridge-script`
3. Update your configuration file. See `sample-config.json` in this repository for a sample.
4. Copy scripts (*.sh) files to own directory.

For autostart homebridge with OSX copy com.homebridge.startup.plist to /Library/LaunchDaemons

Homebridge-script configuration parameters

Name | Value | Required | Notes
----------- | ------- | --------- | --------------
`accessory` | "Script" | yes | Must be set to "Script" and is required
`name` | _(custom)_ | yes | Name of accessory that will appear in homekit app and is required
`on` | _(custom)_ | yes | Location of script to execute the on action and is required
`off` | _(custom)_ | yes | Location of script to execute the off action and is required
`fileState` | _(custom)_ | no (see note) | Location of file that flags on or off current state. If this is configured the plugin will use the existence of this file to determine the current on or off state. If file exists, accessory is determined to be on. If file does not exist, accessory is determined to be off. This is not required. But if set, it will override using the state script. fileState or state must be configured.
`state` | _(custom)_ | no (see note) | Location of script to execute the current state check. It must output to stdout the current state. It is not required if fileState is being used instead. fileState or state must be configured.
`on_value` | _(custom)_ | no (see note) | Used in conjunction with the state script. If using the state script this is the value that will be used to match against the state script output. If this value matches the output, then the accessory will be determined to be on. Required if using state script.

## Configuration

### Configuration example 1:

```
"accessories": [
	{
              "accessory": "Script",
              "name": "RPC3 Socket 1",
              "on": "/var/homebridge/rpc3control/on.sh 1",
              "off": "/var/homebridge/rpc3control/off.sh 1",
              "state": "/var/homebridge/rpc3control/state.sh 1",
              "fileState": "/var/homebridge/rpc3control/script1.flag",
              "on_value" : "true",
              }
	}
]
```
#### Notes
##### Using the above configuration as an example:
- The on.sh script executes when you turn on the accessory via a homekit app. (In this case we are the using existence of a file to determine on or off current state, so you must insure the on.sh script creates the configured fileState file.
- The off.sh script executes when you turn off the accessory via a homekit app. ( In this case we are using existence of a file to determine on or off current state, insure the off.sh script deletes the configured fileState file.)
- The state.sh script in this case would not execute as fileState parameter overrides its use.
- The configured fileState file is used as a flag. When the homekit app checks for current state it checks for the existence of this file. If it exists, current state is on. If it does not exist, current state is off.
- The on_value in this case is not being used as it is only used when the state script is used to check for current state.

### Configuration example 2:
```
"accessories": [
	{
              "accessory": "Script",
              "name": "Alarm of bike",
              "on": "~/on.sh",
              "off": "~/off.sh",
              "state": "~/state.sh",
              "on_value" : "true",
              }
	}
]
```
#### Notes
##### Using the above configuration as an example:
- The on.sh script executes when you turn on the accessory via a homekit app. (In this case we are executing a state script to determine on or off current state.)
- The off.sh script executes when you turn off the accessory via a homekit app. ( In this case we are executing a state script to determine on or off current state.)
- The state.sh script in this case would be executed to check current state.  Insure that this script outputs to stdout the matching on value as configured by the on_value config parameter. If the on_value matches the on value output of this script then the accessory will be determined to be on.
- The configured fileState file is not used in this example. Because it was not configured the state script is being used.
- The on_value in this case is used to match against the state script output. If the value matches the output of the state script the accessory is determined to be on.

