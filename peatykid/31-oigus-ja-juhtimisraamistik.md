[← 30. UUS: Postkvantkrüptograafia ja krüptograafia tulevik](30-postkvantkruptograafia.md) · [Sisukord](../README.md)

## 31. UUS: Küberturbe õigus- ja juhtimisraamistik

Tehnilised meetmed (peatükid 18, 22, 24–30) on mõjusad ainult siis, kui keegi vastutab nende eest, nende rakendamine on plaanitud ja intsidendi korral on teada, kellele ja millal teatada. Peatükk annab IT-süsteemi haldajale ülevaate Eesti ja Euroopa Liidu nõuetest. Peatükk ei ole õigusnõu: konkreetse organisatsiooni kohustused tuleb täpsustada RIA, Andmekaitse Inspektsiooni või juristiga.

Miks peab administraator seadusi tundma? Esiteks rakendab just tema enamikku nõutud meetmetest: logimist, varundust, MFA-d ja krüpteerimist. Teiseks on tema see, kes intsidenti esimesena näeb ja kelle tegevusest sõltub, kas tähtajad peetakse kinni. Kolmandaks on ka administraatoril endal seaduslikud piirid: suured õigused ei tähenda luba vaadata kõike, mida tehniliselt näha saab.

**Pärast peatüki läbimist õppija:**

- nimetab Eesti ja EL-i olulisemad küberturbe- ja andmekaitsenõuded ning nende sihtrühma;
- kirjeldab KüTS-i (NIS2) peamisi kohustusi ja intsidendist teavitamise tähtaegu;
- selgitab isikuandmete kaitse põhimõtteid ja rikkumisest teavitamise korda;
- võrdleb E-ITS-i, ISO/IEC 27001-t ja NIST CSF-i;
- hindab riski ja valib riski käsitlemise viisi;
- tunneb IT-spetsialisti seaduslikke ja eetilisi piire.

### 31.1 Nõuete kaart

![õigusraamistik · KüTS, GDPR, E-ITS, CRA, DORA, eIDAS](../pildid/31-a.png)

Joonis: kuus olulisemat nõuete kogumit. Need kattuvad osaliselt: haigla peab täitma nii küberturvalisuse seadust kui ka isikuandmete kaitse nõudeid, pank lisaks DORA-t ja tarkvaratootja CRA-d.

| Nõue | Mida reguleerib | Keda puudutab |
| --- | --- | --- |
| KüTS (NIS2) | organisatsiooni küberturbe meetmed ja intsidentidest teavitamine | elutähtsad ja olulised teenused, avalik sektor, paljud ettevõtted |
| IKÜM (GDPR) ja isikuandmete kaitse seadus | isikuandmete töötlemine ja kaitse | kõik, kes töötlevad isikuandmeid |
| E-ITS | Eesti infoturbestandard: konkreetsed meetmed | riigi- ja KOV-asutused, vabatahtlikult teised |
| CRA (küberkerksuse määrus) | digitaalsete toodete turvalisus kogu elukaare jooksul | riist- ja tarkvara tootjad, importijad, turustajad |
| DORA | finantssektori IKT-riskide juhtimine ja testimine | pangad, kindlustus, investeerimisühingud ja nende IT-tarnijad |
| eIDAS | e-identimine ja usaldusteenused (digiallkiri, ajatempel) | ID-kaardi, Smart-ID ja usaldusteenuste kasutajad ja osutajad |

Lisaks puudutab IT-t **tehisintellekti määrus** (AI Act, 2024/1689), mis seab tehisintellektisüsteemidele riskipõhised nõuded, ning **karistusseadustik**, mis määrab arvutikuriteod (31.7).

### 31.2 Küberturvalisuse seadus ja NIS2

