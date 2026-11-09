[← 24. UUS: Küberturbe põhimõisted ja ründevektorid](24-kuberturbe-pohimoisted.md) · [Sisukord](../README.md) · [26. UUS: Sümmeetriline krüpteerimine →](26-summeetriline-kruptograafia.md)

## 25. UUS: Krüptograafia ajalugu ja põhimõisted

**Krüptograafia** (kreeka *kryptos* – peidetud, *graphein* – kirjutama) on teadus andmete kaitsmisest matemaatiliste meetoditega. Tema abil saavutatakse konfidentsiaalsus, terviklus, autentsus ja mittesalgamine (ptk 24.2). Windowsis kasutavad krüptograafiat BitLocker (ptk 16), Kerberos (ptk 17), Secure Boot ja draiverite allkirjad (ptk 18) ning iga HTTPS-ühendus.

Selles peatükis vaatame, kuidas inimesed on kahe ja poole tuhande aasta jooksul sõnumeid peitnud ja kuidas neid peidetud sõnumeid on murtud. Ajalugu ei ole siin eesmärk omaette: iga murtud šiffer õpetas midagi, millele kaasaegne krüptograafia toetub. Kui mõistad, miks Caesari šiffer, Vigenère ja Enigma murti, mõistad ka, miks AES on ehitatud nii, nagu ta on.

**Pärast peatüki läbimist õppija:**

- kasutab õigesti mõisteid avatekst, šifritekst, algoritm, võti ja võtmeruum;
- selgitab Kerckhoffsi printsiipi ja seda, miks salajane algoritm ei ole turvaline;
- krüpteerib ja murrab Caesari ning Vigenère’i šifri ning selgitab sagedusanalüüsi;
- selgitab ühekordse ploki ja XOR-tehte põhimõtet;
- kirjeldab Enigma tööpõhimõtet ja murdmise põhjusi;
- eristab kodeerimist, krüpteerimist ja steganograafiat ning selgitab juhuslikkuse tähtsust.

### 25.1 Põhimõisted

![krüpteerimise mudel · saatja, võti, šifritekst, pealtkuulaja, saaja](../pildid/25-a.png)

Joonis: krüpteerimise üldmudel. Saatja (traditsiooniliselt *Alice*) krüpteerib avateksti võtmega, saaja (*Bob*) dekrüpteerib selle. Pealtkuulaja (*Eve*, inglise *eavesdropper*) näeb kanalis šifriteksti, kuid ilma võtmeta ei saa ta sellest avateksti tagasi.

| Mõiste | Tähendus |
| --- | --- |
| Avatekst (plaintext) | algne loetav sõnum või fail |
| Šifritekst (ciphertext) | krüpteeritud, ilma võtmeta loetamatu kuju |
| Šiffer, algoritm (cipher) | reegel, mille järgi krüpteeritakse ja dekrüpteeritakse |
| Võti (key) | salajane väärtus, mis määrab algoritmi täpse tulemuse |
| Krüptoanalüüs | šifrite murdmise teadus |
| Toore jõu rünne (brute force) | kõigi võimalike võtmete läbiproovimine |
| Võtmeruum | kõigi võimalike võtmete hulk, nt 128-bitisel võtmel 2¹²⁸ |

**Kui suur on 2¹²⁸?** See on umbes 3,4 × 10³⁸. Kui miljard arvutit proovivad igaüks miljard võtit sekundis, kulub kõigi võtmete läbimiseks umbes 10¹³ aastat – ligikaudu tuhat korda kauem, kui universum on olemas olnud. Iga lisabitt kahekordistab võtmeruumi: 56-bitine DES-võti on 2⁷² korda „väiksem“ kui 128-bitine AES-võti. Seetõttu ei murta kaasaegseid šifreid toore jõuga, vaid otsitakse vigu nende kasutamises.

**Kodeerimine ei ole krüpteerimine.** Base64, URL-kodeering ja heksakuju muudavad andmete esitusviisi, kuid neil puudub võti: igaüks saab need tagasi muuta. **Steganograafia** peidab sõnumi olemasolu (nt tõmmisfaili pikslitesse), krüptograafia peidab sõnumi sisu. Neid võib kombineerida: krüpteeritud sõnum peidetakse tõmmisesse.

### 25.2 Kerckhoffsi printsiip

Auguste Kerckhoffs sõnastas 1883. aastal reegli, mis kehtib siiani: **süsteem peab jääma turvaliseks ka siis, kui vastane teab kõike peale võtme**. Sama põhimõtet väljendab Shannoni maksiim: „vaenlane tunneb süsteemi“.

