[← 30. UUS: Postkvantkrüptograafia ja krüptograafia tulevik](30-postkvantkruptograafia.md) · [Sisukord](../README.md) · [Valikküsimuste vastused →](vastused.md)

## 31. UUS: Küberturbe õigus- ja juhtimisraamistik

Tehnilised meetmed (peatükid 18, 22, 24–30) on mõjusad ainult siis, kui keegi vastutab nende eest, nende rakendamine on plaanitud ja intsidendi korral on teada, kellele ja millal teatada. Peatükk annab IT-süsteemi haldajale ülevaate Eesti ja Euroopa Liidu nõuetest. Peatükk ei ole õigusnõu: konkreetse organisatsiooni kohustused tuleb täpsustada RIA, Andmekaitse Inspektsiooni või juristiga.

### 31.1 Nõuete kaart

![õigusraamistik · KüTS, GDPR, E-ITS, CRA, DORA, eIDAS](../pildid/31-a.png)

Joonis: kuus olulisemat nõuete kogumit. Need kattuvad osaliselt: haigla peab täitma nii küberturvalisuse seadust kui ka isikuandmete kaitse nõudeid, pank lisaks DORA-t ja tarkvaratootja CRA-d.

### 31.2 Küberturvalisuse seadus ja NIS2

Euroopa Liidu **NIS2-direktiiv** (2022/2555) võeti Eestis üle **küberturvalisuse seaduse** (KüTS) muudatustega, mis jõustusid 1. jaanuaril 2026 ([KPMG](https://kpmg.com/ee/et/sundmused/2026/02/kueberturvalisuse-ja-haedaolukorra-seaduse-muudatused.html)). Eestis oli küberturvalisuse seadus juba 2018. aastast, seega NIS2 laiendas olemasolevat raamistikku. Kohustatud üksuste ring kasvas mitme tuhandeni: lisaks elutähtsate teenuste osutajatele ja avalikule sektorile ka näiteks energeetika, transpordi, tervishoiu, joogivee, digitaristu, IT-teenuste, postiteenuste, jäätmekäitluse ja toidutootmise ettevõtted ([Legiscope](https://www.legiscope.com/blog/nis2-eestis.html)).

Olulisemad kohustused:

- **riskipõhised turvameetmed** (NIS2 art 21): riskianalüüs, intsidentide käsitlus, toimepidevus ja varundus, tarneahela turve, turvaline arendus ja hooldus, krüptograafia, juurdepääsuhaldus, MFA, koolitus ja küberhügieen;
- **juhtkonna vastutus**: juhtkond kinnitab riskijuhtimise meetmed, jälgib nende täitmist ja osaleb ise koolitustel;
- **intsidentidest teavitamine** RIA-le (31.4);
- **järelevalve**: RIA võib teha auditeid ja anda ettekirjutusi; NIS2 näeb rikkumiste eest ette ka suuri trahve.

### 31.3 Isikuandmete kaitse

**Isikuandmete kaitse üldmäärus** (IKÜM, GDPR) ja Eesti **isikuandmete kaitse seadus** kehtivad kõigile, kes töötlevad isikuandmeid, sh koolidele, KOV-idele ja väikeettevõtetele. Turbe seisukohalt on olulised:

- **art 25 – lõimitud ja vaikimisi andmekaitse**: süsteemi kavandatakse algusest peale nii, et see kogub ainult vajalikke andmeid ja piirab ligipääsu;
- **art 32 – töötlemise turvalisus**: krüpteerimine, pseudonüümimine, konfidentsiaalsus, terviklus, käideldavus ja taastamisvõime ning meetmete regulaarne testimine;
- **art 33–34 – rikkumisest teavitamine**: Andmekaitse Inspektsioonile 72 tunni jooksul, kui rikkumine võib ohustada inimeste õigusi; inimesi endid, kui risk on kõrge. Kui andmed olid tugevalt krüpteeritud, ei ole inimeste teavitamine üldjuhul vajalik (ptk 26 näide).

### 31.4 Intsidendist teavitamine

![intsidendist teavitamise tähtajad · NIS2, GDPR, CRA](../pildid/31-b.png)

Joonis: teavitamise tähtajad. KüTS-i (NIS2) järgi saadab üksus olulisest intsidendist RIA-le (CERT-EE-le) **varajase hoiatuse 24 tunni** jooksul, **intsidendi teavituse 72 tunni** jooksul ja **lõpparuande ühe kuu** jooksul. Isikuandmetega seotud rikkumisest teavitatakse Andmekaitse Inspektsiooni 72 tunni jooksul. Küberkerksuse määruse (CRA) järgi peavad tootjad alates 11. septembrist 2026 teatama aktiivselt ärakasutatavatest nõrkustest oma toodetes.

Intsidendi ajal on lihtne teavitamine unustada. Seepärast kirjutatakse intsidendile reageerimise plaani (ptk 22) ette: kes otsustab, kas intsident on oluline, kes teavitab ja millise kanali kaudu, ning kus on teavitusvormid.

### 31.5 Standardid ja raamistikud

| Raamistik | Mis see on | Kellele |
| --- | --- | --- |
| **E-ITS** | Eesti infoturbestandard, RIA haldab; põhineb Saksa BSI IT-Grundschutzil; moodulid ja meetmed | kohustuslik riigi- ja KOV-asutustele, sobib ka ettevõtetele |
| **ISO/IEC 27001:2022** | rahvusvaheline infoturbe juhtimissüsteemi standard (ISMS), sertifitseeritav | ettevõtted, kes vajavad sertifikaati klientidele |
| **NIST CSF 2.0** | USA raamistik: Govern, Identify, Protect, Detect, Respond, Recover | üldine struktuur, tasuta |
| **CIS Controls** | 18 prioriseeritud tehnilist kontrolli | väiksemad organisatsioonid, kiire algus |
| **Microsoft Security Baselines** | soovitatud Windowsi ja M365 turvaseaded | Windowsi haldajad (ptk 22) |

E-ITS-i täisrakendamine on suur projekt. Alates 2025. aasta oktoobrist on osal ettevõtetel võimalus standardi asemel rakendada esmaste turvameetmete komplekti.

### 31.6 Riskijuhtimine

![riskijuhtimise tsükkel ja riskimaatriks](../pildid/31-c.png)

Joonis: riskijuhtimise tsükkel ja lihtne 3 × 3 riskimaatriks. Riskianalüüs (ptk 24.2) seob tehnika juhtimisega: juhtkond ei pea teadma, mis on Kerberos, kuid peab otsustama, kas „andmeleke paikamata VPN-i kaudu“ on vastuvõetav risk. Jääkriski aktsepteerimine on juhtkonna allkirjastatud otsus, mitte vaikimine.

Rollid organisatsioonis:

- **juhtkond** – vastutab, kinnitab riskid ja eelarve;
- **infoturbejuht** (CISO) – koordineerib turvet, raporteerib juhtkonnale;
- **andmekaitsespetsialist** (DPO) – nõustab isikuandmete küsimustes, kohustuslik nt avalikus sektoris;
- **IT-administraator** – rakendab tehnilisi meetmeid, logib ja teavitab;
- **iga töötaja** – järgib reegleid ja teatab kahtlastest sündmustest.

### Näide: lunavara väikeses tootmisettevõttes

Toiduainetetootja (KüTS-i mõistes oluline üksus) avastab esmaspäeval kell 8, et failiserveri failid on krüpteeritud. Mis peab juhtuma?

1. **Kohe**: IT-administraator isoleerib serveri võrgust, säilitab logid ja teavitab juhtkonda (intsidendiplaan, ptk 22).
2. **24 h jooksul**: varajane hoiatus RIA-le (CERT-EE). Kas rünne võib olla pahatahtlik ja mõjutada teisi? Jah.
3. **72 h jooksul**: intsidendi teavitus RIA-le koos esialgse hinnanguga. Failiserveril olid töötajate andmed, kuid kas need lekkisid? Kui ei saa välistada, teavitatakse ka Andmekaitse Inspektsiooni.
4. **Taastamine**: võrguta varukoopiast (ptk 24 näide), pärast seda, kui sissetungi tee on suletud.
5. **Ühe kuu jooksul**: lõpparuanne RIA-le: põhjus, mõju, rakendatud meetmed.
6. **Järel**: juhtkond vaatab üle riskianalüüsi ja eelarve; töötajad saavad koolituse; MFA ja segmentimine lisatakse tegevuskavasse.

### Kokkuvõte

- Küberturvalisuse seadus (NIS2) kehtib Eestis 1. jaanuarist 2026 ja nõuab riskipõhiseid meetmeid, juhtkonna vastutust ja intsidentidest teavitamist.
- Isikuandmete kaitse nõuab turvalist töötlemist ja rikkumisest teavitamist 72 tunni jooksul.
- E-ITS, ISO 27001 ja NIST CSF annavad struktuuri; riskijuhtimine seob tehnika ja juhtimise.
- Teavitamise tähtajad (24 h, 72 h, 1 kuu) tuleb kirjutada intsidendiplaani enne intsidenti.

### Kontrollküsimused

1. Mis muutus Eestis 1. jaanuaril 2026 ja keda see puudutab?
2. Nimeta viis NIS2 artikli 21 nõutud turvameedet.
3. Mida tähendab juhtkonna vastutus NIS2 järgi?
4. Millal ja kellele tuleb teatada isikuandmetega seotud rikkumisest?
5. Kirjelda NIS2 kolmeetapilist teavitamist.
6. Mis vahe on E-ITS-il ja ISO/IEC 27001-l?
7. Milline on riski käsitlemise neli valikut? Too igaühe kohta näide.

### Valikküsimused

1. Millise seadusega võeti Eestis üle NIS2-direktiiv? a) isikuandmete kaitse seadus b) küberturvalisuse seadus c) karistusseadustik d) avaliku teabe seadus
2. Mis aja jooksul tuleb NIS2 järgi olulisest intsidendist saata varajane hoiatus? a) ühe nädala b) ühe kuu c) 72 tunni d) 24 tunni
3. Kellele teatatakse Eestis isikuandmetega seotud rikkumisest? a) Andmekaitse Inspektsioonile b) Politsei- ja Piirivalveametile c) Maksu- ja Tolliametile d) Tarbijakaitse ja Tehnilise Järelevalve Ametile
4. Mida tähendab riski aktsepteerimine? a) risk kõrvaldatakse täielikult b) risk kantakse kindlustusele c) juhtkond otsustab teadlikult jääkriski kanda d) süsteem lülitatakse välja

