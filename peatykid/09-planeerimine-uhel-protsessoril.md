[← 8. Lõimede sünkroniseerimine](08-sunkroniseerimine.md) · [Sisukord](../README.md) · [10. Planeerimine mitmel protsessoril →](10-planeerimine-mitmel-protsessoril.md)

## 9. Protsesside planeerimine ühel protsessoril

**Planeerija** (scheduler) otsustab, milline valmis lõim saab järgmisena protsessori ja kui kauaks. Windows kasutab prioriteedipõhist eelistavat planeerimist 32 prioriteeditasemega; lõim saab ajakvandi ja kaotab protsessori, kui kvant lõpeb või kõrgema prioriteediga lõim muutub valmis.

### 9.1 Protsessori vabatahtlik ja sunnitud loovutamine

Lõim loovutab protsessori **vabatahtlikult**, kui ta hakkab ootama (S/V, lukk, `Sleep`). Ta kaotab protsessori **sunniviisiliselt**, kui OS ta katkestab: kvant sai otsa või saabus olulisem töö. Programmid jagunevad gruppidesse:

- **protsessorisõltuvad** (CPU-bound) – arvutavad palju, nt video kodeerimine, kompileerimine;
- **S/V-sõltuvad** (I/O-bound) – ootavad enamuse ajast ketast, võrku või kasutajat, nt tekstiredaktor.

Hea planeerija annab S/V-sõltuvatele lõimedele kiiresti protsessori, sest nad kasutavad seda vaid hetke ja lähevad siis jälle ootele.

### 9.2 Mitte-eelistav planeerimine

Mitte-eelistavas (non-preemptive) süsteemis töötab lõim seni, kuni ise loovutab. Näited: Windows 3.x, klassikaline Mac OS. Algoritmid:

- **FCFS** (First Come, First Served) – saabumise järjekorras; lihtne, kuid lühikesed tööd ootavad pikkade taga;
- **SJF** (Shortest Job First) – lühim töö enne; minimeerib keskmise ooteaja, kuid nõuab töö pikkuse teadmist.

### 9.3 Eelistav planeerimine

Eelistavas (preemptive) süsteemis võib OS lõime igal hetkel peatada. Algoritmid:

- **Round Robin (RR)** – iga lõim saab kordamööda kvandi;
- **prioriteedipõhine** – alati töötab kõrgeima prioriteediga valmis lõim; risk: madala prioriteediga lõimed näljutatakse;
- **mitmetasemeline tagasisidega järjekord** (MLFQ) – mitu prioriteedijärjekorda; lõime prioriteet muutub tema käitumise järgi.

**Näide (üks protsessor, saabumine samal ajal):** tööd A = 8 ms, B = 4 ms, C = 2 ms.

| Algoritm | Järjekord | Ooteajad A / B / C | Keskmine ooteaeg |
| --- | --- | --- | --- |
| FCFS | A, B, C | 0 / 8 / 12 | 6,7 ms |
| SJF | C, B, A | 6 / 2 / 0 | 2,7 ms |

> 📊 **Joonis:** Gantti diagramm · FCFS, SJF ja Round Robin samade töödega

Joonis: SJF laseb lühikesed tööd ette ja vähendab keskmist ooteaega; Round Robin vahetab töid iga 2 ms järel, mis teeb süsteemi reageerivaks, kuid lisab kontekstivahetusi.

### 9.4 Planeerimine eri operatsioonisüsteemides

- **Windows** – 32 prioriteeti, eelistav, kvandipõhine, dünaamilised prioriteeditõusud.
- **Linux** – kuni versioonini 6.5 CFS (Completely Fair Scheduler), mis jagab protsessoriaega „virtuaalse tööaja“ järgi; alates 6.6 EEVDF (Earliest Eligible Virtual Deadline First). Reaalajalõimedele SCHED\_FIFO ja SCHED\_RR.
- **macOS / iOS** – teenuse kvaliteedi klassid (QoS): kasutajaliides, kasutaja algatatud, utiliit, taust.
- **Reaalaja-OS-id** (nt FreeRTOS, Zephyr) – rangelt prioriteedipõhised, ennustatava viivitusega.

