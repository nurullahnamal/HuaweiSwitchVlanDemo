# HuaweiSwitchVlanDemo


Huawei Switch Konfigürasyonu: SSH, VLAN ve Static Route Ayarları
Bu makalede Huawei switch cihazı üzerinde temel yapılandırma işlemlerini adım adım ele alacağız. Switch ismi değiştirmeden VLAN yapılandırmasına, SSH erişiminden port yapılandırmalarına kadar birçok temel işlemi uygulayacağız.

1. Switch İsmi Değiştirme
İlk olarak switch’e erişip yapılandırma moduna geçiyoruz:
 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20094236.png)
<Switch> system-view
[Switch] sysname Zemin-1
Bu komut ile switch adını “Zemin-1” olarak değiştirmiş olduk.

2. SSH Erişimi için Kullanıcı Oluşturma
SSH erişimi sağlamak için kullanıcılar oluşturmalıyız. Aşağıdaki adımları izleyerek kullanıcı tanımlayabiliriz:

 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20094259.png)
[Zemin-1] aaa
[Zemin-1-aaa] local-user Nurullah password cipher Huawei@1234
[Zemin-1-aaa] local-user Nurullah privilege level 15
[Zemin-1-aaa] local-user Nurullah service-type ssh http
Aynı şekilde sadece SSH ile erişimi olan, yetkisi sınırlı bir kullanıcı oluşturalım:


[Zemin-1-aaa] local-user mustafa password cipher Huawei@1234
[Zemin-1-aaa] local-user mustafa privilege level 1
[Zemin-1-aaa] local-user mustafa service-type ssh
 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20094454.png)
3. VLAN Oluşturma
Switch üzerinde VLAN’ları oluşturup açıklamalar ekleyelim:

[Zemin-1] vlan 10
[Zemin-1-vlan10] description Ziyaretci ve misafir networku

[Zemin-1] vlan 11
[Zemin-1-vlan11] description Personel wifi

[Zemin-1] vlan 20
[Zemin-1-vlan20] description IOT NETWORK

[Zemin-1] vlan 30
[Zemin-1-vlan30] description Ofis

[Zemin-1] vlan 60
[Zemin-1-vlan70] description  Fabrika LAN

[Zemin-1] vlan 70
[Zemin-1-vlan70] description KameraNetwork

[Zemin-1] vlan 80
[Zemin-1-vlan70] description VoIP VLAN

[Zemin-1] vlan 100
[Zemin-1-vlan100] description NetworkManagement
Yapılandırmaları kaydedelim:

<Zemin-1> save

 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20095041.png)
4. VLAN Arayüzlerine IP Adresi Verme
VLAN’lara IP adresi atayalım:

[Zemin-1] interface Vlanif 10
[Zemin-1-Vlanif10] ip address 20.20.20.2 255.255.255.0

[Zemin-1] interface Vlanif 100
[Zemin-1-Vlanif100] ip address 192.168.30.2 255.255.255.0
Tanımlanan IP’leri görüntülemek için:

[Zemin-1] display ip interface brief
VLAN silmek için:

[Zemin-1] undo interface Vlanif 10
[Zemin-1] undo interface Vlanif 200
5. HTTP Erişim VLAN’ı Belirleme
 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20095545.png)
[Zemin-1] http server-source -i vlanif 100
Bu komutla yalnızca VLAN 100 içindeki cihazlar web erişimi sağlayabilir.

6. Switch Portlarına VLAN Atama
Kamera VLAN’ını (70) belirli portlara atayalım:
 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20100609.png)
[Zemin-1] interface range gigabitEthernet 0/0/1 to 0/0/12
[Zemin-1] port link-type access
[Zemin-1] port default vlan 70
Port 13–17 arası Trunk moda alalım:

[Zemin-1] interface range gigabitEthernet 0/0/13 to 0/0/17
[Zemin-1] port link-type trunk
[Zemin-1] port trunk allow-pass vlan all
[Zemin-1] port trunk pvid vlan 100
Sonradan bir port ekleyelim (örneğin port 18):

[Zemin-1] interface gigabitEthernet 0/0/18
[Zemin-1] port link-type access
[Zemin-1] port default vlan 70
Fabrika LAN için:

[Zemin-1] interface range gigabitEthernet 0/0/19 to 0/0/21
[Zemin-1] port link-type access
[Zemin-1] port default vlan 60
VoIP VLAN ataması:

[Zemin-1] interface gigabitEthernet 0/0/22
[Zemin-1] port link-type access
[Zemin-1] port default vlan 80

[Zemin-1] interface gigabitEthernet 0/0/23
[Zemin-1] port link-type access
[Zemin-1] port default vlan 80
Yedek yönetim portu:

[Zemin-1] interface gigabitEthernet 0/0/24
[Zemin-1] port link-type access
[Zemin-1] port default vlan 100
7. 10G (XGigabit) Portlarını Trunk Moda Alma
 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20101716.png)
[Zemin-1] interface range XgigabitEthernet 0/0/1 to 0/0/4
[Zemin-1] port link-type trunk
[Zemin-1] port trunk allow-pass vlan all
8. Static Route Yapılandırması
Varsayılan rota tanımlamak için:
 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20103014.png)
[Zemin-1] ip route-static 0.0.0.0 0.0.0.0 Vlanif 100 192.168.30.1
Burada 192.168.30.1, iç ağdaki firewall’un iç bacağıdır.

Bu adımlar sayesinde Huawei switch üzerinde temel ağ yapılandırması tamamlanmış olur. SSH erişimi, VLAN’lar, IP atamaları, port yetkilendirmeleri ve yönlendirme işlemleriyle tam işlevli bir ağ altyapısı sağlanabilir.
 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20122045.png)
Kaynak : Mustafa Yalçın

https://youtu.be/sDJ9UWYi9aw
