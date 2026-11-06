[← 12. Mäluhaldus](12-maluhaldus.md) · [Sisukord](../README.md) · [14. Sisend ja väljund →](14-sisend-valjund.md)

## 13. Vahemälu

**Vahemälu** (cache) on kiire ja väike mälu, kuhu hoitakse aeglasemast allikast loetud andmete koopiat, et järgmine pöördumine oleks kiirem. Vahemälu töötab, sest programmid kasutavad samu ja lähestikku asuvaid andmeid korduvalt (**ajaline** ja **ruumiline lokaalsus**). Windowsis hoiab failide vahemälu **vahemäluhaldur**.

### 13.1 Vahemälude hierarhia

| Tase | Ligikaudne maht | Ligikaudne pöördumisaeg |
| --- | --- | --- |
| Protsessori registrid | sadu baite | alla 1 ns |
| L1-vahemälu | kümneid KB tuuma kohta | \~1 ns |
| L2-vahemälu | 1–3 MB tuuma kohta | \~3–5 ns |
| L3-vahemälu | kümneid MB | \~10–20 ns |
| RAM | 8–512 GB | \~80–100 ns |
| NVMe-SSD | TB | \~50–100 µs |
| Kõvaketas (HDD) | TB | \~5–10 ms |
| Võrgu- või pilvesalvesti | piiramatu | ms kuni sajad ms |

Iga tase on vahemälu järgmisele. OS haldab peamiselt RAM-i kui ketta- ja võrguandmete vahemälu; protsessori vahemälusid haldab riistvara.

![mäluhierarhia · registritest pilveni, ligikaudsed pöördumisajad](../pildid/13-a.png)

Joonis (ei ole mõõtkavas): ülemised tasemed haldab protsessor ise; OS-i vahemäluhaldur kasutab RAM-i ketta- ja võrguandmete vahemäluna.

Kaks kirjutamisviisi:

- **läbikirjutus** (write-through) – andmed kirjutatakse korraga vahemällu ja püsisalvestisse; turvaline, kuid aeglasem;
- **tagasikirjutus** (write-back) – andmed kirjutatakse vahemällu ja hiljem kettale; kiire, kuid toitekatkestusel võivad andmed kaduda.

### 13.2 Vahemäluhaldur

Windowsi **vahemäluhaldur** (Cache Manager) töötab koos mäluhalduri ja failisüsteemidega:

- vahemälu on **virtuaalsete plokkide** põhine: faili osad vastendatakse süsteemi aadressiruumi vaadetena, vajalikud lehed laadib mäluhaldur lehevea kaudu;
- vahemälu suurus muutub dünaamiliselt vastavalt vabale mälule;
- **laisk kirjutaja** (lazy writer) kirjutab muudetud andmed kettale taustal, tavaliselt sekundi jooksul;
- **intelligentne ettelugemine** (read-ahead) tuvastab järjestikuse lugemise mustri ja loeb järgmised plokid ette;
- rakendus saab vahemälust mööda minna (`FILE_FLAG_NO_BUFFERING`) või nõuda läbikirjutust (`FILE_FLAG_WRITE_THROUGH`); andmebaasid teevad nii, sest neil on oma puhver.

### 13.3 Vahemälud mujal Windowsis

- **Seadme kirjutusvahemälu** – kettal ja kontrolleril on oma vahemälu. Seadmehaldur → ketas → Poliitikad. Eemaldatavatel ketastel on alates Windows 10 1809 vaikimisi „Kiire eemaldamine“ (vahemälu väljas), seega USB-pulga väljatõmbamine on ohutum.
- **Storage Spaces ja Storage Spaces Direct** – kiirem SSD võib olla aeglasemate ketaste vahemälu.
- **SMB-kliendi vahemälu, BranchCache, ühenduseta failid** – võrgufailide koopiad lokaalselt.
- **DNS-kliendi vahemälu** – nimede lahendamise tulemused (`ipconfig /displaydns`).
- **OneDrive’i failid nõudmisel** – pilvefail laaditakse kettale alles avamisel.

### Näide: õpilase kirjutuslaud ja tabeli teistkordne avamine

