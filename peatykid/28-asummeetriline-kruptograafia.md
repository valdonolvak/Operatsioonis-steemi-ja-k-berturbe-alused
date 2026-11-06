[← 27. UUS: Räsimine, sool ja paroolide hoidmine](27-rasimine-ja-paroolid.md) · [Sisukord](../README.md) · [29. UUS: Digiallkiri, sertifikaadid, PKI ja TLS →](29-digiallkiri-pki-tls.md)

## 28. UUS: Asümmeetriline krüptograafia ja Diffie–Hellmani võtmevahetus

Asümmeetrilises ehk **avaliku võtme krüptograafias** on igal osapoolel **võtmepaar**: avalik võti, mida võib kõigile jagada, ja privaatvõti, mida teab ainult omanik. See lahendab sümmeetrilise krüptograafia võtmejagamise probleemi (ptk 26.1) ja võimaldab digiallkirju (ptk 29).

### 28.1 Võtmepaar

![võtmepaar · avalik võti lukustab, privaatvõti avab](../pildid/28-a.png)

Joonis: Mari krüpteerib sõnumi Jaani avaliku võtmega. Avada saab selle ainult Jaani privaatvõti. Avalikku võtit võib avaldada veebilehel või e-kirja allkirjas, sest sellest ei saa privaatvõtit tuletada.

Asümmeetrilised algoritmid tuginevad matemaatilistele „lõksuuksefunktsioonidele“: ühes suunas on arvutamine lihtne, vastupidi aga praktiliselt võimatu, kui ei tea salajast lisateavet.

| Algoritm | Raske ülesanne | Kasutus |
| --- | --- | --- |
| RSA | suure arvu tegurdamine algarvudeks | krüpteerimine, allkiri, vanemad sertifikaadid |
| Diffie–Hellman (DH) | diskreetne logaritm | võtmevahetus |
| ECC: ECDH, ECDSA, X25519, Ed25519 | diskreetne logaritm elliptkõveral | võtmevahetus, allkiri; ID-kaart, TLS, SSH |
| ML-KEM, ML-DSA | võreprobleemid (lattice) | postkvantkrüptograafia (ptk 30) |

### 28.2 Diffie–Hellmani võtmevahetus