Miks see nii on? Algoritmi on raske saladuses hoida: see on igas seadmes, igas tarkvarakoopias ja iga arendaja peas. Kui algoritm lekib, tuleb kogu süsteem välja vahetada. Võtit on aga lihtne vahetada. Teiseks on avalikku algoritmi aastaid analüüsinud tuhanded teadlased ja kui keegi pole seda murdnud, on sellel põhjust usaldada. Salajast „omatehtud“ algoritmi on vaadanud vaid selle autor.

Näited salajastest algoritmidest, mis murti kohe pärast avalikuks tulekut:

- **MIFARE Classic** kiipkaartide (ühistranspordi- ja uksekaardid) algoritm Crypto-1 murti 2008. aastal pärast seda, kui teadlased kiibi kihtide kaupa pildistasid ja algoritmi taastasid;
- **DVD-de CSS**-kaitse murti 1999. aastal ja selle 40-bitine võti oli toore jõuga leitav;
- **GSM-i A5/1** mobiilsidekrüpto leiti ja murti, kuigi seda hoiti aastaid saladuses.

### 25.3 Ajalugu

![krüptograafia ajajoon · Caesarist postkvantstandarditeni](../pildid/25-b.png)

Joonis: krüptograafia arengu verstapostid. Kuni 20. sajandini oli krüptograafia käsitöö ja selle saladus oli tihti algoritm ise. Pärast Shannoni tööd 1949. aastal sai sellest matemaatika haru ja avalikud standardid.

#### Transpositsioon: skütale

Vanimad šifrid ei asendanud tähti, vaid **muutsid nende järjekorda** (transpositsioon). Spartalased kerisid nahkriba ümber kindla jämedusega puupulga (**skütale**) ja kirjutasid sõnumi piki pulka. Lahtikeritud ribal olid tähed segamini. Lugeda sai ainult sama jämedusega pulga abil, seega oli pulga jämedus võti. Näide: kirjutame `TULEHOMMIKUL` kolmetäheliste ridadena `TUL`, `EHO`, `MMI`, `KUL` üksteise alla ja loeme seejärel veergude kaupa ülevalt alla: `TEMK UHMU LOIL`. Tähed on samad, ainult järjekord on muutunud.

#### Caesari šiffer

![Caesari šiffer · nihe 3](../pildid/25-c.png)

Joonis: Caesari šifris asendatakse iga täht tähega, mis asub tähestikus kolm kohta eespool.

Kui nummerdame tähed A = 0, B = 1, …, Z = 25, saame Caesari šifri valemina kirja panna:

- krüpteerimine: **C = (P + k) mod 26**
- dekrüpteerimine: **P = (C − k) mod 26**

kus P on avateksti tähe number, C šifriteksti tähe number ja k võti (nihe). Tehe *mod 26* tähendab jagamisjääki 26-ga: kui läheme Z-ist edasi, alustame uuesti A-st. Näiteks täht X = 23, võti 3: (23 + 3) mod 26 = 0, ehk A.

Caesari šifri võtmeruum on ainult 25 võtit (nihe 0 ei muuda midagi). Ründaja proovib kõik läbi ja näeb kohe, milline tulemus on loetav. **ROT13** on Caesari šiffer nihkega 13. Kuna 13 + 13 = 26, on krüpteerimine ja dekrüpteerimine sama tehe. ROT13 kasutatakse foorumites spoilerite peitmiseks, mitte turvalisuseks.

#### Asendusšifrid ja sagedusanalüüs

Üldisemas **asendusšifris** võib iga täht asenduda suvalise teise tähega: A → Q, B → M, C → Z jne. Võti on kogu asendustabel. Võimalikke tabeleid on 26! ≈ 4 × 10²⁶ – rohkem kui 2⁸⁸. Toore jõuga neid läbi proovida ei saa.

Araabia teadlane **al-Kindi** kirjeldas 9. sajandil siiski meetodit, mis murrab iga lihtsa asendusšifri mõne minutiga: **sagedusanalüüsi**. Igas keeles esinevad mõned tähed teistest sagedamini. Eesti tekstis on kõige sagedasemad *a*, *e*, *i*, *s*, *t*, *l* ja *u*; inglise tekstis *e*, *t*, *a* ja *o*. Asendusšiffer muudab tähtede kuju, kuid mitte sagedust. Murdmine käib nii:

