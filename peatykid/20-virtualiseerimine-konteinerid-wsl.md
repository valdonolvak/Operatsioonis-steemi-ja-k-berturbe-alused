[← 19. Kui Windows jookseb kokku](19-krahhid.md) · [Sisukord](../README.md) · [21. UUS: Tehisintellekt operatsioonisüsteemis →](21-tehisintellekt.md)

## 20. UUS: Virtualiseerimine, konteinerid ja WSL

Virtualiseerimine laseb ühes füüsilises arvutis töötada mitmel operatsioonisüsteemil korraga; konteinerid isoleerivad rakendusi ühe OS-tuuma sees. Windows kasutab hüperviisorit nii serverites (Hyper-V) kui ka kliendi turvalisuses (VBS, ptk 4), Linuxi käitamiseks (WSL 2) ja turvaliseks testimiseks (Windows Sandbox).

### 20.1 Hüperviisorid

| Tüüp | Kirjeldus | Näited |
| --- | --- | --- |
| 1. tüüp (bare-metal) | töötab otse riistvaral, OS-id on selle peal | Hyper-V, VMware ESXi, KVM, Xen |
| 2. tüüp (hosted) | töötab tavalise OS-i rakendusena | VMware Workstation, VirtualBox |

Kaasaegsed hüperviisorid kasutavad protsessori virtualiseerimislaiendusi (Intel VT-x, AMD-V, ARM EL2) ja teisese taseme aadressitõlget (EPT/NPT), et külalis-OS töötaks peaaegu omakeelse kiirusega.

### 20.2 Hyper-V arhitektuur

Hyper-V on 1. tüüpi hüperviisor ka siis, kui see on lubatud Windows 11-s: pärast lubamist käivitub esmalt hüperviisor ja tavaline Windows muutub **juurpartitsiooniks** (root partition), millel on otsene ligipääs seadmetele. Külalis-VM-id töötavad **lapspartitsioonides**. Juur- ja lapspartitsioonid suhtlevad kiire kanali **VMBus** kaudu; külalises töötavad sünteetilised draiverid (enlightenments).

Olulised võimalused:

