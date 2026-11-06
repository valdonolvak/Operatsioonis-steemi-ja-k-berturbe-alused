[← 9. Protsesside planeerimine ühel protsessoril](09-planeerimine-uhel-protsessoril.md) · [Sisukord](../README.md) · [11. Ummikud →](11-ummikud.md)

## 10. Planeerimine mitmel protsessoril

Peaaegu iga tänapäeva arvuti on mitmeprotsessoriline: sülearvutis on 8–24 tuuma, serveris sadu loogilisi protsessoreid. Planeerija peab otsustama mitte ainult **millal**, vaid ka **millisel tuumal** lõim töötab – ja hübriidprotsessoris ka seda, **millist tüüpi** tuumal.

### 10.1 Mitmetuumalised protsessorid

**Mitmetuumaline protsessor** (multi-core) sisaldab ühes kiibis mitut täisväärtuslikku tuuma, millel on oma L1- ja L2-vahemälu ning ühine L3. **Samaaegne mitmelõimelisus** (SMT, Inteli nimetusega Hyper-Threading) laseb ühel füüsilisel tuumal täita kaht lõime korraga, kasutades tuuma vabu täitmisüksusi; OS näeb neid kahe **loogilise protsessorina**.

**Hübriidprotsessorid.** Alates Intel 12. põlvkonnast (2021) ja ARM-i big.LITTLE / DynamIQ lahendustes on kiibis kahte tüüpi tuumi:

- **P-tuumad** (performance) – suured, kiired, voolunõudlikud;
- **E-tuumad** (efficiency) – väikesed, säästlikud, sobivad taustatööks.

Lisaks võib kiibis olla GPU ja NPU, mida OS haldab eraldi planeerijatega (GPU-planeerimine WDDM-is, NPU MCDM-draiveri kaudu).

### 10.2 Mitme protsessori organiseerimine

| Mudel | Kirjeldus |
| --- | --- |
| Asümmeetriline (AMP) | üks protsessor juhib, teised täidavad talle antud töid |
| Sümmeetriline (SMP) | kõik protsessorid on võrdsed ja täidavad nii OS-i kui ka rakenduste koodi |
| NUMA | protsessorid on sõlmedes; oma sõlme mälu on kiirem kui teise sõlme oma |
| Klaster | mitu arvutit võrgu kaudu, igal oma OS |

Serverites on tavaline **NUMA** (Non-Uniform Memory Access): kahe- või neljapesalises serveris on igal protsessoril oma mälukanalid. Kui lõim töötab sõlmes 0, aga tema andmed on sõlme 1 mälus, on iga pöördumine aeglasem. Windows ja Hyper-V püüavad hoida lõime ja tema mälu samas sõlmes.

> 📊 **Joonis:** hübriidprotsessor ja NUMA · kuhu planeerija lõime paneb

Joonis: sülearvutis valib planeerija tuuma tüübi (P või E), serveris NUMA-sõlme – lõim ja tema mälu peaksid asuma samas sõlmes.

### 10.3 Sümmeetriline multitöötlus

Windows on SMP-süsteem: tuum, draiverid ja rakendused võivad töötada igal protsessoril. See nõuab, et kõik jagatud tuumaandmed oleksid lukkudega kaitstud ning et protsessorite vahemälud oleksid **koherentsed** – riistvara protokoll (nt MESI) tagab, et ühe tuuma kirjutus muutub teistele nähtavaks.

Windows toetab kuni 64 loogilist protsessorit ühe **protsessorirühma** (processor group) kohta ja mitut rühma ühes süsteemis. Rakendus, mis tahab kasutada üle 64 protsessori, peab olema rühmadest teadlik (Windows 11 ja Server 2022+ jagavad lõimi rühmadesse vaikimisi automaatselt).

### 10.4 Mitu protsessorit ja sünkroniseerimine

