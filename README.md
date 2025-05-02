# HuaweiSwitchVlanDemo


Huawei Switch Konfigürasyonu: SSH, VLAN ve Static Route Ayarları <br>
Bu makalede Huawei switch cihazı üzerinde temel yapılandırma işlemlerini adım adım ele alacağız. Switch ismi değiştirmeden VLAN yapılandırmasına, SSH erişiminden port yapılandırmalarına kadar birçok temel işlemi uygulayacağız.<br>

1. Switch İsmi Değiştirme 
İlk olarak switch’e erişip yapılandırma moduna geçiyoruz: <br>
 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20094236.png)   <br>
<Switch> system-view<br>
[Switch] sysname Zemin-1    <br>
Bu komut ile switch adını “Zemin-1” olarak değiştirmiş olduk.   <br>

2. SSH Erişimi için Kullanıcı Oluşturma   <br>
SSH erişimi sağlamak için kullanıcılar oluşturmalıyız. Aşağıdaki adımları izleyerek kullanıcı tanımlayabiliriz:   <br>

 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20094259.png)   <br>
[Zemin-1] aaa   <br>
[Zemin-1-aaa] local-user Nurullah password cipher Huawei@1234
[Zemin-1-aaa] local-user Nurullah privilege level 15
[Zemin-1-aaa] local-user Nurullah service-type ssh http
Aynı şekilde sadece SSH ile erişimi olan, yetkisi sınırlı bir kullanıcı oluşturalım:   <br>


[Zemin-1-aaa] local-user mustafa password cipher Huawei@1234   <br>
[Zemin-1-aaa] local-user mustafa privilege level 1   <br>
[Zemin-1-aaa] local-user mustafa service-type ssh   <br>
 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20094454.png)   <br>
3. VLAN Oluşturma   <br>
Switch üzerinde VLAN’ları oluşturup açıklamalar ekleyelim:   <br>

[Zemin-1] vlan 10   <br>
[Zemin-1-vlan10] description Ziyaretci ve misafir networku   <br>

[Zemin-1] vlan 11   <br>
[Zemin-1-vlan11] description Personel wifi   <br>
   <br>
[Zemin-1] vlan 20   <br>
[Zemin-1-vlan20] description IOT NETWORK   <br>

[Zemin-1] vlan 30   <br>
[Zemin-1-vlan30] description Ofis   <br>

[Zemin-1] vlan 60   <br>
[Zemin-1-vlan70] description  Fabrika LAN   <br>

[Zemin-1] vlan 70   <br>
[Zemin-1-vlan70] description KameraNetwork   <br>

[Zemin-1] vlan 80   <br>
[Zemin-1-vlan70] description VoIP VLAN   <br>

[Zemin-1] vlan 100     <br>
[Zemin-1-vlan100] description NetworkManagement   <br>
Yapılandırmaları kaydedelim:   <br>

<Zemin-1> save   <br>

 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20095041.png)    <br>
4. VLAN Arayüzlerine IP Adresi Verme    <br>
VLAN’lara IP adresi atayalım:    <br>

[Zemin-1] interface Vlanif 10    <br>
[Zemin-1-Vlanif10] ip address 20.20.20.2 255.255.255.0    <br>
 
[Zemin-1] interface Vlanif 100    <br>
[Zemin-1-Vlanif100] ip address 192.168.30.2 255.255.255.0    <br>
Tanımlanan IP’leri görüntülemek için:    <br>

[Zemin-1] display ip interface brief    <br>
VLAN silmek için:    <br>

[Zemin-1] undo interface Vlanif 10    <br>
[Zemin-1] undo interface Vlanif 200    <br>
5. HTTP Erişim VLAN’ı Belirleme    <br>
 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20095545.png)    <br>
[Zemin-1] http server-source -i vlanif 100    <br>
Bu komutla yalnızca VLAN 100 içindeki cihazlar web erişimi sağlayabilir.    <br>

6. Switch Portlarına VLAN Atama    <br>
Kamera VLAN’ını (70) belirli portlara atayalım:    <br>
 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20100609.png)    <br>
[Zemin-1] interface range gigabitEthernet 0/0/1 to 0/0/12    <br>
[Zemin-1] port link-type access    <br> 
[Zemin-1] port default vlan 70    <br>
Port 13–17 arası Trunk moda alalım:    <br>

[Zemin-1] interface range gigabitEthernet 0/0/13 to 0/0/17    <br>
[Zemin-1] port link-type trunk    <br>
[Zemin-1] port trunk allow-pass vlan all    <br>
[Zemin-1] port trunk pvid vlan 100    <br>
Sonradan bir port ekleyelim (örneğin port 18):    <br>

[Zemin-1] interface gigabitEthernet 0/0/18    <br>
[Zemin-1] port link-type access    <br>
[Zemin-1] port default vlan 70   <br>
Fabrika LAN için:   <br>

[Zemin-1] interface range gigabitEthernet 0/0/19 to 0/0/21   <br>
[Zemin-1] port link-type access   <br>
[Zemin-1] port default vlan 60   <br>
VoIP VLAN ataması:   <br>

[Zemin-1] interface gigabitEthernet 0/0/22   <br>
[Zemin-1] port link-type access   <br>
[Zemin-1] port default vlan 80   <br>

[Zemin-1] interface gigabitEthernet 0/0/23   <br>
[Zemin-1] port link-type access   <br>
[Zemin-1] port default vlan 80   <br>
Yedek yönetim portu:   <br>

[Zemin-1] interface gigabitEthernet 0/0/24   <br>
[Zemin-1] port link-type access   <br>
[Zemin-1] port default vlan 100   <br>
7. 10G (XGigabit) Portlarını Trunk Moda Alma   <br>
 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20101716.png)   <br>
[Zemin-1] interface range XgigabitEthernet 0/0/1 to 0/0/4   <br>
[Zemin-1] port link-type trunk   <br>
[Zemin-1] port trunk allow-pass vlan all   <br>
8. Static Route Yapılandırması   <br>
Varsayılan rota tanımlamak için:   <br>
 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20103014.png)   <br>
[Zemin-1] ip route-static 0.0.0.0 0.0.0.0 Vlanif 100 192.168.30.1   <br>
Burada 192.168.30.1, iç ağdaki firewall’un iç bacağıdır.   <br>

Bu adımlar sayesinde Huawei switch üzerinde temel ağ yapılandırması tamamlanmış olur. SSH erişimi, VLAN’lar, IP atamaları, port yetkilendirmeleri ve yönlendirme işlemleriyle tam işlevli bir ağ altyapısı sağlanabilir.   <br>
 ![image alt](https://github.com/nurullahnamal/HuaweiSwitchVlanDemo/blob/main/Ekran%20g%C3%B6r%C3%BCnt%C3%BCs%C3%BC%202025-05-01%20122045.png)   <br>
Kaynak : Mustafa Yalçın   <br>

https://youtu.be/sDJ9UWYi9aw
