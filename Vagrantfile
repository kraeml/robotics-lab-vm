# ══════════════════════════════════════════════════════════════════════
#  Robotic-Entwicklungsumgebung – Einstieg für Schüler und Lehrkräfte
# ══════════════════════════════════════════════════════════════════════
#
#  Was bekommst du hier?
#
#  Mit diesem Vagrantfile startest du mit einem einzigen Befehl eine
#  komplette Entwicklungs-VM für Robotik – ohne manuelle Installation:
#
#    * Ubuntu 24.04 Desktop (grafische Oberfläche, deutsch)
#    * ROS 2 Jazzy – das Robotik-Framework (Terminal: `ros2 …`, `colcon …`)
#    * code-server – VS Code im Browser (Port 8080)
#    * PlatformIO – Entwicklung & Flashen für Arduino/ESP32/STM32
#    * JupyterLab – Python-/ROS-Notebooks (Port 8888)
#
#  Alle Werkzeuge sind bereits fertig in der Box enthalten. Es findet
#  kein zusätzliches Provisioning statt – die VM ist nach dem Start
#  sofort einsatzbereit.
#
#  Was ist Vagrant überhaupt?
#    Vagrant steuert VirtualBox-VMs aus einer einzigen, textbasierten
#    Datei (dieser Datei!). Eine „Box" ist ein vorgefertigtes VM-Image –
#    hier: Ubuntu 24.04 Desktop inkl. ROS 2 und allen Robotik-Werkzeugen.
#
#  Voraussetzungen auf dem Rechner:
#    * VirtualBox  (https://www.virtualbox.org)
#    * Vagrant     (https://www.vagrantup.com)
#    * Optional: VirtualBox Extension Pack (gleiche Version!) – nur für
#      USB-Durchreichung (Arduino & Co. flashen), nicht für den Start
#
#  So startest du:
#    1. Dieses Repo klonen
#    2.  vagrant up   (erster Start: einige Minuten Geduld für den
#        Download der Box und vergrößern der Festplatte)
#    3. Die Zugangsdaten stehen bei jedem Start am Ende der Konsolenausgabe
# ══════════════════════════════════════════════════════════════════════

# ── VM-Name ────────────────────────────────────────────────────────────────
# Name der VM in VirtualBox (sichtbar im VirtualBox-Manager) und Basis für
# den Hostnamen innerhalb der VM. Zentral definiert, damit er nicht mehrfach
# im Vagrantfile verstreut gepflegt werden muss (z. B. bei vb.name weiter
# unten).
VM_NAME = "robotics"

# ── Disk-Größe ──────────────────────────────────────────────────────────────
# pi-gen-Image-Builds brauchen 20–40 GB zusätzlich zur Box-Grundlast
# (~18 GB belegt). Vagrant vergrößert die Box-Disk (VMDK) beim ersten
# vagrant up nach dieser Änderung automatisch auf die angegebene Größe –
# intern per VMDK→VDI→Resize→VMDK, einmalig; der Host belegt Platz erst
# bei Nutzung. Der disk-resize-Provisioner unten lässt Partition und
# Dateisystem nachwachsen.
#
# Default: 64 GB. Überschreibbar per Umgebungsvariable, z. B. für Hosts
# mit wenig Plattenplatz oder abweichende Anwendungsfälle:
#   DISK_SIZE=100GB vagrant up
DISK_SIZE = ENV.fetch("DISK_SIZE", "64GB")
unless DISK_SIZE =~ /\A[1-9]\d*[GM]B\z/
  raise "Ungültiges DISK_SIZE-Format: '#{DISK_SIZE}' (erwartet z. B. '64GB' oder '512MB')"
end

