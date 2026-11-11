# RAID levels at a glance / RAID seviyelerine genel bakış

| Level | Min. disks | Usable capacity (n disks of size S) | Fault tolerance | Notes |
|---|---|---|---|---|
| RAID 0 | 2 | n × S | **none** (any loss = all data lost) | Speed only, never for important data |
| RAID 1 | 2 | S | n − 1 (mirror) | Simple, good reads, 50 % overhead with 2 disks |
| RAID 5 | 3 | (n − 1) × S | 1 disk | Write penalty; risky with big disks (long rebuild, second failure / URE) |
| RAID 6 | 4 | (n − 2) × S | 2 disks | Preferred parity level for large disks |
| RAID 10 | 4 (even) | (n / 2) × S | 1 per mirror pair (guaranteed 1) | Best mixed performance for databases/VMs |
| RAID-Z1/Z2/Z3 (ZFS) | 3/4/5 | (n − p) × S | 1/2/3 | Checksummed, no write hole; see [`zfs.md`](zfs.md) |

## 🇬🇧 English
- **RAID is not a backup.** It protects against disk failure, not deletion, ransomware, corruption or controller loss.
- For large disks (≥ 8 TB) prefer **RAID 6 / RAID 10 / RAID-Z2**; single parity rebuilds can take days.
- Use disks from different batches, monitor SMART, keep a **hot spare**, and test rebuilds before production.
- Hardware RAID: enable the battery/flash-backed write cache only if protected; keep controller firmware current; note the exact controller model so a replacement can import the array.

## 🇹🇷 Türkçe
- **RAID yedek değildir.** Disk arızasına karşı korur; silme, fidye yazılımı, bozulma ve kontrolcü kaybına karşı korumaz.
- Büyük disklerde (≥ 8 TB) **RAID 6 / RAID 10 / RAID-Z2** tercih edin; tek pariteli yeniden inşa günler sürebilir.
- Farklı üretim partilerinden diskler kullanın, SMART izleyin, **hot spare** bulundurun, üretimden önce rebuild test edin.
- Donanımsal RAID: yazma önbelleğini yalnızca batarya/flash korumalıysa açın; firmware'i güncel tutun; yedek kontrolcünün diziyi içe aktarabilmesi için model bilgisini kaydedin.