- loe kokku iga šifriteksti tähe esinemised;
- oleta, et sagedaseim šifriteksti täht vastab keele sagedaseimale tähele (eesti keeles *a* või *e*);
- otsi sagedasi tähepaare ja lühikesi sõnu (eesti keeles *ja*, *on*, *et*, *ei*);
- asenda oletatud tähed ja paranda, kuni tekst muutub loetavaks, nagu ristsõna lahendades.

**Õppetund:** suur võtmeruum on vajalik, kuid mitte piisav. Kui šifritekst reedab avateksti struktuuri (sagedused, mustrid, korduvad osad), saab šifri murda ilma võtmeid läbi proovimata.

#### Vigenère’i šiffer

**Vigenère’i šiffer** (16. saj) kasutab võtmesõna ja mitut Caesari nihet vaheldumisi. Võtmesõna kirjutatakse avateksti alla korduvalt ja iga täht nihutatakse võtmetähe võrra (A = 0, B = 1, …, K = 10, L = 11, O = 14).

|  |  |  |  |  |  |  |  |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Avatekst | T | U | L | E | B | H | O | M | M | I | K | U | L |
| Võti | K | O | O | L | K | O | O | L | K | O | O | L | K |
| Nihe | 10 | 14 | 14 | 11 | 10 | 14 | 14 | 11 | 10 | 14 | 14 | 11 | 10 |
| Šifritekst | D | I | Z | P | L | V | C | X | W | W | Y | F | V |

Vaata sõna *HOMMIKUL* kahte *M*-i: üks muutus *X*-iks ja teine *W*-ks, sest nende all olid erinevad võtmetähed. Teisalt muutusid *M* ja *I* mõlemad *W*-ks. Sama täht ei anna alati sama tulemust ja erinevad tähed võivad anda sama tulemuse, seega lihtne sagedusanalüüs enam ei tööta. Vigenère’i peeti kolmsada aastat murdmatuks (*le chiffre indéchiffrable*).

Nõrkus on aga võtme kordumine. Kui võti on 4 tähte pikk, on šifritekst tegelikult neli vaheldumisi kirjutatud Caesari šifrit. **Kasiski meetod** (1863; Charles Babbage leidis sama juba varem) otsib šifritekstist korduvaid tähejadasid. Kui sama avateksti sõna satub sama võtmeosa alla, tekib sama šifritekst. Korduste vahekaugused on siis võtme pikkuse kordsed. Kui kaugused on näiteks 12, 20 ja 32, on nende suurim ühistegur 4 – tõenäoliselt võtme pikkus. Seejärel jagatakse šifritekst neljaks osaks (iga neljas täht) ja igaüks murtakse sagedusanalüüsiga nagu Caesar.

#### Ühekordne plokk ja XOR

Vigenère’i nõrkus on võtme kordumine. Mis juhtub, kui võti on sama pikk kui sõnum, täiesti juhuslik ja seda kasutatakse **ainult üks kord**? Siis saame **ühekordse ploki** (one-time pad, OTP). Claude Shannon tõestas 1949. aastal, et see on **täiuslikult turvaline**: šifritekst ei anna avateksti kohta mingit infot, sest iga sama pikk avatekst on võrdselt võimalik. Šifritekst `XQZB` võib olla nii `TERE` kui ka `KASS` – sõltub ainult võtmest.

Arvutis tehakse ühekordset plokki **XOR**-tehtega (välistav või, tähis ⊕). XOR võrdleb bitte: kui bitid on erinevad, on tulemus 1, kui samad, siis 0.

| A | B | A ⊕ B |
| --- | --- | --- |
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

Näide: täht *T* on ASCII-s `01010100`. Võtmebait on `01011010`. XOR annab `00001110`. Kui teeme sama võtmega uuesti XOR-i, saame tagasi `01010100`, ehk *T*. XOR-i omadus **(P ⊕ K) ⊕ K = P** tähendab, et krüpteerimine ja dekrüpteerimine on sama tehe. Sõna `TERE` (heksas `54 45 52 45`) XOR võti `5a 13 c4 7e` annab šifriteksti `0e 56 96 3b`.

Miks siis kõiki sõnumeid nii ei krüpteerita?

