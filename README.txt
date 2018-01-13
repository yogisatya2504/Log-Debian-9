Konfigurasi Admin Debian 9:
---------------------------
install : net-tools (untuk ifconfig)
Ip static :
  konfigurasi :
  /etc/network/interfaces
  tips :
  auto (untuk ifup interface secara otomatis)
  allow-hotplug (untuk ifup interface secara otomatis saat hotplug)

SSH server :
  install :
  openssh-server (sisi server), openssh-client (sisi client)
  konfigurasi :
  /etc/ssh/sshd_config (sisi server), /etc/ssh/ssh_config (sisi client)

DNS :
  install :
  dns-utils (untuk nslookup dan dig), bind9 (untuk dns)
  konfigurasi :
  1. named.conf (untuk nambah konfigurasi zone mana saja)
     include (tempat file konfigurasi zone)
  2. konfigruasi zone :
     #konfigurasi untuk network internal saja
     view "internal" {
     	# untuk network yang dapat mengakses
	match-clients {
		localhost;
		10.10.10.0/24; # network internalnya enp0s8
	};
	# zone untuk dns yogi.com
	zone "yogi.com" {
		type master;
		file "/etc/bind/yogi.com.lan"; # tempat zone yogi.com
	};
	# zone untuk yogi.com (resolv reverse)
	zone "10.10.10.in-addr.arpa" { # ip dibalik misal 10.9.4.1 jadi 4.9.10.in-addr.arpa
		type master;
		file "/etc/bind/10.10.10.db";
	};
	# boleh sertakan include yang lain
     };
     # 10.10.10.0/24
     # network address           = 10.10.10.0
     # range network             = 10.10.10.0 - 10.10.10.255
     # cara penulisan            = 0.0.10.in-addr.arpa
  3. konfigurasi zone yogi.com :
     1. cp db.local ke yogi.com.lan
     2. replace localhost dengan yogi.com
     3. tambahkan www (dns untuk web server)
  4. konfigurasi zone addr.arpa :
     1. cp db.127 ke 10.10.10.db
     2. replace localhost dengan yogi.com
     3. tambahkan :
	# 1 adalah ip dns (10.10.10.1)
	1	IN	PTR	yogi.com # untuk yogi.com
	1	IN	PTR	www.yogi.com # untuk www.yogi.com
  5. restart service dan dig
