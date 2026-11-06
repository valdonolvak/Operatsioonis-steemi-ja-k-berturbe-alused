[← 11. Ummikud](11-ummikud.md) · [Sisukord](../README.md) · [13. Vahemälu →](13-vahemalu.md)

## 12. Mäluhaldus

Mäluhaldus annab igale protsessile oma privaatse virtuaalse aadressiruumi, vastendab selle füüsilisele mälule lehtede kaupa ja kaitseb protsesse üksteise eest. Windowsis teeb seda **mäluhaldur**; tänapäeval kuulub mäluhalduse juurde ka hulk ründekaitseid (ASLR, DEP, CFG, varjupinu).

### 12.1 Saalimine

**Saalimine** (swapping) tähendab terve protsessi viimist mälust kettale ja tagasi. Vanemates süsteemides oli see ainus viis mälust suurema töökoormusega toime tulla. Kaasaegne OS viib kettale üksikuid **lehti**, mitte tervet protsessi. Windows kasutab saalimist piiratud kujul: Store’i rakenduse peatamisel võib kogu tema tööhulga kirjutada faili `swapfile.sys`.

### 12.2 Mälumudelid

| Mudel | Kirjeldus | Probleem |
| --- | --- | --- |
| Üks pidev ala | programm saab ühe järjestikuse mälulõigu (MS-DOS) | kaitse puudub, üks programm korraga |
| Fikseeritud või muutuvad sektsioonid | mälu jagatakse osadeks | killustumine |
| Segmenteerimine | loogilised segmendid (kood, andmed, pinu) | väline killustumine; x64-l praktiliselt kasutusest väljas |
| Lehekülgedeks jaotamine (paging) | mälu jaotatakse võrdse suurusega lehtedeks (4 KB) | lehetabelid võtavad ruumi |

### 12.3 Virtuaalmälu

**Virtuaalmälu** (virtual memory) annab programmile mulje pidevast suurest mälust. Iga virtuaalaadress tõlgitakse füüsiliseks aadressiks **lehetabelite** abil. x64 protsessor kasutab 4-tasemelisi lehetabeleid (48-bitine aadress, 256 TB) ja uuemad serveriprotsessorid 5-tasemelisi (57-bitine aadress). Viimased tõlked hoitakse kiires vahemälus **TLB** (Translation Lookaside Buffer).

> 📊 **Joonis:** virtuaalmälu · kahe protsessi lehed, RAM-i raamid ja lehefail

Joonis: lehetabel tõlgib iga protsessi lehe eraldi; sama DLL võib olla mõlemal, kuid RAM-is üks kord. Kettale viidud lehe juurde tekib leheviga, reserveeritud lehel füüsilist mälu pole.

Kui programm pöördub lehe poole, mis pole füüsilises mälus, tekib **leheviga** (page fault):

- **pehme leheviga** – leht on mälus, kuid mitte protsessi tööhulgas (nt ootenimekirjas); lahendatakse kiiresti;
- **kõva leheviga** – leht tuleb lugeda kettalt (lehefailist või EXE/DLL-failist); aeglane.

**Lehekülje suurused:** tavaline 4 KB, suur leht 2 MB, hiiglaslik 1 GB. Suured lehed vähendavad TLB-möödalaskmisi andmebaasides ja virtuaalmasinates.

> 📊 **Joonis:** aadressi tõlkimine · neli lehetabeli taset ja TLB

Joonis: virtuaalaadress jaguneb viieks osaks. Neli esimest on indeksid nelja taseme lehetabelitesse, viimane on nihe lehe sees. Kuna iga möödalask nõuab nelja lisapöördumist mällu, on TLB jõudluse jaoks hädavajalik.

### 12.4 Mäluhaldus Windowsis

**Aadressiruum.** 64-bitises Windowsis on igal protsessil 128 TB kasutajaruumi ja tuumal 128 TB. 32-bitisel protsessil on 2 GB (või lipuga 4 GB 64-bitises süsteemis).

**Reserveerimine ja eraldamine.** `VirtualAlloc` võimaldab aadressivahemiku **reserveerida** (ainult aadressid) ja **eraldada** (commit – OS lubab sellele mälu või lehefaili ruumi). **Eraldatud mälu kogus** (commit charge) ei tohi ületada RAM-i ja lehefaili summat.

**Tööhulk** (working set) on protsessi lehed, mis on parajasti füüsilises mälus. Mälupuuduses vähendab tööhulga haldur tööhulki; väljavõetud lehed lähevad **ootenimekirja** (standby) või **muudetud nimekirja** (modified), kust need kirjutatakse lehefaili.

**Lehefail** `pagefile.sys` hoiab muudetud lehti, mida mälus enam ei hoita. Lehefaili ei tohi tavaliselt keelata: see on vajalik eraldatud mälu limiidi ja krahhitõmmise jaoks.

**Mälu tihendamine** (memory compression, Windows 10+): enne kettale kirjutamist tihendatakse lehed mälus; protsess *Memory Compression* hoiab tihendatud lehti. RAM-ist tihendatud lehe lugemine on palju kiirem kui SSD-lt.

