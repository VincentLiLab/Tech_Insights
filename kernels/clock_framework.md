- [概述](#概述)
- [细节](#细节)
  - [_for author to use_](#for-author-to-use)
    - [_file layout_](#file-layout)
    - [_data structure_](#data-structure)
    - [_api_](#api)
  - [_for developer to use_](#for-developer-to-use)
    - [_file layout_](#file-layout-1)
    - [_data structure_](#data-structure-1)
    - [_api_](#api-1)
- [流程](#流程)
  - [_for author to use_](#for-author-to-use-1)
  - [_for developer to use_](#for-developer-to-use-1)

# 概述

_clock_framework_ 负责管理 _platform_ 中的所有 _clock_，_author_ 需要为每个 _clock_ 都定义一个其所对应的 _struct clk_ops_，以执行所对应的 _hardware-specific operations_，而 _clock_framework_ 会为每个 _clock_ 都创建一个其所对应的 _struct clk_core_，以管理所对应的 _struct clk_ops_ 和 _clock tree topology_，并通过一个 _struct clk_hw_ 来将所对应的 _struct clk_core_ 和 _struct clk_ops_ 绑定在一起；各个 _developer_ 需要通过各个 _struct clk_ 来访问一个 _clock_ 所对应的 _struct clk_ops_。

# 细节

## _for author to use_

### _file layout_

_include/linux/clk-provider.h_  
_drivers/clk/clk.c_

### _data structure_

_struct clk_ops used for performing the hardware-specific operations_
```C
    struct clk_ops {
        int		(*prepare)(struct clk_hw *hw);
        void		(*unprepare)(struct clk_hw *hw);
        int		(*is_prepared)(struct clk_hw *hw);
        void		(*unprepare_unused)(struct clk_hw *hw);
        int		(*enable)(struct clk_hw *hw);
        void		(*disable)(struct clk_hw *hw);
        int		(*is_enabled)(struct clk_hw *hw);
        void		(*disable_unused)(struct clk_hw *hw);
        unsigned long	(*recalc_rate)(struct clk_hw *hw,
                        unsigned long parent_rate);
        long		(*round_rate)(struct clk_hw *hw, unsigned long rate,
                        unsigned long *parent_rate);
        int		(*determine_rate)(struct clk_hw *hw,
                        struct clk_rate_request *req);
        int		(*set_parent)(struct clk_hw *hw, u8 index);
        u8		(*get_parent)(struct clk_hw *hw);
        int		(*set_rate)(struct clk_hw *hw, unsigned long rate,
                        unsigned long parent_rate);
        int		(*set_rate_and_parent)(struct clk_hw *hw,
                        unsigned long rate,
                        unsigned long parent_rate, u8 index);
        unsigned long	(*recalc_accuracy)(struct clk_hw *hw,
                        unsigned long parent_accuracy);
        int		(*get_phase)(struct clk_hw *hw);
        int		(*set_phase)(struct clk_hw *hw, int degrees);
        int		(*get_duty_cycle)(struct clk_hw *hw,
                        struct clk_duty *duty);
        int		(*set_duty_cycle)(struct clk_hw *hw,
                        struct clk_duty *duty);
        void		(*init)(struct clk_hw *hw);
        void		(*debug_init)(struct clk_hw *hw, struct dentry *dentry);
        int		(*set_flags)(struct clk_hw *hw, unsigned int flags);
        void		(*list_registers)(struct seq_file *f,
                                struct clk_hw *hw);
        long		(*list_rate)(struct clk_hw *hw, unsigned int n,
                                unsigned long rate_max);
        void		(*bus_vote)(struct clk_hw *hw, bool enable);
    };
```
***

_struct clk_core used for managing struct clk_ops and making up the core of the clock tree topology_
```C
    struct clk_core {
        const char		*name;
        const struct clk_ops	*ops;
        struct clk_hw		*hw;
        struct module		*owner;
        struct device		*dev;
        struct clk_core		*parent;
        const char		**parent_names;
        struct clk_core		**parents;
        unsigned int		num_parents;
        unsigned int		new_parent_index;
        unsigned long		rate;
        unsigned long		req_rate;
        unsigned long		new_rate;
        struct clk_core		*new_parent;
        struct clk_core		*new_child;
        unsigned long		flags;
        bool			orphan;
        bool			rpm_enabled;
        bool			need_sync;
        bool			boot_enabled;
        unsigned int		enable_count;
        unsigned int		prepare_count;
        unsigned int		protect_count;
        bool			need_handoff_enable;
        bool			need_handoff_prepare;
        unsigned long		min_rate;
        unsigned long		max_rate;
        unsigned long		accuracy;
        int			phase;
        struct clk_duty		duty;
        struct hlist_head	children;
        struct hlist_node	child_node;
        struct hlist_head	clks;
        unsigned int		notifier_count;
    #ifdef CONFIG_COMMON_CLK_DEBUGFS
        struct dentry		*dentry;
        struct hlist_node	debug_node;
    #endif
        struct kref		ref;
        struct clk_vdd_class	*vdd_class;
        int			vdd_class_vote;
        int			new_vdd_class_vote;
        struct list_head	rate_change_node;
        unsigned long		*rate_max;
        int			num_rate_max;
    };
```
***

_struct clk_hw tying the struct clk_ops and clk_core together_
```C
    struct clk_init_data {
        const char		*name;
        const struct clk_ops	*ops;
        const char		* const *parent_names;
        unsigned int		num_parents;
        unsigned long		flags;
        struct clk_vdd_class	*vdd_class;
        unsigned long		*rate_max;
        int			num_rate_max;
        unsigned int		bus_cl_id;
    };

    struct clk_hw {
        struct clk_core *core;
        struct clk *clk;
        const struct clk_init_data *init;
    };
```

### _api_

```C
    struct clk *clk_register(struct device *dev, struct clk_hw *hw);
```  
* 根据传入的 _struct device_ 和 _struct clk_hw_，创建并配置 _struct clk_core_ 所对应的 _struct clk_ops_ 及其其他参数。
* 更新所创建的 _struct clk_core_ 所对应的 _clock tree topology_。
* 通过传入的 _struct clk_hw_ 将所创建的 _struct clk_core_ 和所对应的 _struct clk_ops_ 绑定在一起。
***

```c
    int of_clk_add_provider(struct device_node *np,
			struct clk *(*clk_src_get)(struct of_phandle_args *clkspec,
						   void *data),
			void *data);
```
* 将传入的 _clock controller node_ 所对应的 _function used for getting the specific struct clk_hw_ 添加至 _clock_framework_ 中。

## _for developer to use_

### _file layout_

_include/linux/clk.h_  
_include/linux/clkdev.h_  
_drivers/clk/clk.c_  
_drivers/clk/clkdev.c_  

### _data structure_

_struct clk the driver-facing functions operate on_
```C
    struct clk {
        struct clk_core	*core;
        const char *dev_id;
        const char *con_id;
        unsigned long min_rate;
        unsigned long max_rate;
        unsigned int exclusive_count;
        struct hlist_node clks_node;
    };
```

### _api_

```C
    struct clk *clk_get(struct device *dev, const char *con_id);
```
* 根据传入的 _clock consumer device_ 及其相关参数，获取所对应的 _clock controller node_。
* 根据所获取的 _clock controller node_，从 _clock framework_ 中获取 _function used for getting the specific struct clk_hw_。
* 根据传入的 _clock consumer device_ 所对应的 _node_ 和所获取的 _function used for getting the specific struct clk_hw_，获取 _the specific struct clk_hw_。
* 根据所获取的 _the specific struct clk_hw_ 所对应的 _struct clk_core_，创建、配置并返回 _struct clk_。
***

```C
    ...
    int clk_set_rate(struct clk *clk, unsigned long rate);
    int clk_set_rate_range(struct clk *clk, unsigned long min, unsigned long max);
    ...
```
* 根据传入的 _struct clk_，获取所对应的 _struct clk_core_。
* 处理所获取的 _struct clk_core_ 所对应的 _clock tree topology_。
* 执行所获取的 _struct clk_core_ 所对应的 _struct clk_ops_ 去执行 _hardware-specific operations_。

# 流程

## _for author to use_

* _author_ 定义一个 _clock_ 所对应的 _struct clk_ops_ 和 _struct clk_hw_
***
* _author_ 调用 [api](#api) 中的 _clk_register_： 
    * 根据传入的 _struct device_ 和 _struct clk_hw_，创建并配置 _struct clk_core_ 所对应的 _struct clk_ops_ 及其其他参数。
    * 更新所创建的 _struct clk_core_ 所对应的 _clock tree topology_。
    * 通过传入的 _struct clk_hw_ 将所创建的 _struct clk_core_ 和所对应的 _struct clk_ops_ 绑定在一起。
***
* _author_ 调用 [api](#api) 中的 _of_clk_add_provider_：
    * 将传入的 _clock controller node_ 所对应的 _function used for getting the specific struct clk_hw_ 添加至 _clock_framework_ 中。

## _for developer to use_

* _developer_ 调用 [api](#api-1) 中的 _clk_get_：
    * 根据传入的 _clock consumer device_ 及其相关参数，获取所对应的 _clock controller node_。
    * 根据所获取的 _clock controller node_，从 _clock framework_ 中获取 _function used for getting the specific struct clk_hw_。
    * 根据传入的 _clock consumer device_ 所对应的 _node_ 和所获取的 _function used for getting the specific struct clk_hw_，获取 _the specific struct clk_hw_。
    * 根据所获取的 _the specific struct clk_hw_ 所对应的 _struct clk_core_，创建、配置并返回 _struct clk_。
***
* _developer_ 调用 [api](#api-1) 中的 _clk_set_rate_ 等：
    * 根据传入的 _struct clk_，获取所对应的 _struct clk_core_。
    * 处理所获取的 _struct clk_core_ 所对应的 _clock tree topology_。
    * 执行所获取的 _struct clk_core_ 所对应的 _struct clk_ops_ 去执行 _hardware-specific operations_。