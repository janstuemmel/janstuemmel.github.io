React Native is developed on macs for macs. Let's see how to properly setup a react native environment on an ubuntu 14.04 machine.

## Install Android SDK

Since Google partners with the [JetBrains][jetbrains] team and uses IntelliJ for their [Android Studio][android_studio] its very simple to setup a android environment on your machine. So lets install Android Studio:

Downlod [Android Studio][android_studio]

```sh
sudo apt-get install lib32z1 lib32ncurses5 lib32bz2-1.0 lib32stdc++6
cd Downloads
unzip android-studio-ide-*.zip
sudo mv android-studio /opt
sudo chmod +x /opt/android-studio/bin/studio.sh
```
Create the file `/usr/share/applications/android-studio.desktop`
an put in the following contents

```
[Desktop Entry]
Type=Application
Name=Android Studio
Exec="/opt/android-studio/bin/studio.sh" %f
Icon=/opt/android-studio/bin/studio.png
Categories=Development;IDE;
```

Now launch Android Studio. You will see a dialog that wants to download the AndroidSDK for you. Choose a sdk directory and start downloading, that will take a while. A good location for the sdk would be `/usr/local/opt/android-sdk`.

 After downloading the sdk, lets edit your bash PATH:  
Add following lines to your `~/.bashrc`. This adds **adb** to your path.

```sh
# /usr/local/opt/android-sdk if you use that directory for the sdk
export ANDROID_HOME=/usr/local/opt/android-sdk
PATH=$PATH:/usr/local/opt/android-sdk/platform-tools
```

## Install React Native

React native requires node>=4.0 and watchman. Let's install those dependencies,
according to their documentation: [Node Install Docs][node_install] and [Watchman Install Docs][watchman_install]

```sh
# nodjs
curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
sudo apt-get install nodejs
# watchman
sudo apt-get install autoconf automake python-dev
git clone https://github.com/facebook/watchman.git
git checkout v4.5.0
./autogen.sh
./configure
make
sudo make install
```

Finally, we can install **react-native**:

```sh
sudo npm install -g react-native
```

## Use React Native

```
react-native init TestApp
cd TestApp
react-native start & react-native run-android
```

This creates a folder `TestApp` and install all necessary dependencies.
`start` starts the development server and `run-android` initializes a
android virtual device.

## Issues

Start Android Studio and remember two utilities:

1. `Tools > Android > AVD Manager`
2. `Tools > Android > SDK Manager`

### Wrong SDK Version

Go to the `SDK Manager`, click **Launch Standalone SDK Manager** and look which version
the **Android SDK Build Tools** have. Then go into your TestApp directory, replace
**buildToolsVersion** in `./android/app/build.gradle` with your local version.

### Watchman Error

If there occurs any error with watchman, try to fix it with that [Github fix][watchman_error]

### Missing SDK packages

Look at [React Android Setup][react_native_android], if there are any missing packages.

[jetbrains]: https://www.jetbrains.com/
[android_studio]: http://developer.android.com/sdk/index.html
[node_install]: https://nodejs.org/en/download/package-manager/
[watchman_install]: https://facebook.github.io/watchman/docs/install.html
[watchman_error]: https://github.com/facebook/react-native/issues/3199#issuecomment-145426578
[react_native_android]: https://facebook.github.io/react-native/docs/android-setup.html
