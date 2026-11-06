[← 1. Windowsi ajalugu](01-windowsi-ajalugu.md) · [Sisukord](../README.md) · [3. Windowsi põhimõisted →](03-pohimoisted.md)

## 2. Operatsioonisüsteemi ülesanded

Operatsioonisüsteem on vahendaja rakenduste ja riistvara vahel: ta jagab ressursse (protsessoriaeg, mälu, kettad, võrk), kaitseb programme üksteise eest ja pakub programmeerijale ühtse liidese, et rakendus ei peaks iga seadme eripära tundma.

Arvutis töötab kahte liiki tarkvara. **Rakendustarkvara** (application software) on mõeldud lõppkasutajale: brauser, tekstitöötlus, mäng. **Süsteemitarkvara** (system software) on madala taseme programmid: operatsioonisüsteem, draiverid, kompilaatorid, haldustööriistad.

### 2.1 Arvuti ülesehitusest

Klassikaline von Neumanni arhitektuur koosneb protsessorist (CPU), põhimälust (RAM), sisend-väljundseadmetest ja neid ühendavatest siinidest. Programmi käsud ja andmed asuvad samas mälus; protsessor loeb käsu, dekodeerib ja täidab selle.

Tänapäeva arvuti on keerukam:

- **mitmetuumaline protsessor**, sageli hübriidne: jõudluse tuumad (P-cores) ja säästlikud tuumad (E-cores);
- **graafikaprotsessor (GPU)** – graafika ja paralleelarvutuse jaoks;
- **neurotöötlusseade (NPU)** – tehisintellekti mudelite säästlikuks käitamiseks (vt ptk 21);
- **turvaprotsessor** – TPM 2.0 või protsessorisse integreeritud Microsoft Pluton;
- **NVMe-SSD** – kiire salvesti otse PCIe-siinil;
- **UEFI püsivara** – asendas BIOS-i, võimaldab Secure Booti.

### 2.2 Operatsioonisüsteemi kesksed ülesanded

| Ülesanne | Mida OS teeb | Windowsi komponent (näide) |
| --- | --- | --- |
| Protsesside haldus | loob ja lõpetab protsesse, planeerib protsessoriaega | protsessihaldur, planeerija |
| Mäluhaldus | jagab ja kaitseb mälu, virtuaalmälu | mäluhaldur |
| Failide ja salvestuse haldus | failisüsteemid, kettad, vahemälu | NTFS, ReFS, vahemäluhaldur |
| Sisend ja väljund | draiverid, seadmete tuvastus, toide | S/V-haldur, PnP-haldur, toitehaldur |
| Võrk | protokollid, võrguteenused | TCP/IP pinu, SMB |
| Turvalisus | autentimine, õigused, auditilogid | turvaviitemonitor (SRM), LSASS |
| Kasutajaliides | graafiline kest, käsurealõikesüsteem | Explorer, PowerShell, Windows Terminal |

> 📊 **Joonis:** arvutisüsteemi kihid · kasutaja, rakendused, OS, riistvara

Joonis: rakendus ei pöördu kunagi otse riistvara poole – kõik käib OS-i kaudu, mis jagab ressursse ja kaitseb programme.

OS-i saab vaadelda kahest küljest. **Laiendatud masinana** peidab ta riistvara keerukuse: rakendus kutsub välja „ava fail“ ega tea, kas fail on SSD-l, USB-pulgal või võrgukettal. **Ressursihaldurina** otsustab ta, kes ja millal ressurssi kasutab, et süsteem oleks õiglane, tõhus ja turvaline.

Tänapäeval lisanduvad veel kaks ootust: **töökindlus ja taastumine** (süsteem peab vigadest ise taastuma, vt ptk 19) ning **energiatõhusus** (sülearvutis ja telefonis on aku sama oluline ressurss kui protsessoriaeg).

### Näide: mis juhtub, kui vajutad Wordis Ctrl+S

