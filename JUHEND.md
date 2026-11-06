# Google Formsi testide loomine

Skript `looVormid.gs` loob sinu Google'i kontole testid automaatselt. Sa ei pea küsimusi käsitsi sisestama.

| Test | Sisu |
| --- | --- |
| 23 peatükitesti | Iga peatüki valikküsimused (kokku 88) koos õigete vastuste ja punktidega, lõpus ka peatüki avatud kontrollküsimused (hindamata) |
| Üldtest | 48 uut valikküsimust, igast peatükist vähemalt kaks, õiged vastused on eri tähtede vahel segatud |

## Sammud

1. Ava [script.google.com](https://script.google.com) ja vali **Uus projekt**.
2. Kustuta näidiskood `function myFunction() {}`.
3. Kopeeri faili `looVormid.gs` kogu sisu ja kleebi see redaktorisse. Salvesta (Ctrl+S).
4. Vali ülaribal funktsioon **`looKoikVormid`** ja vajuta **Käivita**.
5. Google küsib õigusi. Vali oma konto → **Täpsemalt** → **Ava projekt (ebaturvaline)** → **Luba**. Hoiatus tuleb seetõttu, et skript on sinu enda tehtud ja Google pole seda kontrollinud.
6. Oota umbes 1–3 minutit. Logis on kirjas „Valmis: 24 testi …“.

Tulemus on Google Drive'i kaustas **„OS-i alused – testid“**. Seal on kõik vormid ja tabel **„OS-i alused – testide lingid“**, kus on iga testi muutmise ja vastamise link.

## Seaded

Faili alguses saad seadeid muuta enne käivitamist:

| Seade | Vaikimisi | Tähendus |
| --- | --- | --- |
| `LISA_KONTROLLKYSIMUSED` | `true` | Lisab avatud kontrollküsimused peatükitesti lõppu |
| `KYSI_NIME` | `true` | Esimene väli küsib õppija nime |
| `KOGU_EMAILID` | `false` | `true` korral kogub vorm vastaja e-posti |
| `YKS_VASTUS` | `false` | `true` korral üks vastus kasutaja kohta (vajalik Classroomi hinnete impordiks) |
| `SEGA_KYSIMUSED` | `true` | Iga vastaja näeb küsimusi erinevas järjekorras |
| `PUNKTE_KYSIMUSE_EEST` | `1` | Punktid ühe valikküsimuse eest |

Kui soovid luua ainult ühe testi, vali funktsioon `looPeatykk1` … `looPeatykk23` või `looYldtest`. Kui käivitad `looKoikVormid` uuesti, tekivad uued koopiad. Vanad kustuta Drive'ist käsitsi.

## Google Classroomi jaoks

Enne käivitamist sea `KOGU_EMAILID = true`, `YKS_VASTUS = true` ja `KYSI_NIME = false`. Siis saab Classroom hinded automaatselt importida. Täpsem juhend on Classroomi seadistamisjuhendis.

## Pärast loomist

- **Vastusevariantide segamine:** Apps Script seda seadistada ei võimalda. Lülita see vajadusel sisse vormis küsimuse menüüst (⋮ → *Shuffle option order*).
- **Hinnete avaldamine:** vormi seadetes, jaotises *Quiz*. Vali, kas õppija näeb tulemust kohe või pärast sinu ülevaatust.
- **Ainult kooli domeen:** *Settings → Responses → Restrict to users in …*. Ainult Google Workspace for Education kontodel.
- Üldtesti vastuste võti on failis [`yldtest-vastused.md`](yldtest-vastused.md). Kui test on hindeline, ära pane seda avalikku repositooriumi.