Euroopa Liidu **NIS2-direktiiv** (2022/2555) võeti Eestis üle **küberturvalisuse seaduse** (KüTS) muudatustega, mis jõustusid 1. jaanuaril 2026 ([KPMG](https://kpmg.com/ee/et/sundmused/2026/02/kueberturvalisuse-ja-haedaolukorra-seaduse-muudatused.html)). Eestis oli küberturvalisuse seadus juba 2018. aastast, seega NIS2 laiendas olemasolevat raamistikku. Kohustatud üksuste ring kasvas mitme tuhandeni: lisaks elutähtsate teenuste osutajatele ja avalikule sektorile ka näiteks energeetika, transpordi, tervishoiu, joogivee, digitaristu, IT-teenuste, postiteenuste, jäätmekäitluse ja toidutootmise ettevõtted ([Legiscope](https://www.legiscope.com/blog/nis2-eestis.html)).

NIS2 jagab kohustatud organisatsioonid kaheks: **väga olulised üksused** (nt suured energia-, transpordi- ja tervishoiuettevõtted) ja **olulised üksused** (nt keskmise suurusega toidutootjad, postiteenused, digiteenuste pakkujad). Kohustused on sarnased, erinevad järelevalve viis ja trahvide suurus. Enamasti määrab kuuluvuse sektor ja ettevõtte suurus: üldjuhul alates 50 töötajast või 10 miljoni euro käibest. Organisatsioon peab ise hindama, kas ta on kohustatud, ja end RIA juures registreerima. RIA andmetel puudutab uus seadus ligi 6500 ettevõtet ([KPMG](https://kpmg.com/ee/et/sundmused/2026/02/kueberturvalisuse-ja-haedaolukorra-seaduse-muudatused.html)).

NIS2 üldreegel (art 2–3) seob kuuluvuse sektori ja ettevõtte suurusega. Suuruse määramisel loetakse töötajate arvu ning aastakäivet ja bilansimahtu:

| Ettevõtte suurus | Tingimus | NIS2 I lisa sektor (nt energeetika, transport, tervishoid, joogivesi, digitaristu, IKT-teenuste haldus) | NIS2 II lisa sektor (nt post, jäätmed, toit, keemia, tootmine, digiteenuse pakkujad) |
| --- | --- | --- | --- |
| Suur | vähemalt 250 töötajat või käive üle 50 mln € ja bilanss üle 43 mln € | väga oluline üksus | oluline üksus |
| Keskmine | vähemalt 50 töötajat või käive ja bilanss kumbki üle 10 mln € | oluline üksus | oluline üksus |
| Väike või mikro | alla 50 töötaja ja käive või bilanss kuni 10 mln € | üldjuhul ei kuulu | üldjuhul ei kuulu |

Suurusest olenemata kuuluvad kohustatute hulka näiteks kvalifitseeritud usaldusteenuse osutajad, tippdomeeni registrid ja DNS-teenuse osutajad, keskvalitsuse asutused ning elutähtsa teenuse osutajad. Tabel on üldistus: lõpliku kuuluvuse määravad KüTS ja RIA.

> 🖱️ **Proovi ise:** [NIS2 kuuluvuse hindaja](https://valdonolvak.github.io/Operatsioonis-steemi-ja-k-berturbe-alused/interaktiivne/31-oigus.html#nis2). Vali sektor ja suurus ning vaata, kas organisatsioon on väga oluline, oluline või ei kuulu üldjuhul kohustatute hulka.

Olulisemad kohustused:

- **riskipõhised turvameetmed** (NIS2 art 21): riskianalüüs, intsidentide käsitlus, toimepidevus ja varundus, tarneahela turve, turvaline arendus ja hooldus, krüptograafia, juurdepääsuhaldus, MFA, koolitus ja küberhügieen;
- **juhtkonna vastutus**: juhtkond kinnitab riskijuhtimise meetmed, jälgib nende täitmist ja osaleb ise koolitustel. Juhte saab rikkumiste eest isiklikult vastutusele võtta;
- **intsidentidest teavitamine** RIA-le (31.4);
- **järelevalve**: RIA võib teha auditeid ja anda ettekirjutusi. NIS2 näeb ette trahvid kuni 10 miljonit eurot või 2% ülemaailmsest käibest väga olulistele ja kuni 7 miljonit eurot või 1,4% olulistele üksustele.

Seadus ei ütle täpselt, millist tulemüüri või parooli pikkust kasutada. See nõuab, et meetmed oleksid **riskiga proportsionaalsed** ja võtavad arvesse tehnika taset. Praktikas tähendab see E-ITS-i, ISO 27001 või muu tunnustatud raamistiku kasutamist ja oma otsuste dokumenteerimist.

### 31.3 Isikuandmete kaitse

**Isikuandmete kaitse üldmäärus** (IKÜM, inglise keeles GDPR, määrus 2016/679) kehtib kogu EL-is otse ega vaja ülevõtmist. Eesti **isikuandmete kaitse seadus** (IKS) täiendab seda seal, kus üldmäärus jätab liikmesriikidele otsustusruumi. Järelevalvet teeb **Andmekaitse Inspektsioon** (AKI). Nõuded kehtivad kõigile, kes isikuandmeid töötlevad: koolidele, KOV-idele, haiglatele ja ka väikeettevõtetele. Erinevalt KüTS-ist ei sõltu need organisatsiooni suurusest ega sektorist.

Miks see IT-spetsialisti puudutab? Peaaegu iga süsteem, mida administraator haldab, sisaldab isikuandmeid: kasutajakontod, e-postkastid, logid, varukoopiad, kaamerasalvestised. Paljud üldmääruse nõuded on sisuliselt tehnilised otsused: millised väljad andmebaasis on, kui kaua logisid hoitakse, kes millele ligi pääseb ja kas varukoopia on krüpteeritud.

#### Mis on isikuandmed?

**Isikuandmed** on igasugune teave tuvastatud või tuvastatava inimese kohta (art 4). Oluline on sõna *tuvastatav*: piisab, kui inimese saab kindlaks teha teiste andmetega kombineerides.

| Andmed | Kas isikuandmed? | Selgitus |
| --- | --- | --- |
| Nimi, isikukood, e-post `mari.maasikas@kool.ee` | jah | tuvastavad inimese otse |
| IP-aadress, küpsise ID, seadme ID, auto numbrimärk | jah | inimese saab tuvastada teiste andmetega (nt internetiteenuse pakkuja kaudu) |
| Logirida `2026-03-02 08:14 mari logis sisse 10.0.5.23` | jah | näitab konkreetse inimese tegevust |
| Õpilase kood `Õ-4471`, kui koolil on tabel koodi ja nime vastavusega | jah | see on **pseudonüümitud** andmestik: tuvastamine on võimalik lisateabega |
| Statistika „9.b klassis on 24 õpilast, keskmine hinne 4,1“ | üldjuhul ei | **anonüümsed** andmed, millest inimest tuvastada ei saa |
| Ettevõtte e-post `info@firma.ee` | üldjuhul ei | ei ole seotud konkreetse inimesega |

Pseudonüümimine on hea turvameede, kuid pseudonüümitud andmed on endiselt isikuandmed. Anonüümseks muutub andmestik alles siis, kui tuvastamine ei ole mõistlikult võimalik ka teisi andmeid kasutades. Väike rühm on selles mõttes ohtlik: kui klassis on üks 15-aastane ratastooliga õpilane, siis „15-aastane, ratastool, 9.b“ tuvastab ta.

**Eriliigilised andmed** (art 9) on terviseandmed, geneetilised ja biomeetrilised andmed (nt sõrmejälg või näokujutis tuvastamiseks), rassiline või etniline päritolu, poliitilised vaated, usulised või maailmavaatelised veendumused, ametiühingusse kuulumine ning seksuaalelu ja sättumus. Neid tohib töödelda ainult erandjuhtudel ja nende kaitse peab olema tugevam. Kooli näited: õpilase tervisekaart, haridusliku erivajaduse andmed, töötaja haiguslehed. **Isikukood** ei ole eriliigiline andmeliik, kuid see seob kokku inimese andmed eri andmebaasides. Selle avaldamist ja asjatut kogumist tuleb vältida.

#### Rollid

| Roll | Kes see on | Kooli näide |
| --- | --- | --- |
| Andmesubjekt | inimene, kelle andmeid töödeldakse | õpilane, lapsevanem, töötaja |
| Vastutav töötleja (controller) | otsustab, milleks ja kuidas andmeid töödeldakse, ja vastutab | kool |
| Volitatud töötleja (processor) | töötleb andmeid vastutava töötleja nimel ja tema juhiste järgi | õppeinfosüsteemi pakkuja, pilveteenus, IT-hoolduse firma |
| Alamtöötleja | volitatud töötleja enda teenusepakkuja | õppeinfosüsteemi pakkuja kasutatav andmekeskus |
| Andmekaitsespetsialist (DPO) | nõustab ja kontrollib organisatsiooni sees | avaliku sektori asutuses kohustuslik |
| Järelevalveasutus | kontrollib, menetleb kaebusi, määrab karistusi | Andmekaitse Inspektsioon |

Vastutava ja volitatud töötleja vahel peab olema leping (art 28). Selles on kirjas, milliseid andmeid ja millisel eesmärgil töödeldakse, millised on turvameetmed, kas tohib kasutada alamtöötlejaid ja mis saab andmetest lepingu lõppedes. Kui IT-administraator valib uut pilveteenust, on lepingu ja turvameetmete kontroll osa valikust, mitte juristi hilisem töö.

#### Õiguslik alus: miks tohib andmeid töödelda?

Iga töötlemise jaoks peab olema üks kuuest õiguslikust alusest (art 6). Alus määratakse enne töötlemise algust ja seda ei saa hiljem vahetada.

| Alus | Millal | Näide |
| --- | --- | --- |
| a) nõusolek | inimene on vabatahtlikult nõustunud | kooli uudiskirja tellimine, õpilase foto kooli Facebooki lehel |
| b) leping | vajalik lepingu täitmiseks | e-poe tellimuse tarnimine, töötaja palgaarvestus |
| c) juriidiline kohustus | seadus nõuab | õpilase andmete kandmine EHIS-esse, raamatupidamisdokumentide säilitamine |
| d) eluliste huvide kaitse | elu või tervise kaitseks | teadvuseta inimese andmed kiirabile |
| e) avalik ülesanne | asutus täidab seadusest tulenevat ülesannet | kool korraldab õppetööd, KOV menetleb taotlust |
| f) õigustatud huvi | organisatsiooni huvi kaalub üles inimese huvid | turvalogid sissetungi avastamiseks, videovalve varguste vastu |