- **2. põlvkonna VM** – UEFI, Secure Boot, virtuaalne TPM; Windows Server 2025 Hyper-V-s vaikimisi ([Petri](https://petri.com/windows-server-2025-public-preview/));
- **pesastatud virtualiseerimine** – VM-is saab käitada omakorda hüperviisorit (õppelaborid!);
- **dünaamiline mälu, hetktõmmised** (checkpoints), **otseteisaldus** (live migration);
- **GPU jagamine** (GPU-P) VM-ide vahel;
- **varjutatud VM-id** (shielded VMs) – kaitse ka hosti administraatori eest.

VMware ja Hyper-V kooseksisteerivad: kui Windowsis on hüperviisor (Hyper-V, VBS, WSL 2) sees, kasutab VMware Workstation Windowsi hüperviisoriplatvormi API-t.

### 20.3 Konteinerid

**Konteiner** isoleerib rakenduse koos sõltuvustega, kuid jagab hosti OS-tuuma. Konteiner käivitub sekunditega ja on VM-ist palju väiksem.

> 📊 **Joonis:** virtuaalmasinad ja konteinerid · kus asub tuum

Joonis: iga VM kannab oma OS-i ja tuuma; konteinerid sisaldavad ainult rakendust ja teeke ning kasutavad hosti tuuma. Hyper-V isolatsiooniga konteiner saab oma kerge tuuma, olles nende kahe vahepealne.

|  | Virtuaalmasin | Konteiner |
| --- | --- | --- |
| Isolatsioon | oma tuum, riistvarapõhine | jagatud tuum, nimeruumid ja piirangud |
| Suurus | gigabaidid | megabaidid |
| Käivitusaeg | sekundid–minutid | sekundid |
| Erinev OS | jah | ei (Windowsi konteiner vajab Windowsi tuuma) |

Windowsi konteinerite isolatsiooniviisid:

- **protsessi isolatsioon** – nagu Linuxi konteinerid; kasutab tööobjekte (ptk 7.4), objektihalduri nimeruume ja failisüsteemi virtualiseerimist;
- **Hyper-V isolatsioon** – iga konteiner töötab kerges VM-is oma tuumaga; turvalisem.

Konteinereid hallatakse Dockeri või containerd abil ja orkestreeritakse **Kubernetesega** (nt AKS). Konteineri tõmmised põhinevad Microsofti alustõmmistel: Nano Server, Server Core, Windows Server.

### 20.4 Windows Sandbox

**Windows Sandbox** (Pro, Enterprise) on ühekordne kerge virtuaalmasin: avad kahtlase faili, ja kui akna sulged, kaob kõik. Sandbox kasutab hosti OS-i faile ilma koopiat tegemata ning seadistatakse `.wsb`-failiga (nt võrgu keelamine, jagatud kaust ainult lugemiseks).

### 20.5 Windowsi alamsüsteem Linuxile (WSL)

- **WSL 1** (2016) – Linuxi süsteemikutsed tõlgiti Windowsi tuumas (`lxcore.sys`, pico-protsessid);
- **WSL 2** (2019) – päris Linuxi tuum kerges Hyper-V VM-is; täielik ühilduvus, kiire failisüsteem Linuxi poolel;
- **WSLg** – Linuxi graafilised rakendused Windowsi aknas;
- **GPU-kiirendus** – nt masinõppe (CUDA) jaoks.

Mai 2025 Build-konverentsil avaldas Microsoft WSL-i lähtekoodi GitHubis; avatud on käsureatööriistad, taustateenused ja Linuxi poole deemonid, kuid mõned Windowsi komponendid (nt `lxcore.sys`) jäävad suletuks ([BleepingComputer](https://bleepingcomputer.com/news/microsoft/microsoft-open-sources-windows-subsystem-for-linux-at-build-2025)).

### 20.6 Pilvearvutid

**Windows 365 Cloud PC** ja **Azure Virtual Desktop** käitavad Windowsi andmekeskuses; kasutaja ühendub brauseri või Windows App’i kaudu. Andmed ei asu lõppseadmes, mis lihtsustab haldust ja vähendab varguse riski. Kaugtöölaua ühendus muutub ise ründepinnaks (tugev autentimine, MFA, tingimuslik juurdepääs).

### Näide: IT-õppe labor – mida kuhu panna

| Vajadus | Sobiv lahendus | Põhjus |
| --- | --- | --- |
| Igal õpilasel oma domeen (DC, DNS, DHCP, klient) | VM-id VMware’is või Hyper-V-s, hetktõmmisega | vaja täisväärtuslikku Windows Serverit ja oma võrku; vea korral taastatakse hetktõmmisest |
| Hyper-V harjutused õpilase VM-is | pesastatud virtualiseerimine | hüperviisor hüperviisori sees |
| Veebirakendus (nt WordPress ja MySQL) | konteinerid Docker Compose’iga | käivitub sekunditega, sama keskkond igal õpilasel |
| Kahtlane e-kirja manus | Windows Sandbox võrguta | kaob sulgemisel, host jääb puutumata |
| Linuxi käsud ja bash-skriptid Windowsis | WSL 2 | päris Linuxi tuum, failid kättesaadavad mõlemast poolest |
| Kodust töötamine kooli tarkvaraga | Windows 365 / Azure Virtual Desktop | andmed jäävad andmekeskusesse |

**Arutle:** miks ei sobi domeenikontrolleri harjutuseks konteiner? (Vihje: Active Directory vajab täielikku Windows Serverit, oma võrguidentiteeti ja taaskäivitusi – konteiner on mõeldud ühe rakenduse jaoks.)

### Kokkuvõte

- 1. tüüpi hüperviisor töötab otse riistvaral; Hyper-V muudab Windowsi juurpartitsiooniks.
- Konteinerid jagavad tuuma, VM-idel on oma tuum; Windows pakub mõlemat ja ka Hyper-V-isoleeritud konteinereid.
- WSL 2 ja Windows Sandbox on igapäevased virtualiseerimise rakendused kliendis.

### Kontrollküsimused

1. Mis vahe on 1. ja 2. tüüpi hüperviisoril?
2. Mis on juurpartitsioon ja VMBus Hyper-V-s?
3. Mis eelised on 2. põlvkonna VM-il?
4. Võrdle virtuaalmasinat ja konteinerit.
5. Mis vahe on protsessi isolatsioonil ja Hyper-V isolatsioonil?
6. Milleks sobib Windows Sandbox?
7. Mis vahe on WSL 1-l ja WSL 2-l?
8. Mis on Windows 365 Cloud PC eelised ja riskid?

### Valikküsimused

1. Milline on 2. tüüpi hüperviisor? a) Hyper-V b) ESXi c) VirtualBox d) KVM
2. Mis kasutab WSL 2? a) tõlget Windowsi tuumas b) päris Linuxi tuuma kerges VM-is c) Dockerit d) Cygwinit
3. Mis juhtub Windows Sandboxi andmetega pärast sulgemist? a) salvestatakse OneDrive’i b) kustutatakse c) kopeeritakse hostile d) krüpteeritakse
4. Mis Windowsi mehhanism on protsessi isolatsiooniga konteineri aluseks? a) tööobjekt b) kiud c) postkastipesa d) register

