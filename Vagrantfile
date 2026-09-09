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
#    1. Dieses Repo klonen (oder als ZIP herunterladen und entpacken)
#    2.  vagrant up   (erster Start: einige Minuten Geduld für den
#        Download der Box)
#    3. Die Zugangsdaten stehen bei jedem Start am Ende der Konsolenausgabe
# ══════════════════════════════════════════════════════════════════════

VM_NAME = "robotics"

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
  # Dateien, die du dort ablegst, sind auch in der VM sichtbar (und
  # umgekehrt) – praktisch, um z. B. eigene ROS-Pakete zu sichern.
  config.vm.synced_folder ".", "/vagrant"

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
  #  Eigenes ROS-Paket bauen (colcon ist das Build-Werkzeug):
  #    mkdir -p ~/ros2_ws/src && cd ~/ros2_ws/src
  #    ros2 pkg create mein_paket --build-type ament_python
  #    cd ~/ros2_ws && colcon build && source install/setup.bash
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