**Nõusolek ei ole alati õige valik.** See peab olema vabatahtlik, konkreetne, teadlik ja ühemõtteline ning selle tagasivõtmine peab olema sama lihtne kui andmine (art 7). Töösuhtes ja koolis on osapooled ebavõrdses olukorras: töötaja või õpilane ei pruugi julgeda keelduda. Seepärast toetub kool õppetöö korraldamisel seadusele ja avalikule ülesandele, mitte nõusolekule. Nõusolek sobib lisategevuseks, millest saab ilma tagajärgedeta keelduda. Eestis võib laps alates **13. eluaastast** ise anda nõusoleku talle otse pakutavas infoühiskonna teenuses (nt mängus või rakenduses). Noorema lapse puhul on vaja vanema nõusolekut (IKS § 8).

Õigustatud huvi kasutamiseks tuleb teha ja dokumenteerida **huvide kaalumine**: mis on organisatsiooni huvi, kas töötlemine on selleks vajalik ja kas see ei riiva inimese õigusi ülemäära. Näiteks turvalogide kogumine on põhjendatud, töötajate kõigi veebikülastuste salvestamine ja läbivaatamine üldjuhul mitte.

#### Põhimõtted

Andmetöötluse põhimõtted (art 5) on IT-süsteemi kavandamise nõuded:

| Põhimõte | Mida tähendab | IT-otsus |
| --- | --- | --- |
| Seaduslikkus, õiglus ja läbipaistvus | on õiguslik alus ja inimene teab, mis tema andmetega toimub | privaatsusteade veebilehel ja registreerimisvormi juures |
| Eesmärgipärasus | andmeid kasutatakse ainult kogumise eesmärgil | sissepääsukaardi logisid ei kasutata töötajate tööaja jälgimiseks, kui seda pole ette teatatud |
| Võimalikult väheste andmete kogumine | ainult vajalikud andmed | vormil pole kohustuslikku isikukoodi välja, kui seda vaja ei ole |
| Õigsus | andmed on ajakohased | kasutaja saab oma andmeid ise parandada |
| Säilitamise piiramine | andmeid hoitakse ainult vajaliku aja | logide automaatne kustutamine, varukoopiate aegumine, lahkunud töötaja konto sulgemine |
| Usaldusväärsus ja konfidentsiaalsus | sobivad turvameetmed | MFA, krüpteerimine, vähimad õigused |
| Vastutus | nõuete täitmist peab saama tõendada | töötlemise register, riskianalüüs, logid |

