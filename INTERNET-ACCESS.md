# Vagrant-Box über das Internet erreichbar machen

> **Wichtiger Hinweis zum Hosting-Registry:** HCP Vagrant / Vagrant Cloud wird
> **nicht** verwendet. Nach der offiziellen HashiCorp-Dokumentation können ab
> **1. Oktober 2026** keine neuen Boxen mehr über HCP Vagrant erstellt oder
> geteilt werden; Support und Wartung enden am **2. November 2026**; der
> Betrieb wird am **31. Dezember 2026** vollständig eingestellt. Quelle:
> [HashiCorp HCP Vagrant EOL](https://developer.hashicorp.com/hcp/docs/vagrant/hcp-vagrant-eol).
> Dieses Projekt bezieht die Box direkt über eine feste URL (siehe
> Vagrantfile) und ist von dieser Änderung nicht betroffen.
> Stand: 2026-09-09.

## Kontext

Die Ubuntu-Desktop-VM enthält bereits fertig eingerichtet ROS 2, code-server,
JupyterLab und PlatformIO und läuft aktuell **im Heimnetz** — dort ist sie nur
lokal erreichbar. Manchmal möchte man die laufende VM aber **von außen**
nutzen — z. B. um von unterwegs darauf zuzugreifen oder sie mit anderen zu
teilen. Dieses Dokument beschreibt zwei gangbare Wege dafür und die
**zwingend nötige Härtung** der Box vor einer Internet-Exposition.

## Sicherheitswarnung (vor allem anderen lesen!)

Diese VM ist für den **Vagrant-Entwicklungsworkflow** ausgelegt:

- Standardmäßig wird Vagrants [Insecure Key](https://www.vagrantup.com/docs/security)
  akzeptiert (öffentlich bekannt — nicht sicher!).
- Das Benutzerkonto `vagrant` hat typischerweise ein **einfaches Passwort**
  (`vagrant`) und ist sudo-fähig.

Diese Box **darf so nicht** ins Internet gestellt werden. **Vor jeder**
Internet-Exposition **muss** mindestens Folgendes erledigt sein:

1. **Eigenen SSH-Schlüssel** erzeugen und auf der VM unter
   `~vagrant/.ssh/authorized_keys` ablegen.
2. **Vagrants Insecure Key entfernen:** den öffentlich bekannten Public Key aus
   `~vagrant/.ssh/authorized_keys` löschen. Der zugehörige private Schlüssel
   (`insecure_private_key`) ist öffentlich — er liegt in jeder Vagrant-Installation
   unter `~/.vagrant.d/insecure_private_key` und ist im HashiCorp-Repo unter
   `keys/vagrant` abrufbar — und darf daher nicht als einziger Zugang dienen.
3. **Starkes Passwort** für den `vagrant`-Account (oder besser: den Account
   umbenennen/deaktivieren und einen neuen Benutzer anlegen).
4. **Passwort-Login deaktivieren** in `/etc/ssh/sshd_config`:
   `PasswordAuthentication no`, `PermitRootLogin no`,
   `ChallengeResponseAuthentication no`.
5. **`ufw` (oder vergleichbare Firewall) aktivieren** und nur die wirklich
   benötigten Ports freigeben.
6. **Automatische Sicherheitsupdates** aktivieren
    (`unattended-upgrades` auf Ubuntu).
7. Fail2ban o. ä. installieren (siehe Option A unten).
8. **Browser-Dienste absichern:** code-server (`http://…:8080`) nutzt das
   Passwort `change_me` — starkes eigenes Passwort direkt in der VM setzen
   (Konfigurationsdatei von code-server, siehe dessen Dokumentation);
   JupyterLab (`http://…:8888`) läuft ohne Token — vor Exposition ein Token
   setzen oder den Dienst deaktivieren (siehe JupyterLab-Dokumentation zur
   Konfigurationsdatei `jupyter_lab_config.py`).

Erst danach ist eine der beiden Optionen unten überhaupt sinnvoll anwendbar.

---

## Option A: Port-Forwarding am Heimrouter (klassisch)

Die Idee: Der Router leitet einen öffentlichen Port (z. B. WAN-Port
`22222 → LAN-IP-der-VM:22`) auf den SSH-Port der VM weiter.

**Vorteile**

- Relativ einfach einzurichten (einmal im Router-UI).
- Keine zusätzliche Software auf Client-Seite außer einem SSH-Client.
- Funktioniert mit beliebigen Clients, ohne dass diese VPN-Software brauchen.

**Nachteile**

- Setzt eine **IPv4-Adresse** oder **DynDNS** voraus — ISPs geben häufig
  wechselnde IPs.
- Der SSH-Port ist **direkt aus dem Internet erreichbar** und wird von
  Bots/Skripten permanent gescannt. Du **musst** die VM härten (siehe
  Sicherheitswarnung oben) und Fail2ban einsetzen.
- Kein Vertraulichkeits-/Integritätsschutz auf Netzwerkebene — nur die
  SSH-Verschlüsselung selbst.

**Konfiguration**

1. **Port-Forwarding im Router einrichten** (Beispiel):

   ```
   Externe WAN-Seite:   TCP 22222 (oder ein beliebiger freier High Port)
   Interne LAN-Seite:   <IP-der-VM>:22   (z. B. 192.168.1.50)
   ```

   *Tipp:* Verwende **keinen** Standard-Port wie `22` außen — High Ports
   reduzieren die Lautstärke automatisierter Scans erheblich.

2. **Dynamische DNS (DynDNS)** falls deine öffentliche IP wechselt:
   - Anbieter wie [DuckDNS](https://www.duckdns.org/), No-IP oder die
     DynDNS-Funktion deines Routers.
   - Im Router eintragen oder per `cron`/`ddclient` regelmäßig aktualisieren.

3. **SSH-Client-Verbindung:**

   ```bash
   ssh -p 22222 vagrant@<dein-dyndns-host>.duckdns.org
   ```

4. **Zusätzliche Härtung:**

   - SSH-Key-only (siehe Sicherheitswarnung oben).
   - `sudo apt install fail2ban` und mit Filter für `sshd` aktivieren.
   - `ufw` aktivieren (`sudo ufw allow 22/tcp` lokal, im Internet nur über
     Router erreichbar).
   - Port 22 intern gerne behalten, der **äußere** Port ist ja bereits ein
     anderer.

---

## Option B: VPN-basierter Zugriff (empfohlen)

Die Idee: Statt einzelne Ports freizugeben, baust du ein **privates, virtuelles
Netz** zwischen der VM und deinen Clients auf. Die VM ist **nicht direkt** aus
dem Internet erreichbar — alle Zugriffe laufen durch das VPN.

Empfohlene Werkzeuge:

- **[WireGuard](https://www.wireguard.com/)** — leichtgewichtig, schnell,
  moderne Krypto, im Linux-Kernel enthalten.
- **[Tailscale](https://tailscale.com/)** — basiert auf WireGuard, mit
  einfacher Schlüsselverwaltung, NAT-Traversal, MagicDNS. Sehr komfortabel
  für Heimanwender.

**Vorteile**

- Die VM erscheint **nicht** in offenen Portscans — keine direkte Angriffsfläche.
- Ein einziges VPN für SSH, Web, VNC, alle weiteren Dienste.
- Bei Tailscale: kein Router-Forwarding nötig, NAT-Traversal eingebaut,
  Konfiguration über Web-UI.

**Nachteile**

- Client-Geräte müssen VPN-Software installieren und aktiviert haben.
- Bei reinem WireGuard: ein kleiner UDP-Port (z. B. `51820`) im Router muss
  trotzdem freigegeben werden, dafür genügt aber **eine** Regel.
- Bei Tailscale: Vertrauen in den Anbieter bzw. dessen Koordinationsserver
  (alternativ: Headscale, self-hosted).

**Einrichtung mit WireGuard (Kurzfassung)**

1. Auf der VM: `sudo apt install wireguard` und Schlüsselpaar erzeugen
   (`wg genkey | tee privatekey | wg pubkey > publickey`).
2. `/etc/wireguard/wg0.conf` mit privatem Schlüssel und `[Peer]`-Block
   für deine Clients anlegen, `wg-quick up wg0` und ggf. `systemctl enable
   wg-quick@wg0`.
3. Im Router **einen** UDP-Port (z. B. `51820`) auf die VM forwarden.
4. Auf jedem Client: WireGuard-Profil mit dem öffentlichen VM-Schlüssel
   importieren.
5. Verbindung mit `ssh vagrant@10.x.x.x` (WireGuard-IP) testen.

**Einrichtung mit Tailscale (Kurzfassung)**

1. Auf der VM: `curl -fsSL https://tailscale.com/install.sh | sh` und
   `sudo tailscale up`.
2. Auf dem Client: Tailscale installieren und mit demselben Account einloggen.
3. SSH direkt über die zugewiesene Tailscale-IP, z. B.
   `ssh vagrant@100.x.x.x`.

---

## Entscheidungshilfe

| Ziel | Empfehlung |
|---|---|
| Gelegentlich von unterwegs auf die laufende VM zugreifen | **Option B (Tailscale)** — am bequemsten und sichersten. |
| Mehrere Nutzer/Teams mit unterschiedlichen Rechten | **Option B (WireGuard/Tailscale)** + differenzierte Schlüssel. |
| Schnell mal für eine Demo freigeben | **Option A** mit High Port + Fail2ban, **nur** nach Härtung. |

---

## Checkliste vor dem "Live-Schalten"

- [ ] Eigener SSH-Key auf der VM, Insecure Key entfernt
- [ ] Starkes Passwort / Passwort-Login aus
- [ ] `ufw` aktiv
- [ ] Automatische Updates aktiv
- [ ] Fail2ban aktiv (mindestens bei Option A)
- [ ] code-server-Passwort geändert / Jupyter-Token gesetzt
- [ ] Backups/Snapshots der VM (für den Notfall)
- [ ] Monitoring/Logging (zumindest `journalctl -u ssh` im Auge behalten)
