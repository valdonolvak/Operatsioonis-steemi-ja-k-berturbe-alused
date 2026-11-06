[← 6. Andmehaldus: register ja konfiguratsioonihaldur](06-register.md) · [Sisukord](../README.md) · [8. Lõimede sünkroniseerimine →](08-sunkroniseerimine.md)

## 7. Protsessid, lõimed ja tööd

Protsess on ressursside konteiner, lõim on täitmise üksus ning töö (job) on protsesside rühm, millele saab seada ühiseid piiranguid. Neid kolme kasutavad ka tänapäeva isolatsioonitehnoloogiad: AppContainer, Windowsi konteinerid ja Windows Sandbox.

### 7.1 Protsessid

Protsessil on:

- **privaatne virtuaalne aadressiruum** (x64-l kasutajarežiimile 128 TB);
- **käivitatav programm** (EXE ja laaditud DLL-id);
- **pidemetabel** – avatud objektid;
- **turvakontekst ehk juurdepääsutoken** – kasutaja SID, rühmad, privileegid, terviklikkuse tase;
- **protsessi ID (PID)** ja vanemprotsessi ID;
- **vähemalt üks lõim**.

Tuumas kirjeldab protsessi struktuur **EPROCESS**, kasutajarežiimis **PEB** (Process Environment Block).

**Protsessi loomine** (`CreateProcess`) käib etappidena: avatakse EXE-fail, luuakse protsessiobjekt ja aadressiruum, luuakse esimene lõim, teavitatakse alamsüsteemi (csrss.exe), laaditakse vajalikud DLL-id ja algab täitmine. Turvakontrollid (Smart App Control, Defender, AppLocker / App Control for Business) saavad käivitamise selles etapis peatada.

> 📊 **Joonis:** protsessi loomine · kuus etappi

Joonis: juba esimeses etapis saab turvatarkvara käivituse peatada; teises etapis saavad draiverid ja EDR teate uuest protsessist, mistõttu jääb iga käivitus turvalogidesse.

**Protsesside tüübid tänapäeval:**

| Tüüp | Kirjeldus |
| --- | --- |
| Tavaline protsess | täielik Win32 protsess |
| Kaitstud protsess (PP/PPL) | isegi administraator ei saa selle mälu lugeda; nt lsass.exe LSA kaitsega, viirusetõrje |
| AppContainer | piiratud liivakast Store’i rakendustele ja brauseri sisuprotsessidele |
| Minimaalne / Pico-protsess | ilma Windowsi DLL-ideta, nt WSL 1 Linuxi protsessid |
| Trustlet (IUM) | töötab VTL 1-s, nt LsaIso.exe |

### 7.2 Lõimed

Lõimel on oma **registrite olek**, **kaks pinu** (kasutaja- ja tuumapinu), **prioriteet**, **lõime lokaalne mälu** (TLS) ja olek. Tuumas kirjeldab lõime **ETHREAD/KTHREAD**, kasutajarežiimis **TEB**.

Lõime põhiolekud: **valmis** (ready, ootab protsessorit), **töötab** (running), **ootel** (waiting, ootab sündmust, nt S/V lõppu), **lõpetatud** (terminated). Planeerija liigutab lõime nende olekute vahel (ptk 9).

> 📊 **Joonis:** lõime olekud · valmis, töötab, ootel, lõpetatud

Joonis: lõim ei lähe ootelt otse tööle – kui sündmus saabub, muutub ta valmis-olekusse ja ootab, kuni planeerija ta valib.

Mitmelõimeline programm saab kasutada mitut tuuma ja hoida kasutajaliidese reageerivana, kui raske töö käib taustalõimes. Kaasaegses programmeerimises ei looda lõimi tavaliselt käsitsi, vaid kasutatakse **lõimekogumit** (thread pool) ja kõrgema taseme vahendeid: C# `async/await`, `Task`, C++ `std::jthread`.

### 7.3 Kiud

**Kiud** (fiber) on kasutajarežiimis hallatav kerge täitmisüksus: programm ise otsustab, millal ühe kiu töö peatada ja teisega jätkata. Tuum kiude ei näe. Kiude kasutati vanades serverirakendustes (nt SQL Server); tänapäeval täidavad sama rolli **korutiinid** ja asünkroonne programmeerimine. Sarnane mehhanism on **UMS** (User-Mode Scheduling), mis on Windows 11-s eemaldatud.

### 7.4 Tööd