Seda koondab **lõimitud ja vaikimisi andmekaitse** (art 25): süsteem kavandatakse algusest peale nii, et see kogub ainult vajalikku ja vaikimisi kõige privaatsema valikuga. Näiteks uue kasutaja profiil ei ole vaikimisi avalik, vaid kasutaja peab selle ise avalikuks muutma.

#### Andmesubjekti õigused

Inimesel on oma andmete üle mitu õigust (art 12–22). Organisatsioon peab vastama ühe kuu jooksul. Keerulisel juhul saab tähtaega pikendada kahe kuu võrra, kuid sellest tuleb inimesele teatada.

| Õigus | Sisu | Mida see IT-le tähendab |
| --- | --- | --- |
| Teave (art 13–14) | inimene peab teadma, kes, mida ja miks töötleb | privaatsusteade |
| Tutvumine (art 15) | saada koopia oma andmetest | andmed peab üles leidma kõigist süsteemidest |
| Parandamine (art 16) | vigaste andmete parandamine | muutmise võimalus ja muutuste logi |
| Kustutamine (art 17) | „õigus olla unustatud“, kui alust töötlemiseks enam pole | kustutamine kõigist süsteemidest, sh volitatud töötlejatelt |
| Piiramine (art 18) | andmeid ei kasutata vaidluse ajal | konto või kirje külmutamine |
| Ülekantavus (art 20) | saada oma andmed masinloetaval kujul | eksport CSV või JSON vormingus |
| Vastuväide (art 21) | keelduda nt otseturundusest | turundusloendist eemaldamine |
| Automatiseeritud otsused (art 22) | õigus inimese otsusele | algoritmi otsust peab saama inimene üle vaadata |

**Kustutamine ja varukoopiad.** Andmeid ei pea igast varukoopiast kohe käsitsi välja otsima. Praktiline lahendus on varukoopiate kindel aegumine (nt 90 päeva) ja reegel, et taastamisel ei taastata uuesti juba kustutatud andmeid. See peab olema kirja pandud ja inimesele selgitatav.

#### Dokumentatsioon

- **Töötlemistoimingute register** (art 30): milliseid andmeid, millisel eesmärgil ja õiguslikul alusel, kellele edastatakse, kui kaua hoitakse ja millised on turvameetmed. See on andmekaitse „varade nimekiri“.
- **Andmekaitsealane mõjuhinnang** (DPIA, art 35) tehakse enne töötlemist, mis võib inimestele kaasa tuua kõrge riski: ulatuslik videovalve, eriliigiliste andmete ulatuslik töötlemine, profiilianalüüs ja uus tehnoloogia (nt näotuvastus või tehisintellekt õpilaste hindamisel). Mõjuhinnang on sisuliselt riskianalüüs (31.6) inimese vaatest.
- **Volitatud töötleja lepingud** (art 28) ja nende turvameetmete kontroll.
- **Rikkumiste register** (art 33 lg 5): iga rikkumine, ka siis, kui teavitamiskohustust ei teki.

**Andmed väljaspool EL-i.** Kui pilveteenus hoiab või töötleb andmeid väljaspool Euroopa Majanduspiirkonda, peab edastamiseks olema alus (art 44–49): Euroopa Komisjoni otsus, et riigi andmekaitse tase on piisav, või lepingus Euroopa Komisjoni standardtingimused koos täiendavate meetmetega. Enne teenuse valimist kontrolli, kus andmed asuvad ja millised alamtöötlejad neid näevad.

#### Töötlemise turvalisus

Artikkel 32 nõuab riskiga sobivaid tehnilisi ja korralduslikke meetmeid. Üldmäärus ei ütle, millist algoritmi kasutada, kuid nimetab eesmärgid, mis on otseselt seotud selle õpiku peatükkidega:

| Art 32 nõue | Tehniline meede | Peatükk |
| --- | --- | --- |
| Pseudonüümimine ja krüpteerimine | BitLocker, TLS, krüpteeritud varukoopiad | 26, 29 |
| Konfidentsiaalsus | vähimad õigused, MFA, paroolide räsimine | 18, 27 |
| Terviklus | räsid, digiallkiri, muudatuste logi | 27, 29 |
| Käideldavus ja taastamisvõime | varukoopiad, toimepidevus, harjutatud taastamine | 16, 22 |
| Meetmete regulaarne testimine | taastamise harjutused, haavatavuste skaneerimine, auditid | 22, 24 |

Hea tava on kasutada **testkeskkonnas** väljamõeldud või pseudonüümitud andmeid. Päris andmebaasi koopia arendaja sülearvutis on levinud rikkumise põhjus.

#### Rikkumine ja teavitamine

