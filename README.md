# rtl8192eu-linux-driver-kernel-3.14
RTL8192EU Realtek driver for linux kernel 3.14

**NOTE:** Works on Rosewill RNX-N180UBE v2 N300 Wireless Adapter and TP-Link TL-WN823N

# Support Linux Kernel 3.14
* Works on Linux 3.14.38  
* Support arch and cross compilers

## Building and installing using DKMS

This tree supports Dynamic Kernel Module Support (DKMS), a system for
generating kernel modules from out-of-tree kernel sources. It can be used to
install/uninstall kernel modules, and the module will be automatically rebuilt
from source when the kernel is upgraded (for example using your package manager).

1. Install DKMS and other required tools

    ```shell
    $ sudo apt-get install git linux-headers-generic build-essential dkms;
    ```

2. Clone this repository and change your directory to cloned path.

    ```shell
    $ git clone https://github.com/Mange/rtl8192eu-linux-driver;
    ```
    ```shell
    $ cd rtl8192eu-linux-driver;
    ```

3. The Makefile is preconfigured to handle most x86/PC versions. However, if you are compiling for something other than an intel x86 architecture, you need to first select the platform.

    * for arm64 devices (e.g. Orange Pi PC 2):

    ```sh
    ...
    CONFIG_PLATFORM_I386_PC = n
    ...
    CONFIG_PLATFORM_ARM_AARCH64 = y
    ```
	
	* for arm32 devices (e.g. I.MX6 Sabrelight):

    ```sh
    ...
    CONFIG_PLATFORM_I386_PC = n
    ...
    CONFIG_PLATFORM_ARM_AARCH32 = y
    ```

4. Add the driver to DKMS. This will copy the source to a system directory so
that it can used to rebuild the module on kernel upgrades.

    ```shell
    $ sudo dkms add .;
    ```

5. Build and install the driver.

    ```shell
    $ sudo dkms install rtl8192eu/1.0;
    ```

6. Distributions based on Debian & Ubuntu have RTL8XXXU driver present & running in kernelspace. To use our RTL8192EU driver, we need to blacklist RTL8XXXU.

    ```shell
    $ echo "blacklist rtl8xxxu" | sudo tee /etc/modprobe.d/rtl8xxxu.conf;
    ```

7. Force RTL8192EU Driver to be active from boot.
    ```shell
    $ echo -e "8192eu\n\nloop" | sudo tee /etc/modules;
    ```

8. Newer versions of Ubuntu has weird plugging/replugging issue (Check #94). This includes weird idling issues, To fix this:

    ```shell
    $ echo "options 8192eu rtw_power_mgnt=0 rtw_enusbss=0" | sudo tee /etc/modprobe.d/8192eu.conf;
    ```

9. Update changes to Grub & initramfs

    ```shell
    $ sudo update-grub; sudo update-initramfs -u;
    ```

10. Reboot system to load new changes from newly generated initramfs.

    ```shell
    $ systemctl reboot -i;
    ```

11. Check that your kernel has loaded the right module:
 
    ```shell
    $ sudo lshw -c network;
    ```
   
You should see the line ```driver=8192eu```
    
If you wish to uninstall the driver at a later point, use
_sudo dkms uninstall rtl8192eu/1.0_. To completely remove the driver from DKMS use
_sudo dkms remove rtl8192eu/1.0 --all_.
