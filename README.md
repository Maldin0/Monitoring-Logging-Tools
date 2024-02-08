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

## Log Reader/Analysis
### Journalctl
&nbsp;&nbsp;&nbsp;&nbsp;**Journald** เป็น **daemon** ที่รวบรวมและเก็บบันทึกจากทั้งระบบ โดยข้อความเหล่านั้นเป็นข้อความบูต ข้อความเคอร์เนลและจาก **syslog** หรือ app ต่าง ๆ และเก็บข้อความเหล่านั้นไว้ในตำแหน่งศูนย์กลาง **file journald**

ด้านล่างนี้คือตัวอย่างลักษณะของไฟล์ ดูโดยใช้คำสั่ง `cat`

```bash
$ cat /etc/systemd/journald.conf

# See journald.conf(5) for details.
```

ผลลัพท์ที่ได้

```bash
[Journal]
Storage=auto
Compress=yes
Seal=yes
SplitMode=uid
SyncIntervalSec=5m
RateLimitInterval=30s
RateLimitBurst=1000
SystemMaxUse=
\SystemKeepFree=
\SystemMaxFileSize=
\SystemMaxFiles=100
\RuntimeMaxUse=
\RuntimKeepFree=
\RuntimeMaxFiles=100
\MaxRetentionSec=
\MaxFileSec=1month
\ForwardToSyslog=yes
\ForwardToKMsg=no
\ForwardToConsole=no
\ForwardToWall=yes
\TTYPath=/dev/console
\MaxLevelStore=debug
\MaxLevelSyslog=debug
\MaxLevelKMsg=notice
\MaxLevelConsole=info
\MaxLevelWall=emerg
```

#### การดูข้อความบันทึกโดยใช้คำสั่ง **Journalctl**
- หากต้องการให้แสดงข้อความทั้งหมดโดยไม่มีการกรอง ให้ใช้
```bash
$ journalctl
```
ตัวอย่าง

<img src="./img/log_read01.png">

#### ดูข้อความบันทึกตาม *Boots*
- เราสามารถแสดงรายการ *Boots ID* และการประทับเวลาของข้อความแรกและสุดท้ายโดยใช้
```bash
$ journalctl --list-boots
```
ตัวอย่าง

<img src="./img/log_read02.png">

- หากต้องการดูรายการรายวันให้ใช้ `-b`
```bash
$ journalactl -b
```
- และหากอยากดูบันทึกจากการ Boots ครั้งก่อนให้ใช้ตัวเลือก `-b` ตามด้วยตัวชี้สัมพัทธ์ `-1`  หรือใช้ Boots ID
```bash
$ journalctl -b -1

$ journalctl -b 9fb590b48e1242f58c2579defdbbddc9
```
#### การกรองข้อความบันทึกตามเวลา
- หากต้องการใช้เวลาในรูปแบบเวลาสากลเชิงพิกัด (UTC) ให้ใช้ตัวเลือก `--utc`
```bash
$ journalctl --utc
```
- หากต้องการดูรายการตั้งแต่วันที่และเวลาใดให้ใช้คำสั่งดังนี้ `--since`
```bash
$ journalctl --since “2023-09-30 09:30:00” --until “2023-09-30 12:30:00”

$ journalctl --since “1 hour ago”

$ journalctl --since “2 days ago”

$ journalctl --since today

$ journalctl --since yesterday

```

#### การดูข้อความล่าสุดโดยกำหนดจำนวนบรรทัดที่แสดง
- หากต้องการกำหนดจำนวนบรรทัดที่แสดงให้ใช้  `-n` แล้วตามด้วยจำนวนบรรทัดที่ต้องการ
โดยจำนวนบรรทัดโดยค่าเริ่มต้นคือ 10
```bash
$ journalctl -n 25
```
#### การดูข้อความบันทึกที่สร้างโดย **Kernel**
- ให้ใช้แฟล็ก `-k` ซึ่งจะได้ ouput คล้ายกับ `dmesg`
```bash
$ journalctl -k
```
#### การดูข้อความบันทึกที่สร้างโดย *Unit*
- สำหรับเวลาที่ต้องการดูบันทึกของแต่ล่ะหน่วยให้ใช้ `-u` ตามด้วยหน่วยที่ต้องการ
```bash
$ journalctl -u aoache2.service
```
#### การดูข้อความบันทึกที่สร้างด้วยกระบวนการเฉพาะ
- หากต้องการดูบันทึกที่สร้างด้วยวิธีการเฉพาะ ให้ระบุ `PID`
```bash
$ journalctl _PID=19487
```
#### การดูข้อความบันทึกที่สร้างโดยผู้ใช้หรือ *ID* กลุ่ม
- หากต้องการดูบันทึกที่สร้างโดยผู้ใช้หรือกลุ่มเฉพาะ ให้ระบุ `ID`
```bash
$ journalctl _UID=1000
```
#### การดูบันทึกที่สร้างโดย *File*
- หากต้องการแสดงบันทึกทั้งหมดที่สร้างโดย *file* ให้พิมพ์ `/usr/bin/` ตามด้วย *file* ที่ต้องการดู
```bash
$ journalctl /usr/bin/dbus-daemon
```
#### ดูข้อความบันทึกตามความสำคัญ
- โดยคุณสามารถกรองได้โดยใช้ `-p` ตามด้วยระดับที่คุณเลือกโดยแบ่งระดับดังนี้<br>
    - 0 – ฉุกเฉิน, 
    - 1 – แจ้งเตือน, 
    - 2 – วิกฤติ, 
    - 3 – ผิดพลาด, 
    - 4 – เตือน, 
    - 5 – ประกาศ, 
    - 6 – ข้อมูล,
    - 7 – แก้ไขจุดบกพร่อง
