### at Command (One-Time Scheduled Jobs)
##### Overview
- Used to schedule **one-time** jobs.
- Jobs are stored in:
    ```text
    /var/spool/at
    ```
- For recurring jobs, use **cron** instead.
---
##### Common Commands

|Command|Description|
|---|---|
|`at TIME`|Schedule a new job|
|`atq`|List scheduled jobs|
|`at -l`|Same as `atq`|
|`atrm JOB_ID`|Remove a scheduled job|

---
##### Basic Syntax
```bash
at TIME
```
After running the command, enter the commands you want to execute.
Finish by pressing:
```text
Ctrl + D
```
---
##### Examples
###### Schedule a reboot at 11:00 PM
```bash
at 11:00 PM

reboot
# Press Ctrl + D
```

###### Create a file after 5 minutes
```bash
at now + 5 minutes

touch /tmp/test.txt
# Press Ctrl + D
```

###### Run a script at 3:00 PM
```bash
at 3:00 PM

/home/user/backup.sh
# Press Ctrl + D
```

###### Run a command after 2 hours
```bash
at now + 2 hours
```
---
##### View Scheduled Jobs
```bash
atq
```
or
```bash
at -l
```
Example output:
```text
2  Tue Jul 21 15:00:00 2026 a root
```
- `2` → Job ID
- Remaining fields → Execution time
---
##### Remove a Job
```bash
atrm 2
```
Removes job **ID 2**.

---
##### Common Time Formats
```bash
at 17:00
at 5pm
at noon
at midnight
at tomorrow
at now + 10 minutes
at now + 1 hour
at now + 2 days
```
---
##### Quick Revision

| Task                     | Command         |
| ------------------------ | --------------- |
| Schedule a job           | `at TIME`       |
| List jobs                | `atq` / `at -l` |
| Remove a job             | `atrm JOB_ID`   |
| Finish entering commands | `Ctrl + D`      |
| Job type                 | One-time        |
| Job location             | `/var/spool/at` |

> **Remember**
> - **at** → One-time scheduled jobs.
> - **cron** → Recurring scheduled jobs.
---
##### Access Control (`at.allow` & `at.deny`)
These files control **who is allowed to use the `at` command**.

| File            | Description                       |
| --------------- | --------------------------------- |
| `/etc/at.allow` | Users listed **can use** `at`.    |
| `/etc/at.deny`  | Users listed **cannot use** `at`. |

---
##### Priority
1. If **`/etc/at.allow` exists** → **Only** users listed in this file can use `at`.
2. If **`at.allow` does not exist** but **`/etc/at.deny` exists** → All users **except** those listed can use `at`.
3. If **neither file exists** → Only the **root** user can use `at`.
---
##### Examples
Allow only `saif` and `admin`:
```text
/etc/at.allow

saif
admin
```
---
Deny `student` and `guest`:
```text
/etc/at.deny

student
guest
```
---
##### Quick Revision

| Condition             | Who can use `at`?                  |
| --------------------- | ---------------------------------- |
| `at.allow` exists     | Only users listed in `at.allow`    |
| Only `at.deny` exists | Everyone except users in `at.deny` |
| Neither file exists   | Root only                          |

---
### crontab (Recurring Scheduled Jobs)

##### Overview
- Used to schedule **recurring (repeated)** jobs.
- Jobs are stored in each user's crontab.
- Managed by the **crond** service.
- Each user has their own crontab file.

> **Remember:**  
> - `at` → One-time jobs.  
> - `crontab` → Recurring jobs.


---
##### Common Commands

| Command              | Description                             |
| -------------------- | --------------------------------------- |
| `crontab -e`         | Edit current user's crontab             |
| `crontab -l`         | List current user's crontab             |
| `crontab -r`         | Remove current user's crontab           |
| `crontab -u USER -e` | Edit another user's crontab (root only) |
| `crontab -u USER -l` | List another user's crontab             |
| `crontab -u USER -r` | Remove another user's crontab           |

---
##### Cron Service
```bash
systemctl status crond
systemctl start crond
systemctl enable crond
```
---
##### Crontab Syntax
```text
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of Week (0-7) (0 & 7 = Sunday)
│ │ │ └──── Month (1-12)
│ │ └────── Day of Month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)
```
---
##### Special Characters

| Symbol | Meaning | Example |
|--------|---------|---------|
| `*` | Every value | `* * * * *` |
| `,` | List | `1,15,30` |
| `-` | Range | `1-5` |
| `/` | Step | `*/10` |

---
##### Special Time Strings

| String | Meaning |
|---------|---------|
| `@reboot` | Run once after system boots |
| `@hourly` | Every hour |
| `@daily` | Once a day |
| `@weekly` | Once a week |
| `@monthly` | Once a month |
| `@yearly` or `@annually` | Once a year |

---
##### Examples
Run every minute:
```cron
* * * * * date >> /tmp/date.log
```

Run every day at 2:30 AM:
```cron
30 2 * * * /home/user/backup.sh
```

Run every Monday at 8:00 AM:
```cron
0 8 * * 1 /home/user/report.sh
```

Run every 10 minutes:
```cron
*/10 * * * * /home/user/script.sh
```

Run at reboot:
```cron
@reboot /home/user/startup.sh
```
---
##### System Cron Files

| File                 | Purpose                               |
| -------------------- | ------------------------------------- |
| `/etc/crontab`       | System-wide cron file                 |
| `/etc/cron.d/`       | Directory to add Additional cron jobs |
| `/etc/cron.daily/`   | Runs daily                            |
| `/etc/cron.weekly/`  | Runs weekly                           |
| `/etc/cron.monthly/` | Runs monthly                          |
| `/etc/cron.hourly/`  | Runs hourly                           |
| `/var/spool/cron`    | path of all cron jobs for each user   |

---
##### Access Control

| File | Description |
|------|-------------|
| `/etc/cron.allow` | Only listed users can use `crontab` |
| `/etc/cron.deny` | Listed users cannot use `crontab` |

**Priority:**
1. If `cron.allow` exists → Only listed users can use `crontab`.
2. If only `cron.deny` exists → Everyone except listed users can use it.
3. If neither exists → Only `root` can use `crontab`.
---
##### Backup current user's cron jobs
```bash
crontab -l > cron_backup.txt
```
Exports all cron jobs to a file.

---
##### Restore cron jobs from a backup
```bash
crontab cron_backup.txt
```
Imports the cron jobs from the backup file.
> **Note:** Importing a backup **replaces** the current crontab.

---
##### Example
Backup:
```bash
crontab -l > mycron.bak
```

Restore:
```bash
crontab mycron.bak
```

##### Quick Revision

| Task | Command |
|------|---------|
| Edit crontab | `crontab -e` |
| List crontab | `crontab -l` |
| Remove crontab | `crontab -r` |
| Edit another user's crontab | `crontab -u USER -e` |
| Service | `crond` |
| Job type | Recurring |

> **Memory Tip**
> - **at** = One-Time
> - **cron** = Recurring
> - **crond** = The daemon that executes cron jobs