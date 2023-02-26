# clevis-and-tang
Using Clevis and Tang to unlock fedora 37


First, make sure you have a Tang server that Clevis can reach out to in order to unlock the drives.

Second,image a server with a fresh copy of Fedora 37 and make sure when you install it to encrypt the drive where you are installing Fedora.

Once the server is installed and rebooted do the following:

Install clevis, clevis_luks and clevis-dracut

```sudo dnf install clevis clevis-luks clevis-dracut```

Find the partition that is LUKS encrypted

```sudo blkid -t TYPE=crypto_LUKS -o device```

Verify this is the correct partition by dumping the LUKS information

```sudo cryptsetup luksDump <device from command above>```

Bind Clevis to the device and speficy the server that will provide the LUKS key

```sudo clevis luks bind -d <device from command above> tang '{"url":"tangserver.your.fqdn"}'```

Dump the LUKS information a second time and look for the LUKS key in slot 1 and also look for clevis in the Tokens: section.  You can also look for the LUKS information in cockpit.

```sudo cryptsetup luksDump <device from command above>```

Create a new grub that will wait for the network to start working before proceeding to reach out to the tang server

```sudo grubby --update-kernel=ALL --args="rd.neednet=1"```

Generate a new dracut

```sudo dracut -fv --regenerate-all```

Reboot and watch as your fedora server magically unlocks itself.  It may take a little while for the luks unlock prompt to go away.  Patience is your friend here.

```sudo reboot```
