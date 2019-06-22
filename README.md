# Homemanager AppModeKit Instructions
Official Homemanger AppModeKit Developer Informations and Instructions

# Introduction
Our app switched from specific SSH interactions for every feature to a powerful generic SSH manager which can be managed with a app specific JSON syntax.
The file set informations for different kinds of content in the App. With this file it is possible to customize a lot of things in the app to adapt your homebridge based system. 


You can test your settings inside the app by yourself. If you want to share your configuration with all the other users, just write us under info@homemanger-app.de and we will add it for you in the App.

# General

### Example

```json
"general": {
		"version": "1.0.0.1",
		"author": "Ron Polenthon",
		"appModeName": "Homemanager",
		"id": 570923495709283,
		"website": "http://www.homemanager-app.de",
		"email": "info@homemanager-app.de",
		"appModeSyntax": "0.1",
		"shellNewReadyLineDetection": ":~",
		"checkConnectionTimeout" : 5,
		"maxConnectionRequests" : 3,
		"logShell" : false,
		"tags" : ["Homemanager", "Pi", "Raspberry"],
		"appModeAccessible": true
}
```

| Key      | Description  |
| -------- | ---------    |
| version     | Version of your configuration.    |
| author     | Your name.         |
| appModeName   | Name of your configuration displayed in the app.         |
| id | You will receive a unique id in case your AppMode will be added globaly. Otherwise dont change the id.      |
| website     | Your website. Otherwise choose our one.       |
| email | Contact email for questions from users. If you dont want get emails, choose our one.        |
| appModeSyntax     | Just use the one from the example.       |
| shellNewReadyLineDetection | Enter an identifier which is ever presented in a new ready to interact shell line. Example: pi@bestpi:~ $ ->  :~ |
| checkConnectionTimeout     | Timeout in seconds for the connection check.       |
| maxConnectionRequests     | The maximum of request / trys for a new connection.       |
| logShell     | Use this for debuging. If enabled, every output will be logged to the LogFile.       |
| tags     | In the future AppModes can be searched, tags will be mentioned during the search.       |
| appModeAccessible     | Bool value. If set to true, your AppMode can be edited inside the App. Otherwise the data stored in this file is only visible to us.       |


### Global Constants

You can use the following global constants in your commands.


| Constant      | Description  |
| -------- | ---------    |
| ADConnPass     | The password the app uses for a SHH connection.    |
| ADConnUsrn     | The username the app uses for a SHH connection.    |


### Model Settings

Enter your own supported models.

### Example

```javascript
"modelSettings": {
		"models": {
			"pi3b+": "Raspberry Pi 3 B+"

		}
}
```

| Key      | Description  |
| -------- | ---------    |
| models     | Dictionary of your supported models. The key represents an identifier for interactions and the value represents the displayed name in the app.    |


### Global Instant Interaction

Define globaly for all features on which output the manager should instant respond.

Example

```javascript
"globalShellInstantInteractions": {
	  
	  "[sudo] password for" : "ADConnPass",
	  "Password:" : "ADConnPass",
	  "Do you want to continue? [Y/n]" : "y"
	  
}
```

| Key      | Description  |
| -------- | ---------    |
| YOURKEY     | If the key is found in the shell output, the app will instant enter the command from the defined value.     |


## Example for feature interactions

Every feature interaction is build on the same generic structure. In this example some available options are mentioned. For examples and feature specific settings take a look at the sections below.

### Full Part

```javascript
"exampleFeature": {
    "otherModels": {
      "isSupported": true,
      "commandIDs": [
        "getNPMPlugins"
      ],
      "getNPMPluginsSettings": {
        "shellType": "single",
        "shellNumber": 1,
        "timeout": 25,
        "command": "my favourite command",
	"commandAddons": ["SIGQ"], 
	"customShellNewReadyLineDetection": "root@",
        "customOutputStart": "task is starting...",
        "customOutputStop": "finished task",
        "successPossibilitys": [
          "installation completed",
          "packages updated"
        ],
        "errorPossibilitys": {
          "error while unpacking stuff": "During example feature the execution of my favourite command produced a error."
        },
        "showLogAfterError": true,
        "analyze": [
          {
            "filter": {
              "mode": "line",
              "start": "{",
              "stop": "}",
              "includeFilterMarkers": true,
              "removeWhitespaces": false,
              "removeLines": false
            },
            "extract": {
              "repeat": "all",
              "extractFrom": "line",
              "values": {
                "someGreatData": "standardoutputVALUEafteroutput",
                "someGreatDataLineRequirements": [
                  "├",
                  "@"
                ],
                "someImportantVersion": "version: VALUE "
              }
            },
            "check": {
              "checkFrom": "nextPosition",
              "values": {
                "deactivatedLineRequirements": [
                  "├",
                  "@"
                ],
                "deactivated": "disabled"
              }
            }
          },
          {
            "filter": {
              "mode": "character",
              "start": "some start point",
              "stop": "some end point",
              "includeFilterMarkers": false,
              "removeWhitespaces": true,
              "removeLines": false
            },
            "extract": {
              "repeat": "5",
              "extractFrom": "nextPosition",
              "values": {
                "value5times": "homebridge-VALUE@"
              }
            },
            "analyze": [
              {
                "extract": {
                  "repeat": "all",
                  "extractFrom": "line",
                  "values": {
                    "someGreatData": "standardoutputVALUEafteroutput",
                    "someGreatDataLineRequirements": [
                      "├",
                      "@"
                    ],
                    "someImportantVersion": "version: VALUE "
                  }
                }
              }
            ]
          }
        ],
        "instantReactions": {
          "press y to continue": "y"
        }
        
      }
    }
  }
```


