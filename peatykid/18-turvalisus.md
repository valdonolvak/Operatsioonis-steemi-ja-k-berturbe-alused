[← 17. Võrguvõimalused Windowsis](17-vork.md) · [Sisukord](../README.md) · [19. Kui Windows jookseb kokku →](19-krahhid.md)

## 18. Windowsi turvalisus

Windowsi turvamudel põhineb kolmel sambal: **identiteet** (kes sa oled – SID ja token), **juurdepääsukontroll** (mida sa tohid – turvakirjeldaja ja ACL) ning **audit** (mida sa tegid – turvalogi). 2026. aasta Windows lisab neile riistvaralise usaldusahela (TPM, Pluton, Secure Boot), paroolivaba autentimise ja rakenduste kontrolli.

### 18.1 Turvalisus arvutisüsteemides

Infoturbe eesmärgid on **konfidentsiaalsus**, **terviklus** ja **käideldavus** (CIA). OS peab kaitsma andmeid teiste kasutajate, pahavara ja füüsilise varguse eest. Iga OS-i turvamehhanism vastab küsimusele: kes, mida, millise objektiga ja kas see logitakse.

### 18.2 Turvalisus Windows NT-s

NT turvakomponendid:

| Komponent | Roll |
| --- | --- |
| SRM (Security Reference Monitor) | tuumas: kontrollib iga objekti avamist, loob auditikirjeid |
| LSA (`lsass.exe`) | autentimine, turvapoliitika, tokenite loomine |
| SAM | kohalike kontode andmebaas (registrisse peidetud) |
| Active Directory / Entra ID | domeeni- ja pilvekontod |
| Winlogon ja LogonUI | sisselogimise kasutajaliides |
| Credential Guard (`LsaIso.exe`) | parooliräsid ja Kerberose piletid VTL 1-s |

### 18.3 Turvamehhanismid Windowsis

**SID** (Security Identifier) on kasutaja, rühma või arvuti unikaalne tunnus, nt `S-1-5-21-…-1001`. Tuntud SID-id: `S-1-5-18` (LocalSystem), `S-1-5-32-544` (Administrators).

**Juurdepääsutoken** (access token) luuakse sisselogimisel ja kinnitatakse iga protsessi külge. Sisaldab kasutaja SID-i, rühmade SID-e, privileege (nt `SeDebugPrivilege`, `SeBackupPrivilege`) ja **terviklikkuse taset** (Low, Medium, High, System).

**Turvakirjeldaja** (security descriptor) on igal objektil: omanik, **DACL** (kes mida tohib – ACE-de loend „luba“/„keela“) ja **SACL** (mida auditeerida). Juurdepääsu kontrollil võrdleb SRM tokenit DACL-iga; esimene sobiv keelav ACE võidab.

> 📊 **Joonis:** juurdepääsu kontroll · token, turvakirjeldaja, SRM, tulemus ja audit

Joonis: SRM võrdleb tokeni SID-e DACL-i kirjetega; Mari on Õpetajate rühmas ja saab muutmisõiguse, õpilase puhul võidaks keelav kirje. SACL määrab, mida turvalogisse kirjutatakse.

