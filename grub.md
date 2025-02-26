# UEFI PXE 選單設定

[← 返回首頁](./)

## 設定檔位置與編碼格式

設定檔位於 grub 資料夾中，編輯 grub.cfg 檔案，檔案編碼格式需設定為 UTF-8（無 BOM），才能正確顯示中文

## 選單設定檔說明

GRUB 命令可以參考 <https://www.gnu.org/software/grub/manual/grub/grub.html>

以下為設定檔範例：

``` bash
# 載入模組、設定選單樣式
insmod efi_gop
insmod efi_uga
insmod gfxterm
insmod png
loadfont /grub/unicode.pf2
set gfxmode=800x600x32
set gfxpayload=keep
set menu_color_normal=white/black
set menu_color_highlight=yellow/blue
terminal_output gfxterm

# 設定變數，最後要用 export，讓變數可以在 submenu 使用
# 請將 (X.X.X.X) 替換為伺服器 IP，記得移除 ()
set http_url=http,(X.X.X.X)
set http_protocol=http://(X.X.X.X)
set clonezilla_blacklist=snd_hda_intel,iscsi_tcp,ib_iser,jfs,xfs,aoe,i915,radeonhd,nouveau
set theme=/grub/theme-tela/theme.txt
export http_url http_protocol clonezilla_blacklist theme

# 選單範例
menuentry "離開還原系統，重新開機" --class=reboot {
    echo "重新開機中..."
    reboot
}

menuentry "啟動 CloneZilla 穩定版" --class=clonezilla {
    echo "啟動 CloneZilla Live 穩定版中，請稍後..."
    linux ($http_url)/CloneZilla/vmlinuz fetch=$http_protocol/CloneZilla/filesystem.squashfs boot=live union=overlay config components noswap modprobe.blacklist=$clonezilla_blacklist ethdevice-timeout=60 locales=zh_TW.UTF-8 keyboard-layouts=us edd=on nomodeset nodmraid ocs_live_batch=no vga=788 net.ifnames=0 ocs_prerun1="dhclient -v eth0" ocs_live_run="ocs-live-general"
    initrd ($http_url)/CloneZilla/initrd.img
}

menuentry "啟動 GParted" --class=gparted {
    echo "啟動 GParted Live 中，請稍後..."
    linux ($http_url)/GParted/vmlinuz fetch=$http_protocol/GParted/filesystem.squashfs boot=live ethdevice-timeout=60 locales=zh_TW.UTF-8 keyboard-layouts=us config components union=overlay username=user noswap vga=788 gl_batch
    initrd ($http_url)/GParted/initrd.img
}

menuentry "啟動 Memtest86+" --class=memtest86 {
    echo "啟動 Memtest86+ 中，請稍後..."
    linux ($http_url)/memtest64.efi
}
```