```bash
$ journalctl -p err
$ journalctl -p 1..4
$ journalcrtl -p emerg..warning
```
#### ดูบันทึกแบบ real time
- สามารถดูได้โดยใช้ตัวเลือก `-f` (คล้าย *funtion* ของ `tail -f`)
```bash
$ journalctl -f
```
#### จัดรูปแบบการแสดงผลของบันทึก
- ถ้าต้องการควบคุมจัดการรูปแบบ *output* ของรายการ **journald** ให้ใช้ `-o` ตามด้วยตัวเลือกดังนี้
    - cat, 
    - export, 
    - json, 
    - json-pretty, 
    - json-sse, 
    - short, 
    - short-iso, 
    - short-monotonic, 
    - short-precise 
    - verbose
```bash
$ journalctl -b -u apache2.service -0 cat
```
#### การจัดระบบบันทึก
- หากต้องการตรวจสอบบันทึก *file* journald ให้ใช้ `--verify` หากทุกอย่างเรียบร้อย `PASS`
```bash
$ journalctl --verify
```
<img src="./img/log_read03.png">

#### การลบ *File Journald* เก่า
- โดยคุณสามารถแสดงการใช้ *disk* ปัจจุบันของ **journald** ทั้งหมดได้โดยใช้ `--disk-usage`
```bash
$ journalctl --disk-usage
```
- หากต้องการลบ *file* เก่าที่ถูกเก็บถาวรให้ใช้คำสั่งตามด้านล่าง
```bash
$ sudo journalctl --vacuum-size=50M  
#delete files until the disk space they use falls below the specified size
$ sudo journalctl --vacuum-time=1years	
#delete files so that all journal files contain no data older than the specified timespan
$ sudo journalctl --vacuum-files=4     
#delete files so that no more than the specified number of separate journal files remain in storage location
```

### dmesg
&nbsp;&nbsp;&nbsp;&nbsp;คือคำสั่งสำหรับใช้แสดงข้อความจาก **Kernel rail link buffer** ระบบผ่านระดับการทำงานหลายระดับ โดยข้อความที่แสดงขะมีข้อมูลเกี่ยวกับอุปกรณ์ ทั้งในระดับ *hardware* และระบบ

&nbsp;&nbsp;&nbsp;&nbsp;โดยคำสั่งส่วนมากจะถูกใช้ดังนี้
```bash
$ dmseg [option…]
```
#### แสดงรายการ driver ที่โหลดดิ้งหมดใน Kernel
- โดยเราสามารถใช้เครื่องมือจัการข้อความ เช่น `more`, `tail`, `less` หรือ `grep` กับคำสั่ง `dmesg` ได้เนื่องจาก *output* ของ `dmesg` ไม่พอดีในหน้าเดียว การใช้คำสั่ง dmesg ตามด้วยสัญลักษณ์ **pipr** ( `|` ) `more` หรือ `less` จะทำงห้ข้อความถูกแสดงออกมาในหน้าเดียว

```bash
$ dmesg | more

$ dmesg | less
```
ตัวอย่าง: 

<img src="./img/log_read04.png">

#### แสดงรายการอุปกรณ์ทั้งหมดที่ตรวจพบ
- ถ้ากต้องการดูว่า **Kernel** ตรวจพบ *hardware* ใดบ้าง ให้ใช้ `| grep sda`
```bash
$ dmesg | grep sda
```
ตัวอย่าง: 

<img src="./img/log_read05.png">

#### พิมพ์โดยกำหนดจำนวนบรรทัดและส่วนที่ต้องการของ output
- โดยใช้ `| head -x` โดย `x` คือจำนวนบรรทัดที่ต้องการ ก็จะได้ข้อมูลจากบรรทัดแรกจนถึงบรรทัดที่ `x`
```bash
$ dmesg | head -20
```
ตัวอย่าง:

<img src="./img/log_read06.png">

- หากต้องการเป็นข้อมูลจากส่วนท้ายก็สามารถใช้รูปแบบคำสั่งตามเดิมได้เลยเพียงเปลี่ยนจาก `head` เป็น `tail`
```bash
$ dmesg | tail -20
```
ตัวอย่าง: 

<img src="./img/log_read07.png">

