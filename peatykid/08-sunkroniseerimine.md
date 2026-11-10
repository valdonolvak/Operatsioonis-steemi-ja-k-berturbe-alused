[← 7. Protsessid, lõimed ja tööd](07-protsessid-ja-loimed.md) · [Sisukord](../README.md) · [9. Protsesside planeerimine ühel protsessoril →](09-planeerimine-uhel-protsessoril.md)

## 8. Lõimede sünkroniseerimine

Peatükis 7 nägime, et sama protsessi lõimed jagavad mälu. See on mugav, sest andmeid ei pea kopeerima, kuid ka ohtlik. Kui mitu lõime kasutab samu andmeid ja keegi nende tööd ei kooskõlasta, tekivad **võidujooksud** (race condition) ja andmed rikutakse. Selliseid vigu on raske leida, sest need ilmnevad ainult siis, kui lõimed juhtuvad töötama just „valel“ hetkel. Programm võib testides tuhat korda õigesti töötada ja tuhande esimesel korral kliendi raha kaotada.

**Sünkroniseerimise** eesmärk on tagada kaks asja:

1. **vastastikune välistamine** – jagatud andmetega töötab korraga ainult üks lõim;
2. **õige järjekord** – lõimed teevad oma töid kokkulepitud järjekorras (nt tarbija ei võta andmeid enne, kui tootja need valmis teeb).

