## Configuración de red estática

    [root@localhost network-scripts]# vi ifcfg-eth0 
    
    DEVICE="eth0"
    BOOTPROTO="static"
    IPADDR=192.168.122.14
    NETMASK=255.255.255.0
    DNS1=192.168.122.1
    GATEWAY=192.168.122.1
    BOOTPROTOv6="dhcp"
    ONBOOT="yes"
    TYPE="Ethernet"
    USERCTL="yes"
    PEERDNS="yes"
    IPV6INIT="no"
    PERSISTENT_DHCLIENT="1"
    NM_CONTROLLED=no

## Asignación de nombre del equipo

    [root@localhost network-scripts]# hostnamectl set-hostname rhev4.pisa.com
    [root@localhost network-scripts]# hostnamectl set-hostname --transient rhev4.pisa.com
    [root@localhost network-scripts]# echo '192.168.122.14 rhev4.pisa.com rhev4' >> /etc/hosts

## Suscribiendo equipo

    [root@localhost network-scripts]# subscription-manager register --username=tu-user
      Registering to: subscription.rhn.redhat.com:443/subscription
      Password: 
      The system has been registered with ID: 93fe19f7-14f0-4aa1-bc72-27b0e3164742 

    [root@localhost network-scripts]# subscription-manager list --available
      +-------------------------------------------+
          Available Subscriptions
      +-------------------------------------------+
      Subscription Name:   60 Day Self-Supported Red Hat Enterprise Linux OpenStack Platform Preview
      Provides:            Red Hat Beta
                           Red Hat OpenStack Beta
                           Red Hat Software Collections (for RHEL Server)
                           Oracle Java (for RHEL Server)
                           Red Hat OpenStack
                           Red Hat Enterprise MRG Messaging
                           Red Hat Enterprise Linux Server
                           Red Hat Enterprise Linux High Availability (for RHEL Server)
                           Red Hat Software Collections Beta (for RHEL Server)
                           Red Hat Enterprise Linux Load Balancer (for RHEL Server)

* Asignando pool de suscripciones.  

    [root@localhost network-scripts]# subscription-manager attach --pool=8a85f98a5611056201564a9cfe6a6341

* Deshabilitando canales
  
    [root@localhost network-scripts]# subscription-manager repos --disable=*
  
* Habilitando canales
  
    [root@localhost network-scripts]# subscription-manager repos --enable=rhel-7-server-rpms --enable=rhel-7-server-supplementary-rpms --enable=rhel-7-server-rhv-4-beta-rpms --enable=rhel-7-server-optional-rpms --enable=jb-eap-7-for-rhel-7-server-rpms 
      Repository 'rhel-7-server-rpms' is enabled for this system.
      Repository 'rhel-7-server-rhv-4-beta-rpms' is enabled for this system.
      Repository 'rhel-7-server-supplementary-rpms' is enabled for this system.
      Repository 'rhel-7-server-optional-rpms' is enabled for this system.
      Repository 'jb-eap-7-for-rhel-7-server-rpms' is enabled for this system.

* Actualizando sistema

    [root@localhost network-scripts]# time  yum update -y 

* Congifuración de zona horaria.

    [root@rhev4 ~]# timedatectl set-timezone UTC

    [root@rhev4 ~]# timedatectl list-timezones | grep -i America/Mexico
      America/Mexico_City

    [root@rhev4 ~]# timedatectl set-timezone America/Mexico_City

    [root@rhev4 ~]# timedatectl status
            Local time: lun 2016-08-15 20:12:04 CDT
        Universal time: mar 2016-08-16 01:12:04 UTC
              RTC time: mar 2016-08-16 01:12:04
             Time zone: America/Mexico_City (CDT, -0500)
           NTP enabled: yes
      NTP synchronized: yes
       RTC in local TZ: no
            DST active: yes
       Last DST change: DST began at
                        dom 2016-04-03 01:59:59 CST
                        dom 2016-04-03 03:00:00 CDT
       Next DST change: DST ends (the clock jumps one hour backwards) at
                        dom 2016-10-30 01:59:59 CDT
                        dom 2016-10-30 01:00:00 CST

* Habilitando NTP

    [root@rhev4 ~]# timedatectl set-ntp true

      [root@rhev4 ~]# systemctl status ntpd
      ● ntpd.service - Network Time Service
         Loaded: loaded (/usr/lib/systemd/system/ntpd.service; enabled; vendor preset: disabled)
         Active: active (running) since lun 2016-08-15 19:23:37 CDT; 49min ago
       Main PID: 10138 (ntpd)
         CGroup: /system.slice/ntpd.service
                 └─10138 /usr/sbin/ntpd -u ntp:ntp -g
      
      ago 15 19:23:37 rhev4.pisa.com ntpd[10138]: Listening on routing socket on fd #22 for interface updates
      ago 15 19:23:37 rhev4.pisa.com systemd[1]: Started Network Time Service.
      ago 15 19:23:39 rhev4.pisa.com ntpd[10138]: 0.0.0.0 c016 06 restart
      ago 15 19:23:39 rhev4.pisa.com ntpd[10138]: 0.0.0.0 c012 02 freq_set kernel 0.000 PPM
      ago 15 19:23:39 rhev4.pisa.com ntpd[10138]: 0.0.0.0 c011 01 freq_not_set
      ago 15 19:23:39 rhev4.pisa.com ntpd[10138]: 0.0.0.0 c614 04 freq_mode
      ago 15 19:23:47 rhev4.pisa.com systemd[1]: Started Network Time Service.
      ago 15 19:24:06 rhev4.pisa.com systemd[1]: Started Network Time Service.
      ago 15 19:45:07 rhev4.pisa.com ntpd[10138]: 0.0.0.0 0612 02 freq_set kernel 12.249 PPM
      ago 15 19:45:07 rhev4.pisa.com ntpd[10138]: 0.0.0.0 0615 05 clock_sync
      
      [root@rhev4 ~]# systemctl enable ntpd