**Võrdlus.** Referaadi kirjutamisel on kõige vajalikum raamat lahti laual (L1), paar raamatut riiulil käeulatuses (RAM), ülejäänud raamatukogu hoidlas (SSD) ja haruldane teos tuleb tellida teisest raamatukogust (pilv). Sa ei too iga lause jaoks raamatut hoidlast, vaid hoiad laual neid, mida parajasti kasutad (ajaline lokaalsus), ja kui võtad riiulist peatüki 3, on tõenäoline, et vajad ka peatükki 4 (ruumiline lokaalsus – ettelugemine).

**Windowsis.** Õpetaja avab hommikul 200 MB hindetabeli võrgukettalt.

- Esimene avamine võtab 20 sekundit: andmed tulevad võrgust ja kettalt.
- Ta sulgeb Exceli ja avab faili uuesti – nüüd 3 sekundit, sest faili lehed on veel RAM-is ootenimekirjas (vahemäluhaldur).
- Kui ta salvestab, näeb Excel, et salvestamine on kohe valmis – tegelikult kirjutab laisk kirjutaja andmed võrgukettale mõne sekundi jooksul taustal. Seetõttu ei tohi sülearvutit kohe pärast salvestamist kaant kinni löödes võrgust lahti ühendada, enne kui kirjutamine on lõppenud.

### Kokkuvõte

- Vahemälu kasutab ajalist ja ruumilist lokaalsust.
- Tagasikirjutus on kiire, läbikirjutus turvaline.
- Windowsi vahemäluhaldur kasutab mäluhalduri lehti, laiska kirjutajat ja ettelugemist.

### Kontrollküsimused

1. Mis on vahemälu ja miks see töötab?
2. Selgita ajalist ja ruumilist lokaalsust.
3. Kirjelda mäluhierarhiat registritest pilveni.
4. Mis vahe on läbikirjutusel ja tagasikirjutusel?
5. Mis ülesanded on Windowsi vahemäluhalduril?
6. Mida teeb laisk kirjutaja?
7. Miks lülitavad andmebaasid failisüsteemi vahemälu välja?

### Valikküsimused

1. Milline on kiireim? a) L3 b) RAM c) L1 d) NVMe-SSD
2. Mis on tagasikirjutuse risk? a) aeglus b) andmekadu toitekatkestusel c) suur mälukasutus d) vigased failinimed
3. Mis Windowsi komponent kirjutab muudetud vahemälulehed taustal kettale? a) laisk kirjutaja b) PnP-haldur c) objektihaldur d) SysMain

### Praktiline ülesanne

```powershell
Get-Counter '\Cache\Copy Read Hits %','\Cache\Lazy Write Flushes/sec','\Memory\Cache Bytes' -MaxSamples 5
ipconfig /displaydns | Select-Object -First 20
```

Kopeeri suur fail (nt 2 GB) kaks korda järjest ühest kaustast teise ja võrdle aegu (`Measure-Command { Copy-Item … }`). Miks on teine kord kiirem? Vaata seadmehalduris oma ketta ja USB-pulga kirjutusvahemälu poliitikat.

### Iseseisvad ülesanded

**1. Külm ja soe vahemälu.** Vali 1–2 GB fail (nt video). Mõõda faili lugemise aeg `Measure-Command { Get-FileHash .\video.mp4 }` kolm korda järjest. Seejärel taaskäivita arvuti ja mõõda uuesti üks kord. **Esita:** tabel nelja mõõtmisega ja selgitus, miks esimene mõõtmine pärast taaskäivitust on aeglasem ning milline Windowsi komponent teisel korral aitas.

**2. Vahemälu ja andmete turvalisus.** Uuri seadmehalduris oma süsteemiketta ja USB-pulga kirjutusvahemälu poliitikat. Kirjelda olukorda (nt elektrikatkestus kooli serveriruumis), kus tagasikirjutav vahemälu põhjustab andmekadu, ja nimeta kaks meedet riski vähendamiseks (nt UPS, aku-toega kontrolleri vahemälu, läbikirjutus). **Esita:** ekraanipildid ja ühe lõigu pikkune analüüs.

### Viited

- Krogh (2017), ptk 13.
- Yosifovich jt, *Windows Internals* 7. tr, 2. osa, ptk 11 „Caching and file systems“.
- Hennessy, J. L., Patterson, D. A. (2017). *Computer Architecture: A Quantitative Approach*, 6. tr, ptk 2.

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 12. Mäluhaldus](12-maluhaldus.md) · [Sisukord](../README.md) · [14. Sisend ja väljund →](14-sisend-valjund.md)