#### ค้นหาอุปกรณ์ที่ตรวจพบหรือ *String* เฉพาะ
- เป็นเรื่องยากที่จะค้นหา *String* เฉพาะเนื่องจากความยาวของ *output* ของ `dmesg` ดังนั้นจึงต้องกรองบรรทัดที่มี *String* โดยใช้ `| grep -i txt` โดย `txt` คือ *String* เฉพาะที่ต้องการหา
```bash
$ dmesg | grep -i memory
```
ตัวอย่าง:

<img src="./img/log_read08.png">

#### ล้างบันทึก *Buffer dmesg*
- เราสามารถล้างบันทึกใน *buffer* ได้โดยใช้ `-c`
```bash
$ dmesg -c
```
#### ตรวจสอบ `dmesg` แบบ *real time*
- **distro** บางตัวอนุญาตให้ใช้คำสั่ง *watch* **“someting”** เพื่อตรวจสอบตามเวลาจริงได้
```bash
$ watch “dmesg | tail -20”
```
### Last
&nbsp;&nbsp;&nbsp;&nbsp;เป็นคำสั่งสำหรับแสดงรายชื่อผู้ *login* เข้ามาล่าสุด
```bash
$ last | more
```
### Lastcomm
&nbsp;&nbsp;&nbsp;&nbsp;เป็นคำสั่งที่แสดงคำสั่งสุดท้ายที่ถูกดำเนินการของผู้ใช้แต่ละคน
```bash
$ lastcomm root
```
ตัวอย่าง: 

<img src="./img/log_read09.png">

## Log Collection/Server
### Syslogd
&nbsp;&nbsp;&nbsp;&nbsp;เป็นระบบที่จัดเตรียม *utilities* ของระบบสองระบบที่ support การทำงาน เพื่อดักจับข้อความจาก s**ystem logging** และ **kernel**

&nbsp;&nbsp;&nbsp;&nbsp;**Syslogd** มีการบันทึกประเภทหนึ่ง ทุกข้อความจะมีบันทึกไว้อย่างน้อยหนึ่งครั้งรวมถึง *hostname field* ด้วย โดยปกติจะมี *program name* แต่นั่นขึ้นอยู่กับความน่าเชื่อถือของ **logging program** ด้วย

### Option
| flag               | description                                                                                                                                                                                  |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-a`               | ไม่แสดงการค้นหาชื่อโฮสต์แบบย้อนกลับสำหรับข้อความที่มาจากโฮสต์ระยะไกล และบันทึกที่อยู่ *IP* ของโฮสต์ที่ค้นหาในระยะไกลลงไฟล์บันทึก                                                                                       |
| `-d`               | เปิดใช้งาน **debugging**                                                                                                                                                                       |
| `-e`               | ระบุ *enhanced rotation* ไฟล์ทั้งหมดทั้งที่ **compressed** และ **uncompressed** ที่มีอยู่ใน *log directory* และถูกสร้างโดย **sydlogd daemon** โดยพิจารณาสำหรับการ *rotation*                                   |
| `-f config file`   | ระบุ *file config* สำรอง                                                                                                                                                                       |
| `-m markinterval`  | ระบุจำนวนนาทีที่อยู่ระหว่าง *mark command message* ถ้าไม่สามารถทำได้ *mark command* จะส่งข้อความตามลำดับความสำคัญของ **LOG_INFO** โดยจะส่งทุก ๆ 20 นาที                                                           |
| `-M all`           | ระบุว่าจะไม่ระงับข้อความที่ซ้ำกันใน *log file* ใช้ได้เฉพาะในกรณีที่ใช้กับ *argument* ทั้งหมด                                                                                                                    |
| `-s`               | ระบุข้อความเพื่อส่งข้อความแบบ "***shortened***" ไปยังระบบอื่น (ถ้ากำหนดไว้ก็ทำได้นะ) สำหรับข้อความของทั้งหมด **syslog** ไปสร้างบน *local system*                                                                  |
| `-r`               | ระงับข้อความที่มาจาก *remote hosts*                                                                                                                                                              |
| `-R`               | ปิดการรับข้อความที่ได้รับจาก *network* ที่ใช้ *internet domain socket*                                                                                                                                 |
| `-n`               | ระงับข้อความที่ขึ้นต้นด้วย "***Message forwarded from <log_host_name>:*** "ก่อนที่จะส่งไปให้ *remote log host*                                                                                            |
| `-N`               | ระงับการบันทึกข้อมูลลำดับความสำคัญและ *facility information* ของแต่ละ *log message*                                                                                                                    |
| `-p`               | ระบุเส้นทางอื่นของ *data socket*                                                                                                                                                                 |
| `-A additionalLog` | ระบุบันทึกเพิ่มเติมที่ **syslogd daemon** ตรวจสอบ ตาม **default syslogd daemon** โดยจะตรวจสอบไฟล์ `/dev/log` เพื่อหาข้อความ หากระบุได้ระบบจะตรวจสอบไฟล์เพิ่มเติมสำหรับข้อความด้วย บันทึกเพิ่มเติมอาจอยู่ในเส้นทาง *chroot* |

### Configuration file
&nbsp;&nbsp;&nbsp;&nbsp;ไฟล์กำหนดค่าทำหน้าที่แจ้ง **syslogd daemon** ว่าจะส่งข้อความระบบไปที่ไหน ขึ้นอยู่กับระดับความสำคัญของข้อความและหน่วยงานที่สร้างข้อความนั้น

&nbsp;&nbsp;&nbsp;&nbsp;หากคุณไม่ใช้ `-f` flag ของ **syslogd daemon** จะอ่านไฟล์ที่ถูกกำหนดไว้เป็นค่าเริ่มต้น คือไฟล์ `/etc/syslog.con`

&nbsp;&nbsp;&nbsp;&nbsp;**daemon syslogd** จะไม่สนใจบรรทัดที่เว้นว่างและบรรทัดที่ขึ้นต้นด้วยเครื่องหมาย (`#`)