**SysMain** (endine SuperFetch) laadib kasutusmustrite järgi rakendusi ette ootenimekirja.

**Ründekaitsed mäluhalduses:**

| Kaitse | Mida teeb |
| --- | --- |
| DEP / NX | andmelehti ei saa käivitada |
| ASLR | moodulite, pinu ja kuhja aadressid on juhuslikud |
| CFG (Control Flow Guard) | kaudsed hüpped ainult lubatud sihtkohtadesse |
| Riistvaraline varjupinu (CET shadow stack) | tagastusaadressi muutmine tuvastatakse |
| HVCI / mälu terviklikkus | tuumas ei saa lehte korraga kirjutada ja käivitada |

### 12.5 Mäluhaldur

**Mäluhaldur** (Memory Manager) on käivitava süsteemi komponent, mis vastendab virtuaalaadresse, lahendab leheveaid, haldab tööhulki, füüsiliste lehtede nimekirju (vaba, nullitud, ootel, muudetud) ja jagatud mälu (sektsiooniobjektid). Ta käitab ka süsteemilõimi: tööhulga haldur, muudetud lehtede kirjutaja, nulllehe lõim.

### 12.6 Kuhjahaldur

Rakendused ei kasuta väikeste objektide jaoks `VirtualAlloc`-i, vaid **kuhja** (heap). **Kuhjahaldur** (Heap Manager) jagab suure mälupiirkonna väikesteks plokkideks (`HeapAlloc`, C `malloc`, C++ `new`). Windowsis on kaks kuhjatüüpi:

- **NT-kuhi** – traditsiooniline, madala killustumisega kiht (LFH);
- **segmentkuhi** (segment heap) – uuem, turvalisem ja säästlikum; vaikimisi Store’i rakendustel, Edge’il ja paljudel süsteemiprotsessidel.

Kuhjaületäitumine ja „kasutamine pärast vabastamist“ (use-after-free) on levinud turvavead; Rust ja hallatud keeled (C#) väldivad neid.

### 12.7 Pinud

**Pinu** (stack) hoiab funktsioonide kohalikke muutujaid ja tagastusaadresse. Igal lõimel on kasutajapinu (vaikimisi reserveeritakse 1 MB) ja väike tuumapinu. Pinu kasvab vajadusel; kui see saab täis, tekib erind *stack overflow*. Pinuületäitumise rünnete vastu kasutatakse pinukanaarilinde (`/GS`) ja varjupinu.

### Võrdlus: Linux ja macOS

Kõik kolm kasutavad sama riistvaralist põhimõtet (lehetabelid, TLB), kuid mälupuuduse korral käituvad erinevalt.

|  | Windows | Linux | macOS |
| --- | --- | --- | --- |
| Lehe suurus | 4 KB, suured lehed 2 MB | 4 KB, huge pages 2 MB / 1 GB | 16 KB Apple Siliconil |
| Kettale saalimine | `pagefile.sys`, `swapfile.sys` | swap-partitsioon või -fail | dünaamilised swap-failid kaustas `/private/var/vm` |
| Mälu tihendamine | Memory Compression | zswap, zram | tihendatud mälu (compressed memory) |
| Ülejaotus (overcommit) | ei: eraldatud mälu piir | vaikimisi lubatud | lubatud |
| Kui mälu saab otsa | eraldamine ebaõnnestub, rakendus saab vea | **OOM killer** lõpetab ühe protsessi | süsteem palub rakendusi sulgeda |
| Vaatamine | Ressursimonitor, RAMMap | `free -h`, `vmstat`, `/proc/meminfo` | Activity Monitor → Memory, `vm_stat` |

**Mida sellest õppida:** Linux lubab programmidel küsida rohkem mälu, kui seda on, ja lahendab puuduse hiljem protsessi lõpetamisega. Windows keeldub juba eraldamisel. Seetõttu jälgivad Linuxi serverite administraatorid OOM killeri logisid (`dmesg`), Windowsi administraatorid aga eraldatud mälu (*Committed Bytes*) ja lehefaili suurust.

### Näide: hotelli toanumbrid ja 60 brauseri vahekaarti

**Võrdlus.** Hotellis ei tea külaline, kus tema tuba füüsiliselt asub; ta teab ainult numbrit 214. Vastuvõtt (MMU ja lehetabel) teab, et tuba 214 on teise korruse idatiivas. Kaks eri reisiseltskonda võivad kumbki rääkida „oma toast nr 1“, aga need on eri toad – nii on ka kahe protsessi aadress `0x1000` eri kohtades. Kui hotell on täis, viiakse mõne pikaajalise külalise kohvrid hoiuruumi (lehefail); kui külaline neid küsib, tuleb oodata (kõva leheviga).

**Windowsis.** Õpilase sülearvutis on 8 GB RAM-i ja Edge’is 60 vahekaarti.

1. Tegumihaldur näitab: *Kasutusel 7,4 GB*, *Tihendatud 1,8 GB*, *Eraldatud 14/20 GB*. Eraldatud mälu (commit) on suurem kui RAM, sest osa on lehefailis.
2. Windows tihendab kauem kasutamata lehti mälus ja kirjutab osa lehefaili.
3. Vanale vahekaardile naastes tekib palju kõvasid leheveaid, leht laadib mõne sekundi.
4. Edge paneb mitteaktiivsed vahekaardid magama (unerežiimis vahekaardid), et vabastada nende tööhulk.

**Järeldus:** aeglus ei tähenda viga – virtuaalmälu töötab, kuid tööhulk ei mahu RAM-i. Lahendus on lisamälu või vähem korraga avatud vahekaarte, mitte lehefaili keelamine.

### Kokkuvõte

- Virtuaalmälu eraldab protsessid ja laseb kasutada rohkem mälu, kui on RAM-i.
- Lehetabelid ja TLB tõlgivad aadresse; leheviga toob puuduva lehe mällu.
- Windows kasutab tööhulki, ootenimekirja, tihendamist ja lehefaili.
- Mäluhaldusega on seotud olulised ründekaitsed: DEP, ASLR, CFG, varjupinu, HVCI.

### Kontrollküsimused

1. Mis vahe on saalimisel ja lehekülgedeks jaotamisel?
2. Kirjelda nelja mälumudelit ja nende puudusi.
3. Mis on virtuaalmälu ja kuidas toimub aadressi tõlkimine?
4. Mis vahe on pehmel ja kõval leheveal?
5. Mis vahe on mälu reserveerimisel ja eraldamisel (commit)?
6. Mis on tööhulk ja ootenimekiri?
7. Miks kasutab Windows mälu tihendamist?
8. Nimeta kolm mäluga seotud ründekaitset ja selgita, mille vastu need aitavad.
9. Mis vahe on kuhjal ja pinul?

### Valikküsimused

1. Mis on tavaline lehekülje suurus x64 Windowsis? a) 512 B b) 4 KB c) 64 KB d) 1 MB
2. Mis on TLB? a) failisüsteemi tabel b) aadressitõlgete vahemälu c) lehefail d) tuumapinu
3. Mida teeb ASLR? a) krüpteerib mälu b) muudab moodulite aadressid juhuslikuks c) tihendab mälu d) suurendab lehefaili
4. Kus hoitakse tihendatud lehti? a) pagefile.sys b) swapfile.sys c) protsessis Memory Compression d) TPM-is

