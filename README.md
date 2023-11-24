# h1haiu

#!/bin/bash
display_menu() {
        echo " Instalasi CLAMAV "
        echo " Pilih installasi"
        echo " 1. Konfigurasi clamd.conf "
        echo " 2. Konfigurasi Freshclam "
        echo " 3. Membuat grup user clamav "
        echo " 4. membuat log file Freshclam "
        echo " 5. membuat log file clamav "
        echo " 6. Update signature "
        echo " 7. menjalankan clamav daemon "
        echo " 8. membuat script clamav-daemon.sh "
        echo " 9. edit permission file clamav-daemon.sh "
        echo " 10. Membuat file clamav-daemon.service "
        echo " 11. Membuat file freshclam.service "
        echo " 12. membuat direktori socket clamav "
        echo " 13. start clamav "
        echo " 14. status clamav "
        echo " 15. start freshclam "
        echo " 16. status freshclam "
        echo " 17. Membuat directori scrip "
	echo " 18. Membuat scrip auto scan clamscan_daily "
	echo " 19. Membuat scrip auto scan clamdscan_daily "
	echo " 20. Membuat scrip auto scan logretention_daily "
        echo " 21. Membuat scrip clamav executeable "
        echo " 22. crontab -e "
	echo " 23. Konfigurasi Clamav Log "
	echo " 99. Exit "
}

# Loop utama
while true
do
        display_menu
        read -p "Masukkan pilihan anda: " choice

case $choice in
1)
echo "#Automatically Generated by clamav-daemon postinst
#To reconfigure clamd run #dpkg-reconfigure clamav-daemon
#Please read /usr/share/doc/clamav-daemon/README.Debian.gz for details
#LocalSocket /var/run/clamav/clamd.ctl
FixStaleSocket true
LocalSocketGroup clamav
LocalSocketMode 666
# TemporaryDirectory is not set to its default /tmp here to make overriding
# the default with environment variables TMPDIR/TMP/TEMP possible
User clamav
ScanMail true
ScanArchive true
ArchiveBlockEncrypted false
MaxDirectoryRecursion 15
FollowDirectorySymlinks false
FollowFileSymlinks false
ReadTimeout 180
MaxThreads 12
MaxConnectionQueueLength 15
LogSyslog false
LogRotate true
LogFacility LOG_LOCAL6
LogClean false
LogVerbose false
PreludeEnable no
PreludeAnalyzerName ClamAV
DatabaseDirectory /var/lib/clamav
OfficialDatabaseOnly false
SelfCheck 3600
Foreground false
Debug false
ScanPE true
MaxEmbeddedPE 10M
ScanOLE2 true
ScanPDF true
ScanHTML true
MaxHTMLNormalize 10M
MaxHTMLNoTags 2M
MaxScriptNormalize 5M
MaxZipTypeRcg 1M
ScanSWF true
ExitOnOOM false
LeaveTemporaryFiles false
AlgorithmicDetection true
ScanELF true
IdleTimeout 30
CrossFilesystems true
PhishingSignatures true
PhishingScanURLs true
PhishingAlwaysBlockSSLMismatch false
PhishingAlwaysBlockCloak false
PartitionIntersection false
DetectPUA false
ScanPartialMessages false
HeuristicScanPrecedence false
StructuredDataDetection false
CommandReadTimeout 30
SendBufTimeout 200
MaxQueue 100
ExtendedDetectionInfo true
OLE2BlockMacros false
AllowAllMatchScan true
ForceToDisk false
DisableCertCheck false
DisableCache false
MaxScanTime 120000
MaxScanSize 100M
MaxFileSize 25M
MaxRecursion 16
MaxFiles 10000
MaxPartitions 50
MaxIconsPE 100
PCREMatchLimit 10000
PCRERecMatchLimit 5000
PCREMaxFileSize 25M
ScanXMLDOCS true
ScanHWP3 true
MaxRecHWP3 16
StreamMaxLength 25M
LogFile /var/log/clamav/clamav.log
LogTime true
LogFileUnlock false
LogFileMaxSize 0
Bytecode true
BytecodeSecurity TrustSigned
BytecodeTimeout 60000
OnAccessMaxFileSize 5M
#Forward to Clamav Server Cloudciti
TCPAddr 10.27.15.50
TCPAddr 127.0.0.1
TCPSocket 3310
#Exclude directory
ExcludePath ^/dev/
ExcludePath ^/proc/
ExcludePath ^/sys/" | tee -a /usr/local/etc/clamd.conf
read -p "Tekan Enter untuk melanjutkan..."
;;

2)
echo "DatabaseOwner clamav
UpdateLogFile /var/log/freshclam.log
LogVerbose false
LogSyslog false
LogFacility LOG_LOCAL6
LogFileMaxSize 0
LogRotate true
LogTime true
Foreground false
Debug false
MaxAttempts 5
DatabaseDirectory /var/lib/clamav
DNSDatabaseInfo current.cvd.clamav.net
ConnectTimeout 30
ReceiveTimeout 0
TestDatabases yes
ScriptedUpdates yes
CompressLocalDatabase no
Bytecode true
NotifyClamd usr/local/etc/clamd.conf
Checks 24
DatabaseMirror db.local.clamav.net
DatabaseMirror database.clamav.net
#DatabaseMirror http://10.27.15.50" | tee -a /usr/local/etc/freshclam.conf
read -p "Tekan Enter untuk melanjutkan..."
;;