1. **Rakendus** kutsub välja API funktsiooni „kirjuta fail“. Word ei tea, kas fail on SSD-l, USB-pulgal või OneDrive’is.
2. **OS kontrollib õigusi**: kas sul on lubatud sellesse kausta kirjutada?
3. **Failisüsteem** (NTFS) leiab kettal vaba ruumi ja uuendab oma tabeleid.
4. **Vahemälu** võtab andmed vastu ja Word saab kohe edasi töötada; kettale kirjutatakse veidi hiljem.
5. **Draiver** saadab käsu SSD kontrollerile, mis kirjutab andmed välkmällu.

Samal ajal mängib brauser muusikat ja viirusekaitse kontrollib faili. Igaüks saab protsessoriaega ja mälu, sest OS jagab neid ressursse. **Analüüs:** see on OS-i kahe vaate praktiline näide – sammud 1, 3 ja 5 näitavad **laiendatud masinat** (riistvara on peidetud), protsessoriaja jagamine näitab **ressursihaldurit**.

### Kokkuvõte

- OS on vahendaja rakenduste ja riistvara vahel.
- OS jagab ressursse ja isoleerib programme üksteisest.
- Kaasaegses arvutis on lisaks CPU-le ka GPU, NPU ja turvaprotsessor, mida OS peab haldama.

### Kontrollküsimused

1. Mis vahe on rakendustarkvaral ja süsteemitarkvaral? Too kummastki kaks näidet.
2. Kirjelda von Neumanni arhitektuuri põhiosi.
3. Nimeta viis operatsioonisüsteemi keskset ülesannet.
4. Selgita OS-i kahte vaadet: laiendatud masin ja ressursihaldur.
5. Milliseid uusi riistvarakomponente peab tänapäeva OS haldama, mida 2000. aastatel polnud?

### Valikküsimused

1. Milline neist on süsteemitarkvara? a) veebibrauser b) seadmedraiver c) tabelarvutus d) mäng
2. Mis on NPU peamine ülesanne? a) graafika renderdamine b) võrguliikluse krüpteerimine c) AI-mudelite säästlik käitamine d) andmete salvestamine
3. Mis asendas BIOS-i? a) TPM b) UEFI c) ACPI d) NVMe

### Praktiline ülesanne

Vaata oma arvuti riistvara ja selle kasutust:

```powershell
Get-CimInstance Win32_Processor | Select-Object Name, NumberOfCores, NumberOfLogicalProcessors
Get-CimInstance Win32_PhysicalMemory | Measure-Object Capacity -Sum
Get-Tpm
```

Ava Tegumihaldur (Ctrl+Shift+Esc) → Jõudlus. Leia CPU, mälu, ketta, võrgu, GPU ja (kui on) NPU graafikud. Kirjelda, milline neist on koormatud, kui avad brauseris videot.

### Iseseisvad ülesanded

**1. Minu arvuti kihid.** Joonista ptk 2 kihilise joonise eeskujul oma arvuti skeem: kasutaja, kolm enimkasutatud rakendust, OS ja riistvara konkreetsete mudelitega (protsessor, mälu maht, ketta tüüp, kas on GPU, NPU, TPM). Andmed leiad `msinfo32` ja Tegumihalduri abil. **Esita:** joonis (käsitsi või digitaalselt) ja ekraanipilt `msinfo32` kokkuvõtte lehest.

**2. Elu ilma operatsioonisüsteemita.** Kujuta ette, et programm peab ise juhtima ketast, võrgukaarti ja ekraani. Kirjelda kolme probleemi, mis tekiksid, kui arvutis töötaks korraga kaks sellist programmi. **Esita:** 5–10 lauset; seosta iga probleem ühe ptk 2.2 tabelis nimetatud OS-i ülesandega.

### Viited

- Krogh (2017), ptk 2.
- Tanenbaum, A. S., Bos, H. (2022). *Modern Operating Systems*, 5. tr, ptk 1. Pearson.
- Stallings, W. (2024). *Operating Systems: Internals and Design Principles*, 10. tr, ptk 2. Pearson.

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 1. Windowsi ajalugu](01-windowsi-ajalugu.md) · [Sisukord](../README.md) · [3. Windowsi põhimõisted →](03-pohimoisted.md)
