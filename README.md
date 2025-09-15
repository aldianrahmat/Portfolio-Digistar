# Portfolio-Digistar
Vlan &amp; Routing Dynamic Cisco Packet Tracer

📌 Deskripsi

Proyek ini mensimulasikan jaringan perusahaan dengan 3 kantor (Pusat, Cabang A, Cabang B) menggunakan Cisco Packet Tracer.
Setiap divisi dipisahkan dengan VLAN, lalu komunikasi antar kantor dilakukan dengan protokol routing berbeda (RIP, EIGRP, OSPF) yang dihubungkan lewat Router Gateway.

-------------------------------------------------------------

💻 Skema IP Address

    HR        | Vlan 10	| IP: 192.168.10.0/24	| GW: 192.168.10.1
    Finance	  | Vlan 20	| IP: 192.168.20.0/24	| GW: 192.168.20.1
    IT        | Vlan 30	| IP: 192.168.30.0/24	| GW: 192.168.30.1
    Marketing |	Vlan 40	| IP: 192.168.40.0/24	| GW: 192.168.40.1
    Sales	  | Vlan 50	| IP: 192.168.50.0/24	| GW: 192.168.50.1
    Logistik  | Vlan 60	| IP: 192.168.60.0/24	| GW: 192.168.60.1

-------------------------------------------------------------

⚙️ Konfigurasi CLI

    Konfigurasi Switch – VLAN Configuration (Kantor Pusat)
    
    ! Catatan: Tambahkan VLAN lain dengan cara yang sama seperti dibawah ini
    
    ! Membuat VLAN
    vlan 10
    name HR
    vlan 20
    name Finance
    
    ! Assign port ke VLAN
    interface fa1/1
     switchport mode access
     switchport access vlan 10
    
    interface fa2/1
     switchport mode access
     switchport access vlan 20
    
    ! Trunk ke router
    interface fa0/1
     switchport trunk encapsulation dot1q
     switchport mode trunk
 
-------------------------------------------------------------

    Konfigurasi Router Kantor Pusat – Router on a Stick + RIP
    
    ! Aktifkan interface ke switch
    interface fa0/0
     no shutdown
    
    ! VLAN 10 - HR
    interface fa0/0.10
     encapsulation dot1Q 10
     ip address 192.168.10.1 255.255.255.0
    
    ! VLAN 20 - Finance
    interface fa0/0.20
     encapsulation dot1Q 20
     ip address 192.168.20.1 255.255.255.0
    
    ! Routing RIP
    router rip
     version 2
     network 192.168.10.0
     network 192.168.20.0
     network 192.168.100.0   ! Link ke Gateway

-------------------------------------------------------------

    Konfigurasi Router Cabang B – Router on a Stick + OSPF
    
     ! VLAN 50 - Sales
    interface fa0/0.50
     encapsulation dot1Q 50
     ip address 192.168.50.1 255.255.255.0
    
    ! VLAN 60 - Logistik
    interface fa0/0.60
     encapsulation dot1Q 60
     ip address 192.168.60.1 255.255.255.0
    
    ! Routing OSPF
    router ospf 1
     network 192.168.50.0 0.0.0.255 area 0
     network 192.168.60.0 0.0.0.255 area 0
     network 192.168.102.0 0.0.0.3 area 0   ! Link ke Gateway

 -------------------------------------------------------------

    Konfigurasi Router Gateway – Redistribusi Routing
    
     ! RIP
    router rip
     version 2
     network 192.168.100.0
    
    ! EIGRP
    router eigrp 100
     network 192.168.101.0
     redistribute rip metric 10000 100 255 1 1500
    
    ! OSPF
    router ospf 1
     network 192.168.102.0 0.0.0.3 area 0
     redistribute eigrp 100 subnets
     redistribute rip subnets

-------------------------------------------------------------

Hasil Uji Coba

    - PC antar VLAN dalam satu kantor dapat saling ping (Inter-VLAN routing).
    - PC antar kantor (Pusat, A, B) bisa saling komunikasi meskipun protokol berbeda (RIP ↔ EIGRP ↔ OSPF).
    - Jaringan lebih terstruktur dan scalable berkat penggunaan VLAN + dynamic routing.


