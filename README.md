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
"exampleFeature": {
    "otherModels": {},
    "specificModelID" : {}
  }
```

| Key      | Description  |
| -------- | ---------    |
| otherModels     | Use this key for a dictionary if the following interactions should be used for all models.     |
| specificModelID     | Use your custom model key for a dictionary if the following interactions should be only used for this model.     |











## License

Copyright 2019 by Ron Polenthon. Licensed under MIT.