```javascript
"otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 1,
      "showLogAfterError": true,
      "commandIDs": [
        "getNPMPlugins"
      ],
      "getNPMPluginsSettings": {}
    }
```

Initialize an dictionary inside the feature dictionary for specific models or all models. Use the key `otherModels` if for interactions which will be performed for every selected model. Use your custom modelID from the modelSettings array to seperate the interactions by models.

| Key      | Description  |
| -------- | ---------    |
| isSupported     | Bool Value. If set to true the feature is enabled in the app. Otherwise the user get the feature not displayed      |
| shellType     | Currently only `single` is supported. Use `single` for normal shell interactions and `multi`for not ending interactions with not specific commands (e.g. terminal).     |
| shellNumber     | Number value. Use shell `1` for interactions which can be done in a few seconds, for example getting the status of instances or config files. Use shell `2` for interactions which will be perforemd over a longer period of time, for example requesting the plugins or installations and configurations.     |
| showLogAfterError     | Bool value. If set to true after an error the app presents an error to the user and asks him, if he want see the shell output (can be shared).     |
| commandIDs     | Array of string, ever string represents a identifer for every command. This identifer is used for the following settings part.     |
| YOURIDSettings     | See below for details.      |


```javascript
"getNPMPluginsSettings": {
        "timeout": 25,
        "command": "my favourite command",
	"commandAddons": ["SIGQ"], 
	"customShellNewReadyLineDetection": "root@",
        "customOutputStart": "task is starting...",
        "customOutputStop": "finished task",
        "successPossibilitys": [
          "installation completed",
          "packages updated"
        ],
        "errorPossibilitys": {
          "error while unpacking stuff": "During example feature the execution of my favourite command produced a error."
        },
        "analyze": [],
        "instantReactions": {
          "press y to continue": "y"
        }
      }
```

| Key      | Description  |
| -------- | ---------    |
| timeout     | Number value. The number in seconds for the command timeout. If the command has no result after this time, status timeout will be reported.     |
| command     | The command, which will be performed in the shell. It can be static without changes and also dynamic. Dynamic commands are feature specific. Every feature has its own provided dynamic values, which can be used in the command. Every dynamic value starts with `HMIV` and ends with the provided value key, e.g. `HMIVinstancePath` .   |
| commandAddons     | Array value. If a command needs a special interaction before the next shell interaction possibility is ready, define strings in this array, which will be send after the normal \r of every command.     |
| customShellNewReadyLineDetection     | If you used a command before which for example switched to user root, than the new ready shell line is different. Because after every command the shell is checking itself, you have to define the new shell detection for a successful check. The value will only be used at the beginning of the command, not to detect if it ends. Use therefore the `customOutputStop` definition.     |
| customOutputStart     | Optional. You can enter a string which defines when the shell output should be recorded. Even if this key is defined, the ouput before this string can be added, because its just a check if the current shell part is the needed one. If not defined the output is recorded after the command.      |
| customOutputStop     | Optional. Same as `customOutputStart`, but from the other side. Output will be stopped by default after a new detected shell interaction possibility.     |
| successPossibilitys     | Optional. Array of strings. Every string represents a indicator wherever the command succeeded. If not set, default success is if a new shell interaction possibility is available (Not in case of an error).     |
| errorPossibilitys     | Optional. Dictionary of strings. Use the key for the value which can be found in the shell output and the value for the error description displayed in the app.    |
| analyze     | Optional. Array of analyze objects. Use it for filtering, extracting values and checking for strings in the output. For details see below.     |
| instantReactions     | Optional. Dictionary. Similar to the global instant reactions, use it for command specific reactions.     |



```javascript
"analyze": [
    {
      "filter": {},
      "extract": {},
      "check": {},
      "analyze": [
        {
          "filter": {},
          "extract": {},
          "check": {}
        },
        {
          "filter": {},
          "extract": {},
          "check": {}
        }
      ]
    },
    {
      "filter": {},
      "extract": {},
      "check": {}
    }
  ]
```

Objects in `analyze` are unlimited.
Every `analyze` object can contain another `analyze` array. 
Every object uses as root filter string the last filtered string before the `analyze` array.

| Key      | Description  |
| -------- | ---------    |
| filter     | Optional. Dictionary to filter the previous level output string. See below.    |
| extract     | Optional. Dictionary to extract values from the filterd output string.     |
| check     | Optional. Dictionary to check the filtered output string for specific values.     |


```javascript
"filter": {
              "mode": "line",
              "start": "{",
              "stop": "}",
              "includeFilterMarkers": true,
              "removeWhitespaces": false,
              "removeLines": false
            }
```

| Key      | Description  |
| -------- | ---------    |
| mode     | Can be `line` or `character`. `line` means the output start and end points are the full line. `character` means the output will start with the first character and ends with the last character / string.     |
| start     | Start string     |
| stop     | Stop string     |
| includeFilterMarkers     | Bool value. If set to true, in mode `line` the line containing the start or stop string will be included. In mode `character` the character / string will be included. If set to false, it will use only the line after, or the characters / string after the filter.     |
| removeWhitespaces     | Bool value. Will be added after the above filter. If set to true, every whitespaces will be removed.     |
| removeLines     | Bool value. Will be added after the above filter. If set to true, every line will be removed.     |



```javascript
"extract": {
              "repeat": "all",
              "extractFrom": "line",
              "values": {
                "someGreatData": "standardoutputVALUEafteroutput",
                "someGreatDataLineRequirements": [
                  "├",
                  "@"
                ],
                "someImportantVersion": "version: VALUE "
              }
            }
```

