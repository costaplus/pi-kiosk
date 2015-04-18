# Pi-Kiosk
Pi-Kiosk è una soluzione basata su raspberry pi, che consente di eseguire lo slideshow di una cartella di immagini su un televisore.

Le immagini vengono prevede l'utilizzo di btsync per l'aggiornamento delle immagini. Grazie a btsync è possibile aggiungere o rimuovere le immagini, utilizzando un computer o uno smartphone.

pi-kiosk spegne il televisore la sera e lo riaccende la mattina utilizzando lo standard cec dell'HDMI presente in quasi tutti i televisori di ultima generazione.

1. Materiale occorrente
---
* n. 1 [Raspberry Pi](http://goo.gl/MybLy9)
* n. 1 [Case](http://goo.gl/Znz5zb)
* n. 1 [Scheda di memoria](http://goo.gl/3OPHrh)
* n. 1 [Adattatore USB WiFi nano](http://goo.gl/O1TmFa)
* n.1 [Alimentatore 2A per Raspberry](http://goo.gl/jWQpXN)
* n.1 Cavo HDMI
* n.1 Televisore con HDMI cec

2. Preparare la Raspberry
---
Installate l'ultima versione del sistema operativo raspbian. Ci sono centinaia di guide su internet su come farlo. Ecco la centunesima:

Se nel tuo computer hai Linux, usa questa procedura per installare CFS-OS nella SD.

* Apri una finestra terminale
* Da root, inserisci l'SD nel card reader del computer ed esegui questo comando:
```bash
# df -h
```
*  Esegui l'umount della scheda: (sdb1 è solo un esempio, il nome potrebbe essere diverso)
```bash
# umount /dev/sdb1
```
* Scrivi l'immagine nella SD (Attendi a quello che fate)
```bash
# dd if=./immagine.img of=/dev/sdX bs=4k
```
* Eseguito questo comando per essere sicuri che tutta la cache sia scritta nell'SD
```bash
# sync
```

3. Installare pi-kiosk
---
* Installare il programma di visualizzazione immagini
```bash
$ sudo apt-get install feh
```
* Collegarsi in ssh sulla rasp e posizionarsi su /mnt
```bash
$ cd /mnt
```
* Scaricare il software
```bash
$ git clone https://github.com/teopost/pi-kiosk
```

4. Configurare il software
---
Per disabilitare lo screensaver editare il file autostart situato sotto /etc/xdg/lxsession/LXDE-pi.
```bash
@lxpanel --profile LXDE-pi
@pcmanfm --desktop --profile LXDE-pi
# @xscreensaver -no-splash
@xset s off
@xset -dpms
@xset s noblank
@/mnt/pi-kiosk/bin/slideshow.sh
```
Nel suddetto file, commentare la riga che contiene xscreensaver e aggiugnere la riga in fondo per l'esecuzione automatica di pi-kiosk.

5. Installazione di btsync
---
Per sincronizzare le immagini installare [btsync](http://getsync.com). Ovviamente la versione per ARM.

6. Spegnimento automatico
---
Per spegnere e riaccendere automaticamente il televisore occorre installare la libreria cec per raspberry. Operazione da fare come root
```bash
# apt-get instal cec-client
# apt-get -y install udev libudev-dev autoconf automake libtool gcc liblockdev1
# git clone https://github.com/Pulse-Eight/libcec
# cd libcec/
# ./bootstrap
# ./configure --with-rpi-include-path=/opt/vc/include --with-rpi-lib-path=/opt/vc/lib --enable-rpi
# cec-client
# make
# make install
# ldconfig
```
7. Pianificare lo spegnimento
--
Nel crontab dell'utente pi, incollare le seguenti righe:
```bash
# .---------------- [m]inute: minuto (0 - 59)
# |  .------------- [h]our: ora (0 - 23)
# |  |  .---------- [d]ay [o]f [m]onth: giorno del mese (1 - 31)
# |  |  |  .------- [mon]th: mese (1 - 12) OPPURE jan,feb,mar,apr...
# |  |  |  |  .---- [d]ay [o]f [w]eek: giorno della settimana (0 - 6) (domenica=0 o 7)  OPPURE sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |

54 23 * * * /mnt/pi-kiosk/bin/turntv.sh off
30  7 * * * /mnt/pi-kiosk/bin/turntv.sh on && sleep 3 && /mnt/pi-kiosk/bin/turntv.sh input
```
