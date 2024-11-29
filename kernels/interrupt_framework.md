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
  - [_for developer to use_](#for-developer-to-use-1)
  - [_for framework to use_](#for-framework-to-use)


# _overview_

_interrupt framework_ 负责管理 _platform_ 中的所有 _interrupt controller_ 中的所有 _interrupt_。_author_ 需要为每个 _interrupt controller_ 都分别定义：一个所对应的 _struct irq_chip_，以执行所对应的 _hardware-specific operations_；一个所对应的 _handle_irq_，以做为 _top-level irq flow handler_；一个所对应的 _struct irq_domain_ops_，以被 _interrupt framework_ 为每个 _interrupt controller_ 而所创建的一个 _struct irq_domain_ 所使用，以管理所对应的 _interrupt controller tree topology_ 和 _hwirq number which is a controller-local interrupt number_ 和 _irq number which is an index into the global irq_desc array and an enumeration of the possible interrupt sources on a machine_ 之间的映射，_hwirq number_ 在不同的 _interrupt controller_ 中是可以重复的，而 _irq number_ 则是全局唯一的。_interrupt framework_ 会为每个 _irq number_ 都创建：一个所对应的 _struct irq_desc_，以做为所对应的 _interrupt descriptor_；一个所对应的 _struct irq_data_，以包含所对应的 _struct irq_chip_ 所要使用的相关信息；一个所对应的 _irq_flow_handler_t_，以做为 _high-level irq flow handler_；一个 _struct irqaction_，以做为所对应的 _interrupt action descriptor_。_developer_ 需要为每个 _irq number_ 都定义一个或多个 _irq_handler_t_，以做为 _low-level irq flow handler or thread_。

# _detail_

## _for author to use_

### _file layout_

_include/linux/irq.h_
_include/linux/irqdesc.h_  
_include/linux/irqdomain.h_
_kernel/irq/chip.c_  
_kernel/irq/irqdesc.c_

### _data structure_

_struct irq_domain_ops, the methods for the struct irq_domain_
```C
    struct irq_domain_ops {
        int (*match)(struct irq_domain *d, struct device_node *node,
                enum irq_domain_bus_token bus_token);
        int (*select)(struct irq_domain *d, struct irq_fwspec *fwspec,
                enum irq_domain_bus_token bus_token);
        int (*map)(struct irq_domain *d, unsigned int virq, irq_hw_number_t hw);
        void (*unmap)(struct irq_domain *d, unsigned int virq);
        int (*xlate)(struct irq_domain *d, struct device_node *node,
                const u32 *intspec, unsigned int intsize,
                unsigned long *out_hwirq, unsigned int *out_type);
    #ifdef	CONFIG_IRQ_DOMAIN_HIERARCHY
        /* extended V2 interfaces to support hierarchy irq_domains */
        int (*alloc)(struct irq_domain *d, unsigned int virq,
                unsigned int nr_irqs, void *arg);
        void (*free)(struct irq_domain *d, unsigned int virq,
                unsigned int nr_irqs);
        int (*activate)(struct irq_domain *d, struct irq_data *irqd, bool reserve);
        void (*deactivate)(struct irq_domain *d, struct irq_data *irq_data);
        int (*translate)(struct irq_domain *d, struct irq_fwspec *fwspec,
                unsigned long *out_hwirq, unsigned int *out_type);
    #endif
    #ifdef CONFIG_GENERIC_IRQ_DEBUGFS
        void (*debug_show)(struct seq_file *m, struct irq_domain *d,
                struct irq_data *irqd, int ind);
    #endif
};
```
***

_struct irq_domain to manage mapping between hwirq numbers and irq numbers and to make up the core of the interrupt controller tree topology_
```C
    struct irq_domain {
        struct list_head link;
        const char *name;
        const struct irq_domain_ops *ops;
        void *host_data;
        unsigned int flags;
        unsigned int mapcount;

        /* Optional data */
        struct fwnode_handle *fwnode;
        enum irq_domain_bus_token bus_token;
        struct irq_domain_chip_generic *gc;
    #ifdef	CONFIG_IRQ_DOMAIN_HIERARCHY
        struct irq_domain *parent;
    #endif
    #ifdef CONFIG_GENERIC_IRQ_DEBUGFS
        struct dentry		*debugfs_file;
    #endif

        ANDROID_KABI_RESERVE(1);
        ANDROID_KABI_RESERVE(2);
        ANDROID_KABI_RESERVE(3);
        ANDROID_KABI_RESERVE(4);

        /* reverse map data. The linear map gets appended to the irq_domain */
        irq_hw_number_t hwirq_max;
        unsigned int revmap_direct_max_irq;
        unsigned int revmap_size;
        struct radix_tree_root revmap_tree;
        struct mutex revmap_tree_mutex;
        unsigned int linear_revmap[];
    };
```
***

_struct irq_chip to perform the hardware-specific operations_
```C
    struct irq_chip {
        struct device	*parent_device;
        const char	*name;
        unsigned int	(*irq_startup)(struct irq_data *data);
        void		(*irq_shutdown)(struct irq_data *data);
        void		(*irq_enable)(struct irq_data *data);
        void		(*irq_disable)(struct irq_data *data);

        void		(*irq_ack)(struct irq_data *data);
        void		(*irq_mask)(struct irq_data *data);
        void		(*irq_mask_ack)(struct irq_data *data);
        void		(*irq_unmask)(struct irq_data *data);
        void		(*irq_eoi)(struct irq_data *data);

        int		(*irq_set_affinity)(struct irq_data *data, const struct cpumask *dest, bool force);
        int		(*irq_retrigger)(struct irq_data *data);
        int		(*irq_set_type)(struct irq_data *data, unsigned int flow_type);
        int		(*irq_set_wake)(struct irq_data *data, unsigned int on);

        void		(*irq_bus_lock)(struct irq_data *data);
        void		(*irq_bus_sync_unlock)(struct irq_data *data);

        void		(*irq_cpu_online)(struct irq_data *data);
        void		(*irq_cpu_offline)(struct irq_data *data);

        void		(*irq_suspend)(struct irq_data *data);
        void		(*irq_resume)(struct irq_data *data);
        void		(*irq_pm_shutdown)(struct irq_data *data);

        void		(*irq_calc_mask)(struct irq_data *data);

        void		(*irq_print_chip)(struct irq_data *data, struct seq_file *p);
        int		(*irq_request_resources)(struct irq_data *data);
        void		(*irq_release_resources)(struct irq_data *data);

        void		(*irq_compose_msi_msg)(struct irq_data *data, struct msi_msg *msg);
        void		(*irq_write_msi_msg)(struct irq_data *data, struct msi_msg *msg);

        int		(*irq_get_irqchip_state)(struct irq_data *data, enum irqchip_irq_state which, bool *state);
        int		(*irq_set_irqchip_state)(struct irq_data *data, enum irqchip_irq_state which, bool state);

        int		(*irq_set_vcpu_affinity)(struct irq_data *data, void *vcpu_info);

        void		(*ipi_send_single)(struct irq_data *data, unsigned int cpu);
        void		(*ipi_send_mask)(struct irq_data *data, const struct cpumask *dest);

        unsigned long	flags;
    };
```
***

_handle_irq, top-level irq flow handler_
```C
	void (*handle_irq)(struct pt_regs *);
```

### _api_

```C
    struct irq_domain *__irq_domain_add(struct fwnode_handle *fwnode, int size,
                        irq_hw_number_t hwirq_max, int direct_max,
                        const struct irq_domain_ops *ops,
                        void *host_data)
```
* 根据传入的 _struct irq_domain_ops_ 及其相关参数，创建并配置一个 _struct irq_domain_。
* 根据传入的 _interrupt controller node_，注册所创建的 _struct irq_domain_。
***

```C
	int __init set_handle_irq(void (*handle_irq)(struct pt_regs *));
```
* 根据传入的参数，注册 _handle_irq_。

## _for developer to use_

### _file layout_

_include/linux/interrupt.h_  
_include/linux/irq.h_ 
_kernel/irq/manage.c_   
_kernel/irq/chip.c_  

### _data structure_

_struct irq_data containing informations used by the struct irq_chip_
```C
    struct irq_data {
        u32			mask;
        unsigned int		irq;
        unsigned long		hwirq;
        struct irq_common_data	*common;
        struct irq_chip		*chip;
        struct irq_domain	*domain;
    #ifdef	CONFIG_IRQ_DOMAIN_HIERARCHY
        struct irq_data		*parent_data;
    #endif
        void			*chip_data;
    };
```
***

_irq_flow_handler_t, high-level irq flow handler_
```C
    typedef	void (*irq_flow_handler_t)(struct irq_desc *desc);
```
***

_struct irq_desc, interrupt descriptor_
```C
    struct irq_desc {
        struct irq_common_data	irq_common_data;
        struct irq_data		irq_data;
        unsigned int __percpu	*kstat_irqs;
        irq_flow_handler_t	handle_irq;
    #ifdef CONFIG_IRQ_PREFLOW_FASTEOI
        irq_preflow_handler_t	preflow_handler;
    #endif
        struct irqaction	*action;	/* IRQ action list */
        unsigned int		status_use_accessors;
        unsigned int		core_internal_state__do_not_mess_with_it;
        unsigned int		depth;		/* nested irq disables */
        unsigned int		wake_depth;	/* nested wake enables */
        unsigned int		tot_count;
        unsigned int		irq_count;	/* For detecting broken IRQs */
        unsigned long		last_unhandled;	/* Aging timer for unhandled count */
        unsigned int		irqs_unhandled;
        atomic_t		threads_handled;
        int			threads_handled_last;
        raw_spinlock_t		lock;
        struct cpumask		*percpu_enabled;
        const struct cpumask	*percpu_affinity;
    #ifdef CONFIG_SMP
        const struct cpumask	*affinity_hint;
        struct irq_affinity_notify *affinity_notify;
    #ifdef CONFIG_GENERIC_PENDING_IRQ
        cpumask_var_t		pending_mask;
    #endif
    #endif
        unsigned long		threads_oneshot;
        atomic_t		threads_active;
        wait_queue_head_t       wait_for_threads;
    #ifdef CONFIG_PM_SLEEP
        unsigned int		nr_actions;
        unsigned int		no_suspend_depth;
        unsigned int		cond_suspend_depth;
        unsigned int		force_resume_depth;
    #endif
    #ifdef CONFIG_PROC_FS
        struct proc_dir_entry	*dir;
    #endif
    #ifdef CONFIG_GENERIC_IRQ_DEBUGFS
        struct dentry		*debugfs_file;
        const char		*dev_name;
    #endif
    #ifdef CONFIG_SPARSE_IRQ
        struct rcu_head		rcu;
        struct kobject		kobj;
    #endif
        struct mutex		request_mutex;
        int			parent_irq;
        struct module		*owner;
        const char		*name;
    } ____cacheline_internodealigned_in_smp;

```
***

_irq_handler_t, low-level irq flow handler or thread_  
```C
    typedef irqreturn_t (*irq_handler_t)(int, void *);
```
***

_struct irqaction, interrupt action descriptor_
```C
    struct irqaction {
        irq_handler_t		handler;
        void			*dev_id;
        void __percpu		*percpu_dev_id;
        struct irqaction	*next;
        irq_handler_t		thread_fn;
        struct task_struct	*thread;
        struct irqaction	*secondary;
        unsigned int		irq;
        unsigned int		flags;
        unsigned long		thread_flags;
        unsigned long		thread_mask;
        const char		*name;
        struct proc_dir_entry	*dir;
    } ____cacheline_internodealigned_in_smp;
```

### _api_

```C
    int of_irq_get(struct device_node *dev, int index);
```
* 根据传入的 _interrupt consumer node_，获取所对应的 _interrupt controller node_。
* 根据所获取的 _interrupt controller node_，获取所对应的 _struct irq_domain_。
* 根据传入的相关参数，执行所获取的 _struct irq_domain_ 所对应的 _struct irq_domain_ops_，以获取所对应的 _hwirq number_。
* 从 _interrupt framework_ 中，申请 _hwirq numbers_ 所对应的 _irq number_ 和 _struct irq_desc_。
* 更新所获取的 _struct irq_domain_ 所对应的 _interrupt controller tree topology_。
* 执行所获取的 _struct irq_domain_ 所对应的 _struct irq_domain_ops_，以执行： 
    * 从 _interrupt framework_ 中，获取所获取的 _struct irq_domain_ 所对应的 _struct irq_chip_。
    * 根据所获取的 _hwirq number_ 和 _struct irq_chip_ ，配置所申请的 _struct irq_desc_ 所对应的  _struct irq_data_。
    * 配置所申请的 _struct irq_desc_ 所对应的 _irq_flow_handler_t_。
* 根据所获取的 _hwirq number_，在传入的 _struct irq_domain_ 中创建 _mapping between the hwirq and the irq number_。
* 返回所申请的 _irq number_。
***

```C
    int request_threaded_irq(unsigned int irq, irq_handler_t handler,
			 irq_handler_t thread_fn, unsigned long irqflags,
			 const char *devname, void *dev_id);
```
* 根据传入的 _irq number_，从 _interrupt framework_ 中，获取所对应的 _struct irq_desc_。
* 根据传入的 _irq_handler_ts_ 及其相关参数，创建并配置一个 _struct irqaction_。
* 将所创建的 _struct irqaction_ 添加到所获取的 _struct irq_desc_ 中。

# _flow_

## _for author to use_

* _author_ 定义并注册一个 _interrupt controller_ 所对应的 _struct irq_chip_。
***
* _author_ 定义一个 _interrupt controller_ 所对应的 _struct irq_domain_ops_。
* _author_ 调用 [api](#api) 中的 ___irq_domain_add_：
    * 根据传入的 _struct irq_domain_ops_ 及其相关参数，创建并配置一个 _struct irq_domain_。
    * 根据传入的 _interrupt controller node_，注册所创建的 _struct irq_domain_。
***
* _author_ 定义一个 _interrupt controller_ 所对应的 _handle_irq_。
* _author_ 调用 [api](#api) 中的 _set_handle_irq_：
	* 根据传入的参数，注册 _handle_irq_。
 
## _for developer to use_

* _developer_ 调用 [api](#api-1) 中的 _of_irq_get_：
    * 根据传入的 _interrupt consumer node_，获取所对应的 _interrupt controller node_。
    * 根据所获取的 _interrupt controller node_，获取所对应的 _struct irq_domain_。
    * 根据传入的相关参数，执行所获取的 _struct irq_domain_ 所对应的 _struct irq_domain_ops_，以获取所对应的 _hwirq number_。
    * 从 _interrupt framework_ 中，申请 _hwirq number_ 所对应的 _irq number_ 和 _struct irq_desc_。
    * 更新所获取的 _struct irq_domain_ 所对应的 _interrupt controller tree topology_。
    * 执行所获取的 _struct irq_domain_ 所对应的 _struct irq_domain_ops_，以执行： 
        * 从 _interrupt framework_ 中，获取所获取的 _struct irq_domain_ 所对应的 _struct irq_chip_。
        * 根据所获取的 _hwirq number_ 和 _struct irq_chip_ 以及其他相关参数 ，配置所申请的 _struct irq_desc_ 所对应的 _struct irq_data_。
        * 配置所申请的 _struct irq_desc_ 所对应的 _irq_flow_handler_t_。
    * 根据所获取的 _hwirq number_，在传入的 _struct irq_domain_ 中创建 _mapping between the hwirq and the irq number_。
	* 返回所申请的 _irq number_。
***
* _developer_ 调用 [api](#api-1) 中的 _request_threaded_irq_：
	* 根据传入的 _irq number_，从 _interrupt framework_ 中，获取所对应的 _struct irq_desc_。
    * 根据传入的 _irq_handler_ts_ 及其相关参数，创建并配置一个 _struct irqaction_。
    * 将所创建的 _struct irqaction_ 添加到所获取的 _struct irq_desc_ 中。

## _for framework to use_

*  当一个 _interrupt_ 到来时，_interrupt framework_ 执行 _handle_irq_：
   * 读取相关寄存器，获取 _the specific interrupt_ 所对应的 _hwirq number_。
   * 在 _the specific handle_irq_ 所对应的 _struct irq_domain_ 所对应的 _mapping between hwirq numbers and irq numbers_ 中，获取 _the specific hwirq number_ 所对应的 _irq number_ 和 _struct irq_desc_。
   * 执行所获取的 _struct irq_desc_ 所对应的 _irq_flow_handler_t_：
        * 根据所获取的 _struct irq_desc_ 所对应的 _struct irq_data_，获取所对应的 _struct irq_chip_。
        * 执行所获取的 _struct irq_chip_，以执行 _hardware-specific operations_。
        * 执行所获取的 _struct irq_desc_ 所对应的 _struct irqactions_ 所对应的 _irq_handler_ts_。