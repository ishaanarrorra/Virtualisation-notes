# 🖥️ Virtualisation Notes

A nine-day foundation in virtualisation, from physical servers to cloud infrastructure, with connections to OpenStack and OpenShift. Covers theory, hands-on exercises, and quizzes built around a Linux/KVM lab.

---

### 📚 Course Index

| Day | Topic | What You'll Learn |
|---|---|---|
| 01 | [Fundamentals](01-fundamentals.md) | Hosts, guests, VMs, and types of virtualisation |
| 02 | [Hypervisors and KVM](02-hypervisors-and-kvm.md) | KVM, QEMU, libvirt, virtio, and nested virtualisation |
| 03 | [CPU and Memory](03-cpu-and-memory.md) | Scheduling, overcommit, NUMA, and memory management |
| 04 | [Storage](04-storage.md) | Disks, images, snapshots, and backups |
| 05 | [Networking](05-networking.md) | vNICs, bridges, routing, NAT, VLAN/VXLAN, and MTU |
| 06 | [Provisioning and Lifecycle](06-provisioning-and-lifecycle.md) | Boot sequence, cloud-init, and guest agents |
| 07 | [Migration and Recovery](07-migration-and-recovery.md) | Cold/live migration, evacuation, HA, RPO, and RTO |
| 08 | [Advanced Topics](08-advanced-topics.md) | PCI passthrough, GPUs, SR-IOV, and troubleshooting by layer |
| 09 | [Platform Connections](09-platform-connections.md) | Mapping concepts to OpenStack and OpenShift |

---


### 🧪 Lab Setup

A computer with hardware virtualisation support, about **16 GiB RAM** and **100 GB free SSD**. Two Linux guests with **2 vCPUs** and **2–4 GiB RAM** each are enough for most exercises.

---

### 🎯 Goal

By the end, be able to explain how a VM uses physical resources, distinguish KVM, QEMU, and libvirt, trace virtual networking and storage, understand migration and recovery, troubleshoot by layer, and connect these concepts to OpenStack and OpenShift.
