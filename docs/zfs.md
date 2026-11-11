# ZFS cheat sheet

## 🇬🇧 English

### Create pools (use stable device names)
```bash
ls -l /dev/disk/by-id/                      # prefer by-id over /dev/sdX (names can change)
zpool create -o ashift=12 tank mirror /dev/disk/by-id/ata-DISK1 /dev/disk/by-id/ata-DISK2
zpool create -o ashift=12 tank raidz2 /dev/disk/by-id/...  # 4+ disks, survives 2 failures
zpool create -o ashift=12 tank mirror d1 d2 mirror d3 d4   # striped mirrors (RAID10-like), best IOPS
```
- `ashift=12` (4 KiB sectors) is the safe default for modern disks and **cannot be changed later**.
- Do not mix vdev types/sizes in a pool without a reason. Redundancy lives in the vdev: **losing one vdev loses the pool**.

### Daily operations
```bash
zpool status -v            # health, errors, resilver/scrub progress
zpool list                 # capacity, fragmentation
zfs list -o name,used,avail,refer,compression,mountpoint
zfs create tank/data
zfs set compression=lz4 tank       # nearly free, usually a win
zfs set quota=500G tank/data
zpool scrub tank           # verify all checksums; schedule monthly
zpool clear tank           # clear error counters after fixing the cause
```

### Replace a failed disk
```bash
zpool status tank                              # find the FAULTED/UNAVAIL disk
zpool replace tank <old-disk> <new-disk-by-id>
zpool status tank                              # watch the resilver; do not reboot unnecessarily
```

### Snapshots and replication
```bash
zfs snapshot tank/data@2026-01-01
zfs list -t snapshot
zfs rollback tank/data@2026-01-01              # discards changes after the snapshot
zfs send -R tank/data@snap | ssh backup-host zfs recv -F backup/data       # full
zfs send -i tank/data@snap1 tank/data@snap2 | ssh backup-host zfs recv backup/data   # incremental
```
Snapshots are **not** backups until a copy lives on another system.

### Memory / ARC
ZFS caches in RAM (ARC). It gives memory back under pressure, so "free RAM is low" is normal. Inspect with `arc_summary`. Deduplication needs very large RAM - avoid unless you have measured the benefit.

## 🇹🇷 Türkçe

### Havuz oluşturma
Cihazları `/dev/disk/by-id/` ile verin (`/dev/sdX` adları değişebilir). `ashift=12` modern diskler için güvenli varsayılandır ve **sonradan değiştirilemez**. Yedeklilik vdev içindedir: **bir vdev kaybedilirse havuz kaybedilir**. Eşleşmiş çiftler (mirror şeritleri) en iyi IOPS'u, `raidz2` 4+ diskte 2 disk arızası toleransını verir.

### Günlük işlemler
`zpool status -v` sağlık, `zpool list` kapasite, `zfs create` dataset, `zfs set compression=lz4` (neredeyse bedava kazanç), `zpool scrub` tüm checksum'ları doğrular (aylık planlayın).

### Bozuk disk değiştirme
`zpool status` ile arızalı diski bulun → `zpool replace tank <eski> <yeni-by-id>` → resilver bitene kadar izleyin.

### Snapshot ve replikasyon
`zfs snapshot`, `zfs rollback` (snapshot sonrası değişiklikleri siler), `zfs send | ssh ... zfs recv` ile tam/artımlı kopya. Başka sisteme kopya yoksa snapshot **yedek değildir**.

### Bellek / ARC
ZFS belleği önbellek (ARC) olarak kullanır, baskı olduğunda geri verir; "boş RAM az" normaldir (`arc_summary`). Deduplication çok RAM ister; ölçmeden kullanmayın.
