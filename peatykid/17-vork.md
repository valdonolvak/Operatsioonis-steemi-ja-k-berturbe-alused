[← 16. Salvestushaldus](16-salvestushaldus.md) · [Sisukord](../README.md) · [18. Windowsi turvalisus →](18-turvalisus.md)

## 17. Võrguvõimalused Windowsis

Windowsi võrgupinu on kihiline: rakendused kasutavad API-sid (Winsock, WinHTTP, SMB), nende all on transpordi- ja võrguprotokollid (TCP, UDP, QUIC, IPv4/IPv6) ning kõige all NDIS-draiverid, mis juhivad võrgukaarti. Liiklust filtreerib Windows Filtering Platform (WFP), millele toetuvad tulemüür ja turvatarkvara.

### 17.1 Võrguteenused

| Teenus | Roll |
| --- | --- |
| DHCP-klient | saab IP-aadressi, lüüsi ja DNS-serverid |
| DNS-klient | nimede lahendamine, vahemälu; Windows 11 toetab DNS over HTTPS-i (DoH) |
| SMB-klient ja -server | failide ja printerite jagamine |
| Network Location Awareness (NLA) | tuvastab võrgu profiili: domeen, privaatne, avalik |
| WinRM | PowerShelli kaugjuhtimine |
| Remote Desktop | kaugtöölaud (RDP) |
| Windows Defender Firewall | pakettide filtreerimine (ptk 18) |

**Aegunud ja eemaldatud:** SMB 1.0 ei ole vaikimisi paigaldatud (WannaCry 2017 levis just selle kaudu); NetBIOS ja LLMNR tuleks domeenis keelata, sest neid kasutatakse parooliräside püüdmiseks; NTLM-ist liigutakse Kerberose poole (17.4).

### 17.2 Võrgu-API-d

- **Winsock** (Windows Sockets) – BSD pesade põhine API TCP/UDP jaoks; toetab ka Unixi domeenipesi (AF\_UNIX);
- **WinHTTP ja WinINet** – HTTP-kliendid (teenused vs kasutajarakendused);
- **HTTP Server API (http.sys)** – tuumarežiimi HTTP-server, millel töötavad IIS ja paljud teenused; toetab HTTP/2 ja HTTP/3 (QUIC);
- **MsQuic** – Microsofti avatud lähtekoodiga QUIC-teek, kasutusel HTTP/3 ja SMB over QUIC jaoks;
- **RPC ja nimega torud** – Windowsi teenuste kaugkutsed (AD, haldustööriistad);
- **.NET `HttpClient`, `Socket`** – hallatud koodi liidesed.

### 17.3 Võrgudraiverid Windowsis

- **NDIS** (Network Driver Interface Specification) – võrgukaartide draiverite raamistik: *miniport*-draiver juhib kaarti, *protokolli*draiver (TCP/IP) teenindab ülemist kihti, *filtri*draiverid asuvad nende vahel;
- **tcpip.sys** – Windowsi TCP/IP pinu (IPv4 ja IPv6; IPv6 on eelistatud);
- **WFP** (Windows Filtering Platform) – filtreerimisraamistik, kuhu tulemüür, VPN-id ja EDR-id lisavad oma reegleid;
- **AFD** (`afd.sys`) – Winsocki tuumapoolne osa;
- **jõudlusfunktsioonid** – RSS (vastuvõtu jagamine tuumade vahel), RSC, kontrollsummade ja TCP segmenteerimise mahalaadimine võrgukaardile, RDMA (SMB Direct).

![Windowsi võrgupinu · kihid ja OSI mudeli vasted](../pildid/17-a.png)

Joonis: rakenduse andmed liiguvad API kaudu tuuma TCP/IP pinusse; WFP kontrollib iga ühendust enne, kui NDIS-draiverid selle võrgukaardile annavad.

### 17.4 Autentimine võrgus: NTLM-ist Kerberoseni

**Kerberos** on domeeni vaikimisi autentimisprotokoll: kasutaja saab domeenikontrollerilt (KDC) piletid ja parool võrgus ei liigu. **NTLM** on vanem väljakutse-vastuse protokoll, mis on haavatav releerünnetele ja räside murdmisele.

