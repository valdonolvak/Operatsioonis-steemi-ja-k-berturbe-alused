[← 13. Vahemälu](13-vahemalu.md) · [Sisukord](../README.md) · [15. Failisüsteemid Windowsis →](15-failisusteemid.md)

## 14. Sisend ja väljund

Sisend-väljund (S/V, input/output, I/O) on OS-i kõige mitmekesisem osa: seadmed erinevad kiiruse, andmeedastuse viisi ja vigade poolest miljoneid kordi. OS peidab erinevused draiverite ja ühtse S/V-mudeli taha. Windowsis käib iga S/V päring **IRP**-na (I/O Request Packet) läbi draiverite pinu.

### 14.1 S/V probleemid

- **kiiruste erinevus** – klaviatuur annab mõned baidid sekundis, NVMe-SSD üle 10 GB/s;
- **erinevad andmesüksused** – plokid, märgid, paketid;
- **vead** – ajutised ja püsivad;
- **jagamine** – mõnda seadet saab kasutada korraga üks protsess (printer), teist paljud (ketas);
- **turve** – rakendus ei tohi seadet otse juhtida.

### 14.2 Plokkseadmed ja märkseadmed

- **Plokkseade** (block device) salvestab andmeid kindla suurusega plokkidena, mida saab lugeda suvalises järjekorras: SSD, HDD, USB-mälupulk.
- **Märkseade** (character device) edastab märkide voogu ilma aadressideta: klaviatuur, hiir, jadaport.
- **Võrguseade** käsitleb pakette ja moodustab omaette klassi.

### 14.3 Andmevood

Programmi jaoks on fail, seade või võrguühendus **voog** (stream) – baidijada, mida loetakse ja kirjutatakse samade funktsioonidega (`ReadFile`, `WriteFile`). Igal protsessil on standardsed vood: sisend, väljund ja vead. PowerShellis liiguvad konveieris (pipeline) objektid, mitte baidid.

### 14.4 S/V vigade käsitlemine

Vead tuleb lahendada võimalikult seadme lähedal: kontroller kordab lugemist, draiver proovib uuesti, failisüsteem kasutab varukoopiat (ReFS-i peegel) või logi, ja alles siis antakse viga rakendusele. Püsivatest vigadest annavad märku SMART-andmed ja sündmuslogi (`Disk`, `Ntfs`, `stornvme`).

### 14.5 Sünkroonne ja asünkroonne S/V

- **Sünkroonne** – lõim ootab, kuni operatsioon lõpeb; lihtne programmeerida.
- **Asünkroonne** – lõim annab päringu üle ja jätkab tööd; teade lõpust tuleb sündmuse, tagasikutse või **S/V lõpetamispordi** (I/O completion port, IOCP) kaudu. IOCP on Windowsi suure jõudlusega serverite (IIS, SQL Server) alus.
- **I/O Ring** (Windows 11) – uus API, kus rakendus paneb päringud jagatud mälus järjekorda ja saab tulemused samast kohast; vähem süsteemikutseid. Sarnane Linuxi io\_uringiga.

### 14.6 Puhvri kasutamine

**Puhver** (buffer) on mälupiirkond, kus andmeid hoitakse seadme ja programmi vahel. Puhver tasandab kiiruste erinevusi (klaviatuuripuhver, võrgukaardi järjekord), võimaldab koguda väikesed kirjutused suuremaks ja lubab programmil jätkata enne, kui seade on valmis. **Topeltpuhver** – üht täidetakse, teist tühjendatakse.

### 14.7 Küsitlus

**Küsitlus** (polling) tähendab, et protsessor kontrollib tsüklis seadme olekuregistrit. See raiskab protsessoriaega aeglaste seadmete puhul, kuid on väga kiirete seadmete puhul mõnikord parem kui katkestus, sest väldib katkestuse kulu. Näiteks võrgukaardid lülituvad suure koormuse all katkestustelt küsitlusele.

### 14.8 Katkestused

Seade annab katkestusega teada, et operatsioon on lõppenud või andmed on saabunud (ptk 5). Kaasaegsed seadmed kasutavad MSI-X katkestusi ja katkestuste koondamist (interrupt moderation), et mitte iga paketi kohta katkestada.