### Format

&nbsp;&nbsp;&nbsp;&nbsp;บรรทัดในไฟล์การกำหนดค่าสำหรับ daemon syslogd ประกอบด้วย *selector field*, *action field*, และ *optional rotation field* ซึ่งคั่นด้วย `tab` หรือ `space` หลาย ๆ ครั้ง
- ***Selector field*** ชื่อ *facility* และระดับความสำคัญ (priority level) โดยใช้ `,` (comma) คั่นระหว่างชื่อ *facility* และ `.` (period) คั่นระหว่าง *facility* และระดับความสำคัญใน ***selector field*** ด้วย `;` (semicolon) หากต้องการเลือกทุก *facility* ให้ใช้ `*` (asterisk)
- ***Action field*** ที่ระบุปลายทาง (ไฟล์, โฮสต์, หรือผู้ใช้) ที่จะรับข้อความ หากมีการส่งไปยัง *remote host* ส่วน *remote system* จะจัดการข้อความตามที่ระบุในไฟล์กำหนดค่าของตนเอง หากต้องการแสดงข้อความบนหน้าจอของผู้ใช้ ให้ใช้ชื่อผู้ใช้ที่ถูกต้องที่เป็นผู้ใช้ที่ล็อกอิน
- ***Rotation field*** ระบุว่าการหมุนเวียนใช้อย่างไร หากฟิลด์การทำงานเป็นไฟล์ *rotation* สามารถทำตามขนาดหรือเวลาหรือทั้งคู่ได้ ยังสามารถบีบอัดและ/หรือ เก็บไฟล์ที่หมุนเวียนได้

### Facilities
| Facilities             | Description               |
| ---------------------- | ------------------------- |
| kern                   | Kernel                    |
| user                   | User level                |
| mail                   | Mail subsystem            |
| daemon                 | System daemons            |
| auth                   | Security of authorization |
| syslog                 | Syslogd daemon            |
| lpr                    | Line-printer subsystem    |
| news                   | New subsystem             |
| uucp                   | Uucp subsystem            |
| Locall0 through local? | Local use                 |
| *                      | All facilities            |

### Priority Levels
&nbsp;&nbsp;&nbsp;&nbsp;ใช้ระดับความสำคัญของข้อความต่อไปนี้ใน *selector field* ข้อความที่มีระดับความสำคัญที่ระบุและทุกระดับที่สูงกว่าจะถูกส่งตามที่กำหนด
| Priority | Description                                                                                          |
| -------- | ---------------------------------------------------------------------------------------------------- |
| emerg    | ข้อความที่ต้องการทราบทันทีและต้องการการกระทำทันที                                                              |
| alert    | ข้อความที่ต้องการการกระทำทันที                                                                              |
| crit     | ข้อความที่มีปัญหาว่าอาจทำให้ระบบไม่สามารถทำงานได้                                                               |
| err      | ข้อความที่ระบุข้อผิดพลาด                                                                                   |
| warning  | ข้อความที่เตือนว่ามีปัญหาหรือความเสี่ยง                                                                        |
| notice   | ข้อความที่บ่งบอกถึงสถานะหรือเหตุการณ์ที่เกิดขึ้น                                                                  |
| info     | ข้อความที่ให้ข้อมูลเกี่ยวกับการดำเนินการทั่วไป                                                                    |
| debug    | ข้อความที่ให้ข้อมูลเกี่ยวกับการดำเนินการเพิ่มเติมสำหรับการตรวจสอบข้อผิดพลาด                                            |
| none     | นอกเหนือจากสิ่งที่เลือกเอาไว้ ระดับความสำคัญนี้มีประโยชน์เฉพาะถ้ามีการกรอกที่มี * (asterisk) ในselector fieldเดียวกันก่อนนี้ |

### Destinations
&nbsp;&nbsp;&nbsp;&nbsp;ใช้เป้าหมายของข้อความต่อไปนี้ใน *action field*
- `File Name`
    - ชื่อเต็มของ *path* ที่ไฟล์ถูกเปิดใน *append mode*
- `@ Host`
    - *Host name*, นำหน้าด้วยสัญลักษณ์(`@`)
- `User[, User][...]`
    - *User names*
- `*`
    - All users
- `centralizedlog LogSpaceName/LogStreamName`
    - *PowerHA® pureScale® logstream*
