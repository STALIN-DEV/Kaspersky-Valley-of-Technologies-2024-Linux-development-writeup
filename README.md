# Kaspersky-Valley-of-Technologies-2024-Linux-development-write-up
Нам известно, что нужные нам файлы находятся в каналах процесса и окружении, однако для начала необходимо узнать его `pid`:
```shell
[midori@fedora ~]$ pidof /usr/bin/firewall
707
```
Теперь мы проверяем каналы процесса:
```shell
[midori@fedora ~]$ sudo ls -l /proc/707/fd
[sudo] password for midori: 
total 0
lr-x------. 1 root root 64 Jun 13 11:41 0 -> /usr/share/alsa/alsa.cnf
lrwx------. 1 root root 64 Jun 13 11:41 1 -> 'socket:[7532]'
lrwx------. 1 root root 64 Jun 13 11:41 2 -> 'socket:[7532]'
```
Как и следовало ожидать, мы нашли подходящий файл, теперь двигаемся в направлении окружения:
```shell
[midori@fedora ~]$ ls /proc/707
ls: cannot read symbolic link '/proc/707/cwd': Permission denied
ls: cannot read symbolic link '/proc/707/root': Permission denied
ls: cannot read symbolic link '/proc/707/exe': Permission denied
arch_status  clear_refs          cpuset   fdinfo             latency    mem         ns             pagemap      sched      smaps_rollup  syscall         uid_map
attr         cmdline             cwd      gid_map            limits     mountinfo   numa_maps      patch_state  schedstat  stack         task            wchan
autogroup    comm                environ  io                 loginuid   mounts      oom_adj        personality  sessionid  stat          timens_offsets
auxv         coredump_filter     exe      ksm_merging_pages  map_files  mountstats  oom_score      projid_map   setgroups  statm         timers
cgroup       cpu_resctrl_groups  fd       ksm_stat           maps       net         oom_score_adj  root         smaps      status        timerslack_ns
[midori@fedora ~]$ sudo cat /proc/707/environ
LANG=en_US.UTF-8PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/binUSER=rootINVOCATION_ID=9487e0dc37d04e55b2a861dbc955cc73KLTV_KEY=/usr/share/applications/mimeinfo.cacheSYSTEMD_EXEC_PID=707MEMORY_PRESSURE_WATCH=/sys/fs/cgroup/system.slice/kl-firewall.service/memory.pressureMEMORY_PRESSURE_WRITE=c29tZSAyMDAwMDAgMjAwMDAwMAA=JOURNAL_STREAM=8:7532
```
А вот и переменная `KLTV_KEY=/usr/share/applications/mimeinfo.cache`. Задание решено.