- Võti peab olema sõnumiga sama pikk. 1 GB faili jaoks on vaja 1 GB võtit, mis tuleb saajale turvaliselt toimetada. Kui sul on selleks turvaline kanal, võid sama kanaliga saata ka sõnumi.
- Võtit ei tohi **kunagi** uuesti kasutada. Kui kaks sõnumit on krüpteeritud sama võtmega, siis C₁ ⊕ C₂ = P₁ ⊕ P₂ ja võti kaob arvutusest. Kahe avateksti XOR-ist saab neid mõlemaid sageli taastada. USA projekt **Venona** murdis just nii osa Nõukogude Liidu sõnumitest, sest sõja ajal trükiti osa võtmelehti mitu korda.
- Võti peab olema tõeliselt juhuslik (vt 25.5).

Ühekordset plokki kasutati näiteks Moskva–Washingtoni kuuma liini kaitseks. Kaasaegsed voošifrid (nt ChaCha20) jäljendavad seda: lühikesest võtmest toodetakse pikk juhuslikuna näiv võtmevoog, millega avateksti XOR-itakse (ptk 26).

#### Enigma

Saksa **Enigma** oli elektromehaaniline rootorimasin, mida kasutati Teises maailmasõjas. Operaator vajutas klahvi ja lambipaneelil süttis šifriteksti täht.

![Enigma · signaali tee läbi rootorite ja reflektori](../pildid/25-d.png)

Joonis: Enigma signaali tee. Vool läbib pistikupaneeli, kolm rootorit, pöördub reflektoris tagasi, läbib rootorid ja pistikupaneeli uuesti ning süütab lambi.

- **Rootor** oli ketas, mille sisse oli juhtmetega kodeeritud üks asendustabel. Kolm rootorit valiti viie seast ja pandi kindlasse järjekorda.
- **Parem rootor pöördus iga klahvivajutusega** ühe sammu, nagu auto läbisõidumõõdiku number. Seega muutus asendustabel iga tähe järel ja sama täht krüpteerus iga kord erinevalt – nagu väga pika võtmega Vigenère.
- **Pistikupaneel** (Steckerbrett) vahetas enne ja pärast rootoreid 10 tähepaari, nt A ↔ F.
- **Reflektor** saatis voolu rootorite kaudu tagasi. See tegi masina mugavaks (sama seadistus krüpteeris ja dekrüpteeris), kuid tekitas nõrkuse: **ükski täht ei krüpteerunud iseendaks**.

Päevavõti määras rootorite valiku ja järjekorra (60 võimalust), algasendid (26³ = 17 576) ja pistikupaneeli ühendused (umbes 1,5 × 10¹⁴). Kokku on see umbes **1,59 × 10²⁰** seadistust – toore jõuga tolle aja tehnikaga võimatu.

Kuidas Enigma siiski murti?

- **Poola matemaatikud** eesotsas Marian Rejewskiga taastasid 1932. aastal rootorite juhtmestiku matemaatiliselt, kasutades ära seda, et iga sõnumi alguses krüpteeriti sõnumivõti kaks korda järjest. 1939. aastal andsid nad oma teadmised üle Prantsuse ja Briti luurele.
- **Bletchley Parkis** ehitasid Alan Turing ja Gordon Welchman elektromehaanilised **pommid** (bombe), mis kontrollisid tuhandeid seadistusi tunnis.
- Pomm vajas **vihjet** (crib): avateksti osa, mis sõnumis tõenäoliselt esines. Saksa sõnumites korduti iga päev samu fraase: *WETTERVORHERSAGE* (ilmateade), *KEINE BESONDEREN EREIGNISSE* (erilisi sündmusi pole), *HEIL HITLER*. Kuna ükski täht ei krüpteerunud iseendaks, sai vihjet šifriteksti peal libistades kohe välistada asendid, kus mõni täht oleks iseendaga kohakuti.
- Operaatorite **kasutusvead** aitasid veelgi: laiskade valitud algasendid (`AAA`, `ABC`), sama sõnum saadetud nii Enigma kui ka nõrgema šifriga.

Õppetund kehtib tänini: krüptograafia murtakse harva otse matemaatikaga. Palju sagedamini murtakse see **disainivea** (ükski täht ei krüpteeru iseendaks), **ennustatava avateksti** või **kasutusvigade** kaudu.

#### Lorenz ja esimesed arvutid

