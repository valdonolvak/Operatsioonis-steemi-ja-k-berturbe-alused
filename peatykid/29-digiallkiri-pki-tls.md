[← 28. UUS: Asümmeetriline krüptograafia ja Diffie–Hellmani võtmevahetus](28-asummeetriline-kruptograafia.md) · [Sisukord](../README.md) · [30. UUS: Postkvantkrüptograafia ja krüptograafia tulevik →](30-postkvantkruptograafia.md)

## 29. UUS: Digiallkiri, sertifikaadid, PKI ja TLS

Asümmeetriline krüptograafia lahendab võtmejagamise, kuid jätab ühe küsimuse: **kuidas teada, et avalik võti kuulub tõesti sellele, kellele väidetakse?** Vastus on digiallkiri ja sertifikaatide süsteem ehk **avaliku võtme taristu** (PKI, public key infrastructure). Selle peale on ehitatud HTTPS, Eesti ID-kaart, draiverite allkirjad, Secure Boot (ptk 18) ja tarkvarauuendused.

### 29.1 Digiallkiri

![digiallkiri · allkirjastamine ja kontroll](../pildid/29-a.png)

Joonis: allkirjastaja arvutab dokumendist räsi ja allkirjastab selle oma privaatvõtmega. Kontrollija arvutab sama räsi uuesti ja kontrollib allkirja allkirjastaja avaliku võtmega. Kui dokumendis muutub kas või üks bait, räsid ei klapi ja allkiri on kehtetu.

Allkirjastatakse räsi, mitte kogu dokumenti, sest räsi on lühike ja asümmeetrilised tehted on aeglased. Seetõttu peab ka räsifunktsioon olema kokkupõrkekindel (ptk 27): kui ründaja leiaks kaks sama räsiga dokumenti, kehtiks üks allkiri mõlemale. Levinud allkirjaalgoritmid on RSA-PSS, ECDSA, Ed25519 ja postkvantalgoritm ML-DSA (ptk 30).

### 29.2 Sertifikaadid ja usaldusahel

**Sertifikaat** (X.509) on dokument, mis seob avaliku võtme nimega (domeeni, isiku või organisatsiooniga) ja mille on allkirjastanud **sertifitseerimiskeskus** (CA, certificate authority). Sertifikaadis on omaniku nimi, avalik võti, kehtivusaeg, kasutusotstarve, väljaandja ja väljaandja allkiri.

![sertifikaadiahel · juur-CA, vahe-CA, serveri sertifikaat](../pildid/29-b.png)

Joonis: usaldusahel. Brauser ei tunne www.kool.ee serverit, kuid usaldab juur-CA-d, mille sertifikaat on Windowsi või brauseri usaldushoidlas. Juur-CA privaatvõtit hoitakse võrgust eraldatud riistvaras ja sellega allkirjastatakse ainult vahe-CA-sid. Vahe-CA väljastab igapäevased serverisertifikaadid.

