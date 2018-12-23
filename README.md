# VNC安裝 
### 參考網站:
```
https://hackmd.io/_Y6hnsrVSki7GABCevJJCQ
```
**Install X11VNC**

先進行更新(可選)
```
sudo apt-get update
sudo apt-get upgrade
```

安裝：
```
sudo apt-get install x11vnc -y
```

若未更新直接執行上面指令可能會出現error，解決方法： 
```
sudo apt-get update
```

**設定連接的VNC密碼**
```
sudo x11vnc -storepasswd /etc/x11vnc.pass
```

**切換root權限**
```
su
```

**Create the Service Unit File**
```
cat > /lib/systemd/system/x11vnc.service << EOF
```
```
[Unit]
Description=Start x11vnc at startup.
After=multi-user.target
[Service]
Type=simple
ExecStart=/usr/bin/x11vnc -auth guess -forever -loop -noxdamage -repeat -rfbauth /etc/x11vnc.pass -rfbport 5900 -shared
[Install]
WantedBy=multi-user.target
EOF
```

建議Ubuntu 15.04 以上使用(開機自動啟動)

**Configure the Service**(從 echo "Configure Services" 複製到 sleep 10)然後一次全部貼上
```
echo "Configure Services"
sudo systemctl enable x11vnc.service
sudo systemctl daemon-reload
sleep 10
```

(開機自動啟動)
建議Ubuntu 14.10 以下參考


http://jamyy.us.to/blog/2015/04/7354.html


# Step 2. 設定虛擬解析度
http://www.cnblogs.com/elmaple/p/4354814.html

Server 如果沒有外接螢幕顯示器，x-session 不能從外部獲取解析度，需要在 xorg.conf 中設置虛擬解析度。

ubuntu 默認已經沒有 /etc/X11/xorg.conf，也沒有必要用 Xorg -configure創建，直接手動創建就行，並添加如下代碼(用戶端螢幕解析度是 1920x1080，可以根據實際情況修改 Virtual 參數)

turn to /etc/X11
```
cd /etc/X11
```

```
sudo gedit xorg.conf
```

```
Section "Device"
        Identifier "Configured Video Device"
EndSection

Section "Monitor"
        Identifier "Configured Monitor"
EndSection

Section "Screen"
        Identifier "Default Screen"
        Monitor "Configured Monitor"
        Device "Configured Video Device"
        SubSection "Display"
                   Depth 24
                   Virtual 1920 1080
        EndSubSection
EndSection
```

**Restart System**
```
sudo shutdown -r now
```

# Step 3. start vnc server

脫離連線不停止 x11vnc 程序
```
x11vnc -display :0 -forever -bg
```

啟動vnc
```
x11vnc
```

PS.
ubuntu IP查詢
```
ifconfig
```
PS.
使用VMware時的port設定
注意！！一定要使用VMware WorkStation，VMware Player無此功能
![](https://i.imgur.com/Gqcvzek.jpg)