Saksa kõrgem juhtkond kasutas teletaipi šifreerivat **Lorenzi masinat**, mis tegi XOR-i pseudojuhusliku võtmevooga. Kui üks operaator saatis 1941. aastal sama võtmega kaks peaaegu samasugust sõnumit (sama viga nagu ühekordse ploki korduval kasutamisel), taastas Bill Tutte selle põhjal masina ülesehituse ilma seda kunagi nägemata. Lorenzi sõnumite murdmiseks ehitas Tommy Flowers 1944. aastal **Colossuse** – ühe esimestest programmeeritavatest elektroonilistest arvutitest. Arvutiteadus ja krüptoanalüüs on seega algusest peale koos arenenud.

#### Arvutiajastu

**Claude Shannon** pani 1949. aastal krüptograafiale matemaatilise aluse. Ta nimetas kaks omadust, mis igal heal šifril peavad olema:

- **segadus** (confusion) – võtme ja šifriteksti seos peab olema väga keeruline, nii et šifritekstist ei saa võtme kohta midagi järeldada;
- **hajutus** (diffusion) – ühe avateksti biti muutmine peab muutma ligikaudu pooli šifriteksti bitte, nii et avateksti mustrid kaovad.

Caesar ja asendusšiffer ei hajuta üldse (üks täht muudab ühe tähe), seetõttu töötab sagedusanalüüs. AES teeb mõlemat kümme korda järjest (ptk 26).

Avalikud standardid tekkisid 20. sajandi 70ndatel. **DES** (1977) oli esimene avalik plokkšiffer, kuid selle 56-bitine võti murti 1998. aastal eriehitusega masinaga „Deep Crack“ umbes kahe päevaga ja 1999. aastal koostöös internetiga alla ööpäevaga. Whitfield Diffie ja Martin Hellman avaldasid 1976. aastal **avaliku võtme krüptograafia** idee ja Rivest, Shamir ja Adleman 1977. aastal **RSA** (ptk 28). **AES** valiti 2001. aastal avaliku rahvusvahelise konkursi tulemusel: võitis Belgia teadlaste Joan Daemeni ja Vincent Rijmeni algoritm **Rijndael**. 2024. aastal avaldas NIST esimesed **postkvantstandardid** (ptk 30).

### 25.4 Kaasaegse krüptograafia kaart

![krüptograafia kaart · sümmeetriline, räsi, asümmeetriline](../pildid/25-e.png)

Joonis: kaasaegse krüptograafia kolm ehitusplokki ja peatükid, kus neid käsitletakse. Päris süsteemid kasutavad neid alati koos.

| Ehitusplokk | Mida teeb | Võti | Tuntud algoritmid | Peatükk |
| --- | --- | --- | --- | --- |
| Sümmeetriline krüpteerimine | kaitseb konfidentsiaalsust kiiresti ja suurte andmemahtude puhul | sama salajane võti mõlemal poolel | AES, ChaCha20 | 26 |
| Räsifunktsioon | annab andmetele lihtsalt kontrollitava „sõrmejälje“ | võtit pole (HMAC-il on) | SHA-256, SHA-3, Argon2 | 27 |
| Asümmeetriline krüptograafia | võtmevahetus ja digiallkiri võõraste vahel | avalik ja privaatvõti | RSA, ECDH, ECDSA, ML-KEM | 28–30 |

Näiteks HTTPS-ühenduses (ptk 29) leppivad brauser ja server asümmeetrilise krüptograafiaga kokku ühises võtmes, serveri identiteeti kontrollitakse digiallkirjaga ja sertifikaadiga ning andmed liiguvad AES-i või ChaCha20-ga krüpteeritult. Iga krüpteeritud sõnumi terviklust kontrollitakse autentimiskoodiga.

### 25.5 Juhuslikkus: krüptograafia nähtamatu alus

Iga võti peab olema **ettearvamatu**. Kui ründaja suudab võtme ette arvata, pole tähtis, kui tugev on algoritm. Arvuti on aga deterministlik masin: sama sisend annab alati sama väljundi. Kust tuleb siis juhuslikkus?

- **Entroopia allikad** – operatsioonisüsteem kogub ettearvamatuid sündmusi: klahvivajutuste ja hiireliigutuste täpseid ajastusi, katkestuste aegu, võrgupakettide saabumist ning protsessori riistvaralist juhuarvugeneraatorit (Intel `RDRAND`, TPM-kiip).
- **Krüptograafiliselt turvaline pseudojuhuarvude generaator** (CSPRNG) laiendab seda entroopiat pikaks juhuslikuna näivaks jadaks, millest ei saa eelmisi ega järgmisi väärtusi ennustada. Linuxis on selleks `/dev/urandom` ja `getrandom()`, Windowsis `BCryptGenRandom` ning Pythonis moodul `secrets`.
- **Tavaline juhuarvugeneraator** (Pythoni `random`, C `rand()`, JavaScripti `Math.random()`) on mõeldud simulatsioonideks ja mängudeks. Selle sisemise oleku saab väljundi põhjal taastada. Paroolide, võtmete ja seansitunnuste loomiseks ei tohi seda **kunagi** kasutada.

