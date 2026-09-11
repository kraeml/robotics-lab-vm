# SD-Karte in VirtualBox (Vagrant) durchreichen

## Problem

SD-Karte soll in der Vagrant-VM `robotics` für `rpi-imager` zugänglich sein.

## Warum über USB?

VirtualBox kann SD-Karten nicht direkt durchreichen – nur USB-Geräte. Der SD-Reader selbst ist ein USB-Gerät. Alternativen wie Raw-VMDK oder Dateifreigaben sind deutlich komplexer und fehleranfällig.

## Voraussetzungen

- **VirtualBox Extension Pack** in derselben Version wie VirtualBox – die
  USB-Durchreichung (USB 2.0/3.0) ist ohne dieses Paket nicht möglich.
  Viele SD-Reader melden sich nur als USB-3.0-Gerät. Installation und
  Fehlersuche: siehe README, Abschnitt „USB-Geräte durchreichen".
- Einen SD-Card-Reader (USB) mit eingelegter SD-Karte.

## Lösung: VirtualBox GUI

1. VM-Einstellungen öffnen
2. **USB** auswählen
3. **+ Button** → USB-Filter hinzufügen
4. Den SD-Card-Reader aus der Liste auswählen
5. OK

## Verwendung in der VM

```bash
vagrant ssh

# SD-Karte prüfen
lsblk

# rpi-imager installieren und starten
sudo apt update && sudo apt install -y rpi-imager
rpi-imager
```

---

## Für erfahrene Benutzer

### Alternative: SD-Karte ohne USB-Reader durchreichen

Wenn kein USB-Reader vorhanden ist oder du direkt auf die SD-Karte zugreifen möchtest:

**Raw-VMDK-Methode:**

```bash
# Host: SD-Karte identifizieren (z. B. /dev/sdb)
lsblk

# Raw-Disk-Datei erstellen
# Achtung: VBoxManage expandiert "~" nicht – absoluten Pfad angeben!
VBoxManage internalcommands createrawvmdk \
  -filename /home/$USER/vms/sd-card.vmdk \
  -rawdisk /dev/sdb

# Im Vagrantfile (ebenfalls absoluter Pfad – "~" wird hier nicht
# expandiert):
config.vm.provider "virtualbox" do |vb|
  vb.customize ["storageattach", :id,
    "--storagectl", "SATA Controller",
    "--port", "1",
    "--device", "0",
    "--type", "hdd",
    "--medium", "/home/vagrant-host/vms/sd-card.vmdk"]
end
```

**Nachteile:**

- Braucht Root-Zugriff
- Fehleranfällig, wenn die SD-Karte sich bewegt
- Host-Zugriff auf die Disk gesperrt, während VM läuft
- Betriebssystem-abhängig (Linux anders als macOS/Windows)

**Empfehlung:** USB-Reader ist deutlich praktischer.

### Alternative: Dateifreigabe

SD-Karte auf dem Host mounten und den Mount-Punkt per Vagrant synced
folder (z. B. mit NFS) in die VM einbinden. Funktioniert für Dateizugriff,
ist aber langsamer und für Raw-Zugriff (wie `rpi-imager`, das direkt auf
das Blockgerät schreibt) ungeeignet.
