First pull down the repository using the following command:

```bash

sudo git clone https://github.com/TomKranenburg/ipxe-uefi

```

Then navigate into the newly created directory:

```bash

cd ./ipxe-uefi/src

```

## Compile Instructions

Compile on a standard Linux build. At the time of writing Ubuntu 22.04 was used.

From the ```./src``` directory run:

```bash

sudo make bin-x86_64-efi/ipxe.efi EMBED=embed.ipxe

```

The resulting ```ipxe.efi``` file is the bootloader defined in the DHCP config. This allows for true UEFI network boot as well as graceful hand off to local GPT partitions. Everything was rigorously tested on the most modern operating systems and hardware available at the time of writing.

Within the ```embed.ipxe``` file various operating parameter are defined:

```
:netboot
chain tftp://${next-server}/vanguard/single/${netX/ip}.ipxe || chain tftp://${next-server}/vanguard/main.ipxe || prompt --key s --timeout 10000 Chainloading failed, hit 's' for the iPXE shell; reboot in 10 seconds. Do not change file paths without recompiling! - Thomas Kranenburg && shell || reboot
```

Here the bootloader first looks for an IP specified menu located at: 

[DHCP IP ADDRESS]/vanguard/single/[HOST IP ADDRESS].ipxe. 

For example: ```tftp://10.1.0.3/vanguard/single/10.1.5.20.ipxe```

This allows for host specific menus to be deployed per system. Notice the file paths are hard coded. Any change to file paths will sadly require a recompile.
If this IP specific menu is not present it then checks for a default menu. 

Now place the bootloader in: 

```bash

/var/www/html/vanguard

```

This will then be delivered via the DHCP during boot.
