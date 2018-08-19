# odroid-xu4-arch-linux
I have been wrestling this odroid mini computer to the ground and I will win and hopefully also not forget everything I have learned

* [Basic instructions from archlinuxarm.org](https://archlinuxarm.org/platforms/armv7/samsung/odroid-xu4)

For my specific case, I am actually booking from one odroid card and loading arch on another odroid card. This is logging in (boot to terminal on a working card) via the MMC module, with a non-working (future working) card in the uSD spot.

```bash
# do this all as root (don't typo)
su root # default password: odroid
# mount the uSD / MMC to a place
mount /dev/mmcblk1 /root
# download the package with wget, curl, w/e, and untar it to /root (which is the uSD MMC)
# you got this part
cd /root/boot
sh sd_fusing.sh /dev/mmcblk1
```
