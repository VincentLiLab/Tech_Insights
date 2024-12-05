- [_overview_](#overview)
- [_detail_](#detail)
  - [_for author to use_](#for-author-to-use)
    - [_file layout_](#file-layout)
    - [_data structure_](#data-structure)
    - [_api_](#api)
  - [_for developer to use_](#for-developer-to-use)
    - [_file layout_](#file-layout-1)
    - [_data structure_](#data-structure-1)
    - [_api_](#api-1)
- [_flow_](#flow)
  - [_for author to use_](#for-author-to-use-1)
  - [_for subsystem to use_](#for-subsystem-to-use)
  - [_for developer to use_](#for-developer-to-use-1)

# _overview_

> 友情提醒：开始本文档之前，请先去看 [_usb protocol_](../../chips/usb_protocol.md)。

_usb subsystem_ 负责 _platform_ 中的所有 _usb controller_ 中的所有 _usb device_ 和 _usb root hub_。_author_ 需要为每个 _usb controller_ 都定义一个 _struct hc_driver_，以执行所对应的 _hardware-specific operations_，以被 _usb subsystem_ 为每个 _usb controller_ 而所创建的一个用于表示这个 _usb controller_ 的 _struct usb_hcd_ 和一个用于表示这个 _usb controller_ 所对应的 _usb bus_ 的 _struct usb_bus_ 所使用。_usb subsystem_ 需要为：每个 _usb device_ 或 _usb root hub_、每个 _usb device_ 或 _usb root hub_ 所包含的所有 _usb configuration_、每个 _usb configuration_ 所包含的所有 _usb interface_ 以及每个 _usb interface_ 所包含的所有 _struct ep_device_，分别定义 _struct usb_device_、_struct usb_host_config_、_struct usb_interface_ 以及 _struct ep_device_，以分别用于表示它们，而每个 _struct usb_device_ 都是连接在一个用于表示 _usb hub_ 的 _struct usb_hub_ 上的一个用于表示 _usb hub port_ 的 _struct usb_port_ 上的。同时 _author_ 还需要为：所有 _struct usb_device_ 定义都一个 _struct usb_device_driver_，以管理其所对应的 _usb device_；每个 _struct usb_interface_ 都分别定义一个 _struct usb_driver_，以管理其所对应的 _struct usb_interface_。_developer_ 需要首先申请并配置 _struct urb_，以包含 _all relevant information to execute any USB transaction and deliver the data and status back_，然后通过所要进行通信的 _struct usb_device_ 所连接的 _struct usb_hub_ 所对应的 _usb interface_ 所对应的 _struct usb_device, a root hub_ 所对应的 _usb bus_ 所对应的 _struct usb_hcd_ 所对应的 _struct hc_driver_ 来完成。 

_**struct usb_device, a root hub::[usb1] -- struct usb_interface::[1-0:1.0]::struct usb_hub -- struct usb_port::[usb1-port1] -- usb_device::[1-1] -- struct usb_interface::[1-1:1.0] -- struct struct hc_driver**_																				
																										                    
# _detail_

## _for author to use_

### _file layout_

_include/linux/usb/hcd.h_  
_include/linux/usb.h_  
_include/uapi/linux/usb/ch9.h_  
_drivers/usb/core/endpoint.c_  
_drivers/usb/core/hcd.c_  

### _data structure_

_struct hc_driver to perform the hardware-specific operations_  
```C
    struct hc_driver {
        const char	*description;	/* "ehci-hcd" etc */
        const char	*product_desc;	/* product/vendor string */
        size_t		hcd_priv_size;	/* size of private data */

        /* irq handler */
        irqreturn_t	(*irq) (struct usb_hcd *hcd);

        int	flags;
    #define	HCD_MEMORY	0x0001		/* HC regs use memory (else I/O) */
    #define	HCD_LOCAL_MEM	0x0002		/* HC needs local memory */
    #define	HCD_SHARED	0x0004		/* Two (or more) usb_hcds share HW */
    #define	HCD_USB11	0x0010		/* USB 1.1 */
    #define	HCD_USB2	0x0020		/* USB 2.0 */
    #define	HCD_USB25	0x0030		/* Wireless USB 1.0 (USB 2.5)*/
    #define	HCD_USB3	0x0040		/* USB 3.0 */
    #define	HCD_USB31	0x0050		/* USB 3.1 */
    #define	HCD_USB32	0x0060		/* USB 3.2 */
    #define	HCD_MASK	0x0070
    #define	HCD_BH		0x0100		/* URB complete in BH context */

        /* called to init HCD and root hub */
        int	(*reset) (struct usb_hcd *hcd);
        int	(*start) (struct usb_hcd *hcd);

        /* NOTE:  these suspend/resume calls relate to the HC as
        * a whole, not just the root hub; they're for PCI bus glue.
        */
        /* called after suspending the hub, before entering D3 etc */
        int	(*pci_suspend)(struct usb_hcd *hcd, bool do_wakeup);

        /* called after entering D0 (etc), before resuming the hub */
        int	(*pci_resume)(struct usb_hcd *hcd, bool hibernated);

        /* cleanly make HCD stop writing memory and doing I/O */
        void	(*stop) (struct usb_hcd *hcd);

        /* shutdown HCD */
        void	(*shutdown) (struct usb_hcd *hcd);

        /* return current frame number */
        int	(*get_frame_number) (struct usb_hcd *hcd);

        /* manage i/o requests, device state */
        int	(*urb_enqueue)(struct usb_hcd *hcd,
                    struct urb *urb, gfp_t mem_flags);
        int	(*urb_dequeue)(struct usb_hcd *hcd,
                    struct urb *urb, int status);

        /*
        * (optional) these hooks allow an HCD to override the default DMA
        * mapping and unmapping routines.  In general, they shouldn't be
        * necessary unless the host controller has special DMA requirements,
        * such as alignment contraints.  If these are not specified, the
        * general usb_hcd_(un)?map_urb_for_dma functions will be used instead
        * (and it may be a good idea to call these functions in your HCD
        * implementation)
        */
        int	(*map_urb_for_dma)(struct usb_hcd *hcd, struct urb *urb,
                    gfp_t mem_flags);
        void    (*unmap_urb_for_dma)(struct usb_hcd *hcd, struct urb *urb);

        /* hw synch, freeing endpoint resources that urb_dequeue can't */
        void	(*endpoint_disable)(struct usb_hcd *hcd,
                struct usb_host_endpoint *ep);

        /* (optional) reset any endpoint state such as sequence number
        and current window */
        void	(*endpoint_reset)(struct usb_hcd *hcd,
                struct usb_host_endpoint *ep);

        /* root hub support */
        int	(*hub_status_data) (struct usb_hcd *hcd, char *buf);
        int	(*hub_control) (struct usb_hcd *hcd,
                    u16 typeReq, u16 wValue, u16 wIndex,
                    char *buf, u16 wLength);
        int	(*bus_suspend)(struct usb_hcd *);
        int	(*bus_resume)(struct usb_hcd *);
        int	(*start_port_reset)(struct usb_hcd *, unsigned port_num);
        unsigned long	(*get_resuming_ports)(struct usb_hcd *);

            /* force handover of high-speed port to full-speed companion */
        void	(*relinquish_port)(struct usb_hcd *, int);
            /* has a port been handed over to a companion? */
        int	(*port_handed_over)(struct usb_hcd *, int);

            /* CLEAR_TT_BUFFER completion callback */
        void	(*clear_tt_buffer_complete)(struct usb_hcd *,
                    struct usb_host_endpoint *);

        /* xHCI specific functions */
            /* Called by usb_alloc_dev to alloc HC device structures */
        int	(*alloc_dev)(struct usb_hcd *, struct usb_device *);
            /* Called by usb_disconnect to free HC device structures */
        void	(*free_dev)(struct usb_hcd *, struct usb_device *);
        /* Change a group of bulk endpoints to support multiple stream IDs */
        int	(*alloc_streams)(struct usb_hcd *hcd, struct usb_device *udev,
            struct usb_host_endpoint **eps, unsigned int num_eps,
            unsigned int num_streams, gfp_t mem_flags);
        /* Reverts a group of bulk endpoints back to not using stream IDs.
        * Can fail if we run out of memory.
        */
        int	(*free_streams)(struct usb_hcd *hcd, struct usb_device *udev,
            struct usb_host_endpoint **eps, unsigned int num_eps,
            gfp_t mem_flags);

        /* Bandwidth computation functions */
        /* Note that add_endpoint() can only be called once per endpoint before
        * check_bandwidth() or reset_bandwidth() must be called.
        * drop_endpoint() can only be called once per endpoint also.
        * A call to xhci_drop_endpoint() followed by a call to
        * xhci_add_endpoint() will add the endpoint to the schedule with
        * possibly new parameters denoted by a different endpoint descriptor
        * in usb_host_endpoint.  A call to xhci_add_endpoint() followed by a
        * call to xhci_drop_endpoint() is not allowed.
        */
            /* Allocate endpoint resources and add them to a new schedule */
        int	(*add_endpoint)(struct usb_hcd *, struct usb_device *,
                    struct usb_host_endpoint *);
            /* Drop an endpoint from a new schedule */
        int	(*drop_endpoint)(struct usb_hcd *, struct usb_device *,
                    struct usb_host_endpoint *);
            /* Check that a new hardware configuration, set using
            * endpoint_enable and endpoint_disable, does not exceed bus
            * bandwidth.  This must be called before any set configuration
            * or set interface requests are sent to the device.
            */
        int	(*check_bandwidth)(struct usb_hcd *, struct usb_device *);
            /* Reset the device schedule to the last known good schedule,
            * which was set from a previous successful call to
            * check_bandwidth().  This reverts any add_endpoint() and
            * drop_endpoint() calls since that last successful call.
            * Used for when a check_bandwidth() call fails due to resource
            * or bandwidth constraints.
            */
        void	(*reset_bandwidth)(struct usb_hcd *, struct usb_device *);
            /* Returns the hardware-chosen device address */
        int	(*address_device)(struct usb_hcd *, struct usb_device *udev);
            /* prepares the hardware to send commands to the device */
        int	(*enable_device)(struct usb_hcd *, struct usb_device *udev);
            /* Notifies the HCD after a hub descriptor is fetched.
            * Will block.
            */
        int	(*update_hub_device)(struct usb_hcd *, struct usb_device *hdev,
                struct usb_tt *tt, gfp_t mem_flags);
        int	(*reset_device)(struct usb_hcd *, struct usb_device *);
            /* Notifies the HCD after a device is connected and its
            * address is set
            */
        int	(*update_device)(struct usb_hcd *, struct usb_device *);
        int	(*set_usb2_hw_lpm)(struct usb_hcd *, struct usb_device *, int);
        /* USB 3.0 Link Power Management */
            /* Returns the USB3 hub-encoded value for the U1/U2 timeout. */
        int	(*enable_usb3_lpm_timeout)(struct usb_hcd *,
                struct usb_device *, enum usb3_link_state state);
            /* The xHCI host controller can still fail the command to
            * disable the LPM timeouts, so this can return an error code.
            */
        int	(*disable_usb3_lpm_timeout)(struct usb_hcd *,
                struct usb_device *, enum usb3_link_state state);
        int	(*find_raw_port_number)(struct usb_hcd *, int);
        /* Call for power on/off the port if necessary */
        int	(*port_power)(struct usb_hcd *hcd, int portnum, bool enable);

        int (*sec_event_ring_setup)(struct usb_hcd *hcd, unsigned int intr_num);
        int (*sec_event_ring_cleanup)(struct usb_hcd *hcd,
                unsigned int intr_num);
        phys_addr_t (*get_sec_event_ring_phys_addr)(struct usb_hcd *hcd,
                unsigned int intr_num, dma_addr_t *dma);
        phys_addr_t (*get_xfer_ring_phys_addr)(struct usb_hcd *hcd,
                struct usb_device *udev, struct usb_host_endpoint *ep,
                dma_addr_t *dma);
        int (*get_core_id)(struct usb_hcd *hcd);
        int (*stop_endpoint)(struct usb_hcd *hcd, struct usb_device *udev,
                struct usb_host_endpoint *ep);

        ANDROID_KABI_RESERVE(1);
        ANDROID_KABI_RESERVE(2);
        ANDROID_KABI_RESERVE(3);
        ANDROID_KABI_RESERVE(4);
    };
```
***

_struct usb_hcd to represent a usb host controller_
```C
    struct usb_hcd {

        /*
        * housekeeping
        */
        struct usb_bus		self;		/* hcd is-a bus */
        struct kref		kref;		/* reference counter */

        const char		*product_desc;	/* product/vendor string */
        int			speed;		/* Speed for this roothub.
                            * May be different from
                            * hcd->driver->flags & HCD_MASK
                            */
        char			irq_descr[24];	/* driver + bus # */

        struct timer_list	rh_timer;	/* drives root-hub polling */
        struct urb		*status_urb;	/* the current status urb */
    #ifdef CONFIG_PM
        struct work_struct	wakeup_work;	/* for remote wakeup */
    #endif

        /*
        * hardware info/state
        */
        const struct hc_driver	*driver;	/* hw-specific hooks */

        /*
        * OTG and some Host controllers need software interaction with phys;
        * other external phys should be software-transparent
        */
        struct usb_phy		*usb_phy;
        struct usb_phy_roothub	*phy_roothub;

        /* Flags that need to be manipulated atomically because they can
        * change while the host controller is running.  Always use
        * set_bit() or clear_bit() to change their values.
        */
        unsigned long		flags;
    #define HCD_FLAG_HW_ACCESSIBLE		0	/* at full power */
    #define HCD_FLAG_POLL_RH		2	/* poll for rh status? */
    #define HCD_FLAG_POLL_PENDING		3	/* status has changed? */
    #define HCD_FLAG_WAKEUP_PENDING		4	/* root hub is resuming? */
    #define HCD_FLAG_RH_RUNNING		5	/* root hub is running? */
    #define HCD_FLAG_DEAD			6	/* controller has died? */
    #define HCD_FLAG_INTF_AUTHORIZED	7	/* authorize interfaces? */
    #define HCD_FLAG_DEV_AUTHORIZED		8	/* authorize devices? */

        /* The flags can be tested using these macros; they are likely to
        * be slightly faster than test_bit().
        */
    #define HCD_HW_ACCESSIBLE(hcd)	((hcd)->flags & (1U << HCD_FLAG_HW_ACCESSIBLE))
    #define HCD_POLL_RH(hcd)	((hcd)->flags & (1U << HCD_FLAG_POLL_RH))
    #define HCD_POLL_PENDING(hcd)	((hcd)->flags & (1U << HCD_FLAG_POLL_PENDING))
    #define HCD_WAKEUP_PENDING(hcd)	((hcd)->flags & (1U << HCD_FLAG_WAKEUP_PENDING))
    #define HCD_RH_RUNNING(hcd)	((hcd)->flags & (1U << HCD_FLAG_RH_RUNNING))
    #define HCD_DEAD(hcd)		((hcd)->flags & (1U << HCD_FLAG_DEAD))

        /*
        * Specifies if interfaces are authorized by default
        * or they require explicit user space authorization; this bit is
        * settable through /sys/class/usb_host/X/interface_authorized_default
        */
    #define HCD_INTF_AUTHORIZED(hcd) \
        ((hcd)->flags & (1U << HCD_FLAG_INTF_AUTHORIZED))

        /*
        * Specifies if devices are authorized by default
        * or they require explicit user space authorization; this bit is
        * settable through /sys/class/usb_host/X/authorized_default
        */
    #define HCD_DEV_AUTHORIZED(hcd) \
        ((hcd)->flags & (1U << HCD_FLAG_DEV_AUTHORIZED))

        /* Flags that get set only during HCD registration or removal. */
        unsigned		rh_registered:1;/* is root hub registered? */
        unsigned		rh_pollable:1;	/* may we poll the root hub? */
        unsigned		msix_enabled:1;	/* driver has MSI-X enabled? */
        unsigned		msi_enabled:1;	/* driver has MSI enabled? */
        /*
        * do not manage the PHY state in the HCD core, instead let the driver
        * handle this (for example if the PHY can only be turned on after a
        * specific event)
        */
        unsigned		skip_phy_initialization:1;

        /* The next flag is a stopgap, to be removed when all the HCDs
        * support the new root-hub polling mechanism. */
        unsigned		uses_new_polling:1;
        unsigned		wireless:1;	/* Wireless USB HCD */
        unsigned		has_tt:1;	/* Integrated TT in root hub */
        unsigned		amd_resume_bug:1; /* AMD remote wakeup quirk */
        unsigned		can_do_streams:1; /* HC supports streams */
        unsigned		tpl_support:1; /* OTG & EH TPL support */
        unsigned		cant_recv_wakeups:1;
                /* wakeup requests from downstream aren't received */
        unsigned		rk3288_relinquish_port_quirk:1;

        unsigned int		irq;		/* irq allocated */
        void __iomem		*regs;		/* device memory/io */
        resource_size_t		rsrc_start;	/* memory/io resource start */
        resource_size_t		rsrc_len;	/* memory/io resource length */
        unsigned		power_budget;	/* in mA, 0 = no limit */

        struct giveback_urb_bh  high_prio_bh;
        struct giveback_urb_bh  low_prio_bh;

        /* bandwidth_mutex should be taken before adding or removing
        * any new bus bandwidth constraints:
        *   1. Before adding a configuration for a new device.
        *   2. Before removing the configuration to put the device into
        *      the addressed state.
        *   3. Before selecting a different configuration.
        *   4. Before selecting an alternate interface setting.
        *
        * bandwidth_mutex should be dropped after a successful control message
        * to the device, or resetting the bandwidth after a failed attempt.
        */
        struct mutex		*address0_mutex;
        struct mutex		*bandwidth_mutex;
        struct usb_hcd		*shared_hcd;
        struct usb_hcd		*primary_hcd;


    #define HCD_BUFFER_POOLS	4
        struct dma_pool		*pool[HCD_BUFFER_POOLS];

        int			state;
    #	define	__ACTIVE		0x01
    #	define	__SUSPEND		0x04
    #	define	__TRANSIENT		0x80

    #	define	HC_STATE_HALT		0
    #	define	HC_STATE_RUNNING	(__ACTIVE)
    #	define	HC_STATE_QUIESCING	(__SUSPEND|__TRANSIENT|__ACTIVE)
    #	define	HC_STATE_RESUMING	(__SUSPEND|__TRANSIENT)
    #	define	HC_STATE_SUSPENDED	(__SUSPEND)

    #define	HC_IS_RUNNING(state) ((state) & __ACTIVE)
    #define	HC_IS_SUSPENDED(state) ((state) & __SUSPEND)

        /* more shared queuing code would be good; it should support
        * smarter scheduling, handle transaction translators, etc;
        * input size of periodic table to an interrupt scheduler.
        * (ohci 32, uhci 1024, ehci 256/512/1024).
        */

        ANDROID_KABI_RESERVE(1);
        ANDROID_KABI_RESERVE(2);
        ANDROID_KABI_RESERVE(3);
        ANDROID_KABI_RESERVE(4);

        /* The HC driver's private data is stored at the end of
        * this structure.
        */
        unsigned long hcd_priv[0]
                __attribute__ ((aligned(sizeof(s64))));
    };
```
***

_struct usb_bus to represent a usb bus_
```C
    struct usb_bus {
        struct device *controller;	/* host/master side hardware */
        struct device *sysdev;		/* as seen from firmware or bus */
        int busnum;			/* Bus number (in order of reg) */
        const char *bus_name;		/* stable id (PCI slot_name etc) */
        u8 uses_dma;			/* Does the host controller use DMA? */
        u8 uses_pio_for_control;	/*
                        * Does the host controller use PIO
                        * for control transfers?
                        */
        u8 otg_port;			/* 0, or number of OTG/HNP port */
        unsigned is_b_host:1;		/* true during some HNP roleswitches */
        unsigned b_hnp_enable:1;	/* OTG: did A-Host enable HNP? */
        unsigned no_stop_on_short:1;    /*
                        * Quirk: some controllers don't stop
                        * the ep queue on a short transfer
                        * with the URB_SHORT_NOT_OK flag set.
                        */
        unsigned no_sg_constraint:1;	/* no sg constraint */
        unsigned sg_tablesize;		/* 0 or largest number of sg list entries */

        int devnum_next;		/* Next open device number in
                        * round-robin allocation */
        struct mutex devnum_next_mutex; /* devnum_next mutex */

        struct usb_devmap devmap;	/* device address allocation map */
        struct usb_device *root_hub;	/* Root hub */
        struct usb_bus *hs_companion;	/* Companion EHCI bus, if any */

        int bandwidth_allocated;	/* on this bus: how much of the time
                        * reserved for periodic (intr/iso)
                        * requests is used, on average?
                        * Units: microseconds/frame.
                        * Limits: Full/low speed reserve 90%,
                        * while high speed reserves 80%.
                        */
        int bandwidth_int_reqs;		/* number of Interrupt requests */
        int bandwidth_isoc_reqs;	/* number of Isoc. requests */

        unsigned resuming_ports;	/* bit array: resuming root-hub ports */

    #if defined(CONFIG_USB_MON) || defined(CONFIG_USB_MON_MODULE)
        struct mon_bus *mon_bus;	/* non-null when associated */
        int monitored;			/* non-zero when monitored */
    #endif
        unsigned skip_resume:1;		/* All USB devices are brought into full
                        * power state after system resume. It
                        * is desirable for some buses to keep
                        * their devices in suspend state even
                        * after system resume. The devices
                        * are resumed later when a remote
                        * wakeup is detected or an interface
                        * driver starts I/O.
                        */

        ANDROID_KABI_RESERVE(1);
        ANDROID_KABI_RESERVE(2);
        ANDROID_KABI_RESERVE(3);
        ANDROID_KABI_RESERVE(4);
    };
```
***

_struct usb_device to represent a usb device or a usb root hub_
```C
    struct usb_device {
        int		devnum;
        char		devpath[16];
        u32		route;
        enum usb_device_state	state;
        enum usb_device_speed	speed;
        unsigned int		rx_lanes;
        unsigned int		tx_lanes;

        struct usb_tt	*tt;
        int		ttport;

        unsigned int toggle[2];

        struct usb_device *parent;
        struct usb_bus *bus;
        struct usb_host_endpoint ep0;

        struct device dev;

        struct usb_device_descriptor descriptor;
        struct usb_host_bos *bos;
        struct usb_host_config *config;

        struct usb_host_config *actconfig;
        struct usb_host_endpoint *ep_in[16];
        struct usb_host_endpoint *ep_out[16];

        char **rawdescriptors;

        unsigned short bus_mA;
        u8 portnum;
        u8 level;

        unsigned can_submit:1;
        unsigned persist_enabled:1;
        unsigned have_langid:1;
        unsigned authorized:1;
        unsigned authenticated:1;
        unsigned wusb:1;
        unsigned lpm_capable:1;
        unsigned usb2_hw_lpm_capable:1;
        unsigned usb2_hw_lpm_besl_capable:1;
        unsigned usb2_hw_lpm_enabled:1;
        unsigned usb2_hw_lpm_allowed:1;
        unsigned usb3_lpm_u1_enabled:1;
        unsigned usb3_lpm_u2_enabled:1;
        int string_langid;

        /* static strings from the device */
        char *product;
        char *manufacturer;
        char *serial;

        struct list_head filelist;

        int maxchild;

        u32 quirks;
        atomic_t urbnum;

        unsigned long active_duration;

    #ifdef CONFIG_PM
        unsigned long connect_time;

        unsigned do_remote_wakeup:1;
        unsigned reset_resume:1;
        unsigned port_is_suspended:1;
    #endif
        struct wusb_dev *wusb_dev;
        int slot_id;
        enum usb_device_removable removable;
        struct usb2_lpm_parameters l1_params;
        struct usb3_lpm_parameters u1_params;
        struct usb3_lpm_parameters u2_params;
        unsigned lpm_disable_count;

        u16 hub_delay;

        ANDROID_KABI_RESERVE(1);
        ANDROID_KABI_RESERVE(2);
        ANDROID_KABI_RESERVE(3);
        ANDROID_KABI_RESERVE(4);
    };
```
***

_struct usb_device_driver to manage a struct usb_device_
```C
    struct usb_device_driver {
        const char *name;

        int (*probe) (struct usb_device *udev);
        void (*disconnect) (struct usb_device *udev);

        int (*suspend) (struct usb_device *udev, pm_message_t message);
        int (*resume) (struct usb_device *udev, pm_message_t message);
        struct usbdrv_wrap drvwrap;
        unsigned int supports_autosuspend:1;
    };
```
***

_struct usb_host_endpoint to which urbs are queued and identified by a descriptor within an active interface in a given USB configuration_
```C
    struct usb_host_endpoint {
        struct usb_endpoint_descriptor		desc;
        struct usb_ss_ep_comp_descriptor	ss_ep_comp;
        struct usb_ssp_isoc_ep_comp_descriptor	ssp_isoc_ep_comp;
        struct list_head		urb_list;
        void				*hcpriv;
        struct ep_device		*ep_dev;	/* For sysfs info */

        unsigned char *extra;   /* Extra descriptors */
        int extralen;
        int enabled;
        int streams;
    };
```
***

_struct usb_host_config to represent a usb configuration_
```C
    struct usb_host_config {
        struct usb_config_descriptor	desc;

        char *string;		/* iConfiguration string, if present */

        /* List of any Interface Association Descriptors in this
        * configuration. */
        struct usb_interface_assoc_descriptor *intf_assoc[USB_MAXIADS];

        /* the interfaces associated with this configuration,
        * stored in no particular order */
        struct usb_interface *interface[USB_MAXINTERFACES];

        /* Interface information available even when this is not the
        * active configuration */
        struct usb_interface_cache *intf_cache[USB_MAXINTERFACES];

        unsigned char *extra;   /* Extra descriptors */
        int extralen;
    };
```
***

_struct usb_interface to represent a usb interface_
```C
    struct usb_interface {
        /* array of alternate settings for this interface,
        * stored in no particular order */
        struct usb_host_interface *altsetting;

        struct usb_host_interface *cur_altsetting;	/* the currently
                        * active alternate setting */
        unsigned num_altsetting;	/* number of alternate settings */

        /* If there is an interface association descriptor then it will list
        * the associated interfaces */
        struct usb_interface_assoc_descriptor *intf_assoc;

        int minor;			/* minor number this interface is
                        * bound to */
        enum usb_interface_condition condition;		/* state of binding */
        unsigned sysfs_files_created:1;	/* the sysfs attributes exist */
        unsigned ep_devs_created:1;	/* endpoint "devices" exist */
        unsigned unregistering:1;	/* unregistration is in progress */
        unsigned needs_remote_wakeup:1;	/* driver requires remote wakeup */
        unsigned needs_altsetting0:1;	/* switch to altsetting 0 is pending */
        unsigned needs_binding:1;	/* needs delayed unbind/rebind */
        unsigned resetting_device:1;	/* true: bandwidth alloc after reset */
        unsigned authorized:1;		/* used for interface authorization */

        struct device dev;		/* interface specific device info */
        struct device *usb_dev;
        struct work_struct reset_ws;	/* for resets in atomic context */

        ANDROID_KABI_RESERVE(1);
        ANDROID_KABI_RESERVE(2);
        ANDROID_KABI_RESERVE(3);
        ANDROID_KABI_RESERVE(4);
    };
```
***

_struct usb_driver to manage a struct usb_interface_ 
```C
    struct usb_driver {
        const char *name;

        int (*probe) (struct usb_interface *intf,
                const struct usb_device_id *id);

        void (*disconnect) (struct usb_interface *intf);

        int (*unlocked_ioctl) (struct usb_interface *intf, unsigned int code,
                void *buf);

        int (*suspend) (struct usb_interface *intf, pm_message_t message);
        int (*resume) (struct usb_interface *intf);
        int (*reset_resume)(struct usb_interface *intf);

        int (*pre_reset)(struct usb_interface *intf);
        int (*post_reset)(struct usb_interface *intf);

        const struct usb_device_id *id_table;

        struct usb_dynids dynids;
        struct usbdrv_wrap drvwrap;
        unsigned int no_dynamic_id:1;
        unsigned int supports_autosuspend:1;
        unsigned int disable_hub_initiated_lpm:1;
        unsigned int soft_unbind:1;

        ANDROID_KABI_RESERVE(1);
        ANDROID_KABI_RESERVE(2);
        ANDROID_KABI_RESERVE(3);
        ANDROID_KABI_RESERVE(4);
    };
```
***

_struct ep_device to represent a usb endpoint_
```C
    struct ep_device {
        struct usb_endpoint_descriptor *desc;
        struct usb_device *udev;
        struct device dev;
    };
```
***

_struct usb_device_descriptor, an usb device descriptor_
```C
    /* USB_DT_DEVICE: Device descriptor */
    struct usb_device_descriptor {
        __u8  bLength;
        __u8  bDescriptorType;

        __le16 bcdUSB;
        __u8  bDeviceClass;
        __u8  bDeviceSubClass;
        __u8  bDeviceProtocol;
        __u8  bMaxPacketSize0;
        __le16 idVendor;
        __le16 idProduct;
        __le16 bcdDevice;
        __u8  iManufacturer;
        __u8  iProduct;
        __u8  iSerialNumber;
        __u8  bNumConfigurations;
    } __attribute__ ((packed));
```
*** 

_struct usb_config_descriptor, an usb config descriptor_
```C
    struct usb_config_descriptor {
        __u8  bLength;
        __u8  bDescriptorType;

        __le16 wTotalLength;
        __u8  bNumInterfaces;
        __u8  bConfigurationValue;
        __u8  iConfiguration;
        __u8  bmAttributes;
        __u8  bMaxPower;
    } __attribute__ ((packed));
```
***

_struct usb_interface_descriptor, an usb interface descriptor_
```C
    struct usb_interface_descriptor {
        __u8  bLength;
        __u8  bDescriptorType;

        __u8  bInterfaceNumber;
        __u8  bAlternateSetting;
        __u8  bNumEndpoints;
        __u8  bInterfaceClass;
        __u8  bInterfaceSubClass;
        __u8  bInterfaceProtocol;
        __u8  iInterface;
    } __attribute__ ((packed));
```
***

_struct usb_endpoint_descriptor, an usb endpoint descriptor_
```C
    struct usb_endpoint_descriptor {
        __u8  bLength;
        __u8  bDescriptorType;

        __u8  bEndpointAddress;
        __u8  bmAttributes;
        __le16 wMaxPacketSize;
        __u8  bInterval;

        /* NOTE:  these two are _only_ in audio endpoints. */
        /* use USB_DT_ENDPOINT*_SIZE in bLength, not sizeof. */
        __u8  bRefresh;
        __u8  bSynchAddress;
    } __attribute__ ((packed));
```
***

_struct usb_hub_descriptor, an usb hub descriptor_
```C
    struct usb_hub_descriptor {
        __u8  bDescLength;
        __u8  bDescriptorType;
        __u8  bNbrPorts;
        __le16 wHubCharacteristics;
        __u8  bPwrOn2PwrGood;
        __u8  bHubContrCurrent;

        /* 2.0 and 3.0 hubs differ here */
        union {
            struct {
                /* add 1 bit for hub status change; round to bytes */
                __u8  DeviceRemovable[(USB_MAXCHILDREN + 1 + 7) / 8];
                __u8  PortPwrCtrlMask[(USB_MAXCHILDREN + 1 + 7) / 8];
            }  __attribute__ ((packed)) hs;

            struct {
                __u8 bHubHdrDecLat;
                __le16 wHubDelay;
                __le16 DeviceRemovable;
            }  __attribute__ ((packed)) ss;
        } u;
    } __attribute__ ((packed));
```
***

_struct usb_hub to represent a usb hub_
```C
    struct usb_hub {
        struct device		*intfdev;	/* the "interface" device */
        struct usb_device	*hdev;
        struct kref		kref;
        struct urb		*urb;		/* for interrupt polling pipe */

        /* buffer for urb ... with extra space in case of babble */
        u8			(*buffer)[8];
        union {
            struct usb_hub_status	hub;
            struct usb_port_status	port;
        }			*status;	/* buffer for status reports */
        struct mutex		status_mutex;	/* for the status buffer */

        int			error;		/* last reported error */
        int			nerrors;	/* track consecutive errors */

        unsigned long		event_bits[1];	/* status change bitmask */
        unsigned long		change_bits[1];	/* ports with logical connect
                                status change */
        unsigned long		removed_bits[1]; /* ports with a "removed"
                                device present */
        unsigned long		wakeup_bits[1];	/* ports that have signaled
                                remote wakeup */
        unsigned long		power_bits[1]; /* ports that are powered */
        unsigned long		child_usage_bits[1]; /* ports powered on for
                                children */
        unsigned long		warm_reset_bits[1]; /* ports requesting warm
                                reset recovery */
    #if USB_MAXCHILDREN > 31 /* 8*sizeof(unsigned long) - 1 */
    #error event_bits[] is too short!
    #endif

        struct usb_hub_descriptor *descriptor;	/* class descriptor */
        struct usb_tt		tt;		/* Transaction Translator */

        unsigned		mA_per_port;	/* current for each child */
    #ifdef	CONFIG_PM
        unsigned		wakeup_enabled_descendants;
    #endif

        unsigned		limited_power:1;
        unsigned		quiescing:1;
        unsigned		disconnected:1;
        unsigned		in_reset:1;
        unsigned		quirk_disable_autosuspend:1;

        unsigned		quirk_check_port_auto_suspend:1;

        unsigned		has_indicators:1;
        u8			indicator[USB_MAXCHILDREN];
        struct delayed_work	leds;
        struct delayed_work	init_work;
        struct work_struct      events;
        struct usb_port		**ports;
    };
```
***

_struct usb_port to represent a usb hub port on a usb hub_
```C
    struct usb_port {
        struct usb_device *child;
        struct device dev;
        struct usb_dev_state *port_owner;
        struct usb_port *peer;
        struct dev_pm_qos_request *req;
        enum usb_port_connect_type connect_type;
        usb_port_location_t location;
        struct mutex status_lock;
        u32 over_current_count;
        u8 portnum;
        u32 quirks;
        unsigned int is_superspeed:1;
        unsigned int usb3_lpm_u1_permit:1;
        unsigned int usb3_lpm_u2_permit:1;
    };
```

### _api_

```C
struct usb_hcd *usb_create_hcd(const struct hc_driver *driver,
		struct device *dev, const char *bus_name);   
```
* 创建并配置一个 _struct usb_hcd_。
* 根据所传入的相关参数，配置所创建的 _struct usb_hcd_ 的 _struct usb_bus_。
* 根据所传入的 _struct hc_driver_，配置所创建的 _struct usb_hcd_。
* 返回所创建的 _struct usb_hcd_。
***

```C
struct usb_device *usb_alloc_dev(struct usb_device *parent,
				 struct usb_bus *bus, unsigned port1);
```
* 创建并配置一个 _struct usb_device_。
* 配置所创建的 _struct usb_device_ 所对应的 _usb_host_endpoint 0_：
    * 所对应的 _struct usb_endpoint_descriptor_。
    * 所对应的其他相关参数。
* 配置所创建的 _struct usb_device_ 所对应的 _struct device_ 所对应的 _sysfs_，比如：
    * 所对应的 _struct bus_type::/sys/bus/usb_。
    * 所对应的 _struct device_type::usb_device_type_。
    * 所对应的 _name_：
        * _usb%d if the struct usb_device is a usb root hub_，比如：_/sys/devices/soc0/amba/e0002000.usb/ci_hdrc.0/usb2_，其中的 _2_ 是 _usb subsystem_ 所分配的 _bus number_。
        * _%d-%d or %d-%s.%d the struct usb_device is a usb device_，比如：
            * _/sys/devices/soc0/amba/e0002000.usb/ci_hdrc.0/usb1/2-1_，其中的：_2_ 是 _usb subsystem_ 所分配的 _bus number_；_1_ 是 _the struct usb_device_ 所对应的 _struct usb_hub_ 所对应的 _struct port_。
            * _/sys/devices/soc0/amba/e0002000.usb/ci_hdrc.0/usb1/1-1/1-1.3_，其中的：_1-1_ 是 _the struct usb_device_ 所对应的 _the parent of the struct usb_device_；_3_ 是 _the struct usb_device_ 所对应的 _struct usb_hub_ 所对应的 _struct port_。
* 根据所传入的 _struct usb_bus_，配置所创建的 _struct usb_device_。
*** 

```C
    int usb_new_device(struct usb_device *udev);
```
* 根据所传入的 _struct usb_device_ 所对应的 _struct usb_device_descriptor_，创建 _struct usb_host_configs_。
* 根据所创建的 _struct usb_host_configs_，配置所传入的 _struct usb_device_。
* 获取所传入的 _struct usb_device_ 所对应的 _struct usb_config_descriptors_，并根据其以及其他相关参数，配置所传入的 _struct usb_device_ 所对应的 _struct usb_host_configs_。
* 获取所传入的 _struct usb_device_ 所对应的 _struct usb_interface_descriptors_，并根据其以及其他相关参数，配置 _struct usb_device_ 所对应的 _struct usb_host_configs_。
* 根据所获取的 _struct usb_interface_descriptors_，创建 _struct usb_host_endpoints_。
* 根据所创建的 _struct usb_host_endpoints_，配置所传入的 _struct usb_device_ 所对应的 _struct usb_host_configs_
* 获取所传入的 _struct usb_device_ 所对应的 _struct usb_endpoint_descriptors_，并根据其以及其他相关参数，配置 _struct usb_device_ 所对应的 _struct usb_host_configs_ 所对应的 _struct usb_host_endpoints_。 
* 注册所传入的 _struct usb_device_ 所对应的 _struct device_。
* 根据所传入的 _struct usb_device_ 所对应的 _struct usb_host_endpoint 0_，创建并配置一个 _struct ep_device_。
* 配置所创建的 _struct ep_device_ 所对应的 _struct device_ 所对应的 _sysfs_。
* 注册所创建的 _struct ep_device_。
* 根据所创建的 _struct ep_device_，配置所传入的 _struct usb_device_ 所对应的 _struct usb_host_endpoint 0_。 
***

```C
int usb_add_hcd(struct usb_hcd *hcd,
		unsigned int irqnum, unsigned long irqflags);
```
* 从 _usb subsystem_ 中，申请一个 _bus number_。 
* 根据所申请的 _bus number_，配置所传入的 _struct usb_hcd_ 所对应的 _struct usb_bus_。
* 创建并配置一个 _struct usb_device, a root hub_。
* 配置所创建的 _struct usb_device_ 所对应的 _usb_host_endpoint 0_：
    * 所对应的 _struct usb_endpoint_descriptor_。
    * 所对应的其他相关参数。
* 配置所创建的 _struct usb_device, a root hub_ 所对应的 _struct device_ 所对应的 _sysfs_，比如：
    * 所对应的 _struct bus_type::/sys/bus/usb_。
    * 所对应的 _struct device_type::usb_device_type_。
    * 所对应的 _name::usb%d_，比如：_/sys/devices/soc0/amba/e0002000.usb/ci_hdrc.0/usb2_，其中的 _2_ 是 _usb subsystem_ 所分配的 _bus number_。
* 根据所传入的 _struct usb_hcd_ 所对应的 _struct usb_bus_，配置所创建的 _struct usb_device, a root hub_。
* 根据所传入的 _struct usb_hcd_，配置所传入的 _struct usb_hcd_ 所对应的 _struct usb_bus_。
* 执行所传入的 _struct usb_hcd_ 所对应的 _struct hc_driver_，以执行 _hardware-specific operations_。
* 获取所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_device_descriptor_，并根据其以及其他相关参数，配置 _struct usb_device, a root hub_。
* 根据所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_device_descriptor_，创建 _struct usb_host_configs_。
* 根据所创建的 _struct usb_host_configs_，配置所创建的 _struct usb_device, a root hub_。
* 获取所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_config_descriptors_，并根据其以及其他相关参数，配置所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_host_configs_。
* 获取所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_interface_descriptors_，并根据其以及其他相关参数，配置 _struct usb_device, a root hub_ 所对应的 _struct usb_host_configs_。
* 根据所获取的 _struct usb_interface_descriptors_，创建 _struct usb_host_endpoints_。
* 根据所创建的 _struct usb_host_endpoints_，配置所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_host_configs_
* 获取所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_endpoint_descriptors_，并根据其以及其他相关参数，配置 _struct usb_device, a root hub_ 所对应的 _struct usb_host_configs_ 所对应的 _struct usb_host_endpoints_。 
* 注册所创建的 _struct usb_device, a root hub_ 所对应的 _struct device_。
* 根据所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_host_endpoint 0_，创建并配置一个 _struct ep_device_。
* 配置所创建的 _struct ep_device_ 所对应的 _struct device_ 所对应的 _sysfs_。
* 注册所创建的 _struct ep_device_。
* 根据所创建的 _struct ep_device_，配置所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_host_endpoint 0_。 
***

```C
    int usb_register_device_driver(struct usb_device_driver *new_udriver,
            struct module *owner);
```
* 配置所传入的 _struct usb_device_driver_ 所对应的 _struct device_driver_ 所对应的 _sysfs_，比如：
    * 所对应的 _name_。
    * 所对应的 _struct bus_type::/sys/bus/usb_。
***

```C
    int usb_register_driver(struct usb_driver *new_driver, struct module *owner,
                const char *mod_name)
```
* 配置所传入的 _struct usb_driver_ 所对应的 _struct device_driver_ 所对应的 _sysfs_，比如：
    * 所对应的 _name_。
    * 所对应的 _struct bus_type::/sys/bus/usb_。
***

```C
    int generic_probe(struct usb_device *udev);
```
* 在所传入的 _struct usb_device_ 所对应的 _struct usb_host_configs_ 中，获取一个 _struct usb_host_config_。
* 根据所获取的 _struct usb_host_config_ 所对应的 _struct usb_interface_descriptors_ 以及其他相关参数，创建 _struct usb_interfaces_。 
* 配置所创建的 _struct usb_interfaces_ 所对应的 _struct devices_ 所对应的 _sysfs_，比如：
    * 所对应的 _the parent::所传入的 struct usb_device 所对应的 struct device_。
    * 所对应的 _struct bus_type::/sys/bus/usb_。
    * 所对应的 _struct device_type::usb_if_device_type_。
    * 所对应的 _name :%d-%s:%d.%d_，比如：_/sys/devices/soc0/amba/e0002000.usb/ci_hdrc.0/usb1/1-1/1-1:1.0_，其中的 _1-1_ 是 _the struct usb_interface_ 所对应的 _the struct usb_device_；_1_ 是 _the usb configuration number_；_0_ 是 _the usb interface number_。
* 根据所获取的 _struct usb_host_config_ 所对应的 _struct usb_host_endpoints_，配置所传入的 _struct usb_device_ 和所创建的 _struct usb_interfaces_。
* 注册所创建的 _struct usb_interfaces_ 所对应的 _struct devices_。
* 根据所创建的 _struct usb_device_ 所对应的 _struct usb_host_endpoints_，创建并配置 _struct ep_devices_。
* 配置所创建的 _struct ep_devices_ 所对应的 _struct devices_ 所对应的 _sysfs_。
* 注册所创建的 _struct ep_devices_。
* 根据所创建的 _struct ep_devices_，配置所传入的 _struct usb_device_ 所对应的 _struct usb_host_endpoints_。 
***

```C
    static int hub_probe(struct usb_interface *intf, const struct usb_device_id *id);
```
* 创建并配置一个 _struct usb_hub_。
* 根据所传入的 _struct usb_interface_ 所对应的 _struct device_，配置所创建的 _struct usb_hub_。
* 根据所传入的 _struct usb_interface_ 所对应的 _struct device_ 所对应的 _parent_，获取所对应的 _struct usb_device_。
* 根据所获取的 _struct usb_device_，配置所创建的 _struct usb_hub_。
* 根据 _void hub_event(struct work_struct *work)_，配置所创建的 _struct usb_hub_ 所对应的 _struct work_struct_。
* 向所创建的 _struct usb_hub_ 所对应的 _struct usb_device_，获取所对应的 _struct usb_hub_descriptor_。
* 根据所获取的 _struct usb_hub_descriptor_，配置所创建的 _struct usb_hub_。
* 根据所获取的 _struct usb_hub_descriptor_，创建 _struct usb_ports_。
* 获取所创建的 _struct usb_hub_ 所对应的 _struct usb_device_ 所对应的 _struct usb_bus_ 所对应的 _struct usb_hcd_。
* 执行所获取的 _struct usb_hcd_ 所对应的 _struct hc_driver_，以执行 _hardware-specific operations_。
* 向所创建的 _struct usb_hub_ 所对应的 _struct usb_device_，获取所对应的 _usb hub status_，以执行相关操作。
* 创建一个 _struct urb_。
* 获取所传入的 _struct usb_interface_ 所对应的一个 _struct usb_host_endpoint_ 所对应的 _struct usb_endpoint_descriptor_。
* 根据所获取的 _struct usb_endpoint_descriptor_、所创建的 _struct usb_hub_ 所对应的 _struct usb_device_ 以及其他相关参数，配置所创建的 _struct urb_，以去检查所创建的 _struct usb_hub_ 上的 _struct ports_ 上是否有所对应的 _struct usb_devices_ 连接，主要包含：
    * _transfer type_。
    * _endpoint number_。
    * _direction_。
    * _data transferred from or to the specific usb device_。
    * _the specific struct usb_device from or to which the data transferred_。
    * _routine executed upon data transfer completion_。
* 根据所创建的 _struct urb_，在所传入的 _struct urb_ 所对应的 _struct usb_device_ 中，获取所对应的 _struct usb_host_endpoint_。
* 根据所获取的 _struct usb_host_endpoint_ 以及其他相关参数，配置所创建的 _struct urb_。
* 根据所创建的 _struct urb_ 所对应的 _struct usb_device_ 所对应的 _struct usb_bus_，获取所对应的 _struct usb_hcd_。
* _Is the struct usb_device for the struct urb a root hub?_
    * _yes_： 
        * 根据所创建的 _struct urb_，处理其所对应的 _data transferred from or to the specific usb device_
        * 通过所获取的 _struct usb_hcd_，以调度执行 _struct urb_ 所对应的 routine executed upon data transfer completion_。
    * _no_：
        * 执行所获取的 _struct usb_hcd_ 所对应的 _struct hc_driver_，以执行 _hardware-specific operations_，本质上完成：
            * 根据所创建的 _struct urb_，处理其所对应的 _data transferred from or to the specific usb device_
            * 通过所获取的 _struct usb_hcd_，以调度执行 _struct urb_ 所对应的 routine executed upon data transfer completion_。 
* 根据所获取的 _struct usb_hub_descriptor_，创建并配置 _struct usb_ports_。
* 根据所创建的 _struct usb_ports_，配置所创建的 _struct usb_hub_。
* 配置所创建的 _struct usb_ports_ 所对应的 _struct devices_ 所对应的 _sysfs_：
    * 所对应的 _the parent::所创建的 struct usb_hub 所对应的 struct device_。
    * 所对应的 _struct device_type::usb_port_device_type_。
    * 所对应的 _struct device_driver::usb_port_driver_。
    * 所对应的 _name::%s-port%d_。比如：_/sys/bus/usb/devices/usb1/1-0:1.0/usb1-port1_，其中的：_usb1_ 是所创建的 _struct usb_hub_ 所对应的 _struct usb_device_；_1_ 是 _the usb port number_。
* 注册所创建的 _struct usb_ports_ 所对应的 _struct devices_。
***

```C
    static void hub_event(struct work_struct *work)
```
* 根据所传入的 _struct work_struct_，获取所对应的 _struct usb_hub_。
* 当所获取的 _struct usb_hub_ 上的 _struct ports_ 上有所对应的 _struct usb_devices_ 连接时：
    * 创建并配置一个 _struct usb_device_。
    * 配置所创建的 _struct usb_device_ 所对应的 _usb_host_endpoint 0_：
        * 所对应的 _struct usb_endpoint_descriptor_。
        * 所对应的其他相关参数。
    * 配置所创建的 _struct usb_device_ 所对应的 _struct device_ 所对应的 _sysfs_，比如：
        * 所对应的 _struct bus_type::/sys/bus/usb_。
        * 所对应的 _struct device_type::usb_device_type_。
        * 所对应的 _name::%d-%d or %d-%s.%d_，比如：
            * _/sys/devices/soc0/amba/e0002000.usb/ci_hdrc.0/usb1/2-1_，其中的：_2_ 是 _usb subsystem_ 所分配的 _bus number_；_1_ 是 _the struct usb_device_ 所对应的 _struct usb_hub_ 所对应的 _struct port_。
            * _/sys/devices/soc0/amba/e0002000.usb/ci_hdrc.0/usb1/1-1/1-1.3_，其中的：_1-1_ 是 _the struct usb_device_ 所对应的 _the parent of the struct usb_device_；_3_ 是 _the struct usb_device_ 所对应的 _struct usb_hub_ 所对应的 _struct port_。
    * 根据所传入的 _struct usb_bus_，配置所创建的 _struct usb_device_。
    * 根据所创建的 _struct usb_device_ 所对应的 _struct usb_device_descriptor_，创建 _struct usb_host_configs_。
    * 根据所创建的 _struct usb_host_configs_，配置所创建的 _struct usb_device_。
    * 获取所创建的 _struct usb_device_ 所对应的 _struct usb_config_descriptors_，并根据其以及其他相关参数，配置所创建的 _struct usb_device_ 所对应的 _struct usb_host_configs_。
    * 获取所创建的 _struct usb_device_ 所对应的 _struct usb_interface_descriptors_，并根据其以及其他相关参数，配置 _struct usb_device_ 所对应的 _struct usb_host_configs_。
    * 根据所获取的 _struct usb_interface_descriptors_，创建 _struct usb_host_endpoints_。
    * 根据所创建的 _struct usb_host_endpoints_，配置所创建的 _struct usb_device_ 所对应的 _struct usb_host_configs_
    * 获取所创建的 _struct usb_device_ 所对应的 _struct usb_endpoint_descriptors_，并根据其以及其他相关参数，配置 _struct usb_device_ 所对应的 _struct usb_host_configs_ 所对应的 _struct usb_host_endpoints_。 
    * 注册所创建的 _struct usb_device_ 所对应的 _struct device_。
    * 根据所创建的 _struct usb_device_ 所对应的 _struct usb_host_endpoint 0_，创建并配置一个 _struct ep_device_。
    * 配置所创建的 _struct ep_device_ 所对应的 _struct device_ 所对应的 _sysfs_。
    * 注册所创建的 _struct ep_device_。
    * 根据所创建的 _struct ep_device_，配置所创建的 _struct usb_device_ 所对应的 _struct usb_host_endpoint 0_。 
* 执行所获取的 _struct usb_hub_ 所对应的其他相关操作。

## _for developer to use_

### _file layout_

_include/linux/usb.h_  
_drivers/usb/core/message.c_  
_drivers/usb/core/urb.c_

### _data structure_

_struct urb consisting of all relevant information to execute any USB transaction and deliver the data and status back._
```C
    struct urb {
        /* private: usb core and host controller only fields in the urb */
        struct kref kref;		/* reference count of the URB */
        void *hcpriv;			/* private data for host controller */
        atomic_t use_count;		/* concurrent submissions counter */
        atomic_t reject;		/* submissions will fail */
        int unlinked;			/* unlink error code */

        /* public: documented fields in the urb that can be used by drivers */
        struct list_head urb_list;	/* list head for use by the urb's
                        * current owner */
        struct list_head anchor_list;	/* the URB may be anchored */
        struct usb_anchor *anchor;
        struct usb_device *dev;		/* (in) pointer to associated device */
        struct usb_host_endpoint *ep;	/* (internal) pointer to endpoint */
        unsigned int pipe;		/* (in) pipe information */
        unsigned int stream_id;		/* (in) stream ID */
        int status;			/* (return) non-ISO status */
        unsigned int transfer_flags;	/* (in) URB_SHORT_NOT_OK | ...*/
        void *transfer_buffer;		/* (in) associated data buffer */
        dma_addr_t transfer_dma;	/* (in) dma addr for transfer_buffer */
        struct scatterlist *sg;		/* (in) scatter gather buffer list */
        int num_mapped_sgs;		/* (internal) mapped sg entries */
        int num_sgs;			/* (in) number of entries in the sg list */
        u32 transfer_buffer_length;	/* (in) data buffer length */
        u32 actual_length;		/* (return) actual transfer length */
        unsigned char *setup_packet;	/* (in) setup packet (control only) */
        dma_addr_t setup_dma;		/* (in) dma addr for setup_packet */
        int start_frame;		/* (modify) start frame (ISO) */
        int number_of_packets;		/* (in) number of ISO packets */
        int interval;			/* (modify) transfer interval
                        * (INT/ISO) */
        int error_count;		/* (return) number of ISO errors */
        void *context;			/* (in) context for completion */
        usb_complete_t complete;	/* (in) completion routine */
        struct usb_iso_packet_descriptor iso_frame_desc[0];
                        /* (in) ISO ONLY */
        ANDROID_KABI_RESERVE(1);
        ANDROID_KABI_RESERVE(2);
        ANDROID_KABI_RESERVE(3);
        ANDROID_KABI_RESERVE(4);
    };
```


### _api_

```C
    struct urb *usb_alloc_urb(int iso_packets, gfp_t mem_flags);
```
* 创建一个 _struct urb_。

```C
    static inline void usb_fill_control_urb(struct urb *urb,
                        struct usb_device *dev,
                        unsigned int pipe,
                        unsigned char *setup_packet,
                        void *transfer_buffer,
                        int buffer_length,
                        usb_complete_t complete_fn,
                        void *context);

    static inline void usb_fill_bulk_urb(struct urb *urb,
                        struct usb_device *dev,
                        unsigned int pipe,
                        void *transfer_buffer,
                        int buffer_length,
                        usb_complete_t complete_fn,
                        void *context);

    static inline void usb_fill_int_urb(struct urb *urb,
                        struct usb_device *dev,
                        unsigned int pipe,
                        void *transfer_buffer,
                        int buffer_length,
                        usb_complete_t complete_fn,
                        void *context,
                        int interval);        
```
* 根据所传入的相关参数，配置所传入的 _struct urb_，主要包含：
    * _transfer type_。
    * _endpoint number_。
    * _direction_。
    * _data transferred from or to the specific usb device_。
    * _the specific struct usb_device from or to which the data transferred_。
    * _routine executed upon data transfer completion_。
***

```C
    int usb_submit_urb(struct urb *urb, gfp_t mem_flags);
```
* 根据所传入的 _struct urb_，在所传入的 _struct urb_ 所对应的 _struct usb_device_ 中，获取所对应的 _struct usb_host_endpoint_。
* 根据所获取的 _struct usb_host_endpoint_ 以及其他相关参数，配置所传入的 _struct urb_。
* 根据所传入的 _struct urb_ 所对应的 _struct usb_device_ 所对应的 _struct usb_bus_，获取所对应的 _struct usb_hcd_。
* _Is the struct usb_device for the struct urb a root hub?_
    * _yes_： 
        * 根据所传入的 _struct urb_，处理其所对应的 _data transferred from or to the specific usb device_
        * 通过所获取的 _struct usb_hcd_，以调度执行 _struct urb_ 所对应的 routine executed upon data transfer completion_。
    * _no_：
        * 执行所获取的 _struct usb_hcd_ 所对应的 _struct hc_driver_，以执行 _hardware-specific operations_，本质上完成：
            * 根据所传入的 _struct urb_，处理其所对应的 _data transferred from or to the specific usb device_
            * 通过所获取的 _struct usb_hcd_，以调度执行 _struct urb_ 所对应的 routine executed upon data transfer completion_。 
*** 

# _flow_

## _for author to use_

* _author_ 定义一个 _struct usb_device_driver::usb_generic_driver_。
* _author_ 调用 [_api_](#api) 中的 _usb_register_device_driver_：
    * 配置所传入的 _struct usb_device_driver_ 所对应的 _struct device_driver_ 所对应的 _sysfs_，比如：
        * 所对应的 _name::/sys/bus/usb/drivers/usb_。
        * 所对应的 _struct bus_type::/sys/bus/usb_。
* _author_ 定义一个 _struct struct usb_driver::hub_driver_。
* _author_ 调用 [_api_](#api) 中的 _usb_register_driver_：
    * 配置所传入的 _struct usb_driver_ 所对应的 _struct device_driver_ 所对应的 _sysfs_，比如：
        * 所对应的 _name::/sys/bus/usb/drivers/hub/_。
        * 所对应的 _struct bus_type::/sys/bus/usb_。
***

* _author_ 定义一个 _struct hc_driver_。
* _author_ 调用 [_api_](#api) 中的 _usb_create_hcd_：
    * 创建并配置一个 _struct usb_hcd_。
    * 根据所传入的相关参数，配置所创建的 _struct usb_hcd_ 的 _struct usb_bus_。
    * 根据所传入的 _struct hc_driver_，配置所创建的 _struct usb_hcd_。
    * 返回所创建的 _struct usb_hcd_。
* _author_ 调用 [_api_](#api) 中的 _usb_add_hcd_：
    * 从 _usb subsystem_ 中，申请一个 _bus number_。 
    * 根据所申请的 _bus number_，配置所传入的 _struct usb_hcd_ 所对应的 _struct usb_bus_。
    * 创建并配置一个 _struct usb_device, a root hub_。
    * 配置所创建的 _struct usb_device_ 所对应的 _usb_host_endpoint 0_：
        * 所对应的 _struct usb_endpoint_descriptor_。
        * 所对应的其他相关参数。
    * 配置所创建的 _struct usb_device, a root hub_ 所对应的 _struct device_ 所对应的 _sysfs_，比如：
        * 所对应的 _struct bus_type::/sys/bus/usb_。
        * 所对应的 _struct device_type::usb_device_type_。
        * 所对应的 _name::usb%d_，比如：_/sys/devices/soc0/amba/e0002000.usb/ci_hdrc.0/usb2_，其中的 _2_ 是 _usb subsystem_ 所分配的 _bus number_。
    * 根据所传入的 _struct usb_hcd_ 所对应的 _struct usb_bus_，配置所创建的 _struct usb_device, a root hub_。
    * 根据所传入的 _struct usb_hcd_，配置所传入的 _struct usb_hcd_ 所对应的 _struct usb_bus_。
    * 执行所传入的 _struct usb_hcd_ 所对应的 _struct hc_driver_，以执行 _hardware-specific operations_。
    * 获取所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_device_descriptor_，并根据其以及其他相关参数，配置 _struct usb_device, a root hub_。
    * 根据所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_device_descriptor_，创建 _struct usb_host_configs_。
    * 根据所创建的 _struct usb_host_configs_，配置所创建的 _struct usb_device, a root hub_。
    * 获取所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_config_descriptors_，并根据其以及其他相关参数，配置所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_host_configs_。
    * 获取所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_interface_descriptors_，并根据其以及其他相关参数，配置 _struct usb_device, a root hub_ 所对应的 _struct usb_host_configs_。
    * 根据所获取的 _struct usb_interface_descriptors_，创建 _struct usb_host_endpoints_。
    * 根据所创建的 _struct usb_host_endpoints_，配置所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_host_configs_
    * 获取所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_endpoint_descriptors_，并根据其以及其他相关参数，配置 _struct usb_device, a root hub_ 所对应的 _struct usb_host_configs_ 所对应的 _struct usb_host_endpoints_。 
    * 注册所创建的 _struct usb_device, a root hub_ 所对应的 _struct device_。
    * 根据所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_host_endpoint 0_，创建并配置一个 _struct ep_device_。
    * 配置所创建的 _struct ep_device_ 所对应的 _struct device_ 所对应的 _sysfs_。
    * 注册所创建的 _struct ep_device_。
    * 根据所创建的 _struct ep_device_，配置所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_host_endpoint 0_。 

## _for subsystem to use_

* 当一个 _struct usb_device, a root hub_ 注册到 _usb subsystem_ 时，会执行 [_api_](#api) 中的 _generic_probe_：
    * 在所传入的 _struct usb_device, a root hub_ 所对应的 _struct usb_host_configs_ 中，获取一个 _struct usb_host_config_。
    * 根据所获取的 _struct usb_host_config_ 所对应的 _struct usb_interface_descriptors_ 以及其他相关参数，创建 _struct usb_interfaces_。 
    * 配置所创建的 _struct usb_interfaces_ 所对应的 _struct devices_ 所对应的 _sysfs_，比如：
        * 所对应的 _the parent::所传入的 struct usb_device, a root hub 所对应的 struct device_。
        * 所对应的 _struct bus_type::/sys/bus/usb_。
        * 所对应的 _struct device_type::usb_if_device_type_。
        * 所对应的 _name :%d-%s:%d.%d_，比如：_/sys/devices/soc0/amba/e0002000.usb/ci_hdrc.0/usb1/1-1/1-1:1.0_，其中的 _1-1_ 是 _the struct usb_interface_ 所对应的 _the struct usb_device_；_1_ 是 _the usb configuration number_；_0_ 是 _the usb interface number_。
    * 根据所获取的 _struct usb_host_config_ 所对应的 _struct usb_host_endpoints_，配置所传入的 _struct usb_device, a root hub_ 和所创建的 _struct usb_interfaces_。
    * 注册所创建的 _struct usb_interfaces_ 所对应的 _struct devices_。
    * 根据所创建的 _struct usb_device, a root hub_ 所对应的 _struct usb_host_endpoints_，创建并配置 _struct ep_devices_。
    * 配置所创建的 _struct ep_devices_ 所对应的 _struct devices_ 所对应的 _sysfs_。
    * 注册所创建的 _struct ep_devices_。
    * 根据所创建的 _struct ep_devices_，配置所传入的 _struct usb_device, a root hub_ 所对应的 _struct usb_host_endpoints_。 
***

* 当一个 _struct usb_hub_ 所对应的 _struct usb_interface_ 注册到 _usb subsystem_ 时，会执行 [_api_](#api) 中的 _hub_probe_：
    * 创建并配置一个 _struct usb_hub_。
    * 根据所传入的 _struct usb_interface_ 所对应的 _struct device_，配置所创建的 _struct usb_hub_。
    * 根据所传入的 _struct usb_interface_ 所对应的 _struct device_ 所对应的 _parent_，获取所对应的 _struct usb_device_。
    * 根据所获取的 _struct usb_device_，配置所创建的 _struct usb_hub_。
    * 根据 _void hub_event(struct work_struct *work)_，配置所创建的 _struct usb_hub_ 所对应的 _struct work_struct_。
    * 向所创建的 _struct usb_hub_ 所对应的 _struct usb_device_，获取所对应的 _struct usb_hub_descriptor_。
    * 根据所获取的 _struct usb_hub_descriptor_，配置所创建的 _struct usb_hub_。
    * 根据所获取的 _struct usb_hub_descriptor_，创建 _struct usb_ports_。
    * 获取所创建的 _struct usb_hub_ 所对应的 _struct usb_device_ 所对应的 _struct usb_bus_ 所对应的 _struct usb_hcd_。
    * 执行所获取的 _struct usb_hcd_ 所对应的 _struct hc_driver_，以执行 _hardware-specific operations_。
    * 向所创建的 _struct usb_hub_ 所对应的 _struct usb_device_，获取所对应的 _usb hub status_，以执行相关操作。
    * 创建一个 _struct urb_。
    * 获取所传入的 _struct usb_interface_ 所对应的一个 _struct usb_host_endpoint_ 所对应的 _struct usb_endpoint_descriptor_。
    * 根据所获取的 _struct usb_endpoint_descriptor_、所创建的 _struct usb_hub_ 所对应的 _struct usb_device_ 以及其他相关参数，配置所创建的 _struct urb_，以去检查所创建的 _struct usb_hub_ 上的 _struct ports_ 上是否有所对应的 _struct usb_devices_ 连接，主要包含：
        * _transfer type_。
        * _endpoint number_。
        * _direction_。
        * _data transferred from or to the specific usb device_。
        * _the specific struct usb_device from or to which the data transferred_。
        * _routine executed upon data transfer completion_。
    * 根据所创建的 _struct urb_，在所传入的 _struct urb_ 所对应的 _struct usb_device_ 中，获取所对应的 _struct usb_host_endpoint_。
    * 根据所获取的 _struct usb_host_endpoint_ 以及其他相关参数，配置所创建的 _struct urb_。
    * 根据所创建的 _struct urb_ 所对应的 _struct usb_device_ 所对应的 _struct usb_bus_，获取所对应的 _struct usb_hcd_。
    * _Is the struct usb_device for the struct urb a root hub?_
        * _yes_： 
            * 根据所创建的 _struct urb_，处理其所对应的 _data transferred from or to the specific usb device_
            * 通过所获取的 _struct usb_hcd_，以调度执行 _struct urb_ 所对应的 routine executed upon data transfer completion_。
        * _no_：
            * 执行所获取的 _struct usb_hcd_ 所对应的 _struct hc_driver_，以执行 _hardware-specific operations_，本质上完成：
                * 根据所创建的 _struct urb_，处理其所对应的 _data transferred from or to the specific usb device_
                * 通过所获取的 _struct usb_hcd_，以调度执行 _struct urb_ 所对应的 routine executed upon data transfer completion_。 
    * 根据所获取的 _struct usb_hub_descriptor_，创建并配置 _struct usb_ports_。
    * 根据所创建的 _struct usb_ports_，配置所创建的 _struct usb_hub_。
    * 配置所创建的 _struct usb_ports_ 所对应的 _struct devices_ 所对应的 _sysfs_：
        * 所对应的 _the parent::所创建的 struct usb_hub 所对应的 struct device_。 
        * 所对应的 _struct device_type::usb_port_device_type_。
        * 所对应的 _struct device_driver::usb_port_driver_。
        * 所对应的 _name::%s-port%d_。比如：_/sys/bus/usb/devices/usb1/1-0:1.0/usb1-port1_，其中的：_usb1_ 是所创建的 _struct usb_hub_ 所对应的 _struct usb_device_；_1_ 是 _the usb port number_。
    * 注册所创建的 _struct usb_ports_ 所对应的 _struct devices_。
*** 

* 当一个 _struct usb_device_ 连接到到 _the specific usb hub_ 时，会执行 [_api_](#api) 中的 _hub_event_：
    * 根据所传入的 _struct work_struct_，获取所对应的 _struct usb_hub_。
    * 当所获取的 _struct usb_hub_ 上的 _struct ports_ 上有所对应的 _struct usb_devices_ 连接时：
        * 创建并配置一个 _struct usb_device_。
        * 配置所创建的 _struct usb_device_ 所对应的 _usb_host_endpoint 0_：
            * 所对应的 _struct usb_endpoint_descriptor_。
            * 所对应的其他相关参数。
        * 配置所创建的 _struct usb_device_ 所对应的 _struct device_ 所对应的 _sysfs_，比如：
            * 所对应的 _struct bus_type::/sys/bus/usb_。
            * 所对应的 _struct device_type::usb_device_type_。
            * 所对应的 _name::%d-%d or %d-%s.%d_，比如：
                * _/sys/devices/soc0/amba/e0002000.usb/ci_hdrc.0/usb1/2-1_，其中的：_2_ 是 _usb subsystem_ 所分配的 _bus number_；_1_ 是 _the struct usb_device_ 所对应的 _struct usb_hub_ 所对应的 _struct port_。
                * _/sys/devices/soc0/amba/e0002000.usb/ci_hdrc.0/usb1/1-1/1-1.3_，其中的：_1-1_ 是 _the struct usb_device_ 所对应的 _the parent of the struct usb_device_；_3_ 是 _the struct usb_device_ 所对应的 _struct usb_hub_ 所对应的 _struct port_。
        * 根据所传入的 _struct usb_bus_，配置所创建的 _struct usb_device_。
        * 根据所创建的 _struct usb_device_ 所对应的 _struct usb_device_descriptor_，创建 _struct usb_host_configs_。
        * 根据所创建的 _struct usb_host_configs_，配置所创建的 _struct usb_device_。
        * 获取所创建的 _struct usb_device_ 所对应的 _struct usb_config_descriptors_，并根据其以及其他相关参数，配置所创建的 _struct usb_device_ 所对应的 _struct usb_host_configs_。
        * 获取所创建的 _struct usb_device_ 所对应的 _struct usb_interface_descriptors_，并根据其以及其他相关参数，配置 _struct usb_device_ 所对应的 _struct usb_host_configs_。
        * 根据所获取的 _struct usb_interface_descriptors_，创建 _struct usb_host_endpoints_。
        * 根据所创建的 _struct usb_host_endpoints_，配置所创建的 _struct usb_device_ 所对应的 _struct usb_host_configs_
        * 获取所创建的 _struct usb_device_ 所对应的 _struct usb_endpoint_descriptors_，并根据其以及其他相关参数，配置 _struct usb_device_ 所对应的 _struct usb_host_configs_ 所对应的 _struct usb_host_endpoints_。 
        * 注册所创建的 _struct usb_device_ 所对应的 _struct device_。
        * 根据所创建的 _struct usb_device_ 所对应的 _struct usb_host_endpoint 0_，创建并配置一个 _struct ep_device_。
        * 配置所创建的 _struct ep_device_ 所对应的 _struct device_ 所对应的 _sysfs_。
        * 注册所创建的 _struct ep_device_。
        * 根据所创建的 _struct ep_device_，配置所创建的 _struct usb_device_ 所对应的 _struct usb_host_endpoint 0_。 
    * 执行所获取的 _struct usb_hub_ 所对应的其他相关操作。
***

* 当一个 _struct usb_device_ 注册到 _usb subsystem_ 时，会执行 [_api_](#api) 中的 _generic_probe_：
    * 在所传入的 _struct usb_device_ 所对应的 _struct usb_host_configs_ 中，获取一个 _struct usb_host_config_。
    * 根据所获取的 _struct usb_host_config_ 所对应的 _struct usb_interface_descriptors_ 以及其他相关参数，创建 _struct usb_interfaces_。 
    * 配置所创建的 _struct usb_interfaces_ 所对应的 _struct devices_ 所对应的 _sysfs_，比如：
        * 所对应的 _the parent::所传入的 struct usb_device 所对应的 struct device_。
        * 所对应的 _struct bus_type::/sys/bus/usb_。
        * 所对应的 _struct device_type::usb_if_device_type_。
        * 所对应的 _name :%d-%s:%d.%d_，比如：_/sys/devices/soc0/amba/e0002000.usb/ci_hdrc.0/usb1/1-1/1-1:1.0_，其中的 _1-1_ 是 _the struct usb_interface_ 所对应的 _the struct usb_device_；_1_ 是 _the usb configuration number_；_0_ 是 _the usb interface number_。
    * 根据所获取的 _struct usb_host_config_ 所对应的 _struct usb_host_endpoints_，配置所传入的 _struct usb_device_ 和所创建的 _struct usb_interfaces_。
    * 注册所创建的 _struct usb_interfaces_ 所对应的 _struct devices_。
    * 根据所创建的 _struct usb_device_ 所对应的 _struct usb_host_endpoints_，创建并配置 _struct ep_devices_。
    * 配置所创建的 _struct ep_devices_ 所对应的 _struct devices_ 所对应的 _sysfs_。
    * 注册所创建的 _struct ep_devices_。
    * 根据所创建的 _struct ep_devices_，配置所传入的 _struct usb_device_ 所对应的 _struct usb_host_endpoints_。 

## _for developer to use_

* _developer_ 调用 [_api_](#api-1) 中的 _usb_alloc_urb_：
    * 创建一个 _struct urb_。
* _developer_ 调用 [_api_](#api-1) 中的 _usb_fill_control_urb_、_usb_fill_bulk_urb_ 和 _usb_fill_int_urb_：
    * 根据所传入的相关参数，配置所传入的 _struct urb_，主要包含：
        * _transfer type_。
        * _endpoint number_。
        * _direction_。
        * _data transferred from or to the specific usb device_。
        * _the specific struct usb_device from or to which the data transferred_。
        * _routine executed upon data transfer completion_。 
* _developer_ 调用 [_api_](#api-1) 中的 _usb_submit_urb_：
    * 根据所传入的 _struct urb_，在所传入的 _struct urb_ 所对应的 _struct usb_device_ 中，获取所对应的 _struct usb_host_endpoint_。
    * 根据所获取的 _struct usb_host_endpoint_ 以及其他相关参数，配置所传入的 _struct urb_。
    * 根据所传入的 _struct urb_ 所对应的 _struct usb_device_ 所对应的 _struct usb_bus_，获取所对应的 _struct usb_hcd_。
    * _Is the struct usb_device for the struct urb a root hub?_
        * _yes_： 
            * 根据所传入的 _struct urb_，处理其所对应的 _data transferred from or to the specific usb device_
            * 通过所获取的 _struct usb_hcd_，以调度执行 _struct urb_ 所对应的 routine executed upon data transfer completion_。
        * _no_：
            * 执行所获取的 _struct usb_hcd_ 所对应的 _struct hc_driver_，以执行 _hardware-specific operations_，本质上完成：
                * 根据所传入的 _struct urb_，处理其所对应的 _data transferred from or to the specific usb device_
                * 通过所获取的 _struct usb_hcd_，以调度执行 _struct urb_ 所对应的 routine executed upon data transfer completion_。 