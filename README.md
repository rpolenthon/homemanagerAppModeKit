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
		"logShell" : false
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


# Model Settings

Enter your own supported models.

Example

```json
"modelSettings": {
		"models": {
			"pi3b+": "Raspberry Pi 3 B+"

		},
		"pi3b+WLANEnabled": true  //Optional, Default: false
}
```

| Key      | Description  |
| -------- | ---------    |
| models     | Dictionary of your supported models. The key represents an identifier for interactions and the value represents the displayed name in the app.    |
| YOURMODELIDWLANEnabled     | Define if this device supports WLAN. If you need other parameters, please contact us.         |




