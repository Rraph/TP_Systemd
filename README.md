# TP systemd !

## First steps

Version de systemd:

     systemd --version
     systemd 243 (v243.4-1.fc31)

PID de systemd:  

     pidof systemd
     932 1

## Gestion du temps
- Différence entre Local Time, Universal Time et RTC Time:  

> Local Time : temps ou heure correspondant à la machine.
> Universal Time : temps correspondant à UTC+0
> RTC Time pour, Real Time Clock, qui continue de tourner pour être toujours à l'heure quand la machine s'éteint.
> La pertinence du RTC Time est comme dit au-dessus.

Changer de timezone :

    timedatectl set-timezon Europe/London

Pour Désactiver le service de synchronisation du temps :

> timedatectlset-ntp false

  
    Local time: ven. 2019-11-29 11:55:17 CET
               Universal time: ven. 2019-11-29 10:55:17 UTC
                     RTC time: ven. 2019-11-29 10:55:17
                    Time zone: Europe/London (CET, +0000)
    System clock synchronized: no
                  NTP service: inactive
              RTC in local TZ: no

## Gestion de noms

Changer le nom du hostname : 

> hostname set-hostname OpenSource

    [admin@localhost ~]$ hostnamectl
       Static hostname: OpenSource
             Icon name: computer-vm
               Chassis: vm
            Machine ID: 3b9e350331da4cf3b9f257135d1f113e
               Boot ID: ef48f24669a546379e24bb584f34fb0a
        Virtualization: oracle
      Operating System: Fedora 31 (Server Edition)
           CPE OS Name: cpe:/o:fedoraproject:fedora:31
                Kernel: Linux 5.3.7-301.fc31.x86_64
          Architecture: x86-64

Différence entre **--pretty** **--static** et **--transient** :

> Le hostname static est celui qui de base qui peut être changé par l'utilisateur avec la commande set-hostname. 
> Le hostname pretty est un host libre pour l'user.
> Le hostname transient est un nom d'hote dynamique et géré par le kernel.

On utilise de préference le hostname **static** en production.

# 4. Gestion du réseau (et résolutions de noms)

Afficher les informations DHCP:
> nmcli con show Connexion\ filaire\ 1

        DHCP4.OPTION[1]:                        dhcp_lease_time = 86400
    DHCP4.OPTION[2]:                        dhcp_rebinding_time = 75600
    DHCP4.OPTION[3]:                        dhcp_renewal_time = 43200
    DHCP4.OPTION[4]:                        dhcp_server_identifier = 10.0.3.2
    DHCP4.OPTION[5]:                        domain_name = auvence.co
    DHCP4.OPTION[6]:                        domain_name_servers = 10.33.10.20 10.33>
    DHCP4.OPTION[7]:                        expiry = 1575119389
    DHCP4.OPTION[8]:                        ip_address = 10.0.3.15
    DHCP4.OPTION[9]:                        next_server = 10.0.3.4
    DHCP4.OPTION[10]:                       requested_broadcast_address = 1
    DHCP4.OPTION[11]:                       requested_dhcp_server_identifier = 1
    DHCP4.OPTION[12]:                       requested_domain_name = 1
    DHCP4.OPTION[13]:                       requested_domain_name_servers = 1
    DHCP4.OPTION[14]:                       requested_domain_search = 1
    DHCP4.OPTION[15]:                       requested_host_name = 1
    DHCP4.OPTION[16]:                       requested_interface_mtu = 1
    DHCP4.OPTION[17]:                       requested_ms_classless_static_routes = 1
    DHCP4.OPTION[18]:                       requested_nis_domain = 1
    DHCP4.OPTION[19]:                       requested_nis_servers = 1
    DHCP4.OPTION[20]:                       requested_ntp_servers = 1
    DHCP4.OPTION[21]:                       requested_rfc3442_classless_static_rout>
    DHCP4.OPTION[22]:                       requested_root_path = 1
    DHCP4.OPTION[23]:                       requested_routers = 1
    DHCP4.OPTION[24]:                       requested_static_routes = 1
    DHCP4.OPTION[25]:                       requested_subnet_mask = 1
    DHCP4.OPTION[26]:                       requested_time_offset = 1
    DHCP4.OPTION[27]:                       requested_wpad = 1
    DHCP4.OPTION[28]:                       routers = 10.0.3.2
    DHCP4.OPTION[29]:                       subnet_mask = 255.255.255.0

