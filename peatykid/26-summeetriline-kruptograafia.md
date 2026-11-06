[← 25. UUS: Krüptograafia ajalugu ja põhimõisted](25-kruptograafia-ajalugu.md) · [Sisukord](../README.md) · [27. UUS: Räsimine, sool ja paroolide hoidmine →](27-rasimine-ja-paroolid.md)

## 26. UUS: Sümmeetriline krüpteerimine

Sümmeetrilises krüptograafias kasutavad krüpteerija ja dekrüpteerija **sama võtit**. See on kiire ja töötab kõikjal, kus andmeid on palju: kettakrüpteerimine (BitLocker), võrguühendused (TLS, SMB, Wi-Fi), varukoopiad ja ZIP-arhiivid.

### 26.1 Põhimõte ja võtmejagamise probleem

![sümmeetriline krüptograafia · ühine võti ja võtmete arv](../pildid/26-a.png)

Joonis: sümmeetrilise krüptograafia suurim probleem ei ole matemaatika, vaid võtmete jagamine. Kui kaks inimest ei ole kunagi kohtunud, ei saa nad võtit avalikus võrgus saata. Lisaks vajab iga paar eraldi võtit. Probleemi lahendab asümmeetriline võtmevahetus (ptk 28), mille järel kasutatakse andmete krüpteerimiseks ikkagi sümmeetrilist šifrit.

### 26.2 Plokk- ja voošifrid

- **Plokkšiffer** (block cipher) krüpteerib korraga kindla suurusega ploki, AES-i puhul 128 bitti ehk 16 baiti. Pikem sõnum jagatakse plokkideks ja nende sidumise reegel on **töörežiim** (26.4).
- **Voošiffer** (stream cipher) tekitab võtmest pseudojuhusliku baidivoo ja liidab selle avatekstiga XOR-tehtega. Tänane näide on **ChaCha20**, mida kasutatakse TLS-is seadmetes, millel puudub AES-i riistvaratugi (nt vanemad telefonid).

**XOR** (välistav või, ⊕) on krüptograafia põhitehe: 0 ⊕ 0 = 0, 1 ⊕ 0 = 1, 0 ⊕ 1 = 1, 1 ⊕ 1 = 0. Selle oluline omadus on, et sama võtme teistkordne XOR taastab algse väärtuse: (A ⊕ K) ⊕ K = A. Näide: 0101 ⊕ 0011 = 0110 ja 0110 ⊕ 0011 = 0101.

### 26.3 AES

**AES** (Advanced Encryption Standard, algne nimi Rijndael) on 2001. aastast USA standard FIPS 197 ja maailmas enim kasutatud sümmeetriline šiffer.

![AES-i ring · SubBytes, ShiftRows, MixColumns, AddRoundKey](../pildid/26-b.png)

Joonis: AES käsitleb 16 baiti 4 × 4 tabelina ja segab seda mitu ringi. SubBytes lisab mittelineaarsuse, ShiftRows ja MixColumns levitavad iga biti mõju kogu plokile ning AddRoundKey seob tulemuse võtmega. Kahe ringi järel mõjutab iga avateksti bitt kõiki šifriteksti bitte.

| Algoritm | Võtme pikkus | Seisund 2026 |
| --- | --- | --- |
| DES | 56 bitti | murtud toore jõuga (1998), ei kasutata |
| 3DES | 112 bitti (efektiivne) | NIST keelas uutes süsteemides; Windows eemaldab |
| RC4 | 40–128 bitti | murtud; Kerberoses ja TLS-is keelatud |
| AES-128 | 128 bitti | turvaline, BitLockeri vaikimisi valik |
| AES-256 | 256 bitti | turvaline ka kvantarvutite arvestades (ptk 30) |
| ChaCha20 | 256 bitti | turvaline, kiire ilma riistvaratoeta |

Miks on 128 bitti piisav? Võimalikke võtmeid on 2¹²⁸ ≈ 3,4 × 10³⁸. Kui arvuti proovib miljard miljardit (10¹⁸) võtit sekundis, kulub keskmiselt üle 5 × 10¹² aasta – sadu kordi rohkem, kui on universumi vanus.

### 26.4 Töörežiimid

![töörežiimid · ECB ja GCM](../pildid/26-c.png)

Joonis: töörežiim määrab, kuidas plokid omavahel seotakse. ECB-režiimis krüpteeritakse iga plokk eraldi ja samad avateksti plokid annavad sama šifriteksti. ECB-ga krüpteeritud pildil on esialgne kujutis siiani näha (tuntud „ECB pingviini“ näide).

