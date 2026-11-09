[← 29. UUS: Digiallkiri, sertifikaadid, PKI ja TLS](29-digiallkiri-pki-tls.md) · [Sisukord](../README.md) · [31. UUS: Küberturbe õigus- ja juhtimisraamistik →](31-oigus-ja-juhtimisraamistik.md)

## 30. UUS: Postkvantkrüptograafia ja krüptograafia tulevik

**Kvantarvuti** kasutab kubitte, mis võivad olla korraga mitmes olekus. Mõne kindla matemaatilise ülesande lahendab see eksponentsiaalselt kiiremini kui tavaarvuti. Kahjuks kuuluvad nende hulka just need ülesanded, millel põhineb tänane avaliku võtme krüptograafia. **Postkvantkrüptograafia** (PQC, post-quantum cryptography) on uued algoritmid, mis töötavad tavaarvutites, kuid on kindlad ka kvantarvuti vastu.

Postkvantkrüptograafia ei ole sama mis **kvantkrüptograafia**. Kvantvõtmejaotus (QKD) kasutab võtme edastamiseks footoneid ja vajab eraldi fiiberliine või satelliite. Postkvantkrüptograafia on tavaline tarkvara, mis töötab olemasolevates arvutites ja võrkudes.

**Pärast peatüki läbimist õppija:**

- selgitab, miks kvantarvuti ohustab avaliku võtme krüptograafiat, kuid mitte oluliselt AES-i ja SHA-2-t;
- rakendab Mosca võrratust oma organisatsiooni andmetele;
- nimetab NIST-i postkvantstandardid ja nende otstarbe ning kirjeldab võreprobleemi põhimõtet;
- selgitab hübriidse võtmevahetuse ja krüptopaindlikkuse vajalikkust;
- kasutab OpenSSL-is ML-KEM-i ja ML-DSA-d ning koostab krüpto-inventuuri.

### 30.1 Kvantarvuti lühidalt

Tavaarvuti bitt on kas 0 või 1. **Kubit** võib olla 0 ja 1 **superpositsioonis**: mõõtmisel saame kas 0 või 1 teatud tõenäosusega. n kubitti kirjeldavad korraga 2ⁿ olekut. See ei tähenda, et kvantarvuti proovib lihtsalt kõik vastused korraga läbi – mõõtmisel saame ainult ühe tulemuse. Kvantalgoritm peab olema ehitatud nii, et valed vastused kustutavad üksteist (**interferents**) ja õige vastus jääb alles suure tõenäosusega. Seetõttu kiirendab kvantarvuti ainult mõnda kindlat tüüpi ülesannet.

Tänased kvantarvutid on väikesed ja mürarikkad: kubitid kaotavad oma oleku sekundi murdosaga. Krüptograafiliselt ohtliku kvantarvuti jaoks on vaja **veaparandusega loogilisi kubitte**, millest igaüks koosneb sadadest või tuhandetest füüsilistest kubittidest. Google’i teadlane Craig Gidney hindas 2025. aastal, et 2048-bitise RSA-võtme saaks murda alla miljoni mürarikka kubitiga umbes nädalaga. Tänased seadmed on sellest veel kaugel, kuid hinnangud on aasta-aastalt väiksemaks jäänud. Millal selline masin valmib, ei tea keegi: eksperdid pakuvad enamasti 10–20 aastat, mõned vähem.

### 30.2 Miks tegeleda sellega juba täna

![salvesta nüüd, dekrüpteeri hiljem · Mosca võrratus](../pildid/30-a.png)

Joonis: **salvesta nüüd, dekrüpteeri hiljem** (harvest now, decrypt later). Ründaja võib täna salvestada krüpteeritud liiklust ja dekrüpteerida selle aastate pärast, kui kvantarvuti on olemas.

Michele Mosca võrratus aitab otsustada:

