### The settings value of adb_enabled

- Settings UI : [AbstractEnableAdbPreferenceController.java](http://androidxref.com/9.0.0_r3/xref/frameworks/base/packages/SettingsLib/src/com/android/settingslib/development/AbstractEnableAdbPreferenceController.java)

- Settings db : [Settings.Golbal.ADB_ENABLED](http://androidxref.com/9.0.0_r3/xref/frameworks/base/core/java/android/provider/Settings.java#8730)

- property : [persist.sys.usb.config](http://androidxref.com/9.0.0_r3/search?q=%22persist.sys.usb.config%22&defs=&refs=&path=&hist=&project=art&project=bionic&project=bootable&project=build&project=compatibility&project=cts&project=dalvik&project=developers&project=development&project=device&project=external&project=frameworks&project=hardware&project=kernel&project=libcore&project=libnativehelper&project=packages&project=pdk&project=platform_testing&project=prebuilts&project=sdk&project=system&project=test&project=toolchain&project=tools)

- services.usb : [UsbDeviceManager.java](http://androidxref.com/9.0.0_r3/xref/frameworks/base/services/usb/)
               [UsbManager.java](http://androidxref.com/9.0.0_r3/xref/frameworks/base/core/java/android/hardware/usb/UsbManager.java)

<br />

> [0001-work-around-of-adb-default-enabled-in-user-build-ROM.patch](./aosp/build/core/0001-work-around-of-adb-default-enabled-in-user-build-ROM.patch) : workaround of the issue that adb is default enabled in the user release ROM in the RK3399 platform.
