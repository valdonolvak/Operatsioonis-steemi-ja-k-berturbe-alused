[← 26. UUS: Sümmeetriline krüpteerimine](26-summeetriline-kruptograafia.md) · [Sisukord](../README.md) · [28. UUS: Asümmeetriline krüptograafia ja Diffie–Hellmani võtmevahetus →](28-asummeetriline-kruptograafia.md)

## 27. UUS: Räsimine, sool ja paroolide hoidmine

**Räsifunktsioon** (hash function) arvutab mis tahes andmetest kindla pikkusega „sõrmejälje“. Erinevalt krüpteerimisest ei saa räsist algandmeid tagasi. Räsisid kasutatakse failide tervikluse kontrollimiseks, paroolide hoidmiseks, digiallkirjades (ptk 29), plokiahelates ja Gitis.

### 27.1 Räsifunktsiooni omadused

![räsifunktsioon · fikseeritud pikkus, laviiniefekt, ühesuunalisus](../pildid/27-a.png)

Joonis: SHA-256 räsi on alati 256 bitti, olgu sisend üks sõna või mitme gigabaidine fail. Sisendi ühe biti muutmine muudab ligikaudu poolt räsi bittidest. Räsist sisendit tagasi arvutada ei saa.

Krüptograafiline räsifunktsioon peab olema:

1. **ühesuunaline** (preimage resistance) – räsi teades ei saa leida sisendit;
2. **teise sisendi suhtes kindel** (second preimage resistance) – antud faili jaoks ei saa leida teist faili sama räsiga;
3. **kokkupõrkekindel** (collision resistance) – ei saa leida ükskõik milliseid kahte erinevat sisendit sama räsiga;
4. **laviiniefektiga** – väike muutus sisendis muudab kogu räsi.

| Algoritm | Räsi pikkus | Seisund |
| --- | --- | --- |
| MD5 | 128 bitti | murtud: kokkupõrkeid leitakse sekunditega; Flame-pahavara võltsis 2012 sellega Windows Update’i sertifikaadi |
| SHA-1 | 160 bitti | murtud: Google ja CWI näitasid 2017 kahte erinevat sama räsiga PDF-i (SHAttered) |
| SHA-256, SHA-512 (SHA-2) | 256 / 512 bitti | turvaline, enim kasutatud |
| SHA-3 | 224–512 bitti | turvaline, teistsugune sisemine ülesehitus (Keccak) |
| BLAKE2, BLAKE3 | muudetav | turvaline, väga kiire |

### 27.2 Räsimine ei ole krüpteerimine

![kodeerimine, krüpteerimine ja räsimine · võrdlus](../pildid/27-b.png)

