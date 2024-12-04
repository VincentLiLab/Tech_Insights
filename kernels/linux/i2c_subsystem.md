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

_i2c subsystem_ 负责管理 _platform_ 中的所有 _i2c controller_ 中的所有 _i2c slave device_。_author_ 需要为每个 _i2c controller_ 都分别定义：一个所对应的 _struct algorithm_，以执行所对应的 _hardware-specific operations_；一个所对应的 _i2c_adapter_，以用于识别和访问 _the specific physical i2c, the specific i2c controller_。_developer_ 需要每个 _i2c controller_ 中的每个 _i2c slave device_ 都定义：一个所对应的 _struct i2c_client_，以表示这个 _i2c slave device_；一个所对应的 _struct i2c_driver_，以管理所对应的 _struct i2c_client_。_the specific struct i2c_driver_ 或 _developer_ 需要通过所对应的 _struct i2c_client_ 所对应的 _i2c_adapter_ 所对应的 _struct algorithm_ 执行所对应的 _hardware-specific operations_。

# _detail_

## _for author to use_

### _file layout_

_include/linux/i2c.h_  
_drivers/i2c/i2c-core-base.c_

### _data structure_

_struct i2c_algorithm to perform the hardware-specific operations_
```C
    struct i2c_algorithm {
        /* If an adapter algorithm can't do I2C-level access, set master_xfer
        to NULL. If an adapter algorithm can do SMBus access, set
        smbus_xfer. If set to NULL, the SMBus protocol is simulated
        using common I2C messages */
        /* master_xfer should return the number of messages successfully
        processed, or a negative value on error */
        int (*master_xfer)(struct i2c_adapter *adap, struct i2c_msg *msgs,
                int num);
        int (*smbus_xfer) (struct i2c_adapter *adap, u16 addr,
                unsigned short flags, char read_write,
                u8 command, int size, union i2c_smbus_data *data);

        /* To determine what the adapter supports */
        u32 (*functionality) (struct i2c_adapter *);

    #if IS_ENABLED(CONFIG_I2C_SLAVE)
        int (*reg_slave)(struct i2c_client *client);
        int (*unreg_slave)(struct i2c_client *client);
    #endif
    };
```
***

_struct i2c_adapter used to identify a physical i2c bus along with the access algorithms necessary to access it_
```C
    struct i2c_adapter {
        struct module *owner;
        unsigned int class;		  /* classes to allow probing for */
        const struct i2c_algorithm *algo; /* the algorithm to access the bus */
        void *algo_data;

        /* data fields that are valid for all devices	*/
        const struct i2c_lock_operations *lock_ops;
        struct rt_mutex bus_lock;
        struct rt_mutex mux_lock;

        int timeout;			/* in jiffies */
        int retries;
        struct device dev;		/* the adapter device */

        int nr;
        char name[48];
        struct completion dev_released;

        struct mutex userspace_clients_lock;
        struct list_head userspace_clients;

        struct i2c_bus_recovery_info *bus_recovery_info;
        const struct i2c_adapter_quirks *quirks;

        struct irq_domain *host_notify_domain;
    };
```
***

_struct i2c_client to represent an i2c slave device_
```C
    struct i2c_client {
        unsigned short flags;		/* div., see below		*/
        unsigned short addr;		/* chip address - NOTE: 7bit	*/
                        /* addresses are stored in the	*/
                        /* _LOWER_ 7 bits		*/
        char name[I2C_NAME_SIZE];
        struct i2c_adapter *adapter;	/* the adapter we sit on	*/
        struct device dev;		/* the device structure		*/
        int init_irq;			/* irq set at initialization	*/
        int irq;			/* irq issued by device		*/
        struct list_head detected;
    #if IS_ENABLED(CONFIG_I2C_SLAVE)
        i2c_slave_cb_t slave_cb;	/* callback for slave mode	*/
    #endif
    };
```
***

_struct i2c_driver to manage the struct i2c_client_
```C
    struct i2c_driver {
        unsigned int class;

        /* Standard driver model interfaces */
        int (*probe)(struct i2c_client *, const struct i2c_device_id *);
        int (*remove)(struct i2c_client *);

        /* New driver model interface to aid the seamless removal of the
        * current probe()'s, more commonly unused than used second parameter.
        */
        int (*probe_new)(struct i2c_client *);

        /* driver model interfaces that don't relate to enumeration  */
        void (*shutdown)(struct i2c_client *);

        /* Alert callback, for example for the SMBus alert protocol.
        * The format and meaning of the data value depends on the protocol.
        * For the SMBus alert protocol, there is a single bit of data passed
        * as the alert response's low bit ("event flag").
        * For the SMBus Host Notify protocol, the data corresponds to the
        * 16-bit payload data reported by the slave device acting as master.
        */
        void (*alert)(struct i2c_client *, enum i2c_alert_protocol protocol,
                unsigned int data);

        /* a ioctl like command that can be used to perform specific functions
        * with the device.
        */
        int (*command)(struct i2c_client *client, unsigned int cmd, void *arg);

        struct device_driver driver;
        const struct i2c_device_id *id_table;

        /* Device detection callback for automatic device creation */
        int (*detect)(struct i2c_client *, struct i2c_board_info *);
        const unsigned short *address_list;
        struct list_head clients;

        bool disable_i2c_core_irq_mapping;
    };
```