| Režiim | Kuidas töötab | Kasutus | Märkus |
| --- | --- | --- | --- |
| ECB | iga plokk eraldi | ära kasuta | lekitab mustreid |
| CBC | iga plokk XOR-itakse eelmise šifriplokiga, algul IV | vanemad süsteemid | vajab täidist (padding); haavatav, kui terviklust ei kontrollita |
| CTR | loendur krüpteeritakse, tulemus XOR-itakse andmetega | voona | nonce ei tohi korduda |
| GCM | CTR + autentimissilt (tag) | TLS, SMB 3.1.1, IPsec | **AEAD**: krüpteerib ja kontrollib terviklust |
| XTS | iga sektor oma „puudutusega“ (tweak) | kettakrüpteerimine (BitLocker) | sektori suurus ei muutu |

**AEAD** (authenticated encryption with associated data) tähendab, et lisaks krüpteerimisele arvutatakse **autentimissilt**. Kui keegi muudab šifriteksti kas või ühe biti võrra, keeldub dekrüpteerimine. Ilma sildita võib ründaja šifriteksti sihipäraselt muuta (bitt-ümberpööramise rünne) ilma seda dekrüpteerimata.

**Nonce** (number used once) ja **IV** (initsialiseerimisvektor) ei ole saladused, kuid need peavad iga krüpteerimise jaoks olema erinevad. Kui GCM-is kasutada sama võtit ja nonce’i kaks korda, saab ründaja kahe sõnumi XOR-i ja võib võltsida silte.

### 26.5 Võtmed tulevad juhuslikkusest või paroolist

Krüptovõti peab olema juhuslik. Selleks kasutatakse krüptograafiliselt turvalist juhuarvugeneraatorit (CSPRNG): Windowsis `BCryptGenRandom`, Linuxis `/dev/urandom` või `getrandom()`, Pythonis moodul `secrets` (mitte `random`!). Kui võti tuleb tuletada paroolist, kasutatakse aeglast **võtmetuletusfunktsiooni** (KDF): PBKDF2, scrypt või Argon2 (ptk 27). Need muudavad iga parooliäraarvamise katse aeglaseks.

### 26.6 Sümmeetriline krüpteerimine Windowsis

| Koht | Algoritm |
| --- | --- |
| BitLocker | XTS-AES-128 (vaikimisi) või XTS-AES-256 |
| EFS (failide krüpteerimine) | AES, võti kaitstud kasutaja sertifikaadiga |
| SMB 3.1.1 | AES-128-GCM, AES-256-GCM |
| Kerberos | AES-256 ja AES-128; RC4 on kasutuselt kõrvaldamisel |
| Wi-Fi WPA3 | AES-CCMP või AES-GCMP |
| TLS 1.3 | AES-GCM või ChaCha20-Poly1305 |

### Näide: sülearvuti jäi rongi

Kooli õpetaja sülearvuti, millel on õppijate hinded ja isikuandmed, jäi rongi. Kui ketas on BitLockeriga krüpteeritud ja võti on TPM-is koos PIN-koodiga, ei saa leidja andmeid lugeda, isegi kui ta ketta teise arvutisse paneb: ta näeb ainult XTS-AES-i šifriteksti. Juhtum tuleb isikuandmete kaitse üldmääruse järgi dokumenteerida, kuid kui andmed olid tugevalt krüpteeritud ja võti ei lekkinud, ei ole andmesubjektide teavitamine üldjuhul vajalik ja sageli ei teki ka kohustust Andmekaitse Inspektsiooni teavitada (ptk 31). Krüpteerimata kettaga oleks see olnud isikuandmete leke.

### Kokkuvõte

- Sümmeetriline krüptograafia kasutab ühte võtit; see on kiire, kuid võtme jagamine on probleem.
- AES on tänane standard; 128-bitine võti on toore jõu vastu piisav, pikaajaliseks kaitseks eelista 256-bitist.
- Töörežiim on sama oluline kui algoritm: ära kasuta ECB-d, eelista AEAD-režiimi (GCM, ChaCha20-Poly1305).
- Nonce ja IV ei ole saladused, kuid need ei tohi sama võtmega korduda.

### Kontrollküsimused

1. Miks vajab 20-liikmeline rühm sümmeetrilise krüptograafiaga 190 võtit?
2. Mis vahe on plokkšifril ja voošifril? Too mõlema kohta näide.
3. Mida teevad AES-i ringis SubBytes, ShiftRows, MixColumns ja AddRoundKey?
4. Miks ei tohi kasutada ECB-režiimi?
5. Mis on AEAD ja miks on autentimissilt vajalik?
6. Miks ei tohi GCM-is nonce korduda?
7. Milliseid sümmeetrilisi algoritme kasutab Windows ja kus?

### Valikküsimused