### Praktiline ülesanne

```powershell
Get-CimInstance Win32_OperatingSystem | Select-Object TotalVisibleMemorySize, FreePhysicalMemory, TotalVirtualMemorySize
Get-CimInstance Win32_PageFileUsage | Select-Object Name, AllocatedBaseSize, CurrentUsage, PeakUsage
Get-Counter '\Memory\Page Faults/sec','\Memory\Pages/sec','\Memory\Committed Bytes' -MaxSamples 3
Get-MMAgent
```

Ava Ressursimonitor → Mälu ja tuvasta riba värvid: kasutusel, muudetud, ootel, vaba. Ava Tegumihaldur → Jõudlus → Mälu ja leia „Tihendatud“. Ava Sysinternalsi **RAMMap** ja vaata, milliseid lehti on ootenimekirjas. Kontrolli protsessi mitigatsioone: `Get-ProcessMitigation -Name notepad.exe`.

### Iseseisvad ülesanded

**1. Mälusurve katse.** Ava õppe-VM-is (4 GB RAM) järjest 5, 15 ja 30 brauseri vahekaarti suurte veebilehtedega. Mõõda iga kord Tegumihaldurist: kasutusel, tihendatud, eraldatud (commit) ning `Get-Counter '\Memory\Page Faults/sec','\Memory\Pages/sec'`. **Esita:** tabel ja graafik ning järeldus, millal hakkas süsteem kasutama lehefaili ja kuidas see kasutaja jaoks välja paistis.

**2. Ründekaitsete kaardistus.** Käivita `Get-ProcessMitigation -System` ning kolme rakenduse kohta (`notepad.exe`, `msedge.exe`, mõni vanem programm). Võrdle DEP-i, ASLR-i, CFG ja varjupinu (`UserShadowStack`) olekut. **Esita:** tabel ja selgitus, miks vanemal programmil võib osa kaitseid puududa ning kuidas see mõjutab ründe võimalusi.

### Viited

- Krogh (2017), ptk 12.
- Yosifovich jt, *Windows Internals* 7. tr, 1. osa, ptk 5 „Memory management“.
- Microsoft Learn: [Memory Management](https://learn.microsoft.com/windows/win32/memory/memory-management); [Exploit protection reference](https://learn.microsoft.com/defender-endpoint/exploit-protection-reference).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 11. Ummikud](11-ummikud.md) · [Sisukord](../README.md) · [13. Vahemälu →](13-vahemalu.md)