### Rotation	
&nbsp;&nbsp;&nbsp;&nbsp;ใช้คำหลักการหมุนเวียนต่อไปนี้ใน *rotation field*
- `rotate`
    - เป็น *keyword* ที่ต้องอยู่ด้านหลังของ *action field*
- `size`
    - ระบุว่า *rotation* ขึ้นอยู่กับขนาด ตามด้วยตัวเลขและ k (kilobytes) หรือ m (megabytes)
- `time`
    - ระบุว่า rotation ขึ้นอยู่กับเวลา ตามด้วยตัวเลขและ `h` (hour), `w` (week), `m` (month), `y` (year)
- `files`
    - ระบุจำนวนรวมของ *rotated files* ตามด้วยตัวเลข หากไม่ระบุ จะมีจำนวน *rotated files* ได้ไม่จำกัด
- `compress` 
    - บันทึก *rotated file* แบบ **compress**
- `archive`
    - บันทึก *rotated file* แบบ **copy** ลงใน *directory* ตามที่กำหนดเอาไว้


## Log Files
ไฟล์ Log จะเก็บการทำงานต่าง ๆ ของ OS แอปพลิเคชัน และระบบ เมื่อเกิดปัญหากับระบบ หรือ applitcation การวิเคราะห์ logfiles ถือเป็นสิ่งแรกที่ผู้ดูแลระบบต้องทำ

### หลักการทำงานของไฟล์ Log
#### Troubleshooting
เมื่อมีสิ่งผิดปกติเกิดขึ้นกับระบบปฏิบัติการ Linux logfiles จะช่วยระบุปัญหาได้ ด้วยการตรวจสอบ system logs, application logs และ service logs 
การ Troubleshooting จะช่วยให้ระบุข้อผิดพลาด, คำเตือน, และข้อความอื่นๆ ที่บ่งชี่ถึงความผิดพลาดได้

#### Diagnosing Performance Issues
System loga สามารถช่วยระบุปัญหาด้านประสิทธิภาพ เช่น การรั่วไหลของหน่วยความจำหรือ disk i/o มีอาการคอขวดและการตรวจสอบ application logs สามารถช่วยระบุปัญหาด้านประสิทธิภาพการทำงานของแอปพลิเคชันเฉพาะได้อีกด้วย

#### Monitoring System Health
Linux logs สามารถใช้เพื่อตรวจสอบความสมบูรณ์ของระบบและตรวจหาปัญหาก่อนที่จะกลายเป็นปัญหา ด้วยการตรวจสอบ system logs ผู้ดูแลระบบสามารถระบุแนวโน้มว่าปัญหาจะเกิดจากอะไร

#### Compliance and Auditing
องค์กรหลายแห่งจำเป็นต้องดูแลรักษา logfiles เพื่อวัตถุประสงค์ในการปฏิบัติตามข้อกำหนดและการตรวจสอบ Linux logs ช่วยให้องค์กรปฏิบัติตามข้อกำหนดเหล่านี้ได้โดยจัดทำบันทึกกิจกรรมของระบบ

#### Security
Linux logs เป็นเครื่องมือสำคัญสำหรับการตรวจสอบและตรวจจับปัญหาด้านความปลอดภัย system logs สามารถใช้เพื่อตรวจจับความพยายามในการเข้าถึงโดยไม่ได้รับอนุญาต ในขณะที่ application logs สามารถช่วยระบุกิจกรรมที่น่าสงสัยภายในแอปพลิเคชันที่ระบุได้ ด้วยการตรวจสอบบันทึก ผู้ดูแลระบบสามารถระบุและตอบสนองต่อเหตุการณ์ด้านความปลอดภัยได้อย่างรวดเร็ว

### การเรียกใช้งานและผลลัพธ์ที่ได้:

```bash
$ tail -f /var/log/kern.log
```

คำสั่งนี้จะทำการเรียกดู logs ที่เกี่ยวกับ Kernel และ Warning Data ต่างๆ logs นี้มีความสำคัญสำหรับการ Troubleshooting kernel แบบกำหนดเอง
*/kern.log คือ Directory ที่เก็บการทำงานทั้งหมดของ Kernel