**Näide: Debiani OpenSSL-i viga (2008).** 2006. aastal eemaldas Debiani arendaja OpenSSL-i koodist kaks rida, mille peale hoiatas mälukontrolli tööriist. Need read lisasid juhuslikkuse kogumisse entroopiat. Pärast muudatust sõltus genereeritud võti peaaegu ainult protsessi numbrist (PID), mida oli kuni 32 768 võimalust. Kahe aasta jooksul loodud Debiani ja Ubuntu SSH- ja TLS-võtmeid oli seega iga tüübi ja pikkuse kohta vaid kuni 32 768 – ründaja genereeris need kõik läbi ja leidis õige võtme minutitega. Algoritm (RSA) oli korras, võtme pikkus oli korras, kuid juhuslikkus puudus. Sama klassi vigu on leitud ka hiljem, näiteks Eesti ID-kaardi kiibi võtmegeneraatoris 2017. aastal (ROCA, ptk 29).

### 25.6 Mida krüptograafia ei lahenda

Krüptograafia on tugevaim lüli turvaahelas, kuid ahel on sama tugev kui nõrgim lüli. Krüptograafia ei kaitse:

- **nakatunud seadmes**, kus pahavara loeb andmeid enne krüpteerimist või pärast dekrüpteerimist, salvestab klahvivajutusi või varastab avatud seansi;
- **halva võtmehalduse** korral: võti on samas kaustas mis krüpteeritud fail, parool on skripti sisse kirjutatud, BitLockeri taastevõti on märkmepaberil või privaatvõti on Giti repositooriumis;
- **teostusvigade** korral: Heartbleedi viga (2014) OpenSSL-is lekitas serveri mälu koos privaatvõtmetega, kuigi algoritmid olid korras;
- **kasutusvigade** korral: vale režiim (ECB, ptk 26), korduv nonss, aegunud algoritm (MD5, SHA-1, DES);
- **kõrvalkanalite** eest: ründaja võib võtme taastada arvutusaja, voolutarbimise või elektromagnetkiirguse mõõtmisest;
- **inimese petmisel**: õngitsusleht saab TLS-sertifikaadi samamoodi nagu päris pank, lukk aadressiribal tähendab ainult seda, et ühendus on krüpteeritud, mitte seda, et sait on aus;
- **metaandmeid**: krüpteeritud ühendusest on näha, kes kellega, millal ja kui palju suhtleb.

Seepärast kehtib reegel: **ära kirjuta oma krüptograafiat**. Kasuta testitud teeke (OpenSSL, Windows CNG, libsodium, Pythoni `cryptography`) ja nende vaikeseadeid.

### Näide: Base64 „parool“ seadistusfailis

Veebirakenduse seadistusfailis on rida `db_password=a2Fzc2kxMjM=`. Arendaja arvas, et parool on krüpteeritud. Tegelikult on see Base64:

```bash
echo 'a2Fzc2kxMjM=' | base64 -d     # kassi123
```

Base64 tunneb sageli ära: ainult tähed, numbrid, `+` ja `/`, pikkus jagub neljaga ja lõpus võib olla `=` või `==`. Kodeerimiseks ei ole vaja võtit, seega saab igaüks, kes faili näeb, parooli kätte. Õige lahendus: parool hoitakse saladuste hoidlas (nt Azure Key Vault, Windowsi Credential Manager, keskkonnamuutuja piiratud õigustega), mitte koodis ega seadistusfailis.

### Kokkuvõte

- Krüpteerimine muudab avateksti šifritekstiks. Ilma võtmeta ei saa seda tagasi muuta.
- Kerckhoffsi printsiip: algoritm on avalik, saladus on ainult võti.
- Caesari šifri murrab toore jõu rünne, asendusšifri sagedusanalüüs ja Vigenère’i võtme kordumine (Kasiski meetod).
- Ühekordne plokk on täiuslikult turvaline ainult siis, kui võti on juhuslik, sõnumiga sama pikk ja ühekordne.
- Enigma ja Lorenz murti disainivea, ennustatava avateksti ja kasutusvigade kaudu.
- Hea šiffer tekitab segadust ja hajutust. Võtmed peavad tulema krüptograafiliselt turvalisest juhuarvugeneraatorist.
- Kodeerimine (Base64) ei ole krüpteerimine.

