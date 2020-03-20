I encrypted my root filesystem with the ubuntu default installer script. When the system boots i have to enter a password to decrypt my root partion with a password. But i want my internal drives to be encrypted as well and avoid to enter a password each time the partition is mounted. We will begin with the encryption of a pendrive to see how that works and what happens behind the scenes and resume with auto-mounting encrypted internal drives in another blog post. 

## Encrypting, decrypting and mounting a pendrive

### Encrypt

Open a terminal and type `lsblk`. This lists all your devices. Find out which file is your pendrive, let's asume it's `/dev/sdb`. Open `gparted`, delete all partions and create a new partion table `gpt` with a single partion (i use `ext4` as filesystem). 

After that `lsblk` should list following:

```sh
$ lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
...
sdb           8:16   1  14,3G  0 disk 
└─sdb1        8:17   1  14,3G  0 part 
...
```

The next step is to format the luks partition. We have to set up a pasword and confirm the changes to the partition with (uppercase) YES. 

```sh
sudo cryptsetup -y luksFormat /dev/sdb1
```

### Decrypt and format

The device it not usable because the encrypted partion is not formated yet. To do so we can open the encrypted partition and add it to our device list. Normally, on any modern distribution, opening and mounting is automated by your filemanager, but we will quickly walk through it via terminal.

```
sudo cryptsetup luksOpen /dev/sdb1 test-crypt
```

After you entered your password, `lsblk` should output:

```sh
$ lsblk
NAME           MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINT
sdb              8:16   1  14,3G  0 disk  
└─sdb1           8:17   1  14,3G  0 part  
  └─test-crypt 253:0    0  14,3G  0 crypt 
```

Before the partition is mountable we have to format it, we can find it under `/dev/mapper/test-crypt`. I'am using `ext4` again, but you can use any other filesystem.

```sh
sudo mkfs.ext4 /dev/mapper/test-crypt
```

### Manually mount and unmount

We can finally mount our decrypted partition to a folder. 

```sh
# create a folder in which the pendrive should be mounted
sudo mkdir /media/usb 
# give access rights to this folder
sudo chown $USER.$USER /media/usb
# finally mount it
sudo mount /dev/mapper/test-crypt /media/usb
```

Now we can browse the decrypted pendrive with the filemanager and create files on it. 


### Automatic mount

Before you let the filemanager (like `nautilus` or `pcmanfm`) open and mount the the encrypted pendrive it would be nice to correctly label your partion, otherwise the filemanager would assign a ugly device id as the label.

```sh
sudo e2label /dev/mapper/test-crypt encrypted-pendrive-16gb
```

Let's unmount the partition and close luks.

```sh
sudo umount /dev/mapper/test-crypt
sudo cryptsetup close test-crypt
```

Unplug and plug in the device again. Your filemanger should recognize it and ask you for the password. If you're not able to create files on it, remember to `chown` the mountpoint.

### Adding, removing and changing the password

The password doesn't really decrypt the luks partition. A masterkey, also stored encrypted (by the password) will decrypt the partition. So it's easy to change or add another password without encrypting the partition again, only the masterkey has to be decrypted and encrypted. In this way you can define up to 8 passwords. 

If you mounted your pendrive with your filemanager, use `lsblk` to identify the luks name. Use one of follwing commands to add/change/remove a key from luks. Be carefull, if you remove the last key you can't ever access the encrypted partition again.

```sh
sudo cryptsetup luksAddKey test-crypt 
sudo cryptsetup luksChangeKey test-crypt 
sudo cryptsetup luksRemoveKey test-crypt 
```