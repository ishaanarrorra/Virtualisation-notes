# Day 1: Fundamentals of Virtualisation

**Main outcome:** Explain hosts, guests, VMs, and virtualisation types.

---

## 1. Physical Server and Operating System

A physical server has CPUs, RAM, storage, network interfaces and firmware. The CPU executes instructions; RAM holds active working data; storage retains files; a NIC carries network traffic. Linux or Windows manages these resources for applications. Without VMs, multiple applications usually share the same operating system and its configuration.

### Core Mental Model

```
Applications
Guest OS              →  the OS inside a VM
Virtual hardware      →  vCPU, vRAM, vDisk, vNIC, virtual firmware
Hypervisor / VMM      →  creates, runs and controls VMs
Physical hardware     →  CPU, RAM, storage, NIC, firmware
Host / Compute node   →  the physical server
```

### In OpenStack Terms

```
Management Plane → Compute Node → Hypervisor → Instance/VM → Guest OS
```

---

## 2. Key Terms

| Term | Meaning | Simple Idea |
|---|---|---|
| **Physical Server** | Real hardware: CPU, RAM, storage, NIC, firmware | The actual machine |
| **Host** | Physical server that supplies resources to VMs | Building owner |
| **Guest** | Operating system inside a VM | Tenant |
| **Hypervisor / VMM** | Software layer that enables and controls guest execution | Landlord / referee |
| **VM** | Software-defined computer with virtual hardware and its own OS | Apartment |
| **Compute Node** | Server running compute workload | Building full of apartments |
| **Management Plane** | Creates, schedules, and controls workloads | Property manager |
| **Instance** | Provisioned compute resource, commonly a VM in OpenStack | Rented apartment |

---

## 3. Physical Server and OS

A physical server has:

- **CPU** – executes instructions
- **RAM** – holds active working data
- **Storage** – keeps files
- **NIC** – carries network traffic
- **Firmware** – low-level boot/hardware control

> **Note:** Linux or Windows manages these resources for applications.

---

## 4. Virtual Machine

Virtualisation presents software-defined hardware to a guest operating system. A VM sees virtual CPUs, memory, disk, network cards, and firmware.

The physical hardware still performs the work; the virtualisation layer controls how it is shared. A VM is therefore a computer environment with its own operating system, not merely a folder or application window.

---

## 5. Resource Example

A server with **16 physical cores** and **64 GiB RAM** might run:

| VM | vCPU | RAM |
|---|---|---|
| Ubuntu | 4 | 8 GB |
| Windows | 4 | 16 GB |
| Rocky | 2 | 8 GB |
| **Total** | **10 vCPU** | **32 GB** |

**But remember:**

- These settings do not automatically reserve dedicated physical cores.
- The host also needs capacity for its own services.
- Virtualisation itself has overhead.
- vCPUs are usually scheduled onto physical cores, not permanently tied to them.

---

## 6. Why Virtualise?

| Reason | Simple Explanation |
|---|---|
| **Consolidation** | Use hardware better; run many VMs on one server |
| **Isolation** | Each VM has its own OS, so problems are separated |
| **Images** | Prebuilt templates make provisioning fast |
| **Compatible hosts** | Workload can move between similar hosts |
| **Protected copies** | Snapshots/backups help recovery |

> **Caveat:** Isolation does not remove shared dependencies.

---

## 7. Virtualisation Approaches

| Approach | What It Does | Notes |
|---|---|---|
| **Full virtualisation** | Presents a complete guest machine | Guest usually unmodified |
| **Hardware-assisted** | Uses CPU features like Intel VT-x / AMD-V | More efficient execution |
| **Paravirtualisation** | Guest cooperates with hypervisor | Uses special drivers, e.g. virtio |
| **Emulation** | Reproduces hardware in software | Can run another CPU architecture; slower |
| **Containers** | OS-level isolation | Shares the kernel; not a full VM |

### (a) Full Virtualisation

The hypervisor gives the guest a complete fake computer: virtual CPU, virtual RAM, virtual network card, virtual firmware.

The guest OS thinks it is running on real hardware; it usually does not need to be modified.

### (b) Hardware-Assisted Virtualisation

This is not really a separate "type" like full vs paravirtual. It is a CPU feature that helps the hypervisor run VMs efficiently.

Intel and AMD CPUs have instructions like VT-x and AMD-V that make it easier to trap privileged instructions and switch between guest and hypervisor.

So, full virtualisation can be implemented using hardware-assisted virtualisation.

### (c) Paravirtualisation

Here the guest OS is aware that it is virtualised. It uses special drivers to talk to the hypervisor more directly.

Instead of pretending to be a real disk or network card, the hypervisor says: *"Use this special driver, and we can communicate more efficiently."*

The most common example is **virtio**. Virtio is a set of paravirtualised drivers for disk, network, etc.

> **Important:** Paravirtualisation does not have to replace hardware-assisted CPU virtualisation. It is often used only for I/O devices like disk and network.

### (d) Emulation

Emulation means software pretends to be hardware. Sometimes it pretends to be a completely different CPU architecture.

**Example:** QEMU can emulate an ARM CPU on an x86 laptop.

The guest OS thinks it is running on ARM hardware, but every instruction is translated in software.

Emulation is flexible but usually slower because the real CPU is not running the guest instructions directly.

### (e) Containers

Containers are often mentioned near virtualisation, but they are different.

A container does not run its own full guest OS with its own kernel. Instead, containers share the host kernel and isolate the application using OS features.

So containers give OS-level isolation, not full machine virtualisation.

- **VM** = Separate apartment with its own kitchen, bathroom, and utilities.
- **Container** = Separate room in a shared house. You have privacy, but share the kitchen and bathroom.

Containers are lighter and start faster, but isolation is weaker than a full VM.

### (f) The Overlap: KVM Guests

KVM guests can combine hardware-assisted CPU execution with paravirtualised virtio devices.

This means a single KVM VM can use both approaches at the same time:

- **CPU:** Uses hardware-assisted virtualisation via Intel VT-x or AMD-V. The guest CPU instructions run efficiently on the real CPU.
- **Disk and Network:** Uses paravirtualised virtio drivers. The guest cooperates with the hypervisor for faster I/O.

So KVM is not "only full virtualisation" or "only paravirtualisation". It is a mix: hardware-assisted for CPU, paravirtualised for devices.

### The Main Point

These approaches are not strict boxes. They overlap.

- Full virtualisation can use hardware assistance.
- Paravirtualisation can be used for some parts, like disk and network, while the CPU uses hardware assistance.
- Emulation can be used for a foreign CPU or for specific devices.
- Containers are a different model: OS-level isolation, sharing the kernel.
- KVM commonly combines hardware-assisted CPU virtualisation + paravirtualised virtio I/O.

> Virtualisation can be done in different ways, and real hypervisors like KVM often blend several of them to get both speed and compatibility.

---

[⬅ Back to Index](README.md) · [Next: Day 2 – Hypervisors and KVM ➡](02-hypervisors-and-kvm.md)