### Kontrollküsimused

> 🧪 **Interaktiivne test:** [kontrolli oma teadmisi](https://valdonolvak.github.io/Operatsioonis-steemi-ja-k-berturbe-alused/interaktiivne/25-test.html). Test koosneb 25 valikvastusega küsimusest. Iga vastuse järel näed selgitust ning lõpus saad valesti vastatud küsimusi korrata.

1. Mis vahe on avatekstil, šifritekstil, algoritmil ja võtmel?
2. Sõnasta Kerckhoffsi printsiip ja too näide, mis juhtub, kui seda ei järgita.
3. Mis vahe on transpositsioonil ja asendusel?
4. Krüpteeri Caesari šifriga (k = 3) sõna `KOOL`. Mitu võtit peab ründaja läbi proovima?
5. Kuidas töötab sagedusanalüüs ja miks ei aita selle vastu suur võtmeruum?
6. Miks ei tööta lihtne sagedusanalüüs Vigenère’i šifri vastu ja kuidas Kasiski meetod selle siiski murrab?
7. Arvuta `01100001 ⊕ 00001111`. Mis juhtub, kui teed tulemusega sama tehte uuesti?
8. Miks on ühekordne plokk teoreetiliselt täiuslik, kuid praktikas harv? Mis juhtub, kui võtit kasutatakse kaks korda?
9. Millised Enigma omadused ja kasutajate harjumused aitasid selle murda?
10. Selgita Shannoni mõisteid segadus ja hajutus.
11. Miks ei tohi paroole ja võtmeid luua Pythoni mooduliga `random`? Mida kasutada selle asemel?
12. Mis vahe on kodeerimisel, krüpteerimisel ja steganograafial?

### Valikküsimused

1. Mida väidab Kerckhoffsi printsiip? a) algoritm peab olema salajane b) võti peab olema lühike c) süsteem peab olema turvaline ka siis, kui kõik peale võtme on avalik d) šifrit ei tohi kunagi muuta
2. Milline meetod murrab lihtsa asendusšifri? a) sagedusanalüüs b) DNS-päring c) räsimine d) digiallkiri
3. Mis on Base64? a) sümmeetriline šiffer b) räsifunktsioon c) asümmeetriline šiffer d) kodeering, mitte krüpteerimine
4. Mitu võimalikku võtit on Caesari šifril 26-tähelises ladina tähestikus? a) 2 b) 25 c) 1000 d) 2¹²⁸

### Praktiline ülesanne

**Ajaloolised šifrid ja XOR WSL-is.** Ava WSL Ubuntu (ptk 20.5) ja loo töökaust: `mkdir -p ~/krypto && cd ~/krypto`.

1. Caesari šiffer käsuga `tr`:

```bash
echo 'TERE MAAILM' | tr 'A-Z' 'D-ZA-C'      # krüpteeri, nihe 3
echo 'WHUH PDDLOP' | tr 'D-ZA-C' 'A-Z'      # dekrüpteeri
echo 'Tere' | tr 'A-Za-z' 'N-ZA-Mn-za-m'    # ROT13
```

2. Toore jõu rünne: salvesta fail `caesar.py` ja proovi kõik 25 võtit:

```python
sifr = "WHUH PDDLOP"
for k in range(1, 26):
    print(k, "".join(chr((ord(c) - 65 - k) % 26 + 65) if c.isalpha() else c for c in sifr))
```

3. Sagedusanalüüs: kopeeri pikem eestikeelne tekst (nt uudisartikkel) faili `tekst.txt` ja loe tähti:

```bash
tr -cd 'a-zA-ZõäöüÕÄÖÜ' < tekst.txt | tr 'A-ZÕÄÖÜ' 'a-zõäöü' | grep -o . | sort | uniq -c | sort -rn | head
```

4. Vigenère’i šiffer: salvesta fail `vigenere.py`:

```python
def vigenere(tekst, voti, suund=1):
    tulemus, i = "", 0
    for c in tekst.upper():
        if "A" <= c <= "Z":
            nihe = ord(voti[i % len(voti)]) - 65
            tulemus += chr((ord(c) - 65 + suund * nihe) % 26 + 65)
            i += 1
        else:
            tulemus += c
    return tulemus

c = vigenere("TULEBHOMMIKUL", "KOOL")
print("Šifritekst:", c)                          # DIZPLVCXWWYFV
print("Avatekst: ", vigenere(c, "KOOL", -1))
```

