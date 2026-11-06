[← 10. Planeerimine mitmel protsessoril](10-planeerimine-mitmel-protsessoril.md) · [Sisukord](../README.md) · [12. Mäluhaldus →](12-maluhaldus.md)

## 11. Ummikud

**Ummik** (deadlock) on olukord, kus kaks või enam lõime ootavad üksteise käes olevaid ressursse ja ükski ei saa edasi minna. Ummik ei lahene iseenesest; tavaliselt tuleb üks osaline lõpetada või ressurss jõuga vabastada.

### 11.1 Ummikute näited

- **Kaks lukku vastupidises järjekorras:** lõim A võtab luku L1 ja ootab L2; lõim B võtab L2 ja ootab L1.
- **Andmebaas:** tehing 1 lukustab rea X ja tahab rida Y, tehing 2 vastupidi. SQL Server tuvastab selle ja katkestab ühe tehingu („deadlock victim“).
- **Kasutajaliides:** taustalõim ootab, et UI-lõim midagi teeks, UI-lõim aga ootab sünkroonselt taustalõime lõppu – aken „ei vasta“.
- **Igapäevaelu:** neljaviisilisel ristmikul ilma valgusfoorita on igast suunast üks auto ja kõik annavad paremalt tulijale teed.

**Sarnased probleemid:**

- **elavlukk** (livelock) – lõimed reageerivad üksteisele, kuid ei jõua edasi (kaks inimest koridoris astuvad korraga samale poole);
- **näljutamine** (starvation) – üks lõim ei saa kunagi ressurssi, sest teised on alati eespool;
- **prioriteedi inversioon** – kõrge prioriteediga lõim ootab lukku, mida hoiab madala prioriteediga lõim, keda omakorda takistab keskmise prioriteediga lõim. Windows leevendab seda: näljas valmis lõimed saavad ajutise prioriteeditõusu.

### 11.2 Ummiku tekkimise tingimused

Coffmani järgi (1971) tekib ummik ainult siis, kui kehtivad kõik neli tingimust:

1. **vastastikune välistamine** – ressurssi saab korraga kasutada üks lõim;
2. **hoia ja oota** – lõim hoiab ressurssi ja ootab samal ajal teist;
3. **eelistamise puudumine** – ressurssi ei saa lõimelt jõuga ära võtta;
4. **tsükliline ootamine** – tekib lõimede ahel, kus igaüks ootab järgmise ressurssi.

Olukorda saab kujutada **ressursijaotuse graafina**: sõlmed on lõimed ja ressursid, nooled näitavad „hoiab“ ja „ootab“. Tsükkel graafis tähendab (üheeksemplariliste ressursside korral) ummikut.

> 📊 **Joonis:** ressursijaotuse graaf · kolm lõime ja kolm ressurssi tsüklis

Joonis: T1 ootab printerit, mida hoiab T2; T2 ootab skannerit, mida hoiab T3; T3 ootab faili, mida hoiab T1. Tsükli saab murda, kui kõik võtavad ressursid samas järjekorras.

### 11.3 Kuidas OS ummikuid käsitleb

| Strateegia | Sisu | Kus kasutatakse |
| --- | --- | --- |
| Ignoreerimine („jaanalinnu algoritm“) | eeldatakse, et ummikud on harvad; lahendab kasutaja või arendaja | Windows, Linux üldiselt |
| Tuvastamine ja taastamine | perioodiliselt otsitakse tsükleid ja lõpetatakse osaline | andmebaasid (SQL Server, PostgreSQL) |
| Vältimine | enne ressursi andmist kontrollitakse, kas olek jääb ohutuks (pankuri algoritm) | teoorias; praktikas harva |
| Ennetamine | tingimustest üks muudetakse võimatuks | programmeerimistavad |

**Ennetamine praktikas:**

- kõik lõimed võtavad lukud **kindlas järjekorras** (murrab tsüklilise ootamise) – kõige levinum võte; Windowsi tuumas on lukkudel tasemed;
- võta kõik vajalikud ressursid korraga (`WaitForMultipleObjects` kõigi objektidega);
- kasuta ajapiiranguga ootamist (`TryEnter`, ajalõpp) ja vabasta oma lukud, kui ei õnnestu;
- ära kutsu luku all välist koodi (tagasikutsed, sündmuskäsitlejad).

### 11.4 Tööriistad ummikute leidmiseks

- **Ressursimonitor** (`resmon`) → CPU → paremklõps protsessil → „Analüüsi ooteahelat“ (Wait Chain Traversal);
- **Process Explorer** – lõimede pinud ja ooteobjektid;
- **WinDbg** – käsud `!locks`, `!analyze -hang`, `~*k`;
- **Driver Verifier** – draiverite lukureeglite kontroll;
- **Application Verifier** – kasutajarežiimi lukuvead;
- **SQL Serveri laiendatud sündmused** – ummikugraafid (deadlock graph);
- **Visual Studio Parallel Stacks** – lõimede ootamise visualiseerimine.

