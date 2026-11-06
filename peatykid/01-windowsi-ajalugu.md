[← Sissejuhatus](00-sissejuhatus.md) · [Sisukord](../README.md) · [2. Operatsioonisüsteemi ülesanded →](02-os-i-ulesanded.md)

## 1. Windowsi ajalugu

Windows on arenenud MS-DOS-i graafilisest kestast üheks laiemalt kasutatavaks operatsioonisüsteemiks, mille tänane tuum pärineb 1993. aastal ilmunud Windows NT-st. 2026. aasta sügisel on toetatud kliendiversioon Windows 11 (viimane funktsiooniuuendus 26H2) ja serveriversioon Windows Server 2025.

### 1.1 MS-DOS

IBM-i personaalarvuti (1981) jaoks ostis Microsoft Seattle Computer Productsilt süsteemi 86-DOS, kohandas selle ja nimetas MS-DOS-iks. MS-DOS oli ühe kasutaja ja ühe ülesandega (single-tasking) käsurealõikesüsteem: korraga töötas üks programm, mälu kaitset polnud ning programm võis otse riistvara poole pöörduda. Need piirangud on hea lähtekoht, et mõista, milleks kaasaegne OS mälukaitset ja õigusi vajab.

### 1.2 Esimesed Windowsi versioonid

Windows 1.0 (1985) kuni Windows 3.x olid graafilised kasutajaliidesed, mis töötasid MS-DOS-i peal. Windows 95, 98 ja ME („9x-pere“) tutvustasid Start-menüüd, 32-bitiseid rakendusi ja Plug and Play’d, kuid alus oli endiselt DOS: üks vigane programm võis kogu süsteemi kokku jooksutada.

### 1.3 Windows NT

Windows NT (New Technology) loodi nullist, juhiks Dave Cutler. Eesmärgid olid töökindlus, turvalisus, kaasaskantavus eri protsessoriarhitektuuridele ja mitme kasutaja tugi. NT 3.1 ilmus 1993. NT-l on kasutajarežiim ja tuumarežiim, mälukaitse, ACL-ipõhine õiguste süsteem ning failisüsteem NTFS.

> 📊 **Joonis:** Windowsi kaks arendusliini · DOS-liin lõppes 2000, NT-liin jätkub

Joonis: DOS-põhine liin lõppes Windows ME-ga; Windows XP-st alates kasutavad ka koduarvutid NT-tuuma, millel põhineb ka Windows 11.

### 1.4 NT-põhised Windowsi versioonid

| Aasta | Versioon | Olulisem muutus |
| --- | --- | --- |
| 2001 | Windows XP | NT-tuum jõudis koduarvutitesse |
| 2007 | Windows Vista | UAC, BitLocker, uus graafikamudel |
| 2009 | Windows 7 | stabiilsus, kiirus |
| 2012 | Windows 8 / 8.1 | puutetundlik liides, UEFI Secure Boot |
| 2015 | Windows 10 | „Windows as a Service“, regulaarsed funktsiooniuuendused |
| 2021 | Windows 11 | kohustuslik TPM 2.0 ja Secure Boot, VBS vaikimisi |
| 2024 | Windows 11 24H2 | Copilot+ arvutid, uus tuumaväljalase |
| 2026 | Windows 11 26H2 | iga-aastane funktsiooniuuendus (enablement package) |