- systemd-networkd

Stopper et désactiver le démarrage de NetwokManager : 

> systemctl stop NetwokManage.service
>systemctl disable NetwokManage.service

    ==== AUTHENTICATION COMPLETE ====
    Removed /etc/systemd/system/multi-user.target.wants/NetworkManager.service.
    Removed /etc/systemd/system/dbus-org.freedesktop.nm-dispatcher.service.
    Removed /etc/systemd/system/network-online.target.wants/NetworkManager-wait-online.service.
    
    Démarrer et activer le démarrage de systemd-networkd:
    systemctl start systemd-networkd.service
    systemctl enable systemd-networkd.service
    
    ==== AUTHENTICATION COMPLETE ====
    Created symlink /etc/systemd/system/dbus-org.freedesktop.network1.service → /usr/lib/systemd/system/systemd-networkd.service.
    Created symlink /etc/systemd/system/multi-user.target.wants/systemd-networkd.service → /usr/lib/systemd/system/systemd-networkd.service.
    Created symlink /etc/systemd/system/sockets.target.wants/systemd-networkd.socket → /usr/lib/systemd/system/systemd-networkd.socket.
    Created symlink /etc/systemd/system/network-online.target.wants/systemd-networkd-wait-online.service → /usr/lib/systemd/system/systemd-networkd-wait-online.service.

Editer la configuration d'une carte réseau :
> ip a show enp0s3

Avant:

    2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
        link/ether 08:00:27:0f:33:b9 brd ff:ff:ff:ff:ff:ff
        inet 10.0.2.15/24 brd 10.0.2.255 scope global enp0s3
           valid_lft forever preferred_lft forever
Après :

    2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
        link/ether 08:00:27:0f:33:b9 brd ff:ff:ff:ff:ff:ff
        inet 10.0.2.16/24 brd 10.0.2.255 scope global enp0s3
           valid_lft forever preferred_lft forev

- systemd-resolved

Activer la résolution des noms avec systemd-resolved :

> sudo systemctl start systemd-resolved.service
> sudo systemctl enable systemd-resolved.service

