# Custom Cordova Plugin
---
This guide shows you how to create a custom cordova plugin and bridge it between your native android code and your new or existing Cordova project.

First of all make sure you got everything installed and the latest version that you need to create your own cordova project. (npm, Cordova CLI, etc)

## Cordova
---
### Create a new cordova project

**If you already have an existing cordova project, you can skip this step.**
Create a new cordova project using [CLI](https://cordova.apache.org/docs/en/latest/guide/cli/), from scratch or using your desired IDE e.g. (WebStorm ```new project -> PhoneGap/Cordova App```).

```
$ cordova create LogicLinkApp com.endare.logiclinkplugin
```

### Add the platform to your cordova project

Add the platforms that you want to target your app. We will add the 'android' platform and ensure they get saved to ```config.xml``` and ```package.json```:

```
$ cordova platform add android
```

## Plugman
---
### Install Plugman

Install Plugman globally using the following command:
```
$ npm install -g plugman
```

You can use the ```plugman help``` command to display the help file and documentation about plugman.

### Create a plugin
Navigate to the folder where you want to create your plugin.

```
$ plugman create --name <pluginName> --plugin_id <pluginID> --plugin_version <version> [--path <directory>] [--variable NAME=VALUE]
```

This will create a folder in your specified path with the plugin name you defined.

Example:
```
$ plugman create --name LogicLinkPlugin --plugin_id cordova-plugin-logiclinkplugin --plugin_version 0.0.1
```

### Add the platform to your plugin

This generates a generic template for your plugin for your desired platform.
```
$ plugman platform add --platform_name android
```

### Add a Package.JSON file

Creates a package.json file in the plugin based on values from plugin.xml.

```
 $ plugman createpackagejson <directory>
```

> You can use ```.``` for your current directory.

Here you will need to enter some information about your plugin.

### Install the plugin

Now we install the plugin in our existing Cordova project for our platform.
The code below is specific for adding a plugin to an Cordova-Android project.
```
$ plugman install --platform android --project platforms/android --plugin ../LogicLinkPlugin/
```

**Note!**
This will copy your existing plugin to the src folder of your desired platform.
**Do not edit plugin files in your Cordova Project!**
Whenever you make changes to your plugin, you have to uninstall the plugin using:

```
$ plugman uninstall --platform android --project platforms/android --plugin ../LogicLinkPlugin/
```

and reinstall it with the ```plugman install``` command above.

## Usage
---

The JavaScript interface provides the front-facing interface, making it perhaps the most important part of the plugin. You can structure your plugin's JavaScript however you like, but you need to call ```cordova.exec``` to communicate with the native platform, using the following syntax:

Here is how each parameter works:

 * **function(winParam) {}:** A success callback function. Assuming your exec call completes successfully, this function executes along with any parameters you pass to it.

 * **function(error) {}:** An error callback function. If the operation does not complete successfully, this function executes with an optional error parameter.

 * **service:** The service name to call on the native side. This corresponds to a native class, for which more information is available in the native guides listed below.

 * **action:** The action name to call on the native side. This generally corresponds to the native class method. See the native guides listed below.

 * **[/ arguments /]:** An array of arguments to pass into the native environment.

Go to the ```plugin.xml``` file in your **Plugin project** and look for the value of the ```<clobbers target="value">```. **Copy this value**. This will be used in the Cordova Application to bridge between the Cordova and Android application

In your ```<pluginname>.js``` file, you can write your own methods by extending the ```exports``` function with a new ```exec```.

```js
exports.coolMethod = function(arg0, success, error) {
    exec(success, error, "LogicLinkPlugin", "coolMethod", [arg0]);
};
```
Add the following code to wherever you need to call the plugin functionality:

```
cordova.plugins.<PluginName>.<method>();
```

For example:

```js
function testPlugin() {
    cordova.plugins.LogicLinkPlugin.coolMethod("coolMethod", function(response){
        console.log(response);
    }, function(error){
        console.log(error);
    });
}
```