Joonis: kolm sageli segi aetavat tehnikat. Kodeerimine on ainult teine esitusviis, krüpteerimine on võtmega pööratav ja räsimine on pöördumatu. Hea selgitus koos näidetega on artiklis [Difference between hashing, encryption and salting](https://thecyphere.com/blog/difference-hashing-encryption-salting/).

### 27.3 Paroolide hoidmine

Veebirakendus ega operatsioonisüsteem ei tohi paroole hoida avatekstina ega krüpteeritult – ainult räsina. Sisselogimisel arvutatakse sisestatud parooli räsi ja võrreldakse seda salvestatuga. Tavaline kiire räsi (SHA-256) ei sobi, sest graafikakaart arvutab miljardeid räsisid sekundis ja ründaja proovib läbi kõik tavalised paroolid.

![parooli soolamine · sama parool, eri räsi](../pildid/27-c.png)

Joonis: **sool** (salt) on iga kasutaja jaoks juhuslikult valitud väärtus, mis lisatakse paroolile enne räsimist ja salvestatakse räsi kõrvale. Sool ei ole saladus, kuid see teeb eelarvutatud tabelid kasutuks ja peidab sama parooli kasutajad. Parooliräsi funktsioon (Argon2id, scrypt, bcrypt, PBKDF2) on meelega aeglane, nii et iga katse maksab ründajale aega ja mälu.

Lisameetmena kasutatakse vahel ka **pipar** (pepper) – salajane väärtus, mida hoitakse andmebaasist eraldi (nt riistvaralises võtmehoidlas). Siis ei piisa ründajale ainult andmebaasi varastamisest.

| Kus | Kuidas paroole hoitakse |
| --- | --- |
| Linux (`/etc/shadow`) | yescrypt (`$y$`) või SHA-512-crypt (`$6$`), igal kasutajal oma sool |
| Windows kohalik konto, AD | NT-räsi (MD4, **ilma soolata**); seetõttu toimib pass-the-hash rünne (ptk 17–18) ja Credential Guard kaitseb räsisid VBS-iga |
| Veebirakendus (soovitus) | Argon2id; kui see pole saadaval, siis scrypt, bcrypt või PBKDF2 suure iteratsioonide arvuga ([OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)) |

PHP-s tee seda nii: `password_hash($parool, PASSWORD_ARGON2ID)` ja kontrolli `password_verify($parool, $rasi)`. Ära kirjuta oma räsimisskeemi.

### 27.4 Terviklus: kontrollsumma ja HMAC

![kontrollsumma ja HMAC · juhuslik viga ja teadlik võltsing](../pildid/27-d.png)

Joonis: kontrollsumma avastab allalaadimisvea, aga mitte ründajat, kes vahetab nii faili kui ka räsi. **HMAC** (hash-based message authentication code) arvutab räsi koos salajase võtmega. Ilma võtmeta ei saa keegi kehtivat HMAC-i luua. HMAC-i kasutavad nt TLS, JWT-tokenid (HS256), API-päringute allkirjad ja Kerberos. Kui on vaja, et ka võtmeta osapool saaks päritolu kontrollida, kasutatakse digiallkirja (ptk 29).

### Näide: lekkinud paroolide andmebaas

Veebipoe andmebaas lekib. Kui paroolid on salvestatud soolamata MD5-räsina, leiab ründaja populaarsete paroolide räsid valmis tabelitest ja enamiku ülejäänutest mõne tunniga. Kuna inimesed kasutavad sama parooli mitmes kohas, proovib ta neid kohe e-posti ja pangateenustes (credential stuffing). Kui paroolid oleksid Argon2id-räsid soolaga, peaks ründaja iga kasutaja paroole eraldi ja aeglaselt arvama ning tugev parool jääks avastamata. Kasutaja poolt aitavad paroolihaldur, unikaalsed paroolid ja MFA. Kas sinu e-post on lekkes olnud, saab kontrollida teenusest [Have I Been Pwned](https://haveibeenpwned.com/).

### Kokkuvõte

- Räsi on ühesuunaline, fikseeritud pikkusega ja laviiniefektiga; MD5 ja SHA-1 on murtud, kasuta SHA-2 või SHA-3.
- Paroole hoitakse soolatud, meelega aeglase räsina (Argon2id, scrypt, bcrypt, PBKDF2).
- Kontrollsumma avastab vea, HMAC ka võltsingu; võtmeta kontrolliks on vaja digiallkirja.
- Windowsi NT-räsi on soolamata, seetõttu tuleb kaitsta räsisid endid (Credential Guard, LAPS, vähimad õigused).

### Kontrollküsimused

1. Nimeta krüptograafilise räsifunktsiooni neli omadust.
2. Miks on MD5 ja SHA-1 kasutamine digiallkirjades ohtlik?
3. Mis vahe on räsimisel ja krüpteerimisel? Miks ei hoita paroole krüpteeritult?
4. Mida teeb sool ja miks ei pea see olema salajane?
5. Miks on parooliräsi funktsioon meelega aeglane?
6. Miks ei piisa allalaaditud faili kontrollimiseks samalt lehelt võetud räsist?
7. Mis vahe on HMAC-il ja digiallkirjal?

### Valikküsimused

1. Mis on räsifunktsiooni peamine omadus? a) räsist saab võtmega avateksti tagasi b) räsist ei saa sisendit tagasi arvutada ja väike muutus sisendis muudab kogu räsi c) räsi on alati sisendist pikem d) räsi sõltub kellaajast
2. Milleks lisatakse paroolile sool? a) parool muutub lühemaks b) räsi on kiirem arvutada c) parooli saab hiljem dekrüpteerida d) sama parool annab eri kasutajatel erineva räsi ja eelarvutatud tabelid ei tööta
3. Milline algoritm sobib paroolide hoidmiseks? a) Argon2id b) MD5 c) SHA-1 d) Base64
4. Mis vahe on HMAC-il ja tavalisel räsil? a) HMAC on lühem b) HMAC on pööratav c) HMAC kasutab salajast võtit, seega ei saa ründaja seda ise uuesti arvutada d) HMAC ei kasuta räsifunktsiooni

