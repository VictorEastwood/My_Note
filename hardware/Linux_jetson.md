Jetson 使能 uart
https://forums.developer.nvidia.com/t/missing-dev-ttyths1-for-uart0-uartb-on-nvidia-jetson-orin-nx/249685


1.安装设备树编译器
	sudo apt-get install device-tree-compiler
	(验证是否安装成功 dtc -v)

2.反编译设备树文件(.dtb)(先备份好)
	将/boot/dtb/文件夹中的.dtb文件复制到外边单独一个目录
	在该目录打开终端，反编译设备树文件
		$ sudo dtc -I dtb -O dts A.dtb > A.dts
	A是你的设备树文件名
	
3.修改设备树源文件(.dts)
	serial@3110000 {
		compatible = "nvidia,tegra194-hsuart";
		iommus = <0x06 0x04>;
		dma-coherent;
		reg = <0x00 0x3110000 0x00 0x10000>;
		reg-shift = <0x02>;
		interrupts = <0x00 0x71 0x04>;
		nvidia,memory-clients = <0x0e>;
		dmas = <0x3f 0x09 0x3f 0x09>;
		dma-names = "rx\0tx";
		clocks = <0x02 0x9c 0x02 0x66>;
		clock-names = "serial\0parent";
		resets = <0x02 0x65>;
		reset-names = "serial";
		status = "okay";
		phandle = <0x2f9>;
	};
	
	将上面的serial字段插入到反编译出的.dts文件中的相关位置(不只有一个serial，搜索一下，放到同类字段的位置即可)
	
	如果.dts文件中已经有serial@3110000字段，那么将原字段的status = "disable";修改为status = "okay";大概率可以解决问题
	
	(不知道phandle = <0x2f9>和其它设备重复会怎么样，但我感觉不能重复)
	
4.编译设备树
	$ sudo dtc -I dts -O dtb A.dts > A.dtb
	
5.将新的设备树文件替换掉/boot/dtb/文件夹中原有的设备树文件

6.重启





## 交叉编译内核文件





sudo apt update
sudo apt install flex
sudo apt install build-essential flex bison libssl-dev
sudo MicroXRCEAgent serial --dev /dev/ttyTHS1 -b 921600