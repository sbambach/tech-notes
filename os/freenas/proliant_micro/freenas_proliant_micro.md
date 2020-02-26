# FreeNAS on HP ProLiant Micro X3418 (P07203-421)

## Did you ever see this

Without this fix the installer stops booting and appears to be stuck at:

```txt
pcib0: <ACPI Host-PCI bridge> port 0xcf8-0xcff on acpi0
pcib0: _OSC returned error 0x10
pci0: <ACPI PCI bus> on pcib0
```

see also: [Fix for FreeNAS on HPE MicroServer Gen10 X3216 Stuck Console Issue](https://www.virten.net/2017/10/fix-for-freenas-on-hpe-microserver-gen10-x3216-stuck-console-issue/).

## Temporary fix while installing/updating

Wait for the FreeBSD Boot menu.

- press **3**
- Press **6** to select "Escape to loader prompt"
  - enter: **set hw.pci.realloc_bars=1**
  - enter: **boot**

## Permanent fix within FreeNAS UI

When you've already installed FreeNAS, the problem can be fixed by adding the tunable `hw.pci.realloc_bars=1` to the Boot Loader config. This step can be easily done from the Web-Gui:

- Open FreeNAS Web-Interface
- Login as **root**
- Navigate to **System > Tunables**
- Click **Add Tunable**
- Add the following tunable
  - Variable: **hw.pci.realloc_bars**
  - Value: **1**
  - Type: **Loader**
- Reboot NAS
