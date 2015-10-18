---
layout:     post
title:      React Native - Modules
date:       2015-10-15 11:21:29
summary:    How to create a Native SMS module in Android using React Native
categories: javascript react-native android
---
React Native was announced earlier this year bringing the Native and web-stack all together in a beautiful framework for creating first-class Native mobile applications.

Read more about React Native [here](https://facebook.github.io/react-native/).

React Native ships with many modules and components out of the box, like ListView, Text and ImageView. But sometimes you want to create your own module. Maybe you have some existing native code you want to reuse, or maybe React Native don't have the exact functionality you need.

The example below shows how you simply can expose Native functions in React Native, and how to use them in JavaScript.

If you don't have React Native installed, see this guide to get started

[React Native - Getting Started](https://facebook.github.io/react-native/docs/getting-started.html#content)


Create a project:

```bash

$ react-native init MyApp

```

To create a Module create a java class extending **ReactContextBaseJavaModule** in your android project.

The class needs to implement **getName()** which simply tells React Native the name of the module.
To create exposable methods, decorate them with **@ReactMethod**. Will do this with our **send** method taking four arguments:

1. (String) phone number
2. (String) message
3. (Callback) success
4. (Callback) error

#### SmsModule.java
```java

public class SmsModule extends ReactContextBaseJavaModule {

    public SmsModule(ReactApplicationContext reactContext) {
        super(reactContext);
    }

    @Override
    public String getName() {
        return "SmsModule";
    }
    // Exposed to the bridge, accessible from javascript
    @ReactMethod
    public void send(String phoneNo, 
                      String sms, 
                      Callback success, // Callbacks for success 
                      Callback err) { // Callback for error
        try {
            SmsManager m = SmsManager.getDefault();
            m.sendTextMessage(phoneNo, null, sms, null, null);

            success.invoke(); // Call success callback
        } catch (Exception e) {
            err.invoke(e.getMessage());
        }
    }
}

```
In addition we need to tell React Native that this module will be a part of the package. If we want to create more modules we simple add them in the **createNativeModules** method.

#### CustomPackages.java
```java
public class CustomPackages implements ReactPackage {
    @Override
    public List<NativeModule> createNativeModules(ReactApplicationContext reactApplicationContext) {
        List<NativeModule> modules = new ArrayList<>();
        modules.add(new SmsModule(reactApplicationContext));

        return modules;
    }

    @Override
    public List<Class<? extends JavaScriptModule>> createJSModules() {
        return Collections.emptyList();
    }

    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactApplicationContext) {
        return Collections.emptyList();
    }
}

```
And finally add the package in the MainActivity:

```java
mReactInstanceManager = ReactInstanceManager.builder()
                ...
                .addPackage(new MainReactPackage())
                .addPackage(new CustomPackages())
                ...
                .build();
```

Thats basically it. Now we can require the module in javascript and call the **send()** message.

The example below will call the **send()** method in our SmsModule.java

#### index.android.js

```javascript
// Require Sms module from NativeModules
var sms = React.NativeModules.SmsModule;

class App extends React.Component {
  constructor() {
    super();
    this.state = { msg: '' };
  }
  
  sendSms() {
    // The exposed method
    sms.send(
      "9*****1",
      "hello",
      () => this.setState({ msg: 'Message was sent!'}),
      (err) => this.setState({ msg: 'Could not send message'})
    )
  }

  render() {
    return (
      <View style={styles.container}>
        <TouchableOpacity style={styles.btn} onPress={() => this.sendSms()}>
          <Text style={styles.btn_txt}>Send sms</Text>
        </TouchableOpacity>
        <Text style={styles.msg}>{ this.state.msg }</Text>
      </View>
    );
  }
}


```
 Result:

![Sms module - Andorid](/images/react_native_sms_module.png)

Success!


React Native provide a flexible and powerful way of creating custom modules. The community have already made a dusin of new modules and components. Many of them can be found [here](https://react.parts/native).

---