| Key      | Description  |
| -------- | ---------    |
| repeat     | Can be `single`, `all` or a number as string. Use this to define how many times values can be extracted.     |
| extractFrom     | Can be `line` or `nextPosition`. `line` means every value will be only extracted from one line. `nextPosition` means every next value will be extracted.    |
| values     | Dictionary. See below.     |

You can add unlimited values to the values dictionary. Use as key a identifer without space, this identifer will be used to pass the value to the specific feature, for details please see the description of every feature.
The value part describes where to find your `VALUE`. 
If `extractFrom` is set to `line` you have also the possibility to add requirements for a line. Do this by defining a array with your value identifer and `LineRequirements`. Every element in the array has to be in the output for success.


```javascript
"check": {
              "checkFrom": "line",
              "values": {
                "deactivatedLineRequirements": [
                  "├",
                  "@"
                ],
                "deactivated": "disabled"
              }
            }
```

| Key      | Description  |
| -------- | ---------    |
| checkFrom     | Can be `line` or `nextPosition`. Tip: use line if you need requirements to make sure the check will not check the wrong data. Otherwise you can just use nextPosition.    |
| values     | Dictionary. See below.     |

You can add unlimited values to the values dictionary. Use as key a identifer without space, this identifer will be used to pass the value to the specific feature, for details please see the description of every feature.
The value part describes which strings has to be in the output to return a positive check.
If `extractFrom` is set to `line` you have also the possibility to add requirements for a line. Do this by defining a array with your value identifer and `LineRequirements`. Every element in the array has to be in the output for success.





# Feature-Implementations

## Plugins

### Get Plugins
This implementation is needed to request the homebridge plugins. 

Pass the plugin code (name excluding homebridge-) with `plugin` and the version with `version` inside the `extract` part.

`showLogAfterError` is not supported.
Use `shellNumber` = 2 because plugin requests are not that fast.

### Example for Homemanager installation

```javascript
"getPlugins": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 2,
      "showLogAfterError": false,
      "commandIDs": [
        "getNPMPlugins"
      ],
      "getNPMPluginsSettings": {
        "timeout": 25,
        "command": "sudo npm list -g --depth=0",
        "successPossibilitys": [
          "homebridge"
        ],
        "analyze": [
          {
            "extract": {
              "repeat": "all",
              "extractFrom": "line",
              "values": {
                "plugin": "homebridge-VALUE@",
                "pluginLineRequirements": [
                  "├",
                  "@",
                  "homebridge-"
                ],
                "version": "@VALUE",
                "versionLineRequirements": [
                  "├",
                  "@",
                  "homebridge-"
                ]
              }
            }
          }
        ]
      }
    }
  }
```

### Install Plugin
This implementation is needed to request an installation of a plugin. 

It is neccessary to define a success value. Other values are not required.

`showLogAfterError` is supported and recommended.
Use `shellNumber` = 2 because plugin installations are not that fast.

Use `HMIVpluginInstallCommand` in the command to get the user entered command. If needed, add something before or after this key in the command string.

The timeout is set to a high number, because plugins could be installed slowly with bad internet connections.


### Example for Homemanager installation

```javascript
"installPlugin": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 2,
      "showLogAfterError": true,
      "commandIDs": [
        "installPluginCmd"
      ],
      "installPluginCmdSettings": {
        "timeout": 300,
        "command": "HMIVpluginInstallCommand",
        "successPossibilitys": [
          "added"
        ],
        "errorPossibilitys": {
          "404 Not Found": "Plugin not found. Check your name.",
          "code EACCES": "The user has no permission to install the plugin.",
          "ERR!": "General error during installation, please check the log or contact the support."
        }
      }
    }
  }
```

### Uninstall Plugin
This implementation is needed to request an uninstallation of a plugin. 

`showLogAfterError` is supported and recommended.
Use `shellNumber` = 2 because plugin installations are not that fast.

Use `HMIVpluginUninstallCommand` in the command to get the user entered command. If needed, add something before or after this key in the command string.

The timeout is set to a high number, because plugins could be uninstalled slowly with bad internet connections.


### Example for Homemanager installation

```javascript
"uninstallPlugin": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 2,
      "showLogAfterError": true,
      "commandIDs": [
        "uninstallPluginCmd"
      ],
      "uninstallPluginCmdSettings": {
        "timeout": 300,
        "command": "HMIVpluginUninstallCommand",
        "successPossibilitys": [
          "removed"
        ],
        "errorPossibilitys": {
          "404 Not Found": "Plugin not found. Check your name.",
          "code EACCES": "The user has no permission to uninstall the plugin.",
          "ERR!": "General error during uninstallation, please check the log or contact the support."
        }
      }
    }
  }
```

## Instance Features

First of all define an array in the main object which contains the the supported instance types, e.g. systemd, initd, launchd or docker

### Example

```javascript
"instanceTypes": [
    "systemd"
  ]
```
Now all upcomming instance related features will be defined starting with the feature name and followed by the instance type. If you support different types, you have to define every feature for every type.


### Get instance Status

This feature exists for getting the current status of a homebridge instance. 
To request the status for the specific instance, the value `HMIVinstanceName` can be used in the command.

Beacuse some interactions have to be quit before the shell can be used again, make sure that you use in this case the `commandAddons` with for example `SIGQ` to quit the action.

`showLogAfterError` is not supported by this feature.

If the autostart system provides a date of the last change, make sure that you define the `featureSettings` as shown below. If `timeStampPreperation` is activated, everything excluding numbers, "-" and ";" will be removed from the `timeStamp` string. This makes it easier to define the correct `timeStampFormat`.

To extract a time stamp use the value key `timeStamp`.

Not all autostart systems provide the following status types, so if your system not provide one of the statuses, just dont use this one in the analyze check section.

