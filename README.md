# Ionic app repository demonstrating iOS and Android mobile builds

`Ionic App Build` is the code repository demonstating how to setup ionic cordova project to create iOS and Android mobile builds for development and app-store distribution.

All instruction here is specifically for Mac since we cannot do iOS build without XCode command line tools which can only be installed on Mac.

## Prerequisites    

Install following packages:

1.  NodeJS
    -   `node -v` - verify if you have NodeJS installed otherwise go to this [link](https://nodejs.org/en/) to download the latest.
2. Java JDK
    - `java -version` to verify if you have Java JDK installed on your Mac otherwise install one for macOS from [here](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
3. Ionic & Cordova
    - `ionic -v` - verify if you have ionic installed.
    - `cordova -v` - verify if you have Cordova installed.
    - `npm install -g cordova ionic` - if you don't have then run this command.
4. Gradle
    - `brew install gradle` - Use this [link](https://gradle.org/install/) to explore different installation option if you don't have brew installed.
4. Android Studio
    - If you don't have Android Studio installed then download the latest from [here](https://developer.android.com/studio/index.html).
5. XCode
    - If you don't have XCode installed in your Mac then download the latest from [here](https://developer.apple.com/xcode/downloads/).


## Installation

#### Android SDK Tools
-   Open Android Studio, select Configure and install Android SDK build tools. This will install SDK tools under following directory, (replace *** with your username)

    `/Users/***/Library/android/bin/tools`

-   Once Android SDK tools is installed, it requies to accept all licenses for the first time. 

```
cd /Users/user_name/Library/Android/sdk
/tools/bin/
sdkmanager --licenses
```

#### Environment variables
-   Last step will be to set `JAVA_HOME` and `ANDROID_HOME` environment variables, [Here](https://ionicframework.com/docs/developer-resources/platform-setup/mac-setup.html) is the official link on ionic-docs. 

`vim ~/.bash_profile` 
```
  export JAVA_HOME=$(/usr/libexec/java_home)
  # Add that to the global PATH variable
  export PATH=${JAVA_HOME}/bin:$PATH
  # Set Android_HOME
  export ANDROID_HOME=~/Library/Android/sdk/
  # Add the Android SDK to the ANDROID_HOME variable
  export PATH=$ANDROID_HOME/platform-tools:$PATH
  export PATH=$ANDROID_HOME/tools:$PATH
  #Set GRADLE_HOME
  export GRADLE_HOME=/usr/local/Cellar/gradle/4.4.1
  export PATH=$PATH:$GRADLE_HOME/bin
  ```
`:wq!` - to exit the VIM editor.

#### XCode Command Line Tools
If you have installed XCode for the first time then it will need to install command line tools. Open XCode application and this will prompt you to install command line tools.
    
## Setup Ionic Cordova Application
1. Create Ionic App - replace `IonicAppBuild` with the name of your app.
    - `ionic start IonicAppBuild tabs`

2. Add platforms
    - `ionic cordova platform add ios`
    - `ionic cordova platform add android`  

3. Run the project to make sure evrrything is setup correctly so far. Go to the project's root directory and run following commands,
```
npm install
ionic serve
```

This shall build ionic project and launch default browser with `http://localhost:8100`   

## Build
### Apple specific
To setup iOS mobile build, follow this steps,

1. Create Apple Developer Account [here](https://idmsa.apple.com/IDMSWebAuth/login?appIdKey=891bd3417a7776362562d2197f89480a8547b108fd934911bcbea0110d07f757&path=%2Faccount%2F&rv=1).

2. Code Signing Certificate
-   To create code signing certificate we first have to create code signing request using Keychain.
    *   Open `Keychain Access`
    *   Certificate Assistant
    *   Create Certificate Authority. Enter your details and save `.certSigningRequest` file to your file system. (Below is the screenshot of creating code signing request using Keychain.)
        
![Keychain screenshot](https://i.imgur.com/aDGzfK6.png)

-   Once you have `.certSigningRequest` ready, sign into your Apple Developer Console to create iOS development certificate that we will use to sign our development/testing iOS app.
    *   Go to the Certificate section,
    *   Create new
    *   Select iOS App Developement (or App Store and Ad Hoc under Production if you want to create for release.)
    *   Upload `.certSigningRequest` file that we created in the previous step.
    *   Download the certificate. Double click on the downloaded file. This will install certificate and private key pair in your Keychain.

3. App-ID
    *   In Apple Developer Console, go to App IDs section and register app-id that matches your company and project description. For example: `com.meetshah.IonicAppBuild`
    *   Make sure to change `widget id` in config.xml file to match to this app-id.

4. Provisioning Profile
-   Go to Provisioning Profiles section in Apple Developer Console
-   Create `iOS App Developement` (or App Store for app-store distribution or Ad-hoc for in-house distribution.)
-   Select `app-id`
-   select certificate
-   select test devices
-   register profile.

Once created, download provisional profile and double click to install. 

### Android specific    
-   For Android developement/testing app we don't need to sign the app. It will be signed using default debug keystore that comes with Android studio.
-   For Android production app we will need it to be signed by our own keystore. To creare Keystore, run following command in terminal,
```
keytool -genkey -v -keystore my-android-release.keystore -alias my-android-release-alias -keyalg RSA -keysize 2048 -validity 10000
```

### Configure project for mobile builds
Create `build.json` file in your project's root directory and add ios and android build information as follows. 
```
{
    "ios": {
        "debug": {
            "codeSignIdentity": "iPhone Developer",
            "provisioningProfile": "72180949-67c0-4b2d-95a5-36a6d8d81d11",
            "developmentTeam": "RQ6CCQ3QDT",
            "packageType": "development"
        },
        "release": {
            "codeSignIdentity": "iPhone Distribution",
            "provisioningProfile": "bf4be63b-5fc5-4140-9b67-8e2217ab70f1",
            "developmentTeam": "RQ6CCQ3QDT",
            "packageType": "app-store"
        }
    },
    "android":{
        "release": {
            "keystore":"/Users/user_name/my-android-release.keystore",
            "storePassword":"*****",
            "alias":"my-android-release-alias",
            "password":"*****"
            }
    }
}
```
Open provisional profile in text editor to get values of provisioningProfile and developmentTeam. 

### Mobile builds
[Here](https://ionicframework.com/docs/cli/cordova/build/) is the official link on Ionic to create mobile builds.

1. For iOS
    *   Development - `ionic cordova build ios --buildConfig=build.json --device`
    *   Production - `ionic cordova build ios --buildConfig=build.json --device --prod --release`

2. For Android
    * Development - `ionic cordova build android`
    * Production - `ionic cordova build android --buildConfig=build.json --prod --release`