### 14.9 Otsemälupöördus

**DMA** (Direct Memory Access) laseb seadmel kanda andmeid otse mällu ilma protsessorita; protsessor saab katkestuse alles lõpus. Kuna DMA-seade saab kirjutada mis tahes mäluaadressile, on see turvarisk (nt pahatahtlik Thunderbolt-seade). Kaitse: **IOMMU** ja Windowsi **Kernel DMA Protection**, mis lubab välisseadmele ainult talle määratud mälupiirkonnad.

### 14.10 S/V-seadmete draiverid

Draiverid on kihtides:

- **funktsioonidraiver** – põhiline seadme juhtija (nt `stornvme.sys`);
- **siini draiver** – tuvastab siinil seadmed (PCI, USB);
- **filtridraiverid** – asuvad teiste vahel ja muudavad või jälgivad päringuid (viirusetõrje failifilter, BitLocker).

Windows Update levitab draivereid tootjate eest. Alates 2024. aastast on Microsofti draiveriprogrammis rangemad nõuded ning soovitus viia draiverid kasutajarežiimi (UMDF) või kirjutada mäluturvalises keeles.

### 14.11 S/V Windowsis

Tavaline faili lugemine:

1. Rakendus kutsub `ReadFile`.
2. `ntdll.dll` teeb süsteemikutse `NtReadFile`.
3. S/V-haldur loob IRP ja saadab selle failisüsteemi draiverile (NTFS).
4. Kui andmed on vahemälus, kopeeritakse need sealt; muidu saadab NTFS päringu edasi helitugevushaldurile ja kettadraiverile.
5. Kettadraiver programmeerib kontrolleri, kontroller teeb DMA ja annab katkestuse.
6. ISR → DPC → IRP lõpetatakse ja tulemus jõuab rakenduseni.

> 📊 **Joonis:** faili lugemine · IRP draiverite pinus, vahemälu otsetee ja lõpetamine

Joonis: kui andmed on vahemälus, lõpeb päring kohe; muidu liigub IRP draiverite pinus alla seadmeni ning lõpetatakse katkestuse ja DPC kaudu.

### 14.12 S/V-haldur

**S/V-haldur** (I/O Manager) loob ja suunab IRP-sid, haldab draiverite pinu, tagab ühtse liidese kõigile seadmetele ja failisüsteemidele ning toetab asünkroonset S/V-d ja tühistamist.

### 14.13 Plug and Play haldur

**PnP-haldur** tuvastab seadmed, laadib nende draiverid, jagab ressursse (katkestused, mälualad) ja teavitab rakendusi seadmete lisamisest või eemaldamisest. Seadmed tuvastatakse riistvara ID-de järgi (nt `PCI\VEN_8086&DEV_…`). Seadmete paigaldamist saab ettevõttes piirata rühmapoliitikaga (nt USB-salvestite keelamine).

### 14.14 Toitehaldur

**Toitehaldur** (Power Manager) koordineerib süsteemi ja seadmete toiteolekuid (ACPI). Süsteemiolekud: S0 töötab, S3 unerežiim, S4 talveunerežiim, S5 väljalülitatud. Kaasaegsed sülearvutid kasutavad **Modern Standby**-d (S0 madala energiaga ooteolek): arvuti on „nnutelefoni moodi“ võrgus ja uuendab e-kirju ka ooterežiimis. Seadmetel on olekud D0–D3. **Kiire käivitus** (Fast Startup) on hübriid väljalülitusest ja talveunest: tuumaseanss salvestatakse `hiberfil.sys`-i.

### Näide: USB-pulk arvutiklassis

**Mis juhtub, kui õpilane paneb arvutisse USB-pulga:**

1. USB-kontroller tuvastab uue seadme ja annab katkestuse.
2. **PnP-haldur** küsib seadmelt riistvara-ID (`USB\VID_0781&PID_5581`) ja leiab sobiva draiveri (`usbstor.sys`).
3. Draiver laaditakse, seade saab nime, partitsioonid tuvastatakse ja exFAT-köide ühendatakse tähena `E:`.
4. PnP-haldur teavitab rakendusi; Explorer avab akna ja Defender skannib köite.
5. Pulga eemaldamisel saadetakse IRP-d, mis sulgevad failid; kuna eemaldatavatel ketastel on kiire eemaldamine (vahemälu väljas), ei kao andmed ka ilma „turvalise eemaldamiseta“, kui faili parajasti ei kirjutata.