Use `restart` to indicate whether the instance is restarting (error loop)
Use `running` to indicate whether the instance is ready for use in HomeKit
Use `stopped` to indicate whether the instance is stopped (if possibile, only if stopped manualy by user)
Use `inactive` to indicate whether the instance isn´t ready to use (if possible, only if stopped without user interaction)
Use `activated` to indicate whether the autostart is activated (only for supported systems)
Use `deactivated` to indicate whether the autostart is deactivated (only for supported systems)


Mark: Because of special vanilla shell formatting the string is maybe different at the beginning or end, not as in desktop terminals. This is why i used for `running` `mactive`. `active` alone could also be `inactive`. Because the beginning includes a m in the special shell version, i choosed this way. You can check the output in the app´s log file if `logShell` is set to `true`.

### Example systemd

```javascript
"getInstanceStatussystemd": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 1,
      "showLogAfterError": false,
      "commandIDs": [
        "getStatus"
      ],
      "getStatusSettings": {
        "timeout": 5,
        "command": "sudo systemctl status HMIVinstanceName",
        "commandAddons": [
          "SIGQ"
        ],
        "successPossibilitys": [
          "PID",
          "(dead)"
        ],
        "analyze": [
          {
            "extract": {
              "repeat": "single",
              "extractFrom": "line",
              "values": {
                "timeStamp": "since VALUE;",
                "timeStampLineRequirements": [
                  "Active:"
                ]
              }
            },
            "check": {
              "checkFrom": "line",
              "values": {
                "restartLineRequirements": [
                  "Active:"
                ],
                "restart": "activating",
                "runningLineRequirements": [
                  "Active:"
                ],
                "running": "mactive",
                "stoppedLineRequirements": [
                  "Active:"
                ],
                "stopped": "failed",
                "inactiveLineRequirements": [
                  "Active:"
                ],
                "inactive": "inactive",
                "activatedLineRequirements": [
                  "Loaded:"
                ],
                "activated": "enabled",
                "deactivatedLineRequirements": [
                  "Loaded:"
                ],
                "deactivated": "disabled"
              }
            }
          }
        ]
      },
      "featureSettings": {
        "timeStampPreperation": true,
        "timeStampFormat": "yyyy-MM-ddHH:mm:ss"
      }
    }
  }
```

## Change Instance Status

This feature represents the interaction of the instance control buttons. To request the status change for the specific instance, the value `HMIVinstanceName` can be used in the command.

`showLogAfterError` is not supported by this feature.

If some interaction types are not supported for a instance type, just dont define them here in the file.

Use the following keys between `changeInstanceStatus` and the instance type:

Use `restarting` to restart the instance
Use `stopping` to stop the instance, but not to remove the autostart
Use `activating` to enable the autostart if the system supports it
Use `deactivating` to disable the autostart if the system supports it, this should not stop the instance itself.

### Example Stopping


```javascript
"changeInstanceStatusstoppingsystemd": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 1,
      "showLogAfterError": false,
      "commandIDs": [
        "changeStatus"
      ],
      "changeStatusSettings": {
        "timeout": 5,
        "command": "sudo systemctl stop HMIVinstanceName"
      },
      "featureSettings": {
        "timeBeforeRefresh": 5
      }
    }
  }
```

### Example Restarting

```javascript
 "changeInstanceStatusrestartingsystemd": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 1,
      "showLogAfterError": false,
      "commandIDs": [
        "changeStatus"
      ],
      "changeStatusSettings": {
        "timeout": 5,
        "command": "sudo systemctl restart HMIVinstanceName"
      },
      "featureSettings": {
        "timeBeforeRefresh": 5
      }
    }
  }
```

### Example Activating

```javascript
 "changeInstanceStatusactivatingsystemd": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 1,
      "showLogAfterError": false,
      "commandIDs": [
        "changeStatus"
      ],
      "changeStatusSettings": {
        "timeout": 5,
        "command": "sudo systemctl enable HMIVinstanceName"
      },
      "featureSettings": {
        "timeBeforeRefresh": 5
      }
    }
  }
```

### Example Deactivating


```javascript
"changeInstanceStatusdeactivatingsystemd": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 1,
      "showLogAfterError": false,
      "commandIDs": [
        "changeStatus"
      ],
      "changeStatusSettings": {
        "timeout": 5,
        "command": "sudo systemctl disable HMIVinstanceName"
      },
      "featureSettings": {
        "timeBeforeRefresh": 5
      }
    }
  }
```

## Instance Service File Interactions

Users can edit service related files under the instance settings tab. This feature is split into set and get. 

First of all you need one array for every instance type you support, beginning with the name `instanceServiceFileTypes` and ending with the instance type.

If you support the instance type `systemd` you should also integrate the service file types `.Service` and `Service Default` because the app will show up a graphic mask with the most relevant settings if these types are supported.

Every set interaction is starting with `getInstanceServiceFile` followed by one of the keys of the `instanceServiceFileType` array and ends with the instance type.

The following examples show the interaction with the instance type `systemd` for both service file types.

The string of the file has to be transfered via the key `fileString`
You can use `HMIVinstanceName` for get interactions and in set interactions also the `HMIVfileString` which offers you the content of the file

`showLogAfterError` is not supported by this feature.


### Example Instance Service File Types

```javascript
"instanceServiceFileTypessystemd": [
    ".Service",
    "Service Default"
  ]
```

### Example Get .Service

