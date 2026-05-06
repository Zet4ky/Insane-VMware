## What?
VMware Workstation 25.0.1 kernel modules (vmmon + vmnet) compiled for Fedora 44 / **kernel 6.19.14-300.fc44.x86_64**

Source: https://github.com/ngodn/vmware-vmmon-vmnet-linux-6.16.x.git

## Manually compile the modules.
#### Install the dependencies:
```bash
dnf install -y kernel-headers kernel-devel gcc make git
```

#### Build.
Note: Im doing a silly modification to the script here, if for some reason *you* are blindly following this and it doesnt work for you thats a *you* problem.
```bash
git clone https://github.com/ngodn/vmware-vmmon-vmnet-linux-6.16.x.git
cd vmware-vmmon-vmnet-linux-6.16.x
sed -i '/\[\[ \$EUID -eq 0 \]\]/,/exit 1/d' repack_and_patch.sh
sed -i '/command -v vmware/,/fi$/c\VMWARE_VERSION="25.0.1"\nprint_success "Detected VMware Workstation version: $VMWARE_VERSION"' repack_and_patch.sh
sed -i 's/sudo /run0 /g' repack_and_patch.sh
bash repack_and_patch.sh
```


## Why?
*...VMware needs to compile several kernel modules...*

- It's VMware's fault.
- VMware isn't smart enough to compile the kernel modules by itself.
- I don't want to compile this anymore.
- Read the first one again.

## Will you?
Keep this updated? - No, and you shouldn't be using these blobs anyways.

---

## Load.
```bash
run0 cp ./*.ko /lib/modules/$(uname -r)/misc/
run0 depmod -a
run0 modprobe vmmon && run0 modprobe vmnet
```

## Test.
```bash
lsmod | grep -q "^vmmon" && echo "vmmon OK" || echo "vmmon FAIL"
lsmod | grep -q "^vmnet" && echo "vmnet OK" || echo "vmnet FAIL"
```

## Fix - VMware crashes on XKB events (XWayland)
*...VMware crashes when interacting with the VM...*

VMware 25.x ships a *garbage* `libX11` that crashes on `XkbGetMapChanges` under XWayland.

Replace libX11 with the system library:
```bash
run0 cp /usr/lib64/libX11.so.6.4.0 /usr/lib/vmware/lib/libX11.so.6/libX11.so.6
```

## Note - SELinux
Temporarily disable SELinux, then remember to re-enable it.
```bash
run0 setenforce 0
# ... Things going on here ...
run0 setenforce 1
```