**Isikuandmetega seotud rikkumine** on turvanõrkus, mis põhjustab isikuandmete juhusliku või ebaseadusliku hävimise, kaotsimineku, muutmise, loata avalikustamise või neile loata juurdepääsu. See ei ole ainult häkkimine. Rikkumine on ka valele adressaadile saadetud hinnetetabel, kaotatud krüpteerimata mälupulk, lunavara poolt krüpteeritud andmebaas (käideldavuse rikkumine) või kogemata avalikuks jäetud pilvekaust. Iga rikkumine tuleb dokumenteerida sisemisse registrisse ka siis, kui teavitamiskohustust ei teki.

Kuidas otsustada, kellele teatada:

| Olukord | Sisemine register | Andmekaitse Inspektsioon (72 h) | Andmesubjektid |
| --- | --- | --- | --- |
| Rikkumine ei ohusta tõenäoliselt inimeste õigusi (nt kaotati tugevalt krüpteeritud sülearvuti, võti ei lekkinud) | jah | ei | ei |
| Rikkumine võib ohustada inimeste õigusi (nt hinnetetabel saadeti valele vanemale) | jah | jah | ei, kui risk ei ole kõrge |
| Rikkumine põhjustab tõenäoliselt kõrge riski (nt lekkisid terviseandmed või paroolid) | jah | jah | jah, põhjendamatu viivituseta |

72 tundi hakkab jooksma hetkest, kui organisatsioon saab rikkumisest teada, mitte siis, kui uurimine on lõppenud. Kui kõike ei ole veel teada, esitatakse teave osade kaupa. Teates kirjeldatakse rikkumise olemust, puudutatud inimeste ja andmete kategooriaid ning ligikaudset arvu, andmekaitsespetsialisti kontakti, tõenäolisi tagajärgi ja võetud või kavandatud meetmeid (art 33 lg 3). Inimestele kirjutatakse selges keeles, mis juhtus ja mida nad saavad ise teha, näiteks parooli vahetada või pangakaardi sulgeda.

#### Trahvid ja järelevalve Eestis

Üldmäärus näeb ette kaks trahvimäära: kuni **10 miljonit eurot või 2%** ülemaailmsest aastakäibest näiteks turvameetmete ja rikkumisest teavitamise nõuete eiramise eest ning kuni **20 miljonit eurot või 4%** põhimõtete, õigusliku aluse ja andmesubjekti õiguste rikkumise eest (art 83).