```javascript
"getInstanceServiceFile.Servicesystemd": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 1,
      "showLogAfterError": false,
      "commandIDs": [
        "getFileString"
      ],
      "getFileStringSettings": {
        "timeout": 8,
        "command": "sudo cat /etc/systemd/system/HMIVinstanceName.service",
        "successPossibilitys": [
          "[Install]"
        ],
        "analyze": [
          {
            "filter": {
              "mode": "line",
              "start": "sudo cat /etc/systemd/",
              "stop": ":~",
              "includeFilterMarkers": false,
              "removeWhitespaces": false,
              "removeLines": false
            },
            "extract": {
              "repeat": "single",
              "extractFrom": "nextPosition",
              "values": {
                "fileString": "VALUE"
              }
            }
          }
        ]
      }
    }
  }
```

### Example Get Service Default

```javascript
"getInstanceServiceFileService Defaultsystemd": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 1,
      "showLogAfterError": false,
      "commandIDs": [
        "getFileString"
      ],
      "getFileStringSettings": {
        "timeout": 8,
        "command": "sudo cat /etc/default/HMIVinstanceName",
        "successPossibilitys": [
          "HOMEBRIDGE_OPTS"
        ],
        "analyze": [
          {
            "filter": {
              "mode": "line",
              "start": "sudo cat /etc/default/",
              "stop": ":~",
              "includeFilterMarkers": false,
              "removeWhitespaces": false,
              "removeLines": false
            },
            "extract": {
              "repeat": "single",
              "extractFrom": "nextPosition",
              "values": {
                "fileString": "VALUE"
              }
            }
          }
        ]
      }
    }
  }
```

### Example Set .Service

```javascript
"setInstanceServiceFile.Servicesystemd": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 1,
      "showLogAfterError": false,
      "commandIDs": [
        "switchToRoot",
        "stopInstance",
        "replaceFileString",
        "daemon-reload",
        "restartInstance",
        "switchToNormalUser"
      ],
      "switchToRootSettings": {
        "timeout": 5,
        "command": "sudo su",
        "customOutputStop": "root@"
      },
      "stopInstanceSettings": {
        "timeout": 5,
        "command": "sudo systemctl stop HMIVinstanceName",
        "customOutputStop": "root@",
        "customShellNewReadyLineDetection": "root@"
      },
      "replaceFileStringSettings": {
        "timeout": 8,
        "command": "sudo echo 'HMIVfileString' > /etc/systemd/system/HMIVinstanceName.service",
        "customOutputStop": "root@",
        "customShellNewReadyLineDetection": "root@"
      },
      "daemon-reloadSettings": {
        "timeout": 5,
        "command": "sudo systemctl daemon-reload",
        "customOutputStop": "root@",
        "customShellNewReadyLineDetection": "root@"
      },
      "restartInstanceSettings": {
        "timeout": 5,
        "command": "sudo systemctl restart HMIVinstanceName",
        "customOutputStop": "root@",
        "customShellNewReadyLineDetection": "root@"
      },
      "switchToNormalUserSettings": {
        "timeout": 5,
        "command": "su ADConnUsrn",
        "customShellNewReadyLineDetection": "root@"
      }
    }
  }
```
Mark: We change file strings through linux commands not by uploading the file itself. To provide permission problems, we do these command as root. You have to switch back to the normal connection user after all needed commands have been sent.


### Example Set Service Default

```javascript
"setInstanceServiceFileService Defaultsystemd": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 1,
      "showLogAfterError": false,
      "commandIDs": [
        "switchToRoot",
        "stopInstance",
        "replaceFileString",
        "daemon-reload",
        "restartInstance",
        "switchToNormalUser"
      ],
      "switchToRootSettings": {
        "timeout": 5,
        "command": "sudo su",
        "customOutputStop": "root@"
      },
      "stopInstanceSettings": {
        "timeout": 5,
        "command": "sudo systemctl stop HMIVinstanceName",
        "customOutputStop": "root@",
        "customShellNewReadyLineDetection": "root@"
      },
      "replaceFileStringSettings": {
        "timeout": 8,
        "command": "sudo echo 'HMIVfileString' > /etc/default/HMIVinstanceName",
        "customOutputStop": "root@",
        "customShellNewReadyLineDetection": "root@"
      },
      "daemon-reloadSettings": {
        "timeout": 5,
        "command": "sudo systemctl daemon-reload",
        "customOutputStop": "root@",
        "customShellNewReadyLineDetection": "root@"
      },
      "restartInstanceSettings": {
        "timeout": 5,
        "command": "sudo systemctl restart HMIVinstanceName",
        "customOutputStop": "root@",
        "customShellNewReadyLineDetection": "root@"
      },
      "switchToNormalUserSettings": {
        "timeout": 5,
        "command": "su ADConnUsrn",
        "customShellNewReadyLineDetection": "root@"
      }
    }
  }
```

## Get the instance config

Every time the app needs the homebridge config of a instance, this feature will be called.

The string of the file has to be transfered via the key `fileString`

`HMIVinstancePath` provides the path to the instance. (always ending with /)

This feature is not customizable for every instance type, because it uses a instance related path to get the data.

`showLogAfterError` is not supported by this feature.

See the example for details.

### Example

```javascript
"getInstanceConfig": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 1,
      "showLogAfterError": false,
      "commandIDs": [
        "getFileString"
      ],
      "getFileStringSettings": {
        "timeout": 8,
        "command": "sudo cat HMIVinstancePathconfig.json",
        "analyze": [
          {
            "filter": {
              "mode": "line",
              "start": "sudo cat",
              "stop": ":~",
              "includeFilterMarkers": false,
              "removeWhitespaces": false,
              "removeLines": false
            },
            "extract": {
              "repeat": "single",
              "extractFrom": "nextPosition",
              "values": {
                "fileString": "VALUE"
              }
            }
          }
        ]
      }
    }
  }
```

## Set the instance config

Every time the app saves the homebridge config of a instance, this feature will be called.

