# Use GitHub Actions to quickly customize and compile OpenWrt firmware

For process documentation, please refer to [KFERMercer/OpenWrt-CI](https://github.com/KFERMercer/OpenWrt-CI). Thank you very much!

The firmware source code used includes the official openwrt, as well as versions maintained by coolsnowwolf, Lienol, immortalwrt, and x-wrt. For details, see [Table] (#firmwaresourcecode).

Preset models include Xiaomi 4A Gigabit Edition, Xiaomi CR6606, Redmi AX6S, etc. For details, see `preset*/headers.json`.

**Quickly generate firmware ---> Log in to GitHub, fork this warehouse, click on `Actions` above, select `build XXX` in the process on the left to run, and download the firmware after running. The indication is as follows: **

<img src="extra-files/images/action_running.gif" width="70%" ></img>

Select the model: Click on the drop-down box of `Select Device` in the run workflow interface to manually select the model.

If there is no preset model that you need, you can use the file in the [templet] (templet) directory to add a new model.

If you like it, star it in the upper right corner so you can find it again easily.

## To use this project you need

- GitHub account

- Basic skills for using GitHub Actions

**Liunx, OpenWrt, [Actions](https://docs.github.com/cn/actions) and other related knowledge, you can search and learn by yourself**

## Tutorial

<details>
  
   <summary>Click to expand/close</summary>

### 1. Register a GitHub account and enable GitHub Actions

### 2. fork [hugcabbage/shared-lede](https://github.com/hugcabbage/shared-lede)

### 3. Custom firmware

Do not modify anything. According to the default configuration, you can skip this step.

Each model is associated with three files in the preset* directory.

-[number].clone.sh

This script is used to pull the firmware source code and extension plug-in source code. When adding plug-in sources, it is recommended to test locally whether there are any missing dependencies.

Commonly used cloning commands are as follows (clone can be understood as downloading):

`git clone link`

`git clone -b branch name link`

-[number].modify.sh

This script is used to initialize the firmware settings and modify the login IP, host name, WiFi name, etc.

The most commonly used command in this script is sed. For detailed usage, please refer to [link](https://www.runoob.com/linux/linux-comm-sed.html). Here is a brief explanation.

For example, the following command is used to modify the management IP:

`sed -i 's/192.168.1.1/192.168.31.1/g' package/base-files/files/bin/config_generate`

`192.168.1.1` is the default lan port login IP in the source code, which is the initial one; `192.168.31.1` is new and is used to replace the initial text.

It can be seen that the composition of the command is as follows:

`sed -i 's/original string/new string/g' file path`

This can be used to replace specific locations in the source code. -i refers to changing the file directly, s refers to replacement, and g refers to global.

The original string is recorded as str1, and the new string is recorded as str2. Just change the position of str2 in the custom settings. If you change str1, the command will not match anything in the source code, and the replacement will be invalid.

>🎈🎈🎈 For the usage of each basic command, please refer to this [link](https://github.com/danshui-git/shuoming/blob/master/ming.md), which is suitable for novices.

-[number].config

This file corresponds to the .config file generated after local compilation and execution of make menuconfig.

This file mainly contains the luci application and will be automatically converted into a complete .config during the process.

Just add or delete plug-ins to modify this file. Taking the argon theme as an example, the format is as follows:

  `CONFIG_PACKAGE_luci-theme-argon=y` is selected to be compiled into the firmware.

  `CONFIG_PACKAGE_luci-theme-argon=m` is selected to only compile the ipk plug-in.

  `# CONFIG_PACKAGE_luci-theme-argon is not set` This is the case if it is not selected

### 4. Manually start the compilation process in Actions

Select the `build XXX` workflow you need, then click `Run workflow`, fill in the content as required, and run it.

Each option is described below:

- Overclocked to 1100Mhz:

Only `build lede` has this option.

Not checked by default. It is only applicable to the 5.10 kernel. Except for the Redmi AX6S, all other models use the 5.10 kernel by default.

- Using 5.15 kernel:

Only `build lede` has this option.

Not checked by default. When lean lede source code checks this option, an error will be reported when compiling Xiaomi 4A Gigabit Edition and Xiaomi 3Gv2, so do not use it.

Redmi AX6S only has 5.15 core, so there is no need to check it.

- Select model:

Click the drop-down box to select different models.

- Upload to release:

Checked by default. A single file cannot exceed 2GB, and content records can be added. See the figure below for the release area:

<img src="extra-files/images/release_zone.png" width="70%" ></img>

- Upload to artifact:

Not checked by default. See the figure below for the artifact area:

<img src="extra-files/images/artifact_zone.png" width="70%" ></img>

- Version description:

You can make some simple records and they will be displayed in the release.

### 5. Compilation completed

After the Actions process is successfully completed, go to release (or artifact) to download your firmware. Allfiles.zip in release is the package of all files.

</details>

## preset*directory description

<details>
  
   <summary>Click to expand/close</summary>

All model information can be found in the file `preset*/headers.json`. Each configuration directory is slightly different, such as [preset-openwrt/headers.json](preset-openwrt/headers.json).

### config description
- 1.config for small flash devices (16MB and below)
- 2.config for large flash devices

### Labeling rules
- The numerical label of each model in headers.json is used to select the corresponding clone.sh, modify.sh, and config.
- According to the model number in headers.json, if the corresponding clone.sh, modify.sh, and config cannot be found, 1.clone.sh, 1.modify.sh, and 1.config will be selected by default.

### Custom configuration
#### method one
Modify the three files clone.sh, modify.sh, and config

#### Method Two
- Add new clone.sh, modify.sh, and config and label them with numbers, such as 5.clone.sh, 5.modify.sh, 5.config
- Modify the label of the specified model in headers.json, for example, change `"xiaomi-ac2100": ["1", "ramips", "mt7621", "xiaomi_mi-router-ac2100"]` to `"xiaomi-ac2100" : ["5", "ramips", "mt7621", "xiaomi_mi-router-ac2100"]`

#### Method 3
- Add new clone.sh, modify.sh, and config and label them with numbers, such as 5.clone.sh, 5.modify.sh, 5.config
- Add new models to headers.json, for example, add `"xiaomi-ac2100-xxx": ["5", "ramips", "mt7621", "xiaomi_mi-router-ac2100"]`
- Add new models to `.github/workflows/build-xxx.yml`inputs.model.options, for example, add `- 'xiaomi-ac2100-xxx'` to .github/workflows/build-openwrt.yml

</details>

## Firmware source code

|Configuration directory|Process name|Source code|
|:----:|:----:|:----:|
|preset-lede|build lede|[coolsnowwolf/lede](https://github.com/coolsnowwolf/lede)|
|preset-lienol-openwrt|build lienol openwrt|[Lienol/openwrt](https://github.com/Lienol/openwrt)|
|preset-openwrt|build openwrt|[openwrt/openwrt](https://github.com/openwrt/openwrt)|
|preset-immortalwrt|build immortalwrt|[immortalwrt/immortalwrt](https://github.com/immortalwrt/immortalwrt)|
|preset-x-wrt|build x-wrt|[x-wrt/x-wrt](https://github.com/x-wrt/x-wrt)|

## hint

1. Run `build XXX` directly in Actions to compile the firmware, but the default number of plug-ins is small. If you need to add or delete plug-ins, you can modify `preset*/[number].config`. If the plug-in source is added to `[Number].clone.sh` and corresponding modifications need to be made in `[Number].config`, it is recommended to test it by making menuconfig locally first.

1. The overclocking solution is not enabled by default, and the solution comes from this [post](https://www.right.com.cn/forum/thread-4042045-1-1.html).

1. Xiaomi 4A Gigabit Edition and Xiaomi 3Gv2 need to modify the partition to directly flash in breed. Please refer to this [post](https://www.right.com.cn/forum/thread-4052254-1-1.html), It has been modified in this project, see the script [modify-xiaomi-router-4a-3g-v2.sh](extra-files/modify-xiaomi-router-4a-3g-v2.sh).

1. Xiaomi 4A Gigabit Edition and Xiaomi 3Gv2 have small flash memory (only 16MB). If too many plug-ins are compiled and the package size exceeds the upper limit of the flash memory, sysupgrade.bin will not be generated.

---

## at last

There are no particularly detailed tutorials, just figure it out on your own.

If you have any questions, please use the huge network knowledge base to quickly solve your problems.