### Praktiline ülesanne

```powershell
Get-ComputerInfo -Property HyperV*
Get-WindowsOptionalFeature -Online | Where-Object FeatureName -match 'Hyper-V|Containers|Subsystem-Linux|DisposableClientVM' | Select-Object FeatureName, State
wsl --install -d Ubuntu
wsl --list --verbose
wsl uname -a
```

Loo fail `test.wsb`:

```xml
<Configuration>
  <Networking>Disable</Networking>
  <MappedFolders>
    <MappedFolder><HostFolder>C:\Kahtlane</HostFolder><ReadOnly>true</ReadOnly></MappedFolder>
  </MappedFolders>
</Configuration>
```

Käivita see ja kontrolli, et võrk puudub ja kaust on kirjutuskaitsega. VMware’i kasutajale: lisa olemasolevale Windows Serveri VM-ile pesastatud virtualiseerimine (`vhv.enable = "TRUE"`) ja paigalda selle sisse Hyper-V.

### Iseseisvad ülesanded

**1. Sama rakendus kolmel moel.** Paigalda lihtne veebiserver (nt IIS või nginx) kolmel viisil: VM-i, Windows Sandboxi ja konteinerisse (`docker run -d -p 8080:80 nginx` WSL 2 või Docker Desktopiga). Mõõda iga variandi käivitusaeg, kettakasutus ja mälukasutus. **Esita:** tabel ja soovitus, millist varianti kasutaksid õppelaboris ja millist tootmises.

**2. Hetktõmmise strateegia.** Koosta oma õppelabori VM-idele (DC, failiserver, klient) hetktõmmiste reegel: millal hetktõmmis tehakse, kui kaua seda hoitakse ja millal kustutatakse. Selgita, miks domeenikontrolleri vana hetktõmmise taastamine võib põhjustada probleeme (USN rollback) ja miks hetktõmmis ei ole varukoopia. **Esita:** reeglid tabelina ja lühiselgitus.

### Viited

- Microsoft Learn: [Hyper-V architecture](https://learn.microsoft.com/windows-server/administration/performance-tuning/role/hyper-v-server/architecture); [Windows containers](https://learn.microsoft.com/virtualization/windowscontainers/about/); [Windows Sandbox](https://learn.microsoft.com/windows/security/application-security/application-isolation/windows-sandbox/); [WSL](https://learn.microsoft.com/windows/wsl/about).
- Popek, G., Goldberg, R. (1974). Formal Requirements for Virtualizable Third Generation Architectures. *Communications of the ACM*, 17(7).
- Allikad lingitud tekstis.

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 19. Kui Windows jookseb kokku](19-krahhid.md) · [Sisukord](../README.md) · [21. UUS: Tehisintellekt operatsioonisüsteemis →](21-tehisintellekt.md)
