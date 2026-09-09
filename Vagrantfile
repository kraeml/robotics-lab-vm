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
#    * JupyterLab – Python-/ROS-Notebooks (Port 8888; ab Box 26.09.8
#      enthalten, bei älteren Boxen beim ersten Start nachinstalliert)
#
#  Was ist Vagrant überhaupt?
#    Vagrant steuert VirtualBox-VMs aus einer einzigen, textbasierten
#    Datei (dieser Datei!). Eine „Box" ist ein vorgefertigtes VM-Image –
#    hier: Ubuntu 24.04 Desktop inkl. ROS 2. „Provisioning" heißt:
#    nach dem ersten Start richtet ein Automatisierungswerkzeug (Ansible)
#    Software in der VM ein – bei uns wird JupyterLab sichergestellt.
#
#  Voraussetzungen auf dem Rechner:
#    * VirtualBox  (https://www.virtualbox.org)
#    * Vagrant     (https://www.vagrantup.com)
#    * Optional: VirtualBox Extension Pack (gleiche Version!) – nur für
#      USB-Durchreichung (Arduino & Co. flashen), nicht für den Start
#    * Kein Ansible nötig – das steckt in der Box bzw. wird bei älteren
#      Boxen beim ersten Start IN der VM installiert
#
#  So startest du:
#    1. Dieses Repo klonen (oder als ZIP herunterladen und entpacken)
#    2.  cd vagrant && vagrant up   (erster Start: einige Minuten Geduld)
#    3. Die Zugangsdaten stehen bei jedem Start am Ende der Konsolenausgabe
#
#  Achtung: Diese Datei MUSS im Unterordner vagrant/ liegen, weil sie das
#  übergeordnete Repo in die VM einmountet (siehe Verzeichnisstruktur).
# ══════════════════════════════════════════════════════════════════════

# ── Verzeichnisstruktur ──────────────────────────────────────────────────────
# Host:
#   <repo>/                     ← Repo-Root (HOST_WORKSPACE): site.yml, roles/,
#                                 group_vars/ – hieraus entsteht die Box
#   <repo>/vagrant/Vagrantfile  ← diese Datei (Bestandteil des Repos)
#
# Guest (VM):
#   /home/vagrant/workspace     ← Mount des Repo-Roots
#   /home/vagrant/workspace/site.yml      ← Playbook fürs Provisioning
#   /home/vagrant/workspace/roles/        ← Ansible-Rollen
# ─────────────────────────────────────────────────────────────────────────────
HOST_WORKSPACE        = File.expand_path("..", __dir__)  # Repo-Root (eine Ebene höher)
GUEST_WORKSPACE       = "/home/vagrant/workspace"
VM_NAME               = "robotics"

# Playbook/Provisioning-Pfad per Umgebungsvariable überschreibbar
# (Defaults reichen völlig – wer will, kann z. B. ROS_PLAYBOOK=… setzen)
ROS_PROVISIONING_PATH = ENV.fetch("ROS_PROVISIONING_PATH", GUEST_WORKSPACE)
ROS_PLAYBOOK          = ENV.fetch("ROS_PLAYBOOK", "site.yml")

# ── Dynamische Inventar-Generierung ──────────────────────────────────────────
# Ansible braucht ein „Inventar" (Liste der Zielsysteme). Weil das
# Provisioning IN der VM läuft (ansible_local), ist das Ziel die VM
# selbst: ansible_connection=local. Die Datei wird bei jedem Vagrant-
# Start neu geschrieben – bitte nicht von Hand editieren!
VAGRANT_INVENTORY_FILE = File.expand_path("hosts.ini", __dir__)
begin
  File.write(VAGRANT_INVENTORY_FILE,
    "# Automatisch generiert vom Vagrantfile – nicht editieren!\n" \
    "[vagrant_vm]\n" \
    "#{VM_NAME} ansible_connection=local ansible_python_interpreter=/usr/bin/python3\n" \
    "\n" \
    "# Parent-Gruppe: lädt group_vars/ubuntu2404.yml (Ubuntu-Version der Box)\n" \
    "[ubuntu2404:children]\n" \
    "vagrant_vm\n")
rescue Errno::ENOENT => e
  warn "WARN: hosts.ini konnte nicht geschrieben werden: #{e.message}"
end

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
  # Standard: der Ordner mit diesem Vagrantfile erscheint in der VM als
  # /vagrant. Zusätzlich wird der Repo-Root nach /home/vagrant/workspace
  # gemountet – dort liegen Playbook, Rollen und Inventar fürs Provisioning.
  config.vm.synced_folder ".", "/vagrant"
  config.vm.synced_folder HOST_WORKSPACE, GUEST_WORKSPACE

  # ── Provisioning: JupyterLab aktivieren (interner Ansible-Provisioner) ─────
  # ansible_local installiert Ansible per apt IN der VM (install_mode:
  # "default") – ab Box 26.09.8 steckt Ansible bereits in der Box, dann
  # überspringt sich der Install-Schritt selbst. Version nicht gepinnt –
  # apt liefert die Ubuntu-Paketversion.
  # Für gepinnte Version: install_mode: "pip" + ansible.version = "2.17.0".
  #
  # Das Playbook (site.yml) und die Rollen kommen über den Workspace-Mount.
  # Deshalb läuft dieses Vagrantfile nur in einem Checkout dieses Repos!
  # Das Provisioning ist idempotent: erneutes `vagrant provision` ist
  # gefahrlos, es wird nur ausgeführt, was sich geändert hat. Beim ERSTEN
  # Start dauert es einige Minuten (idempotenter Durchlauf, bei älteren
  # Boxen zusätzlich JupyterLab via pip).
  config.vm.provision "ansible_local" do |ansible|
    ansible.install           = true
    ansible.install_mode      = "default"
    ansible.provisioning_path = ROS_PROVISIONING_PATH
    ansible.playbook          = ROS_PLAYBOOK
    ansible.inventory_path    = "vagrant/hosts.ini"  # siehe oben, generiert
    ansible.limit             = "all"
    ansible.skip_tags         = ["cleanup", "additions"]
  end

  # ── Zugangsdaten bei jedem Start anzeigen ──────────────────────────────────
  # run: "always" → läuft bei JEDEM vagrant up / reload / resume, nicht nur
  # beim ersten Start. Beim allerersten Start ist JupyterLab zu diesem
  # Zeitpunkt noch dabei, sich zu installieren – kurz gedulden!
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
  #    vagrant provision → Provisioning erneut ausführen (idempotent)
  #
  # ══════════════════════════════════════════════════════════════════════

  # ── Sicherheit ─────────────────────────────────────────────────────────────
  # Diese Konfiguration ist für das heimische, vertrauenswürdige Netz
  # gedacht. code-server nutzt das Passwort "change_me" – bitte ändern
  # (z. B. über das Provisioning: extra_vars robot_codeserver_password).
  # JupyterLab läuft ohne Token. Vor jeder Form von Internet-Exposition:
  # INTERNET-ACCESS.md im Repo lesen!
end
