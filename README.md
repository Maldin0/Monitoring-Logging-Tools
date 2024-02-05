# Monitoring-Logging-Tools

some Description here

## Topic
- Log Reader/Analysis
    - [ไปที่ Log Reader/Analysis](#log-readeranalysis)
- Log Collection/Server
    - [ไปที่ Log Collection/Server](#log-collectionserver)
- Log Files
    - [ไปที่ Log Files](#log-files)
- Working with Texts
    - [ไปที่ Working with Texts](#working-with-texts)

---

- [ ] daad
- [x] dad

[^1]: This is the footnote.

> :warning: **Warning**

**bold**
*italic*
***bold italic***


~~kill yourself~~

```bash
rm -rf /
```

d

d
d

d
d

d
d

d
d

d
d

d
d

d
d

d
d

d
d

d
d

d
d

d
d

d
d

d
d

d
d

d
d

d
d

d
d

d

## Log Reader/Analysis
### Journalctl
**Journald** เป็น **daemon** ที่รวบรวมและเก็บบันทึกจากทั้งระบบ โดยข้อความเหล่านั้นเป็นข้อความบูต ข้อความเคอร์เนลและจาก **syslog** หรือ app ต่าง ๆ และเก็บข้อความเหล่านั้นไว้ในตำแหน่งศูนย์กลาง **file journald**

ด้านล่างนี้คือตัวอย่างลักษณะของไฟล์ ดูโดยใช้คำสั่ง cat

```
$ cat /etc/systemd/journald.conf

# See journald.conf(5) for details.
```

ผลลัพท์ที่ได้

```
[Journal]
#Storage=auto
#Compress=yes
#Seal=yes
#SplitMode=uid
#SyncIntervalSec=5m
#RateLimitInterval=30s
#RateLimitBurst=1000
#SystemMaxUse=
#SystemKeepFree=
#SystemMaxFileSize=
#SystemMaxFiles=100
#RuntimeMaxUse=
#RuntimKeepFree=
#RuntimeMaxFiles=100
#MaxRetentionSec=
#MaxFileSec=1month
#ForwardToSyslog=yes
#ForwardToKMsg=no
#ForwardToConsole=no
#ForwardToWall=yes
#TTYPath=/dev/console
#MaxLevelStore=debug
#MaxLevelSyslog=debug
#MaxLevelKMsg=notice
#MaxLevelConsole=info
#MaxLevelWall=emerg
```
## Log Collection/Server
some description here

## Log Files
some description here

## Working with Texts
soms description here


