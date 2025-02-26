# 傳統 BIOS PXE 選單設定

[← 返回首頁](./)

## 設定檔位置與編碼格式

設定檔位於 pxelinux.cfg 資料夾中，直接編輯 default 檔案，檔案編碼格式設定為 ANSI

## 設定檔說明

SYSLINUX 選單設定可以參考 <https://wiki.syslinux.org/wiki/index.php?title=Menu>

以下為設定檔範例：

```bash
# 選單設定

# 啟動 vesamenu 模組，功能較豐富
UI vesamenu.c32

# 設定解析度，建議使用 800 × 600，字型大小較為適中
MENU RESOLUTION 800 600

# 背景圖片檔案，支援 PNG、JPG 檔
MENU BACKGROUND background.png

# 選單設定，請參考 SYSLINUX 選單設定
MENU ROWS 9
MENU TIMEOUTROW 17
MENU HELPMSGROW 18
MENU TABMSG
MENU CMDLINEROW 18
MENU VSHIFT 3
MENU COLOR TITLE 0 #ccc8c4b7 #00000000 std
MENU COLOR DISABLED 0 #ff00afff #00000000 std
MENU COLOR BORDER 0 #ffffffff #00000000 std
MENU COLOR SEL 0 #ffffcc00 #ee0000fd std
MENU COLOR TIMEOUT 0 #ffff3300 #00000000 std
MENU COLOR HELP 0 #ffff3300  #00000000 std
MENU COLOR CMDLINE 0 #ffffffff #ff000000 std

# 選單標題
MENU TITLE pxeboot tilte

# 選單範例
# 請將 (X.X.X.X) 替換為伺服器 IP，記得移除 ()
LABEL Reboot
    MENU LABEL Reboot
    COM32 reboot.c32
    TEXT HELP
        Reboot
    ENDTEXT

# 啟動 CloneZilla 穩定版
LABEL clonezillaStable
    MENU INDENT 4
    MENU LABEL CloneZilla Stable version
    KERNEL http://(X.X.X.X)/CloneZilla/vmlinuz
    APPEND initrd=http://(X.X.X.X)/CloneZilla/initrd.img fetch=http://(X.X.X.X)CloneZilla/filesystem.squashfs boot=live union=overlay config components noswap modprobe.blacklist=snd_hda_intel,iscsi_tcp,ib_iser,jfs,xfs,aoe,i915,radeonhd,nouveau ethdevice-timeout=60 locales=zh_TW.UTF-8 keyboard-layouts=us edd=on nomodeset nodmraid ocs_live_batch=no vga=788 net.ifnames=0 ocs_prerun1="dhclient -v eth0" ocs_live_run="ocs-live-general"
    TEXT HELP
        CloneZilla Live stable version
    ENDTEXT

# 啟動 GParted
LABEL gparted
    MENU INDENT 4
    MENU LABEL GParted
    KERNEL http://(X.X.X.X)/GParted/vmlinuz
    APPEND initrd=http://(X.X.X.X)/GParted/initrd.img fetch=http://(X.X.X.X)/GParted/filesystem.squashfs boot=live ethdevice-timeout=60 locales=zh_TW.UTF-8 keyboard-layouts=us config components union=overlay username=user noswap noeject vga=788 gl_batch
    TEXT HELP
        GParted Live
    ENDTEXT

# 啟動 Memtest86+ 穩定版
LABEL memtest86
    MENU INDENT 4
    MENU LABEL Memtest86+
    # 要使用 LINUX 指令載入檔案，或者使用 KERNEL 載入，但不能有副檔名
    LINUX http://(X.X.X.X)/memtest64.bin
 TEXT HELP
        Memtest86+
    ENDTEXT
 ```
