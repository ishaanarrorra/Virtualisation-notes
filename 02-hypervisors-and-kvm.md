# Day 2: Hypervisors and KVM

**Main outcome:** Identify KVM, QEMU, libvirt, virtio, and nesting.

---

## 1. Big Picture: Where Everything Sits

```
Physical hardware (CPU, RAM, disk, NIC)
        ↓
Linux Kernel + KVM      ←  hypervisor layer (uses VT-x / AMD-V)
        ↓
QEMU                    ←  emulates virtual hardware and devices
        ↓
libvirt                 ←  management API / services
        ↓
virsh / virt-manager    ←  command-line / graphical clients
        ↓
VM / guest OS
```

---

## 2. Type 1 vs Type 2 Hypervisors

| Type | Where It Runs | Examples | Notes |
|---|---|---|---|
| **Type 1** | Directly on hardware / at kernel layer | ESXi, Hyper-V, KVM-based Linux | Bare metal (efficient) |
| **Type 2** | On top of a conventional host OS | VirtualBox, VMware Workstation | Easier for desktop |

> **Important:** This classification is useful, but real component roles matter more than the label.
>
> **Example:** KVM is a kernel module inside Linux, so it blurs the line between Type 1 and Type 2.

---

## 3. KVM – Kernel-based Virtual Machine

KVM adds virtualisation facilities to Linux.

- On x86, Intel VT-x or AMD-V helps execute guest CPU instructions efficiently.
- KVM exposes facilities through `/dev/kvm`.
- KVM is not a complete GUI or cloud orchestration platform.
- Linux supplies scheduling, memory management, and other host functions.

> **Simple idea:** KVM turns the Linux kernel into a hypervisor. The Linux kernel still does normal OS work, but it can also run VMs.

---

## 4. QEMU – The Virtual Hardware Builder

QEMU models a machine and its devices.

- With KVM, guest CPU execution is accelerated by hardware.
- QEMU supplies much of the surrounding virtual hardware and device handling.
- QEMU can also emulate CPUs in software:
  - Useful across architectures (e.g., ARM on x86)
  - Generally much slower for ordinary server workloads

> **Simple idea:** KVM makes the CPU fast. QEMU builds the fake motherboard, disk controller, network card, etc.
>
> **Together:** KVM + QEMU = fast CPU execution + virtual hardware.

---

## 5. libvirt, virsh, and virt-manager

| Component | Role | Simple Idea |
|---|---|---|
| **libvirt** | Provides virtualisation-management APIs and services | The manager / API |
| **virsh** | Command-line client for libvirt | CLI control |
| **virt-manager** | Graphical client for libvirt | GUI control |

### How a Request Flows

```
User runs virsh or virt-manager
        ↓
libvirt receives the request
        ↓
libvirt arranges QEMU and its configuration
        ↓
QEMU uses KVM for CPU acceleration
        ↓
VM runs
```

Some distributions use modular libvirt daemons rather than one monolithic service.

> **Simple idea:** You don't talk to QEMU directly. You talk to libvirt, and libvirt talks to QEMU/KVM.

---

## 6. Virtio Devices

Virtio is a set of virtualisation-aware device interfaces.

**Examples:**

- `virtio-net` – network
- `virtio-blk` – block disk
- `virtio-scsi` – SCSI storage

### Why Use Virtio?

- It reduces the need to emulate older hardware behaviour.
- It is faster because the guest and hypervisor cooperate.

**But:** The guest needs matching drivers (virtio drivers).

> ⚠️ **Warning example:** A migrated Windows VM with a missing storage driver may not boot when its disk is moved to a virtio controller.

> **Simple idea:** Virtio is a "fast lane" for virtual devices. But the guest OS must have the right driver installed.

---

## 7. Nested Virtualisation

Nested virtualisation means running a hypervisor inside a VM.

| Level | Name | Meaning |
|---|---|---|
| **L0** | Physical hypervisor | The real host |
| **L1** | VM running another hypervisor | First-level guest |
| **L2** | Guest created inside L1 | Second-level guest |

