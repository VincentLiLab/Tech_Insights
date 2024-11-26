- [_overview_](#overview)
- [_detail_](#detail)
  - [_for author to use_](#for-author-to-use)
    - [_file layout_](#file-layout)
    - [_data structure_](#data-structure)
    - [_api_](#api)
- [_flow_](#flow)
  - [_for author to use_](#for-author-to-use-1)
  - [_for framework to use_](#for-framework-to-use)


# _overview_

_driver-model framework_ 负责探测 _device_ 和 _driver_ 何时添加、何时删除和是否匹配...，并通过 _sysfs_ 来将 _driver-model_ 的层次结构输出到了用户空间。_driver-model framework_ 简化了流程，但是具体核心操作是仍然是 _struct file_operations_ 来完成的，注意没有它也是可以的，比如：一些 _legacy driver_ 就没有涉及 _driver-model framework_。

_driver-model framework_ 是对 _kernel_ 中先前使用的各种不同 _driver model_ 的统一。目的是通过将相关的数据和操作合并到相关的全局可访问的 _data structure_ 中，比如：_struct bus_type_、_struct device_、_struct device_driver_ 和 _struct class_，以增强 _bridge_ 和 _device_ 所对应的 _bus-specific driver_，比如：增强 _pci_ 所对应的 _struct pci_driver_。

_struct bus_type_ 可以在不牺牲 _bus-specific_ 功能或 _type-safety_ 的情况下，将所其下所挂载的 _struct device_ 与特定的 _struct device_driver_ 进行匹配。_struct device_driver_ 是静态分配的，一个 _struct device_driver_ 可以对应有多个 _struct device_。

通常的做法是：_bridge_ 和 _device_ 会定义所对应的 _bus-specific device containing struct device_，比如：_struct pci_dev_，和所对应的 _bus-specific driver containing struct device_driver_，比如：_struct pci_driver_，其中的 _struct device_driver_ 的 _callback_ 会调用所对应的 _bus-specific driver_ 的 _callback_，以去操作所传入的 _struct device_ 所对应的 _bus-specific device_。注意这只是过渡，只是为了减少基础架构的复杂性和代码大小，最终会只使用 _struct device_ 和 _struct device_driver_ 而不使用 _bus-specific device_ 和 _bus-specific driver_。

# _detail_

## _for author to use_

### _file layout_

_include/linux/device.h_  
_drivers/base/bus.c_  
_drivers/base/core.c_

### _data structure_

_struct bus_type to represent a bus_  
```C
    struct bus_type {
        const char		*name;
        const char		*dev_name;
        struct device		*dev_root;
        const struct attribute_group **bus_groups;
        const struct attribute_group **dev_groups;
        const struct attribute_group **drv_groups;

        int (*match)(struct device *dev, struct device_driver *drv);
        int (*uevent)(struct device *dev, struct kobj_uevent_env *env);
        int (*probe)(struct device *dev);
        void (*sync_state)(struct device *dev);
        int (*remove)(struct device *dev);
        void (*shutdown)(struct device *dev);

        int (*online)(struct device *dev);
        int (*offline)(struct device *dev);

        int (*suspend)(struct device *dev, pm_message_t state);
        int (*resume)(struct device *dev);

        int (*num_vf)(struct device *dev);

        int (*dma_configure)(struct device *dev);

        const struct dev_pm_ops *pm;

        const struct iommu_ops *iommu_ops;

        struct subsys_private *p;
            // device-list
            // driver-list
        struct lock_class_key lock_key;

        bool need_parent_lock;

        ANDROID_KABI_RESERVE(1);
        ANDROID_KABI_RESERVE(2);
        ANDROID_KABI_RESERVE(3);
        ANDROID_KABI_RESERVE(4);
    };
``` 
***

_struct device to represent a device_  
```C
    struct device {
        struct device		*parent;

        struct device_private	*p;

        struct kobject kobj;
        const char		*init_name; /* initial name of the device */
        const struct device_type *type;

        struct mutex		mutex;	/* mutex to synchronize calls to
                        * its driver.
                        */

        struct bus_type	*bus;		/* type of bus device is on */
        struct device_driver *driver;	/* which driver has allocated this
                        device */
        void		*platform_data;	/* Platform specific data, device
                        core doesn't touch it */
        void		*driver_data;	/* Driver data, set and get with
                        dev_set/get_drvdata */
        struct dev_links_info	links;
        struct dev_pm_info	power;
        struct dev_pm_domain	*pm_domain;

    #ifdef CONFIG_GENERIC_MSI_IRQ_DOMAIN
        struct irq_domain	*msi_domain;
    #endif
    #ifdef CONFIG_PINCTRL
        struct dev_pin_info	*pins;
    #endif
    #ifdef CONFIG_GENERIC_MSI_IRQ
        struct list_head	msi_list;
    #endif

    #ifdef CONFIG_NUMA
        int		numa_node;	/* NUMA node this device is close to */
    #endif
        const struct dma_map_ops *dma_ops;
        u64		*dma_mask;	/* dma mask (if dma'able device) */
        u64		coherent_dma_mask;/* Like dma_mask, but for
                            alloc_coherent mappings as
                            not all hardware supports
                            64 bit addresses for consistent
                            allocations such descriptors. */
        u64		bus_dma_mask;	/* upstream dma_mask constraint */
        unsigned long	dma_pfn_offset;

        struct device_dma_parameters *dma_parms;

        struct list_head	dma_pools;	/* dma pools (if dma'ble) */

        struct dma_coherent_mem	*dma_mem; /* internal for coherent mem
                            override */
    #ifdef CONFIG_DMA_CMA
        struct cma *cma_area;		/* contiguous memory area for dma
                        allocations */
    #endif
        struct removed_region *removed_mem;
        /* arch specific additions */
        struct dev_archdata	archdata;

        struct device_node	*of_node; /* associated device tree node */
        struct fwnode_handle	*fwnode; /* firmware device node */

        dev_t			devt;	/* dev_t, creates the sysfs "dev" */
        u32			id;	/* device instance */

        spinlock_t		devres_lock;
        struct list_head	devres_head;

        struct klist_node	knode_class;
        struct class		*class;
        const struct attribute_group **groups;	/* optional groups */

        void	(*release)(struct device *dev);
        struct iommu_group	*iommu_group;
        struct iommu_fwspec	*iommu_fwspec;

        bool			offline_disabled:1;
        bool			offline:1;
        bool			of_node_reused:1;
        bool			state_synced:1;

        ANDROID_KABI_RESERVE(1);
        ANDROID_KABI_RESERVE(2);
        ANDROID_KABI_RESERVE(3);
        ANDROID_KABI_RESERVE(4);
        ANDROID_KABI_RESERVE(5);
        ANDROID_KABI_RESERVE(6);
        ANDROID_KABI_RESERVE(7);
        ANDROID_KABI_RESERVE(8);
    };
```
***

_struct device_driver to represent a driver_
```C
    struct device_driver {
        const char		*name;
        struct bus_type		*bus;

        struct module		*owner;
        const char		*mod_name;	/* used for built-in modules */

        bool suppress_bind_attrs;	/* disables bind/unbind via sysfs */
        enum probe_type probe_type;

        const struct of_device_id	*of_match_table;
        const struct acpi_device_id	*acpi_match_table;

        int (*probe) (struct device *dev);
        void (*sync_state)(struct device *dev);
        int (*remove) (struct device *dev);
        void (*shutdown) (struct device *dev);
        int (*suspend) (struct device *dev, pm_message_t state);
        int (*resume) (struct device *dev);
            // 尽可能多地实现这些 _callback_，虽然每个都是可选的
        const struct attribute_group **groups;

        const struct dev_pm_ops *pm;
        void (*coredump) (struct device *dev);

        struct driver_private *p;

        ANDROID_KABI_RESERVE(1);
        ANDROID_KABI_RESERVE(2);
        ANDROID_KABI_RESERVE(3);
        ANDROID_KABI_RESERVE(4);
    };
```

### _api_

```C
    int bus_register(struct bus_type * bus);
```
* 注册一个 _struct bus_type_。
***

```C
    int device_register(struct device *dev);
```
* 注册一个挂载在指定的 _struct bus_type_ 下的 _struct device_。
***

```C
    int driver_register(struct device_driver * drv);
```
* 注册一个挂载在指定的 _struct bus_type_ 下的 _struct device_driver_。
***

# _flow_

## _for author to use_

* _author_ 调用 [api](#api) 中的 _bus_register_：  
    * 注册一个 _struct bus_type_，比如：_/sys/bus/i2c_、_/sys/bus/platform_ 和 _/sys/bus/usb_...。
***
* _author_ 调用 [api](#api) 中的 _driver_register_：  
    * 注册一个挂载在指定的 _struct bus_type_ 下的 _struct device_driver_。
***
* _author_ 调用 [api](#api) 中的 _device_register_：  
    * 注册一个挂载在指定的 _struct bus_type_ 下的 _struct device_。

## _for framework to use_

* _driver-model framework_ 遍历一个 _struct bus_type_，将所其下所挂载的所有 _struct device_ 与特定的 _struct device_driver_ 进行匹配，并生成相应的 _sysfs_。