5. XOR ja ühekordne plokk: salvesta fail `otp.py`:

```python
import secrets

def xor(a, b):
    return bytes(x ^ y for x, y in zip(a, b))

p1 = b"RYNNAK KELL 6"
p2 = b"TAGANE KELL 9"
k = secrets.token_bytes(len(p1))       # krüptograafiliselt juhuslik võti

c1 = xor(p1, k)
print("võti:        ", k.hex())
print("šifritekst 1:", c1.hex())
print("tagasi:      ", xor(c1, k))

# VIGA: sama võti teise sõnumi jaoks
c2 = xor(p2, k)
print("c1 XOR c2:   ", xor(c1, c2).hex())
print("p1 XOR p2:   ", xor(p1, p2).hex())   # sama tulemus – võti kadus
print("p2 taastatud:", xor(xor(c1, c2), p1))  # kui p1 on teada või arvatav
```

6. Kodeerimine ja juhuslikkus:

```bash
echo -n 'kassi123' | base64            # kodeerimine, võtit pole
openssl rand -hex 16                   # juhuslik 128-bitine võti
head -c 16 /dev/urandom | xxd          # sama otse kernelist
```

Vasta: milline võti andis Caesari ülesandes loetava teksti? Millised viis tähte olid sinu tekstis kõige sagedasemad? Miks olid `c1 XOR c2` ja `p1 XOR p2` võrdsed ja mida see ründajale tähendab? Miks ei kaitse Base64 parooli?

### Iseseisvad ülesanded

**1. Vigenère’i murdmine.** Krüpteeri praktilise ülesande programmiga vähemalt 300 tähe pikkune eestikeelne tekst võtmega, mille pikkus on 3–6 tähte, ja anna šifritekst kaaslasele. Kaaslane leiab šifritekstist korduvad kolmetähelised järjendid ja nende vahekaugused ning oletab võtme pikkuse (Kasiski meetod). Seejärel jagab ta šifriteksti selle pikkuse järgi osadeks ja leiab iga osa nihke sagedusanalüüsiga. **Esita:** korduste tabel, vahekaugused ja nende ühistegurid, leitud võti ja selgitus, kus tekkisid raskused.

**2. Salajane algoritm ei kaitse.** Uuri üht juhtumit, kus salajasele algoritmile tuginenud süsteem murti (nt MIFARE Classic Crypto-1, DVD-de CSS, GSM-i A5/1). **Esita:** pool lehekülge: mis oli süsteem, kuidas algoritm avalikuks tuli, mis oli tagajärg ja kuidas see seostub Kerckhoffsi printsiibiga.

**3. Enigma simulaator.** Leia veebist Enigma simulaator (nt Cryptii Enigma). Krüpteeri sama sõna `AAAAA` ja selgita, miks iga A annab erineva tähe. Seejärel kontrolli, et šifritekstis ei esine kunagi sama täht samal kohal kui avatekstis. **Esita:** kuvatõmmis ja selgitus, kuidas see omadus aitas Bletchley Parkis vihjeid paigutada.

### Viited

- Singh, S. (1999). *The Code Book: The Science of Secrecy from Ancient Egypt to Quantum Cryptography*. Fourth Estate.
- Kahn, D. (1996). *The Codebreakers: The Comprehensive History of Secret Communication*. Scribner.
- Shannon, C. E. (1949). Communication Theory of Secrecy Systems. *Bell System Technical Journal*, 28(4).
- Kerckhoffs, A. (1883). La cryptographie militaire. *Journal des sciences militaires*.
- Katz, J., Lindell, Y. (2020). *Introduction to Modern Cryptography*, 3. tr. CRC Press.
- Debian. [DSA-1571-1 openssl: predictable random number generator](https://www.debian.org/security/2008/dsa-1571) (2008).
- Tartu Ülikool. [Infoturve: andmete kaitsmine krüpteerimise abil](https://courses.cs.ut.ee/2020/infsec/spring/Main/Loeng-AndmeteKrypteerimine).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 24. UUS: Küberturbe põhimõisted ja ründevektorid](24-kuberturbe-pohimoisted.md) · [Sisukord](../README.md) · [26. UUS: Sümmeetriline krüpteerimine →](26-summeetriline-kruptograafia.md)