- **x** – mitu aastat peavad andmed salajased olema;
- **y** – mitu aastat kulub organisatsioonil üleminekuks;
- **z** – mitu aastat on jäänud krüptograafiliselt ohtliku kvantarvuti tekkimiseni.

Kui **x + y > z**, on andmed juba täna ohus. Näide: haigla patsiendiandmed peavad olema salajased vähemalt 25 aastat (x = 25), IT-süsteemide üleminek võtab 5 aastat (y = 5). Isegi kui kvantarvuti tuleb alles 20 aasta pärast (z = 20), on 25 + 5 = 30 > 20: täna salvestatud liiklus võib kvantarvuti valmimise ajal veel tundlik olla. Seevastu e-poe tellimuse kinnitus, mis peab salajane olema paar nädalat, ei ole ohus.

Salvesta-nüüd-rünne ohustab ainult **konfidentsiaalsust** (võtmevahetust ja krüpteerimist). Allkirja ei saa tagantjärele võltsida, kui see on juba kontrollitud ja sertifikaat aegunud. Seetõttu alustatakse üleminekut võtmevahetusest.

### 30.3 Mida kvantarvuti murrab

![kvantalgoritmid · Shor ja Grover](../pildid/30-b.png)

Joonis: Shori algoritm murrab kõik tänapäeval levinud avaliku võtme algoritmid. Pikem RSA-võti ei aita. Groveri algoritm kiirendab ainult toore jõu otsingut, seega piisab sümmeetrilises krüptograafias ja räsides pikemast võtmest või räsist ([SecurityWeek](https://www.securityweek.com/nist-announces-hqc-as-fifth-standardized-post-quantum-algorithm/)).

**Shori algoritm** (1994) taandab tegurdamise ja diskreetse logaritmi **perioodi leidmisele**: kui vaadata jada a¹, a², a³, … mod n, hakkab see mingi aja pärast korduma. Perioodi pikkusest saab arvutada n-i tegurid. Tavaarvuti peab perioodi otsima samm-sammult, kvantarvuti leiab selle kvant-Fourier’ teisenduse abil polunüomiaalses ajas. Sama põhimõte murrab ka elliptkõverad.

**Groveri algoritm** (1996) otsib sorteerimata hulgast õiget vastust umbes √N sammuga N asemel. 2¹²⁸ võtme otsimiseks kuluks 2⁶⁴ kvantsammu – teoreetiliselt vähem, kuid kvantsammud on aeglased ja neid ei saa hästi paralleelselt teha. Seetõttu loetakse AES-128 praktikas endiselt piisavaks ja AES-256 kindlasti turvaliseks.

| Algoritm | Kasutus | Kvantarvuti mõju | Mida teha |
| --- | --- | --- | --- |
| RSA, DH | võtmevahetus, allkiri | murtud (Shor) | asenda ML-KEM-i ja ML-DSA-ga |
| ECDH, ECDSA, Ed25519 | võtmevahetus, allkiri | murtud (Shor) | asenda ML-KEM-i ja ML-DSA-ga |
| AES-128 | sümmeetriline krüpteerimine | nõrgeneb teoreetiliselt (Grover) | pikaajaliselt AES-256 |
| AES-256, ChaCha20 | sümmeetriline krüpteerimine | turvaline | muutust pole vaja |
| SHA-256, SHA-3 | räsi | turvaline | muutust pole vaja |

### 30.4 NIST-i standardid

USA standardiinstituut NIST korraldas 2016–2024 avaliku konkursi, millest kasvasid välja esimesed postkvantstandardid. Need avaldati 13. augustil 2024.

![NIST postkvantstandardid · KEM ja allkirjad](../pildid/30-c.png)

Joonis: postkvantstandardite ülevaade. HQC valiti 2025. aasta märtsis ML-KEM-i varuvariandiks ja selle standardi kavand on oodata 2026. aastal. FN-DSA (FIPS 206) on kavandi staadiumis ([SecurityWeek](https://www.securityweek.com/nist-announces-hqc-as-fifth-standardized-post-quantum-algorithm/)).

| Standard | Nimi varem | Põhineb | Otstarve |
| --- | --- | --- | --- |
| FIPS 203: ML-KEM | Kyber | võred | võtmekapseldus, peamine valik |
| FIPS 204: ML-DSA | Dilithium | võred | allkiri, peamine valik |
| FIPS 205: SLH-DSA | SPHINCS+ | räsid | allkiri, varuvariant; suured allkirjad, kuid tugineb ainult räsi turvalisusele |
| FIPS 206: FN-DSA (kavand) | Falcon | võred | allkiri, väiksemad allkirjad |
| HQC (kavand) | HQC | veaparanduskoodid | võtmekapseldus, ML-KEM-i varuvariant |

**Võreprobleemi intuitsioon.** ML-KEM ja ML-DSA tuginevad probleemile **LWE** (learning with errors, „õppimine vigadega“). Kujutle võrrandisüsteemi, kus salajane vektor s on tundmatu ja avalikult on teada palju võrrandeid kujul b = a · s + e (mod q). Kui viga e oleks null, lahendaks Gaussi meetodiga iga kooliõpilane süsteemi minutitega. Iga võrrandi juurde on aga lisatud väike juhuslik viga e. Selle tõttu ei klapi ükski võrrand täpselt ja s leidmine muutub sadade mõõtmete korral väga raskeks – ka kvantarvutile ei ole teada kiiret meetodit. Omanik, kes teab s-i, saab vea eemaldada, sest see on väike.

**KEM ei ole Diffie–Hellman.** Võtmekapseldusmehhanism (KEM) töötab nii: Jaan avaldab ML-KEM avaliku võtme. Mari genereerib juhusliku saladuse, „kapseldab“ selle Jaani avaliku võtmega ja saadab kapsli. Jaan avab kapsli privaatvõtmega. Mõlemal on nüüd sama saladus, millest tuletatakse seansivõti. Tulemus on sama mis DH-l, kuid sõnumid on asümmeetrilised.

Postkvantalgoritmide võtmed ja allkirjad on suuremad:

| Algoritm | Avalik võti | Šifritekst või allkiri |
| --- | --- | --- |
| X25519 (klassikaline) | 32 baiti | 32 baiti |
| ML-KEM-768 | 1184 baiti | 1088 baiti |
| Ed25519 (klassikaline) | 32 baiti | 64 baiti |
| ML-DSA-65 | 1952 baiti | 3309 baiti |

Suuremad sõnumid tähendavad veidi pikemat TLS-käepigistust: ClientHello ei mahu enam ühte võrgupaketti ja mõni vana tulemüür või koormusejaotur, mis eeldab ühepaketilist ClientHellot, katkestab ühenduse. Praktikas on ML-KEM arvutuslikult isegi kiirem kui klassikaline võtmevahetus.

### 30.5 Hübriidne üleminek

![hübriidne võtmevahetus · X25519 + ML-KEM-768](../pildid/30-d.png)

Joonis: üleminekuperioodil kasutatakse **hübriidvõtmevahetust**: klassikaline ja postkvantalgoritm töötavad korraga ning seansivõti tuletatakse mõlemast. See kaitseb kahe riski eest korraga: kvantarvuti ja uues algoritmis leitava vea eest.

Miks mitte minna kohe ainult postkvantalgoritmidele? Uued algoritmid on alles mõned aastad laialdaselt analüüsitud. NIST-i konkursi ajal murti üks finalistidest, **SIKE**, 2022. aastal tavalise sülearvutiga umbes tunniga. Kui sellise algoritmi oleks kasutusele võtnud ainsana, oleks kogu liiklus olnud avatud. Hübriidis peaks ründaja murdma mõlemad algoritmid.

Seis 2026. aasta lõpus:

- **Brauserid** (Chrome, Edge, Firefox, Safari) ja suured sisuedastusvõrgud kasutavad TLS-is vaikimisi X25519MLKEM768.
- **OpenSSL 3.5** (aprill 2025) toetab ML-KEM-i, ML-DSA-d ja SLH-DSA-d. Ubuntu 26.04 LTS-is on OpenSSH 10.2 ja OpenSSL 3.5, mis kasutavad SSH-s ja TLS-is vaikimisi hübriidvõtmevahetust ([Ubuntu Discourse](https://discourse.ubuntu.com/t/post-quantum-support-in-the-upcoming-26-04-lts/76840)).
- **Euroopa Liidu koordineeritud tegevuskava** (juuni 2025) näeb ette, et liikmesriigid alustavad üleminekut ja koostavad riikliku kava 2026. aasta lõpuks, kõrge riskiga süsteemid viiakse postkvantkrüptograafiale 2030. aastaks ja ülejäänud võimalikult palju 2035. aastaks ([Euroopa Komisjon](https://digital-strategy.ec.europa.eu/en/library/coordinated-implementation-roadmap-transition-post-quantum-cryptography)).
- **Allkirjade** üleminek on aeglasem kui võtmevahetuse oma, sest sertifikaadid ja kiipkaardid (sh ID-kaart) vajavad uusi standardeid, riistvara ja usaldushoidlaid. Salvesta-nüüd-rünne allkirju ei ohusta: allkiri tuleb murda enne selle aegumist.

### 30.6 Krüptopaindlikkus ja üleminekuplaan

**Krüptopaindlikkus** (crypto-agility) tähendab, et süsteemis saab algoritmi vahetada ilma kogu süsteemi ümber ehitamata: algoritm on seadistus, mitte koodi sisse kirjutatud konstant. Ajalugu näitab, et iga algoritm vahetatakse kunagi välja: DES → AES, MD5 → SHA-1 → SHA-256, RSA-1024 → RSA-2048 → ECC. Iga vahetus on võtnud organisatsioonidelt aastaid, sest algoritme oli kõvasti kodeeritud rakendustesse, seadmetesse ja protokollidesse.

Organisatsiooni üleminek algab krüpto-inventuurist:

1. **Inventuur**: kus ja millist krüptograafiat kasutatakse (TLS-serverid, VPN, SSH, sertifikaadid, rakendused, kiipkaardid, varukoopiad)? Seda nimekirja nimetatakse ka CBOM-iks (cryptographic bill of materials).
2. **Riskihinnang**: millised andmed peavad salajased olema kaua (Mosca võrratus)?
3. **Tarnijad**: millal toetavad tulemüür, VPN-seade, PKI ja rakendused PQC-d? Lisa nõue uutesse hankelepingutesse.
4. **Pilootprojekt**: hübriidvõtmevahetus TLS-is ja SSH-s.
5. **Üleminek** riskide järjekorras ja vanade algoritmide väljalülitamine.

Administraatori praktilised sammud täna: hoia OpenSSL, OpenSSH, brauserid ja VPN-tarkvara uuendatud (need toovad hübriidvõtmevahetuse ise), kasuta AES-256 pikaajaliselt säilitatavate andmete krüpteerimisel, ära kirjuta koodi kindlaid algoritme, vaid kasuta teekide vaikeseadeid.

### Näide: kas meie VPN on postkvantkindel?

Omavalitsuse IT-juht kontrollib, kas täna salvestatud kaugtööliiklus võiks 15 aasta pärast lekkida. VPN kasutab IKEv2-d ECDH-võtmevahetusega, seega oleks see kvantarvutiga murtav. Liikluses on isikuandmeid, mille kaitse peab kestma aastakümneid, seega x + y > z on võimalik. IT-juht kontrollib tulemüüri tootja teekaarti ja leiab, et uus tarkvaraversioon toetab IKEv2 lisavõtmevahetust ML-KEM-iga (RFC 9370). Ta lisab selle krüpto-inventuuri tegevuskavasse, katsetab seda testkeskkonnas ja seab tootmise üleminekule tähtaja. Siseveebi TLS-i üleminek on lihtsam: piisab veebiserveri OpenSSL-i värskendamisest.

### Kokkuvõte

- Kvantarvuti murrab RSA, DH ja ECC (Shori algoritm), AES-i ja SHA-2 puhul piisab pikemast võtmest (Groveri algoritm).
- Salvesta-nüüd-rünne teeb pikaajaliselt salajased andmed ohtlikuks juba täna. Mosca võrratus x + y > z aitab otsustada, millega alustada.
- NIST-i standardid: ML-KEM (võtmekapseldus), ML-DSA ja SLH-DSA (allkiri); varuvariandid HQC ja FN-DSA. ML-KEM ja ML-DSA põhinevad võreprobleemil LWE.
- Üleminek on hübriidne ja algab krüpto-inventuurist; EL-i tähtajad on 2026, 2030 ja 2035.

### Kontrollküsimused

1. Mis vahe on postkvantkrüptograafial ja kvantkrüptograafial?
2. Miks ei proovi kvantarvuti lihtsalt kõiki vastuseid korraga läbi?
3. Mis on salvesta-nüüd-dekrüpteeri-hiljem rünne ja keda see ohustab? Miks ei ohusta see allkirju?
4. Selgita Mosca võrratust oma näite abil.
5. Mille poolest erinevad Shori ja Groveri algoritmi mõjud krüptograafiale?
6. Milleks on ML-KEM ja milleks ML-DSA? Mis vahe on KEM-il ja Diffie–Hellmanil?
7. Selgita LWE-probleemi põhimõtet. Miks muudab väike viga võrrandisüsteemi lahendamise raskeks?
8. Miks on NIST valinud ka teistsugusel matemaatikal põhinevad varualgoritmid?
9. Miks kasutatakse üleminekuperioodil hübriidvõtmevahetust? Mis juhtus SIKE-ga?
10. Mis on krüptopaindlikkus ja millest algab organisatsiooni üleminek?

### Valikküsimused

1. Milline algoritm on kvantarvuti suhtes haavatav? a) AES-256 b) SHA-512 c) RSA-4096 d) ML-KEM-768
2. Mis on ML-KEM? a) postkvant-võtmevahetusmehhanism (FIPS 203) b) uus räsifunktsioon c) kvantarvuti tootja d) Windowsi failisüsteem
3. Miks loetakse AES-256 kvantajastul turvaliseks? a) kvantarvuti ei oska XOR-i b) Groveri algoritm vähendab turvataseme umbes poole võrra, jääb \~128 bitti c) AES põhineb võredel d) AES-256 on salajane algoritm
4. Mida tagab hübriidne võtmevahetus X25519MLKEM768? a) ainult kiiruse b) andmete pakkimise c) digiallkirja d) seansivõti on kaitstud, kui vähemalt üks kahest algoritmist peab vastu

