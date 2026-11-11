# Ceph operations cheat sheet

## 🇬🇧 English

### Health at a glance
```bash
ceph -s                    # overall status: health, mon quorum, OSDs up/in, PG states, I/O
ceph health detail         # explains every warning/error
ceph osd tree              # hosts / OSDs and whether they are up/down, in/out
ceph osd df tree           # utilization per OSD (look for outliers > 80 %)
ceph df                    # pool usage and raw capacity
ceph pg stat               # PG summary; want all "active+clean"
ceph osd pool ls detail    # size / min_size / pg_num / autoscale mode per pool
```

### Rules of thumb
- Replicated pools: `size 3`, `min_size 2`. With `size 2` / `min_size 1` one failure can cause data loss.
- Keep OSD utilization below ~70-75 %; Ceph warns at 85 % (`nearfull`), stops writes at 95 % (`full`). Plan expansion early.
- Use **at least 3 monitors** (odd number) on separate failure domains.
- Separate **public** and **cluster** networks where possible; ≥ 10 GbE for OSD nodes. Rebuild traffic is heavy.
- Let the PG autoscaler manage `pg_num`: `ceph osd pool set <pool> pg_autoscale_mode on`.

### Planned maintenance (one node at a time)
```bash
ceph osd set noout          # do not mark OSDs "out" and start rebalancing while the node is down
# ... patch / reboot the node ...
ceph -s                     # wait until its OSDs are back up and PGs are active+clean
ceph osd unset noout
```
Always finish with `ceph -s` showing `HEALTH_OK` before touching the next node.

### Common warnings
| Message | Meaning / first step |
|---|---|
| `OSD down` | Check `systemctl status ceph-osd@<id>`, disk health (`smartctl -a`), kernel log. |
| `PG degraded` / `undersized` | Fewer copies than `size`; wait for recovery or fix the down OSD. |
| `nearfull` | Add capacity / delete data / rebalance (`ceph osd reweight-by-utilization`). |
| `clock skew detected` | Fix NTP/chrony on monitors. |
| `slow ops` / `slow requests` | A failing disk or saturated network; find the OSD with `ceph health detail`. |

### RBD basics
```bash
rbd ls <pool>
rbd info <pool>/<image>
rbd snap create <pool>/<image>@<snap>
rbd du <pool>/<image>
```

## 🇹🇷 Türkçe

### Durum kontrolü
`ceph -s` genel durum; `ceph health detail` uyarıların açıklaması; `ceph osd tree` OSD'lerin up/down ve in/out durumu; `ceph osd df tree` OSD doluluk oranı (> %80 sapmalara dikkat); `ceph df` havuz kullanımı; `ceph pg stat` hepsi `active+clean` olmalı.

### Pratik kurallar
- Replikalı havuzlarda `size 3`, `min_size 2` kullanın. `size 2 / min_size 1` tek arızada veri kaybı riski taşır.
- OSD doluluğunu ~%70-75 altında tutun; Ceph %85'te `nearfull` uyarır, %95'te yazmayı durdurur. Kapasite artışını erken planlayın.
- **En az 3 monitor** (tek sayıda), farklı arıza alanlarında.
- Mümkünse **public** ve **cluster** ağlarını ayırın; OSD düğümlerinde ≥ 10 GbE. Yeniden dengeleme trafiği yüksektir.
- PG sayısını autoscaler yönetsin: `ceph osd pool set <havuz> pg_autoscale_mode on`.

### Planlı bakım (düğüm düğüm)
`ceph osd set noout` → düğümü yamala/yeniden başlat → OSD'ler dönüp PG'ler `active+clean` olana kadar bekle (`ceph -s`) → `ceph osd unset noout`. Sonraki düğüme geçmeden önce `HEALTH_OK` görün.

### Sık uyarılar
`OSD down` → servis, disk sağlığı (`smartctl`) ve kernel log; `PG degraded/undersized` → kurtarmayı bekleyin ya da düşen OSD'yi düzeltin; `nearfull` → kapasite ekleyin/veri silin; `clock skew` → monitörlerde NTP; `slow ops` → bozuk disk ya da doygun ağ.
