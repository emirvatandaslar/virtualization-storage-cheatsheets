# Proxmox VE cheat sheet

## 🇬🇧 English

### VMs and containers
```bash
qm list                                   # all VMs on this node
qm start|shutdown|stop|reset <vmid>       # shutdown = ACPI, stop = hard power-off
qm config <vmid>                          # VM configuration
qm migrate <vmid> <target-node> --online  # live migration (shared storage, or add --with-local-disks)
qm snapshot <vmid> <name> --description "before upgrade"
qm rollback <vmid> <name>
pct list                                  # LXC containers
pct enter <ctid>                          # shell inside a container
```

### Backups
```bash
vzdump <vmid> --storage <backup-storage> --mode snapshot --compress zstd
qmrestore /path/vzdump-qemu-<vmid>-....vma.zst <new-vmid> --storage <storage>
pct restore <new-ctid> /path/vzdump-lxc-<ctid>-....tar.zst --storage <storage>
```
Schedule jobs in *Datacenter → Backup*; keep at least one copy **off-cluster**; test a restore regularly.

### Cluster and HA
```bash
pvecm status        # quorum, votes
pvecm nodes
ha-manager status   # HA resources and their state
pvesm status        # storages and free space
pveversion -v       # package versions (keep nodes on the same version)
```
- A cluster needs **quorum** (majority). Use an odd number of nodes or a QDevice for 2-node clusters.
- Use a **dedicated, redundant network for corosync**; do not saturate it with backups/migration traffic.
- Never remove a node by hand-editing files while the cluster is quorate; use `pvecm delnode` after the node is permanently offline.

### Ceph on Proxmox
```bash
pveceph status
pveceph pool create <name> --add_storages
```
See [`ceph.md`](ceph.md) for operating the cluster.

## 🇹🇷 Türkçe

### VM ve container komutları
Yukarıdaki komutlar: `qm list` (VM listesi), `qm shutdown` (ACPI ile kapatma) / `qm stop` (zorla kapatma), `qm migrate <vmid> <hedef> --online` (canlı taşıma), `qm snapshot` / `qm rollback`, `pct list` (LXC).

### Yedekleme
`vzdump` ile snapshot modunda zstd sıkıştırmalı yedek alın, `qmrestore` / `pct restore` ile geri yükleyin. Zamanlanmış işleri *Datacenter → Backup* altında tanımlayın; en az bir kopyayı **küme dışında** tutun ve geri yüklemeyi düzenli test edin.

### Küme ve HA
- Kümenin **quorum**'a (çoğunluk) ihtiyacı vardır: tek sayıda düğüm ya da 2 düğümlü kümede QDevice kullanın.
- corosync için **ayrı ve yedekli bir ağ** kullanın; yedek/taşıma trafiğiyle doldurmayın.
- Küme quorum'dayken dosyaları elle düzenleyerek düğüm silmeyin; düğüm kalıcı olarak kapandıktan sonra `pvecm delnode` kullanın.
- Düğümleri aynı Proxmox sürümünde tutun (`pveversion -v`).