### 9.5 Kontekstivahetus

**Kontekstivahetus** (context switch) on üleminek ühelt lõimelt teisele: salvestatakse vana lõime registrid ja pinuviit, laaditakse uue omad; kui lõimed kuuluvad eri protsessidesse, vahetatakse ka aadressiruum (lehetabelite baas). Kontekstivahetus maksab mikrosekundeid ja rikub vahemälude sisu, seetõttu ei tohi kvant olla liiga lühike.

### 9.6 Kvant

**Kvant** (quantum) on aeg, mille lõim saab enne, kui planeerija kontrollib, kas keegi teine peaks töötama. Windowsis mõõdetakse kvanti kellatiksudes (tavaliselt umbes 15,6 ms). Kliendiversioonis on vaikimisi kvant 2 tiksu, serveris 12 tiksu: server eelistab läbilaskevõimet, klient reageerimiskiirust. Esiplaani rakenduse lõimed võivad kliendis saada pikema kvandi. Seadet muudab Juhtpaneel → Süsteem → Täpsemad süsteemisätted → Jõudlus → Täpsemalt → „Programmid“ või „Taustteenused“.

### 9.7 Planeerimine Windowsis

**Prioriteedid:**

| Vahemik | Tähendus |
| --- | --- |
| 16–31 | reaalajaprioriteedid (ei muutu dünaamiliselt) |
| 1–15 | muutuvad prioriteedid (tavalised rakendused) |
| 0 | nulllehe lõim (vaba mälu nullimine) |

Protsessil on **prioriteediklass** (Idle, Below Normal, Normal, Above Normal, High, Realtime) ja lõimel suhteline prioriteet selle sees. Normal-klassi tavaline lõim on prioriteediga 8.

**Dünaamilised tõusud** (priority boost): lõim saab ajutiselt kõrgema prioriteedi, kui lõppeb tema S/V, kui ta on graafilise liidese lõim ja saab sisendi või kui ta on kaua näljas olnud (näljutamisvastane tõus). Nii reageerib süsteem kasutajale kiiresti ka koormuse all.

**Energiasääst ja QoS.** Windows 11 märgib lõimedele teenuse kvaliteedi taseme (High, Medium, Low, Eco, Utility). Tegumihalduri **Efektiivsusrežiim** (Efficiency mode) seab protsessi EcoQoS-ile: madal prioriteet ja eelistatult säästlikud tuumad väiksema taktsagedusega. See on oluline sülearvutis, kus aku on piiratud ressurss.

### Näide: poe kassad ja video kodeerimine

**Võrdlus.** Poes on kassa, mille ees seisab käru täis ostudega klient ja tema taga kolm inimest, kellel on üks piimapakk. FCFS järgi ootavad kõik, kuni täis käru on läbi. **Kiirkassa** („kuni 5 toodet“) on SJF-i idee: lühikesed tööd saavad kiiresti tehtud ja keskmine ooteaeg väheneb. Round Robin oleks kassa, kus iga klient saab korraga lüüa läbi kaks toodet ja läheb siis järjekorra lõppu.

**Windowsis.** Õpetaja kodeerib HandBrake’iga tunnipikkust videot ja kirjutab samal ajal Wordis.

- HandBrake on protsessorisõltuv ja kasutab kõiki tuumi; Word on S/V-sõltuv (ootab klahvivajutusi).
- Iga klahvivajutus annab Wordi lõimele **prioriteeditõusu** ja Word katkestab HandBrake’i lõime – kirjutamine ei hangu.
- Kui arvuti siiski aeglustub, seab õpetaja HandBrake’i Tegumihalduris prioriteedile *Below normal* või lülitab sisse **Efektiivsusrežiimi**. Video valmib veidi hiljem, kuid töö arvutiga on sujuv ja sülearvuti aku kestab kauem.

### Kokkuvõte

