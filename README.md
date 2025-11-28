# Born2BeRoot
## Requirements
- [Virtual Box](https://www.virtualbox.org/wiki/Downloads)
- [OS: Debian](https://www.debian.org/)

>[!NOTE]
> To setup debian with out the GUI, the netinst (network installer) ISO image can be downloaded. This pakage required internet connection and download the required pakages according to the need.

## Memory allocation to the VM
- RAM: 1GB, CLI interface is pretty light weight, this does not create stress on the main PC at the same time give breating space for VM also.
- CPU: 1
- Disk Size: 30 GB **dynamic allocation**, if want to do the bonus, else minimum 8GB is enough.
- EFI: unchecked.
- Network: NAT (default).

<details>
  <summary>BIOS</summary>
  <br>
  BIOS (Basic Input/Output System) is a tiny programm stored in a     small chip, attached to the computer mother board. When we turn     on the power, the BIOS runs performance check called POST(Power     On Self Test). It checks is CPU, RAM, KeyBoard ... are connected,   after the checking, it handover the operation to bootloader.
  <br>
  UEFI is a moder version of BIOS with better graphical interface,    mouse option but it required seperate partition /boot/efi, for      this project, it is not required.
</details>

<details>
<summary>Dynamic Allocation</summary>
<br>
Dynamic allocation is a process that tell the VM machine that it    has 30GB space. But after all the installation is done, it takes    say 2GB of space. For the main computer, it sees only 2GB space     is used by the VM. Insted of dynamic sllocation, if we do a fix     size allocation, then the main pc will always see the VM is         occupying 30GB space. If you try to create a 30GB fixed memory on   a 20GB system, it will give error but dynamic allowcation will wok fine.
</details>

<details>
<summary>NAT</summary>
<br>
Network Address Translaton, it uses the same internate connection used by the main computer. VM send request to google, VirtualBox intercept it, stamps it with you main computer IP and send it out, when reply comes, the VB send it to VM.
<br>
Pros: It works everywhere because no one can see the VM directly from outside.
<br>
Cons: Can not SSH directly, we need to set port forwarding (for this project 4242). Anything comming on port 4242 of the main computer will be forwarded to VM.
<br>
Other mode like Bride Adapter is used in real server, where VM act as seperate unit with its own IP address. We can talk to it directly. But in 42 campus or any place with network security, might block the new IP and we will never have internet access.
</details>

### installation steps
***
- Install (no graphical installation).
- Choose language: English.
- Country: Select your current county, this set the timezone correctly
- Keymap: American English.
- **Hostname**: (42 username)42. Ex: user42.
- Domain name: Empty.
- Root Password: Any password is fine for now.
- New User: Any name you like.
- Username for account: any name you like.
- password for new user: any
- **Partition method**: Guided - use entire disk and set up encrypted LVM.
> [!NOTE]
> ### What is LVM?
> LVM (Logical volume manager), say you create three partition in the 30GB space, part1, part2, part3. Now with LVM option enable, at anytime you can increase the space of any pertition according to your need. The partitions are kind of dynamic. With encryption on top, if someone access your machine, with out the pass key, they cant reat the data stored.
- select disk: the disk space you created at the begning.
- Partition scheme: seperate /home partition -> if something happens, you can reinstall the os but your personal data stay safe in home directory.
- Configure change: yes.
- Erasing data: :warning: **Cancel**, otherwise the system will fill the entire 30GB with random zeros and this will break the dynamic allocation.
- Encryption passphrase: This phrase will be needed to access the VM everytime you try to to boot.
- Volume group: keep the default value
- overview: will have LV home, root, swap_1, select finish partitioning ..
- Write changes: yes.
- Scan Extra media: No, there is no use or cd, only the netinst file.
- mirror country: your current country. This is required to download additional packages since netinst has only installer, nothing else in it.
- Debian archive mirrir: 1st option.
- HTTP proxy: leave blank
- package usage survey: no.
- Software to install:
  - [x] SSH server
  - [x] standard system utitlities
- Install GRUB bootloader: yes
> [!NOTE]
> ### What is  Bootloader?
> bootloader is a small program that runs after the machine performs its self-checks (BIOS) but before the Operating System starts. Its only job is to load the OS Kernel into memory.
>
> GRUB(GRand Unified Bootloader) is used in almost all linux distributions. if you have multiple OS, it popup the option to select.
- Device for bootloader installation: /dev/sda

Now the OS is successfully installed. Press continue too reboot. Provide the passkey first. Then user id and Password (not the Host name and password).

## After First Login
<details>
<summary>APT</summary>
<br>
Advance Package Tool, the app store of Debian, it connect to the server we selected during installation and download and install the software.
<br>
apt update, download the latest version available.
<br>
apt install "name", install the software.
</details>

<details>
<summary>SU</summary>
<br>
su (Substitute User), it allow to swith identity entirely with the target, we need to provide the target password. only **su** allow you to be root if you know root password. On success, it allow you to stay root unless you type **exit**.
<br>
Risk: once you root, if you delete anything, there will be no record who deleated it.
</details>

<details>
<summary>SUDO</summary>
<br>
sudo (SuperUser DO), it allow the normal user to borrow the power of root temporarily, you need to provide the password but it is you password not the root password.
<br>
Whatever you do with sudo user power, the system will generate a log telling who used the command at which time. Once the command executed, you are out of root user previllage.
</details>
- Right now, the system does not have any fire wall, wrong SSH port and sudo not install.

if you are not logged in as root, use "su" and the root user password to switch to root
```bash
apt update && apt install sudo
```

But the user do not have the power to use it yet. You need to add the user to the sudo group.

To check available user you can use
```bash
ls /home
```
add user to the the sudo group
```bash
adduser username sudo
```
If you get error like adduser command not found, either you can use full path /usr/sbin/adduser or you can exit and use "su -" to have root access with correct path.
to varify if the user is added or not use
```bash
getent group sudo
```
it wil show sudo: x : 27 : user -> group name : password placeholder : group id : user list

once done, type reboot to reboot the system and login as a normal user and check if the user still is in sudo group or not. You can use following command
```bash
sudo whoami
```
you will get reponse as: root.

## Setting up SSH
<details>
<summary>SSH</summary>
<br>
Secure Shell
<br>
</details>

Use the following command to check if the SSH is active
```bash
sudo service ssh status
sudo systemctl status ssh

sudo systemctl stop ssh -> stop ssh connection
sudo systemctl start ssh -> start ssh connection
```
On success, it will return Active: active (running) in green, press q to exit.