Ühe protsessori süsteemis piisab kriitilise piirkonna kaitseks katkestuste keelamisest. Mitme protsessori puhul see ei aita, sest teine tuum töötab edasi. Seetõttu kasutab tuum **spinlukke**: protsessor ootab aktiivselt tsüklis, kuni lukk vabaneb. **Järjekorraga spinlukk** (queued spinlock) vähendab vahemälu-liiklust, sest iga ootaja keerleb oma muutujal. Spinlukku tohib hoida väga lühikest aega ning luku all ei tohi lõim magama jääda.

Probleem **vale jagamine** (false sharing): kaks tuuma muudavad eri muutujaid, mis asuvad samal 64-baidisel vahemälureal, ja rida „põrkab“ tuumade vahel. Lahendus on andmete joondamine.

### 10.5 Planeerimine mitmel protsessoril

Planeerimise strateegiad:

- **ühine järjekord** – kõik tuumad võtavad töö ühest järjekorrast; lihtne, kuid järjekorralukk muutub kitsaskohaks;
- **protsessoripõhised järjekorrad** – igal tuumal oma järjekord + koormuse tasakaalustamine (töö „varastamine“ jõudeta tuumalt);
- **afiinsus** (affinity) – lõime hoitakse samal tuumal, et vahemälu sisu jääks kasutatavaks;
- **rühmaplaneerimine** (gang scheduling) – koostöötavad lõimed käivitatakse korraga eri tuumadel.

### 10.6 Mitmetöötlus Windowsis

- Windows hoiab igal protsessoril oma valmisolekujärjekorrad (32 prioriteedi kaupa).
- Igal lõimel on **ideaalne protsessor** ja **afiinsusmask** (lubatud protsessorid).
- Valmis lõime jaoks otsitakse esmalt vaba protsessorit, eelistatult ideaalset või viimati kasutatud; kui vaba pole, võib lõim tõrjuda madalama prioriteediga lõime.
- **Tuumade parkimine** (core parking) lülitab vähese koormusega tuumad süvaunne, et säästa energiat.
- **Hübriidplaneerimine**: Windows 11 kasutab protsessori vihjeid (Intel Thread Director, AMD ja Qualcommi vastavad mehhanismid) ning lõime QoS-taset. Esiplaani ja kõrge QoS-iga lõimed lähevad P-tuumadele, taustatöö ja EcoQoS E-tuumadele. Windows 10 hübriidprotsessoritega nii hästi ei toime.
- **CPU Sets** – API, millega rakendus (nt mäng) saab tuumi reserveerida.

### Näide: videokõne akutoitel ja suur virtuaalmasin

**Sülearvuti.** Õpetaja peab akutoitel Teamsi tundi ja OneDrive sünkroonib taustal faile. Teamsi videotöötlus on esiplaanil ja kõrge QoS-iga – see läheb P-tuumadele, tausta hägustamine (Studio Effects) kasutab NPU-d. OneDrive’i sünkroonimine saab EcoQoS-i ja töötab E-tuumadel madala sagedusega. Pilt ei hangu ja aku kestab kauem kui siis, kui kõik töötab P-tuumadel.

**Server (VMware või Hyper-V).** Hostil on kaks 16-tuumalist protsessorit (kaks NUMA-sõlme, kummalgi 256 GB RAM-i). Administraator loob SQL Serveri VM-i 24 vCPU ja 384 GB mäluga – see ei mahu ühte sõlme.

- Hüperviisor jagab VM-i kahe sõlme vahel ja esitab külalisele **virtuaalse NUMA** (vNUMA) topoloogia.
- Kui SQL Server teab topoloogiat, hoiab ta lõimed ja puhvrid samas sõlmes.
- Kui vNUMA on vale (nt käsitsi valesti seatud „cores per socket“), teevad paljud päringud kaugpöördumisi ja jõudlus langeb märgatavalt.

**Rusikareegel:** võimaluse korral tee VM, mis mahub ühte NUMA-sõlme.

### Kokkuvõte