![img](https://1.bp.blogspot.com/-CnJu2D7q62g/ToiXxZfJN7I/AAAAAAAAAEA/U8GcsBIXnjI/s1600/Screenshot.png)

```bash
$ dmesg -H
```

คำสั่งนี้จะเรียกดู log ที่เก็บข้อความในไฟล์ของ Device driver
dmesg -H คือเรียกดูข้อความในไฟล์ของ Device driver ที่มนุษย์สามารถอ่านได้

![img](https://phoenixnap.com/kb/wp-content/uploads/2022/01/enabling-human-readable-output-in-dmesg.png)

```
/var/log/boot.log
```

ที่เก็บข้อมูลทั้งหมดที่เกี่ยวข้องกับการบูทและข้อความใด ๆ ที่บันทึกไว้ระหว่างที่เริ่มเปิดระบบปฏิบัติการ
boot.log จะเก็บ log เวลาบูทระบบปฏิบัติการขึ้นมา ทำให้สามารถดูได้ว่ามีอะไรทำงานผิดพลาดบ้าง

### ข้อควรระวัง
ต้องใช้ความระมัดระวังในการลบ logfiles เนื่องจากไฟล์เหล่านี้อาจมีข้อมูลสำคัญที่อาจจำเป็นในภายหลังสำหรับการแก้ไขปัญหาหรือการตรวจสอบ ก่อนที่จะลบควรจะตรวจสอบให้แน่ใจว่าไฟล์ที่ถูกลบนั้นเป็นไฟล์ที่ถูกต้อง เนื่องจากการลบไฟล์ผิดอาจส่งผลที่ตามมาโดยไม่ตั้งใจ

[แหล่งอ้างอิง](https://stackify.com/linux-logs/?fbclid=IwAR3voKfiAeL16lCU8nSwy_UbJJ6D8a6o0voL227B-srGwj6-O5UaF4pPPEs)


## Working with Texts
ไฟล์ส่วนใหญ่ในระบบมักจะถูกเก็บในรูปแบบของข้อความ ซึ่งในไฟล์ก็จะประกอบไปด้วยข้อความจำนวนมาก การจะดึงเอาข้อมูลที่ต้องการออกมาจึงต้องมีการใช้คำสั่งเพื่อให้ผู้ใช้งานจัดการกับข้อความได้เพื่อเพิ่มประสิทธิภาพในการแสดงออกของข้อมูล

### `cat`
เป็นคำสั่งที่ใช้ในการแสดงข้อมูลในไฟล์นั้น ๆ โดยใช้คำสั่ง `cat` ตามด้วยชื่อไฟล์ที่ต้องการแสดง

```bash
$ cat food.txt
```

นอกจากนั้น `cat` ยังใช้สร้างไฟล์ใหม่ได้ด้วย โดยใช้คำสั่ง `cat` ตามด้วยชื่อไฟล์ที่ต้องการสร้าง และใช้ `>` ตามด้วยชื่อไฟล์ที่ต้องการสร้าง

```bash
$ cat > food.txt
```

จากนั้นให้พิมพ์ข้อความที่ต้องการเพิ่มลงไปในไฟล์ และกด `Ctrl + C` เพื่อบันทึกไฟล์

ในกรณีที่ใช้ `>` แล้วมีไฟล์ที่มีข้อมูลอยู่แล้ว การใช้คำสั่ง `cat` จะทำให้ข้อมูลในไฟล์เดิมถูกเขียนทับด้วยข้อมูลใหม่

แต่หากต้องการจะเพิ่มข้อมูลเข้าไปในไฟล์ที่มีข้อมูลอยู่แล้ว ให้ใช้คำสั่ง `cat` ตามด้วยชื่อไฟล์ที่ต้องการเพิ่มข้อมูล และใช้ `>>` ตามด้วยชื่อไฟล์ที่ต้องการเพิ่มข้อมูล

```bash
$ cat >> food.txt
```

### `echo`/`printf`
2 คำสั่งนี้ทำหน้าที่คล้ายกับ `cat` แต่มีความหลากหลายในการใช้งานมากกว่า

`echo` สร้างพร้อมเพิ่มข้อความพร้อมกันได้โดยพิมพ์
    
```bash
$ echo "Hello" > greet.txt
```

หรือสามารถสร้างหลาย ๆ ไฟล์พร้อมกันได้โดยพิมพ์

```bash
$ echo >> file1.txt >> file2.txt
```

`printf` สามารถใช้สร้างข้อความที่มีรูปแบบได้แบบภาษา C อย่างเช่น `%s` `%d` และอื่น ๆ
```bash
$ printf "Hello\n" > greet.txt
```

### Vi / Vim Editor
`vi` และ `vim` เป็นโปรแกรมที่ใช้ในการแก้ไขข้อความที่ติดตั้งพร้อมมากับ Linux Server

`vim` คือ `vi` ที่ถูกพัฒนาขึ้นมาใหม่ โดยมีความสามารถที่มากกว่า `vi` และมีการใช้งานที่ง่ายขึ้น ใช้งานโดยใช้คำสั่ง `vim` ตามด้วยชื่อไฟล์ที่ต้องการแก้ไข

```bash
$ vim food.txt
```

โดย `vi` และ `vim` จะมีโหมดการทำงาน 2 โหมด คือ

1. **Command Mode** โหมดนี้ใช้ในรับคำสั่งหรือคีย์ลัด เมื่อกด `I` จะเป็นการเข้า Insert mode หากต้องการกลับมาให้กด `ESC`

2. **Insert Mode** โหมดนี้ใช้ในการแก้ไขข้อมูลในไฟล์เราสามารถเปิดไฟล์อื่นมาแก้หรือสร้างขึ้นมาโดยการพิมพฺ `:e` ตามด้วยชื่อไฟล์ที่ต้องการเปิด

```bash
:e file.txt
```

#### คำสั่งที่ใช้บ่อย

|คำสั่ง|คำอธิบาย|
|-|-|
|`:w`|บันทึกไฟล์|
|`:q`|ออกจากโปรแกรม|
|`:wq`|บันทึกและออกจากโปรแกรม|
|`:q!`|ออกจากโปรแกรมโดยไม่บันทึก|
|`:e`|เปิดไฟล์ใหม่|
|`:e!`|โหลดไฟล์ใหม่|

#### คำสั่งใข้ Navigate
|คำสั่ง|คำอธิบาย|
|-|-|
|`h`|ขยับไปทางซ้าย|
|`j`|ขยับลง|
|`k`|ขยับขึ้น|
|`l`|ขยับไปทางขวา|
|`0`|ไปที่ต้นบรรทัด|
|`$`|ไปที่ท้ายบรรทัด|
|`123` หรือ `123G`|ขยับไปที่บรรทัดที่ 123|
|`gg`|ขยับไปที่บรรทัดแรก|
|`G`|ขยับไปที่บรรทัดสุดท้าย|
|`Ctrl + f`|เลื่อนหน้าลง|
|`Ctrl + b`|เลื่อนหน้าขึ้น|
|`Ctrl + u`|เลื่อนขึ้นครึ่งหน้า|
|`Ctrl + d`|เลื่อนลงครึ่งหน้า|
|`w`|ขยับไปหน้าคำถัดไป|
|`b`|ขยับไปหน้าคำก่อนหน้า|
|`e`|ขยับไปท้ายคำถัดไป|
|`ge`|ขยับไปท้ายคำก่อนหน้า|
|`*`|ขยับไปท้ายคำถัดไปที่เหมือนกับคำที่เรากำลังเลือก|
|`#`|ขยับไปท้ายคำก่อนหน้าที่เหมือนกับคำที่เรากำลังเลือก|
|`(`|ขยับไปหน้าประโยคถัดไป|
|`)`|ขยับไปหน้าประโยคก่อนหน้า|
|`}`|ขยับไปหน้าย่อหน้าถัดไป|
|`{`|ขยับไปหน้าย่อหน้าก่อนหน้า|
|`Ctrl + o`|ขยับไปหน้าหน้าที่เราเคยอยู่|
|`Ctrl + i`|ขยับไปหน้าหน้าที่เราเคยอยู่ก่อนหน้า|
|`Ctrl + g`|แสดงข้อมูลของไฟล์|
|`Ctrl + r`|ทำการ Undo|
#### คำสั่งใข้ค้นหา
|คำสั่ง|คำอธิบาย|
|-|-|
|`/`|ค้นหาข้อความ เช่น `/txt` จะค้นหาข้อความ `txt` ในไฟล์|
|`n`|ขยับไปหน้าข้อความถัดไปที่เราค้นหา|
|`N`|ขยับไปหน้าข้อความก่อนหน้าที่เราค้นหา|
|`:noh`|ปิดการ Highlight ข้อความที่เราค้นหา จนกว่าเราจะค้นหาข้อความใหม่|

#### คำสั่งใช้แก้ไขข้อความ
|คำสั่ง|คำอธิบาย|
|-|-|
|`y`|คัดลอกข้อความ|
|`p`|วางข้อความที่คัดลอกไว้|
|`x`|ลบข้อความ|
|`dd`|ลบบรรทัด|
|`d$`|ลบข้อความจากตำแหน่งปัจจุบันไปจนถึงท้ายบรรทัด|
|`d0`|ลบข้อความจากตำแหน่งปัจจุบันไปจนถึงต้นบรรทัด|
|`d^`|ลบข้อความจากตำแหน่งปัจจุบันไปจนถึงต้นบรรทัด|
|`dgg`|ลบข้อความจากตำแหน่งปัจจุบันไปจนถึงบรรทัดแรก|
|`dG`|ลบข้อความจากตำแหน่งปัจจุบันไปจนถึงบรรทัดสุดท้าย|
|`d}`|ลบข้อความจากตำแหน่งปัจจุบันไปจนถึงย่อหน้าถัดไป|
|`d{`|ลบข้อความจากตำแหน่งปัจจุบันไปจนถึงย่อหน้าก่อนหน้า|
|`d*`|ลบข้อความจากตำแหน่งปัจจุบันไปจนถึงคำถัดไปที่เหมือนกับคำที่เรากำลังเลือก|
|`d#`|ลบข้อความจากตำแหน่งปัจจุบันไปจนถึงคำก่อนหน้าที่เหมือนกับคำที่เรากำลังเลือก|
|`d(`|ลบข้อความจากตำแหน่งปัจจุบันไปจนถึงประโยคถัดไป|
|`d)`|ลบข้อความจากตำแหน่นไปจนถึงประโยคก่อนหน้า|
|`daw`|ลบคำที่เรากำลังเลือกพร้อมกับช่องว่างด้วย|
|`diw`|ลบคำที่เรากำลังเลือก|

#### เลิกทำ/ทำซ้ำคำสั่ง
|คำสั่ง|คำอธิบาย|
|-|-|
|`.`|ทำซ้ำคำสั่งที่เราเคยทำ|
|`u`|ทำการ Undo|
|`U`|ทำการ Undo ในบรรทัดนั้น ๆ|
|`Ctrl + r`|ทำการ Redo|

### Pager (less/more)
`less` และ `more` เป็นคำสั่งที่ใช้ในการอ่านและค้นหาข้อมูลในไฟล์ขนาดนิยมใช้กับไฟล์ขนาดใหญ่เพื่อที่จะช่วยให้หาข้อมูลได้ง่ายขึ้น ซึ่งจะทำให้เราสามารถเลื่อนขึ้นลงไปด้วย Arrow keys

```bash
$ less food.txt
```

หรือ

```bash
$ more food.txt
```

- ไม่สน case sensitive (พิมพ์เล็กพิมพ์ใหญ่) โดยการพิมพ์ less -I filename.txt
- ค้นหาคำโดย / เช่น /text โดยเมื่อหาเจอจะ highlight ข้อความไว้ถ้าไม่เจอจะขึ้นว่า Pattern not found
- กระไปบรรทัดที่ต้องการโดยการพิมพ์ g เช่น g55 คือ กระโดดไปบรรทัดที่ 55
- h เพื่อแสดง command บางส่วนที่จำเป็น q เพื่อออกจาก less

more จะคล้ายกับ less เพียงแต่ more จะเลื่อนหน้าลงได้อย่างเดียวเลื่อนขึ้นไม่ได้

### Head/Tail
`head` และ `tail` เป็นคำสั่งที่ใช้ในการแสดงข้อมูลบางส่วนของไฟล์ โดย `head` จะแสดงข้อมูลบรรทัดแรก ๆ ของไฟล์ และ `tail` จะแสดงข้อมูลบรรทัดสุดท้าย ๆ ของไฟล์

```bash
$ head food.txt
```

หรือ

```bash
$ tail food.txt
```

เราสามารถใช้คำสั่ง `head` และ `tail` พร้อมกับ `-n` เพื่อแสดงข้อมูลบรรทัดที่เราต้องการ เช่น 5 บรรทัด

```bash
$ head -n 5 food.txt
```

หรือ

```bash
$ tail -n 5 food.txt
```

### Grep/Pipe

`grep` เป็นคำสั่งที่ใช้ในการค้นหาข้อความในไฟล์ โดยเราสามารถใช้คำสั่ง `grep` พร้อมกับข้อความที่ต้องการค้นหา และชื่อไฟล์ที่ต้องการค้นหา

```bash
$ grep "text" food.txt
```

Pipe หรือ | ใช้ในการเชื่อม command เข้าด้วยกันทำให้การใช้งาน command มีประสิทธิภาพ ยิ่งขึ้นและมีความหลากหลายมากขึ้น

```bash
$ cat food.txt | grep "text"
```

หรือแสดงไฟล์ที่ใน directory เฉพาะที่มี .txt
```bash
$ ls -l | grep '.txt'
```

#### ตัวเลือกที่ใช้บ่อย
|ตัวเลือก|คำอธิบาย|
|-|-|
|`-i`|ไม่สน case sensitive (พิมพ์เล็กพิมพ์ใหญ่)|
|`-c`|แสดงจำนวนข้อความที่ตรงกับเงื่อนไข|
|`-l`|แสดงชื่อไฟล์ที่เนื้อหาข้างในตรงกับเงื่อนไข|
|`-w`|แสดงประโยคและ highlight ข้อความที่มันตรงกันกับคำในเงื่อนไขเท่านั้น|
|`-o`|แสดงเฉพาะคำที่ข้อความตรงกันกับเงื่อนไข|
|`-n`|แสดงบรรทัดของข้อความและประโยคที่ในบรรทัดที่ตรงกับเงื่อนไข|
|`-v`|แสดงบรรทัดที่ข้อความไม่ตรงกับเงื่อนไข|
|`^`|แสดงข้อความที่คำในเงื่อนไขอยู่ตัวแรกสุดของบรรทัด|
|`$`|ใช้แสดงข้อความที่จบบรรทัดด้วยคำในเงื่อนไข|

### Standard IO
คือการที่ระบบปฏิบัติการ linux สามารถช่วยให้เราเปลี่ยนช่องในการ input หรือ output โดยแบ่งได้เป็น 3 ส่วนคือ
- Standard Input (stdin) คือ input มาตรฐานในการรับคำสั่งโดยมักจะมาจาก keyboard แต่อาจมาจากไฟล์ก็ได้ สามารถใช้ < ในการเปลี่ยนช่อง input ได้ เช่น tr a-z A-Z > ex.txt เป็นการให้เรา input ด้วย keyboard ซึ่งข้อมูลจะอยู่ใน ex.txt
- Standard Output (stdout) คือ output มาตฐานในการแสดงผลของ command โดยมักจะแสดงผ่านทางหน้าจอ สามารถใช้ > ในการเปลี่ยนช่อง output ได้ เช่น ls > file.txt
- Standard Error (stderr) คือ การแสดงข้อมูลที่เกิดข้อผิดพลาดออกทางหน้าจอ ข้อผิดพลาดใดที่เกิดขึ้นจากโปรแกรม โดยมักจะแสดงออกทางหน้าจอ