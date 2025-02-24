# CloneZilla 參數設定

## 映像檔儲存區設定

可以使用 ocs_repository 來設定映像檔儲存區，參數說明：
<https://clonezilla.nchc.org.tw/clonezilla-live/doc/fine-print.php?path=99_Misc/00_live-boot-parameters.doc>

以下是幾個範例：

* 使用 NFS：

```bash
ocs_repository="nfs://192.168.100.254/home/partimag/"
```

* 使用 SMB 3.0：

```bash
ocs_repository="smb3://administrator:mypasswd@192.168.100.175/images/"
```

## 自動儲存 / 還原映像檔

可以使用 ocs_live_run 參數來設定自動儲存 / 還原映像檔，參數說明：<https://clonezilla.org/fine-print-live-doc.php?path=./clonezilla-live/doc/98_ocs_related_command_manpages/01-ocs-sr.doc>

### 自動儲存

參數範例：

```bash
ocs_live_run="ocs-sr -q2 -j2 -batch -rm-win-swap-hib -z9p -i 0 -sfsck -scs -senc -p poweroff savedisk ask_user all"
```

參數說明：

* **-q2**：使用 Partclone 來保存映像檔
* **-j2**：保存磁碟中 MBR 的隱藏資料
* **-batch**：使用批次模式，不會進行任何確認
* **-rm-win-swap-hib**：移除 Windows 系統中虛擬記憶體檔、休眠檔，可節省映像檔大小
* **-z9p**：使用 zstd 多執行緒模式壓縮，多數情況下會是最佳模式
* **-i 0**：映像檔分片大小，單位是 MB，0 表示不分片，如果儲存映像檔目標是 fat32 系統，分片大小不能大於 4096 MB
* **-sfsck**：建立映像檔前跳過系統磁區檢查
* **-scs**：建立映像檔後跳過映像檔檢查
* **-senc**：建立映像檔前跳過映像檔加密
* **-p power off**：建立映像檔後關機
* **save_disk**：進行儲存映像檔動作
* **ask_user**：儲存映像檔前詢問使用者要用什麼名稱
* **all**：保存所有磁碟

## 自動還原

參數範例：

```bash
ocs_live_run="ocs-sr -g auto -batch -e1 auto -e2 -r -hn1 pc -icds -j2 -k1 -scr -p reboot restoredisk IMAGE_NAME all"
```

參數說明：

* **-g auto**：自動安裝 Grub，有偵測到 Grub 才會執行
* **-batch**：使用批次模式，不會有確認訊息
* **-e1 auto**：如果是 NTFS 磁碟，則自動調整開機磁區的 CHS (cylinders, heads, sectors) 數值
* **-e2**：建立磁碟分割表時使用 EDD (Enhanced Disk Device)
* **-r**：自動調整磁區大小，例如從小硬碟還原到大硬碟，會等比例放大分割區
* **-hn1 pc**：自動建立還原電腦名稱（Windows 適用），hn1 會以 MAC 後面 6 碼命名，後面的參數則做為名稱前綴，這個範例會將電腦命名為 pc-xxxxxxx
* **-icds**：忽略還原目標硬碟大小，大硬碟到小硬碟適用
* **-j2**：複製 MBR 到第一個分割區的隱藏資料
* **-k1**：依照比例調整分割區
* **-scr**：跳過映像檔檢查
* **-p reboot**：還原後重新啟動
* **restoredisk**：進行還原映像檔動作
* **IMAGE_NAME**：將 IMAGE_NAME 替換為還原映像檔名稱
* **all**：自動選擇第一個磁碟

### Q & A

#### Q1. 儲存映像檔出現錯誤

進行 chkdsk 掃描磁碟後再進行儲存。

#### Q2. 從大硬碟還原到小硬碟出現錯誤

如果原本磁區有分割（例如 C、D），通常大硬碟到小硬碟可以成功，如果只有單一分割區，就無法進行大硬碟到小硬碟的還原，如果電腦型號較多，建議以最小硬碟的機型做為映像檔範本。
