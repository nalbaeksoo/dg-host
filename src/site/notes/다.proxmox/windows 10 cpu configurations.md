---
{"dg-publish":true,"permalink":"/다.proxmox/windows 10 cpu configurations/","dgPassFrontmatter":true,"noteIcon":""}
---

#### CreateDate : 2026-07-13

### GOAL
Optimized Proxmox on windows 10 guest OS 

### SOLUTION

# Proxmox VE enhancement report draft

## Suggested Bugzilla fields

- Product: `Proxmox VE`
- Component: `Qemu Server` / `qemu-server` (select the closest available value)
- Version: `8.4`
- Severity: `enhancement`
- Summary: `[Feature request] Conditionally recommend/enable hv-tlbflush for large SMP Windows guests on supported hosts`

## Report body

### Summary

A 64-vCPU Windows 10 guest showed sustained kernel/System CPU usage while the guest was otherwise idle. In a before/after observation, adding the Hyper-V TLB flush enlightenment (`hv-tlbflush`) was strongly correlated with a reduction in average guest CPU usage from **32.67% to 4.77%**. The Windows `System` process (PID 4) dropped from **23.82% to 2.47%**.

The only VM CPU configuration change was adding `+hv-tlbflush`, followed by a full shutdown and start. No BSOD or functional regression was observed.

Please consider enabling this enlightenment conditionally for newly created, compatible large Windows guests, or providing a prominent recommended option in the VM creation UI/documentation when `ostype: win10` is used with a large vCPU count. Existing VMs should remain unchanged.

### Environment

Proxmox VE host:

- `pve-manager: 8.4.17`
- Kernel: `6.8.12-20-pve`
- CPU: AMD EPYC 7742
- 1 physical socket, 64 cores, 128 threads
- 1 NUMA node, CPUs 0-127

Guest:

- Windows 10 Pro for Workstations
- Version 22H2, build 19045.7417
- 64 vCPUs: 1 socket x 64 cores
- 128 GiB RAM
- `ostype: win10`
- `cpu: x86-64-v2-AES`
- `machine: pc-i440fx-9.0`
- `numa: 0`

The host did not appear CPU- or I/O-saturated at the time of observation:

- Host CPU usage: approximately 27%
- Load average: approximately 49 on 128 logical CPUs
- I/O delay: 0.29%
- Swap usage: 0

The VM had ten E1000 NICs and three SATA disks, but these devices were unchanged during the test. Guest network, disk, and paging counters were idle or near zero, and DPC/interrupt time was also near zero.

### Original relevant VM configuration

```text
ostype: win10
sockets: 1
cores: 64
cpu: x86-64-v2-AES
memory: 131072
numa: 0
machine: pc-i440fx-9.0
```

The generated QEMU CPU arguments already exposed several Hyper-V enlightenments, including `hv-vpindex`, but did not expose `hv-tlbflush`.

### Behavior before enabling `hv-tlbflush`

Guest-side performance-counter averages:

| Counter | Average |
|---|---:|
| Total CPU | 32.67% |
| Windows System process (PID 4) | 23.82% |
| Privileged CPU time | 26.84% |
| DPC time | 0.00% |
| Interrupt time | 0.05% |

The workload was therefore predominantly Windows kernel/System CPU time rather than application, disk, network, paging, DPC, or interrupt activity. The Proxmox VM graph showed sustained CPU usage of approximately 25-50%.

### Reproduction and workaround

1. Configure and boot a Windows 10 guest with 64 vCPUs and the CPU type `x86-64-v2-AES`.
2. Leave the guest otherwise idle and measure total CPU, System PID 4, privileged, DPC, interrupt, disk, network, and paging activity.
3. Fully shut down the VM.
4. Add `hv-tlbflush`:

   ```shell
   qm set <VMID> --cpu 'x86-64-v2-AES,flags=+hv-tlbflush'
   ```

5. Start the VM and repeat the measurements under the same conditions.

### Result after enabling `hv-tlbflush`

Guest-side performance-counter averages:

| Counter | Before | After | Reduction |
|---|---:|---:|---:|
| Total CPU | 32.67% | 4.77% | approximately 85% |
| Windows System process (PID 4) | 23.82% | 2.47% | approximately 90% |
| Privileged CPU time | 26.84% | 3.92% | approximately 85% |
| DPC time | 0.00% | 0.00% | — |
| Interrupt time | 0.05% | 0.01% | — |

The Proxmox graph dropped immediately from approximately 25-50% before the change to approximately 3-10% afterward. No BSOD or other functional regression was observed.

### Expected behavior / enhancement request

For a compatible modern Windows guest, the Hyper-V TLB flush enlightenment can substantially reduce the cost of cross-vCPU TLB invalidation. This appears especially important for large SMP Windows guests, even when the Proxmox host is not overcommitted or saturated.

Please consider one or more of the following:

1. For newly created VMs only, enable `hv-tlbflush` by default for compatible Windows guest types, including `ostype: win10`, above an appropriate vCPU threshold.
2. Gate the default on relevant KVM/CPU capabilities, the required Hyper-V enlightenments, and compatibility across all configured cluster migration targets.
3. Keep an explicit opt-out and do not modify existing VMs.
4. If a conditional default is still considered too risky, add a recommended checkbox in the VM creation wizard and a visible Processor-options recommendation for large Windows guests with sustained `System`/PID 4 CPU usage.
5. If the UI/help text still presents this option mainly as an optimization for “overcommitted Windows guests,” broaden it to “large SMP and/or overcommitted Windows guests.” This test showed a large improvement without host saturation.

### Compatibility considerations

I understand that Proxmox previously avoided automatically enabling `hv-tlbflush` because it could cause guest BSODs or boot problems on some older CPUs or affected kernel/KVM versions. I am therefore not requesting an unconditional default for every Windows VM. A host-capability-gated, Windows-version-aware, migration-safe, large-vCPU-specific policy—or at minimum a documented/prominent opt-in—would be appropriate.

This result is from one hardware and guest configuration and includes a cold restart between the two measurements. It should therefore be treated as a strong observed correlation rather than proof from a controlled multi-run experiment. Broader validation is appropriate before changing any default. I can provide additional logs or perform an `off -> on -> off` test with equal idle observation periods after each cold start if needed.

### References

- QEMU Hyper-V enlightenments documentation (`hv-tlbflush` and prerequisites): https://www.qemu.org/docs/master/system/i386/hyperv.html
- Initial Proxmox patch that automatically added `hv-tlbflush`: https://lists.proxmox.com/pipermail/pve-devel/2019-June/037720.html
- Historical Proxmox patch exposing `hv-tlbflush` as an optional flag and noting the old-CPU BSOD concern: https://lists.proxmox.com/pipermail/pve-devel/2019-July/038392.html
- QEMU issue concerning older Intel hosts without the relevant EPT/VPID capabilities: https://bugs.launchpad.net/qemu/+bug/1837851

### Attachments to include

1. Proxmox CPU graph showing the exact configuration-change/restart point.
2. Sanitized output of `qm config 900` before and after the change.
3. Sanitized output of `qm showcmd 900 --pretty` before and after the change.
4. Windows counter results before and after the change.
5. Output of `pveversion -v`, `uname -a`, `/usr/bin/kvm --version`, and `lscpu`.

Please redact IP addresses, MAC addresses, storage names, guest names, and other site-specific identifiers before attaching the command output.



### REFERENCES
codex 5.6 sol