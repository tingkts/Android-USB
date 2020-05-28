
### The definition of Android Developers

- [MtpDevice  |  Android Developers](https://developer.android.com/reference/android/mtp/MtpDevice)
- [UsbDevice  |  Android Developers](https://developer.android.com/reference/android/hardware/usb/UsbDevice)
- [UsbInterface  |  Android Developers](https://developer.android.com/reference/android/hardware/usb/UsbInterface)

![avatar](https://github.com/tingkts/Android-USB/blob/master/MTP%2C%20PTP/reference/UML%20-%20MtpDevice%2C%20MtpObject.png)


### MTP knowledge

- [Android之 MTP框架和流程分析 - Google 搜尋](https://www.google.com/search?q=Android%E4%B9%8B+MTP%E6%A1%86%E6%9E%B6%E5%92%8C%E6%B5%81%E7%A8%8B%E5%88%86%E6%9E%90&rlz=1C1GCEU_zh-TWTW892TW892&oq=Android%E4%B9%8B+MTP%E6%A1%86%E6%9E%B6%E5%92%8C%E6%B5%81%E7%A8%8B%E5%88%86%E6%9E%90&aqs=chrome..69i57j69i65j69i61l2.895j0j4&sourceid=chrome&ie=UTF-8)

    - [MTP协议总结与Android源码分析](https://wujingchao.github.io/2018/01/28/mtp/)
    - [Android之 MTP框架和流程分析 - 如果天空不死 - 博客园](https://www.cnblogs.com/skywang12345/p/3474206.html)
    - [Android之 MTP框架和流程分析 (3) - 开发者知识库](https://www.itdaan.com/tw/1a6b8bee6d3e06f273b04f55979231cc)

![avatar](https://github.com/tingkts/Android-USB/blob/master/MTP%2C%20PTP/reference/MTP%20concept%20quick%20view.png)


### Windows USB driver of android device

- [Get the Google USB Driver  |  Android Developers](https://developer.android.com/studio/run/win-usb)

- Before installing or updating the new driver, you need to delete the strange old driver.

    - [[心得] 解決 Android 手機無法正確安裝 Google 通用驅動程式 - Page 2 of 2 - 風月無邊](https://izaka.tw/2014-10-04-207/2/)

    - [ADB Drivers showing up as 'LeMobile Android Device' - HELP? : Nexus6P](https://www.reddit.com/r/Nexus6P/comments/5nc64z/adb_drivers_showing_up_as_lemobile_android_device/)


![avatar](https://github.com/tingkts/Android-USB/blob/master/MTP%2C%20PTP/reference/Windows%20Device%20Manager%20ADB%20MTP%20driver.PNG)


### USB glossary

- [USB（FS、HS、LS）_嵌入式_王建国的专栏-CSDN博客](https://blog.csdn.net/wjgwrr/article/details/61191214)





### Source Code

- [Gadget Function](http://androidxref.com/9.0.0_r3/xref/hardware/interfaces/usb/gadget/1.0/types.hal)

```hidl
enum GadgetFunction : uint64_t {
    /**
     * Removes all the functions and pulls down the gadget.
     */
    NONE = 0,

    /**
     * Android Debug Bridge function.
     */
    ADB = 1 << 0,

    /**
     * Android open accessory protocol function.
     */
    ACCESSORY = 1 << 1,

    /**
     * Media Transfer protocol function.
     */
    MTP = 1 << 2,

    /**
     * Peripheral mode USB Midi function.
     */
    MIDI = 1 << 3,

    /**
     * Picture transfer protocol function.
     */
    PTP = 1 << 4,

    /**
     * Tethering function.
     */
    RNDIS = 1 << 5,

    /**
     * AOAv2.0 - Audio Source function.
     */
    AUDIO_SOURCE = 1 << 6,
};
```

- Source tree

```
	Java :
		frameworks/base/media/java/android/mtp
		frameworks/base/packages/MtpDocumentsProvider

	Native :
		frameworks/base/media/jni/android_mtp_*
		frameworks/av/media/mtp/			    // system/lib/libmtp.so

	HIDL :
		hardware/interfaces/usb
		vendor/nxp-opensource/imx/usb                       // the NXP's implementation of usb & usb gadget

	Kernel :
		drivers/usb/gadget/function/f_fs.c

		drivers/usb/gadget/configfs.h
		drivers/usb/gadget/configfs.c

		include/uapi/linux/usb/ch9.h

	Sepolicy:
		system/sepolicy/private/mtp.te
		system/sepolicy/public/mtp.te
```


### System Property

```
	// Google Pixel 3 Android 10
	# adb shell getprop | grep -E "mtp|ptp|usb"
	[init.svc.usbd]: [stopped]
	[persist.data.df.dev_name]: [rmnet_usb0]
	[persist.sys.usb.config]: [adb]
	[sys.usb.configfs]: [2]
	[sys.usb.controller]: [a600000.dwc3]
	[sys.usb.ffs.ready]: [1]
	[sys.usb.mtp.device_type]: [3]
```

### Device Node

```
	/sys/devices/soc0/soc/2100000.aips-bus/2184000.usb/ci_hdrc.0/udc/ci_hdrc.0

		MS5:/sys/devices/soc0/soc/2100000.aips-bus/2184000.usb/ci_hdrc.0/udc/ci_hdrc.0 # ls -al
		total 0
		drwxr-xr-x 3 root root    0 2020-05-27 14:59 .
		drwxr-xr-x 3 root root    0 2020-05-27 14:59 ..
		-r--r--r-- 1 root root 4096 2020-05-27 17:29 a_alt_hnp_support
		-r--r--r-- 1 root root 4096 2020-05-27 17:29 a_hnp_support
		-r--r--r-- 1 root root 4096 2020-05-27 17:29 b_hnp_enable
		-r--r--r-- 1 root root 4096 2020-05-27 17:29 current_speed
		lrwxrwxrwx 1 root root    0 2020-05-27 17:29 device -> ../../../ci_hdrc.0
		-r--r--r-- 1 root root 4096 2020-05-27 17:29 function
		-r--r--r-- 1 root root 4096 2020-05-27 17:29 is_a_peripheral
		-r--r--r-- 1 root root 4096 2020-05-27 17:29 is_otg
		-r--r--r-- 1 root root 4096 2020-05-27 17:29 is_selfpowered
		-r--r--r-- 1 root root 4096 2020-05-27 17:29 maximum_speed
		drwxr-xr-x 2 root root    0 2020-05-27 14:59 power
		--w------- 1 root root 4096 2020-05-27 17:29 soft_connect
		--w------- 1 root root 4096 2020-05-27 17:29 srp
		-r--r--r-- 1 root root 4096 2020-05-27 17:29 state
		lrwxrwxrwx 1 root root    0 2020-05-27 17:29 subsystem -> ../../../../../../../../class/udc
		-rw-r--r-- 1 root root 4096 2020-05-27 14:59 uevent


	/sys/devices/virtual/android_usb/android0

		MS5:/sys/devices/virtual/android_usb/android0 # ls -al
		total 0
		drwxr-xr-x 5 root root    0 2020-05-27 15:00 .
		drwxr-xr-x 3 root root    0 2020-05-27 15:00 ..
		drwxr-xr-x 3 root root    0 2020-05-27 15:00 f_audio_source
		drwxr-xr-x 3 root root    0 2020-05-27 15:00 f_midi
		drwxr-xr-x 2 root root    0 2020-05-27 17:31 power
		-r--r--r-- 1 root root 4096 2020-05-27 15:00 state
		lrwxrwxrwx 1 root root    0 2020-05-27 17:31 subsystem -> ../../../../class/android_usb
		-rw-r--r-- 1 root root 4096 2020-05-27 17:31 uevent

	/dev/usb-ffs/adb/ep0~2
				/mtp/ep0~3
				/ptp/ep0~3

	/config/usb-gadget/g1

		MS5:/config/usb_gadget/g1 # ls -al
		total 0
		drwxr-xr-x 6 root root    0 2020-05-27 15:00 .
		drwxr-xr-x 3 root root    0 2020-05-27 15:00 ..
		-rw-r--r-- 1 root root 4096 2020-05-27 15:00 UDC
		-rw-r--r-- 1 root root 4096 2020-05-27 15:00 bDeviceClass
		-rw-r--r-- 1 root root 4096 2020-05-27 15:00 bDeviceProtocol
		-rw-r--r-- 1 root root 4096 2020-05-27 15:00 bDeviceSubClass
		-rw-r--r-- 1 root root 4096 2020-05-27 17:14 bMaxPacketSize0
		-rw-r--r-- 1 root root 4096 2020-05-27 15:00 bcdDevice
		-rw-r--r-- 1 root root 4096 2020-05-27 15:00 bcdUSB
		drwxr-xr-x 3 root root    0 2020-05-27 15:00 configs
		drwxr-xr-x 9 root root    0 2020-05-27 15:00 functions
		-rw-r--r-- 1 root root 4096 2020-05-27 15:00 idProduct
		-rw-r--r-- 1 root root 4096 2020-05-27 15:00 idVendor
		drwxr-xr-x 2 root root    0 2020-05-27 15:00 os_desc
		drwxr-xr-x 3 root root    0 2020-05-27 15:00 strings
```

base on android 9


