[← Sissejuhatus](00-sissejuhatus.md) · [Sisukord](../README.md) · [2. Operatsioonisüsteemi ülesanded →](02-os-i-ulesanded.md)

## 1. Windowsi ajalugu

Windows on arenenud MS-DOS-i graafilisest kestast üheks laiemalt kasutatavaks operatsioonisüsteemiks, mille tänane tuum pärineb 1993. aastal ilmunud Windows NT-st. 2026. aasta sügisel on toetatud kliendiversioon Windows 11 (viimane funktsiooniuuendus 26H2) ja serveriversioon Windows Server 2025.

### 1.1 MS-DOS

IBM-i personaalarvuti (1981) jaoks ostis Microsoft Seattle Computer Productsilt süsteemi 86-DOS, kohandas selle ja nimetas MS-DOS-iks. MS-DOS oli ühe kasutaja ja ühe ülesandega (single-tasking) käsurealõikesüsteem: korraga töötas üks programm, mälu kaitset polnud ning programm võis otse riistvara poole pöörduda. Need piirangud on hea lähtekoht, et mõista, milleks kaasaegne OS mälukaitset ja õigusi vajab.

### 1.2 Esimesed Windowsi versioonid

Windows 1.0 (1985) kuni Windows 3.x olid graafilised kasutajaliidesed, mis töötasid MS-DOS-i peal. Windows 95, 98 ja ME („9x-pere“) tutvustasid Start-menüüd, 32-bitiseid rakendusi ja Plug and Play’d, kuid alus oli endiselt DOS: üks vigane programm võis kogu süsteemi kokku jooksutada.

#### DOS-põhise liini ajajoon