### Praktiline ülesanne

**Turvaaudit ja nõuetele vastavus WSL-is.** Ava WSL Ubuntu ja käivita turvaauditi tööriist Lynis:

```bash
sudo apt install -y lynis
sudo lynis audit system --quick | tee lynis.txt
grep -E 'Warning|Suggestion' /var/log/lynis-report.dat | head -20
grep 'hardening_index' /var/log/lynis-report.dat
```

Windowsis (PowerShell administraatorina) vaata, kas olulised kaitsed on sees:

```powershell
Get-MpComputerStatus | Select AMServiceEnabled, RealTimeProtectionEnabled, AntivirusSignatureLastUpdated
Get-BitLockerVolume | Select MountPoint, ProtectionStatus
Get-NetFirewallProfile | Select Name, Enabled
```

Vali Lynise ja Windowsi tulemustest viis leidu. Seo igaüks NIS2 artikli 21 meetmega (nt „krüptograafia“, „juurdepääsuhaldus“) ja hinda riskimaatriksiga.

Vasta: milline oli Lynise hardening index? Milline leid on kõige suurema riskiga ja miks? Milline meede seda vähendaks?

### Iseseisvad ülesanded

**1. Kas meie organisatsioon on KüTS-i subjekt?** Vali Eesti organisatsioon (kool, KOV, haigla, ettevõte). Uuri RIA NIS2 lehelt, kas see kuulub olulise või väga olulise üksuse hulka, ja selgita, millised kohustused sellest tulenevad. **Esita:** 1 lk analüüs koos allikaviidetega.