### Praktiline ülesanne

**Postkvantkrüptograafia WSL-is.** Vajad OpenSSL 3.5 või uuemat: Ubuntu 26.04 (`wsl --install -d Ubuntu-26.04`, kui see on loendis `wsl --list --online`) või Kali Linux. Kontrolli: `openssl version`.

1. Vaata saadaval algoritme:

```bash
openssl list -kem-algorithms | grep -i ml-kem
openssl list -signature-algorithms | grep -i -E 'ml-dsa|slh-dsa'
ssh -Q kex | grep -i mlkem
```

2. ML-KEM: kapselda saladus avaliku võtmega ja ava see privaatvõtmega:

```bash
openssl genpkey -algorithm ML-KEM-768 -out kem.key
openssl pkey -in kem.key -pubout -out kem.pub
openssl pkeyutl -encap -pubin -inkey kem.pub -out kapsel.bin -secret saladus1.bin
openssl pkeyutl -decap -inkey kem.key -in kapsel.bin -secret saladus2.bin
cmp saladus1.bin saladus2.bin && echo 'Sama saladus!'
ls -l kem.pub kapsel.bin
```

3. ML-DSA allkiri ja võrdlus Ed25519-ga:

```bash
openssl genpkey -algorithm ML-DSA-65 -out dsa.key && openssl pkey -in dsa.key -pubout -out dsa.pub
echo 'Otsus nr 12' > otsus.txt
openssl pkeyutl -sign -rawin -inkey dsa.key -in otsus.txt -out otsus.sig
openssl pkeyutl -verify -rawin -pubin -inkey dsa.pub -in otsus.txt -sigfile otsus.sig
openssl genpkey -algorithm ED25519 -out ed.key && openssl pkeyutl -sign -rawin -inkey ed.key -in otsus.txt -out ed.sig
ls -l otsus.sig ed.sig
```