Whitfield Diffie ja Martin Hellman avaldasid 1976. aastal meetodi, mille abil kaks osapoolt saavad avalikus võrgus kokku leppida ühises saladuses, ilma et see saladus kunagi võrgus liiguks ([Wikipedia: Diffie–Hellmani võtmevahetus](https://et.wikipedia.org/wiki/Diffie-Hellmani_v%C3%B5tmevahetus)).

![Diffie–Hellman · värvide segamine ja arvud](../pildid/28-b.png)

Joonis: värvide segamise analüüs ja sama arvutus väikeste arvudega. Mõlemad segavad avaliku ühise värvi oma salajase värviga ja vahetavad segud. Seejärel lisab kumbki saadud segule oma salajase värvi. Tulemus on mõlemal sama, sest segamise järjekord ei loe: (gᵃ)ᵇ = (gᵇ)ᵃ mod p. Pealtkuulaja näeb ainult segusid ja ühist värvi ning ei suuda neist salajasi värve eraldada.

Kaks olulist täpsustust:

- **Diffie–Hellman üksi ei autendi.** Kui Mari ei tea, et sai segu just Jaanilt, võib ründaja teha mõlemaga eraldi võtmevahetuse ja istuda nende vahel (MITM, ptk 24.5). Seepärast allkirjastab TLS-server oma võtmevahetuse sertifikaadiga ja SSH-klient kontrollib serveri võtme sõrmejälge.
- **Ajutised võtmed (ephemeral DH, ECDHE)** luuakse iga ühenduse jaoks uuesti ja kustutatakse pärast seda. See annab **edasise salastatuse** (forward secrecy): kui serveri privaatvõti hiljem lekib, ei saa varem salvestatud liiklust dekrüpteerida. TLS 1.3 kasutab ainult selliseid võtmevahetusi.

### 28.3 RSA

![RSA · väikeste arvudega näide](../pildid/28-c.png)

Joonis: RSA põhimõte nii väikeste arvudega, et neid saab kalkulaatoriga kontrollida. Avalik võti on arvude paar (n, e), privaatvõti (n, d). Päris RSA-s on n vähemalt 2048, soovitatavalt 3072 bitti pikk, mis teeb kuni 925 kümnendkohta.

Päris RSA-s ei krüpteerita kunagi sõnumit otse („textbook RSA“): enne lisatakse juhuslik täidis (OAEP krüpteerimisel, PSS allkirjal). Vastasel juhul annab sama sõnum alati sama šifriteksti.

### 28.4 Elliptkõverad ja võtmepikkused

**Elliptkõverkrüptograafia** (ECC) teeb sama töö palju lühemate võtmetega, seetõttu on see kiirem ja sobib kiipkaartidele ning telefonidele. Eesti ID-kaart kasutab alates 2018. aastast elliptkõvervõtmeid (P-384), sest varasemate kaartide RSA-võtmete genereerimises leiti 2017. aastal viga (ROCA), mille tõttu sai avalikust võtmest privaatvõtme arvutada. Ligi 750 000 kaarti tuli uuendada kaugteel.

| Turvatase | Sümmeetriline | RSA, DH | ECC |
| --- | --- | --- | --- |
| 112 bitti (kuni 2030) | 3DES | 2048 bitti | 224 bitti |
| 128 bitti | AES-128 | 3072 bitti | 256 bitti (P-256, X25519) |
| 192 bitti | AES-192 | 7680 bitti | 384 bitti (P-384) |
| 256 bitti | AES-256 | 15 360 bitti | 521 bitti (P-521) |

Allikas: [NIST SP 800-57](https://csrc.nist.gov/pubs/sp/800/57/pt1/r5/final). RSA ja ECC on mõlemad kvantarvutite suhtes haavatavad (ptk 30).

### 28.5 Hübriidkrüpteerimine

![hübriidkrüpteerimine · võtmevahetus, seansivõti, andmed](../pildid/28-d.png)

Joonis: asümmeetriline krüptograafia on sadu kuni tuhandeid kordi aeglasem kui AES, seetõttu ei krüpteerita sellega andmeid. Sellega vahetatakse või krüpteeritakse ainult juhuslik sümmeetriline seansivõti ja andmed krüpteeritakse AES-iga. Nii töötavad praktiliselt kõik kaasaegsed protokollid.

### Näide: SSH-võtmega serverisse

Administraator logib Linuxi serverisse ilma paroolita. Ta loob oma arvutis võtmepaari `ssh-keygen -t ed25519` ja kopeerib avaliku võtme serveri faili `~/.ssh/authorized_keys`. Ühendusel juhtub kolm asümmeetrilist asja:

1. **ECDH või ML-KEM võtmevahetus** loob seansivõtme (Ubuntu 26.04 vaikimisi hübriid `mlkem768x25519-sha256`);
2. **server allkirjastab** võtmevahetuse oma hostivõtmega ja klient võrdleb serveri võtme sõrmejälge failiga `known_hosts` – see takistab MITM-i;
3. **klient tõestab** oma privaatvõtmega allkirja abil, et ta on administraator. Privaatvõti ei lahku kunagi tema arvutist.

Pärast seda krüpteeritakse kogu liiklus sümmeetriliselt (AES-GCM või ChaCha20-Poly1305). Paroolitõestuse võib serveris seejärel välja lülitada, mis peatab paroolide äraarvamise ründed.

### Kokkuvõte

- Avaliku võtmega krüpteeritakse ja kontrollitakse allkirja, privaatvõtmega dekrüpteeritakse ja allkirjastatakse.
- Diffie–Hellman loob avalikus võrgus ühise saladuse, kuid vajab autentimist MITM-i vastu.
- RSA turvalisus põhineb tegurdamise raskusel, ECC oma diskreetse logaritmi raskusel elliptkõveral; ECC võtmed on palju lühemad.
- Praktikas on krüpteerimine hübriidne: asümmeetriline seansivõti, sümmeetrilised andmed.

### Kontrollküsimused

1. Millise võtmega krüpteerid sõnumi sõbrale ja millisega sõber selle avab?
2. Selgita Diffie–Hellmani võtmevahetust värvide abil.
3. Miks on Diffie–Hellman ilma autentimiseta haavatav MITM-i suhtes?
4. Mida tähendab edasine salastatus ja miks on see oluline?
5. Millele põhineb RSA turvalisus?
6. Miks eelistatakse kiipkaartidel ja telefonides ECC-d?
7. Mis on hübriidkrüpteerimine ja miks seda kasutatakse?

### Valikküsimused

1. Millise võtmega krüpteeritakse sõnum, mida ainult Jaan peab lugema saama? a) Jaani avaliku võtmega b) Jaani privaatvõtmega c) saatja privaatvõtmega d) ühise AES-võtmega, mis saadetakse e-postiga
2. Mis on Diffie–Hellmani võtmevahetuse tulemus? a) mõlemad saavad teineteise privaatvõtme b) avalik võti krüpteeritakse c) mõlemad arvutavad sama jagatud saladuse, ilma et see võrgus liiguks d) parool salvestatakse serverisse
3. Millele põhineb RSA turvalisus? a) räsi kokkupõrgetele b) XOR-tehtele c) elliptkõveratele d) suurte arvude tegurdamise raskusele
4. Miks kasutatakse praktikas hübriidkrüpteerimist? a) asümmeetriline krüptograafia on kiirem b) asümmeetriline on aeglane, seepärast vahetatakse sellega ainult sümmeetriline seansivõti c) sümmeetriline ei vaja võtit d) see on seadusega kohustuslik

