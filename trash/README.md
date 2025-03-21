---
icon: linux
---

# trash

Tips

멀티스레드로 압축하기
XZ_OPT='-T0' tar cJvf output.tar.xz inputFile
xz --help
...(중략)
-T, -thread=<count> : 스레드 수 설정, 기본값 : 1, 0 : 실제 프로세스 코어 수 사용
tar options
-c, --create : 아카이브 생성
-J, --xz : 압축 방식 xz 로 설정
-v, --verbose : 처리내역 자세하게 출력
-f, --file : 아카이브 대상파일 이름

----

resource / info

CPU 정보 확인
lscpu (List Central Processing Unit)
➜  ~ lscpu
Architecture:             x86_64
CPU op-mode(s):         32-bit, 64-bit
Address sizes:          48 bits physical, 48 bits virtual
Byte Order:             Little Endian
CPU(s):                   16
On-line CPU(s) list:    0-15
Vendor ID:                AuthenticAMD
Model name:             AMD Ryzen 7 5700U with Radeon Graphics
CPU family:           23
Model:                104
Thread(s) per core:   2
Core(s) per socket:   8
Socket(s):            1
Stepping:             1
CPU max MHz:          4372.0000
CPU min MHz:          400.0000
BogoMIPS:             3593.52
...

Memory 정보
free
➜  ~ free -h
total        used        free      shared    buff/cache    available
Mem:       30Gi        20Gi       325Mi       359Mi          10Gi       9.9Gi
Swap:        0B          0B          0B

---

