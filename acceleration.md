Hardware Acceleration
=====================

Ops can utilize your CPU's virtualization extension through the various
supported hypervisors. Ops will only attempt to enable acceleration on systems
that support it.

To have Ops enable hardware acceleration when running an image, first check to
see if your system's chip has the appropriate support. An easy way to do this on
modern Linux based systems is to run the following command:

```sh
$ grep -woE 'svm|vmx' /proc/cpuinfo | uniq
```

If you have a supported Intel CPU you should see the output `vmx`, for supported
AMD processors you should see `svm`. If your CPU does not support a
virtualization extension you will see no output. If you are certain that your
CPU provides virtualization support and yet you see no output, then please
ensure that the extension is enabled in your BIOS/UEFI system.

If using KVM you need to ensure that your user is a member of the `kvm` group.
Ops will not attempt to enable acceleration if your user is not a member of this
group. First check if your user is apart of the kvm group:

```sh
$ groups
```

If your user is a member of the group you should see `kvm` in the list. If not
already a member you can add yourself to the group with the following command:

```sh
$ usermod -aG kvm `whoami`
```

The change will take affect upon the next login. Finally, you can check to see
if Ops is using virtualization support by inspecting the command it uses to run
an image. You can do this by enabling verbose output when using the `run` or
`load` command. So assuming I have hardware acceleration enabled in my run-time
configuration:

```json
{
    "RunConfig": {
        "UseKvm": true
    }
}
```


Then the following command should indicate that hardware acceleration is being used:

```sh
$ ops run -v hello
...
qemu-system-x86_64 -drive file=/home/externalreality/.ops/images/hello.img,format=raw,if=virtio -device virtio-net,netdev=n0 -netdev user,id=n0 -enable-kvm -nodefaults -no-reboot -device isa-debug-exit -m 2G -display none -serial stdio
...
```

Notice that Qemu command is generated with `-enable-kvm` option.