| Mõiste | Tähendus |
| --- | --- |
| Usaldushoidla (trust store) | OS-i või brauseri loend usaldatud juur-CA-dest |
| CSR (certificate signing request) | serveri avalik võti + nimi, mis saadetakse CA-le allkirjastamiseks |
| Tühistamine (revocation) | sertifikaadi kehtetuks tunnistamine enne aja lõppu, nt privaatvõtme lekke tõttu (CRL, OCSP) |
| Sertifikaadi läbipaistvus (CT) | avalikud logid kõigist väljastatud TLS-sertifikaatidest; vaata nt [crt.sh](https://crt.sh/) |
| Iseallkirjastatud sertifikaat | omaniku enda allkirjastatud; sobib laborisse, mitte avalikku teenusesse |

TLS-sertifikaatide kehtivus lüheneb: CA/Browser Forumi otsuse kohaselt väheneb maksimaalne kehtivus 2026. aasta märtsist 200 päevale ja 2029. aastaks 47 päevale. Seetõttu peab sertifikaatide uuendamine olema automaatne (ACME-protokoll, nt Let’s Encrypt, win-acme, certbot).

### 29.3 TLS ja HTTPS

**TLS** (Transport Layer Security) kaitseb HTTPS-i, e-posti (SMTPS, IMAPS), VPN-e ja paljusid teisi protokolle. Kehtivad versioonid on TLS 1.2 ja 1.3; SSL 3.0, TLS 1.0 ja 1.1 on keelatud.

![TLS 1.3 käepigistus · sõnumid brauseri ja serveri vahel](../pildid/29-c.png)

Joonis: TLS 1.3 käepigistus. Brauser saadab oma võtmejagamise kohe esimeses sõnumis. Pärast serveri vastust on mõlemal sama seansivõti ja ülejäänud käepigistus, sh serveri sertifikaat, liigub juba krüpteeritult. CertificateVerify on serveri allkiri kogu senisele käepigistusele – see tõendab, et serveril on sertifikaadi privaatvõti, ja takistab MITM-i.

TLS ühendab kõik eelmiste peatükkide ehitusplokid: võtmevahetus (ECDHE või hübriidne X25519MLKEM768, ptk 28 ja 30), sertifikaat ja allkiri (29.1–29.2), sümmeetriline AEAD-krüpteerimine (AES-GCM või ChaCha20-Poly1305, ptk 26) ja räsipõhine võtmetuletus (HKDF, ptk 27). Ubuntu 25.10 ja 26.04 kasutavad OpenSSL 3.5-ga TLS-is vaikimisi hübriidset postkvantvõtmevahetust ([Ubuntu Discourse](https://discourse.ubuntu.com/t/post-quantum-support-in-the-upcoming-26-04-lts/76840)).

**Mida HTTPS ei tõenda.** Lukuikoon näitab, et ühendus on krüpteeritud ja server omab sertifikaati selle domeeni jaoks. See ei näita, et domeen on usaldusväärne: õngitsusleht `minu-pank-turvakontroll.com` saab samamoodi tasuta sertifikaadi. Kontrolli alati aadressi.

### 29.4 Eesti e-identiteet

![ID-kaart ja Smart-ID · kus privaatvõtmed asuvad](../pildid/29-d.png)

Joonis: Eesti ID-kaardi kiibis on kaks eraldi võtmepaari koos sertifikaatidega. PIN1 võimaldab autentimist, PIN2 kvalifitseeritud digiallkirja, mis on Euroopa Liidu eIDAS-määruse järgi võrdne omakäelise allkirjaga. Smart-ID kasutab Cyberneticas loodud jagatud võtme tehnoloogiat. Märtsi 2025 seisuga oli Eestis 1,4 miljonit kehtivat ID-kaarti ja Smart-ID tunnistati kõrge tagatistasemega identimisvahendiks ([RIA](https://ria.ee/en/state-information-system/electronic-identity-eid-and-trust-services/integration-tools-eid)).

Eesti e-identiteedi juhtumid näitavad, et krüptograafiat tuleb pidevalt hooldada: 2017. aasta **ROCA** viga kiibi RSA-võtmete genereerimises (ptk 28.4) lahendati kaartide kaugsertifikaatide uuendamisega ja üleminekuga elliptkõveratele. Alates 15. novembrist 2025 väljastatav uus ID-kaart on kontaktivaba ja QR-koodiga, kuid autentimise ja allkirjastamise põhimõte on sama.

### Näide: Windowsi koodiallkiri

Kasutaja laadib alla programmi `setup.exe`. Windows kontrollib selle **Authenticode** allkirja:

- kehtiv allkiri usaldatud tootjalt → UAC näitab tootja nime (nt „Verified publisher: Mozilla Corporation“);
- allkiri puudub või on vigane → UAC näitab „Unknown publisher“ ja SmartScreen või Smart App Control võivad käivituse peatada.

Kui ründaja muudab allkirjastatud faili kas või ühe baidi võrra, allkiri enam ei kehti. Seetõttu proovivad ründajad varastada tootjate allkirjastamisvõtmeid. Sama juhtus 2010. aastal Stuxnetiga, mille draiverid olid allkirjastatud kahelt Taiwani firmalt varastatud sertifikaatidega. Turvaline arendaja hoiab koodiallkirja võtit riistvaralises turvamoodulis (HSM) või pilveteenuses, nt Azure Trusted Signing.

### Kokkuvõte

- Digiallkiri tehakse privaatvõtmega dokumendi räsist ja kontrollitakse avaliku võtmega; see annab tervikluse, autentsuse ja mittesalgamise.
- Sertifikaat seob avaliku võtme nimega; usaldus tekib ahelast, mis lõpeb usaldushoidlas oleva juur-CA-ga.
- TLS 1.3 ühendab võtmevahetuse, sertifikaadi, allkirja ja sümmeetrilise krüpteerimise; lukuikoon ei tõenda, et leht on aus.
- ID-kaardi privaatvõtmed on kiibis: PIN1 autentimiseks, PIN2 allkirjastamiseks.

### Kontrollküsimused

1. Kirjelda digiallkirja andmise ja kontrollimise samme.
2. Miks allkirjastatakse dokumendi räsi, mitte dokument ise?
3. Mida sisaldab X.509-sertifikaat?
4. Kuidas töötab usaldusahel ja miks on juur-CA privaatvõti võrgust eraldatud?
5. Miks on TLS-sertifikaatide kehtivusaeg lühenemas ja mida see administraatorile tähendab?
6. Mida tõestab CertificateVerify-sõnum TLS 1.3 käepigistuses?
7. Mis vahe on ID-kaardi PIN1-l ja PIN2-l?
8. Miks ei tähenda lukuikoon brauseris, et veebileht on usaldusväärne?

### Valikküsimused

1. Millise võtmega allkirjastatakse dokument digitaalselt? a) saaja avaliku võtmega b) AES-võtmega c) allkirjastaja privaatvõtmega d) sertifitseerimiskeskuse avaliku võtmega
2. Mida kinnitab TLS-sertifikaat? a) et avalik võti kuulub nimetatud domeenile, kinnitatud usaldusväärse CA allkirjaga b) et veebileht ei sisalda pahavara c) et server asub Eestis d) et ühendus on kiire
3. Mis on juursertifikaat? a) serveri privaatvõti b) usaldusahela tipus olev CA sertifikaat, mis on OS-i või brauseri usaldushoidlas c) ajutine seansivõti d) räsifunktsioon
4. Milleks on ID-kaardi PIN2? a) avab kaardi füüsiliselt b) logib arvutisse c) muudab PIN1 d) kinnitab digiallkirja andmise privaatvõtmega