Before the interation the stopping feature is called and afterwards the restarting feature is called.

`HMIVfileString` provieds the string of the config.

`HMIVinstancePath` provides the path to the instance. (always ending with /)

`HMIVinstanceName` provides the name of the instance.

This feature is not customizable for every instance type, because it uses a instance related path to get the data.

`showLogAfterError` is not supported by this feature.

See the example for details.

### Example

```javascript
"setInstanceConfig": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 1,
      "showLogAfterError": false,
      "commandIDs": [
        "switchToRoot",
        "replaceFileString",
        "switchToNormalUser"
      ],
      "switchToRootSettings": {
        "timeout": 5,
        "command": "sudo su",
        "customOutputStop": "root@"
      },
      "replaceFileStringSettings": {
        "timeout": 8,
        "command": "sudo echo 'HMIVfileString' > HMIVinstancePathconfig.json",
        "customOutputStop": "root@",
        "customShellNewReadyLineDetection": "root@"
      },
      "switchToNormalUserSettings": {
        "timeout": 5,
        "command": "su ADConnUsrn",
        "customShellNewReadyLineDetection": "root@"
      }
    }
  }
  
  
```
Mark: We change file strings through linux commands not by uploading the file itself. To provide permission problems, we do these command as root. You have to switch back to the normal connection user after all needed commands have been sent.


## Add existing instances

The user can add existing instances. 
Every instanceType defined in the `instanceTypes` array, can be selected.

In a `featureSettings` dictionary you have to define the `instanceTypeDefaultSelection` and for every instance type the dictionarys `normalInstancePresetsYOURTYPE` and `appInstallInstancePresetsYOURTYPE`.

Both must include `instanceDisplayName`, `instanceName` and `instancePath`.

Please take a look at the example!

### Example

```javascript
"addExistingInstance": {
    "otherModels": {
      "isSupported": true,
      "featureSettings": {
        "instanceTypeDefaultSelection": "systemd",
        "normalInstancePresetssystemd": {
          "instanceDisplayName": "",
          "instanceName": "homebridge-",
          "instancePath": "/var/homebridge-"
        },
        "appInstallInstancePresetssystemd": {
          "instanceDisplayName": "Homebridge",
          "instanceName": "homebridge",
          "instancePath": "/var/homebridge/"
        }
      }
    }
  }
```

## Add existing devices (Presets)

This dictionary is used whether the app is configured for the specific app mode.

It must include `displayName`, `connectionUsername` and `port` (uInt16).

Please take a look at the example!

### Example

```javascript
"addExistingDevicePresets": {
    "displayName" : "HM Pi",
    "connectionUsername": "pi",
    "port": 22
  }
```

## Set new Localization

This feature changes the localtime and timezone of supported devices.

Provided is the timezone with `HMIVtimezone` in a format as "Europe/Berlin"


Please take a look at the example!

### Example

```javascript
"setNewLocalizaion": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 2,
      "showLogAfterError": false,
      "commandIDs": [
        "removeTimezone",
        "setTimezone",
        "removeLocaltime",
        "setLocaltime"
      ],
      "removeTimezoneSettings": {
        "timeout": 8,
        "command": "sudo rm /etc/timezone"
      },
      "setTimezoneSettings": {
        "timeout": 12,
        "command": "echo 'HMIVtimezone' | sudo tee /etc/timezone"
      },
      "removeLocaltimeSettings": {
        "timeout": 8,
        "command": "sudo rm /etc/localtime"
      },
      "setLocaltimeSettings": {
        "timeout": 12,
        "command": "sudo cp /usr/share/zoneinfo/HMIVtimezone /etc/localtime"
      }
    }
  }
```

## Reboot Server

This feature changes reboots the server. It is used internaly in other features and can be used in the interface soon.

Please take a look at the example!

### Example

```javascript
  "rebootServer": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 1,
      "showLogAfterError": false,
      "commandIDs": [
        "command"
      ],
      "commandSettings": {
        "timeout": 3,
        "command": "sudo reboot"
      }
    }
  }
```


# Homemanager private special functionality (Not completely dynamic, allows only less changes) 

## Add a new instance

### Presets example

```javascript
"createNewInstance": {
    "otherModels": {
      "isSupported": true,
      "featureSettings": {
        "wantNewUser": false,
        "addUserCommand": "",
        "userPermPath": "",
        "userPerm": "",
        "username": "homebridge",
        "instanceName": "",
        "instancePath": "",
        "config": {
          "bridge": {
            "name": "",
            "username": "",
            "port": 1,
            "pin": "031-45-154"
          },
          "description": "Added and managed with Homemanager App for Homebridge",
          "platforms": [],
          "accessories": []
        },
        "serviceString": "",
        "serviceDefaultString": "",
        "displayName": ""
      }
    }
  }
```

### User example

```javascript
"createNewInstanceCreateUser": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 2,
      "showLogAfterError": false,
      "commandIDs": [
        "addUser",
        "userCreatePerm",
        "userInsertPerm"
      ],
      "addUserSettings": {
        "timeout": 8,
        "command": "HMIVaddUserCommand"
      },
      "userCreatePermSettings": {
        "timeout": 8,
        "command": "sudo touch HMIVuserPermPath"
      },
      "userInsertPermSettings": {
        "timeout": 8,
        "command": "sudo ex -sc '1i|HMIVuserPerm' -cx HMIVuserPermPath"
      }
    }
  }
```

### Main part example