- **Requirements:** The outer environment must expose and support the required CPU features.
- **Use cases:** Useful for labs, testing, and training.
- **Constraints:** Compatibility, performance, and operational limitations.

> **Simple idea:** A VM that itself runs other VMs.

---

## 8. Key Command Examples (Conceptual)

| Task | Tool | Example |
|---|---|---|
| List VMs | virsh | `virsh list --all` |
| Start VM | virsh | `virsh start vmname` |
| Shut down VM | virsh | `virsh shutdown vmname` |
| View VM info | virsh | `virsh dominfo vmname` |
| Graphical management | virt-manager | Open GUI, click VM |

---

## 9. Common Confusion

- **KVM vs QEMU:** KVM accelerates the CPU. QEMU emulates devices and can also emulate CPUs.
- **KVM vs libvirt:** KVM is the kernel module. libvirt is the management layer.
- **virsh vs virt-manager:** Both talk to libvirt. One is CLI, one is GUI.
- **Virtio vs emulated devices:** Virtio is faster but needs guest drivers. Emulated devices are more compatible but slower.
- **Type 1 vs Type 2:** Useful labels, but real roles matter more.

---

## 10. Self-Check Questions

**(a) What does KVM add to Linux?**

<details>
<summary>Show answer</summary>

KVM turns the Linux kernel into a hypervisor. It lets virtual machines use the real CPU's virtualisation features, so guest CPU instructions run fast on the physical CPU.

</details>

**(b) What does QEMU do when used with KVM?**

<details>
<summary>Show answer</summary>

QEMU creates and manages the VM. It provides the virtual devices – virtual disk, network card, display, USB, etc. It uses KVM to make the CPU run fast.

</details>

**(c) What is libvirt's role?**

<details>
<summary>Show answer</summary>

libvirt is a management layer. It gives a common way to manage VMs across different hypervisors, like KVM/QEMU, Xen, etc. It handles VM lifecycle, storage, and networking. Tools like virsh and virt-manager use libvirt.

</details>

**(d) What is the difference between virsh and virt-manager?**

<details>
<summary>Show answer</summary>

- virsh is a command-line tool for managing VMs through libvirt.
- virt-manager is a graphical tool for managing VMs through libvirt.

They do similar things, but one is CLI and the other is GUI.

</details>

**(e) Name three virtio device types.**

<details>
<summary>Show answer</summary>

- `virtio-net` – network
- `virtio-blk` – block disk
- `virtio-scsi` – SCSI storage

</details>

**(f) Why might a migrated Windows VM fail to boot with a virtio disk?**

<details>
<summary>Show answer</summary>

Because Windows does not have the virtio storage driver installed. The boot disk is now on a virtio controller, but Windows cannot read it without the driver, so it fails to boot.

</details>

**(g) What are L0, L1, and L2 in nested virtualisation?**

<details>
<summary>Show answer</summary>

- **L0** → Real physical host / root hypervisor running on bare metal
- **L1** → VM running on L0, which itself acts as a hypervisor
- **L2** → A VM running inside the L1 hypervisor

</details>

**(h) Why is nested virtualisation useful but limited?**

<details>
<summary>Show answer</summary>

**Useful:** You can run VMs inside VMs – good for testing hypervisors, development, cloud labs, and running virtualised workloads inside a VM.

**Limited:** It adds performance overhead, needs special hardware/software support, is more complex to configure, and not all features work perfectly. L2 is usually slower than L1.

</details>

---

## 11. Key Takeaways

- Type 1 hypervisors run at the hardware/kernel layer; Type 2 run on a host OS.
- KVM is a Linux kernel module that uses Intel VT-x / AMD-V and exposes `/dev/kvm`.
- QEMU models virtual hardware; with KVM, CPU execution is accelerated.
- libvirt is the management API; virsh is CLI; virt-manager is GUI.
- Virtio provides fast virtual devices, but guests need matching drivers.
- Nested virtualisation runs a hypervisor inside a VM: L0 → L1 → L2.
- Real component roles matter more than the Type 1 / Type 2 label.

---

[⬅ Day 1 – Fundamentals](01-fundamentals.md) · [Back to Index](README.md)