**Töö** (job object) koondab protsesse, et neid koos hallata: piirata mälu- ja protsessorikasutust, keelata uute protsesside loomist, lõpetada kõik korraga. Tööobjektid on aluseks:

- **Windowsi konteineritele** (serveri konteinerid, ptk 20);
- **brauserite liivakastidele** (Edge ja Chrome piiravad sisuprotsesse tööobjektidega);
- **WSL 2 ja Windows Sandboxi** ressursipiirangutele.

### 7.5 Protsessihaldur

**Protsessihaldur** (Process Manager) on käivitava süsteemi osa, mis loob ja lõpetab protsesse, lõimi ja töid. Ta ei planeeri lõimi – see on tuuma töö. Protsessihaldur võimaldab draiveritel registreerida teavitusi protsesside loomise kohta; nii saab EDR-tarkvara näha iga uut protsessi.

### 7.6 Mitmeülesandelisus

**Mitmeülesandelisus** (multitasking) tähendab, et mitu programmi näivad töötavat samaaegselt. Ühe tuumaga tekitab illusiooni kiire kontekstivahetus; mitme tuumaga töötavad lõimed päriselt paralleelselt. Windows kasutab **eelistavat** (preemptive) mitmeülesandelisust: OS võib lõime peatada igal ajal, ilma et lõim peaks protsessorit ise loovutama (ptk 9).

### 7.7 Protsessidevaheline suhtlus

Protsessid on teineteisest eraldatud, seega vajavad nad suhtlemiseks OS-i abi. Windowsi IPC-mehhanismid:

| Mehhanism | Kasutus |
| --- | --- |
| Jagatud mälu (failivastendus, memory-mapped file) | suured andmemahud, kõige kiirem |
| Nimega toru (named pipe) | kliendi-serveri suhtlus samas arvutis või võrgus (SMB kaudu) |
| Anonüümne toru | vanem- ja lapsprotsessi suhtlus |
| Postkastipesa (mailslot) | ühesuunaline teavitus, aegunud |
| Pesa (socket) | TCP/UDP võrgusuhtlus, ka Unixi domeenipesad (AF\_UNIX) |
| ALPC | kiire sõnumivahetus OS-i komponentide vahel |
| RPC / COM | kaugprotseduuride väljakutse |
| Aknasõnumid, lõikelaud | graafilised rakendused |

IPC on ka ründepind: nimega torud ja RPC-liidesed peavad kontrollima kliendi õigusi, muidu saab neid kasutada õiguste tõstmiseks.

### Võrdlus: Linux ja macOS

Protsessi ja lõime mõisted on samad, kuid loomise viis erineb. Unixi-laadsetes süsteemides (Linux, macOS) luuakse protsess kahe sammuga: `fork()` teeb vanemprotsessist koopia ja `exec()` laadib koopiasse uue programmi. Windowsi `CreateProcess` teeb sama ühe käsuga.

|  | Windows | Linux | macOS |
| --- | --- | --- | --- |
| Protsessi loomine | `CreateProcess` | `fork()` + `exec()` | `fork()` + `exec()`, `posix_spawn()` |
| Lõim | eraldi objekt (ETHREAD) | ülesanne (`task_struct`), luuakse `clone()`-ga | Machi lõim + POSIX-lõim |
| Teenuste haldus | services.exe (SCM) | systemd | launchd |
| Ressursipiirangud | tööobjekt (job) | cgroups v2 | launchd piirangud, QoS-klassid |
| Isolatsioon / konteinerid | tööobjektid + objektide nimeruumid | nimeruumid (namespaces) + cgroups | omakeelseid konteinereid pole; Docker kasutab Linuxi VM-i |
| Tööriistad | Tegumihaldur, `Get-Process` | `ps`, `top`, `htop`, `/proc` | Activity Monitor, `ps`, `top` |

**Mida sellest õppida:** Docker on Linuxi tuuma nimeruumide ja cgroupide kasutajaliides. Seetõttu töötavad Linuxi konteinerid Windowsis ja macOS-is tegelikult Linuxi virtuaalmasinas (WSL 2 või Docker Desktopi VM).

### Näide: restoraniköök ja brauser

**Võrdlus.** Restoran on arvuti, iga köök on **protsess**: oma ruum (aadressiruum), oma külmkapid ja nõud (pidemed) ning luba teatud toiduaineid kasutada (token). **Lõimed** on kokad samas köögis: nad jagavad köögi asju ja peavad omavahel kokku leppima, kes millist potti kasutab (ptk 8). **Töö** (job) on peokorraldus: mitu kööki saavad ühise eelarve ja tunnipiirangu ning kui pidu jäetakse ära, lõpetatakse korraga kõigi töö.