# ── Vagrant-Mindestversion ─────────────────────────────────────────────────
# `config.vm.disk` (native Disk-Größenanpassung, siehe unten) wird erst ab
# Vagrant 2.2.0 unterstützt. Bei älteren Versionen bricht Vagrant sonst mit
# einer schwer verständlichen Fehlermeldung ab oder ignoriert die Einstellung
# stillschweigend. Diese Prüfung sorgt für einen klaren, frühen Fehlerhinweis.
Vagrant.require_version ">= 2.2.0"

Vagrant.configure("2") do |config|

  # ── Die Box ────────────────────────────────────────────────────────────────
  # Feste Bezugsquelle: Produktion (Hetzner Object Storage). Keine
  # Umgebungsvariablen nötig – funktioniert so auf jedem Rechner.
  config.vm.box     = "ubuntu-2404-desktop"
  config.vm.box_url = "https://hel1.your-objectstorage.com/kraeml-bayern/ubuntu-2404-desktop/metadata.json"

  config.vm.hostname = VM_NAME

  # ── Netzwerk ───────────────────────────────────────────────────────────────
  # Privates Netzwerk (Host-Only): der Host erreicht die VM direkt unter
  # 192.168.33.10 – das ist die Adresse für die Browser-Werkzeuge.
  config.vm.network "private_network", ip: "192.168.33.10"

  # Browser-Werkzeuge (in der Box enthalten):
  #   code-server:  http://192.168.33.10:8080  (Passwort "change_me" → ändern!)
  #   JupyterLab:   http://192.168.33.10:8888  (ohne Passwort – nur Heimnetz!)
  # Über das Host-Only-Netz sind sie so direkt erreichbar. Wer lieber über
  # localhost zugreifen will, kommentiert die NAT-Weiterleitungen ein:
  # config.vm.network :forwarded_port, guest: 8080, host: 8080, auto_correct: true
  # config.vm.network :forwarded_port, guest: 8888, host: 8888, auto_correct: true

  # Bridged Netzwerk: nur nötig, wenn die VM selbst im Heimnetz sichtbar
  # sein soll (z. B. echte Roboter mit ROS-Topics über Netzwerk erreichen):
  # config.vm.network "public_network", bridge: "enp3s0"

  # ── Disk-Größe ─────────────────────────────────────────────────────────────
  # Details, Begründung und ENV-Override siehe DISK_SIZE-Definition und
  # Kommentar im Datei-Header oben. Validierung des Formats erfolgt dort
  # ebenfalls – hier wird der geprüfte Wert nur noch verwendet.
  config.vm.disk :disk, size: DISK_SIZE, primary: true

  # ── Disk-Resize-Provisioner ────────────────────────────────────────────────
  # Passt Partition und Dateisystem an die von Vagrant vergrößerte Disk an
  # (siehe config.vm.disk oben). Idempotent und bei jedem Start aktiv
  # (run: "always") – schlägt nichts fehl, wenn nichts zu tun ist. Zwei
  # Zweige: LVM (zukünftige Boxen mit Standard-Server-Layout) und die hier
  # genutzte Variante mit direkter Root-Partition (ext4).
  config.vm.provision "shell", name: "disk-resize", run: "always", inline: <<~SHELL
    set -euo pipefail

    echo "==> Aktuelle Disk-Belegung:"
    lsblk -o NAME,SIZE,FSSIZE /dev/sda || true

    ROOT_SOURCE="$(findmnt -no SOURCE /)"
    echo "==> Root-Dateisystem: ${ROOT_SOURCE}"

    if vgs ubuntu-vg >/dev/null 2>&1; then
      FREE_MiB=$(vgs --noheadings -o vg_free --units m --nosuffix ubuntu-vg \
                 | tr -d ' ' | cut -d'.' -f1)
      echo "==> Freier VG-Platz: ${FREE_MiB} MiB"

      if [ "${FREE_MiB}" -le 0 ]; then
        echo "==> LV bereits voll erweitert – überspringe."
      else
        echo "==> Erweitere Logical Volume..."
        lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv

        echo "==> Erweitere Filesystem..."
        resize2fs /dev/ubuntu-vg/ubuntu-lv
      fi
    elif [ -b "${ROOT_SOURCE}" ]; then
      if ! command -v growpart >/dev/null 2>&1; then
        echo "==> growpart nicht gefunden – Partition wird nicht erweitert."
      else
        PART_NUM="${ROOT_SOURCE##*[!0-9]}"
        BASE_DEV="${ROOT_SOURCE%"${PART_NUM}"}"
        BASE_DEV="${BASE_DEV%p}"
        echo "==> Erweitere Partition ${PART_NUM} auf ${BASE_DEV}..."
        set +e
        GROW_OUT="$(growpart "${BASE_DEV}" "${PART_NUM}" 2>&1)"
        GROW_RC=$?
        set -e
        # rc 0 = vergrößert, rc 1 = NOCHANGE (bereits voll erweitert)
        if [ "${GROW_RC}" -le 1 ]; then
          echo "==> ${GROW_OUT}"
        else
          echo "==> WARNUNG: growpart fehlgeschlagen – fahre mit resize2fs fort."
          echo "${GROW_OUT}"
        fi
      fi

      echo "==> Erweitere Filesystem..."
      if ! resize2fs "${ROOT_SOURCE}"; then
        echo "==> WARNUNG: resize2fs fehlgeschlagen – Dateisystem evtl. unverändert."
      fi
    else
      echo "==> Weder LVM-VG noch direkt erweiterbare Root-Partition erkannt – überspringe."
    fi

    echo "==> Fertig – neue Größe:"
    df -h /
  SHELL

  # ── VirtualBox-Einstellungen ───────────────────────────────────────────────
  config.vm.provider "virtualbox" do |vb|
    vb.name = VM_NAME
    vb.gui  = true   # Desktop-Oberfläche im Fenster anzeigen

    # Ressourcen – Robotik-Software (Gazebo, RViz) ist hungrig.
    # Weniger RAM/CPU im Host? Hier anpassen.
    vb.memory = 8192
    vb.cpus   = 4

    # 3D-Beschleunigung – wichtig für RViz, Gazebo und Simulatoren
    vb.customize ["modifyvm", :id, "--vram", "128"]
    vb.customize ["modifyvm", :id, "--accelerate3d", "on"]
    vb.customize ["modifyvm", :id, "--graphicscontroller", "vmsvga"]

    # Copy & Paste sowie Drag & Drop zwischen Host und VM
    vb.customize ["modifyvm", :id, "--clipboard-mode", "bidirectional"]
    vb.customize ["modifyvm", :id, "--draganddrop", "bidirectional"]

    # USB-Controller aktivieren – zum Flashen von Boards (Arduino, ESP32,
    # ST-Link) die Geräte damit in die VM durchreichen. USB 2.0/3.0 erfordern
    # das VirtualBox Extension Pack in derselben Version wie VirtualBox!
    vb.customize ["modifyvm", :id, "--usb", "on"]
    vb.customize ["modifyvm", :id, "--usbehci", "on"]
    vb.customize ["modifyvm", :id, "--usbxhci", "on"]

    # Remote Desktop aus – der Desktop läuft lokal im Fenster (vb.gui)
    vb.customize ["modifyvm", :id, "--vrde", "off"]
  end

  # ── Geteilte Ordner ────────────────────────────────────────────────────────
  # Der Ordner mit diesem Vagrantfile erscheint in der VM als /vagrant.
  # Nach Repo-Updates auf dem Host einmal `vagrant reload` ausführen,
  # damit Änderungen am Vagrantfile wirksam werden.
  config.vm.synced_folder ".", "/vagrant"

  # Arbeitsbereich der Teilnehmer: eigene ROS-Projekte und eigene
  # Notebooks. Der Inhalt bleibt auf dem Host-Rechner erhalten (Ordner
  # "workspace" im Repo-Verzeichnis) und ist per .gitignore vollständig
  # vom Git ausgeschlossen – Teilnehmer-Änderungen landen nicht im
  # Repository. Unterrichtsmaterial (notebooks/, examples/) wird dagegen
  # über Git verteilt und in der VM als Kopie nach ~/notebooks geholt:
  #   cp -a /vagrant/notebooks/. ~/notebooks/
  config.vm.synced_folder "./workspace", "/home/vagrant/workspace"

  # ── Zugangsdaten bei jedem Start anzeigen ──────────────────────────────────
  # run: "always" → läuft bei JEDEM vagrant up / reload / resume, nicht nur
  # beim ersten Start.
  config.vm.provision "info", type: "shell", run: "always", inline: <<-SHELL
    echo ""
    echo "╔═══════════════════════════════════════════════════════════╗"
    echo "║   Robotic-Entwicklungsumgebung – Zugriff im Browser       ║"
    echo "╠═══════════════════════════════════════════════════════════╣"
    echo "║   code-server (VS Code im Browser)                        ║"
    echo "║     Adresse:  http://192.168.33.10:8080                   ║"
    echo "║     Passwort: change_me                                   ║"
    echo "║                                                           ║"
    echo "║   JupyterLab (Python-/ROS-Notebooks)                      ║"
    echo "║     Adresse:  http://192.168.33.10:8888                   ║"
    echo "║     Login:    ohne Passwort (nur im Heimnetz nutzen!)     ║"
    echo "║                                                           ║"
    echo "║   Terminal:   vagrant ssh (oder Terminal in code-server)  ║"
    echo "║                                                           ║"
    echo "║   Arbeiten:   ~/workspace · Material: ~/notebooks (Kopie) ║"
    echo "╚═══════════════════════════════════════════════════════════╝"
  SHELL

  # ══════════════════════════════════════════════════════════════════════
  #  Erste Schritte in der Robotik (im code-server-Terminal ausprobieren)
  # ══════════════════════════════════════════════════════════════════════
  #
  #  ROS 2 Jazzy ist bereits aktiviert (Terminal öffnet mit ROS-Umgebung):
  #    ros2 topic list                 # laufende ROS-Themen anzeigen
  #    ros2 run demo_nodes_cpp talker  # Demo-Publisher starten
  #    ros2 topic echo /chatter        # Nachrichten live mitlesen
  #
  #  Eigenes ROS-Paket bauen (colcon ist das Build-Werkzeug), Ordner:
  #    mkdir -p ~/workspace/src && cd ~/workspace/src
  #    ros2 pkg create mein_paket --build-type ament_python
  #    cd ~/workspace && colcon build && source install/setup.bash
  #
  #  Mikrocontroller (Arduino/ESP32) mit PlatformIO:
  #    – in code-server: Seitenleiste → PlatformIO-Icon → New Project
  #    – oder im Terminal:  pio init --board uno && pio run
  #    – Board-Toolchains lädt PlatformIO beim ersten Build selbst
  #    – Flashen: Board per USB durchreichen (Extension Pack, siehe oben)
  #
  #  Merksätze:
  #    vagrant up        → VM starten (Zugangsdaten erscheinen am Ende)
  #    vagrant ssh       → Kommandozeile in der VM
  #    vagrant suspend   → Zustand einfrieren (schnell fortsetzen)
  #    vagrant halt      → VM herunterfahren
  #    vagrant destroy   → VM vollständig löschen (Box bleibt erhalten)
  #
  # ══════════════════════════════════════════════════════════════════════

  # ── Sicherheit ─────────────────────────────────────────────────────────────
  # Diese Konfiguration ist für das heimische, vertrauenswürdige Netz
  # gedacht. code-server nutzt das Passwort "change_me" – bitte ändern.
  # JupyterLab läuft ohne Token. Vor jeder Form von Internet-Exposition:
  # INTERNET-ACCESS.md im Repo lesen!
end