- Mitmetuumalisus, SMT ja hübriidtuumad muudavad planeerimise ruumiliseks probleemiks.
- SMP nõuab koherentseid vahemälusid ja lukke, eriti spinlukke.
- NUMA-s tuleb hoida lõim ja tema mälu samas sõlmes.
- Windows 11 planeerija arvestab tuuma tüüpi ja lõime QoS-taset.

### Kontrollküsimused

1. Mis vahe on mitmetuumalisusel ja SMT-l?
2. Mis on hübriidprotsessor ja kuidas OS selle tuumi kasutab?
3. Mis vahe on AMP-l ja SMP-l?
4. Mis on NUMA ja miks see planeerimist mõjutab?
5. Miks ei piisa mitmeprotsessorilises süsteemis katkestuste keelamisest?
6. Mis on spinlukk ja millal seda kasutatakse?
7. Mis on afiinsus ja ideaalne protsessor?
8. Mis on protsessorirühm Windowsis?

### Valikküsimused

1. Mitu loogilist protsessorit mahub ühte Windowsi protsessorirühma? a) 16 b) 32 c) 64 d) 128
2. Millisele tuumale eelistab Windows 11 suunata EcoQoS-lõime? a) P-tuumale b) E-tuumale c) GPU-le d) NPU-le
3. Mis on „vale jagamine“? a) kaks protsessi jagavad sama faili b) eri muutujad samal vahemälureal põhjustavad liigset liiklust c) kasutaja jagab parooli d) lõim kasutab vale lukku

### Praktiline ülesanne

```powershell
Get-CimInstance Win32_Processor | Select-Object Name, NumberOfCores, NumberOfLogicalProcessors
(Get-CimInstance Win32_ComputerSystem).NumberOfLogicalProcessors
Start-Process notepad; $p = Get-Process notepad | Select-Object -First 1
$p.ProcessorAffinity
$p.ProcessorAffinity = 1   # ainult protsessor 0
```

Ava Tegumihaldur → Jõudlus → CPU, paremklõps graafikul → „Muuda graafiku tüüpi: loogilised protsessorid“. Käivita PowerShellis koormus `1..4 | ForEach-Object -Parallel { while($true){} }` ja vaata, millised tuumad koormuse saavad. Lõpeta koormus Ctrl+C-ga. Hübriidprotsessoriga arvutis võrdle esiplaanil ja minimeeritud aknas käivitatud koormust.

### Iseseisvad ülesanded

**1. Paralleelsuse kasu mõõtmine.** Mõõda PowerShell 7-s, kui kaua võtab 8 suure arvutuse tegemine järjest ja paralleelselt: `Measure-Command { 1..8 | ForEach-Object { 1..3000000 | Measure-Object -Sum } }` vs `… ForEach-Object -Parallel { … } -ThrottleLimit 8`. Korda paralleelset varianti `-ThrottleLimit` väärtustega 1, 2, 4 ja 8. **Esita:** tabel ajakulu kohta ja vastus: miks kiirenemine ei ole lineaarne (vihje: tuumade arv, E-tuumad, ühine mälu)?

**2. Serveri topoloogia.** Uuri ühe serveri (oma töökoha, kooli labori või tootja kodulehe näite) protsessorite ja mälu ülesehitust: pesade arv, tuumade arv pesa kohta, NUMA-sõlmed (Windowsis `Get-CimInstance Win32_Processor`, Sysinternalsi `Coreinfo`). **Esita:** skeem ja soovitus, kui suure (vCPU, RAM) VM-i võiks sellele luua, et see mahuks ühte NUMA-sõlme.

### Viited

- Krogh (2017), ptk 10.
- Yosifovich jt, *Windows Internals* 7. tr, 1. osa, ptk 4 (Multiprocessor systems).
- Microsoft Learn: [Processor Groups](https://learn.microsoft.com/windows/win32/procthread/processor-groups).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 9. Protsesside planeerimine ühel protsessoril](09-planeerimine-uhel-protsessoril.md) · [Sisukord](../README.md) · [11. Ummikud →](11-ummikud.md)
