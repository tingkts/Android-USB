
#### patch overview :
|   | file                          | modification                             |
|---|-------------------------------|------------------------------------------|
| M | include/linux/usb/otg.h       | +	int select_id_gpio;                     |
| M | drivers/usb/chipidea/core.c   | pull down gpio pin select_id when hw init |
| M | drivers/usb/common/common.c   | get instance of gpio pin select_id       |
| M | drivers/usb/gadget/udc/core.c | DEVICE_ATTR(select_id): export select_id |
| M | fsl-imx8qm-mek.dtsi           | declare select_id gpio pin               |