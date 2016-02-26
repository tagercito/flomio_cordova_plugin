# Flomio Cordova Plugin

Flomio's Proximity ID plugin for Cordova / Meteor

**Requires Flomio SDK version** ***1.9***

## Installation

- Download and install [Node](http://nodejs.org/) plus the [Cordova CLI](http://cordova.apache.org/docs/en/4.0.0/guide_cli_index.md.html) or [Meteor CLI](https://www.meteor.com/install).

- Create your Cordova or Meteor example app.

```bash
cordova create my-plugin-example-app && cd $_
--
meteor create my-plugin-example-app && cd $_
```

- Add the plugin to it.

```bash
cordova plugin add https://github.com/flomio/flomio_cordova_plugin.git
--
meteor add cordova:com.flomio.proximityid@https://github.com/flomio/flomio_cordova_plugin/tarball/<latest-commit-code>
```

- Register plugin within `config.xml` of your app in Cordova. Meteor takes care of this for you (so skip this step).

```xml
<feature name="FLOPlugin">
    <param name="ios-package" value="FLOPlugin" />
</feature>
```

- Implement a simple code snippet to test your setup.

```
function resultCallback(result)
{
	console.log(result);
}
floPlugin.init();
floPlugin.selectReaderType("flojack");
floPlugin.startReader(resultCallback);  // note: reader UID is omitted so all connected devices will start polling
```

- Prepare the app.

```bash
cordova prepare
--
meteor add-platform ios
```

- Open the generated Xcode project located at `platforms/ios` or with Meteor, `.meteor/local/cordova-build/platforms/ios`.

- Drag and drop the Flomio SDK folder into the project (check "Create groups" and "Add to targets").

- Add `-lc++` to "Other Linker Flags" under "Build Settings".

- Goto "Build Settings -> Build Options -> Enable Bitcode" and **disable** bitcode.

- Manually add the SDK folder to library search paths. "Build Settings -> Search Paths -> User Header Search Paths", double-click and add `$(SRCROOT)/FlomioSDKv1.<X>/include/SDKClasses`.

- Build and run the app on an iOS device.

## API

**Required for operation**

* `init()`

	Initialises the plugin, preparing it for first use in the current session
	
* `selectReaderType(readerType)`

	Activates the specified reader type for the current session. Choice of FloJack, FloBLE-EMV or FloBLE-Plus
	
	`String readerType: <"flojack", "floble-emv" or "floble-plus">`
	
**Optional methods**

* `setReaderSettings(readerSettings, [optional]readerUid)`

	Configures settings for a target reader for the current session. Every setting in `readerSettings` is optional
	
	```
	Object readerSettings
	{
		int scanPeriod,  // scan period in ms
		bool scanSound,  // toggle scan sound on/off
		String operationState: <"read-uid", "read-data-blocks" or "write-data-blocks">,
		int startBlock,  // the data block from which to start reading
		String messageToWrite  // default message to write
	}
	```
	`String readerUid:` the unique ID number of the target reader
	
* `getReaderSettings(resultCallback, readerUid)`

	Retrieves settings for a target reader
	
	```
	function resultCallback(result)
	Object result
	{
		int scanPeriod,  // scan period in ms
		bool scanSound,  // toggle scan sound on/off
		String operationState: <"read-uid", "read-data-blocks" or "write-data-blocks">,
		int startBlock,  // the data block from which to start reading
		String messageToWrite  // default message to write
	}
	```
	`String readerUid:` unique ID number of the target reader
	
* `onReaderStatusChange(resultCallback)`

	Assign a callback function to fire when the status of **any** reader changes
	
	```
	function resultCallback(result)
	Object result
	{
		String readerUid,  // unique ID number of the reader
		bool connected,  // whether or not the reader is connected
		int batteryLevel  // battery level of the reader in %
	}
	```
	
* `startReader(resultCallback, readerUid)`

	Start polling for proximity ID tags with the target reader
	
	```
	function resultCallback(result)
	Object result
	{
		String tagUid,  // unique ID number of the tag
		String readerUid  // unique ID number of the reader
	}
	```
	`String readerUid:` unique ID number of the target reader
	
* `stopReader(readerUid)`

	Stop polling on the target reader
	
	`String readerUid:` unique ID number of the target reader
	
* `sendApdu(resultCallback, readerUid, apdu)`

	Sends an APDU command to the target reader and receives the response APDU
	
	```
	function resultCallback(result)
	Object result
	{
		String responseApdu  // hexidecimal format
	}
	```
	`String readerUid:` unique ID number of the target reader
	
	`String apdu:` command to be sent to the target reader (hexadecimal format)
	