1. Mis on sümmeetrilise krüptograafia peamine probleem? a) see on aeglane b) see ei tööta failidega c) see vajab kvantarvutit d) sama võti tuleb turvaliselt mõlemale poolele toimetada
2. Miks ei tohi kasutada ECB-režiimi? a) see on liiga aeglane b) samad avateksti plokid annavad sama šifriteksti ja muster jääb nähtavaks c) see nõuab 512-bitist võtit d) see töötab ainult Linuxis
3. Mida lisab AES-GCM tavalisele krüpteerimisele? a) terviklikkuse kontrolli (autentimissildi) b) pakkimise c) digiallkirja d) võtmevahetuse
4. Milline AES-i võtmepikkus on soovitatav pikaajaliseks kaitseks, arvestades kvantarvuteid? a) 56 bitti b) 64 bitti c) 256 bitti d) 40 bitti

### Praktiline ülesanne

**AES WSL-is.** Ava WSL Ubuntu ja paigalda tööriistad: `sudo apt install -y openssl python3-cryptography xxd`.

1. Krüpteeri ja dekrüpteeri fail paroolist tuletatud võtmega:

```bash
echo 'Hinded: Mari 5, Jaan 4' > hinded.txt
openssl enc -aes-256-cbc -pbkdf2 -iter 600000 -salt -in hinded.txt -out hinded.enc
xxd hinded.enc | head -3          # algab sõnaga Salted__, edasi šifritekst
openssl enc -d -aes-256-cbc -pbkdf2 -iter 600000 -in hinded.enc   # vale parool → viga
```

2. Näe ECB mustrit oma silmaga:

```bash
printf 'AAAAAAAAAAAAAAAA%.0s' {1..4} > kordus.bin    # 4 ühesugust 16-baidist plokki
K=$(openssl rand -hex 16)
openssl enc -aes-128-ecb -K $K -nosalt -nopad -in kordus.bin | xxd
openssl enc -aes-128-cbc -K $K -iv $(openssl rand -hex 16) -nosalt -nopad -in kordus.bin | xxd
```

3. AES-GCM ja muutmise avastamine (fail `gcm.py`):

```python
from cryptography.hazmat.primitives.ciphers.aead import AESGCM
import os
voti = AESGCM.generate_key(bit_length=256)
aes = AESGCM(voti)
nonce = os.urandom(12)
sifr = aes.encrypt(nonce, b"Ylekanne: 100 eurot", None)
print(aes.decrypt(nonce, sifr, None))
muudetud = bytearray(sifr); muudetud[12] ^= 0x01     # muuda üks bitt
try:
    aes.decrypt(nonce, bytes(muudetud), None)
except Exception as e:
    print("Muudatus avastati:", type(e).__name__)
```

4. Windowsis (PowerShell administraatorina): `Get-BitLockerVolume | Select MountPoint, EncryptionMethod, ProtectionStatus`.

Vasta: mitu ühesugust rida näitas `xxd` ECB-režiimis ja mitu CBC-režiimis? Mis juhtus muudetud GCM-sõnumiga? Millist meetodit kasutab sinu BitLocker?

### Iseseisvad ülesanded

**1. Kiiruse mõõtmine.** Käivita WSL-is `openssl speed -evp aes-128-gcm`, `openssl speed -evp aes-256-gcm` ja `openssl speed -evp chacha20-poly1305`. Kontrolli, kas protsessor toetab AES-NI-d (`grep -o -m1 aes /proc/cpuinfo`). **Esita:** tabel (algoritm, MB/s 16 KB plokkidega) ja selgitus, miks AES on sinu arvutis kiirem või aeglasem kui ChaCha20.

**2. Krüpteeritud varukoopia plaan.** Koosta väikese kontori (5 tööjaama, 1 failiserver) varukoopiate krüpteerimise plaan: millega krüpteeritakse, kus hoitakse võtit, kes saab taastada, mis juhtub, kui võti kaob, ja kuidas taastamist testitakse. **Esita:** ühe lehekülje protseduur.

### Viited

- NIST. [FIPS 197: Advanced Encryption Standard (AES)](https://csrc.nist.gov/pubs/fips/197/final) (2001, uuendatud 2023).
- NIST. [SP 800-38D: Galois/Counter Mode (GCM)](https://csrc.nist.gov/pubs/sp/800/38/d/final).
- Microsoft Learn. [BitLocker overview](https://learn.microsoft.com/windows/security/operating-system-security/data-protection/bitlocker/).
- Ferguson, N., Schneier, B., Kohno, T. (2010). *Cryptography Engineering*. Wiley.
- Tartu Ülikool. [Infoturve: andmete kaitsmine krüpteerimise abil](https://courses.cs.ut.ee/2020/infsec/spring/Main/Loeng-AndmeteKrypteerimine).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 25. UUS: Krüptograafia ajalugu ja põhimõisted](25-kruptograafia-ajalugu.md) · [Sisukord](../README.md) · [27. UUS: Räsimine, sool ja paroolide hoidmine →](27-rasimine-ja-paroolid.md)