- Planeerija valib järgmise lõime prioriteedi ja algoritmi järgi.
- Eelistav planeerimine laseb OS-il katkestada iga lõime.
- Windows kasutab 32 prioriteeti, kvante ja dünaamilisi tõuse; Windows 11 lisab energiatõhususe QoS-taseme.

### Kontrollküsimused

1. Mis vahe on protsessori vabatahtlikul ja sunnitud loovutamisel?
2. Mis vahe on protsessorisõltuval ja S/V-sõltuval programmil?
3. Kirjelda FCFS, SJF ja Round Robin algoritmi.
4. Arvuta näite tööde A, B, C keskmine ooteaeg Round Robinil kvandiga 2 ms.
5. Mis on kontekstivahetus ja miks see maksab?
6. Miks on serveri kvant pikem kui kliendi oma?
7. Kirjelda Windowsi prioriteedivahemikke ja dünaamilisi tõuse.
8. Mida teeb Tegumihalduri Efektiivsusrežiim?

### Valikküsimused

1. Mitu prioriteeditaset on Windowsi planeerijal? a) 8 b) 16 c) 32 d) 64
2. Milline algoritm minimeerib keskmise ooteaja, kui tööde pikkused on teada? a) FCFS b) SJF c) RR d) juhuslik
3. Milline Linuxi planeerija asendas CFS-i? a) O(1) b) BFS c) EEVDF d) SCHED\_FIFO
4. Mis prioriteet on Normal-klassi tavalisel lõimel? a) 4 b) 8 c) 13 d) 24

### Praktiline ülesanne

```powershell
Start-Process notepad
$p = Get-Process notepad | Select-Object -First 1
$p.PriorityClass
$p.PriorityClass = 'BelowNormal'
$p.Threads | Select-Object Id, BasePriority, CurrentPriority, ThreadState, WaitReason | Format-Table
```

Ava Tegumihaldur → Üksikasjad, paremklõps protsessil → „Määra prioriteet“. Seejärel lülita mõnele taustarakendusele sisse Efektiivsusrežiim ja vaata, kuidas muutub selle olek veerus „Olek“. Miks ei tohiks tavaprogrammile anda prioriteeti Realtime?

### Iseseisvad ülesanded

**1. Planeerimise arvutusülesanne.** Neli tööd saabuvad ajal 0: P1 = 6 ms, P2 = 3 ms, P3 = 8 ms, P4 = 1 ms (järjekord P1, P2, P3, P4). Arvuta iga töö ooteaeg ja keskmine ooteaeg algoritmidega FCFS, SJF ja Round Robin (kvant 2 ms). Joonista iga algoritmi kohta Gantti diagramm. **Esita:** kolm diagrammi, arvutused ja vastus: milline algoritm sobiks interaktiivsele töölaua-arvutile ja miks?

**2. Prioriteedi mõju katses.** Käivita kaks PowerShelli akent, mis mõlemad koormavad protsessorit (`while($true){}`), ja sea ühe protsessi afiinsus samale tuumale teisega (`$p.ProcessorAffinity = 1`). Muuda ühe prioriteet *High* ja teise *Idle* ning vaata Tegumihalduris, kuidas protsessoriaeg jaguneb. **Esita:** ekraanipildid ja selgitus. Lõpeta katse kohe pärast mõõtmist.

### Viited

- Krogh (2017), ptk 9.
- Yosifovich jt, *Windows Internals* 7. tr, 1. osa, ptk 4 „Threads“ (Thread scheduling).
- Microsoft Learn: [Scheduling Priorities](https://learn.microsoft.com/windows/win32/procthread/scheduling-priorities); [Quality of Service](https://learn.microsoft.com/windows/win32/procthread/quality-of-service).
- Linuxi tuuma dokumentatsioon: [EEVDF Scheduler](https://docs.kernel.org/scheduler/sched-eevdf.html).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 8. Lõimede sünkroniseerimine](08-sunkroniseerimine.md) · [Sisukord](../README.md) · [10. Planeerimine mitmel protsessoril →](10-planeerimine-mitmel-protsessoril.md)