> systemctl status systemd-resolved.service

    ● systemd-resolved.service - Network Name Resolution
       Loaded: loaded (/usr/lib/systemd/system/systemd-resolved.service; enabled; v>
       Active: active (running) since Fri 2019-11-29 16:21:32 CET; 1s ago
         Docs: man:systemd-resolved.service(8)
               https://www.freedesktop.org/wiki/Software/systemd/resolved
               https://www.freedesktop.org/wiki/Software/systemd/writing-network-co>
               https://www.freedesktop.org/wiki/Software/systemd/writing-resolver-c>
     Main PID: 1559 (systemd-resolve)
       Status: "Processing requests..."
        Tasks: 1 (limit: 2341)
       Memory: 6.4M
          CPU: 733ms
       CGroup: /system.slice/systemd-resolved.service
               └─1559 /usr/lib/systemd/systemd-resolved
          

         nov. 29 16:21:31 OpenSource systemd[1]: Starting Network Name Resolution...
    nov. 29 16:21:32 OpenSource systemd-resolved[1559]: Positive Trust Anchors:
    nov. 29 16:21:32 OpenSource systemd-resolved[1559]: . IN DS 19036 8 2 49aac11d7>
    nov. 29 16:21:32 OpenSource systemd-resolved[1559]: . IN DS 20326 8 2 e06d44b80>
    nov. 29 16:21:32 OpenSource systemd-resolved[1559]: Negative trust anchors: 10.>
    nov. 29 16:21:32 OpenSource systemd-resolved[1559]: Using system hostname 'Open>
    nov. 29 16:21:32 OpenSource systemd[1]: Started Network Name Resolution.


# 5. Gestions de sessions loginid

> loginctl

    SESSION  UID USER SEAT  TTY
          1 1000 admin  seat0 tty1
          3 1000 admin      pts/0
          4 1000 admin
    
    3 sessions listed.

# 6. Gestion d'unités basiques (services)

Trouver l'unité associée au processus chronyd :
> sudo cat /var/run/chrony/chronyd.pid

    2080

> systemctl status 2080

    ● session-3.scope - Session 3 of user admin
       Loaded: loaded (/run/systemd/transient/session-3.scope; transient)
    Transient: yes
       Active: active (running) since Thu 2019-12-05 10:05:23 CET; 2h 31min ago
        Tasks: 6
       Memory: 37.7M
          CPU: 1min 8.760s
       CGroup: /user.slice/user-1000.slice/session-3.scope
               ├─ 916 sshd: admin [priv]
               ├─ 922 sshd: admin@pts/0
               ├─ 923 -bash
               ├─2080 chronyd
               ├─2148 systemctl status 2080
               └─2149 less

## II - Boot et logs :

Générer un graphe de la séquence de boot :

> cd /home/admin/Desktop
> systemd-analyze plot > graphe.svg

    cat graphe.svg | grep "sshd.service"
      <text class="left" x="2756.568" y="4694.000">sshd.service (101ms)</text>

sshd.service boot en 101ms.

## III - Mécanismes manipulés par systemd

### 1. cgroups
Identifier le cgroup utilisé par votre session SSH.
> ps -e -o pid,cmd,cgroup

       1367 sshd: admin [priv]            0::/user.slice/user-1000.slice/session-21.scope
       1371 sshd: admin [priv]            0::/user.slice/user-1000.slice/session-22.scope
       1372 sshd: admin@pts/0             0::/user.slice/user-1000.slice/session-21.scope
       1376 sshd: admin@notty             0::/user.slice/user-1000.slice/session-22.scope

> cat /proc/1367/cgroup

    0::/user.slice/user-1000.slice/session-21.scope

Modifier la RAM dediée à ma session utilisateur :

    [admin@OpenSource ~]$ sudo systemctl set-property user-1000.slice MemoryMax=510M
    [admin@OpenSource ~]$ cat /sys/fs/cgroup/user.slice/user-1000.slice/memory.max
    534773760
    [admin@OpenSource ~]$ sudo systemctl set-property user-1000.slice MemoryMax=512M
    [admin@OpenSource ~]$ cat /sys/fs/cgroup/user.slice/user-1000.slice/memory.max
    536870912

Vérification de la création du fichier dans le chemin /etc/systemd/systemd.control/ :
> sudo cat /etc/systemd/system.control/user-1000.slice.d/50-MemoryMax.conf

    # This is a drop-in unit file extension, created via "systemctl set-property"
    # or an equivalent operation. Do not edit.
    [Slice]
    MemoryMax=536870912

### 2. D-Bus
Récupérer la valeur d'une propriété d'un objet D-Bus :
> sudo dbus-monitor --system

    signal time=1576057237.295059 sender=org.freedesktop.DBus -> destination=:1.53 serial=4294967295 path=/org/freedesktop/DBus; interface=org.freedesktop.DBus; member=NameAcquired
       string ":1.53"

## IV. systemd units in-depth

sudo systemctl status auditd.service 

    ● auditd.service - Security Auditing Service
       Loaded: loaded (/usr/lib/systemd/system/auditd.service; enabled; vendor preset: enabled)
       Active: active (running) since Wed 2019-12-11 08:49:09 UTC; 1h 0min ago
         Docs: man:auditd(8)
               https://github.com/linux-audit/audit-documentation
      Process: 427 ExecStart=/sbin/auditd (code=exited, status=0/SUCCESS)
      Process: 432 ExecStartPost=/sbin/augenrules --load (code=exited, status=0/SUCCESS)
     Main PID: 429 (auditd)
        Tasks: 2 (limit: 1146)
       Memory: 4.8M
          CPU: 83ms
       CGroup: /system.slice/auditd.service
               └─429 /sbin/auditd

sudo systemctl cat auditd.service

    # /usr/lib/systemd/system/auditd.service
    [Unit]
    Description=Security Auditing Service
    DefaultDependencies=no
    ## If auditd is sending or recieving remote logging, copy this file to
    ## /etc/systemd/system/auditd.service and comment out the first After and
    ## uncomment the second so that network-online.target is part of After.
    ## then comment the first Before and uncomment the second Before to remove
    ## sysinit.target from "Before".
    After=local-fs.target systemd-tmpfiles-setup.service
    ##After=network-online.target local-fs.target systemd-tmpfiles-setup.service
    Before=sysinit.target shutdown.target
    ##Before=shutdown.target
    Conflicts=shutdown.target
    RefuseManualStop=yes
    ConditionKernelCommandLine=!audit=0
    Documentation=man:auditd(8) https://github.com/linux-audit/audit-documentation
    
    [Service]
    Type=forking
    PIDFile=/run/auditd.pid
    ExecStart=/sbin/auditd
    ## To not use augenrules, copy this file to /etc/systemd/system/auditd.service
    ## and comment/delete the next line and uncomment the auditctl line.
    ## NOTE: augenrules expect any rules to be added to /etc/audit/rules.d/
    ExecStartPost=-/sbin/augenrules --load
    #ExecStartPost=-/sbin/auditctl -R /etc/audit/audit.rules
    # By default we don't clear the rules on exit. To enable this, uncomment
    # the next line after copying the file to /etc/systemd/system/auditd.service
    #ExecStopPost=/sbin/auditctl -R /etc/audit/audit-stop.rules
    
    ### Security Settings ###
    MemoryDenyWriteExecute=true
    LockPersonality=true
    ProtectControlGroups=true
    ProtectKernelModules=true
    
    [Install]
    WantedBy=multi-user.target
    
sudo touch /etc/systemd/system/test.service
sudo nano /etc/systemd/system/test.service

    [Unit]
    Description=Le service de test
    After=network.target
    StartLimitIntervalSec=0
    [Service]
    Type=simple
    Restart=always
    RestartSec=1
    User=vagrant
    ExecStart=la commande pour lancer le serveur web 
    ExecStartPre=/usr/bin/firewall-cmd --add-port 80/tcp`
    ExecStopPost=/usr/bin/firewall-cmd --remove-port 80/tcp`
    
    [Install]
    WantedBy=multi-user.target

sudo systemctl enable test.service

    Created symlink /etc/systemd/system/multi-user.target.wants/test.service → /etc/systemd/system/test.service.

cette commande créer un liens symbolique entre le service et les services a lancer au démarrage.

yum install docker

    # Création d'un fichier .service
    $ sudo vim /etc/systemd/system/docker.service
    # Création d'un fichier .socket correspondant
    $ sudo vim /etc/systemd/system/docker.socket
    # Syntaxe du fichier
    $ cat /etc/systemd/system/dock.socket
    [Unit]
    Description=docker socket
    [Socket]
    ListenStream=0.0.0.0:80
    ExecStartPre=/usr/bin/firewall-cmd --add-port 80/tcp
    ExecStartPre=/usr/bin/firewall-cmd --add-port 80/tcp --permanent
    ExecStopPost=/usr/bin/firewall-cmd --remove-port 80/tcp
    ExecStopPost=/usr/bin/firewall-cmd --remove-port 80/tcp --permanent
    [Install]
    WantedBy=sockets.target