Microsoft avaldas 2026. aasta alguses kolmeetapilise plaani NTLM-i vaikimisi keelamiseks: 1) parem auditeerimine Windows 11 24H2 ja Server 2025-s; 2) 2026. aasta teisel poolel uued võimalused **IAKerb** ja **Local KDC**, mis katavad olukorrad, kus seni langeti NTLM-ile tagasi; 3) järgmises Windows Serveri versioonis ja vastavas kliendis on võrgu-NTLM vaikimisi keelatud, kuid poliitikaga taaslubatav ([The Hacker News](https://thehackernews.com/2026/02/microsoft-begins-ntlm-phase-out-with.html), [Petri](https://petri.com/microsoft-disable-ntlm-default-windows/)). Administraator peaks juba praegu auditeerima, millised rakendused NTLM-i kasutavad.

![Kerberos ja NTLM · kuidas klient serverile end tõestab](../pildid/17-b.png)

Joonis: Kerberose korral saab klient domeenikontrollerilt ajapiiranguga pileti ja esitab selle serverile; NTLM-i korral vastab klient serveri väljakutsele parooli räsi abil, mida ründaja saab vahendada (relee) või murda.

### Näide: „internet ei tööta“ – kihthaaval altpoolt üles

Kasutaja ütleb, et ükski veebileht ei avane. Hea tõrkeotsing liigub võrgupinus alt üles ja kontrollib iga kihti ühe käsuga:

| Kiht | Käsk | Hea tulemus |
| --- | --- | --- |
| 1–2 võrgukaart | `Get-NetAdapter` | *Status: Up*, kiirus näha |
| 3 IP-aadress | `Get-NetIPConfiguration` | IPv4 aadress, lüüs, DNS (mitte 169.254.x.x) |
| 3 lüüs | `Test-NetConnection 192.168.1.1` | *PingSucceeded: True* |
| 3 internet | `Test-NetConnection 1.1.1.1` | vastus tuleb |
| 7 DNS | `Resolve-DnsName www.ria.ee` | IP-aadress tagastatakse |
| 4 port | `Test-NetConnection www.ria.ee -Port 443` | *TcpTestSucceeded: True* |
| tulemüür/proksi | `Get-NetFirewallProfile`, `netsh winhttp show proxy` | ootamatut keeldu ega proksit pole |

**Lahendus antud juhul:** aadress oli `169.254.12.7`, mis tähendab, et DHCP-server ei vastanud. Pärast võrgukaabli kontrolli ja käsku `ipconfig /renew` saadi aadress `192.168.1.57` ja internet töötas. Kui esimesed kolm kihti oleksid korras olnud ja ainult DNS ebaõnnestunud, oleks probleem olnud DNS-serveris, mitte võrgukaardis.

### Kokkuvõte

- Windowsi võrgupinu: API-d → TCP/IP ja QUIC → WFP → NDIS → võrgukaart.
- QUIC ja HTTP/3 on Windowsi osa; SMB over QUIC laiendab failijagamist internetti.
- Vanad protokollid (SMB1, NetBIOS, LLMNR, NTLM) on turvarisk ja neist loobutakse.

### Kontrollküsimused

1. Nimeta viis Windowsi võrguteenust ja nende rolli.
2. Mis on Winsock ja mis on http.sys?
3. Mis on NDIS-i miniport-, protokolli- ja filtridraiveri roll?
4. Mis on WFP ja kes seda kasutavad?
5. Miks on SMB 1.0 vaikimisi eemaldatud?
6. Mis vahe on Kerberosel ja NTLM-il?
7. Kirjelda Microsofti NTLM-ist loobumise kolme etappi.
8. Milleks on QUIC ja kus Windows seda kasutab?

### Valikküsimused

1. Milline komponent on Windowsi pakettide filtreerimise raamistik? a) NDIS b) WFP c) AFD d) RSS
2. Mis on domeenis vaikimisi autentimisprotokoll? a) NTLM b) Kerberos c) LDAP d) RADIUS
3. Millise protokolli kaudu levis WannaCry? a) RDP b) SMB 1.0 c) DNS d) HTTP/3
4. Mis on IAKerb? a) uus tulemüür b) Kerberose laiendus, kui kliendil pole otseühendust domeenikontrolleriga c) NTLM-i uus versioon d) DNS-i turvalaiendus

### Praktiline ülesanne

```powershell
Get-NetIPConfiguration
Get-NetAdapter | Select-Object Name, InterfaceDescription, LinkSpeed, Status
Resolve-DnsName www.ria.ee -Type A
Test-NetConnection www.ria.ee -Port 443
Get-NetTCPConnection -State Established | Select-Object -First 10 LocalPort, RemoteAddress, RemotePort, OwningProcess
Get-SmbConnection
Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Salvesta võrguliiklus Windowsi sisseehitatud tööriistaga ja ava see Wiresharkis:

```powershell
pktmon start --capture --pkt-size 0 -f $env:TEMP\v6rk.etl
# ava brauseris mõni leht
pktmon stop
pktmon etl2pcap $env:TEMP\v6rk.etl --out $env:TEMP\v6rk.pcapng
```

Domeenikeskkonnas: ava sündmusvaatur → Applications and Services Logs → Microsoft → Windows → NTLM → Operational ja leia, millised teenused kasutavad NTLM-i.

### Iseseisvad ülesanded

**1. Veebilehe avamise analüüs.** Salvesta `pktmon`-iga või Wiresharkiga liiklus, kui avad brauseris ühe HTTPS-lehe (nt `www.ria.ee`). Leia salvestisest DNS-päring ja -vastus, TCP kolmesammuline käepigistus (SYN, SYN-ACK, ACK) või QUIC-pakettide algus ja TLS Client Hello koos serveri nimega (SNI). **Esita:** ekraanipildid ja selgitus, millises võrgupinu kihis iga leitud pakett tekkis.

**2. Vanad protokollid minu võrgus.** Kontrolli õppe- või koolivõrgus: kas SMB1 on paigaldatud (`Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol`), kas LLMNR ja NetBIOS on lubatud (võrgukaardi IPv4 seaded → Täpsemalt → WINS) ning kas domeenis kasutatakse NTLM-i (NTLM Operational logi). **Esita:** tulemused ja iga protokolli kohta üks lause, milliseks rünnakuks seda saab kasutada.

### Viited

- Krogh (2017), ptk 17.
- Russinovich, M., Solomon, D., Ionescu, A. (2012). *Windows Internals*, 6. tr, 1. osa, ptk 7 „Networking“ (7. tr-s eraldi võrgupeatükki pole).
- Microsoft Learn: [Windows Filtering Platform](https://learn.microsoft.com/windows/win32/fwp/windows-filtering-platform-start-page); [Packet Monitor](https://learn.microsoft.com/windows-server/networking/technologies/pktmon/pktmon).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 16. Salvestushaldus](16-salvestushaldus.md) · [Sisukord](../README.md) · [18. Windowsi turvalisus →](18-turvalisus.md)