top
Linux에서 실행 중인 프로세스와 커널 관리 작업 에 대한 동적이고 실시간 뷰 제공한다.
options
-h | -v  :Help/Version
Show library version and the usage prompt, then quit.

       -b  :Batch-mode operation
            Starts  top  in  Batch mode, which could be useful for sending output from top to other programs or to a file.
            In this mode, top will not accept input and  runs  until  the  iterations  limit  you've  set  with  the  `-n'
            command-line option or until killed.

       -c  :Command-line/Program-name toggle
            Starts  top  with the last remembered `c' state reversed.  Thus, if top was displaying command lines, now that
            field will show program names, and vice versa.  See the `c' interactive command for additional information.

       -d  :Delay-time interval as:  -d ss.t (secs.tenths)
            Specifies the delay  between  screen  updates,  and  overrides  the  corresponding  value  in  one's  personal
            configuration  file  or  the  startup  default.   Later  this  can  be changed with the `d' or `s' interactive
            commands.

            Fractional seconds are honored, but a negative number is not allowed.  In all cases, however, such changes are
            prohibited  if  top  is running in Secure mode, except for root (unless the `s' command-line option was used).
            For additional information on Secure mode see topic 6d. SYSTEM Restrictions File.

       -e  :Enforce-Task-Memory-Scaling as:  -e  k | m | g | t | p
            Instructs top to force task area memory to be scaled as:
               k - kibibytes
               m - mebibytes
               g - gibibytes
               t - tebibytes
               p - pebibytes

            Later this can be changed with the `e' command toggle.

       -E  :Enforce-Summary-Memory-Scaling as:  -E  k | m | g | t | p | e
            Instructs top to force summary area memory to be scaled as:
               k - kibibytes
               m - mebibytes
               g - gibibytes
               t - tebibytes
               p - pebibytes
               e - exbibytes

            Later this can be changed with the `E' command toggle.
            
       -H  :Threads-mode operation
            Instructs top to display individual threads.  Without this command-line option a summation of all  threads  in
            each process is shown.  Later this can be changed with the `H' interactive command.

       -i  :Idle-process toggle
            Starts top with the last remembered `i' state reversed.  When this toggle is Off, tasks that have not used any
            CPU since the last update will not be displayed.  For additional information regarding this toggle  see  topic
            4c. TASK AREA Commands, SIZE.

       -n  :Number-of-iterations limit as:  -n number
            Specifies the maximum number of iterations, or frames, top should produce before ending.

       -o  :Override-sort-field as:  -o fieldname
            Specifies  the  name  of  the  field  on  which  tasks will be sorted, independent of what is reflected in the
            configuration file.  You can prepend a `+' or `-' to the field name to also override the  sort  direction.   A
            leading `+' will force sorting high to low, whereas a `-' will ensure a low to high ordering.

            This option exists primarily to support automated/scripted batch mode operation.

       -O  :Output-field-names
            This  option acts as a form of help for the above -o option.  It will cause top to print each of the available
            field names on a separate line, then quit.   Such  names  are  subject  to  NLS  (National  Language  Support)
            translation.

       -p  :Monitor-PIDs mode as:  -pN1 -pN2 ...  or  -pN1,N2,N3 ...
            Monitor  only  processes  with  specified  process  IDs.   This option can be given up to 20 times, or you can
            provide a comma delimited list with up to 20 pids.  Co-mingling both approaches is permitted.

            A pid value of zero will be treated as the process id of the top program itself once it is running.

            This is a command-line option only and should you wish to return to normal operation, it is not  necessary  to
            quit and restart top  --  just issue any of these interactive commands: `=', `u' or `U'.

            The `p', `u' and `U' command-line options are mutually exclusive.

       -s  :Secure-mode operation
            Starts  top  with  secure  mode  forced,  even  for root.  This mode is far better controlled through a system
            configuration file (see topic 6. FILES).

       -S  :Cumulative-time toggle
            Starts top with the last remembered `S' state reversed.  When Cumulative time mode  is  On,  each  process  is
            listed  with  the  cpu  time  that  it  and  its dead children have used.  See the `S' interactive command for
            additional information regarding this mode.

       -u | -U  :User-filter-mode as:  -u | -U number or name
            Display only processes with a user id or user name matching that given.  The `-u' option matches on  effective
            user whereas the `-U' option matches on any user (real, effective, saved, or filesystem).

            Prepending  an  exclamation  point  (`!')  to the user id or name instructs top to display only processes with
            users not matching the one provided.

            The `p', `u' and `U' command-line options are mutually exclusive.

       -w  :Output-width-override as:  -w [ number ]
            In Batch mode, when used without an argument top will format output using the COLUMNS= and LINES=  environment
            variables, if set.  Otherwise, width will be fixed at the maximum 512 columns.  With an argument, output width
            can be decreased or increased (up to 512) but the number of rows is considered unlimited.

            In normal display mode, when used without an argument top will attempt to format output using the COLUMNS= and
            LINES=  environment  variables,  if set.  With an argument, output width can only be decreased, not increased.
            Whether using environment variables or an argument with -w, when not in Batch mode actual terminal  dimensions
            can never be exceeded.

            Note:  Without  the use of this command-line option, output width is always based on the terminal at which top
            was invoked whether or not in Batch mode.

       -1  :Single/Separate-Cpu-States toggle
            Starts top with the last remembered Cpu  States  portion  of  the  summary  area  reversed.   Either  all  cpu
            information  will  be  displayed  in  a single line or each cpu will be displayed separately, depending on the
            state of the NUMA Node command toggle ('2').

            See the `1' and '2' interactive commands for additional information.

----

Vim

.vimrc
set tabstop=4       " The width of a TAB is set to 4.
" Still it is a \t. It is just that
" Vim will interpret it to be having
" a width of 4.

set shiftwidth=4    " Indents will have a width of 4

set softtabstop=4   " Sets the number of columns for a TAB

set expandtab       " Expand TABs to spaces

---

fs-cache

참고
FS-Cache는 네트워크를 통해 가져온 데이터를 로컬 디스크에 캐시하여 네트워크 파일 시스템(NFS)과 같은 네트워크 기반 파일 시스템의 성능을 향상시키는 기능입니다. 이를 통해 네트워크 트래픽을 최소화하고 데이터 접근 속도를 개선할 수 있습니다.

주요 특징:
투명성: FS-Cache는 사용자와 관리자에게 투명하게 동작하여, 기존 파일 시스템의 기본 작업 방식을 변경하지 않습니다. 예를 들어, NFS 공유를 마운트할 때 별도의 파일 시스템을 오버마운트하지 않고도 로컬 캐시와 직접 상호작용할 수 있습니다.
캐시 백엔드: FS-Cache는 캐시 서비스를 제공하기 위해 cachefiles와 같은 캐시 백엔드를 필요로 합니다. 이를 위해 확장 속성과 bmap을 지원하는 ext3, ext4, Btrfs, XFS와 같은 파일 시스템을 사용하여 캐시를 저장합니다.
NFS와의 통합: NFS는 명시적으로 지시하지 않는 한 캐시를 사용하지 않습니다. NFS 마운트 시 -o fsc 옵션을 추가하여 FS-Cache를 활성화할 수 있습니다. 이를 통해 NFS 버전 2, 3, 4에서 캐싱을 지원하며, 각 버전은 캐싱을 위한 다른 분기를 사용합니다.
성능 보장: FS-Cache는 성능 향상을 보장하지 않지만, 네트워크 혼잡을 피함으로써 일관된 성능을 제공합니다. 캐시 백엔드를 사용하는 것은 디스크 접근을 추가하므로 약간의 성능 저하를 가져올 수 있지만, 로컬에서 읽기 요청을 처리하여 네트워크 및 서버 부하를 크게 줄일 수 있습니다.
캐시 한계 및 제한: FS-Cache는 직접 I/O 또는 쓰기를 위해 열리는 파일에 대해서는 캐시를 우회합니다. 또한, 현재 릴리스에서는 정규 NFS 파일만 캐시하며, 디렉토리, 심볼릭 링크, 디바이스 파일 등은 캐시하지 않습니다.
캐시 설정: cachefilesd 데몬을 사용하여 캐시를 설정하고 관리합니다. 캐시 디렉토리는 /etc/cachefilesd.conf 파일에서 설정하며, 일반적으로 /var/cache/fscache를 사용합니다. 캐시를 시작하려면 cachefilesd 서비스를 시작하고 부팅 시 자동으로 시작되도록 설정할 수 있습니다.
통계 정보: FS-Cache는 일반적인 통계 정보를 추적하며, /proc/fs/fscache/stats 파일을 통해 이러한 정보를 확인할 수 있습니다.

---