**2. Intsidendi teavitamise plaan.** Koosta leheküljeline juhend, mida organisatsioon teeb intsidendi esimese 72 tunni jooksul: kes otsustab intsidendi olulisuse, kes teavitab RIA-d ja Andmekaitse Inspektsiooni, millised andmed kogutakse ja kuidas säilitatakse tõendeid. Kasuta näidet 31.6-st. **Esita:** juhend ja kontaktide tabel (rollid, mitte päris nimed).

### Viited

- [Küberturvalisuse seadus](https://www.riigiteataja.ee/akt/kyberturvalisuse-seadus). Riigi Teataja.
- Euroopa Parlament ja nõukogu. [Direktiiv (EL) 2022/2555 (NIS2)](https://eur-lex.europa.eu/eli/dir/2022/2555/oj).
- Euroopa Parlament ja nõukogu. [Määrus (EL) 2016/679 (IKÜM)](https://eur-lex.europa.eu/eli/reg/2016/679/oj).
- Euroopa Parlament ja nõukogu. [Määrus (EL) 2024/2847 (küberkerksuse määrus, CRA)](https://eur-lex.europa.eu/eli/reg/2024/2847/oj).
- RIA. [E-ITS – Eesti infoturbestandard](https://eits.ria.ee/).
- KPMG Eesti (2026). [Küberturvalisuse ja hädaolukorra seaduse muudatused](https://kpmg.com/ee/et/sundmused/2026/02/kueberturvalisuse-ja-haedaolukorra-seaduse-muudatused.html).
- Legiscope (2026). [NIS2 Eestis 2026: küberturvalisuse seadus ja RIA järelevalve](https://www.legiscope.com/blog/nis2-eestis.html).
- NIST. [Cybersecurity Framework 2.0](https://www.nist.gov/cyberframework) (2024).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 30. UUS: Postkvantkrüptograafia ja krüptograafia tulevik](30-postkvantkruptograafia.md) · [Sisukord](../README.md) · [Valikküsimuste vastused →](vastused.md)
