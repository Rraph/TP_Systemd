# TP_Systemd

FIRST STEPS

Check la version de systemd:
systemd --version
systemd 243 (v243.4-1.fc31)


Check le PID de systemd:
pidof systemd
932 1



GESTION DU TEMPS

Différence entre Local Time, Universal Time et RTC Time:
Local Time, c'est le temps ou l'heure correspondant à ma machine.
Universal Time, c'est le temps correspondant au méridien de Greenwitch 
RTC Time pour, Real Time Clock, qui continue de tourner pour être toujours à l'heure quand la machine s'éteint.

Expliquer dans quel cas il peut être pertinent d'utiliser le RTC Time:
La pertinence du RTC Time est comme dit au-dessus, garder une machine à l'heure bien qu'elle soit éteinte ou en pause.


Changer de timezone:
timedatectl set-timezon Europe/Zurich

Désactiver le service de synchronisation du temps:
timedatectlset-ntp false
Local time: ven. 2019-11-29 11:55:17 CET
           Universal time: ven. 2019-11-29 10:55:17 UTC
                 RTC time: ven. 2019-11-29 10:55:17
                Time zone: Europe/Zurich (CET, +0100)
System clock synchronized: no
              NTP service: inactive
          RTC in local TZ: no



GESTION DE NOMS

Changer le nom du hostname:
hostname set-hostname OpenSource
[leo@localhost ~]$ hostnamectl
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

Différence entre --pretty --static et --transient:
Le hostname static est celui qui est utilisé de base et qui peut être changé par l'utilisateur..
Le hostname pretty est un host libre pour l'utilisateur.
Le hostname transient est un nom d'hote dynamique et gérer par le kernel.

On utilisera donc le hostname static en production.


GESTION DU RESEAU (ET RESOLUTIONS DE NOMS)


Affciher les informations DHCP:
nmcli con show Connexion\ filaire\ 1
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

Stopper et désactiver le démarrage de NetwokManager:
systemctl stop NetwokManage.service
systemctl disable NetwokManage.service

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

Editer la configuration d'une carte réseau:
ip a show enp0s3
AVANT:
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:0f:33:b9 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global enp0s3
       valid_lft forever preferred_lft forever

APRES:
ip a show enp0s3
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:0f:33:b9 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.16/24 brd 10.0.2.255 scope global enp0s3
       valid_lft forever preferred_lft forev2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:0f:33:b9 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.16/24 brd 10.0.2.255 scope global enp0s3
       valid_lft forever preferred_lft forev
	

SYSTEMD-RESOLVED

Activer la résolution des noms avec systemd-resolved:
sudo systemctl start systemd-resolved.service
sudo systemctl enable systemd-resolved.service

systemctl status systemd-resolved.service
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
lines 1-22/22 (END)

Vérifier qu'un serveur DNS tourne localement et écoute sur un port:
sudo ss -laputn
Netid      State       Recv-Q       Send-Q                Local Address:Port             Peer Address:Port
udp        UNCONN      0            0                           0.0.0.0:5355                  0.0.0.0:*           users:(("systemd-resolve",pid=1559,fd=12))
udp        UNCONN      0            0                     127.0.0.53%lo:53                    0.0.0.0:*           users:(("systemd-resolve",pid=1559,fd=18))
udp        UNCONN      0            0                  10.0.3.15%enp0s8:68                    0.0.0.0:*           users:(("systemd-network",pid=1356,fd=20))
udp        UNCONN      0            0                              [::]:5355                     [::]:*           users:(("systemd-resolve",pid=1559,fd=14))
tcp        LISTEN      0            128                   127.0.0.53%lo:53                    0.0.0.0:*           users:(("systemd-resolve",pid=1559,fd=19))
tcp        LISTEN      0            128                         0.0.0.0:22                    0.0.0.0:*           users:(("sshd",pid=835,fd=5))
tcp        LISTEN      0            128                         0.0.0.0:5355                  0.0.0.0:*           users:(("systemd-resolve",pid=1559,fd=13))



Effectuer une commande de résoution de nom avec dig:
dig google.com @127.0.0.53

Résultat:
dig google.com @127.0.0.53

; <<>> DiG 9.11.13-RedHat-9.11.13-2.fc31 <<>> google.com @127.0.0.53
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 30216
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             233     IN      A       216.58.215.46

;; Query time: 100 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: ven. nov. 29 16:42:25 CET 2019
;; MSG SIZE  rcvd: 55

Effectuer une requête DNS avec systemd-resolve:
systemd-resolve google.fr
google.fr: 172.217.19.227                      -- link: enp0s8

-- Information acquired via protocol DNS in 1.2ms.
-- Data is authenticated: no



Un lien symbolique pointant vers /run/systemd/resolve/stub-resolv.conf :

Supprimer le fichier : sudo rm -rf /etc/resolv.conf

Mise en place du lien symbolique : 
sudo ln -s /run/systemd/resolve/stub-resolv.conf /etc/resolv.conf

Résultat:
cat /etc/resolv.conf

# This file is managed by man:systemd-resolved(8). Do not edit.
#
# This is a dynamic resolv.conf file for connecting local clients to the
# internal DNS stub resolver of systemd-resolved. This file lists all
# configured search domains.
#
# Run "resolvectl status" to see details about the uplink DNS servers
# currently in use.
#
# Third party programs must not access this file directly, but only through the
# symlink at /etc/resolv.conf. To manage man:resolv.conf(5) in a different way,
# replace this symlink by a static file or a different symlink.
#
# See man:systemd-resolved.service(8) for details about the supported modes of
# operation for /etc/resolv.conf.