**Kooli poliitika.** IT-juht soovib, et õpilased saaksid pulgalt lugeda, kuid mitte kirjutada (andmelekete vältimiseks). Rühmapoliitika *Removable Disks: Deny write access* paneb Windowsi keelduma kirjutamis-IRP-dest. Kirjutamiskatse annab kasutajale veateate „Juurdepääs keelatud“; lugemine töötab.

### Kokkuvõte

- S/V on mitmekesine; OS ühtlustab selle IRP-de ja draiveripinude abil.
- Asünkroonne S/V (IOCP, I/O Ring) on suure jõudluse eeldus.
- DMA on kiire, kuid vajab IOMMU kaitset.
- PnP- ja toitehaldur haldavad seadmete elutööd ja energiat.

### Kontrollküsimused

1. Milliseid probleeme tekitab S/V operatsioonisüsteemile?
2. Mis vahe on plokk- ja märkseadmel?
3. Mis vahe on sünkroonsel ja asünkroonsel S/V-l?
4. Milleks on puhver?
5. Võrdle küsitlust ja katkestusi. Millal on küsitlus parem?
6. Mis on DMA ja millist turvariski see tekitab?
7. Kirjelda faili lugemise teekonda rakendusest kettani.
8. Mis on S/V-, PnP- ja toitehalduri ülesanded?
9. Mis on Modern Standby ja Fast Startup?

### Valikküsimused

1. Mis on IRP? a) katkestuse prioriteet b) S/V päringupakett c) draiveri allkiri d) failisüsteem
2. Milline seade on plokkseade? a) hiir b) klaviatuur c) SSD d) jadaport
3. Mis kaitseb pahatahtliku DMA eest? a) UAC b) IOMMU / Kernel DMA Protection c) ASLR d) SmartScreen
4. Mis on S4? a) tööolek b) unerežiim c) talveunerežiim d) Modern Standby

### Praktiline ülesanne

```powershell
Get-PnpDevice -PresentOnly | Group-Object Class | Sort-Object Count -Descending | Select-Object -First 10 Name, Count
Get-PnpDevice -Class DiskDrive | Get-PnpDeviceProperty DEVPKEY_Device_DriverVersion
powercfg /a
powercfg /energy /output $env:TEMP\energia.html
```

Ava seadmehaldur → Vaade → „Seadmed ühenduste järgi“ ja leia oma SSD teekond PCI siinilt kettani. Ava `energia.html` ja nimeta kaks probleemi, mida aruanne leidis. Kontrolli `msinfo32` alt, kas „Kernel DMA Protection“ on sees.

### Iseseisvad ülesanded

**1. Seadme teekond.** Vali oma arvutist üks seade (nt võrgukaart, kaamera või SSD). Leia seadmehaldurist selle riistvara-ID, draiveri fail(id), draiveri versioon ja kuupäev ning seadme vanem-seade vaates „Seadmed ühenduste järgi“. **Esita:** ekraanipildid ja skeem: siin → kontroller → seade → draiver.

**2. Toite- ja seadmeprobleemide aruanne.** Käivita administraatorina `powercfg /energy` ja `powercfg /sleepstudy` (Modern Standby arvutis) või `powercfg /batteryreport`. Leia aruandest kolm seadet või rakendust, mis kulutavad energiat või takistavad unerežiimi. **Esita:** loetelu ja iga kohta soovitus, mida kasutaja või administraator saaks teha.

### Viited

- Krogh (2017), ptk 14.
- Yosifovich jt, *Windows Internals* 7. tr, 1. osa, ptk 6 „I/O system“.
- Microsoft Learn: [Kernel DMA Protection](https://learn.microsoft.com/windows/security/hardware-security/kernel-dma-protection-for-thunderbolt); [Modern Standby](https://learn.microsoft.com/windows-hardware/design/device-experiences/modern-standby).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 13. Vahemälu](13-vahemalu.md) · [Sisukord](../README.md) · [15. Failisüsteemid Windowsis →](15-failisusteemid.md)
