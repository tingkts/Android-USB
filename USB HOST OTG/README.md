


## USB Controller (UDC)

|    UDC name   | description                                                                                                                                                                                                                                                |
|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CDNS3     | [CDNS3](http://software-dl.ti.com/jacinto7/esd/processor-sdk-linux-jacinto7/latest/exports/docs/linux/Foundational_Components/Kernel/Kernel_Drivers/USB/CDNS3.html) is a SuperSpeed (SS) USB 3.0 Dual-Role-Device (DRD) controller from Cadence |
| DWC3/DWC2 | The Synopsys DesignWare Core SuperSpeed USB 3.0 Controller (hereinafter referred to as [DWC3](https://www.kernel.org/doc/html/v4.18/driver-api/usb/dwc3.html)) is a USB SuperSpeed compliant controller                                         |
| ci_udc, ci_hdrc    |                                                                                                                                                                                                                                                 |


## USB glossary

- [usb ohci echi xhci - Google 搜尋](https://www.google.com/search?q=usb+ohci+echi+xhci+-+Google+%E6%90%9C%E5%B0%8B&rlz=1C1GCEU_zh-TWTW892TW892&oq=usb+ohci+echi+xhci+-+Google+%E6%90%9C%E5%B0%8B&aqs=chrome..69i57j0j69i60.439j0j1&sourceid=chrome&ie=UTF-8)
  - [幾種USB控制器類型：OHCI，UHCI，EHCI，xHCI @ 立你斯學習記錄 :: 痞客邦 ::](https://b8807053.pixnet.net/blog/post/322349580-%E5%B9%BE%E7%A8%AEusb%E6%8E%A7%E5%88%B6%E5%99%A8%E9%A1%9E%E5%9E%8B%EF%BC%9Aohci%EF%BC%8Cuhci%EF%BC%8Cehci%EF%BC%8Cxhci)
  - [迷途工程師: USB的種類(OHCI、UHCI、EHCI、XHCI)](http://dannysun-unknown.blogspot.com/2017/12/usbohciuhciehcixhci.html)

- [USB的DP(D-) DM(D+)的英文全称是什么?_百度知道](https://zhidao.baidu.com/question/269448960.html?qbpn=1_1&fr=newsearchlist&word=USB%E7%9A%84DP%28D-%29%20DM%28D%2B%29%E7%9A%84%E8%8B%B1%E6%96%87%E5%85%A8%E7%A7%B0%E6%98%AF%E4%BB%80%E4%B9%88%3F&aup=1)


## USB Host, USB OTG　

![avatar](.\reference\usb%20port.png)


- [USB HOST與 USB OTG的區別及工作原理 - IT閱讀](https://www.itread01.com/content/1541235793.html)

### USB ID Pin : The swith of USB Host or Slave (USB Host or OTG)

- patch :
  - [kernel](.\patch\vendor.nxp-opensource.kernel_imx\0001-usb-port1-host-otg-function-switch.patch) : export usb id pin as switch_id node.
  - [framework](.\patch\frameworks.base\0001-Add-for-USB-host-and-device-mode-switch.patch) : read/write switch_id node to switch usb function Host or OTG.
  - [sepolicy, device config](.\patch\device.fsl\0001-support-usb-host-otg-function-switch.patch) : add sepolicy and file permission of switch_id node, and change UDC to ci_hdrc.0




- switch_id node :
    ```
    -rw-rw-rw- 1 root root u:object_r:usb_switch_file:s0 4096 2020-04-23 14:00 /sys/devices/platform/5b0d0000.usb/ci_hdrc.0/udc/ci_hdrc.0/switch_id
    ```