> 🖱️ **Proovi ise:** [interaktiivne õpileht](https://valdonolvak.github.io/Operatsioonis-steemi-ja-k-berturbe-alused/interaktiivne/08-sunkroniseerimine.html). Lehel on võidujooks samm-sammult, semafor parklas, tootja–tarbija puhver, mutex ja mehhanismi valimise harjutus.

### 8.1 Koostöötavad lõimed

Lõimed teevad sageli **koostööd**: üks toodab andmeid, teine tarbib. Seda nimetatakse **tootja–tarbija probleemiks** (producer–consumer). Lõimede vahel on piiratud suurusega **puhver** (järjekord).

**Näide: video voogedastus.** Kui vaatad YouTube’i videot, töötab brauseris vähemalt kaks lõime:

- **tootja** laadib võrgust videolõike ja paneb need puhvrisse;
- **tarbija** võtab puhvrist lõike ja näitab neid ekraanil.

Kui võrk on aeglane, saab puhver tühjaks ja tarbija peab ootama: video jääb „laadima“. Kui võrk on kiire, saab puhver täis ja tootja peab ootama, et mälu ei saaks otsa.

Ilma sünkroniseerimiseta tekiks kaks viga:

- tarbija loeb tühjast puhvrist ja näitab prügi või jookseb kokku;
- tootja kirjutab täis puhvrisse ja kirjutab üle lõigu, mida pole veel näidatud.

Samamoodi töötavad printeri järjekord (rakendused toodavad, spuuler tarbib) ja võrgukaardi draiver (katkestus toodab pakette, lõim tarbib).

### 8.2 Konkureerivad lõimed

Lõimed võivad ka **konkureerida** sama ressursi pärast: muutuja, fail, mälupiirkond, printer. Klassikaline näide on kaks lõime, mis suurendavad sama loendurit.

Tehe `x = x + 1` näeb välja nagu üks samm, kuid protsessor teeb selle **kolmes sammus**:

1. **loe** – loe `x` mälust registrisse;
2. **liida** – liida registrile 1;
3. **kirjuta** – kirjuta register tagasi mällu.

Kui planeerija vahetab lõimi nende sammude vahel, läheb üks suurendus kaduma. Olgu alguses `x = 5`:

| Aeg | Lõim A | Lõim B | x mälus |
| --- | --- | --- | --- |
| 1 | loeb x → registris 5 | | 5 |
| 2 | | loeb x → registris 5 | 5 |
| 3 | liidab → 6 | | 5 |
| 4 | | liidab → 6 | 5 |
| 5 | kirjutab 6 | | 6 |
| 6 | | kirjutab 6 | **6** |

Mõlemad lõimed suurendasid, kuid tulemus on 6, mitte 7. Üks suurendus läks kaduma, sest B luges vana väärtuse enne, kui A jõudis uue kirjutada.

![võidujooks · kaks lõime suurendavad sama muutujat, aeg kulgeb alla](../pildid/08-a.png)

Joonis: aeg kulgeb ülalt alla. Vasakul loevad mõlemad lõimed vana väärtuse 5 ja kirjutavad 6; paremal laseb lukk lõimel B alustada alles siis, kui A on lõpetanud.

> 🖱️ **Proovi ise:** [võidujooks samm-sammult](https://valdonolvak.github.io/Operatsioonis-steemi-ja-k-berturbe-alused/interaktiivne/08-sunkroniseerimine.html#voidujooks). Otsusta ise, kumb lõim järgmise sammu teeb, ja vaata, millal suurendus kaduma läheb.

**Näide: pangakonto.** Kontol on 100 €. Mari maksab poes kaardiga 30 € ja samal ajal laekub palk 1000 €. Mõlemad tehingud loevad saldo 100. Esimene kirjutab 70, teine 1100. Olenevalt sellest, kumb kirjutas viimasena, on kontol kas 70 € (palk kadus) või 1100 € (ost oli tasuta). Õige oleks 1070 €. Seepärast kasutavad pangad tehinguid (transaction), mis on sisuliselt kriitilised piirkonnad.

**Kontrolli, siis tegutse.** Võidujooks tekib ka siis, kui programm kontrollib midagi ja tegutseb hiljem: „kas koht on vaba? → jah → müü“. Kontrolli ja tegutsemise vahel võib teine lõim olukorra muuta. Turvalisuses nimetatakse seda **TOCTOU-veaks** (time-of-check to time-of-use): programm kontrollib, kas kasutajal on õigus faili avada, kuid enne avamist vahetab ründaja faili lingiga süsteemifailile. Sellised vead on Windowsis ja Linuxis olnud õiguste tõstmise rünnakute aluseks.

### 8.3 Kriitiline piirkond

**Kriitiline piirkond** (critical region / critical section) on koodiosa, mis kasutab jagatud ressurssi ja kus korraga tohib olla ainult üks lõim. Programmis näeb see välja nii:

```
sisene_kriitilisse_piirkonda()    // võta lukk
    x = x + 1                     // ainult üks lõim korraga
lahku_kriitilisest_piirkonnast()  // vabasta lukk
```

Hea lahendus peab rahuldama nelja tingimust. Iga tingimuse juures on näide, mis juhtub, kui see ei kehti:

1. **Kaks lõime ei ole kunagi korraga kriitilises piirkonnas.** Kui see ei kehti, tekib võidujooks ja kaks kassapidajat müüvad sama kinopileti.
2. **Lahendus ei eelda midagi protsessorite arvu ega kiiruse kohta.** Lahendus „lõim B ootab igaks juhuks 1 ms“ võib arendaja arvutis töötada, kuid aeglasemas või koormatud serveris enam mitte.
3. **Kriitilisest piirkonnast väljas olev lõim ei blokeeri teisi.** Kui lõimed peavad rangelt vahetult kordamööda sisenema (A, B, A, B…) ja A-l pole midagi teha, ootab B asjata.
4. **Ükski lõim ei oota lõputult (näljutamise puudumine).** Kui lukk antakse alati kõige kiiremale lõimele, võib aeglane lõim jääda igaveseks ootama.

**Rusikareegel:** hoia kriitiline piirkond **võimalikult lühike**. Mida kauem lukku hoitakse, seda rohkem teised ootavad ja seda vähem on mitmest tuumast kasu. Ära tee luku all aeglaseid asju, nagu faili lugemine või võrgupäring, kui see pole vältimatu.

### 8.4 Vastastikune välistamine ja aatomikäsud

**Vastastikune välistamine** (mutual exclusion) on põhimõte, et kui üks lõim on kriitilises piirkonnas, peavad teised ootama.

Esmapilgul tundub, et luku saab teha tavalise muutujaga:

```
kui lukk == 0:     // kas vaba?
    lukk = 1       // võtan
```

See **ei tööta**, sest tegu on sama võidujooksuga: kaks lõime võivad mõlemad näha `lukk == 0` ja mõlemad lukku võtta. Lukk ise peab olema tehtud jagamatu (aatomse) tehtega.

Selleks on protsessoris **aatomikäsud**, mida teised tuumad ei saa poole pealt katkestada:

- x86/x64 `LOCK CMPXCHG` (võrdle ja vaheta): „kui mälus on 0, kirjuta 1 ja teata õnnestumisest – kõik ühe sammuna“;
- `LOCK XADD` – suurenda mälus olevat arvu aatomselt;
- ARM64 LSE-laiendused (nt `CAS`, `LDADD`).

Nende peal on ehitatud kõik kõrgema taseme lukud. Windowsis saab neid kasutada otse `Interlocked`-funktsioonidega, nt `InterlockedIncrement(&x)` teeb `x = x + 1` ühe jagamatu sammuna.

Kui lukk on hõivatud, saab lõim oodata kahel viisil:

| | Aktiivne ootamine (spinlock) | Blokeeriv ootamine |
| --- | --- | --- |
| Kuidas | lõim kontrollib tsüklis: „kas juba vaba?“ | lõim pannakse ootele, OS äratab ta, kui lukk vabaneb |
| Protsessoriaeg | raiskab, sest tuum on hõivatud tühja kontrolliga | ei raiska, sest ootel lõim ei tööta |
| Kiirus | väga kiire, kui lukk vabaneb mõne mikrosekundi jooksul | aeglasem, sest ootele minek ja äratamine nõuavad kontekstivahetust |
| Sobib | väga lühikeseks ajaks; tuumas kõrgel IRQL-il, kus ootele minna ei saa (ptk 5) | kõigeks pikemaks, nt faili või võrgu ootamiseks |

**Näide.** Spinlukk on nagu ootamine ukse taga ja käepideme pidev katsumine. Kui teine inimene tuleb kohe välja, on see kiire. Kui ta jääb tunniks, raiskasid sa tunni. Blokeeriv ootamine on nagu numbri võtmine ja istumine: sind kutsutakse, kui on sinu kord.

Windowsi kriitiline sektsioon kasutab mõlemat: alguses proovib ta lühidalt spinnida ja kui lukk ei vabane, läheb lõim ootele.

### 8.5 Semafor

**Semafor** (semaphore, Edsger Dijkstra 1965) on täisarvuline loendur kahe aatomtehtega:

- **P / wait** – vähenda loendurit; kui see on juba 0, oota, kuni keegi suurendab;
- **V / signal** – suurenda loendurit ja ärata üks ootaja.

Semafori väärtus näitab, mitu ressurssi on vaba. Seetõttu sobib **loendav semafor** piiratud arvu ressursside haldamiseks.

**Näide: andmebaasiühendused.** Serveril on litsents ainult viie samaaegse andmebaasiühenduse jaoks. Semafori algväärtus on 5:

1. Viis päringut teevad P: loendur langeb 5 → 0 ja kõik saavad ühenduse.
2. Kuues päring teeb P: loendur on 0, seega jääb ta ootama.
3. Esimene päring lõpetab ja teeb V: loendur tõuseb 1-ni ja kuues päring ärkab.

**Tootja–tarbija lahendus.** Klassikaline lahendus kasutab kahte semafori ja ühte mutexit:

```
vabad    = Semafor(5)   // vabu kohti puhvris
täidetud = Semafor(0)   // andmetega kohti puhvris
lukk     = Mutex()      // puhvri enda kaitse

Tootja:                     Tarbija:
  P(vabad)                    P(täidetud)
  lukk.võta()                 lukk.võta()
    pane andmed puhvrisse       võta andmed puhvrist
  lukk.vabasta()              lukk.vabasta()
  V(täidetud)                 V(vabad)
```

Kui puhver on täis, on `vabad = 0` ja tootja jääb ootama. Kui puhver on tühi, on `täidetud = 0` ja tarbija jääb ootama. Mutex tagab, et puhvri sisemisi andmeid muudab korraga ainult üks lõim.

Tänapäeval ei pea seda tavaliselt ise kirjutama. .NET-is teeb sama `BlockingCollection<T>` või `Channel<T>`.

> 🖱️ **Proovi ise:** [semafor parklas ja tootja–tarbija puhver](https://valdonolvak.github.io/Operatsioonis-steemi-ja-k-berturbe-alused/interaktiivne/08-sunkroniseerimine.html#semafor)

### 8.6 Mutex

**Mutex** (mutual exclusion object) on lukk, millel on kaks olekut (vaba või võetud) ja **omanik**: lukku saab vabastada ainult see lõim, kes selle võttis. Selles seisneb põhiline erinevus semaforist väärtusega 1: semafori saab suurendada ükskõik milline lõim, mutexit vabastada ainult selle omanik.

Windowsi mutexi omadused:

- see on **tuumaobjekt**, seega saab seda kasutada ka **protsesside vahel**, kui sellele nime anda (nt `Global\Logi`);
- see on **rekursiivne**: omanik võib sama lukku võtta mitu korda, kuid peab selle ka sama mitu korda vabastama;
- kui omaniklõim lõpeb lukku vabastamata, saab järgmine ootaja tagastusväärtuse **`WAIT_ABANDONED`**. Lukk on nüüd tema käes, kuid ta teab, et jagatud andmed võivad olla poolikud ja neid tuleb kontrollida.

**Näide: ainult üks programmi eksemplar.** Paljud programmid kontrollivad käivitumisel nimega mutexi abil, kas nad juba töötavad. Kui mutex `Global\MinuProgramm` on juba olemas, toob programm olemasoleva akna ette ja teist eksemplari ei käivita.

**Näide turvalisusest.** Sama nippi kasutab pahavara: enne nakatamist kontrollib see, kas tema nimega mutex on juba olemas, et mitte arvutit kaks korda nakatada. Näiteks lunavara WannaCry kontrollis mutexit `MsWinZonesCacheCounterMutexA` (mõnes variandis `Global\MsWinZonesCacheCounterMutexA0`). Turvaanalüütikud kasutavad selliseid mutexi nimesid **ründetunnustena** (IoC) ja vahel isegi „vaktsiinina“: kui luua see mutex ise, arvab pahavara, et arvuti on juba nakatunud, ja lõpetab töö.

> 🖱️ **Proovi ise:** [mutex ehk WC võti](https://valdonolvak.github.io/Operatsioonis-steemi-ja-k-berturbe-alused/interaktiivne/08-sunkroniseerimine.html#mutex)

### 8.7 Sündmus

**Sündmus** (event) ei kaitse andmeid, vaid **teavitab**: üks lõim ütleb teistele „nüüd võib edasi minna“. Sündmusel on kaks olekut: signaalitud ja signaalimata.

Windowsis on kaks liiki sündmusi:

- **käsitsi lähtestuv** (manual-reset) – kui sündmus signaalitakse, ärkavad **kõik** ootajad ja sündmus jääb signaalituks, kuni keegi selle lähtestab. See sobib teadaannetele, mis puudutavad kõiki, näiteks „seadistus on laaditud, kõik võivad alustada“;
- **automaatselt lähtestuv** (auto-reset) – ärkab **üks** ootaja ja sündmus lähtestub kohe. See sobib „järgmine palun“ olukordadesse.

**Näide: koolikell.** Kell heliseb ja kõik klassid lähevad vahetunnile. See on käsitsi lähtestuv sündmus, sest kõik ootajad ärkavad korraga. Arsti ooteruumi tablool süttiv number kutsub aga ainult ühe patsiendi. See on automaatselt lähtestuv sündmus.

### 8.8 Tüüpilised vead

| Viga | Mis juhtub | Näide |
| --- | --- | --- |
| Lukk on unustatud | võidujooks, andmed rikuvad aeg-ajalt | loendur, mida üks koodikoht suurendab lukuga ja teine ilma |
| Lukku ei vabastata | kõik teised ootavad igavesti | erindi korral jääb `ReleaseMutex` täitmata; lahendus on `try/finally` või C# `lock` |
| Liiga suur kriitiline piirkond | programm on aeglane, tuumadest pole kasu | luku all tehakse võrgupäringut |
| Ummik (deadlock) | kaks lõime ootavad teineteist igavesti | A hoiab lukku 1 ja ootab lukku 2, B hoiab lukku 2 ja ootab lukku 1 (ptk 11) |
| Kontrolli, siis tegutse | olukord muutub kontrolli ja tegutsemise vahel | TOCTOU-viga failiõiguste kontrollimisel |

### 8.9 Sünkroniseerimismehhanismid Windowsis

| Mehhanism | Režiim | Kasutus | Näide |
| --- | --- | --- | --- |
| Interlocked-funktsioonid | kasutaja | üks aatomtehe | `InterlockedIncrement` külastajate loenduris |
| Kriitiline sektsioon (CRITICAL\_SECTION) | kasutaja | kiire lukk ühe protsessi lõimede vahel | logifaili kirjutamine ühes programmis |
| SRW-lukk (Slim Reader/Writer) | kasutaja | palju lugejaid või üks kirjutaja; väike ja kiire | vahemälu, mida loetakse tihti ja muudetakse harva |
| Tingimusmuutuja (condition variable) | kasutaja | oota, kuni tingimus kehtib (koos SRW-luku või kriitilise sektsiooniga) | tarbija ootab, kuni järjekorras on andmeid |
| WaitOnAddress | kasutaja | oota, kuni mäluaadressi väärtus muutub (futexi sarnane) | kerge lipp lõimede vahel |
| Sündmus (event) | tuumaobjekt | üks lõim teavitab teisi | „allalaadimine on valmis“ |
| Mutex | tuumaobjekt | lukk ka protsesside vahel | kaks skripti kirjutavad samasse faili |
| Semafor | tuumaobjekt | loendav lukk | kuni 5 samaaegset ühendust |
| Ootetaimer (waitable timer) | tuumaobjekt | äratus kindlal ajal | automaatsalvestus iga 5 minuti järel |
| Spinlukk, pushlukk, ERESOURCE | tuum | draiverite ja tuuma sisemised lukud | draiver kaitseb oma seadme andmeid |

Lõim ootab tuumaobjekte funktsioonidega `WaitForSingleObject` ja `WaitForMultipleObjects`. Kasutajarežiimi lukud (SRW, kriitiline sektsioon) on kiiremad, sest tavajuhul, kui lukk on vaba, ei toimu süsteemikutset. Tuumaobjekt nõuab alati tuuma poole pöördumist, kuid töötab ka protsesside vahel.

**Tava ja soovitus.** Uues koodis kasuta keele vahendeid: C# `lock`, `SemaphoreSlim`, `Channel<T>`, C++ `std::mutex`, `std::atomic`. Need vabastavad luku automaatselt ka erindi korral. Rust keelab andmevõidujooksud juba kompileerimisel: kood, kus kaks lõime saaksid samu andmeid lukuta muuta, ei kompileeru.

> 🖱️ **Proovi ise:** [vali sobiv mehhanism](https://valdonolvak.github.io/Operatsioonis-steemi-ja-k-berturbe-alused/interaktiivne/08-sunkroniseerimine.html#vali) erinevate olukordade jaoks.

### Näide: viimane kinopilet, parkla ja WC võti

**Võidujooks.** Kinos on vaba üks koht. Kaks kassapidajat vaatavad samal sekundil ekraanilt „vaba“, müüvad selle kumbki oma kliendile ja kirjutavad „hõivatud“. Saalis on kaks inimest ühe tooli peal. Lahendus: koha kontroll ja müük peavad olema üks jagamatu samm (kriitiline piirkond lukuga) või andmebaasi tehing.

**Semafor.** Parklas on 5 kohta ja sissepääsu juures tabloo „vabu kohti: 5“. Iga sisenev auto vähendab arvu (P), iga lahkuv suurendab (V). Kui arv on 0, ootab tõkkepuu ees järjekord.

**Mutex.** Kohviku WC-l on üks võti. Võtme saab see, kes sisse läheb, ja tagastada saab ainult tema ise (omanik). Kui keegi läheb võtmega koju, jäävad teised lõputult ootama – Windowsis annab selle kohta teada `WAIT_ABANDONED`.

**Sündmus.** Koolikell heliseb ja kõik klassid lähevad korraga vahetunnile (käsitsi lähtestuv sündmus).

**Windowsis.** Kaks PowerShelli skripti kirjutavad samaaegselt samasse logifaili `Add-Content`-iga. Ilma lukuta segunevad read või tekib viga „fail on kasutusel“. Lahendus on nimega mutex:

```powershell
$m = [System.Threading.Mutex]::new($false, 'Global\Logi')
$m.WaitOne() | Out-Null
try {
    Add-Content -Path C:\Logid\too.log -Value "$(Get-Date) skript $PID"
} finally {
    $m.ReleaseMutex()
}
```

`try/finally` tagab, et mutex vabastatakse ka siis, kui kirjutamisel tekib viga. Muidu jääks teine skript igavesti ootama.

### Kokkuvõte

- Võidujooks tekib, kui mitu lõime muudab jagatud andmeid kontrollimatult; `x = x + 1` pole üks samm, vaid kolm.
- Kriitilist piirkonda kaitstakse vastastikuse välistamisega; hoia see lühike.
- Lukud on ehitatud protsessori aatomikäskudele; tavaline muutuja lukuna ei tööta.
- Spinlukk sobib väga lühikeseks ootamiseks, blokeeriv ootamine kõigeks pikemaks.
- Semafor loendab, mutexil on omanik, sündmus teavitab.
- Windowsis on kiired kasutajarežiimi lukud ja tuumaobjektid, mis töötavad ka protsesside vahel.
- Võidujooksud on ka turvavead (TOCTOU); mutexi nimed on pahavara tunnused.

### Kontrollküsimused

> 🧪 **Interaktiivne test:** [kontrolli oma teadmisi](https://valdonolvak.github.io/Operatsioonis-steemi-ja-k-berturbe-alused/interaktiivne/08-test.html). Test koosneb 25 valikvastusega küsimusest. Iga vastuse järel näed selgitust ning lõpus saad valesti vastatud küsimusi korrata.

1. Mis on võidujooks? Too näide ja selgita tabeli abil, kuidas suurendus kaduma läheb.
2. Mis vahe on koostöötavatel ja konkureerivatel lõimedel? Too kummastki näide.
3. Mis on kriitiline piirkond ja millised neli tingimust peab hea lahendus täitma?
4. Miks ei saa lukku teha tavalise muutujaga `if (lukk == 0) lukk = 1`?
5. Mis vahe on aktiivsel ja blokeerival ootamisel? Millal on kumbki sobiv?
6. Kuidas töötab semafor? Selgita näite abil, mis juhtub kuuenda päringuga, kui semafori algväärtus on 5.
7. Mille poolest erineb mutex semaforist? Mida tähendab `WAIT_ABANDONED`?
8. Mis vahe on käsitsi ja automaatselt lähtestuval sündmusel?
9. Mis on TOCTOU-viga ja miks on see turvarisk?
10. Nimeta neli Windowsi sünkroniseerimismehhanismi ja nende kasutusala.

### Valikküsimused

1. Mis on mutexi eriomadus semafori ees? a) loendab mitu ressurssi b) tal on omanik c) ei vaja tuuma d) töötab ainult ühes lõimes
2. Milline mehhanism sobib „palju lugejaid, üks kirjutaja“ olukorraks? a) postkastipesa b) SRW-lukk c) ootetaimer d) sündmus
3. Mis põhjustab kaotatud suurenduse `x = x + 1` puhul? a) liiga väike mälu b) tehte mitteaatomilisus c) vale prioriteet d) leheviga
4. Milline mehhanism äratab korraga kõik ootajad? a) automaatselt lähtestuv sündmus b) käsitsi lähtestuv sündmus c) SRW-lukk d) Interlocked-funktsioon