**UAC** (User Account Control): administraator töötab vaikimisi kahe tokeniga – tavalisega ja täieliku administraatori omaga, mida kasutatakse ainult kinnituse järel. **Administraatori kaitse** (Administrator protection) on UAC-i järglane: kasutajal pole püsivaid administraatoriõigusi, vaid iga tõstmise jaoks luuakse Windows Helloga kinnitatud ajutine eraldi konto token. Funktsioon jõudis Windows 11-sse 2026. aasta augusti lõpus vaikimisi väljalülitatuna ([Itelio / Microsoft Message Center](https://www.itelio.com/en/microsoft-message-center/mc1444375)), ja oktoobri 2026 turvauuendusest on see Windows 11 turvabaasjoontes vaikimisi sisse lülitatud ([Pupuweb / MC1506291](https://pupuweb.com/mc1506291-manage-administrator-protection-within-windows-11-security-baseline/)).

**Autentimine tänapäeval:**

- **Windows Hello / Windows Hello for Business** – PIN, näo- või sõrmejäljetuvastus; võti on seotud TPM-iga, parool võrgus ei liigu;
- **pääsvõtmed** (passkeys, FIDO2) – paroolivaba ja õngitsuskindel veebiautentimine;
- **LAPS** – iga arvuti kohaliku administraatori parool on erinev ja vahetub automaatselt (Windows LAPS on OS-i sisse ehitatud);
- **LSA kaitse** – lsass.exe töötab kaitstud protsessina (PPL), mis takistab paroolide varastamist mälust.

**Riistvaraline usaldusahel:**

- **UEFI Secure Boot** – käivitatakse ainult allkirjastatud alglaadur;
- **Measured Boot ja TPM** – käivitusahela mõõtmised salvestatakse TPM-i, BitLocker ja kaugkinnitus (attestation) kasutavad neid;
- **Microsoft Pluton** – protsessorisse integreeritud turvaprotsessor, mille püsivara uuendatakse Windows Update’i kaudu ([Redmond Magazine](https://redmondmag.com/articles/2022/04/07/microsoft-outlines-new-security-protections-coming-to--windows-11.aspx)).

> 📊 **Joonis:** turvaline käivitus · Secure Boot, Measured Boot, TPM, BitLocker, kaugkinnitus

Joonis: Secure Boot peatab allkirjata koodi, Measured Boot salvestab iga sammu räsi TPM-i. Kui keegi muudab alglaadurit, ei klapi mõõtmised – BitLocker küsib taastevõtit ja Intune märgib seadme mitteühilduvaks.

**Tähelepanu 2026: Secure Booti sertifikaatide vahetus.** 2011. aastal väljastatud Microsofti Secure Booti sertifikaadid aeguvad: KEK CA 2011 24. juunil, UEFI CA 2011 27. juunil ja Windows Production PCA 2011 19. oktoobril 2026; asemele tulevad 2023. aasta sertifikaadid ([Microsoft Support](https://support.microsoft.com/help/5062710), [UW–Madison KB](https://kb.wisc.edu/159935)). Seade, mis uusi sertifikaate ei saa, ei saa enam Secure Booti ja alglaaduri turvaparandusi. Kontroll PowerShellis on praktilises ülesandes.

### 18.4 Kaitse nuhkvara ja pahavara eest

Algse õpiku Windows Defender on nüüd **Microsoft Defender Antivirus**, mis kasutab:

- allkirju ja heuristikat;
- **pilvekaitset** ja masinõpet – tundmatu fail saadetakse sekunditega pilve analüüsiks;
- **käitumisjälgimist** ja **AMSI**-t (Antimalware Scan Interface) – skriptid (PowerShell, VBA, JavaScript) kontrollitakse enne käivitamist ka siis, kui need on hajutatud;
- **ründepinna vähendamise reegleid** (ASR) – nt keelab Office’il lapsprotsesside loomise;
- **kontrollitud kaustale juurdepääsu** – lunavarakaitse;
- **rikkumiskaitset** (Tamper Protection) – pahavara ei saa Defenderit välja lülitada.

Organisatsioonides lisandub **EDR** (nt Microsoft Defender for Endpoint), mis kogub telemeetriat ja võimaldab intsidendile reageerida.

**Rakenduste kontroll:**

- **Smart App Control** – lubab käivitada ainult allkirjastatud või pilve AI-mudeli järgi usaldusväärseid rakendusi ning blokeerib internetist tulnud skriptid; saadaval puhtal paigaldusel ([Dark Reading](https://www.darkreading.com/remote-workforce/microsoft-details-new-security-features-for-windows-11), [Petri](https://petri.com/windows-11-2022-update-security-features/));
- **App Control for Business** (endine WDAC) ja **AppLocker** – organisatsiooni lubatud rakenduste poliitika;
- **haavatavate draiverite blokeerimisloend** – vaikimisi sees;
- **SmartScreen** – hoiatab tundmatute allalaadimiste ja õngitsuslehtede eest.

### 18.5 Viirusekaitse

Pahavara liigid: viirus (nakatab faile), uss (levib ise võrgus), troojalane (maskeerub kasulikuks), lunavara (krüpteerib ja väljapressib), nuhkvara, juurkomplekt (rootkit, peidab end tuumas), botid. Kaasaegne pahavara on sageli **failivaba**: kasutab PowerShelli, WMI-d ja teisi süsteemi enda tööriistu („living off the land“).

Kasutaja saab skannida Windowsi turbe rakenduses (kiir-, täis-, kohandatud ja **võrguühenduseta skannimine**, mis käivitub enne Windowsi laadimist ja leiab juurkomplekte).

### 18.6 Windowsi tulemüür

**Windows Defender Firewall** on olekupõhine (stateful) hostitulemüür, mis töötab WFP peal. Omadused:

- profiilid **domeen**, **privaatne**, **avalik** – eri reeglid eri võrkudes;
- sissetulev liiklus vaikimisi keelatud, väljaminev lubatud;
- reeglid programmi, pordi, protokolli, IP-aadressi, teenuse ja kasutaja järgi;
- **IPsec-ühenduse turvareeglid**;
- logimine faili `pfirewall.log`.

Pakett lubatakse, kui see kuulub olemasolevasse ühendusse või vastab lubavale reeglile; keelav reegel võidab lubava.

### 18.7 Windows Update

Windows saab:

- **kumulatiivse kvaliteediuuenduse** iga kuu teisel teisipäeval („Patch Tuesday“) ja valikulise eelvaate neljandal nädalal;
- **funktsiooniuuenduse** kord aastas (Windows 11 26H2 jne), enamasti väikese lüliti-paketina (enablement package);
- **draiveri- ja püsivarauuendusi**, Defenderi allkirju mitu korda päevas;
- **hotpatch** – Windows 11 Enterprise 24H2+ x64 seadmetel alates aprillist 2025 turvauuendused ilma taaskäivituseta, kvartaalse tsükliga (baaskuu taaskäivitusega, kaks hotpatch-kuud) Autopatchi kaudu ([Microsoft Tech Community](https://techcommunity.microsoft.com/blog/windows-itpro-blog/hotpatch-for-client-frequently-asked-questions/4413582)).

Haldusvahendid: **Windows Autopatch** ja **Intune** (pilv), **WSUS** (aegunud, kuid veel olemas), **Configuration Manager**. **Kohaletoimetamise optimeerimine** (Delivery Optimization) jagab uuendusi arvutite vahel.

### Võrdlus: Linux ja macOS

Kõigil kolmel on kasutajad, rühmad ja failide õigused, kuid lisakaitsed on eri nimedega ja kohati eri loogikaga.

|  | Windows | Linux | macOS |
| --- | --- | --- | --- |
| Kasutaja tunnus | SID | UID, GID | UID, GID |
| Administraator | Administrators; UAC / administraatori kaitse | `root`; `sudo` | admin-rühm; `sudo`, parooli või Touch ID kinnitus |
| Failiõigused | DACL / SACL | `rwx` (omanik, rühm, teised) + POSIX ACL | `rwx` + ACL |
| Kohustuslik juurdepääsukontroll | terviklikkuse tasemed, AppContainer | SELinux, AppArmor | System Integrity Protection, liivakast (sandbox) |
| Rakenduste usaldus | Smart App Control, App Control, SmartScreen | paketihalduri allkirjad, distro hoidlad | Gatekeeper, notariseerimine, XProtect |
| Privaatsusload | Seaded → Privaatsus ja turve | sõltub distrost ja paketivormingust | TCC: kaamera, mikrofon, failid luba rakenduse kaupa |
| Ketta krüpteerimine | BitLocker + TPM | LUKS (+ TPM) | FileVault + Secure Enclave |
| Käivituse kaitse | Secure Boot, Measured Boot, TPM, Pluton | Secure Boot (shim), TPM | Apple Silicon Secure Boot, Secure Enclave |

**Mida sellest õppida:** põhimõtted on samad – vähimad õigused, allkirjastatud kood, krüpteeritud ketas, riistvaraline usaldusahel. Linuxi `chmod 777` on sama vale nagu Windowsis õigus „Everyone: Full control“.

### Näide: hindefail ja programmi paigaldamine

**Juurdepääs failile.** Failiserveris on kaust `\\srv\Hinded`. Õpilane Jaan avastab kausta tee ja proovib avada faili `hinded.xlsx`.

1. Jaani tokenis on SID-id `KOOL\jaan`, `Õpilased`, `Domain Users`.
2. SRM loeb faili DACL-i: esimene sobiv kirje on „keela Õpilased – kirjuta“; lugemiseks sobib „luba Domain Users – loe“.
3. Jaan saab faili avada ainult lugemiseks. Parem lahendus: õpilastele ei anta kausta kõige vähematki õigust (vähimate õiguste põhimõte) ja kausta lisatakse SACL, mis logib ebaõnnestunud katsed (sündmus 4656/4663).

**Õiguste tõstmine.** Mari (kohalik administraator) laadib alla tasuta PDF-redaktori ja käivitab paigaldaja.

- **UAC-ga:** ilmub „Kas lubate sellel rakendusel teha muudatusi?“ – Mari klõpsab „Jah“ ja paigaldaja saab täieliku administraatori tokeni. Pahavara, mis juba Mari kontol töötab, võib sama nuppu imiteerida või UAC-st mööda hiilida.
- **Administraatori kaitsega:** Windows küsib Windows Hello kinnitust (PIN või nägu) ja käivitab paigaldaja eraldi ajutise süsteemi loodud kontoga. Kui paigaldus lõpeb, kaob ka administraatoriõigus. Mari igapäevaseanss ei hoia kunagi administraatori tokenit.

Kontrolli ise: `whoami /groups` näitab tavalises PowerShellis *Mandatory Label\\Medium*, administraatorina käivitatuna *High*.

### Kokkuvõte

- Windowsi juurdepääsukontroll: token (kes) + turvakirjeldaja (mida tohib) + SRM (kontroll).
- Administraatori kaitse, Windows Hello, LAPS ja Credential Guard vähendavad paroolide ja õiguste kuritarvitamist.
- Defender, Smart App Control, ASR ja tulemüür moodustavad kihilise kaitse.
- Uuendused on kaitse alus; 2026 on Secure Booti sertifikaatide vahetuse aasta.

### Kontrollküsimused

1. Miks on arvutisüsteemis vaja turvalisust? Selgita CIA-kolmikut.
2. Mis ülesanne on SID-il?
3. Mida sisaldab juurdepääsutoken?
4. Mis ülesanne on turvakirjeldajal? Mis vahe on DACL-il ja SACL-il?
5. Kuidas töötab UAC ja mille poolest erineb sellest administraatori kaitse?
6. Milliseid võimalusi on kasutajal arvuti viiruste suhtes skannida?
7. Milliste mehhanismidega avastab Microsoft Defender pahavara?
8. Mis on Smart App Control?
9. Selgita lühidalt, kuidas Windowsi tulemüür pakette filtreerib.
10. Mis on Windows Update’i ülesanne ja kuidas see töötab? Mis on hotpatch?
11. Miks peab IT-administraator 2026. aastal kontrollima Secure Booti sertifikaate?

### Valikküsimused

1. Mis on `S-1-5-18`? a) Administrators b) LocalSystem c) Guest d) Everyone
2. Kus on kirjas, kes võib faili lugeda? a) SACL b) DACL c) SAM d) token
3. Mis takistab pahavaral Defenderi väljalülitamist? a) AMSI b) Tamper Protection c) SmartScreen d) ASR
4. Mis muudab kohaliku administraatori parooli igal arvutil erinevaks? a) LAPS b) BitLocker c) Hello d) UAC
5. Millal on Patch Tuesday? a) iga kuu esimesel esmaspäeval b) iga kuu teisel teisipäeval c) igal reedel d) kord kvartalis

