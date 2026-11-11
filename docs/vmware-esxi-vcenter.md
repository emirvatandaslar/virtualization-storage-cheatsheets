# VMware ESXi / vCenter cheat sheet

## 🇬🇧 English

### vMotion prerequisites (live migration with zero downtime)
- Both hosts managed by the same vCenter; **VMkernel adapter with the vMotion service enabled** on each host (dedicated VLAN, ≥ 10 GbE recommended).
- VM disks on **shared storage** visible to both hosts (or use Storage vMotion / shared-nothing vMotion when storage differs).
- Same port group / VLAN names for the VM's networks on both hosts.
- Compatible CPU families, or an **EVC** mode on the cluster.
- Time in sync (NTP) and DNS working for both hosts.

### HA basics
- Enable **vSphere HA** on the cluster; use **admission control** (reserve capacity for at least N host failures).
- Datastore heartbeating needs ≥ 2 shared datastores.
- Set VM restart priority for critical VMs; test with a planned host failure in a maintenance window.

### Useful `esxcli` / `vim-cmd` commands (ESXi shell/SSH)
```bash
esxcli system version get                       # ESXi version/build
esxcli hardware cpu list | head                 # CPU info
esxcli network nic list                         # physical NICs, link speed/state
esxcli network ip interface ipv4 get            # VMkernel IPs
esxcli storage core device list                # disks / LUNs
esxcli storage vmfs extent list                 # VMFS datastores and their backing devices
esxcli software vib list                        # installed VIBs (drivers)
esxcli system maintenanceMode set --enable true # enter maintenance mode (move/shut VMs first)
vim-cmd vmsvc/getallvms                         # list VMs and their IDs
vim-cmd vmsvc/power.getstate <vmid>             # power state
vim-cmd vmsvc/power.on|off|shutdown <vmid>
esxtop                                          # live CPU / memory / disk / network (press c, m, d, n)
```

### Troubleshooting pointers
- **High `%RDY` in esxtop (CPU ready > ~5 % per vCPU)** → too many vCPUs / overcommit; reduce vCPUs.
- **High `DAVG` (device latency) > 20–30 ms** → storage array / path issue; `KAVG` high → host queue/queue depth.
- **vMotion fails at 14 %/21 %** → check vMotion VMkernel connectivity (`vmkping -I vmkX <peer>`), MTU consistency, and shared datastore visibility.
- Keep host BIOS/firmware, ESXi build and VMware Tools versions consistent across the cluster.

## 🇹🇷 Türkçe

### vMotion ön koşulları (kesintisiz canlı taşıma)
- Her iki host aynı vCenter'da; her hostta **vMotion servisi açık VMkernel adaptörü** (ayrı VLAN, ≥ 10 GbE önerilir).
- VM diskleri iki hostun da gördüğü **paylaşımlı depolamada** (depolama farklıysa Storage vMotion / shared-nothing vMotion).
- VM ağları için iki hostta aynı port group / VLAN adları.
- Uyumlu CPU aileleri veya kümede **EVC** modu.
- NTP ile zaman senkronu ve çalışan DNS.

### HA temelleri
- Kümede **vSphere HA** açın; **admission control** ile en az N host arızası kadar kapasite ayırın.
- Datastore heartbeat için ≥ 2 paylaşımlı datastore gerekir.
- Kritik VM'lere yeniden başlatma önceliği verin; bakım penceresinde planlı host arızası ile test edin.

### Sorun giderme ipuçları
- **esxtop'ta yüksek `%RDY`** (vCPU başına ~%5 üzeri) → fazla vCPU / aşırı taahhüt; vCPU azaltın.
- **Yüksek `DAVG`** (> 20–30 ms) → depolama/yol sorunu; `KAVG` yüksekse host kuyruğu.
- **vMotion %14/%21'de takılıyor** → vMotion VMkernel erişimi (`vmkping -I vmkX <eş>`), MTU tutarlılığı ve paylaşımlı datastore görünürlüğü.
- Küme genelinde BIOS/firmware, ESXi build ve VMware Tools sürümlerini tutarlı tutun.