```javascript
"createNewInstanceMainPart": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 2,
      "showLogAfterError": false,
      "commandIDs": [
        "createInstancePath",
        "createConfig",
        "insertConfig",
        "chownToUser",
        "createService",
        "insertService",
        "createServiceDefault",
        "insertServiceDefault",
        "daemonReload",
        "enableService",
        "startInstance"
      ],
      "createInstancePathSettings": {
        "timeout": 8,
        "command": "sudo mkdir -p HMIVinstancePath"
      },
      "createConfigSettings": {
        "timeout": 8,
        "command": "sudo touch HMIVinstancePathconfig.json"
      },
      "insertConfigSettings": {
        "timeout": 8,
        "command": "sudo ex -sc '1i|HMIVconfig' -cx HMIVinstancePathconfig.json"
      },
      "chownToUserSettings": {
        "timeout": 8,
        "command": "sudo chown -R HMIVusername:HMIVusername HMIVpathWithoutSlash"
      },
      "createServiceSettings": {
        "timeout": 8,
        "command": "sudo touch /etc/systemd/system/HMIVinstanceName.service"
      },
      "insertServiceSettings": {
        "timeout": 8,
        "command": "sudo ex -sc '1i|HMIVserviceString' -cx /etc/systemd/system/HMIVinstanceName.service"
      },
      "createServiceDefaultSettings": {
        "timeout": 8,
        "command": "sudo touch /etc/default/HMIVinstanceName"
      },
      "insertServiceDefaultSettings": {
        "timeout": 8,
        "command": "sudo ex -sc '1i|HMIVserviceDefaultString' -cx /etc/default/HMIVinstanceName"
      },
      "daemonReloadSettings": {
        "timeout": 8,
        "command": "sudo systemctl daemon-reload"
      },
      "enableServiceSettings": {
        "timeout": 8,
        "command": "sudo systemctl enable HMIVinstanceName",
        "successPossibilitys": [
          "Created symlink"
        ]
      },
      "startInstanceSettings": {
        "timeout": 8,
        "command": "sudo systemctl restart HMIVinstanceName"
      }
    }
  }
```

## Main Installation

### Full homemanager installation process example

