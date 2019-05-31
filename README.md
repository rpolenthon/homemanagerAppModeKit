# Homemanager AppModeKit Instructions
Official Homemanger AppModeKit Developer Informations and Instructions

# Introduction
Our app switched from specific SSH interactions for every feature to a powerful generic SSH manager which can be managed with a app specific JSON syntax.
The file set informations for different kinds of content in the App. With this file it is possible to customize a lot of things in the app to adapt your homebridge based system. 


You can test your settings inside the app by yourself. If you want to share your configuration with all the other users, just write us under info@homemanger-app.de and we will add it for you in the App.

# General

Example

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


# Global Constants

You can use the following global constants in your commands.


| Constant      | Description  |
| -------- | ---------    |
| ADConnPass     | The password the app uses for a SHH connection.    |


# Model Settings

Enter your own supported models.

Example

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


# Global Instant Interaction

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


# Example for feature interactions

Every feature interaction is build on the same generic structure. In this example some available options are mentioned. For examples and feature specific settings take a look at the sections below.

Full Part:

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
      "commandIDs": [
        "getNPMPlugins"
      ],
      "getNPMPluginsSettings": {}
    }
```

| Key      | Description  |
| -------- | ---------    |
| isSupported     | Bool Value. If set to true the feature is enabled in the app. Otherwise the user get the feature not displayed      |
| specificModelID     | Use your custom model key for a dictionary if the following interactions should be only used for this model.     |



```javascript
"getNPMPluginsSettings": {
        "shellType": "single",
        "shellNumber": 1,
        "timeout": 25,
        "command": "my favourite command",
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
        "analyze": [],
        "instantReactions": {
          "press y to continue": "y"
        }
      }
```

| Key      | Description  |
| -------- | ---------    |
| shellType     | Currently only `single` is supported. Use `single` for normal shell interactions and `multi`for not ending interactions with not specific commands (e.g. terminal).     |
| shellNumber     | Number value. Use shell `1` for interactions which can be done in a few seconds, for example getting the status of instances or config files. Use shell `2` for interactions which will be perforemd over a longer period of time, for example requesting the plugins or installations and configurations.     |
| timeout     | Number value. The number in seconds for the command timeout. If the command has no result after this time, status timeout will be reported.     |
| command     | The command, which will be performed in the shell. It can be static without changes and also dynamic. Dynamic commands are feature specific. Every feature has its own provided dynamic values, which can be used in the command. Every dynamic value starts with `HMIV` and ends with the provided value key, e.g. `HMIVinstancePath` .   |
| customOutputStart     | Optional. You can enter a string which defines when the shell output should be recorded. Even if this key is defined, the ouput before this string can be added, because its just a check if the current shell part is the needed one. If not defined the output is recorded after the command.      |
| customOutputStop     | Optional. Same as `customOutputStart`, but from the other side. Output will be stopped by default after a new detected shell interaction possibility.     |
| successPossibilitys     | Optional. Array of strings. Every string represents a indicator wherever the command succeeded. If not set, default success is if a new shell interaction possibility is available (Not in case of an error).     |
| errorPossibilitys     | Optional. Dictionary of strings. Use the key for the value which can be found in the shell output and the value for the error description displayed in the app.    |
| showLogAfterError     | Bool value. If set to true after an error the app presents an error to the user and asks him, if he want see the shell output (can be shared).     |
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
              "checkFrom": "nextPosition",
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


## Feature-Implementations

Will be added soon!



## License

Copyright 2019 by Ron Polenthon. Licensed under MIT.

