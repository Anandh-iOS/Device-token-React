# Device-token-React
Fetch device token in ios with react native
In Swift, to fetch the device token (push notification token) in a React Native iOS application, you can use a bridge module to handle the communication between React Native and the native iOS code. Here's how you can do it:

1. Create a new Swift file named PushNotificationBridge.swift:

'''
import Foundation
import React

@objc(PushNotificationBridge)
class PushNotificationBridge: NSObject, RCTBridgeModule {
  // Required method to export the module to React Native
  static func moduleName() -> String! {
    return "PushNotificationBridge"
  }
  
  // Required method to define methods accessible from React Native
  @objc
  func fetchDeviceToken(_ resolver: RCTPromiseResolveBlock, rejecter reject: RCTPromiseRejectBlock) {
    let appDelegate = UIApplication.shared.delegate as! AppDelegate
    if let deviceToken = appDelegate.deviceToken {
      resolver(deviceToken)
    } else {
      reject("error", "Device token not available", nil)
    }
  }
}
'''

2. In your AppDelegate.swift file, add the following property to store the device token:

var deviceToken: String?

3. In your AppDelegate.swift file, add the following methods to handle push notifications and store the device token:

import UserNotifications

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {

  // ...
  
  func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
    let token = deviceToken.map { String(format: "%02.2hhx", $0) }.joined()
    self.deviceToken = token
  }

  // ...
}


4. In your React Native project, create a new file named PushNotificationBridge.js in the same directory as your App.js file. Add the following code to create a JavaScript module for fetching the device token:

import { NativeModules } from 'react-native';

export default NativeModules.PushNotificationBridge;

5. Finally, in your App.js file or any other relevant component, import the PushNotificationBridge module and fetch the device token:

import PushNotificationBridge from './PushNotificationBridge';

// ...

const fetchDeviceToken = async () => {
  try {
    const deviceToken = await PushNotificationBridge.fetchDeviceToken();
    console.log('Device Token:', deviceToken);
  } catch (error) {
    console.error('Error fetching device token:', error);
  }
};

// Call the fetchDeviceToken function wherever needed
fetchDeviceToken();


Make sure you have properly linked the Swift code with your React Native project and rebuilt the iOS app for the changes to take effect.

With the above setup, the PushNotificationBridge module provides a fetchDeviceToken method that can be called from React Native to fetch the device token stored in the native iOS code. The device token will be printed in the console when fetched successfully.
