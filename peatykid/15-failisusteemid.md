[← 14. Sisend ja väljund](14-sisend-valjund.md) · [Sisukord](../README.md) · [16. Salvestushaldus →](16-salvestushaldus.md)

## 15. Failisüsteemid Windowsis

**Failisüsteem** (file system) korraldab, kuidas failid ja kaustad kettale salvestatakse, leitakse ja kaitstakse. Windows 11 süsteemiketas on NTFS; välised kandjad on sageli exFAT või FAT32; serverites ja arendajate Dev Drive’il kasutatakse ReFS-i.

> 📊 **Joonis:** salvestuse kihid · ketas, partitsioonitabel, partitsioon, köide, failisüsteem, failid

Joonis: failisüsteem on üks kiht kuuest. Kui „ketas ei avane“, tuleb kindlaks teha, millises kihis viga on – ketas, partitsioon, köide (nt lukus BitLocker) või failisüsteem.

### 15.1 FAT

**FAT** (File Allocation Table) on 1970. aastate lihtne failisüsteem: kettal on tabel, mis näitab iga klastri järgmist klastrit. Variandid FAT12, FAT16 ja FAT32. FAT32 puudused: ühe faili suurus kuni 4 GB, õigusi ja logimist pole, toitekatkestusel võib struktuur rikneda. FAT32 on endiselt UEFI süsteemipartitsiooni (EFI System Partition) failisüsteem.

**exFAT** (2006) on FAT-i edasiarendus mälukaartidele ja USB-pulkadele: suured failid, suured kettad, kerge struktuur, töötab Windowsis, macOS-is, Linuxis ja telefonides. Õigusi ja logimist ei ole.

### 15.2 NTFS

**NTFS** (NT File System, 1993) on Windowsi põhifailisüsteem. Põhiomadused:

- **MFT** (Master File Table) – iga fail ja kaust on MFT kirje; väikesed failid mahuvad otse kirjesse;
- **logimine** (journaling) – metaandmete muudatused kirjutatakse logisse `$LogFile`; krahhi järel taastatakse ühtlane olek;
- **õigused** – iga faili ja kausta kohta ACL (ptk 18);
- **tihendamine** ja **EFS-krüpteerimine** failide kaupa;
- **kettakvoodid** kasutajate kaupa;
- **kõvaviited, sümbolviited, ühenduspunktid** (junctions);
- **alternatiivsed andmevood** (ADS) – näiteks `Zone.Identifier` vood märgivad internetist alla laaditud faile („Mark of the Web“), mille alusel SmartScreen ja Office hoiatavad;
- **muudatuste päevik** (USN Journal) – varundus- ja indekseerimisprogrammid saavad teada, mis muutus;
- **varikoopiad** (Volume Shadow Copy) – hetkeseisud varunduseks ja „Eelmised versioonid“.

> 📊 **Joonis:** NTFS-i MFT · väikese ja suure faili kirje

Joonis: iga fail on MFT kirje, mis koosneb atribuutidest. Väikese faili sisu ($DATA) on kirje sees; suure faili $DATA sisaldab ainult jooksude loendit ehk viiteid kettal olevatele klastritele.

> 📊 **Joonis:** NTFS-i logimine · toitekatkestus ja taastamine

Joonis: NTFS kirjutab kavatsuse enne muudatust logisse. Pärast katkestust loeb ta logi ja kas tühistab pooleli jäänud tehingu või viib selle lõpuni – seetõttu ei pea NTFS-i puhul pärast iga krahhi `chkdsk`-i tegema, nagu FAT-i puhul.

### 15.3 ReFS ja Dev Drive

**ReFS** (Resilient File System, Windows Server 2012) on loodud suurte andmemahtude ja töökindluse jaoks:

