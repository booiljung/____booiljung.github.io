# Ubuntu 18.04 LTS에서 GUI와 CLI를 전환

2019년 4월 8일

## CLI 모드로 전환하기

`CTRL+ALT+F1`~`CTRL+ALT+F6`을 누르면 CLI 모드로 전환 할 수 있습니다.

다시 GUI 모드로 전환하려면 `CTRL+ALT+F7`을 누릅니다.

## GUI 서비스 종료하기

CLI 모드에서 다음을 입력합니다.

```sh
sudo service lightdm stop
```

GUI 모드가 종료 됩니다.

## CLI 모드로 부팅하기

vi 에디터로 `/etc/default/grub`파일을 편집합니다.

```sh
sudo vi /etc/default/grub
```

그러면 다음 vi 에디터에 내용이 표시 됩니다.

```
# If you change this file, run 'update-grub' afterwards to update
# /boot/grub/grub.cfg.
# For full documentation of the options in this file, see:
#   info -f grub -n 'Simple configuration'

GRUB_DEFAULT=0
GRUB_TIMEOUT_STYLE=hidden
GRUB_TIMEOUT=0
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX=""

# Uncomment to enable BadRAM filtering, modify to suit your needs
# This works with Linux (no patch required) and with any kernel that obtains
# the memory map information from GRUB (GNU Mach, kernel of FreeBSD ...)
#GRUB_BADRAM="0x01234567,0xfefefefe,0x89abcdef,0xefefefef"

# Uncomment to disable graphical terminal (grub-pc only)
#GRUB_TERMINAL=console

# The resolution used on graphical terminal
# note that you can use only modes which your graphic card supports via VBE
# you can see them in real GRUB with the command `vbeinfo'
#GRUB_GFXMODE=640x480

# Uncomment if you don't want GRUB to pass "root=UUID=xxx" parameter to Linux
#GRUB_DISABLE_LINUX_UUID=true

# Uncomment to disable generation of recovery mode menu entries
#GRUB_DISABLE_RECOVERY="true"

# Uncomment to get a beep at grub start
#GRUB_INIT_TUNE="480 440 1"                  
```

여기서 `GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"`라인을 주석 처리 합니다.

```
#GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
```

다음은 `GRUB_CMDLINE_LINUX=""` 라인에 `text`로 지정합니다.

```
GRUB_CMDLINE_LINUX="text"
```

다음은  `#GRUB_TERMINAL=console`의 주석을 제거 합니다.

```
GRUB_TERMINAL=console
```

이제 `wq!`를 입력하여 저장합니다.

터미널에서 다음을 입력하여 변경사항을 grub에 업데이트 합니다.

```sh
sudo update-grub
```

다음을 입력하여 시스템 설정을 변경 합니다.

```sh
sudo systemctl set-default multi-user.target
```

리부트 합니다.

```sh
reboot
```

## GUI 모드로 되돌리기

시스템 설정을 원래대로 되돌립니다.

```sh
sudo systemctl set-default graphical.target
```

vi 에디터로 `/etc/default/grub`파일을 편집하여 원래대로 되돌립니다.

```sh
sudo vi /etc/default/grub
```

주석을 제거하고

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
```

`GRUB_CMDLINE_LINUX`에서 `text`지정을 해제하고

```
GRUB_CMDLINE_LINUX=""
```

다시 `GRUB_TERMINAL`에 주석을 추가 합니다.

```
#GRUB_TERMINAL=console
```

`wq!`를 입력하여 저장 및 종료 후에 터미널에서 다음을 입력하여 변경사항을 grub에 업데이트 합니다.

```sh
sudo update-grub
```

다시 재부팅 합니다.

```sh
reboot
```