### Praktiline ülesanne

**Võtmepaarid WSL-is.** Ava WSL Ubuntu: `sudo apt install -y openssl openssh-client gnupg python3`.

1. Diffie–Hellman väikeste arvudega Pythonis (kontrolli joonise arvutust ja proovi oma arvudega):

```python
p, g = 23, 5
a, b = 6, 15                       # salajased
A, B = pow(g, a, p), pow(g, b, p)  # avalikud
print("A =", A, "B =", B)
print("Mari:", pow(B, a, p), "Jaan:", pow(A, b, p))
```

2. Päris X25519 võtmevahetus OpenSSL-iga:

```bash
openssl genpkey -algorithm X25519 -out mari.key && openssl pkey -in mari.key -pubout -out mari.pub
openssl genpkey -algorithm X25519 -out jaan.key && openssl pkey -in jaan.key -pubout -out jaan.pub
openssl pkeyutl -derive -inkey mari.key -peerkey jaan.pub | xxd -p
openssl pkeyutl -derive -inkey jaan.key -peerkey mari.pub | xxd -p   # sama tulemus!
```

3. RSA krüpteerimine ja dekrüpteerimine:

```bash
openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:3072 -out rsa.key
openssl pkey -in rsa.key -pubout -out rsa.pub
echo 'Salajane kohtumine kell 15' > s.txt
openssl pkeyutl -encrypt -pubin -inkey rsa.pub -pkeyopt rsa_padding_mode:oaep -in s.txt -out s.enc
openssl pkeyutl -decrypt -inkey rsa.key -pkeyopt rsa_padding_mode:oaep -in s.enc
ls -l rsa.pub mari.pub            # võrdle võtmete suurust
```

4. SSH-võti: `ssh-keygen -t ed25519 -C "oma.nimi@kool.ee"` ja vaata `cat ~/.ssh/id_ed25519.pub`.

Vasta: kas Mari ja Jaan said sama jagatud saladuse? Kui suur on RSA avalik võti võrreldes X25519 omaga? Miks võib `id_ed25519.pub` faili kellelegi saata, aga `id_ed25519` mitte?

### Iseseisvad ülesanded

**1. Krüpteeritud kiri kaaslasele.** Loo GPG-võtmepaar (`gpg --full-generate-key`, vali ECC), ekspordi avalik võti (`gpg --armor --export`) ja vaheta seda kaaslasega. Krüpteeri talle fail (`gpg --encrypt --recipient`) ja dekrüpteeri tema saadetud fail. Kontrollige teineteise võtme sõrmejälge suuliselt. **Esita:** kasutatud käsud, võtme sõrmejälg ja selgitus, miks sõrmejälje kontroll on vajalik.

**2. RSA käsitsi.** Vali kaks väikest algarvu (nt 11 ja 17), arvuta n, φ, vali e ja leia d (Pythonis `pow(e, -1, phi)`). Krüpteeri ja dekrüpteeri arv 42. Seejärel näita, kuidas pealtkuulaja, kes teab ainult n-i ja e-d, leiab d, kui n on väike. **Esita:** arvutused ja selgitus, miks see päris RSA puhul ei õnnestu.

### Viited

- Diffie, W., Hellman, M. (1976). New Directions in Cryptography. *IEEE Transactions on Information Theory*, 22(6).
- Rivest, R., Shamir, A., Adleman, L. (1978). A Method for Obtaining Digital Signatures and Public-Key Cryptosystems. *Communications of the ACM*, 21(2).
- [Diffie–Hellmani võtmevahetus](https://et.wikipedia.org/wiki/Diffie-Hellmani_v%C3%B5tmevahetus). Vikipeedia.
- Tartu Ülikool. [Infoturve: avaliku võtme krüptograafia ja PKI](https://courses.cs.ut.ee/2022/infsec/spring/Main/PKI).
- NIST. [SP 800-57 Part 1 Rev. 5: Recommendation for Key Management](https://csrc.nist.gov/pubs/sp/800/57/pt1/r5/final).
- Nemec, M. jt (2017). [The Return of Coppersmith’s Attack: Practical Factorization of Widely Used RSA Moduli (ROCA)](https://crocs.fi.muni.cz/public/papers/rsa_ccs17). ACM CCS.

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 27. UUS: Räsimine, sool ja paroolide hoidmine](27-rasimine-ja-paroolid.md) · [Sisukord](../README.md) · [29. UUS: Digiallkiri, sertifikaadid, PKI ja TLS →](29-digiallkiri-pki-tls.md)