> 🖱️ **Proovi ise:** [interaktiivne ajajoon](https://valdonolvak.github.io/Operatsioonis-steemi-ja-k-berturbe-alused/interaktiivne/01-ajajoon.html#v0) – sama ajajoon klõpsatava versioonina.

![DOS-põhise Windowsi ajajoon · peamised uuendused versioonide kaupa 1985–2000](../pildid/01-b.png)

Joonis: DOS-põhise Windowsi versioonid ja iga versiooni peamised uuendused võrreldes eelmisega. Allpool on samad uuendused täpsemalt lahti kirjutatud.

**1985 · Windows 1.0** – võrreldes puhta MS-DOS-iga:

- **Graafiline kasutajaliides DOS-i peal** – käskude tippimise asemel sai programme avada ja faile hallata ekraanil klõpsates. Windows ise oli DOS-i rakendus: arvuti käivitus DOS-i ja Windows pandi käima käsuga `win`.
- **Hiir ja kõrvuti aknad** – hiirest sai tavaline sisendseade. Aknad jaotati ekraanil kõrvuti (tiled) ega saanud üksteise peale ulatuda, sest tolleaegne graafikariistvara oli nõrk.
- **Koostööpõhine multitegumtöö** (cooperative multitasking) – mitu programmi võis olla korraga avatud, kuid iga programm pidi ise juhtimise teistele loovutama. Kui programm seda ei teinud, jäi kogu süsteem seisma.
- **Ühised seadmedraiverid** – ekraani ja printeri draiver paigaldati üks kord Windowsile ning kõik Windowsi rakendused said seda kasutada. DOS-is pidi iga programm (nt tekstitöötlus) ise iga printerimudelit toetama.
- **Kaasasolevad rakendused** – MS-DOS Executive (failihaldur), Paint, Write, Notepad, Calculator, Calendar ja Cardfile näitasid, milleks graafilist liidest kasutada saab.

*Allikad:* [Wikipedia: Windows 1.0](https://en.wikipedia.org/wiki/Windows_1.0)

**1987 · Windows 2.0 / 2.1** – võrreldes 1.0-ga:

- **Üksteise peale ulatuvad aknad** (overlapping windows) – aknaid sai vabalt paigutada ja üksteise peale tõsta, nagu paberilehti laual. Sellest sai graafiliste kasutajaliideste standard.
- **Minimeerimine ja maksimeerimine** – akna sai peita ikooniks või laiendada üle ekraani. Nii oli mitme programmiga töötamine mugavam.
- **Kiirklahvid** – menüüsid ja aknaid sai juhtida ka klaviatuuriga. Sellest tulid ka kombinatsioonid, mida kasutatakse Windowsis siiani.
- **DDE** (Dynamic Data Exchange) – programmid said omavahel andmeid vahetada, nt Exceli tabeli muutus kajastus automaatselt Wordi dokumendis. See oli esimene samm rakendustevahelise koostöö suunas.
- **Windows/386** – kasutas 386-protsessori virtuaalset 8086-režiimi: iga DOS-programm sai oma „virtuaalse arvuti“ ja mitu DOS-programmi töötasid korraga eraldi akendes. Samal ajal ilmusid ka esimesed Windowsi Word ja Excel.

*Allikad:* [Wikipedia: Windows 2.0](https://en.wikipedia.org/wiki/Windows_2.0) · [Wikipedia: Windows 2.1x](https://en.wikipedia.org/wiki/Windows_2.1x)

**1990 · Windows 3.0, 1992 · Windows 3.1, 1993 · Windows for Workgroups 3.11** – võrreldes 2.x-ga:

- **Kaitstud režiim** (protected mode) – Windows sai kasutada kogu arvuti mälu, mitte ainult DOS-i 640 KB piiri. 386-režiimis lisandus virtuaalmälu (saalefail kettal), nii et korraga sai avada rohkem programme, kui füüsilisse mällu mahtus.
- **Program Manager ja File Manager** – programmid olid grupeeritud ikoonidena ühte aknasse ja failihaldur näitas kaustapuud. Windowsiga oli nüüd lihtne töötada ka arvutit vähem tundval inimesel ning 3.0-st sai esimene suur müügiedu.
- **TrueType fondid** (3.1) – fondid olid skaleeritavad: sama kiri nägi igas suuruses ühtviisi hea välja nii ekraanil kui ka printeril („mida näed, seda saad“, WYSIWYG).
- **OLE ja lohista-ja-kukuta** (3.1) – ühte dokumenti sai põimida teise programmi objekti (nt Exceli tabeli Wordi), mis avanes topeltklõpsuga oma programmis. Faile sai liigutada hiirega lohistades.
- **Võrgutugi** (3.11) – arvutid said töörühmas faile ja printereid jagada ilma eraldi võrgutarkvarata. Nii sai Windowsist ka kontorivõrkude operatsioonisüsteem.

*Allikad:* [Wikipedia: Windows 3.0](https://en.wikipedia.org/wiki/Windows_3.0) · [Wikipedia: Windows 3.1x](https://en.wikipedia.org/wiki/Windows_3.1x)

**1995 · Windows 95** – võrreldes Windows 3.1-ga:

- **Start-menüü, tegumiriba ja töölaud** – kõik programmid leidis Start-nupust ja avatud aknad olid tegumiribal näha. See liidese põhimõte kehtib Windowsis tänini.
- **32-bitised rakendused** (Win32) – uued programmid said kasutada rohkem mälu ja töötasid kiiremini. Eelisjärjestusega multitegumtöös jagab OS ise protsessori aega, nii et üks 32-bitine programm ei saa teisi kinni hoida. Vanad 16-bitised programmid jagasid aga endiselt ühte ühist ruumi.
- **Plug and Play** – OS tuvastas uue seadme (nt helikaardi) ja seadistas selle võimaluse korral ise. Varem pidi kasutaja ise draivereid paigaldama ja IRQ-sid ning muid ressursse käsitsi määrama.
- **Pikad failinimed** – DOS-i 8.3-vormingu (nt `ARUANNE1.DOC`) asemel sai faili nimeks panna kuni 255 märki, nt „Aruanne 2025 lõplik.doc“.
- **Sisseehitatud TCP/IP** – interneti protokollide tugi oli OS-is kaasas ja sissehelistamisega sai võrku ühendada ilma lisatarkvarata. See soodustas interneti levikut koduarvutites.

*Allikad:* [Wikipedia: Windows 95](https://en.wikipedia.org/wiki/Windows_95)

**1998 · Windows 98 / 98 SE** – võrreldes Windows 95-ga:

- **Internet Explorer integreeriti kesta** – brauser oli OS-i osa ning kaustu sai vaadata nagu veebilehti (Active Desktop). Sellest kasvas USA-s Microsofti vastu monopolikohtuasi.
- **Laialdane USB tugi** – hiire, klaviatuuri, printeri ja muud seadmed sai ühendada ühe universaalse pistikuga, ilma arvutit taaskäivitamata.
- **FAT32 failisüsteem** – toetas suuremaid kettaid ja salvestas väikesed failid tõhusamalt (väiksemad klastrid), nii et kettaruumi raisati vähem.
- **Windows Update** – parandusi ja draivereid sai alla laadida Microsofti veebilehelt, ilma uut plaati ostmata. Sellest kasvas välja tänane uuendussüsteem.
- **WDM-draiverid, ACPI ja mitu monitori** – sama draiverimudel töötas nii Windows 98-s kui ka Windows 2000-s. ACPI võimaldas OS-il juhtida toidet (unerežiim, väljalülitus) ja ühe arvutiga sai kasutada mitut ekraani. 98 SE lisas interneti ühiskasutuse: üks arvuti sai jagada internetiühendust teistega.

*Allikad:* [Wikipedia: Windows 98](https://en.wikipedia.org/wiki/Windows_98)

**2000 · Windows ME** – võrreldes Windows 98-ga (viimane DOS-põhine Windows):

- **System Restore** (süsteemitaaste) – süsteem salvestas taastepunkte ja ebaõnnestunud paigalduse järel sai arvuti seadistused varasemasse olekusse tagasi viia, ilma et kasutaja failid kaoksid.
- **Süsteemifailide kaitse** – takistas programmidel olulisi Windowsi faile vanemate versioonidega üle kirjutada. Varem oli see sage põhjus, miks süsteem pärast tarkvara paigaldamist katki läks („DLL-põrgu“).
- **Automaatsed uuendused** – Windows laadis uuendused ise taustal alla ja pakkus neid paigaldada, nii et kasutaja ei pidanud meeles pidama Windows Update'i lehel käimist.
- **DOS-i reaalrežiim peideti** – arvuti käivitus kiiremini, kuid paljud vanad DOS-i programmid ja mängud enam ei töötanud. ME-l oli ka halb stabiilsuse maine, mis kiirendas üleminekut NT-põhisele XP-le.
- **Multimeedia** – Windows Movie Maker (videotöötlus), Windows Media Player 7 ja UPnP (võrguseadmete automaatne leidmine) näitasid, et koduarvutit kasutati üha enam meedia jaoks.

*Allikad:* [Wikipedia: Windows Me](https://en.wikipedia.org/wiki/Windows_Me)

### 1.3 Windows NT

Windows NT (New Technology) loodi nullist, juhiks Dave Cutler. Eesmärgid olid töökindlus, turvalisus, kaasaskantavus eri protsessoriarhitektuuridele ja mitme kasutaja tugi. NT 3.1 ilmus 1993. NT-l on kasutajarežiim ja tuumarežiim, mälukaitse, ACL-ipõhine õiguste süsteem ning failisüsteem NTFS.

![Windowsi kaks arendusliini · DOS-liin lõppes 2000, NT-liin jätkub](../pildid/01-a.png)

Joonis: DOS-põhine liin lõppes Windows ME-ga; Windows XP-st alates kasutavad ka koduarvutid NT-tuuma, millel põhineb ka Windows 11.

### 1.4 NT-põhised Windowsi versioonid

| Aasta | Versioon | Olulisem muutus |
| --- | --- | --- |
| 1993 | Windows NT 3.1 | 32-bitine tuum, mälukaitse, NTFS |
| 1996 | Windows NT 4.0 | Windows 95 kasutajaliides NT-le |
| 2000 | Windows 2000 | Active Directory, grupipoliitika, Plug and Play |
| 2001 | Windows XP | NT-tuum jõudis koduarvutitesse |
| 2007 | Windows Vista | UAC, BitLocker, uus graafikamudel |
| 2009 | Windows 7 | stabiilsus, kiirus |
| 2012 | Windows 8 / 8.1 | puutetundlik liides, UEFI Secure Boot |
| 2015 | Windows 10 | „Windows as a Service“, regulaarsed funktsiooniuuendused |
| 2021 | Windows 11 | kohustuslik TPM 2.0 ja Secure Boot, VBS vaikimisi |
| 2024 | Windows 11 24H2 | Copilot+ arvutid, uus tuumaväljalase |
| 2025 | Windows 11 25H2 | esimene puhas lubamispakett 24H2 peal |
| 2026 | Windows 11 26H2 | iga-aastane funktsiooniuuendus (enablement package) |

#### NT-põhise liini ajajoon ja peamised uuendused

> 🖱️ **Proovi ise:** [interaktiivne ajajoon](https://valdonolvak.github.io/Operatsioonis-steemi-ja-k-berturbe-alused/interaktiivne/01-ajajoon.html). Klõpsa versioonil ja vaata selle uuendusi koos selgitustega. Lehel saad ka versioone õigesse järjekorda panna ja arvata, millises versioonis mingi uuendus tuli.

![NT-põhise Windowsi ajajoon · peamised uuendused versioonide kaupa 1993–2026](../pildid/01-c.png)

Joonis: NT-põhise Windowsi versioonid Windows NT 3.1-st kuni Windows 11 26H2-ni; sinisega on märgitud praegune versioon. Allpool on samad uuendused täpsemalt lahti kirjutatud.

**1993 · Windows NT 3.1** – võrreldes Windows 3.1-ga:

- **Täielikult 32-bitine tuum, mis ei sõltu MS-DOS-ist** – NT on iseseisev operatsioonisüsteem, mis haldab riistvara ise. Kõik selle all töötab NT reeglite järgi, mitte DOS-i piirangutega.
- **Eelisjärjestusega multitegumtöö, lõimed ja SMP** – OS-i ajastaja otsustab, millal iga programm protsessorit kasutab, nii et ükski programm ei saa teisi kinni hoida. Programm võib jaguneda mitmeks lõimeks (thread), mis töötavad paralleelselt. SMP tähendab mitme protsessori samaaegset kasutamist ja seetõttu sobis NT ka serveriks.
- **Mälukaitse** – igal protsessil on oma aadressiruum ja ta ei saa teise protsessi mälu lugeda ega muuta. Kui üks programm jookseb kokku, suletakse see ja muud programmid jätkavad tööd.
- **NTFS ja ACL-ipõhised õigused** – NTFS failisüsteem säilitab iga faili ja kausta juures pääsuloendi (ACL): kes tohib lugeda, muuta või kustutada. Logimine (journaling) aitab ketta pärast voolukatkestust kiiresti korda saada.
- **Turvamudel** – kasutaja peab sisse logima (Ctrl+Alt+Del, mida ükski programm järele teha ei saa), igal kasutajal on oma konto ja õigused ning olulised sündmused kirjutatakse turvalogisse (audit). DOS-is ja Windows 9x-is tõelist kasutajate eristamist polnud.
- **Kaasaskantavus ja HAL** – riistvara abstraktsioonikiht (HAL) peidab protsessori ja emaplaadi erinevused, nii et sama NT töötas x86-, MIPS- ja Alpha-arvutitel. Sama põhimõte võimaldab tänapäeval Windowsit käitada ka ARM-protsessoritel.

*Allikad:* [Wikipedia: Windows NT 3.1](https://en.wikipedia.org/wiki/Windows_NT_3.1) · Yosifovich jt (2017), *Windows Internals*, 7. tr, 1. osa, ptk 1–2

**1996 · Windows NT 4.0** – võrreldes NT 3.51-ga:

- **Windows 95 kasutajaliides** – NT sai Start-menüü, tegumiriba ja Exploreri. Kasutaja jaoks nägi turvaline NT nüüd välja samasugune nagu tuttav Windows 95, mis aitas NT-l ettevõtetes levida.
- **Graafika tuumarežiimi** – graafikaalamsüsteem (GDI) ja ekraanidraiverid viidi kasutajarežiimist tuumarežiimi. Graafika muutus kiiremaks, kuid vigane graafikadraiver võis nüüd kogu süsteemi kokku jooksutada (sinine ekraan). See on näide, kus kiiruse nimel loobuti osaliselt töökindlusest.
- **Task Manager** (tegumihaldur) – sai vaadata töötavaid protsesse, protsessori ja mälu koormust ning hangunud programmi sulgeda. Tegumihaldur on üks administraatori põhitööriistu tänini.
- **IIS ja domeenivõrgud** – serveris oli kaasas IIS veebiserver, nii et NT Serverist sai ka veebiserver. Domeenide abil sai paljude arvutite kasutajakontosid hallata keskselt.
- **Internet Explorer ja DirectX** – brauser oli kaasas, DirectX-i (mängude ja multimeedia liidese) tugi oli aga piiratud. Seetõttu jäi NT 4.0 eelkõige tööarvutite ja serverite süsteemiks ning koduarvutites jätkus Windows 9x.

*Allikad:* [Wikipedia: Windows NT 4.0](https://en.wikipedia.org/wiki/Windows_NT_4.0)

**2000 · Windows 2000** – võrreldes NT 4.0-ga:

- **Active Directory** – kataloogiteenus, kus kasutajad, arvutid, grupid ja printerid on hierarhilises andmebaasis (domeenid, organisatsiooniüksused). NT 4.0 lame domeenimudel ei sobinud suurtele asutustele. AD on tänini enamiku organisatsioonide identiteedihalduse alus.
- **Grupipoliitika** (Group Policy, GPO) – administraator saab ühe reegliga seadistada tuhandeid arvuteid, nt parooli nõuded, tulemüüri, paigaldatava tarkvara ja töölaua piirangud. Varem pidi iga arvutit eraldi seadistama.
- **Kerberos autentimine** – piletipõhine autentimine, kus parooli ei saadeta üle võrgu. Kerberos on turvalisem ja kiirem kui vana NTLM. NTLM jäi ühilduvuse jaoks alles ja sellest loobutakse alles nüüd (vt Windows Server 2025).
- **Plug and Play, USB ja ACPI** – NT-liin sai lõpuks samad riistvara mugavused, mis Windows 98-l. Nii sai NT-põhist süsteemi kasutada ka sülearvutis (toitehaldus, unerežiim) ja koduarvutis.
- **EFS, kettakvoodid ja dünaamilised kettad** – EFS krüpteerib üksikuid faile kasutaja võtmega. Kvoodid piiravad, kui palju kettaruumi kasutaja tohib võtta. Dünaamilised kettad võimaldavad tarkvaralist RAID-i (peegeldamine, triibutamine).
- **Windows Installer** (MSI) – standardne paigaldusvorming, mis oskab paigalduse ebaõnnestumisel muudatused tagasi võtta ja mida saab grupipoliitikaga arvutitesse laiali jagada.

*Allikad:* [Wikipedia: Windows 2000](https://en.wikipedia.org/wiki/Windows_2000) · [Microsoft: Overview of Active Directory Domain Services](https://learn.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) · [Microsoft: Kerberos Authentication Overview](https://learn.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)

**2001 · Windows XP** – võrreldes Windows 2000 ja ME-ga:

- **NT-tuum ka koduarvutites** – XP Home ja Professional ühendasid kaks arendusliini. Ka kodukasutajad said NT stabiilsuse, mälukaitse ja õigused ning DOS-põhine liin lõppes.
- **Luna kasutajaliides ja ClearType** – värvikam, kodukasutajale mõeldud välimus. ClearType muutis teksti LCD-ekraanidel teravamaks, kasutades ekraanipikslite värvilisi alampiksleid.
- **Kiire kasutajavahetus** – mitu kasutajat võis olla korraga sisse logitud ja nende vahel sai vahetada programme sulgemata. See oli võimalik just NT mitme kasutaja toe tõttu.
- **Remote Desktop ja Remote Assistance** – arvutit sai kasutada üle võrgu, nagu istuks selle taga. Abi sai kutsuda sõbra või IT-toe, kes nägi ekraani ja sai aidata.
- **Ühilduvusrežiim** – vanu Windows 9x jaoks tehtud programme sai käivitada nii, et Windows teeskles nende jaoks vanemat versiooni. See oli üleminekul DOS-liinilt NT-le hädavajalik.
- **Tooteaktiveerimine** – litsents seoti arvuti riistvaraga ja aktiveeriti interneti või telefoni teel, et piirata piraatkoopiate kasutamist.
- **SP2 (2004): tulemüür ja Security Center** – pärast suuri ussirünnakuid (Blaster, Sasser) lülitati Windows Firewall vaikimisi sisse ja Security Center hakkas jälgima tulemüüri, uuenduste ning viirusetõrje olekut. Sellest algas Microsofti „vaikimisi turvaline“ mõtteviis.

*Allikad:* [Wikipedia: Windows XP](https://en.wikipedia.org/wiki/Windows_XP) · [Wikipedia: Windows XP SP2](https://en.wikipedia.org/wiki/Windows_XP#Service_Pack_2)

**2007 · Windows Vista** – võrreldes XP-ga:

- **UAC** (User Account Control) – XP-s töötas enamik kasutajaid administraatoriõigustega, nii et ka pahavara sai kõik õigused. Vistas töötab ka administraator vaikimisi tavaõigustega ja süsteemi muutmiseks tuleb kinnitada eraldi aknas. See on vähima õiguse põhimõtte rakendus.
- **BitLocker** – krüpteerib terve süsteemiketta, nii et varastatud sülearvutist ei saa andmeid lugeda. Võtit hoitakse TPM-kiibis.
- **WDDM ja Aero** – uus graafikadraiverite mudel viis suure osa draiverist kasutajarežiimi: kui graafikadraiver jookseb kokku, taaskäivitatakse see ilma sinise ekraanita. Aero kasutas graafikakaarti akende joonistamiseks (läbipaistvus, animatsioonid).
- **ASLR, PatchGuard ja draiverite allkirjastamine** – ASLR paigutab programmid mällu juhuslikult, mis teeb rünnakud raskemaks. PatchGuard keelab tuuma muutmise. 64-bitises Windowsis peavad draiverid olema digitaalselt allkirjastatud, mistõttu pahatahtliku draiveri paigaldamine on raskem.
- **Windows Defender ja uus võrgupinu** – nuhkvaratõrje oli OS-is kaasas. Ümber kirjutatud TCP/IP pinu toetas IPv6 vaikimisi. Kiirotsing indekseeris failid, nii et Start-menüüst leiab faili nime või sisu järgi.

*Allikad:* [Wikipedia: Windows Vista](https://en.wikipedia.org/wiki/Windows_Vista) · [Microsoft: How User Account Control works](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/how-user-account-control-works) · [Microsoft: BitLocker overview](https://learn.microsoft.com/windows/security/operating-system-security/data-protection/bitlocker)

**2009 · Windows 7** – võrreldes Vistaga:

- **Uus tegumiriba ja hüpikloendid** (jump lists) – programme sai tegumiribale kinnitada (pin) ning paremklõpsuga avanesid programmi viimati kasutatud failid. Tegumiriba toimib nii tänini.
- **Aero Snap, Peek ja Shake** – akna lohistamine ekraani servale paigutab selle pool ekraani suuruseks. Peek näitab korraks töölauda ja Shake minimeerib kõik teised aknad. Need lihtsustasid mitme aknaga töötamist.
- **Seadistatav UAC** – Vista UAC küsis kinnitust nii tihti, et kasutajad hakkasid seda pimesi kinnitama või lülitasid selle välja. Windows 7-s sai valida taseme ning tavalised sätete muudatused enam kinnitust ei nõudnud. Hea turve peab olema ka kasutatav.
- **BitLocker To Go** – USB-mälupulga ja välise ketta krüpteerimine. See on oluline, sest kadunud mälupulk on sage andmelekke põhjus.
- **AppLocker ja DirectAccess** – AppLockeriga saab administraator määrata, milliseid programme tohib käivitada (lubatud rakenduste nimekiri). DirectAccess ühendas sülearvuti automaatselt ettevõtte võrguga ilma käsitsi VPN-ita.
- **XP Mode ja jõudlus** – vanu XP rakendusi sai käitada virtuaalses Windows XP-s. Windows 7 kasutas vähem mälu ja töötas samal riistvaral kiiremini kui Vista, mistõttu sellest sai pikaks ajaks ettevõtete standard.

*Allikad:* [Wikipedia: Windows 7](https://en.wikipedia.org/wiki/Windows_7) · [Microsoft: How UAC Works (Windows 7)](https://technet.microsoft.com/en-us/library/dd835548)

**2012 · Windows 8, 2013 · Windows 8.1** – võrreldes Windows 7-ga:

- **Start-ekraan ja puuteliides** – Start-menüü asemel avanes täisekraanil paanidega Start-ekraan, mis oli mõeldud tahvelarvutitele. Lauaarvuti kasutajad pidasid seda ebamugavaks. 8.1 tõi Start-nupu tagasi ja lubas arvutil käivituda otse töölauale.
- **Windows Store ja AppContainer** – rakendused tulid kontrollitud poest ja töötasid liivakastis (AppContainer): rakendus näeb ainult seda, milleks tal on luba (nt kaamera, failid). See on mobiilsete OS-ide turvamudel.
- **UEFI Secure Boot ja ELAM** – Secure Boot kontrollib, et alglaadur ja tuum on digitaalselt allkirjastatud, nii et alglaadimisviirus (bootkit) ei saa käivituda enne OS-i. ELAM laseb viirusetõrjel käivituda enne teisi draivereid.
- **Sisseehitatud viirusetõrje** – Windows Defender sai täisväärtuslikuks viirusetõrjeks (varem oli see eraldi Microsoft Security Essentials). Igal Windowsi arvutil on nüüd vaikimisi pahavarakaitse.
- **Microsofti konto, Hyper-V ja lähtestamine** – Microsofti kontoga sisse logides sünkroonitakse sätted arvutite vahel. Hyper-V võimaldas Pro-väljaandes virtuaalmasinaid käitada. Reset/Refresh taastas Windowsi ilma täieliku uuesti paigaldamiseta. Kiire käivitus (Fast Startup) kasutab talveunerežiimi tehnikat, et arvuti käivituks sekunditega.

*Allikad:* [Wikipedia: Windows 8](https://en.wikipedia.org/wiki/Windows_8) · [Wikipedia: Windows 8.1](https://en.wikipedia.org/wiki/Windows_8.1) · [Microsoft: Secure Boot (Windows 8 / 8.1)](https://learn.microsoft.com/previous-versions/windows/it-pro/windows-8.1-and-8/dn486875(v=ws.11))

**2015 · Windows 10** – võrreldes Windows 8.1-ga:

- **Start-menüü tagasi ja virtuaalsed töölauad** – Start-menüü ühendas tuttava programminimekirja ja Windows 8 paanid. Task View ja virtuaalsed töölauad võimaldasid aknaid eri töölaudadele jagada.
- **„Windows as a Service“** – uut Windowsi ei tule iga paari aasta tagant. Windows 10 uuenes pidevalt: igakuised kumulatiivsed turvauuendused ning regulaarsed funktsiooniuuendused (algul kaks korda aastas). IT-haldurile tähendab see pidevat testimist ja uuenduste juhtimist.
- **Windows Hello** – näo-, sõrmejälje- või PIN-koodiga sisselogimine, kus võti on seotud seadme TPM-kiibiga. Windows Hello for Business asendab ettevõttes parooli tugevama autentimisega.
- **Virtualiseerimispõhine turve** (VBS) – hüperviisor eraldab osa mälust isegi OS-i tuuma eest. Credential Guard hoiab paroolide räsisid ja Kerberose pileteid seal, nii et pahavara ei saa neid varastada (nt pass-the-hash rünnakus).
- **WSL, Edge ja Autopilot** – WSL võimaldab käitada Linuxi käsureatööriistu otse Windowsis. Edge asendas Internet Exploreri. Autopilot ja Intune lasevad uue arvuti otse tehasest kasutajale saata, sest arvuti seadistab end esimesel sisselogimisel ise ettevõtte reeglite järgi.

*Allikad:* [Wikipedia: Windows 10](https://en.wikipedia.org/wiki/Windows_10) · [Microsoft: How Credential Guard works](https://learn.microsoft.com/windows/security/identity-protection/credential-guard/how-it-works) · [Microsoft: What is WSL](https://learn.microsoft.com/windows/wsl/about)

**2021 · Windows 11** – võrreldes Windows 10-ga:

- **Kohustuslik TPM 2.0, Secure Boot ja uuem protsessor** – riistvaraline turve on baasnõue, mitte valikuline lisa. Seetõttu ei saa paljudele vanematele arvutitele Windows 11 ametlikult paigaldada.
- **Mälu terviklikkus (HVCI) ja VBS vaikimisi** – uutel seadmetel on virtualiseerimispõhine turve kohe sees: tuuma saab käivitada ainult allkirjastatud ja kontrollitud koodi, mis muudab draiveripõhised rünnakud raskemaks.
- **Uus kasutajaliides** – tegumiriba ja Start-menüü on ekraani keskel, aknanurgad ümarad ning sätete rakendus ümber kujundatud. Vanad juhtpaneeli osad on järk-järgult Sätetesse viidud.
- **Snap Layouts** – kui hiir on akna maksimeerimisnupul, saab valida valmis paigutuse (nt kolm akent kõrvuti). See on mugav eriti suurtel ekraanidel.
- **Üks funktsiooniuuendus aastas** – Windows 10 kahe uuenduse asemel tuleb üks: Home/Pro saavad igale versioonile 24 kuud tuge, Enterprise/Education 36 kuud. Nii jääb administraatoritel uuenduste planeerimiseks rohkem aega.
- **Muud uuendused** – vidinad (Widgets) ja Androidi rakenduste alamsüsteem (WSA, lõpetati 2025). 22H2 lisas Smart App Controli, mis blokeerib tundmatud ja allkirjastamata programmid, ning vahekaardid File Exploreris.

*Allikad:* [Wikipedia: Windows 11](https://en.wikipedia.org/wiki/Windows_11) · [Microsoft: Windows 11 hardware requirements](https://learn.microsoft.com/en-us/windows/compatibility/windows-11) · [Microsoft: What's new for Windows 11, version 22H2](https://support.microsoft.com/help/5018680)

**2024 · Windows 11 24H2** – võrreldes 23H2-ga:

- **Uus alusplatvorm** – tuum ja süsteemi alus uuendati (nn uus platvormiväljalase). See on 25H2 ja 26H2 ühine põhi, nii et need versioonid saavad tulla väikeste lubamispakettidena.
- **Copilot+ arvutid** – vähemalt 40 TOPS-i jõudlusega tehisintellekti protsessoriga (NPU) arvutid said kohalikud AI-funktsioonid: Recall (ekraani ajaloo otsing), Click to Do, reaalajas tõlgitud subtiitrid ja kaameraefektid. Tehisintellekt töötab seadmes, mitte pilves.
- **Rust tuumas, Sudo ja Wi-Fi 7** – osa tuuma koodist kirjutati ümber Rust-keelde, mis väldib terveid mäluvigade klasse (nt puhvri ületäitumist). Sudo for Windows laseb käsurealt üht käsku tõstetud õigustega käivitada, nagu Linuxis. Wi-Fi 7 toetab uusimat traadita võrgu standardit.
- **Turvalisemad vaikeväärtused** – SMB-allkirjastamine on nõutud (kaitseb failijagamist vahelesegaja rünnakute eest), LSA kaitse hoiab sisselogimisandmeid mälus kaitstuna ja aegunud NTLMv1 eemaldati.
- **Eemaldused ja uued nõuded** – WordPad ja Steps Recorder eemaldati ning VBScript muudeti valikuliseks (seda kasutati sageli pahavaras). Protsessor peab toetama SSE4.2/POPCNT käske, mistõttu väga vanadel protsessoritel 24H2 ei käivitu. Uuendused on väiksemad, sest arvuti laadib alla ainult muutunud osad. Enterprise-väljaandes on võimalik hotpatch ehk turvauuendus ilma taaskäivituseta.

*Allikad:* [Microsoft: Windows 11, version 24H2: What's new for IT pros](https://techcommunity.microsoft.com/blog/windows-itpro-blog/windows-11-version-24h2-what%E2%80%99s-new-for-it-pros/4259108) · [Microsoft: What's new in Windows 11 Enterprise LTSC 2024](https://learn.microsoft.com/en-us/windows/whats-new/ltsc/whats-new-windows-11-2024) · [Wikipedia: Windows 11 version history](https://en.wikipedia.org/wiki/Windows_11_version_history)

**2025 · Windows 11 25H2** – võrreldes 24H2-ga:

- **Lubamispakett** (enablement package) – 25H2 kood oli 24H2 arvutites juba olemas, peidetuna. Uuendus on väike pakett, mis selle sisse lülitab ja versiooninumbri muudab. Paigaldus vajab ainult ühte taaskäivitust, nagu tavaline igakuine uuendus.
- **Funktsioonid tulevad igakuiste uuendustega** – uued võimalused jõuavad arvutisse aasta jooksul järk-järgult ja 25H2 lülitas need kõigile sisse. Funktsiooniuuendus ei ole enam suur muutus, vaid pigem tugiaja pikendus.
- **PowerShell 2.0 ja WMIC eemaldati** – vana PowerShell 2.0 mootorit kasutati ründetes turvapiirangutest mööda hiilimiseks. WMIC on vana käsurea tööriist, mille asemel tuleb nüüd kasutada PowerShelli CIM-käske (nt `Get-CimInstance`). Administraatorid peavad oma vanad skriptid üle vaatama.
- **Eelinstallitud rakenduste eemaldamine poliitikaga** – Enterprise- ja Education-väljaandes saab administraator grupipoliitika või MDM-iga määrata, millised kaasasolevad poerakendused eemaldatakse. Nii on kooli- ja tööarvutid puhtamad.
- **Uus tugiperiood** – uuele versioonile üleminek käivitab tugiaja kella uuesti (Home/Pro 24 kuud, Enterprise/Education 36 kuud). Seepärast tuleb versiooni uuendada ka siis, kui uusi funktsioone pole vaja.

*Allikad:* [Microsoft: An IT pro's guide to Windows 11, version 25H2](https://techcommunity.microsoft.com/blog/windows-itpro-blog/an-it-pro%E2%80%99s-guide-to-windows-11-version-25h2/4457409) · [Windows Central: Windows 11 version 25H2](https://www.windowscentral.com/software-apps/windows-11/windows-11-version-25h2-faq)

**2026 · Windows 11 26H2** – võrreldes 25H2-ga:

- **Väike lubamispakett** – 24H2, 25H2 ja 26H2 on sama koodibaas ja uuenduspakett on x64-arvutil vaid ligikaudu 174 KB. Paigaldus võtab mõne minuti. Erand on 26H1 Arm-arvutid, mis on teisel platvormil ega saa 26H2-te.
- **Start-menüü ja tegumiriba sätted** – Start-menüül on väike ja suur suurus ning jaotisi (kinnitatud, hiljutised, kõik rakendused) saab peita. Tegumiriba saab taas paigutada üles, vasakule või paremale, mida Windows 11 algul ei lubanud.
- **Parem otsing** – otsingutulemustel on eelvaade ja otsing talub kirjavigu. Veebi- ja poesoovitused saab välja lülitada, nii et otsing näitab ainult arvutis olevat.
- **Uued vaikeväärtused tööarvutites** – varem „ajutiste ärikontrollidega“ peidetud funktsioonid (nt sätete varundamine) lülituvad sisse. Ettevõtte arvutites on ajapunktipõhine taastamine (point-in-time restore), Smart App Controlil on rohkem administraatori valikuid ja Windows Hello toetab rohkem sõrmejäljelugejaid.
- **AI ja jälgimine** – tegumihaldur näitab NPU ja tehisintellekti koormust ning Settings Agent laseb sätteid otsida loomulikus keeles (nt „muuda ekraan tumedaks“).
- **Uus tugiperiood** – Home/Pro saavad uuendusi kuni oktoobrini 2028. 24H2 Home/Pro tugi lõppes 13.10.2026, seega tuli neil arvutitel 26H2-le uuendada.

*Allikad:* [Windows Latest: what's new in Windows 11 26H2](https://www.windowslatest.com/2026/10/06/microsoft-reveals-whats-actually-new-in-windows-11-26h2-and-how-to-verify-it/) · [Pureinfotech: Windows 11 26H2 vs 25H2](https://pureinfotech.com/windows-11-26h2-vs-25h2/)

**Windows 10 elutsükli lõpp.** Windows 10 tasuta tugi lõppes 14. oktoobril 2025; laiendatud turvauuenduste (ESU) programm pikendas tarbijate turvauuendusi aasta vörra, kuni 13. oktoobrini 2026 ([Pureinfotech](https://pureinfotech.com/windows-10-end-support-october-14-2025/)). Organisatsioonid saavad ESU-d osta kuni kolmeks aastaks, s.t hiljemalt 2028. aasta oktoobrini ([heise](https://heise.de/-10001284)). See tähendab, et 2026. aasta novembris ei saa tavaline Windows 10 koduarvuti enam turvaparandusi.

**Windows 11 versioonid.** Windows 11 26H2 ilmus 29. septembril 2026 ja jõudis oktoobri lõpuks kõigile sobivatele 24H2 ja 25H2 seadmetele ([Pureinfotech](https://pureinfotech.com/windows-11-26h2-released-force-install/), [Windows Latest](https://www.windowslatest.com/2026/10/27/microsoft-confirms-windows-11-26h2-is-now-available-for-everyone-and-it-installs-in-under-five-minutes/)). Kodu- ja Pro-versiooni tugi kestab kuni 9. oktoobrini 2028, Enterprise- ja Education-versioonil kuni 8. oktoobrini 2029 ([PCWorld](https://www.pcworld.com/article/3270715/windows-11-26h2-is-now-open-to-everyone-dont-expect-big-changes.html)). Erandlik on 26H1: see on mõeldud ainult uutele Snapdragon X2 protsessoriga seadmetele ega ole olemasolevate arvutite uuendus ([Microsoft Tech Community](https://techcommunity.microsoft.com/blog/Windows-ITPro-blog/what-to-know-about-windows-11-version-26h1/4491941)).

**Windows 11 miinimumnõuded** on 64-bitine kahetuumaline 1 GHz protsessor, 4 GB RAM, 64 GB salvestusruumi, TPM 2.0 ja Secure Boot ([Pureinfotech](https://pureinfotech.com/windows-11-25h2-system-requirements/)).

### 1.5 Windows Server

Windows Server kasutab sama NT-tuuma, kuid on häälestatud teenuste pakkumiseks: Active Directory, DNS, DHCP, failiteenused, IIS, Hyper-V. Serveril on pikk tugiperiood (LTSC, umbes 10 aastat), väiksem graafiline koormus (Server Core) ja litsentseerimine tuumade järgi.

Windows Server 2025 (üldkasutuses alates novembrist 2024) uuendused:

- **hotpatching** – turvauuendused paigaldatakse ilma taaskäivituseta; Standard- ja Datacenter-väljaandes Azure Arci kaudu ([Petri](https://petri.com/windows-server-2025-generally-available/));
- **SMB over QUIC** kõigis väljaannetes ja SMB-allkirjastamine vaikimisi nõutud väljuvatel ühendustel;
- uus Active Directory domeeni- ja metsatase, Hyper-V-s vaikimisi 2. põlvkonna virtuaalmasinad, GPU jagamine;
- eemaldatud või aegunud: WordPad, IIS 6 haldusliides, SMTP server, PowerShell 2.0 mootor; aegunuks kuulutatud ka WMIC, NTLM ja WSUS ([Petri](https://petri.com/windows-server-2025-generally-available/)).

### Näide: kooli arvutiklass 2026. aasta sügisel

Koolis on 20 arvutit: 12 Windows 11 24H2, 6 Windows 10 22H2 ja 2 vana masinat, millel peab töötama 1990. aastate DOS-i õppeprogramm.

- **Windows 11 24H2** – uuenda 26H2-le, sest 24H2 Home/Pro tugi lõppes 13.10.2026.
- **Windows 10** – tarbija-ESU lõppes 13.10.2026; arvutid tuleb uuendada Windows 11-le (kui riistvara lubab), viia organisatsiooni ESU alla või välja vahetada. Ilma uuendusteta arvutit ei tohi võrku lubada.
- **DOS-programm** – 64-bitine Windows 16-bitist koodi ei käita (ptk 5.4). Lahendus on emulaator (nt DOSBox) või võrgust eraldatud virtuaalmasin, mitte vana Windows võrgus.

Näide näitab, et OS-i versioon pole ainult mugavuse küsimus: see määrab turvauuendused, riistvaranõuded ja rakenduste ühilduvuse.

### Kokkuvõte

- Tänane Windows põhineb Windows NT tuumal, mitte MS-DOS-il.
- Windows 11 nõuab riistvaralist turvet (TPM 2.0, Secure Boot) ja saab üks kord aastas funktsiooniuuenduse.
- Windows 10 on 2026. aasta sügisest tarbijatele toetuseta; organisatsioonid saavad ESU-ga aega osta.
- Windows Server 2025 toob uuendused ilma taaskäivituseta ja liigub NTLM-ist eemale.

### Kontrollküsimused

> 🧪 **Interaktiivne test:** [kontrolli oma teadmisi](https://valdonolvak.github.io/Operatsioonis-steemi-ja-k-berturbe-alused/interaktiivne/01-test.html). Test koosneb 25 valikvastusega küsimusest. Iga vastuse järel näed selgitust ning lõpus saad valesti vastatud küsimusi korrata.

1. Mis oli MS-DOS-i roll esimestes Windowsi versioonides?
2. Miks arendas Microsoft Windows NT, kuigi Windows 95 oli populaarne?
3. Nimeta kolm erinevust Windows NT ja Windows 9x vahel.
4. Mis vahe on Windows 11 kliendiversioonil ja Windows Serveril?
5. Mida tähendab „Windows as a Service“ ja kuidas see mõjutab IT-haldurit?
6. Mida peab organisatsioon tegema, kui tal on 2026. aastal veel Windows 10 arvuteid?
7. Miks on Windows 11 riistvaranõuded rangemad kui Windows 10 omad?

### Valikküsimused

1. Mis aastal ilmus Windows NT 3.1? a) 1985 b) 1990 c) 1993 d) 1995
2. Millal lõppes Windows 10 tasuta tugi? a) 14.01.2020 b) 14.10.2025 c) 13.10.2026 d) 10.10.2028
3. Milline väide Windows 11 26H1 kohta on õige? a) see on kõigile tasuta funktsiooniuuendus b) see on eelinstallitud vaid valitud uutele seadmetele c) see on serveriversioon d) see on Windows 12 testversioon
4. Mida võimaldab hotpatching Windows Server 2025-s? a) draiverite automaatset vahetamist b) turvauuendusi ilma taaskäivituseta c) virtuaalmasinate kiiret kloonimist d) Active Directory varundamist

### Praktiline ülesanne

Ava PowerShell ja selgita väljundit:

```powershell
Get-ComputerInfo -Property OsName, OsVersion, OsBuildNumber, WindowsVersion, OsArchitecture
winver
```

Leia [Windows release health](https://learn.microsoft.com/windows/release-health/) lehelt, mis kuupäeval lõpeb sinu versiooni tugi.

### Iseseisvad ülesanded

**1. Arvutipargi inventuur.** Kogu kodu, kooli või töökoha vähemalt viie arvuti andmed: Windowsi väljaanne, versioon (nt 24H2), järk (build), protsessor, TPM-i olemasolu. Leia Microsofti elutsükli lehelt iga versiooni toe lõppkuupäev. **Esita:** tabel ja iga arvuti kohta soovitus (jätka, uuenda, vaheta välja) koos ühe lause põhjendusega.

**2. Ajajoon ja põhjendus.** Koosta oma ajajoon ühe Windowsi versiooni kohta, mida oled ise kasutanud (nt Windows 7 või 10): ilmumise aasta, kolm uut võimalust, toe lõpp ja mis sai selle järel kasutajatest. **Esita:** pool lehekülge teksti ja vastus küsimusele „Miks ei saa Microsoft vana versiooni lõputult toetada?“

### Viited

- Krogh, E. (2017). *An Introduction to Windows Operating System*, 2. tr, ptk 1. Bookboon.
- Yosifovich, P., Ionescu, A., Russinovich, M., Solomon, D. (2017). *Windows Internals*, 7. tr, 1. osa, ptk 1. Microsoft Press.
- Allikad lingitud tekstis.

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← Sissejuhatus](00-sissejuhatus.md) · [Sisukord](../README.md) · [2. Operatsioonisüsteemi ülesanded →](02-os-i-ulesanded.md)
