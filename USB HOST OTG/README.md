## USB Device Controller (UDC)

|    UDC name   | description                                                                                                                                                                                                                                                |
|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CDNS3     | [CDNS3](http://software-dl.ti.com/jacinto7/esd/processor-sdk-linux-jacinto7/latest/exports/docs/linux/Foundational_Components/Kernel/Kernel_Drivers/USB/CDNS3.html) is a SuperSpeed (SS) USB 3.0 Dual-Role-Device (DRD) controller from Cadence |
| DWC3/DWC2 | The Synopsys DesignWare Core SuperSpeed USB 3.0 Controller (hereinafter referred to as [DWC3](https://www.kernel.org/doc/html/v4.18/driver-api/usb/dwc3.html)) is a USB SuperSpeed compliant controller                                         |
| ci_udc, ci_hdrc    | UDC made by [Chipidea](https://www.google.com/search?q=Chipidea&rlz=1C1GCEU_zh-TWTW892TW892&oq=Chipidea&aqs=chrome..69i57j0l6j69i60.855j0j1&sourceid=chrome&ie=UTF-8) (abbreviation "ci")                                                                                                                                                                                                                                                 |



<br>

## USB glossary

- [usb ohci echi xhci - Google 搜尋](https://www.google.com/search?q=usb+ohci+echi+xhci+-+Google+%E6%90%9C%E5%B0%8B&rlz=1C1GCEU_zh-TWTW892TW892&oq=usb+ohci+echi+xhci+-+Google+%E6%90%9C%E5%B0%8B&aqs=chrome..69i57j0j69i60.439j0j1&sourceid=chrome&ie=UTF-8)
  - [幾種USB控制器類型：OHCI，UHCI，EHCI，xHCI @ 立你斯學習記錄 :: 痞客邦 ::](https://b8807053.pixnet.net/blog/post/322349580-%E5%B9%BE%E7%A8%AEusb%E6%8E%A7%E5%88%B6%E5%99%A8%E9%A1%9E%E5%9E%8B%EF%BC%9Aohci%EF%BC%8Cuhci%EF%BC%8Cehci%EF%BC%8Cxhci)
  - [迷途工程師: USB的種類(OHCI、UHCI、EHCI、XHCI)](http://dannysun-unknown.blogspot.com/2017/12/usbohciuhciehcixhci.html)

    |     |   |
    |  ----  | ----  |
    | OHCI<br>UHCI  | USB 1.1<br>USB1.0, USB1.1 |
    | ECHI  | USB 2.0 |
    | xHCI  | USB 3.0 |

- [USB的DP(D-) DM(D+)的英文全称是什么?_百度知道](https://zhidao.baidu.com/question/269448960.html?qbpn=1_1&fr=newsearchlist&word=USB%E7%9A%84DP%28D-%29%20DM%28D%2B%29%E7%9A%84%E8%8B%B1%E6%96%87%E5%85%A8%E7%A7%B0%E6%98%AF%E4%BB%80%E4%B9%88%3F&aup=1)


<br>

## USB Host, USB OTG　

![](https://github.com/tingkts/Android-USB/blob/master/USB%20HOST%20OTG/reference/usb%20port.png)


- [USB HOST與 USB OTG的區別及工作原理 - IT閱讀](https://www.itread01.com/content/1541235793.html)

  - dualrole device: can be host or slave negotiated by OTG protocol
  - peripheral only device: acts as slave only

### USB ID Pin : The switch of USB Host or Slave (USB Host or OTG)

- patch :
  - [kernel](./patch/usb%20host%20otg%20switch/vendor.nxp-opensource.kernel_imx/0001-usb-port1-host-otg-function-switch.patch) : export usb id pin as switch_id node.

    |   |                            |                               |
    |---|-------------------------------|-------------------------------------------|
    | M | include/linux/usb/otg.h       | +	int select_id_gpio;                      |
    | M | drivers/usb/chipidea/core.c   | pull down gpio pin select_id when hw init |
    | M | drivers/usb/common/common.c   | get instance of gpio pin select_id        |
    | M | drivers/usb/gadget/udc/core.c | DEVICE_ATTR(select_id): export select_id  |
    | M | fsl-imx8qm-mek.dtsi           | declare select_id gpio pin                |

  - [framework](https://github.com/tingkts/Android-USB/blob/master/USB%20HOST%20OTG/patch/usb%20host%20otg%20switch/frameworks.base/0001-Add-for-USB-host-and-device-mode-switch.patch) : read/write switch_id node to switch usb function Host or OTG.
  - [sepolicy, device config](https://github.com/tingkts/Android-USB/blob/master/USB%20HOST%20OTG/patch/usb%20host%20otg%20switch/device.fsl/0001-support-usb-host-otg-function-switch.patch) : add sepolicy and file permission of switch_id node, and change UDC to ci_hdrc.0







- switch_id node :
    ```
    -rw-rw-rw- 1 root root u:object_r:usb_switch_file:s0 4096 2020-04-23 14:00 /sys/devices/platform/5b0d0000.usb/ci_hdrc.0/udc/ci_hdrc.0/switch_id
    ```

- use case :

    if disable adb debug, usb port 1 & 2 both work as USB Host;<br>
    else if enable adb debug, usb port 1 works as USB OTG, usb port 2 works as USB Host.

#### DTS, Pin Control, Driver

- dts :

    - usb port 1 :

        ```
        // USB port 1

        usbotg1: usb@5b0d0000 {
            compatible = "fsl,imx8qm-usb", "fsl,imx27-usb";
            reg = <0x0 0x5b0d0000 0x0 0x200>;
            interrupt-parent = <&wu>;
            interrupts = <GIC_SPI 267 IRQ_TYPE_LEVEL_HIGH>;
            fsl,usbphy = <&usbphy1>;
            fsl,usbmisc = <&usbmisc1 0>;
            clocks = <&clk IMX8QM_USB2_OH_AHB_CLK>;
            ahb-burst-config = <0x0>;
            tx-burst-size-dword = <0x10>;
            rx-burst-size-dword = <0x10>;
            #stream-id-cells = <1>;
            power-domains = <&pd_conn_usbotg0>;
            status = "disabled";
        };

        &usbotg1 {
            pinctrl-names = "default";
            pinctrl-0 = <&pinctrl_usbotg1>;
            //dr_mode = "host";
            srp-disable;                    		// Session Request Protocol
            hnp-disable;                    		// Host Negotiation Protocol
            adp-disable;                    		// Attach Detection Protocol
            select_id = <&gpio2 16 1>;
            disable-over-current;
            power-polarity-active-high;
            status = "okay";
        };

        pinctrl_usbotg1: usbotg1 {
            fsl,pins = <
                SC_P_USB_SS3_TC0_CONN_USB_OTG1_PWR        0x00000021    // power pin
                SC_P_USB_SS3_TC2_CONN_USB_OTG1_OC	  0x00000021    // oc pin is Overcurrent protection (過電流保護, 過載保護）
                SC_P_SPDIF0_EXT_CLK_LSIO_GPIO2_IO16	  0x0600004c    // USB ID pin, high(1) is otg(slave), low(1) is host.  // 2*32 + 16 = 80, so gpio2_io16 is equal gpio80.
            >;
        };
        ```

    - usb port 2 :

        ```
        // USB port 2

        usbotg3: usb3@5b110000 {
            compatible = "Cadence,usb3";
            reg = <0x0 0x5B110000 0x0 0x10000>,
                <0x0 0x5B130000 0x0 0x10000>,
                <0x0 0x5B140000 0x0 0x10000>,
                <0x0 0x5B160000 0x0 0x40000>,
                <0x0 0x5B120000 0x0 0x10000>;
            interrupt-parent = <&wu>;
            interrupts = <GIC_SPI 271 IRQ_TYPE_LEVEL_HIGH>;
            clocks = <&clk IMX8QM_USB3_LPM_CLK>,
                <&clk IMX8QM_USB3_BUS_CLK>,
                <&clk IMX8QM_USB3_ACLK>,
                <&clk IMX8QM_USB3_IPG_CLK>,
                <&clk IMX8QM_USB3_CORE_PCLK>;
            clock-names = "usb3_lpm_clk", "usb3_bus_clk", "usb3_aclk",
                "usb3_ipg_clk", "usb3_core_pclk";
            power-domains = <&pd_conn_usb2>;
            cdns3,usbphy = <&usbphynop1>;
            status = "disabled";
        };

        &usbotg3 {
            pinctrl-names = "default";
            pinctrl-0 = <&pinctrl_usbh1>;
            srp-disable;
            hnp-disable;
            adp-disable;
            disable-over-current;
            power-polarity-active-high;
            status = "okay";
        };

        pinctrl_usbh1: usbh1 {
            fsl,pins = <
                SC_P_USB_SS3_TC1_CONN_USB_OTG2_PWR	  0x00000021
                SC_P_USB_SS3_TC3_CONN_USB_OTG2_OC	  0x00000021
            >;
        };
        ```



- pinctrl (pin definition):

    ```
    // GPIO mapping table

    iMX8 Pin Name		iMX8 Function Name	0C Assign Name		I / O	Hi / Lo

    USB_SS3_TC0		LSIO.GPIO4.IO03		USB_OTG_ENA		O	H
    USB_SS3_TC2		LSIO.GPIO4.IO05		USB_OTG_OVC_b		I

    USB_SS3_TC1		LSIO.GPIO4.IO04		USB_HOST_ENB		O	H
    USB_SS3_TC3		LSIO.GPIO4.IO06		USB_HOST_OVC_b		I

    SPDIF0_EXT_CLK		LSIO.GPIO2.IO16         USB_OTG_ID		I


    // pads-imx8qm.h

    #define SC_P_USB_SS3_TC0                         	153	/* DMA.I2C1.SCL, CONN.USB_OTG1.PWR, LSIO.GPIO4.IO03 */
    #define SC_P_USB_SS3_TC0_CONN_USB_OTG1_PWR                      SC_P_USB_SS3_TC0                   1


    #define SC_P_USB_SS3_TC2                         	155	/* DMA.I2C1.SDA, CONN.USB_OTG1.OC, LSIO.GPIO4.IO05 */
    #define SC_P_USB_SS3_TC2_CONN_USB_OTG1_OC                       SC_P_USB_SS3_TC2                   1


    #define SC_P_SPDIF0_EXT_CLK                      	97	/* AUD.SPDIF0.EXT_CLK, DMA.DMA0.REQ_IN0, LSIO.GPIO2.IO16 */
    #define SC_P_SPDIF0_EXT_CLK_LSIO_GPIO2_IO16                     SC_P_SPDIF0_EXT_CLK                3
    ```


- driver

    ```
    // drivers/usb/chipidea/ci_hdrc_imx.c:105:	{ .compatible = "fsl,imx27-usb", .data = &imx27_usb_data},
    // drivers/usb/chipidea/ci_hdrc_imx.c:105:	{ .compatible = "fsl,imx8qm-usb", .data = &imx8qm_usb_data}

    static const struct of_device_id ci_hdrc_imx_dt_ids[] = {
    	{ .compatible = "fsl,imx23-usb", .data = &imx23_usb_data},
    	{ .compatible = "fsl,imx28-usb", .data = &imx28_usb_data},
    	{ .compatible = "fsl,imx27-usb", .data = &imx27_usb_data},
    	{ .compatible = "fsl,imx6q-usb", .data = &imx6q_usb_data},
    	{ .compatible = "fsl,imx6sl-usb", .data = &imx6sl_usb_data},
    	{ .compatible = "fsl,imx6sx-usb", .data = &imx6sx_usb_data},
    	{ .compatible = "fsl,imx6ul-usb", .data = &imx6ul_usb_data},
    	{ .compatible = "fsl,imx7d-usb", .data = &imx7d_usb_data},
    	{ .compatible = "fsl,imx7ulp-usb", .data = &imx7ulp_usb_data},
    	{ .compatible = "fsl,imx8mm-usb", .data = &imx8mm_usb_data},
    	{ .compatible = "fsl,imx8qm-usb", .data = &imx8qm_usb_data},
    	{ /* sentinel */ }
    };

    // drivers/usb/cdns3/core.c:763:	{ .compatible = "Cadence,usb3" },

    static const struct of_device_id of_cdns3_match[] = {
    	{ .compatible = "Cadence,usb3" },
    	{ },
    };
    ```


<br>

### USB "dr_mode" : USB role of Host, Peripheral, OTG

```
// host, peripheral, otg

cat /sys/devices/platform/soc/7000000.ssusb/mode

echo peripheral >　/sys/devices/platform/soc/7000000.ssusb/mode
```

<br>

## UUU refresh eMMMC

- patch of [uboot](./patch/uuu%20refresh%20eMMC/vendor.nxp-opensource.uboot-imx/0001-UUU-burn-eMMC.patch) :

    - pull high usb id pin
      ```
      // declaration
      #define BB_GPIO_OTG1_ID IMX_GPIO_NR(2, 16)

      static iomux_cfg_t usb_otg_id_gpio[] = {
      	SC_P_SPDIF0_EXT_CLK | MUX_PAD_CTRL(GPIO_PAD_CTRL),
      };

      // pull high
	  imx8_iomux_setup_multiple_pads(usb_otg_id_gpio, ARRAY_SIZE(usb_otg_id_gpio));
	  gpio_request(BB_GPIO_OTG1_ID, "bb_otg1_id");
	  gpio_direction_output(BB_GPIO_OTG1_ID, 1);
      ```
    - remove config of usb type-c, xhci
      ```
      # CONFIG_USB_TCPC=y

      # CONFIG_USB_XHCI_HCD is not set
      # CONFIG_USB_XHCI_IMX8 is not set
      ```
    - change UDC from cdns3 to ci_udc
      ```
      # CONFIG_USB_CDNS3 is not set
      # CONFIG_USB_CDNS3_GADGET is not set

      CONFIG_CI_UDC=y
      ```
    - comment out "CONFIG_FASTBOOT_USB_DEV=1" to let it use the default value of 0

- [uboot-imx](https://github.com/Freescale/u-boot-fslc) :
  - [cmd/fastboot.c](https://github.com/Freescale/u-boot-fslc/blob/2020.04%2Bfslc/cmd/fastboot.c)

   ```

    static int do_fastboot_usb(int argc, char *const argv[],
    			   uintptr_t buf_addr, size_t buf_size)
    {
    #if CONFIG_IS_ENABLED(USB_FUNCTION_FASTBOOT)
    	int controller_index;
    	char *usb_controller;
    	char *endp;
    	int ret;
    	int index;

    	if (argc < 2)
    		return CMD_RET_USAGE;

    	if (!strcmp(argv[1], "auto")) {
    		index = board_usb_gadget_port_auto();
    		if (index >= 0)
    			controller_index = index;
    		else
    			return CMD_RET_USAGE;
    	} else {
    		usb_controller = argv[1];
    		controller_index = simple_strtoul(usb_controller, &endp, 0);
    		if (*endp != '\0') {
    			pr_err("Error: Wrong USB controller index format\n");
    			return CMD_RET_FAILURE;
    		}
    #ifdef CONFIG_FASTBOOT_USB_DEV
    		controller_index = CONFIG_FASTBOOT_USB_DEV;
    #endif
    	}

    	ret = usb_gadget_initialize(controller_index);
    	if (ret) {
    		pr_err("USB init failed: %d\n", ret);
    		return CMD_RET_FAILURE;
    	}

    	g_dnl_clear_detach();
    	ret = g_dnl_register("usb_dnl_fastboot");
    	if (ret)
    		return ret;

    	if (!g_dnl_board_usb_cable_connected()) {
    		puts("\rUSB cable not detected.\n" \
    		     "Command exit.\n");
    		ret = CMD_RET_FAILURE;
    		goto exit;
    	}

    	while (1) {
    		if (g_dnl_detach())
    			break;
    		if (ctrlc())
    			break;
    		WATCHDOG_RESET();
    		usb_gadget_handle_interrupts(controller_index);
    	}

    	ret = CMD_RET_SUCCESS;

    exit:
    	g_dnl_unregister();
    	g_dnl_clear_detach();
    	usb_gadget_release(controller_index);

    	return ret;
    #else
    	pr_err("Fastboot USB not enabled\n");
    	return CMD_RET_FAILURE;
    #endif
    }

   ```

    - [drivers/usb/gadget/g_dnl.c](https://github.com/Freescale/u-boot-fslc/blob/2020.04%2Bfslc/drivers/usb/gadget/g_dnl.c)

   ```
    int g_dnl_register(const char *name)
    {
        int ret;

        debug("%s: g_dnl_driver.name = %s\n", __func__, name);
        g_dnl_driver.name = name;

        ret = usb_composite_register(&g_dnl_driver);
        if (ret) {
            printf("%s: failed!, error: %d\n", __func__, ret);
            return ret;
        }
        return 0;
    }
   ```

    - [drivers/usb/gadget/composite.c](https://github.com/Freescale/u-boot-fslc/blob/2020.04%2Bfslc/drivers/usb/gadget/composite.c)
   ```
    int usb_composite_register(struct usb_composite_driver *driver)
    {
        int res;

        if (!driver || !driver->dev || !driver->bind || composite)
            return -EINVAL;

        if (!driver->name)
            driver->name = "composite";
        composite = driver;

        res = usb_gadget_register_driver(&composite_driver);
        if (res != 0)
            composite = NULL;

        return res;
    }
   ```

    - [drivers/usb/gadget/ci_udc.c](https://github.com/Freescale/u-boot-fslc/blob/2020.04%2Bfslc/drivers/usb/gadget/ci_udc.c)
   ```
    int usb_gadget_register_driver(struct usb_gadget_driver *driver)
    {
        int ret;

        if (!driver)
            return -EINVAL;
        if (!driver->bind || !driver->setup || !driver->disconnect)
            return -EINVAL;

    #if CONFIG_IS_ENABLED(DM_USB)
        ret = usb_setup_ehci_gadget(&controller.ctrl);
    #else
        ret = usb_lowlevel_init(0, USB_INIT_DEVICE, (void **)&controller.ctrl);
    #endif
        if (ret)
            return ret;

        ret = ci_udc_probe();
        if (ret) {
            DBG("udc probe failed, returned %d\n", ret);
            return ret;
        }

        ret = driver->bind(&controller.gadget);
        if (ret) {
            DBG("driver->bind() returned %d\n", ret);
            return ret;
        }
        controller.driver = driver;

        return 0;
    }
   ```

  - [drivers/usb/host/ehci-mx6.c](https://github.com/Freescale/u-boot-fslc/blob/2020.04%2Bfslc/drivers/usb/host/ehci-mx6.c)
   ```

   static int ehci_usb_probe(struct udevice *dev)
   {
    	struct usb_platdata *plat = dev_get_platdata(dev);
    	struct usb_ehci *ehci = (struct usb_ehci *)devfdt_get_addr(dev);
    	struct ehci_mx6_priv_data *priv = dev_get_priv(dev);
    	enum usb_init_type type = plat->init_type;
    	struct ehci_hccr *hccr;
    	struct ehci_hcor *hcor;
    	int ret;

    #if defined(CONFIG_MX6)
    	if (mx6_usb_fused((u32)ehci)) {
    		printf("USB@0x%x is fused, disable it\n", (u32)ehci);
    		return -ENODEV;
    	}
    #endif

    	priv->ehci = ehci;
    	priv->portnr = dev->seq;

    	/* Init usb board level according to the requested init type */
    	ret = board_usb_init(priv->portnr, type);
    	if (ret) {
    		printf("Failed to initialize board for USB\n");
    		return ret;
    	}

    #if CONFIG_IS_ENABLED(DM_REGULATOR)
    	ret = device_get_supply_regulator(dev, "vbus-supply",
    					  &priv->vbus_supply);
    	if (ret)
    		debug("%s: No vbus supply\n", dev->name);
    #endif

    	ret = ehci_get_usb_phy(dev);
    	if (ret) {
    		debug("%s: fail to get USB PHY base\n", dev->name);
    		return ret;
    	}

    	ret = ehci_mx6_common_init(ehci, priv->portnr);
    	if (ret)
    		return ret;

    	/* If the init_type is unknown due to it is not forced in DTB, we use USB ID to detect */
    	if (priv->init_type == USB_INIT_UNKNOWN) {
    		ret = ehci_usb_phy_mode(dev);
    		if (ret)
    			return ret;
    		if (priv->init_type != type)
    			return -ENODEV;
    	}

    #if CONFIG_IS_ENABLED(DM_REGULATOR)
    	if (priv->vbus_supply) {
    		ret = regulator_set_enable(priv->vbus_supply,
    					   (priv->init_type == USB_INIT_DEVICE) ?
    					   false : true);
    		if (ret) {
    			puts("Error enabling VBUS supply\n");
    			return ret;
    		}
    	}
    #endif

    	if (priv->init_type == USB_INIT_HOST) {
    		setbits_le32(&ehci->usbmode, CM_HOST);
    		writel(CONFIG_MXC_USB_PORTSC, &ehci->portsc);
    		setbits_le32(&ehci->portsc, USB_EN);
    	}

    	mdelay(10);

    	hccr = (struct ehci_hccr *)((ulong)&ehci->caplength);
    	hcor = (struct ehci_hcor *)((ulong)hccr +
    			HC_LENGTH(ehci_readl(&(hccr)->cr_capbase)));

    	return ehci_register(dev, hccr, hcor, &mx6_ehci_ops, 0, priv->init_type);
   }
   ```

<br>

## Misc.

- Kconfig syntax :

  ```
  // if enable CONFIG_DM_USB_GADGET, CONFIG_DM_USB must be enabled first.
  config DM_USB_GADGET
  	bool "Enable driver model for USB Gadget"
  	depends on DM_USB
  	help
  	  Enable driver model for USB Gadget (Peripheral
  	  mode)

  // if enable CONFIG_CU_UDC, then CONFIG_USB_GADGET_DUALSPEED and CONFIG_DM_USB_GADGET are both enabled.
  config CI_UDC
  	bool "ChipIdea device controller"
  	select USB_GADGET_DUALSPEED
  	imply DM_USB_GADGET
  	help
  	  Say Y here to enable device controller functionality of the
  	  ChipIdea driver.
  ```

- out :

  ```
    // kernel
    out/target/product/ms5765/obj/KERNEL_OBJ
        .config

        arch/arm64/boot/dts/freescale/fsl-imx8qm-mek.dtb

        drivers/usb/built-in.a	// a list of built-in modules


    // u-boot
    out/target/product/ms5765/obj/UBOOT_OBJ

        .config

        u-boot.cfg
        u-boot.cfg.configs

        dts/dt.dtb

        drivers/usb

    	 common/common.o

    	 eth/asix.o
    	 eth/usb_ether.o

    	 gadget/ci_udc.o
    	 gadget/config.o
    	 gadget/epautoconf.o
    	 gadget/f_fastboot.o
    	 gadget/f_mass_storage.o
    	 gadget/g_dnl.o
    	 gadget/usbstring.o
    	 gadget/udc/udc-core.o
    	 gadget/udc/udc-uclass.o

    	 host/ehci-hcd.o
    	 host/ehci-mx6.o
    	 host/usb-uclass.o

  ```


<br>
<br>
<br>

<p align="right"  style="text-decoration:underline">imx8qm mek @ android9 android 10</p>