nameserver 127.0.0.53
options edns0
search auvence.co


Modifier la configuration de systems-resolved:

J'ai décommenté la ligne #DNS= du fichier /etc/resolv.conf et ajouté le DNS 1.2.3.4 (sans oublier le systemctl restart systemd-resolved

Résultat:

MulticastDNS setting: yes
  DNSOverTLS setting: no
      DNSSEC setting: allow-downgrade
    DNSSEC supported: yes
         DNS Servers: 1.2.3.4
Fallback DNS Servers: 1.1.1.1
                      8.8.8.8
                      1.0.0.1
                      8.8.4.4
                      2606:4700:4700::1111
                      2001:4860:4860::8888
                      2606:4700:4700::1001
                      2001:4860:4860::8844
          DNSSEC NTA: 10.in-addr.arpa


Mise en place de DNS over TLS :

Modificiation du fichier avec DNS=1.1.1.1 et DNSovertTLS=yes

Mais je n'ai pas réussi à utiliser la commande TCPDUMP.



Activer l'uitlisation de DNSSEC:

Résultat :
sudo cat /etc/systemd/resolved.conf
[Resolve]
DNS=1.1.1.1
#FallbackDNS=1.1.1.1 8.8.8.8 1.0.0.1 8.8.4.4 2606:4700:4700::1111 2001:4860:4860::8888 2606:4700:4700::1001 2001:4860:4860::8844
#Domains=
#LLMNR=yes
#MulticastDNS=yes
DNSSEC=force
DNSOverTLS=yes
#Cache=yes
#DNSStubListener=yes
#ReadEtcHosts=yes



5. Gestions de sessions loginid

loginctl

SESSION  UID USER SEAT  TTY
      1 1000 leo  seat0 tty1
      3 1000 leo        pts/0
      4 1000 leo

3 sessions listed.



6. GESTION D'UNITES BASIQUES (SERVICES)

Trouver l'unité associée au processus chronyd

Résultat:
[leo@OpenSource ~]$ sudo cat /var/run/chrony/chronyd.pid
2080
[leo@OpenSource ~]$ systemctl status 2080
● session-3.scope - Session 3 of user leo
   Loaded: loaded (/run/systemd/transient/session-3.scope; transient)
Transient: yes
   Active: active (running) since Thu 2019-12-05 10:05:23 CET; 2h 31min ago
    Tasks: 6
   Memory: 37.7M
      CPU: 1min 8.760s
   CGroup: /user.slice/user-1000.slice/session-3.scope
           ├─ 916 sshd: leo [priv]
           ├─ 922 sshd: leo@pts/0
           ├─ 923 -bash
           ├─2080 chronyd
           ├─2148 systemctl status 2080
           └─2149 less

II BOOT ET LOGS :


cat graphe.svg | grep "sshd.service"
  <text class="right" x="2734.235" y="4614.000">sshd.service (197ms)</text>

sshd.service boot en 197ms







III -  MECANISMES MANIPULES PAR SYSTEMD

1. CGROUPS

Identifier le cgroup utilisé par votre session SSH.
ps -e -o pid,cmd,cgroup

   1367 sshd: leo [priv]            0::/user.slice/user-1000.slice/session-21.scope
   1371 sshd: leo [priv]            0::/user.slice/user-1000.slice/session-22.scope
   1372 sshd: leo@pts/0             0::/user.slice/user-1000.slice/session-21.scope
   1376 sshd: leo@notty             0::/user.slice/user-1000.slice/session-22.scope

Résultat:
[leo@OpenSource ~]$ cat /proc/1367/cgroup
0::/user.slice/user-1000.slice/session-21.scope

Modifier la RAM dediée à ma session utilisateur

Résultat:
[leo@OpenSource ~]$ sudo systemctl set-property user-1000.slice MemoryMax=510M
[leo@OpenSource ~]$ cat /sys/fs/cgroup/user.slice/user-1000.slice/memory.max
534773760
[leo@OpenSource ~]$ sudo systemctl set-property user-1000.slice MemoryMax=512M
[leo@OpenSource ~]$ cat /sys/fs/cgroup/user.slice/user-1000.slice/memory.max
536870912

Vérification de la création du fichier dans le chemin /etc/systemd/systemd.control/

Résultat:
[leo@OpenSource ~]$ sudo cat /etc/systemd/system.control/user-1000.slice.d/50-MemoryMax.conf
# This is a drop-in unit file extension, created via "systemctl set-property"
# or an equivalent operation. Do not edit.
[Slice]
MemoryMax=536870912



3. RESTRICTION ET ISOLATION

Identifier le cgroup utilisé

Résultat :
[root@OpenSource /]# systemctl status run-u118.service
● run-u118.service - /bin/bash
   Loaded: loaded (/run/systemd/transient/run-u118.service; transient)
Transient: yes
   Active: active (running) since Thu 2019-12-05 15:18:15 CET; 7min ago
 Main PID: 1499 (bash)
    Tasks: 3 (limit: 2341)
   Memory: 3.5M
      CPU: 470ms
   CGroup: /system.slice/run-u118.service
           ├─1499 /bin/bash
           ├─1802 systemctl status run-u118.service
           └─1803 less