Eesti õigussüsteem ei tunne üldmääruses kirjeldatud haldustrahvi. Seepärast määrab AKI trahvi **väärteomenetluses** ([Legiscope](https://www.legiscope.com/blog/andmekaitse-eestis.html)). Sagedamini teeb AKI ettekirjutusi ja määrab sunniraha. Kaks juhtumit näitavad, et trahvid on siiski tõelised ja et menetluse aeg loeb:

- AKI karistas 2023. aastal Ida-Tallinna Keskhaiglat 200 000 euro suuruse trahviga, kuid Riigikohus lõpetas menetluse, sest väärtegu oli aegunud ([Riigikohus](https://www.riigiteataja.ee/public-api/api/v1/kohtuteave/kohtulahendid/373734680/file));
- Allium UPI-le määrati klientide andmete lekke eest 3 miljoni euro suurune trahv ja Harju Maakohus jättis ettevõtte kaebuse rahuldamata ([ERR](https://www.err.ee/1610126344/kohus-jattis-rahuldamata-alliumi-kaebuse-miljonitrahvi-tuhistamiseks)).

Lisaks trahvile võib rikkumisest kahju saanud inimene nõuda kahjuhüvitist (art 82) ja maine kahju on sageli suurem kui trahv.

#### Igapäevased olukorrad IT-s

- **Videovalve.** Eesmärk peab olema kindel (nt varguste ennetamine), kaamera ei tohi filmida riietusruume ega tualette, sissepääsu juures on teavitussilt, salvestisi hoitakse piiratud aja ja nende vaatamine logitakse.
- **Töötaja e-post ja jälgimine.** Töötajal on õigus eraelule ka tööl. Kontrolli reeglid kehtestatakse ette ja kirjalikult. Lahkunud töötaja postkasti ei jäeta lõputult avatuks: sinna seatakse automaatvastus ja hiljem suletakse.
- **Õpilaste fotod veebis.** Kooli avalikule lehele või sotsiaalmeediasse panekuks on üldjuhul vaja nõusolekut. Siselehel või õppeinfosüsteemis kehtivad teised reeglid.
- **Logid.** Logides on isikuandmed. Nende kogumine turvalisuse tagamiseks on põhjendatud, kuid neid hoitakse nii kaua, kui vaja (nt 6–12 kuud), ja ligipääs on piiratud.
- **Uus süsteem.** Enne kasutuselevõttu küsi: milliseid andmeid see kogub, millisel alusel, kus need asuvad (EL-is või väljaspool), kes neid näeb, kui kaua neid hoitakse ja kuidas need kustutatakse.

### 31.4 Intsidendist teavitamine

![intsidendist teavitamise tähtajad · NIS2, GDPR, CRA](../pildid/31-b.png)

Joonis: teavitamise tähtajad. KüTS-i (NIS2) järgi saadab üksus olulisest intsidendist RIA-le (CERT-EE-le) **varajase hoiatuse 24 tunni** jooksul, **intsidendi teavituse 72 tunni** jooksul ja **lõpparuande ühe kuu** jooksul pärast intsidendi teavitust. Isikuandmetega seotud rikkumisest teavitatakse Andmekaitse Inspektsiooni 72 tunni jooksul. Küberkerksuse määruse (CRA) järgi peavad tootjad alates 11. septembrist 2026 teatama aktiivselt ärakasutatavatest nõrkustest oma toodetes.

| Etapp | Tähtaeg | Sisu |
| --- | --- | --- |
| Varajane hoiatus | 24 h olulisest intsidendist teadasaamisest | mis juhtus, kas võib olla pahatahtlik, kas võib mõjutada teisi riike või organisatsioone |
| Intsidendi teavitus | 72 h teadasaamisest | esialgne hinnang: raskusaste, mõju, kompromiteerimise tunnused (IP-aadressid, failide räsid) |
| Vahearuanne | RIA nõudmisel | olukorra muutused |
| Lõpparuanne | 1 kuu pärast intsidendi teavitust ([KPMG](https://kpmg.com/ee/et/insights/2026/01/kueberjamadega-pannakse-nueued-juhatuse-pea-pakule.html)) | põhjus, mõju, rakendatud ja kavandatud meetmed |

Näide: oluline intsident avastatakse esmaspäeval kell 8.00 ja intsidendi teavitus saadetakse kolmapäeval kell 15.00. Varajase hoiatuse tähtaeg on teisipäeval kell 8.00, intsidendi teavituse oma neljapäeval kell 8.00 ja lõpparuande oma kuu aega pärast kolmapäevast teavitust. Kui on lekkinud ka isikuandmed, jookseb Andmekaitse Inspektsiooni 72-tunnine tähtaeg paralleelselt ja lõpeb samuti neljapäeval kell 8.00.

> 🖱️ **Proovi ise:** [teavitamise tähtaegade kalkulaator](https://valdonolvak.github.io/Operatsioonis-steemi-ja-k-berturbe-alused/interaktiivne/31-oigus.html#tahtajad). Sisesta avastamise aeg ja vaata, millal on iga tähtaeg ja kellele tuleb teatada.

**Mis on oluline intsident?** NIS2 järgi on intsident oluline, kui see on põhjustanud või võib põhjustada teenuse tõsise häire või rahalise kahju või mõjutada teisi isikuid olulise kahjuga. Kahtluse korral on mõistlikum teatada: RIA saab hoiatada teisi sama ründe sihtmärke ja anda abi. Teavitamine ei ole karistus, vaid osa riiklikust kaitsest.

Intsidendi ajal on lihtne teavitamine unustada. Seepärast kirjutatakse intsidendile reageerimise plaani (ptk 22) ette: kes otsustab, kas intsident on oluline, kes teavitab ja millise kanali kaudu, ning kus on teavitusvormid. Plaanis peab olema ka see, kuidas säilitada tõendeid (logid, mälutõmmis, ketta tõmmisfail koos räsiga, ptk 27.5), sest neid võib vaja minna politseile ja kindlustusele.

### 31.5 Standardid ja raamistikud

| Raamistik | Mis see on | Kellele |
| --- | --- | --- |
| **E-ITS** | Eesti infoturbestandard, RIA haldab; põhineb Saksa BSI IT-Grundschutzil; moodulid ja meetmed | kohustuslik riigi- ja KOV-asutustele, sobib ka ettevõtetele |
| **ISO/IEC 27001:2022** | rahvusvaheline infoturbe juhtimissüsteemi standard (ISMS), sertifitseeritav | ettevõtted, kes vajavad sertifikaati klientidele |
| **NIST CSF 2.0** | USA raamistik: Govern, Identify, Protect, Detect, Respond, Recover | üldine struktuur, tasuta |
| **CIS Controls** | 18 prioriseeritud tehnilist kontrolli | väiksemad organisatsioonid, kiire algus |
| **Microsoft Security Baselines** | soovitatud Windowsi ja M365 turvaseaded | Windowsi haldajad (ptk 22) |

**E-ITS** on ehitatud kataloogidena. Organisatsioon kirjeldab oma varad (süsteemid, ruumid, rakendused) ja valib igale sobivad **moodulid** (nt „Windows Server“, „Veebiserver“, „Varundus“). Iga mooduli juures on ohud ja **meetmed**, mis jagunevad põhi-, standard- ja kõrgmeetmeteks. Näiteks Windowsi kliendi moodul nõuab muu hulgas turvauuendusi, kasutajakontode haldust ja logimist. E-ITS-i täisrakendamine on suur projekt. Alates 2025. aasta oktoobrist on osal ettevõtetel võimalus standardi asemel rakendada esmaste turvameetmete komplekti.

**ISO/IEC 27001** ei anna valmis tehnilisi meetmeid, vaid nõuab **juhtimissüsteemi**: organisatsioon määrab ulatuse, hindab riskid, valib meetmed (lisa A-st 93 meetme seast), dokumenteerib otsused ja parendab pidevalt (PDCA-tsükkel: planeeri, teosta, kontrolli, täiusta). Sertifitseerimise auditi teeb sõltumatu audiitor.

### 31.6 Riskijuhtimine

![riskijuhtimise tsükkel ja riskimaatriks](../pildid/31-c.png)

Joonis: riskijuhtimise tsükkel ja lihtne 3 × 3 riskimaatriks. Riskianalüüs (ptk 24.3) seob tehnika juhtimisega: juhtkond ei pea teadma, mis on Kerberos, kuid peab otsustama, kas „andmeleke paikamata VPN-i kaudu“ on vastuvõetav risk.

Kui risk on hinnatud, on selle käsitlemiseks neli võimalust:

| Valik | Tähendus | Näide |
| --- | --- | --- |
| Vähendamine | rakendatakse meetmeid, mis vähendavad tõenäosust või mõju | MFA, paikamine, varukoopiad |
| Vältimine | loobutakse riskantsest tegevusest | vana äppi, mida ei saa paigata, enam ei kasutata |
| Jagamine (üleandmine) | osa riskist kantakse kolmandale osapoolele | küberkindlustus, teenuse sisseostmine pilvest |
| Aktsepteerimine | juhtkond otsustab teadlikult jääkriski kanda | väikese mõjuga süsteem, mille kaitse oleks kahjust kallim |

Jääkriski aktsepteerimine on juhtkonna allkirjastatud otsus, mitte vaikimine.

> 🖱️ **Proovi ise:** [riskimaatriks ja riskiregister](https://valdonolvak.github.io/Operatsioonis-steemi-ja-k-berturbe-alused/interaktiivne/31-oigus.html#risk). Hinda näidisriske, vali käsitlemise viis ja vaata, kuidas jääkrisk muutub. Riskide nimekiri (**riskiregister**) vaadatakse üle vähemalt kord aastas ja pärast suuremaid muudatusi või intsidente.

Rollid organisatsioonis:

- **juhtkond** – vastutab, kinnitab riskid ja eelarve;
- **infoturbejuht** (CISO) – koordineerib turvet, raporteerib juhtkonnale;
- **andmekaitsespetsialist** (DPO) – nõustab isikuandmete küsimustes, kohustuslik nt avalikus sektoris;
- **IT-administraator** – rakendab tehnilisi meetmeid, logib ja teavitab;
- **iga töötaja** – järgib reegleid ja teatab kahtlastest sündmustest.

### 31.7 IT-spetsialisti seaduslikud ja eetilised piirid

Administraatoril on sageli rohkem õigusi kui ükskõik millisel teisel töötajal. Need õigused on antud töö tegemiseks, mitte uudishimu rahuldamiseks.

- **Karistusseadustik** näeb ette vastutuse muu hulgas arvutisüsteemi ebaseadusliku kasutamise (§ 217), arvutiandmetesse sekkumise (§ 206), arvutisüsteemi toimimise takistamise (§ 207), pahavara levitamise (§ 208) ja arvutikelmuse (§ 213) eest. Ka „hea kavatsusega“ võõra süsteemi skaneerimine või nõrkuse proovimine ilma loata võib olla kuritegu.
- **Arvutikuriteo ettevalmistamine** (§ 216¹) – karistatav on ka sellise programmi, seadme või paroolide hankimine, valmistamine ja levitamine, mis on mõeldud eelkõige arvutikuritegude toimepanemiseks, kui eesmärk on kuritegu toime panna või võimaldada seda teisel. Turvatööriistad (nt Nmap, John the Ripper) on laboris õppimiseks lubatud. Nende kasutamine või jagamine võõraste süsteemide ründamiseks ei ole.
- **Turvatestimine** (penetration test) on lubatud ainult kirjaliku loaga, kus on kirjas ulatus (millised süsteemid), aeg ja meetodid. Kui leiad nõrkuse kogemata, teata sellest omanikule või CERT-EE-le ega proovi seda edasi ära kasutada (vastutustundlik avalikustamine). Kirjelda, mida nägid, kuidas see juhtus ja millal, kuid ära lae alla andmeid „tõenditeks“ ega jaga nõrkust avalikult enne, kui omanikul on olnud aega see parandada.
- **Töötajate andmed ja kirjavahetus.** Administraator võib näha teiste e-kirju ja faile, kuid neid ei tohi lugeda ilma õigusliku aluse ja organisatsiooni kehtestatud korrata. Iga ligipääs peaks olema põhjendatud ja logitud.
- **Logid on isikuandmed.** Logides on kasutajanimed, IP-aadressid ja tegevused. Neid hoitakse nii kaua, kui on vajalik, ja ligipääs neile on piiratud.

### Näide: lunavara väikeses tootmisettevõttes

Toiduainetetootja (KüTS-i mõistes oluline üksus) avastab esmaspäeval kell 8, et failiserveri failid on krüpteeritud. Mis peab juhtuma?

1. **Kohe**: IT-administraator isoleerib serveri võrgust, säilitab logid ja teavitab juhtkonda (intsidendiplaan, ptk 22). Serverit ei lülitata välja enne, kui mälutõmmis on tehtud, sest mälus võivad olla tõendid.
2. **24 h jooksul**: varajane hoiatus RIA-le (CERT-EE). Kas rünne võib olla pahatahtlik ja mõjutada teisi? Jah.
3. **72 h jooksul**: intsidendi teavitus RIA-le koos esialgse hinnanguga. Failiserveril olid töötajate andmed, kuid kas need lekkisid? Kui ei saa välistada, teavitatakse ka Andmekaitse Inspektsiooni. Kuriteoteade politseile.
4. **Taastamine**: võrguta varukoopiast (ptk 24 näide), pärast seda, kui sissetungi tee on suletud ja paroolid vahetatud.
5. **Ühe kuu jooksul pärast intsidendi teavitust**: lõpparuanne RIA-le: põhjus, mõju, rakendatud meetmed.
6. **Järel**: juhtkond vaatab üle riskianalüüsi ja eelarve; töötajad saavad koolituse; MFA ja segmentimine lisatakse tegevuskavasse.

Lunaraha maksmist riik ei soovita: see ei taga andmete tagasisaamist ega seda, et varastatud andmed avaldamata jäävad, ning rahastab järgmisi rünnakuid.

### Kokkuvõte

- Küberturvalisuse seadus (NIS2) kehtib Eestis uuel kujul 1. jaanuarist 2026 ja nõuab riskipõhiseid meetmeid, juhtkonna vastutust ja intsidentidest teavitamist.
- Isikuandmete kaitse nõuab õiguslikku alust, andmete minimeerimist, turvalist töötlemist, andmesubjekti õiguste tagamist ja rikkumisest teavitamist 72 tunni jooksul. Paljud nõuded on sisuliselt IT-otsused.
- E-ITS, ISO 27001 ja NIST CSF annavad struktuuri; riskijuhtimine seob tehnika ja juhtimise. Riski saab vähendada, vältida, jagada või aktsepteerida.
- Teavitamise tähtajad (24 h, 72 h ja lõpparuanne 1 kuu pärast teavitust) tuleb kirjutada intsidendiplaani enne intsidenti.
- Administraatori õigused on antud tööks: võõraid süsteeme testitakse ainult kirjaliku loaga.

### Kontrollküsimused

> 🧪 **Interaktiivne test:** [kontrolli oma teadmisi](https://valdonolvak.github.io/Operatsioonis-steemi-ja-k-berturbe-alused/interaktiivne/31-test.html). Test koosneb 30 valikvastusega küsimusest. Iga vastuse järel näed selgitust ning lõpus saad valesti vastatud küsimusi korrata.

1. Mis muutus Eestis 1. jaanuaril 2026 ja keda see puudutab?
2. Mis vahe on väga olulisel ja olulisel üksusel?
3. Nimeta viis NIS2 artikli 21 nõutud turvameedet.
4. Mida tähendab juhtkonna vastutus NIS2 järgi?
5. Mis vahe on vastutaval ja volitatud töötlejal? Too näide koolist.
6. Nimeta kolm isikuandmete töötlemise põhimõtet, mis on otseselt IT-otsused.
7. Too kolm näidet isikuandmetega seotud rikkumisest, mis ei ole häkkimine.
8. Millal ja kellele tuleb teatada isikuandmetega seotud rikkumisest?
9. Kirjelda NIS2 kolmeetapilist teavitamist.
10. Mis vahe on E-ITS-il ja ISO/IEC 27001-l?
11. Millised on riski käsitlemise neli valikut? Too igaühe kohta näide.
12. Millal on turvatestimine seaduslik ja mida teha, kui leiad kogemata võõra süsteemi nõrkuse?
13. Kas IP-aadress ja pseudonüümitud andmed on isikuandmed? Aga anonüümne statistika? Põhjenda.
14. Nimeta kuus õiguslikku alust. Miks ei ole nõusolek kooli õppetöö korraldamisel sobiv alus?
15. Inimene soovib oma andmete koopiat ja hiljem nende kustutamist. Mida peab IT-süsteem selleks võimaldama ja mis saab varukoopiatest?

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

**2. Intsidendi teavitamise plaan.** Koosta leheküljeline juhend, mida organisatsioon teeb intsidendi esimese 72 tunni jooksul: kes otsustab intsidendi olulisuse, kes teavitab RIA-d ja Andmekaitse Inspektsiooni, millised andmed kogutakse ja kuidas säilitatakse tõendeid. Kasuta peatüki näidet „Lunavara väikeses tootmisettevõttes“. **Esita:** juhend ja kontaktide tabel (rollid, mitte päris nimed).

### Viited

- [Küberturvalisuse seadus](https://www.riigiteataja.ee/akt/kyberturvalisuse-seadus). Riigi Teataja.
- Euroopa Parlament ja nõukogu. [Direktiiv (EL) 2022/2555 (NIS2)](https://eur-lex.europa.eu/eli/dir/2022/2555/oj).
- Euroopa Parlament ja nõukogu. [Määrus (EL) 2016/679 (IKÜM)](https://eur-lex.europa.eu/eli/reg/2016/679/oj).
- Euroopa Parlament ja nõukogu. [Määrus (EL) 2024/2847 (küberkerksuse määrus, CRA)](https://eur-lex.europa.eu/eli/reg/2024/2847/oj).
- RIA. [E-ITS – Eesti infoturbestandard](https://eits.ria.ee/).
- KPMG Eesti (2026). [Küberturvalisuse ja hädaolukorra seaduse muudatused](https://kpmg.com/ee/et/sundmused/2026/02/kueberturvalisuse-ja-haedaolukorra-seaduse-muudatused.html).
- KPMG Eesti (2026). [Küberjamadega pannakse nüüd juhatuse pea pakule](https://kpmg.com/ee/et/insights/2026/01/kueberjamadega-pannakse-nueued-juhatuse-pea-pakule.html).
- [Karistusseadustik](https://www.riigiteataja.ee/akt/karistusseadustik). Riigi Teataja.
- Legiscope (2026). [NIS2 Eestis 2026: küberturvalisuse seadus ja RIA järelevalve](https://www.legiscope.com/blog/nis2-eestis.html).
- NIST. [Cybersecurity Framework 2.0](https://www.nist.gov/cyberframework) (2024).

> 🧪 **Test:** [peatüki 31 interaktiivne test](https://valdonolvak.github.io/Operatsioonis-steemi-ja-k-berturbe-alused/interaktiivne/31-test.html).

---

[← 30. UUS: Postkvantkrüptograafia ja krüptograafia tulevik](30-postkvantkruptograafia.md) · [Sisukord](../README.md)