### Praktiline ülesanne

```powershell
whoami /all
Get-Acl C:\Windows\System32\drivers\etc\hosts | Format-List
Get-MpComputerStatus | Select-Object AMServiceEnabled, RealTimeProtectionEnabled, IsTamperProtected, AntivirusSignatureLastUpdated
Get-NetFirewallProfile | Select-Object Name, Enabled, DefaultInboundAction, DefaultOutboundAction
Confirm-SecureBootUEFI
[System.Text.Encoding]::ASCII.GetString((Get-SecureBootUEFI db).bytes) -match 'Windows UEFI CA 2023'
Get-HotFix | Sort-Object InstalledOn -Descending | Select-Object -First 5
```

Vasta: milline on sinu PowerShelli protsessi terviklikkuse tase? Kas Tamper Protection on sees? Kas arvutis on uus Secure Booti sertifikaat? Loo tulemüürireegel, mis blokeerib Notepadi väljamineva liikluse, ja kustuta see.

### Iseseisvad ülesanded

**1. Õiguste kujundamine.** Loo õppe-VM-is kohalikud rühmad `Õpetajad` ja `Õpilased` ning kasutajad `mari` ja `jaan`. Loo kaust `C:\Kool\Hinded` ja määra õigused nii, et õpetajad saavad muuta, õpilased ei näe kausta üldse ja kõik kustutamised logitakse (SACL + auditipoliitika). Kontrolli tulemust mõlema kasutajana ja vaata turvalogi. **Esita:** `icacls C:\Kool\Hinded` väljund, ekraanipilt sündmusest 4663 ja lühike selgitus, miks kasutasid rühmi, mitte üksikkasutajaid.

**2. Turvabaasjoone võrdlus.** Lae alla Microsoft Security Compliance Toolkit ja käivita *Policy Analyzer*, võrreldes oma õppe-VM-i seadistust Windows 11 turvabaasjoonega. Vali erinevuste hulgast viis, mis on sinu arvates kõige olulisemad. **Esita:** tabel „baasjoone säte – praegune väärtus – soovitatud väärtus – mille vastu kaitseb“.

### Viited

- Krogh (2017), ptk 18.
- Yosifovich jt, *Windows Internals* 7. tr, 1. osa, ptk 7 „Security“.
- Microsoft Learn: [Windows security](https://learn.microsoft.com/windows/security/); [Windows security baselines](https://learn.microsoft.com/windows/security/operating-system-security/device-management/windows-security-configuration-framework/windows-security-baselines).
- Allikad lingitud tekstis.

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 17. Võrguvõimalused Windowsis](17-vork.md) · [Sisukord](../README.md) · [19. Kui Windows jookseb kokku →](19-krahhid.md)