### Näide: ristmik ja kaks andmebaasitehingut

**Liiklus.** Neljaviisilisele ristmikule sõidab igast suunast korraga auto ja igaüks annab teed paremalt tulijale. Kõik neli Coffmani tingimust kehtivad: ristmiku osa saab olla korraga ühes autos (välistamine), auto seisab oma kohal ja ootab teist (hoia ja oota), kedagi ei tõsteta ära (eelistamise puudumine) ja ooteahel on ring (tsükkel). Valgusfoor murrab tsükli: see määrab kindla järjekorra.

**Andmebaas.** Kooli õppeinfosüsteemis teeb üks päring „kanna hinne ja uuenda keskmist“ (lukustab tabeli `Hinded`, siis `Opilased`) ja teine „muuda õpilase rühma ja arvuta keskmine ümber“ (lukustab `Opilased`, siis `Hinded`). Koormuse all juhtub vahel, et kumbki hoiab ühte ja ootab teist. SQL Server tuvastab tsükli, katkestab ühe tehingu ja rakendus saab vea 1205 (*deadlock victim*).

**Parandus:** arendaja muudab mõlemad päringud lukustama tabeleid samas järjekorras (`Opilased` → `Hinded`) ja lisab rakendusse vea 1205 korral tehingu uue katse.

### Kokkuvõte

- Ummik vajab nelja tingimust korraga; ühe murdmine väldib ummiku.
- Üldotstarbelised OS-id ummikuid ei väldi, vaid jätavad vastutuse arendajale; andmebaasid tuvastavad ja lahendavad neid ise.
- Lukkude kindel järjekord on kõige tõhusam praktiline kaitse.

### Kontrollküsimused

1. Defineeri ummik ja too kaks näidet.
2. Nimeta ja selgita Coffmani nelja tingimust.
3. Mis vahe on ummikul, elavlukul ja näljutamisel?
4. Mis on prioriteedi inversioon ja kuidas Windows seda leevendab?
5. Kirjelda nelja ummikute käsitlemise strateegiat.
6. Miks ei kasuta Windows pankuri algoritmi?
7. Milliste tööriistadega saab Windowsis ummikut tuvastada?

### Valikküsimused

1. Milline võte murrab tsüklilise ootamise tingimuse? a) lukkude võtmine kindlas järjekorras b) suurem mälu c) kõrgem prioriteet d) rohkem tuumi
2. Kuidas lahendab SQL Server ummiku? a) taaskäivitab serveri b) katkestab ühe tehingu c) ootab lõputult d) kustutab tabeli
3. Mitu Coffmani tingimust peab kehtima, et tekiks ummik? a) üks b) kaks c) kolm d) kõik neli

### Praktiline ülesanne

Joonista ressursijaotuse graaf olukorrast: lõim T1 hoiab faili F ja ootab printerit P; T2 hoiab printerit P ja ootab skannerit S; T3 hoiab skannerit S ja ootab faili F. Kas tekib ummik? Paku kaks viisi, kuidas seda vältida.

Windowsis: ava Ressursimonitor, vali mõni „Ei vasta“ olekus või ootav protsess ja käivita „Analüüsi ooteahelat“. Mida ahel näitab?

### Iseseisvad ülesanded

**1. Ummiku tekitamine ja lahendamine.** Kirjuta PowerShell 7-s või C#-s kaks lõime ja kaks lukku (`[System.Threading.Monitor]` või C# `lock`). Lõim 1 võtab lukud järjekorras A, B; lõim 2 järjekorras B, A; lisa lukkude vahele `Start-Sleep -Milliseconds 100`. Veendu, et programm jääb kinni. Paranda programm nii, et mõlemad võtaksid lukud samas järjekorras. **Esita:** mõlemad koodid ja selgitus, millise Coffmani tingimuse parandus murdis.

**2. Ummiku analüüs päriselust.** Leia uudistest, foorumist või oma kogemusest üks näide, kus süsteem või protsess jäi üksteist ootama (IT-s või igapäevaelus, nt asutuste vahel, kus igaüks ootab teise allkirja). **Esita:** ressursijaotuse graaf, neli Coffmani tingimust selle näite peal ja ettepanek, kuidas ummikut vältida.

### Viited

- Krogh (2017), ptk 11.
- Coffman, E. G., Elphick, M., Shoshani, A. (1971). System Deadlocks. *ACM Computing Surveys*, 3(2).
- Tanenbaum, Bos (2022), *Modern Operating Systems*, ptk 6.
- Microsoft Learn: [Wait Chain Traversal](https://learn.microsoft.com/windows/win32/debug/wait-chain-traversal).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 10. Planeerimine mitmel protsessoril](10-planeerimine-mitmel-protsessoril.md) · [Sisukord](../README.md) · [12. Mäluhaldus →](12-maluhaldus.md)