- **terviklikkusvood** (integrity streams) – kontrollsummad tuvastavad vaikse andmeriknemise; Storage Spaces peegliga parandatakse viga automaatselt;
- **kirjutamisel kopeerimine** metaandmetele – logi pole vaja;
- **plokkide kloonimine** – suure faili koopia tehakse hetkega, viidates samadele plokkidele (kasulik Hyper-V ja varunduse jaoks);
- Windows Server 2025-s **dedubleerimine ja tihendamine** kõigis väljaannetes ([HKU AI Hub / Microsoft](https://aihub.hkuspace.hku.hk/2024/11/04/windows-server-2025-now-generally-available-with-advanced-security-improved-performance-and-cloud-agility/)).

> 📊 **Joonis:** ReFS · plokkide kloonimine ja kirjutamisel kopeerimine

Joonis: 100 GB virtuaalketta koopia tekib ReFS-il sekundiga, sest koopia viitab samadele plokkidele. Kui koopiat muudetakse, kirjutatakse muudetud plokk uude kohta ja algne fail jääb puutumata. Sama põhimõtet kasutavad ka Linuxi Btrfs ja macOS-i APFS.

ReFS-il puuduvad mõned NTFS-i omadused (nt EFS, kettakvoodid), ja Windowsi kliendis ei saa sellelt käivitada.

**Dev Drive** on Windows 11 arendajatele mõeldud ReFS-köide, millel Microsoft Defender töötab **jõudlusrežiimis**: reaalajakontroll toimub asünkroonselt. Microsofti andmetel kiirendab see ehitusaegu kuni 30% ([Windows Developer Blog](https://blogs.windows.com/windowsdeveloper/2023/06/01/dev-drive-performance-security-and-control-for-developers/)). Jõudlusrežiim töötab ainult Dev Drive’il, mitte NTFS-süsteemikettal ([Microsoft Learn](https://learn.microsoft.com/defender-endpoint/microsoft-defender-endpoint-antivirus-performance-mode)). Turvalisuse mõttes on see kompromiss: Dev Drive’ile ei panda allalaadimisi ega kahtlase päritoluga faile.

### 15.4 SSD-ketaste tugi

SSD-l puuduvad liikuvad osad ning pöördumisaeg ei sõltu andmete asukohast, seega pole killustatuse eemaldamist vaja. Windows tuvastab SSD automaatselt ja:

- saadab **TRIM**-käske (NVMe-l *Deallocate*): failisüsteem teatab, millised plokid on vabad, et SSD saaks neid ette kustutada;
- teeb ajastatud optimeerimisel (`Optimize-Volume`) ainult TRIM-i, mitte killustatuse eemaldamist;
- kasutab NVMe mitme järjekorra tuge (ptk 5, 14).

### 15.5 Optiliste ketaste ja tõmmisfailide failisüsteemid

CD-de failisüsteem on **ISO 9660** (laiendustega Joliet pikkade nimede jaoks), DVD-del ja Blu-ray’del **UDF** (Universal Disk Format). Optilisi seadmeid kasutatakse harva, kuid samad vormingud elavad edasi **ISO-tõmmistes**: Windowsi paigaldustõmmis on ISO-fail, mille saab Exploreris topeltklõpsuga ühendada virtuaalseks kettaks. Virtuaalmasinate kettatõmmised on **VHD/VHDX** failid, mida saab samuti ühendada (`Mount-VHD`, `Mount-DiskImage`).

ISO- ja VHD-failid ei pruugi kanda „Mark of the Web“ märget sisu failidele edasi, mistõttu kasutati neid 2022–2023 pahavara levitamiseks; Microsoft on seda vahepeal parandanud, kuid e-kirjaga tulnud tõmmisfaile tuleb endiselt kahtlustada.

### 15.6 Pilvefailid

OneDrive kasutab **Cloud Files API**-t: failid on kettal kohtäitjatena (placeholders) ja laaditakse alla avamisel. Failisüsteemi filtridraiver `cldflt.sys` vahendab päringuid. Kasutaja näeb olekuikoone: ainult pilves, lokaalselt saadaval, alati selles seadmes.

| Failisüsteem | Kus kasutatakse | Õigused | Logimine | Märkus |
| --- | --- | --- | --- | --- |
| FAT32 | EFI-partitsioon, vanad kandjad | ei | ei | fail kuni 4 GB |
| exFAT | mälukaardid, USB | ei | ei | platvormiülene |
| NTFS | Windowsi süsteemiketas | jah | jah | kõige rohkem funktsioone |
| ReFS | serverid, Dev Drive | jah | kirjutamisel kopeerimine | terviklikkusvood, plokkide kloonimine |
| UDF / ISO 9660 | DVD, ISO-tõmmised | ei | ei | ainult lugemiseks tõmmistes |

> 📊 **Joonis:** failisüsteemi valik · kuus tavalist olukorda

Joonis: süsteemikettale NTFS, eemaldatavale kandjale exFAT, EFI-partitsioonile FAT32 ja suurtele serveriköidetele ReFS.

### Võrdlus: Linux ja macOS

Windows jätab failisüsteemid ketta tähtedena (`C:`, `D:`) eraldi. Linux ja macOS ühendavad kõik köited ühte puusse, mille juur on `/`. Uuemad failisüsteemid (APFS, Btrfs, ReFS) kasutavad kirjutamisel kopeerimist.

|  | Windows | Linux | macOS |
| --- | --- | --- | --- |
| Põhifailisüsteem | NTFS | ext4 (ka XFS, Btrfs) | APFS |
| Töökindlus | metaandmete logi | ext4: logi; Btrfs: kirjutamisel kopeerimine | kirjutamisel kopeerimine |
| Hetktõmmised | varikoopiad (VSS) | LVM- või Btrfs-hetktõmmised | APFS-hetktõmmised (Time Machine) |
| Failide kloonimine | ReFS-i plokkide kloonimine | reflink (Btrfs, XFS) | APFS-i kloonid |
| Krüpteerimine | BitLocker, EFS | LUKS / dm-crypt, fscrypt | FileVault, APFS-i omakeelne krüpteerimine |
| Õigused | ACL | `rwx` + POSIX ACL | `rwx` + ACL |
| Suur- ja väiketähed | ei erista (vaikimisi) | eristab: `Fail.txt` ≠ `fail.txt` | ei erista (vaikimisi) |
| Köidete paiknemine | ketta tähed | üks puu, nt `/home`, `/mnt/usb` | üks puu, välised kettad `/Volumes` |
| Ühilduvus | loeb ja kirjutab exFAT-i ja FAT32 | loeb ja kirjutab NTFS-i (ntfs3), exFAT-i, FAT32 | loeb NTFS-i, kirjutab exFAT-i ja FAT32 |

**Mida sellest õppida:** õppevahendeid jagades sobivad exFAT ja FAT32, sest kõik kolm OS-i nii loevad kui ka kirjutavad neid vaikimisi; suurte failide tõttu on exFAT parem valik. Linuxi serverisse faile kopeerides tuleb arvestada suur- ja väiketähtede eristamisega – Windowsis töötav veebilehe link `Pilt.JPG` võib Linuxis viia veani 404, kui fail on `pilt.jpg`.

### Näide: 6 GB video ja e-kirjaga tulnud arve

**Probleem 1.** Õpetaja kopeerib 6 GB tunnisalvestust USB-pulgale ja saab teate „Fail on sihtfailisüsteemi jaoks liiga suur“, kuigi pulgal on 20 GB vaba. Põhjus: pulk on vormindatud FAT32-ga, mille faili suurus on piiratud 4 GB-ga. Lahendus: varunda pulga sisu ja vorminda see exFAT-iks (`Format-Volume -DriveLetter E -FileSystem exFAT`) – siis loevad seda ka teleri ja Maci.

**Probleem 2.** Raamatupidaja laadis e-kirjast alla faili `arve.docm`. Faili avamisel kuvab Word kollase riba „KAITSTUD VAADE“ ja makrod on blokeeritud. Miks Word teab, et fail tuli internetist?

```powershell
Get-Content .\arve.docm -Stream Zone.Identifier
# [ZoneTransfer]
# ZoneId=3            <- 3 = internet
# HostUrl=https://…
```

Brauser kirjutas faili juurde NTFS-i **alternatiivse andmevoo** `Zone.Identifier` („Mark of the Web“). Kui sama fail kopeerida FAT32-pulgale, kaob andmevoog, sest FAT32 seda ei toeta – üks põhjus, miks failisüsteemi valik mõjutab ka turvalisust.

### Kokkuvõte

- NTFS on Windowsi põhifailisüsteem: MFT, logi, ACL-id, ADS, varikoopiad.
- ReFS keskendub terviklikkusele ja suurtele mahtudele; Dev Drive kasutab ReFS-i arendajate kiirendamiseks.
- SSD vajab TRIM-i, mitte killustatuse eemaldamist.

### Kontrollküsimused

1. Mis on FAT32 peamised piirangud?
2. Miks kasutatakse USB-pulkadel sageli exFAT-i?
3. Nimeta viis NTFS-i omadust.
4. Mis on MFT?
5. Mis on alternatiivne andmevoog ja kuidas seda kasutab „Mark of the Web“?
6. Mis vahe on NTFS-il ja ReFS-il?
7. Mis on Dev Drive ja milline turvakompromiss sellega kaasneb?
8. Miks ei tehta SSD-l killustatuse eemaldamist?
9. Mis on ISO-tõmmis ja VHDX-fail?

### Valikküsimused

1. Mis on FAT32-l ühe faili maksimumsuurus? a) 2 GB b) 4 GB c) 16 GB d) 2 TB
2. Millises NTFS-i struktuuris on kirje iga faili kohta? a) FAT b) MFT c) USN d) VSS
3. Mis on ReFS-i terviklikkusvoogude eesmärk? a) krüpteerimine b) vaikse andmeriknemise tuvastamine c) tihendamine d) kvootide jälgimine
4. Mida saadab Windows SSD-le vabade plokkide kohta? a) SMART b) TRIM c) DMA d) IRP\_MJ\_FLUSH

