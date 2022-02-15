# Linux

The operating system of our experimental environment is Ubuntu 18.04 which is a free and open-source Linux distribution. In order to run Linux, you need to install a virtual machine hypervisor on your computer. There are two reasons.

1. Our experiments involve changes to the network. Using a virtual machine (VM) can avoid impact on your computer.
2. We will introduce Mininet and Switchyard later. It is much more easier to install and run them on Linux.

{% hint style="info" %}
You are also free to use your favorite virtualization software for the lab assignment but you will most probably have to deal with the possible issues on yourself.
{% endhint %}

## Install VirtualBox

VirtualBox is a free, open-soure, cross-platform VM hypervisor.

First download the VirtualBox installer, you can find it [here](https://www.virtualbox.org/wiki/Downloads). You can choose the latest VirtualBox installer. If the operating system of your host is Windows, then click the download link "Windows hosts". Download "OS X hosts" for macOS. Another tool you need to download is "Extension Pack" which allows you resize your Linux display in VirtualBox.&#x20;

![VirtualBox download](<../../.gitbook/assets/vb-download (1).png>)

If you have network issue when visiting the official website, we provide the installers in [NJU box](https://box.nju.edu.cn/d/f334d2c3bd4446b68003/).

After completing download, install VirtualBox first then the extension pack.

## Import the VM Image

To ensure that your experimental environment is consistent, we provide a VM image. This image has Switchyard, Mininet and Wireshark installed so you do not need to worry about setting up the environment.

You can find the VM image [here](https://box.nju.edu.cn/d/f334d2c3bd4446b68003/).

* User name: `njucs`
* Password: `123`

You can learn about importing a VM image in VirtualBox [here](https://docs.oracle.com/cd/E36500\_01/E36513/html/qs-import-vm.html).

## Install the Extension in VM

Start your VM and check if your Linux runs well. You will find the display size is fixed to 800×600. To resize it you need to install the VirtualBox extension pack into your VM.

You can learn about installing the extension at [here](https://docs.oracle.com/en/virtualization/virtualbox/6.0/user/intro-installing.html).

## Use Linux

Most of our operations will be completed inside the terminal. You need to know

* Power on & off
* File system operations
* File and User permissions
* Run programs

A tutorial of Linux can be found at [鸟哥的 Linux 私房菜 —— 基础学习篇](http://cn.linux.vbird.org/linux\_basic/linux\_basic.php). Select the part you want to know and read it.

## Other hypervisor

Instead of VirtualBox, you can also choose other VM hypervisors. Here we list some of them:

* VMware Workstation Player: free on Windows and Linux
* VMware Fusion: free on macOS 11.0.0+ (Big Sur)

## Customize Your own VM

If you are a free soul and want to setup Mininet and Switchyard in a different environment you are welcome to do that as well. You can find some useful information [here](../../appendix/environment-setup.md). This might or might not be useful for you depending on your environment. FYI, Switchyard is cross-platform but Mininet supports only Linux.