### _api_

```C
    int i2c_add_adapter(struct i2c_adapter *adapter);
```
* 配置所传入的 _struct i2c_adapter_ 所对应的 _struct device_ 所对应的 _sysfs_，比如：
    * 所对应的 _name::_i2c-%d_，比如：_/sys/bus/i2c/devices/i2c-0_，其中的 _0_ 是 _i2c subsystem_ 所分配的 _bus number_。
    * 所对应的 _struct bus_type::/sys/bus/i2c_。
* 注册所传入的 _struct i2c_adapter_ 所对应的 _struct device_。
* 获取所传入的 _struct i2c_adapter_ 所对应的 _struct device_ 所对应的 _node_
* 遍历所获取的 _node_ 所对应的 _child nodes_:
    * 创建一个 _struct client_。
    * 根据所传入的 _struct i2c_adapter_ 以及其他相关参数，配置所创建的 _struct client_。
    * 配置所创建的 _struct client_ 所对应的 _struct device_，比如：所对应的 :/sys/bus/i2c_。
    * 注册所创建的 _struct client_ 所对应的 _struct device_。
***

```C
    #define i2c_add_driver(driver) \
	    i2c_register_driver(THIS_MODULE, driver)
```
* 配置所传入的 _struct i2c_driver_ 所对应的 _struct device_driver_ 所对应的 _sysfs_，比如：
    * 所对应的 _name_。
    * 所对应的 _struct bus_type::/sys/bus/i2c_。
* 注册所传入的 _struct i2c_driver_ 所对应的 _struct device_driver_。
    
## _for developer to use_

### _file layout_

_include/linux/i2c.h_

### _data structure_

### _api_

```C
    static inline int i2c_master_send(const struct i2c_client *client,
                    const char *buf, int count);
    static inline int i2c_master_recv(const struct i2c_client *client,
                    char *buf, int count);
```
* 根据所传入的 _struct i2c_client_，获取所对应的 _struct i2c_algorithm_。
* 根据所传入的相关参数，执行所传入的 struct i2c_client_ 所对应的 _struct i2c_algorithm_，以执行 _hardware-specific operations_。

# _flow_

## _for author to use_

* _author_ 定义一个 _struct i2c_driver_。
* _author_ 调用 [api](#api) 中的 _i2c_add_driver_：
    * 配置所传入的 _struct i2c_driver_ 所对应的 _struct device_driver_ 所对应的 _sysfs_，比如：
        * 所对应的 _name_。
        * 所对应的 _struct bus_type::/sys/bus/i2c_。
    * 注册所传入的 _struct i2c_driver_ 所对应的 _struct device_driver_。
***

* _author_ 定义一个 _struct i2c_algorithm_。
* _author_ 定义一个 _struct i2c_adapter_。
* 根据所定义的 _struct i2c_algorithm_ 以及其他相关参数，配置 _struct i2c_adapter_。
* _author_ 调用 [api](#api) 中的 _i2c_add_adapter_：
    * 配置所传入的 _struct i2c_adapter_ 所对应的 _struct device_ 所对应的 _sysfs_，比如：
        * 所对应的 _name::i2c-%d_，比如：_/sys/bus/i2c/devices/i2c-0_，其中的 _0_ 是 _i2c subsystem_ 所分配的 _bus number_。
        * 所对应的 _struct bus_type::/sys/bus/i2c_。
    * 注册所传入的 _struct i2c_adapter_ 所对应的 _struct device_。
    * 获取所传入的 _struct i2c_adapter_ 所对应的 _struct device_ 所对应的 _node_
    * 遍历所获取的 _node_ 所对应的 _child nodes_:
        * 创建一个 _struct client_。
        * 根据所传入的 _struct i2c_adapter_ 以及其他相关参数，配置所创建的 _struct client_。
        * 配置所创建的 _struct client_ 所对应的 _struct device_，比如：所对应的 :/sys/bus/i2c_。
        * 注册所创建的 _struct client_ 所对应的 _struct device_。

## _for subsystem to use_

* _driver-model subsystem_ 遍历 _struct bus_type::/sys/bus/i2c_，将所其下所挂载的所有 _struct i2c_client_ 与特定的 _struct i2c_driver_ 进行匹配，并生成相应的 _sysfs_。

## _for developer to use_

* _developer_ 调用 [api](#api-1) 中的 _i2c_master_send_ 或 _i2c_master_recv_：
    * 根据所传入的 _struct i2c_client_，获取所对应的 _struct i2c_algorithm_。
    * 根据所传入的相关参数，执行所传入的 struct i2c_client_ 所对应的 _struct i2c_algorithm_，以执行 _hardware-specific operations_。 