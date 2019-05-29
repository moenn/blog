---
title: DSP TMS320F28335 烧录程序到 flash
date:   2019-05-29
---
### 准备
当开发 dsp 程序的时候，一般是烧录到 RAM 中，这样下载程序的速度快，不过 dsp 掉电后程序就会消失。当开发完成后，需要将程序固化到 FLASH 中，掉电后程序仍存在 dsp 中。

烧录之前确保你可以用仿真器下载程序到 RAM 中运行，即电脑、仿真器和dsp芯片的连接是无误的。

这篇文章以 TMS320F28335 芯片为例，参考的是 c2000ware 软件中的 `Example_28335_Flash` 示例程序。 如果你使用的是其他芯片，在以下步骤中适当替换即可。
### 烧录步骤
1. 选择 Boot Mode 为 Jump to Flash. 根据 28335 数据手册的 6.1.9一节，向下表中的 4 个 GPIO 口输入高电平即可。

    | MODE | GPIO87/XA15 | GPIO86/XA14 | GPIO85/XA13 | GPIO84/XA12 | MODE          |
    |------|-------------|-------------|-------------|-------------|---------------|
    | F    | 1           | 1           | 1           | 1           | Jump to Flash |

    可以使用如下的电路图。

    ![Boot_from_flash.png](https://i.loli.net/2019/05/29/5cee4b68bef7678719.png)

2. 在 `28335_RAM_Lnk.cmd` 文件上右键，勾选为 exclude from Build。 

    ![exclude_from_build.png](https://i.loli.net/2019/05/29/5cee4bbd632cb23470.png)

3. 在工程项目上右键 => add file, 添加以下文件。

    |  文件                        | 所在位置                                                             |
    |------------------------------|----------------------------------------------------------------------|
    | F28335.cmd                   | C:\ti\c2000\C2000Ware_1_00_06_00\device_support\f2833x\common\cmd    |
    | DSP2833x_CodeStartBranch.asm | C:\ti\c2000\C2000Ware_1_00_06_00\device_support\f2833x\common\source |
    | DSP2833x_MemCopy.c           | C:\ti\c2000\C2000Ware_1_00_06_00\device_support\f2833x\common\source |

4. 在 main.c 文件中添加函数声明 

    ```
    extern Uint16 RamfuncsLoadStart;
    extern Uint16 RamfuncsLoadEnd;
    extern Uint16 RamfuncsRunStart;
    extern Uint16 RamfuncsLoadSize;
    ```

5. 在 main.c 文件中的 main()函数开头部分添加语句 、

    ```
    InitSysCtrl();
    memcpy(&RamfuncsRunStart, &RamfuncsLoadStart, (Uint32)&RamfuncsLoadSize);
    InitFlash();   
    ```

6. 编译下载，ccs 会提示 erasing flash sectors. 下载完成后，将 DSP 芯片断电后再上电，观察程序是否仍然还能运行。

###### 注意事项
- 如果 dsp 使用 usb 扩展坞供电，可能无法正常从 flash 启动。