### Praktiline ülesanne

```powershell
Get-Volume | Select-Object DriveLetter, FileSystem, FileSystemLabel, Size, SizeRemaining
fsutil fsinfo ntfsinfo C:
fsutil behavior query DisableDeleteNotify   # 0 = TRIM sees
Get-Item $env:USERPROFILE\Downloads\*.* -Stream Zone.Identifier -ErrorAction SilentlyContinue | Select-Object -First 5 FileName, Stream
Get-Content "$env:USERPROFILE\Downloads\<mõni_fail>" -Stream Zone.Identifier
```

Loo virtuaalmasinas Dev Drive (Seaded → Süsteem → Salvestus → Täpsemad salvestussätted → Kettad ja köited) ja kontrolli `fsutil devdrv query D:`. Laadi alla Windows 11 ISO-tõmmis, ühenda see ja vaata köite failisüsteemi.

### Iseseisvad ülesanded

**1. Failisüsteemide võrdlus katses.** Loo õppe-VM-is kolm 8 GB virtuaalketast ja vorminda need FAT32, exFAT ja NTFS failisüsteemiga (Windows ei luba üle 32 GB FAT32 köidet tavatööriistaga vormindada). Proovi igale kopeerida 5 GB fail, määrata kaustale õigusi (vahekaart Turve) ja lisada failile alternatiivne andmevoog (`Set-Content fail.txt -Stream Salajane -Value 'test'`). **Esita:** tabel „funktsioon – FAT32 – exFAT – NTFS“ tulemustega.

**2. Varikoopiad ja eelmised versioonid.** Lülita õppe-VM-is köitele sisse süsteemikaitse, loo taastepunkt (`Checkpoint-Computer -Description Test`), muuda dokumenti ja taasta selle eelmine versioon (paremklõps → Atribuudid → Eelmised versioonid). Kontrolli `vssadmin list shadows`. **Esita:** ekraanipildid ja vastus: miks kustutab lunavara tavaliselt esimese asjana varikoopiad ja miks ei asenda varikoopia varundust?

### Viited

- Krogh (2017), ptk 15.
- Yosifovich jt, *Windows Internals* 7. tr, 2. osa, ptk 11.
- Microsoft Learn: [Resilient File System overview](https://learn.microsoft.com/windows-server/storage/refs/refs-overview); [Set up a Dev Drive](https://learn.microsoft.com/windows/dev-drive/).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 14. Sisend ja väljund](14-sisend-valjund.md) · [Sisukord](../README.md) · [16. Salvestushaldus →](16-salvestushaldus.md)