### Praktiline ülesanne

**Räsid ja paroolid WSL-is.** Ava WSL Ubuntu: `sudo apt install -y whois john openssl`.

1. Laviiniefekt ja fikseeritud pikkus:

```bash
echo -n 'tere' | sha256sum
echo -n 'Tere' | sha256sum
echo -n 'tere' | md5sum
sha256sum /usr/bin/ls
```

Windowsis sama: `Get-FileHash -Algorithm SHA256 C:\Windows\notepad.exe`.

2. Sool Linuxi paroolides:

```bash
mkpasswd -m sha-512 'Suvi2025!'     # käivita kaks korda: tulemused erinevad
mkpasswd -m yescrypt 'Suvi2025!'
sudo grep "^$USER:" /etc/shadow     # sinu enda parooli räsi ja algoritm
```

3. Nõrkade paroolide murdmine **oma** räsidest (John the Ripper):

```bash
for p in kevad2024 Parool123 qwerty 'K8#vL2!pX9z@'; do mkpasswd -m sha-512 "$p"; done | nl -s: -w1 > rasid.txt
john --format=sha512crypt --wordlist=/usr/share/john/password.lst rasid.txt
john --show --format=sha512crypt rasid.txt
```

4. HMAC:

```bash
echo 'Ylekanne 100 EUR' > s.txt
openssl dgst -sha256 -hmac 'salajane-voti' s.txt
openssl dgst -sha256 -hmac 'vale-voti' s.txt
```

Vasta: mitu bitti erines „tere“ ja „Tere“ räsis ligikaudu? Miks andis `mkpasswd` iga kord uue tulemuse? Millised paroolid John leidis ja miks viimast mitte? Mida näitab HMAC-i muutumine vale võtmega?

### Iseseisvad ülesanded

**1. Kontrolli Ubuntu tõmmisfaili.** Laadi alla Ubuntu ISO-tõmmise räsifail `SHA256SUMS` ja selle allkiri `SHA256SUMS.gpg`. Kontrolli allkirja (`gpg --verify`) ja tõmmisfaili räsi (`sha256sum -c SHA256SUMS --ignore-missing`). **Esita:** käsud, väljund ja selgitus, mida kontrollib räsi ja mida allkiri ning miks on vaja mõlemat.

**2. Paroolipoliitika.** Kirjuta oma kooli või ettevõtte jaoks paroolipoliitika, mis järgib NIST SP 800-63B soovitusi: minimaalne pikkus, lekkinud paroolide kontroll, MFA, paroolihaldur, sunniviisilise perioodilise vahetamise puudumine. **Esita:** poliitika (kuni 1 lk) ja lühike selgitus, miks keerukusreeglid (nt „1 suurtäht ja 1 number“) aitavad vähem kui pikkus.

### Viited

- The Cyphere. [Differences between hashing and encryption and salting explained with examples](https://thecyphere.com/blog/difference-hashing-encryption-salting/).
- OWASP. [Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html).
- NIST. [SP 800-63B: Digital Identity Guidelines – Authentication](https://pages.nist.gov/800-63-4/sp800-63b.html).
- NIST. [FIPS 180-4: Secure Hash Standard](https://csrc.nist.gov/pubs/fips/180-4/upd1/final); [FIPS 202: SHA-3](https://csrc.nist.gov/pubs/fips/202/final).
- Stevens, M. jt (2017). [The first collision for full SHA-1](https://shattered.io/).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 26. UUS: Sümmeetriline krüpteerimine](26-summeetriline-kruptograafia.md) · [Sisukord](../README.md) · [28. UUS: Asümmeetriline krüptograafia ja Diffie–Hellmani võtmevahetus →](28-asummeetriline-kruptograafia.md)
