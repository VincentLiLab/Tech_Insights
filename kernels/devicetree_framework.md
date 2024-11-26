
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

_devicetree framework_ 负责描述 _hardware_ 的信息，比如：_data bus_、_interrupt line_ 和 _GPIO
connection_...。

使用 _DT_ 主要有三个目的：_platform identification_、_runtime configuration_ 和 _device population_。

# _detail_

## _for author to use_

### _file layout_

_include/linux/of*.h_   
drivers/of/*.c  
_arch/arm/include/asm/mach/arch.h_  

### _data structure_

_struct machine_desc used for platform identification to run machine-specific fixups during early boot_
```C
    struct machine_desc {
        unsigned int		nr;		/* architecture number	*/
        const char		*name;		/* architecture name	*/
        unsigned long		atag_offset;	/* tagged list (relative) */
        const char *const 	*dt_compat;	/* array of device tree
                * 'compatible' strings	*/

        unsigned int		nr_irqs;	/* number of IRQs */

    #ifdef CONFIG_ZONE_DMA
        phys_addr_t		dma_zone_size;	/* size of DMA-able area */
    #endif

        unsigned int		video_start;	/* start of video RAM	*/
        unsigned int		video_end;	/* end of video RAM	*/

        unsigned char		reserve_lp0 :1;	/* never has lp0	*/
        unsigned char		reserve_lp1 :1;	/* never has lp1	*/
        unsigned char		reserve_lp2 :1;	/* never has lp2	*/
        enum reboot_mode	reboot_mode;	/* default restart mode	*/
        unsigned		l2c_aux_val;	/* L2 cache aux value	*/
        unsigned		l2c_aux_mask;	/* L2 cache aux mask	*/
        void			(*l2c_write_sec)(unsigned long, unsigned);
        const struct smp_operations	*smp;	/* SMP operations	*/
        bool			(*smp_init)(void);
        void			(*fixup)(struct tag *, char **);
        void			(*dt_fixup)(void);
        long long		(*pv_fixup)(void);
        void			(*reserve)(void);/* reserve mem blocks	*/
        void			(*map_io)(void);/* IO mapping function	*/
        void			(*init_early)(void);
        void			(*init_irq)(void);
        void			(*init_time)(void);
        void			(*init_machine)(void);
        void			(*init_late)(void);
    #ifdef CONFIG_GENERIC_IRQ_MULTI_HANDLER
        void			(*handle_irq)(struct pt_regs *);
    #endif
        void			(*restart)(enum reboot_mode, const char *);
    };
```
***

_chosen node used for runtime configuration during early boot_
```C
    // example
    chosen {
		bootargs = "console=ttyS0,115200 loglevel=8";
		initrd-start = <0xc8000000>;
		initrd-end = <0xc8200000>;
	};
```
***

_hardware node used for device population_
```C
    /{
        ...

        soc {
            compatible = "nvidia,tegra20-soc", "simple-bus";
            #address-cells = <1>;
            #size-cells = <1>;
            ranges;

            intc: interrupt-controller@50041000 {
                compatible = "nvidia,tegra20-gic";
                interrupt-controller;
                #interrupt-cells = <1>;
                reg = <0x50041000 0x1000>, < 0x50040100 0x0100 >;
            };

            ...
        };
```
***

### _api_

```C
    #define DT_MACHINE_START(_name, _namestr)		\
    static const struct machine_desc __mach_desc_##_name	\
    __used							\
    __attribute__((__section__(".arch.info.init"))) = {	\
        .nr		= ~0,				\
        .name		= _namestr,

    #endif
```  
* 定义并注册一个 _struct machine_desc_。
***

```C
    // example
    chosen {
		bootargs = "console=ttyS0,115200 loglevel=8";
		initrd-start = <0xc8000000>;
		initrd-end = <0xc8200000>;
	};
```
* 定义并注册一个 _chosen node_。
***

```C
    /{
        ...

        soc {
            compatible = "nvidia,tegra20-soc", "simple-bus";
            #address-cells = <1>;
            #size-cells = <1>;
            ranges;

            intc: interrupt-controller@50041000 {
                compatible = "nvidia,tegra20-gic";
                interrupt-controller;
                #interrupt-cells = <1>;
                reg = <0x50041000 0x1000>, < 0x50040100 0x0100 >;
            };
            
            ...
        };
```
* 定义并注册相关的 _hardware node_
***

# _flow_

## _for author to use_

* _author_ 调用 [api](#api) 中的 _DT_MACHINE_START_：
  	* 定义并注册一个 _struct machine_desc_。
***
* _author_ 定义并注册一个 _chosen node_。
***
* _author_ 定义并注册相关的 _hardware node_。
***

## _for framework to use_

* _devicetree framework_ 执行 _setup_arch()/setup_machine_fdt()_：
    * 根据所对应的 _device tree data_，选择所对应的一个 _struct machine_desc_，并执行所对应的 _platform identification_，以运行所对应的 _machine-specific fixups_。
    * 根据所对应的 _device tree data_，选择所对应的一个 _chosen node_，并执行所对应的 _runtime configuration_。
***
* _devicetree framework_ 执行 _of_platform_populate()_：
    * 根据所对应的 _device tree data_，执行所对应的 _device population_，以描述 _hardware_ 的信息。
***