**Windows 10 elutsükli lõpp.** Windows 10 tasuta tugi lõppes 14. oktoobril 2025; laiendatud turvauuenduste (ESU) programm pikendas tarbijate turvauuendusi aasta vörra, kuni 13. oktoobrini 2026 ([Pureinfotech](https://pureinfotech.com/windows-10-end-support-october-14-2025/)). Organisatsioonid saavad ESU-d osta kuni kolmeks aastaks, s.t hiljemalt 2028. aasta oktoobrini ([heise](https://heise.de/-10001284)). See tähendab, et 2026. aasta novembris ei saa tavaline Windows 10 koduarvuti enam turvaparandusi.

**Windows 11 versioonid.** Windows 11 26H2 ilmus 29. septembril 2026 ja jõudis oktoobri lõpuks kõigile sobivatele 24H2 ja 25H2 seadmetele ([Pureinfotech](https://pureinfotech.com/windows-11-26h2-released-force-install/), [Windows Latest](https://www.windowslatest.com/2026/10/27/microsoft-confirms-windows-11-26h2-is-now-available-for-everyone-and-it-installs-in-under-five-minutes/)). Kodu- ja Pro-versiooni tugi kestab kuni 9. oktoobrini 2028, Enterprise- ja Education-versioonil kuni 8. oktoobrini 2029 ([PCWorld](https://www.pcworld.com/article/3270715/windows-11-26h2-is-now-open-to-everyone-dont-expect-big-changes.html)). Erandlik on 26H1: see on mõeldud ainult uutele Snapdragon X2 protsessoriga seadmetele ega ole olemasolevate arvutite uuendus ([Microsoft Tech Community](https://techcommunity.microsoft.com/blog/Windows-ITPro-blog/what-to-know-about-windows-11-version-26h1/4491941)).

**Windows 11 miinimumnõuded** on 64-bitine kahetuumaline 1 GHz protsessor, 4 GB RAM, 64 GB salvestusruumi, TPM 2.0 ja Secure Boot ([Pureinfotech](https://pureinfotech.com/windows-11-25h2-system-requirements/)).

### 1.5 Windows Server

Windows Server kasutab sama NT-tuuma, kuid on häälestatud teenuste pakkumiseks: Active Directory, DNS, DHCP, failiteenused, IIS, Hyper-V. Serveril on pikk tugiperiood (LTSC, umbes 10 aastat), väiksem graafiline koormus (Server Core) ja litsentseerimine tuumade järgi.

Windows Server 2025 (üldkasutuses alates novembrist 2024) uuendused:

- **hotpatching** – turvauuendused paigaldatakse ilma taaskäivituseta; Standard- ja Datacenter-väljaandes Azure Arci kaudu ([Petri](https://petri.com/windows-server-2025-generally-available/));
- **SMB over QUIC** kõigis väljaannetes ja SMB-allkirjastamine vaikimisi nõutud väljuvatel ühendustel;
- uus Active Directory domeeni- ja metsatase, Hyper-V-s vaikimisi 2. põlvkonna virtuaalmasinad, GPU jagamine;
- eemaldatud või aegunud: WordPad, IIS 6 haldusliides, SMTP server, PowerShell 2.0 mootor; aegunuks kuulutatud ka WMIC, NTLM ja WSUS ([Petri](https://petri.com/windows-server-2025-generally-available/)).

### Näide: kooli arvutiklass 2026. aasta sügisel

Koolis on 20 arvutit: 12 Windows 11 24H2, 6 Windows 10 22H2 ja 2 vana masinat, millel peab töötama 1990. aastate DOS-i õppeprogramm.

- **Windows 11 24H2** – uuenda 26H2-le, sest 24H2 Home/Pro tugi lõppes 13.10.2026.
- **Windows 10** – tarbija-ESU lõppes 13.10.2026; arvutid tuleb uuendada Windows 11-le (kui riistvara lubab), viia organisatsiooni ESU alla või välja vahetada. Ilma uuendusteta arvutit ei tohi võrku lubada.
- **DOS-programm** – 64-bitine Windows 16-bitist koodi ei käita (ptk 5.4). Lahendus on emulaator (nt DOSBox) või võrgust eraldatud virtuaalmasin, mitte vana Windows võrgus.

Näide näitab, et OS-i versioon pole ainult mugavuse küsimus: see määrab turvauuendused, riistvaranõuded ja rakenduste ühilduvuse.

### Kokkuvõte

- Tänane Windows põhineb Windows NT tuumal, mitte MS-DOS-il.
- Windows 11 nõuab riistvaralist turvet (TPM 2.0, Secure Boot) ja saab üks kord aastas funktsiooniuuenduse.
- Windows 10 on 2026. aasta sügisest tarbijatele toetuseta; organisatsioonid saavad ESU-ga aega osta.
- Windows Server 2025 toob uuendused ilma taaskäivituseta ja liigub NTLM-ist eemale.

### Kontrollküsimused

1. Mis oli MS-DOS-i roll esimestes Windowsi versioonides?
2. Miks arendas Microsoft Windows NT, kuigi Windows 95 oli populaarne?
3. Nimeta kolm erinevust Windows NT ja Windows 9x vahel.
4. Mis vahe on Windows 11 kliendiversioonil ja Windows Serveril?
5. Mida tähendab „Windows as a Service“ ja kuidas see mõjutab IT-haldurit?
6. Mida peab organisatsioon tegema, kui tal on 2026. aastal veel Windows 10 arvuteid?
7. Miks on Windows 11 riistvaranõuded rangemad kui Windows 10 omad?

### Valikküsimused

1. Mis aastal ilmus Windows NT 3.1? a) 1985 b) 1990 c) 1993 d) 1995
2. Millal lõppes Windows 10 tasuta tugi? a) 14.01.2020 b) 14.10.2025 c) 13.10.2026 d) 10.10.2028
3. Milline väide Windows 11 26H1 kohta on õige? a) see on kõigile tasuta funktsiooniuuendus b) see on eelinstallitud vaid valitud uutele seadmetele c) see on serveriversioon d) see on Windows 12 testversioon
4. Mida võimaldab hotpatching Windows Server 2025-s? a) draiverite automaatset vahetamist b) turvauuendusi ilma taaskäivituseta c) virtuaalmasinate kiiret kloonimist d) Active Directory varundamist

### Praktiline ülesanne

Ava PowerShell ja selgita väljundit:

```powershell
Get-ComputerInfo -Property OsName, OsVersion, OsBuildNumber, WindowsVersion, OsArchitecture
winver
```

Leia [Windows release health](https://learn.microsoft.com/windows/release-health/) lehelt, mis kuupäeval lõpeb sinu versiooni tugi.

### Iseseisvad ülesanded

**1. Arvutipargi inventuur.** Kogu kodu, kooli või töökoha vähemalt viie arvuti andmed: Windowsi väljaanne, versioon (nt 24H2), järk (build), protsessor, TPM-i olemasolu. Leia Microsofti elutsükli lehelt iga versiooni toe lõppkuupäev. **Esita:** tabel ja iga arvuti kohta soovitus (jätka, uuenda, vaheta välja) koos ühe lause põhjendusega.

**2. Ajajoon ja põhjendus.** Koosta oma ajajoon ühe Windowsi versiooni kohta, mida oled ise kasutanud (nt Windows 7 või 10): ilmumise aasta, kolm uut võimalust, toe lõpp ja mis sai selle järel kasutajatest. **Esita:** pool lehekülge teksti ja vastus küsimusele „Miks ei saa Microsoft vana versiooni lõputult toetada?“

### Viited

- Krogh, E. (2017). *An Introduction to Windows Operating System*, 2. tr, ptk 1. Bookboon.
- Yosifovich, P., Ionescu, A., Russinovich, M., Solomon, D. (2017). *Windows Internals*, 7. tr, 1. osa, ptk 1. Microsoft Press.
- Allikad lingitud tekstis.

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← Sissejuhatus](00-sissejuhatus.md) · [Sisukord](../README.md) · [2. Operatsioonisüsteemi ülesanded →](02-os-i-ulesanded.md)