### Praktiline ülesanne

Käivita PowerShell 7-s võidujooksu demo. Esimene variant kaotab suurendusi, teine mitte:

```powershell
# Ilma lukuta
$c = [ref]0
1..8 | ForEach-Object -Parallel {
  $r = $using:c
  1..10000 | ForEach-Object { $r.Value++ }
} -ThrottleLimit 8
"Ilma lukuta: $($c.Value) (oodatud 80000)"

# Lukuga (Monitor = kriitiline sektsioon)
$d = [ref]0
$lukk = [object]::new()
1..8 | ForEach-Object -Parallel {
  $r = $using:d; $l = $using:lukk
  1..10000 | ForEach-Object {
    [System.Threading.Monitor]::Enter($l)
    try { $r.Value++ } finally { [System.Threading.Monitor]::Exit($l) }
  }
} -ThrottleLimit 8
"Lukuga: $($d.Value)"
```

Käivita esimest varianti mitu korda. Kas tulemus on iga kord sama? Selgita, miks tulemused erinevad ja miks teine variant on aeglasem.

### Iseseisvad ülesanded

**1. Tootja ja tarbija.** Kirjuta PowerShell 7-s või C#-s programm, kus üks lõim paneb järjekorda arvud 1–20 ja teine võtab need sealt ning kirjutab ekraanile. Kasuta `System.Collections.Concurrent.BlockingCollection` (PowerShellis `[System.Collections.Concurrent.BlockingCollection[int]]::new(5)`), mille maksimaalne suurus on 5. **Esita:** kood, väljund ja selgitus, mis juhtub, kui järjekord on täis või tühi – millise ptk 8 mehhanismi see asendab?

**2. Sünkroniseerimine igapäevaelus.** Leia oma elust kolm olukorda, mis vastavad mutexile, semaforile ja sündmusele (event), nt ühine köök, parkla, kooli kell. **Esita:** tabel „olukord – mehhanism – mis juhtuks ilma selleta“.

### Viited

- Krogh (2017), ptk 8.
- Tanenbaum, Bos (2022), *Modern Operating Systems*, ptk 2.3.
- Microsoft Learn: [Synchronization](https://learn.microsoft.com/windows/win32/sync/synchronization).
- Microsoft Learn: [Synchronization Objects](https://learn.microsoft.com/windows/win32/sync/synchronization-objects).
- Microsoft Learn: [Slim Reader/Writer (SRW) Locks](https://learn.microsoft.com/windows/win32/sync/slim-reader-writer--srw--locks).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 7. Protsessid, lõimed ja tööd](07-protsessid-ja-loimed.md) · [Sisukord](../README.md) · [9. Protsesside planeerimine ühel protsessoril →](09-planeerimine-uhel-protsessoril.md)
