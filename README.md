# What?
VMware Workstation 25.0.1 kernel modules (vmmon + vmnet) compiled for Fedora 44 / **kernel 6.19.14-300.fc44.x86_64**

Source: https://github.com/ngodn/vmware-vmmon-vmnet-linux-6.16.x.git

# Why?
- It's VMware's fault.
- I don't want to compile this anymore.

# Will you?
Keep this updated? - No, and you shouldn't be using this blobs anyways.

---

# Load
```bash
cp ./*.ko /lib/modules/$(uname -r)/misc/
depmod -a
modprobe vmmon && modprobe vmnet
```

# Test?

```bash
lsmod | grep -q "^vmnet" && echo "vmnet OK" || echo "vmnet FAIL"
```