**Windowsis.** Ava Edge 10 vahekaardiga ja vaata Process Exploreris: näed üht peaprotsessi ning mitut renderdusprotsessi, GPU-protsessi ja võrguteenuse protsessi.

- Iga vahekaart töötab eraldi **AppContaineris** madala terviklikkuse tasemega. Kui veebilehe kood leiab brauserist vea, ei pääse see kasutaja failideni.
- Vahekaardi kokkujooksmine ei sulge kogu brauserit.
- Sisuprotsessid on **tööobjektis**, mis piirab nende ressursikasutust.
- Edge ja muud komponendid suhtlevad omavahel **IPC** kaudu (nimega torud, jagatud mälu).

Sama mudelit kasutab Windows ise: teenused jagatakse eri `svchost.exe` protsessidesse, et ühe teenuse viga ei viiks kaasa teisi.

### Kokkuvõte

- Protsess = aadressiruum + ressursid + turvatoken; lõim = täitmine.
- Tööobjektid rühmitavad protsesse ja on konteinerite aluseks.
- Kaitstud protsessid ja AppContainer piiravad ka administraatori ning pahavara võimalusi.
- IPC on vajalik, kuid ka turvarisk.

### Kontrollküsimused

1. Mis kuulub protsessi juurde?
2. Kirjelda protsessi loomise etappe.
3. Mis vahe on protsessil ja lõimel?
4. Nimeta lõime põhiolekud.
5. Mis on kiud ja millega on need tänapäeval asendunud?
6. Milleks kasutatakse tööobjekte?
7. Mis on kaitstud protsess (PPL) ja milleks seda vaja on?
8. Nimeta neli protsessidevahelise suhtluse mehhanismi.

### Valikküsimused

1. Mis kirjeldab protsessi turvakonteksti? a) PEB b) juurdepääsutoken c) pidemetabel d) TEB
2. Mis on kiire IPC-mehhanism suurte andmemahtude jaoks? a) postkastipesa b) aknasõnum c) jagatud mälu d) lõikelaud
3. Kes planeerib lõimi Windowsis? a) protsessihaldur b) tuum c) objektihaldur d) teenusehaldur
4. Milline neist on konteinerite aluseks? a) kiud b) tööobjekt c) mailslot d) TLS

### Praktiline ülesanne

```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10 Name, Id, CPU, @{n='Threads';e={$_.Threads.Count}}
Get-CimInstance Win32_Process -Filter "Name='svchost.exe'" | Select-Object ProcessId, ParentProcessId, CommandLine
```

Ava Process Exploreris üks `svchost.exe` protsess ja vaata vahekaarti Services. Seejärel ava Edge mitme vahekaardiga ja leia Process Exploreris Edge’i sisuprotsessid. Millise terviklikkuse tasemega (Integrity) need töötavad? Lisa veerg „Protection“ ja leia kõik kaitstud protsessid.

### Iseseisvad ülesanded

**1. Protsessipuu uurimine.** Käivita Process Exploreris puuvaade ja järgi ahelat `System` → `smss.exe` → … → `explorer.exe` → sinu avatud rakendus. Leia, milline protsess on iga süsteemiprotsessi vanem. **Esita:** joonistatud protsessipuu (vähemalt 8 protsessi) ja lühike selgitus, miks oleks kahtlane, kui `lsass.exe` vanem oleks `winword.exe`.

**2. Tööobjekt praktikas.** Kirjuta PowerShelli skript, mis käivitab Notepadi, loeb selle PID-i, lõimede arvu ja tööhulga ning lõpetab protsessi 10 sekundi pärast. Seejärel uuri Process Exploreris (vahekaart *Job*), kas Edge’i sisuprotsessid kuuluvad tööobjekti ja millised piirangud sellel on. **Esita:** skript, selle väljund ja ekraanipilt tööobjekti piirangutest.

### Viited

- Krogh (2017), ptk 7.
- Yosifovich jt, *Windows Internals* 7. tr, 1. osa, ptk 3 ja 4.
- Microsoft Learn: [Processes and Threads](https://learn.microsoft.com/windows/win32/procthread/processes-and-threads).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 6. Andmehaldus: register ja konfiguratsioonihaldur](06-register.md) · [Sisukord](../README.md) · [8. Lõimede sünkroniseerimine →](08-sunkroniseerimine.md)
