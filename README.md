# virtualization-storage-cheatsheets

Concise, bilingual (TR/EN) operations cheat sheets for **VMware vSphere, Proxmox VE, Ceph, ZFS and RAID**: the commands, rules of thumb and "what to check first" that matter in day-to-day data-center work.

**VMware vSphere, Proxmox VE, Ceph, ZFS ve RAID** için kısa, iki dilli (TR/EN) operasyon notları: günlük veri merkezi işlerinde işe yarayan komutlar, pratik kurallar ve "önce neye bakılır" listeleri.

| Topic | File |
|---|---|
| VMware ESXi / vCenter: vMotion, HA, `esxcli`, `esxtop` | [`docs/vmware-esxi-vcenter.md`](docs/vmware-esxi-vcenter.md) |
| Proxmox VE: `qm`, `pct`, `vzdump`, cluster, HA | [`docs/proxmox-ve.md`](docs/proxmox-ve.md) |
| Ceph: health, maintenance with `noout`, common warnings, RBD | [`docs/ceph.md`](docs/ceph.md) |
| ZFS: pools, scrub, replace disk, snapshots, send/recv | [`docs/zfs.md`](docs/zfs.md) |
| RAID levels: capacity, tolerance, advice | [`docs/raid-levels.md`](docs/raid-levels.md) |

> Commands are written from official documentation conventions and common operational practice; always test in a lab and check the documentation for **your version** before running on production systems.
>
> Komutlar resmi dokümantasyon ve yaygın operasyon pratiğine göre yazılmıştır; üretimde çalıştırmadan önce laboratuvarda deneyin ve **kendi sürümünüzün** dokümantasyonunu kontrol edin.

MIT licensed.