4. Kontrolli päris ühendust: `curl -sv https://www.google.com -o /dev/null 2>&1 | grep -i 'SSL connection'` ja brauseris leht [pq.cloudflareresearch.com](https://pq.cloudflareresearch.com/).

Vasta: kui suured on ML-KEM-768 avalik võti ja kapsel võrreldes X25519-ga (ptk 28 praktiline ülesanne)? Mitu korda suurem on ML-DSA allkiri kui Ed25519 oma? Kas sinu brauser kasutab postkvantvõtmevahetust?

### Iseseisvad ülesanded

**1. Krüpto-inventuur.** Koosta oma õppelabori või töökoha 10 süsteemi krüpto-inventuur: süsteem, protokoll, võtmevahetus, allkirjaalgoritm, sertifikaadi väljastaja, andmete salajasuse nõue aastates ja tootja PQC-tugi. **Esita:** tabel ja kolme esimese ülemineku järjekord põhjendusega (Mosca võrratus).

**2. Postkvant-SSH.** Seadista WSL-is või VM-is OpenSSH-server nii, et see lubab ainult postkvantvõtmevahetust (`KexAlgorithms mlkem768x25519-sha256`). Ühendu kliendiga, kontrolli `ssh -v` väljundist valitud algoritmi ja proovi ühendust vana kliendiga. **Esita:** seadistusfail, `ssh -v` väljundi asjakohane rida ja selgitus, mis juhtus vana kliendiga.

### Viited

- NIST. [FIPS 203: ML-KEM](https://csrc.nist.gov/pubs/fips/203/final); [FIPS 204: ML-DSA](https://csrc.nist.gov/pubs/fips/204/final); [FIPS 205: SLH-DSA](https://csrc.nist.gov/pubs/fips/205/final) (2024).
- SecurityWeek. [NIST Announces HQC as Fifth Standardized Post Quantum Algorithm](https://www.securityweek.com/nist-announces-hqc-as-fifth-standardized-post-quantum-algorithm/) (2025).
- Euroopa Komisjon. [A Coordinated Implementation Roadmap for the Transition to Post-Quantum Cryptography](https://digital-strategy.ec.europa.eu/en/library/coordinated-implementation-roadmap-transition-post-quantum-cryptography) (2025).
- Ubuntu. [Post Quantum Support in the upcoming 26.04 LTS](https://discourse.ubuntu.com/t/post-quantum-support-in-the-upcoming-26-04-lts/76840).
- Mosca, M. (2018). Cybersecurity in an Era with Quantum Computers: Will We Be Ready? *IEEE Security & Privacy*, 16(5).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 29. UUS: Digiallkiri, sertifikaadid, PKI ja TLS](29-digiallkiri-pki-tls.md) · [Sisukord](../README.md) · [31. UUS: Küberturbe õigus- ja juhtimisraamistik →](31-oigus-ja-juhtimisraamistik.md)