3)
groupadd clamav
useradd -g clamav -s /bin/false -c "Clam Antivirus" clamav
mkdir /usr/local/share/clamav
chown -R clamav:clamav /usr/local/share/clamav
read -p "Tekan Enter untuk melanjutkan..."
;;

4)
touch /var/log/freshclam.log
chmod 600 /var/log/freshclam.log
chown clamav /var/log/freshclam.log
read -p "Tekan Enter untuk melanjutkan..."
;;

5)
mkdir /var/log/clamav
touch /var/log/clamav/clamav.log
chmod 600 /var/log/clamav/clamav.log
chown clamav.clamav /var/log/clamav/clamav.log
read -p "Tekan Enter untuk melanjutkan..."
;;

6)
/usr/local/bin/freshclam
read -p "Tekan Enter untuk melanjutkan..."
;;

7)
/usr/local/bin/freshclam -d
read -p "Tekan Enter untuk melanjutkan..."
;;

8)
echo "#!/bin/sh
/usr/local/sbin/clamd --foreground=true" | tee -a /usr/local/clamav-daemon.sh

cat /usr/local/clamav-daemon.sh
read -p "Tekan Enter untuk melanjutkan..."
;;

9)
chmod 777 /usr/local/clamav-daemon.sh
read -p "Tekan Enter untuk melanjutkan..."
;;

10)
echo "Description=Clam AntiVirus userspace daemon
Documentation=man:clamd(8) man:clamd.conf(5) https://www.clamav.net/documents/
# Check for database existence
ConditionPathExistsGlob=/var/lib/clamav/main.{c[vl]d,inc}
ConditionPathExistsGlob=/var/lib/clamav/daily.{c[vl]d,inc}

[Service]
User=clamav
Group=clamav
#ExecStart=/usr/local/bin/clamd --foreground=true
ExecStart=/usr/local/clamav-daemon.sh
# Reload the database
ExecReload=/bin/kill -USR2
StandardOutput=syslog
TimeoutStartSec=420

[Install]
WantedBy=multi-user.target" | tee -a /etc/systemd/system/clamav-daemon.service

cat /etc/systemd/system/clamav-daemon.service
read -p "Tekan Enter untuk melanjutkan..."
;;

11)
echo "[Unit]
Description = ClamAV Signature Update
After = network.target

[Service]
Type = forking
# if you want to scan more than one in a day change the number 1 with your desired number in below line.
ExecStart = /usr/local/bin/freshclam -d -c 1
Restart = on-failure
PrivateTmp =true

[Install]
WantedBy=multi-user.target" | tee -a /etc/systemd/system/freshclam.service

cat /etc/systemd/system/freshclam.service
read -p "Tekan Enter untuk melanjutkan..."
;;

12)
mkdir /var/run/clamav
chown clamav.clamav /var/run/clamav
chmod 755 /var/run/clamav
read -p "Tekan Enter untuk melanjutkan..."
;;

13)
 systemctl daemon-reload
 systemctl enable clamav-daemon
 systemctl start clamav-daemon
read -p "Tekan Enter untuk melanjutkan..."
;;

14)
 systemctl status clamav-daemon
read -p "Tekan Enter untuk melanjutkan..."
;;

15)
 systemctl enable freshclam
 systemctl start freshclam
read -p "Tekan Enter untuk melanjutkan..."
;;

16)
 systemctl status freshclam
read -p "Tekan Enter untuk melanjutkan..."
;;

17)
mkdir /usr/local/clamav
mkdir /usr/local/clamav/script
read -p "Tekan Enter untuk melanjutkan..."
;;

18)
echo "#!/bin/bash

TMP_LOG=/var/log/clamav_daily/clam.log

av_scan() {
        touch "${TMP_LOG}"
/usr/local/bin/clamscan / --quiet --log="${TMP_LOG}"
}
av_scan" | tee -a /usr/local/clamav/script/clamscan_daily

cat /usr/local/clamav/script/clamscan_daily
echo " PERLU DIHAPUS tanda (" ") nya "
read -p "Tekan Enter untuk melanjutkan..."
;;

19)
echo "#!/bin/bash

av_scan() {
/usr/local/bin/clamdscan / --fdscan --quiet
}
av_scan" | tee -a /usr/local/clamav/script/clamdscan_daily

cat /usr/local/clamav/script/clamdscan_daily
read -p "Tekan Enter untuk melanjutkan..."
;;

20)
echo "#!/bin/bash
#logger.sh script
mv /var/log/clamav_daily/clam.log /var/log/clamav_daily/clamav.$(date +%d%m%Y).log" | tee -a /usr/local/clamav/script/logretention_daily

cat /usr/local/clamav/script/logretention_daily
read -p "Tekan Enter untuk melanjutkan..."
;;

21)
 chmod +x /usr/local/clamav/script/clamscan_daily
 chmod +x /usr/local/clamav/script/clamdscan_daily
 chmod +x /usr/local/clamav/script/logretention_daily
 read -p "Tekan Enter untuk melanjutkan..."
;;

22)
crontab -e
#echo "0 1 * * * /usr/local/clamav/script/clamdscan_daily"
#echo "0 3 * * * /usr/local/clamav/script/clamscan_daily"
#echo "0 5 * * * /usr/local/clamav/script/logretention_daily"
;;

23)
mkdir /var/log/clamav_daily
read -p "Tekan Enter untuk melanjutkan..."
;;

99)
echo "terimakasih, semoga berjalan clamavnya"
exit 0
;;

*)
echo "Pilihan tidak valid, Coba Lagi"
read -p "Tekan Enter untuk melanjutkan..."
;;

esac
done