```javascript
"mainInstallation": {
    "otherModels": {
      "isSupported": true,
      "featureSettings": {
        "wantNewUser": true,
        "addUserCommand": "sudo useradd -m -c 'Homebridge Service' -s /bin/bash -G audio,bluetooth,dialout,gpio,video homebridge",
        "userPermPath": "/etc/sudoers.d/homebridge",
        "userPerm": "homebridge ALL=(root) SETENV:NOPASSWD: /usr/local/bin/npm, /bin/systemctl restart homebridge, /bin/journalctl, /usr/local/bin/node",
        "username": "homebridge",
        "instanceName": "homebridge",
        "instancePath": "/var/homebridge/",
        "config": {
          "bridge": {
            "name": "Homebridge",
            "username": "",
            "port": 1,
            "pin": "031-45-154"
          },
          "description": "Added and managed with Homemanager App for Homebridge",
          "platforms": [],
          "accessories": []
        },
        "serviceString": "",
        "serviceDefaultString": "",
        "restartSeconds": "10",
        "insecureMode": true,
        "displayName": "Homebridge",
        "connectionUsername": "pi",
        "connectionPort": 22,
        "nodeVersion": "",
        "homebridgeInstallationCommand": "sudo npm install -g --unsafe-perm homebridge",
        "demoPiTemp": true
      }
    }
  },
  "mIsetNewHostname": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "command"
      ],
      "commandSettings": {
        "timeout": 30,
        "command": "sudo raspi-config nonint do_hostname HMIVnewHostname"
      }
    }
  },
  "mIsetNewPassword": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "command"
      ],
      "commandSettings": {
        "timeout": 30,
        "command": "(echo 'HMIVactivePassword' ; echo 'HMIVnewPassword' ; echo 'HMIVnewPassword') | passwd",
        "successPossibilitys": [
          "password updated successfully"
        ]
      }
    }
  },
  "mIperformUpdate": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "command"
      ],
      "commandSettings": {
        "timeout": 1200,
        "command": "sudo apt-get update",
        "successPossibilitys": [
          "Reading package lists... Done"
        ]
      }
    }
  },
  "mIperformUpgrade": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "command"
      ],
      "commandSettings": {
        "timeout": 3600,
        "command": "sudo apt-get upgrade"
      }
    }
  },
  "mIsetNewTimezone": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "remove",
        "set"
      ],
      "removeSettings": {
        "timeout": 30,
        "command": "sudo rm /etc/timezone"
      },
      "setSettings": {
        "timeout": 30,
        "command": "echo 'HMIVtimezone' | sudo tee /etc/timezone"
      }
    }
  },
  "mIsetNewLocalTime": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "remove",
        "set"
      ],
      "removeSettings": {
        "timeout": 30,
        "command": "sudo rm /etc/localtime"
      },
      "setSettings": {
        "timeout": 30,
        "command": "sudo cp /usr/share/zoneinfo/HMIVtimezone /etc/localtime"
      }
    }
  },
  "mIinstallAvahi": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "command"
      ],
      "commandSettings": {
        "timeout": 1200,
        "command": "sudo apt-get install libavahi-compat-libdnssd-dev"
      }
    }
  },
  "mIcheckARM": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "command"
      ],
      "commandSettings": {
        "timeout": 30,
        "command": "uname -m",
        "analyze": [
          {
            "check": {
              "checkFrom": "nextPosition",
              "values": {
                "armv7l": "armv7l"
              }
            }
          }
        ]
      }
    }
  },
  "mIinstallNode": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "get",
        "unzip",
        "move"
      ],
      "getSettings": {
        "timeout": 600,
        "command": "wget https://nodejs.org/dist/vHMIVnodeVersion/node-vHMIVnodeVersion-linux-HMIVarmType.tar.gz",
        "errorPossibilitys": {
          "ERROR": "Node error"
        },
        "successPossibilitys": [
          "’ saved"
        ]
      },
      "unzipSettings": {
        "timeout": 180,
        "command": "tar xf node-vHMIVnodeVersion-linux-HMIVarmType.tar.gz"
      },
      "moveSettings": {
        "timeout": 180,
        "command": "sudo cp -R node-vHMIVnodeVersion-linux-HMIVarmType/* /usr/local/"
      }
    }
  },
  "mIcheckNodeInstallation": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "command"
      ],
      "commandSettings": {
        "timeout": 30,
        "command": "node -v",
        "analyze": [
          {
            "filter": {
              "mode": "line",
              "start": "node",
              "stop": ":~",
              "includeFilterMarkers": false,
              "removeWhitespaces": false,
              "removeLines": false
            },
            "extract": {
              "repeat": "single",
              "extractFrom": "nextPosition",
              "values": {
                "outputString": "VALUE"
              }
            }
          }
        ]
      }
    }
  },
  "mIaddHomebridgeUser": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "add",
        "createPath",
        "sendPerm"
      ],
      "addSettings": {
        "timeout": 30,
        "command": "HMIVaddUserCommand"
      },
      "createPathSettings": {
        "timeout": 30,
        "command": "sudo touch HMIVuserPermPath"
      },
      "sendPermSettings": {
        "timeout": 30,
        "command": "sudo ex -sc '1i|HMIVuserPerm' -cx HMIVuserPermPath"
      }
    }
  },
  "mIinstallHomebridge": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "command"
      ],
      "commandSettings": {
        "timeout": 900,
        "command": "HMIVhomebridgeInstallationCommand"
      }
    }
  },
  "mIcheckHomebridgeInstallation": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "command"
      ],
      "commandSettings": {
        "timeout": 30,
        "command": "sudo npm view homebridge version",
        "analyze": [
          {
            "filter": {
              "mode": "line",
              "start": "npm view",
              "stop": ":~",
              "includeFilterMarkers": false,
              "removeWhitespaces": false,
              "removeLines": false
            },
            "extract": {
              "repeat": "single",
              "extractFrom": "nextPosition",
              "values": {
                "outputString": "VALUE"
              }
            }
          }
        ]
      }
    }
  },
  "mIinstallDemoPlugin": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "command"
      ],
      "commandSettings": {
        "timeout": 240,
        "command": "sudo npm install -g --unsafe-perm homebridge-raspberrypi-temperature"
      }
    }
  },
  "mIprepareHBConfiguration": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "createPath",
        "createConfig",
        "insertConfig",
        "chown"
      ],
      "createPathSettings": {
        "timeout": 30,
        "command": "sudo mkdir -p HMIVinstancePath"
      },
      "createConfigSettings": {
        "timeout": 30,
        "command": "sudo touch HMIVinstancePathconfig.json"
      },
      "insertConfigSettings": {
        "timeout": 30,
        "command": "sudo ex -sc '1i|HMIVconfig' -cx HMIVinstancePathconfig.json"
      },
      "chownSettings": {
        "timeout": 30,
        "command": "sudo chown -R HMIVusername:HMIVusername HMIVpathWithoutSlash"
      }
    }
  },
  "mIprepareServiceConfiguration": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "createDotService",
        "sendDotService",
        "createServiceDefault",
        "sendServiceDefault"
      ],
      "createDotServiceSettings": {
        "timeout": 30,
        "command": "sudo touch /etc/systemd/system/HMIVinstanceName.service"
      },
      "sendDotServiceSettings": {
        "timeout": 30,
        "command": "sudo ex -sc '1i|HMIVserviceString' -cx /etc/systemd/system/HMIVinstanceName.service"
      },
      "createServiceDefaultSettings": {
        "timeout": 30,
        "command": "sudo touch /etc/default/HMIVinstanceName"
      },
      "sendServiceDefaultSettings": {
        "timeout": 30,
        "command": "sudo ex -sc '1i|HMIVserviceDefaultString' -cx /etc/default/HMIVinstanceName"
      }
    }
  },
  "mIfinishUpInstance": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "daemonReload",
        "enableService",
        "startService"
      ],
      "daemonReloadSettings": {
        "timeout": 30,
        "command": "sudo systemctl daemon-reload"
      },
      "enableServiceSettings": {
        "timeout": 30,
        "command": "sudo systemctl enable HMIVinstanceName"
      },
      "startServiceSettings": {
        "timeout": 30,
        "command": "sudo systemctl restart HMIVinstanceName"
      }
    }
  },
  "mIconfigureWifi": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "setWifiCountry",
        "sendWifiData",
        "reconfigureWifiData"
      ],
      "setWifiCountrySettings": {
        "timeout": 30,
        "command": "raspi-config nonint do_wifi_country HMIVwifiRegion"
      },
      "sendWifiDataSettings": {
        "timeout": 30,
        "command": "sudo wpa_passphrase 'HMIVSSID' 'HMIVwifiPassword' | sudo tee -a /etc/wpa_supplicant/wpa_supplicant.conf"
      },
      "reconfigureWifiDataSettings": {
        "timeout": 30,
        "command": "sudo wpa_cli -i wlan0 reconfigure"
      }
    }
  },
  "mIlastReboot": {
    "otherModels": {
      "isSupported": true,
      "shellType": "single",
      "shellNumber": 3,
      "showLogAfterError": false,
      "commandIDs": [
        "command"
      ],
      "commandSettings": {
        "timeout": 10,
        "command": "sudo reboot"
      }
    }
```

## Take a look at the repo for full examples of every mode


## License

Copyright 2019 by Ron Polenthon. Licensed under MIT.