### Praktiline ülesanne

**Oma CA ja TLS-server WSL-is.** Ava WSL Ubuntu.

1. Loo laboris oma sertifitseerimiskeskus ja serveri sertifikaat:

```bash
mkdir -p ~/pki && cd ~/pki
openssl req -x509 -new -newkey ec -pkeyopt ec_paramgen_curve:P-256 -nodes -days 365 \
  -keyout ca.key -out ca.crt -subj "/CN=Kooli Labori CA"
openssl req -new -newkey ec -pkeyopt ec_paramgen_curve:P-256 -nodes \
  -keyout server.key -out server.csr -subj "/CN=localhost"
printf 'subjectAltName=DNS:localhost\nextendedKeyUsage=serverAuth\n' > ext.cnf
openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -days 90 -extfile ext.cnf -out server.crt
openssl verify -CAfile ca.crt server.crt
openssl x509 -in server.crt -noout -text | less
```

2. Käivita TLS-server ühes terminalis ja ühendu teisest:

```bash
openssl s_server -accept 8443 -cert server.crt -key server.key -www
openssl s_client -connect localhost:8443 -CAfile ca.crt -brief      # teises aknas
```

3. Vaata päris serveri ahelat ja kasutatud võtmevahetust:

```bash
openssl s_client -connect www.ria.ee:443 -servername www.ria.ee -showcerts </dev/null 2>/dev/null | grep -E 's:|i:|Protocol|group|Cipher'
```

4. Allkirjasta fail ja kontrolli allkirja:

```bash
echo 'Leping: 100 eurot' > leping.txt
openssl dgst -sha256 -sign server.key -out leping.sig leping.txt
openssl dgst -sha256 -verify <(openssl x509 -in server.crt -pubkey -noout) -signature leping.sig leping.txt
echo 'Leping: 900 eurot' > leping.txt && openssl dgst -sha256 -verify <(openssl x509 -in server.crt -pubkey -noout) -signature leping.sig leping.txt
```

5. Windowsis: `Get-AuthenticodeSignature C:\Windows\System32\notepad.exe | Format-List` ja ava `certlm.msc`.

Vasta: miks ütleb `s_client` ilma `-CAfile` võtmeta, et sertifikaati ei saa kontrollida? Milline TLS-versioon ja võtmevahetuse rühm valiti www.ria.ee ühenduses? Mis juhtus allkirjaga pärast lepingu muutmist?

### Iseseisvad ülesanded

**1. Sertifikaadi uurimine.** Vali kolm Eesti veebilehte (nt kool, pank, riigiasutus). Uuri iga sertifikaati brauseris ja lehel [crt.sh](https://crt.sh/): väljaandja, kehtivusaeg, alternatiivnimed (SAN), võtme tüüp ja ahela pikkus. **Esita:** võrdlustabel ja selgitus, kui palju sertifikaate on domeenile viimase aasta jooksul väljastatud ja miks CT-logid on kasulikud.

**2. Digiallkiri ID-kaardi või Smart-ID-ga.** Allkirjasta DigiDoc4-ga dokument (.asice) ja lase kaaslasel see kontrollida. Seejärel muuda konteineris olevat faili (paki lahti, muuda, paki uuesti) ja kontrolli uuesti. **Esita:** kuvatõmmised mõlemast kontrollist ja selgitus, miks muudetud fail allkirja ei läbi.

### Viited

- Tartu Ülikool. [Infoturve: avaliku võtme taristu (PKI)](https://courses.cs.ut.ee/2022/infsec/spring/Main/PKI).
- IETF. [RFC 8446: The Transport Layer Security (TLS) Protocol Version 1.3](https://www.rfc-editor.org/rfc/rfc8446).
- RIA. [Integration tools of eID](https://ria.ee/en/state-information-system/electronic-identity-eid-and-trust-services/integration-tools-eid); [id.ee](https://www.id.ee/).
- Ubuntu. [Post Quantum Support in the upcoming 26.04 LTS](https://discourse.ubuntu.com/t/post-quantum-support-in-the-upcoming-26-04-lts/76840).
- Parsovs, A. (2021). [Estonian Electronic Identity Card and its Security Challenges](https://dspace.ut.ee/handle/10062/71481). Doktoritöö, Tartu Ülikool.
- Microsoft Learn. [Cryptography and Certificate Management](https://learn.microsoft.com/windows/security/operating-system-security/data-protection/).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 28. UUS: Asümmeetriline krüptograafia ja Diffie–Hellmani võtmevahetus](28-asummeetriline-kruptograafia.md) · [Sisukord](../README.md) · [30. UUS: Postkvantkrüptograafia ja krüptograafia tulevik →](30-postkvantkruptograafia.md)
