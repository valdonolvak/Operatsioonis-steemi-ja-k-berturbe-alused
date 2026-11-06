[← 15. Failisüsteemid Windowsis](15-failisusteemid.md) · [Sisukord](../README.md) · [17. Võrguvõimalused Windowsis →](17-vork.md)

## 16. Salvestushaldus

Salvestushaldus otsustab, kuidas füüsilised kettad jagatakse partitsioonideks ja köideteks, kuidas andmeid kaitstakse riketest (peegeldamine, paarsus) ja varguse eest (krüpteerimine) ning kuidas serverid salvestusruumi jagavad. Windowsis on selleks kettahaldus, Storage Spaces, BitLocker ja SMB-failiteenus.

### 16.1 Salvestamine kettal

HDD salvestab andmeid magnetiliselt pöörlevatel plaatidel; pöördumisaeg koosneb otsinguajast ja pöördeviitest. SSD salvestab andmeid NAND-välkmällu; kirjutada saab ainult kustutatud plokki, mistõttu SSD kontroller teeb kulumise ühtlustamist ja prügikoristust. NVMe-SSD ühendub otse PCIe-siiniga ja on SATA-SSD-st mitu korda kiirem.

### 16.2 Kõvaketaste korraldus

**Partitsioonitabelid:**

|  | MBR | GPT |
| --- | --- | --- |
| Aasta | 1983 | 2000ndad (UEFI osa) |
| Max ketta suurus | 2 TB | praktiliselt piiramatu (9,4 ZB) |
| Partitsioone | 4 primaarset (või laiendatud) | 128 (Windowsis) |
| Varukoopia | puudub | päise koopia ketta lõpus, CRC |
| Käivitus | BIOS | UEFI (nõutud Windows 11-l) |

**Tavaline Windows 11 ketas (GPT):** EFI süsteemipartitsioon (FAT32, \~100–260 MB), Microsofti reserveeritud partitsioon (MSR, 16 MB), Windowsi partitsioon (NTFS) ja taastepartitsioon (Windows RE).

**Põhikettad ja dünaamilised kettad.** Põhiketas (basic disk) kasutab tavalisi partitsioone. Dünaamiline ketas võimaldas tarkvaralist RAID-i, kuid on aegunud; selle asemel kasutatakse **Storage Spaces**’it.

**Köide** (volume) on vormindatud ala, millel on failisüsteem ja tavaliselt ketta täht või ühenduspunkt.

**RAID-tasemed:**

| Tase | Meetod | Talub rikkeid | Kasutatav maht |
| --- | --- | --- | --- |
| RAID 0 | triibutus (striping) | 0 ketast | 100% |
| RAID 1 | peegeldamine | 1 ketas | 50% |
| RAID 5 | triibutus + paarsus | 1 ketas | (n−1)/n |
| RAID 6 | topeltpaarsus | 2 ketast | (n−2)/n |
| RAID 10 | peegeldatud triibud | 1 ketas paari kohta | 50% |

> 📊 **Joonis:** GPT-ketta partitsioonid ning RAID 1 ja RAID 5 andmepaigutus

Joonis: RAID 1 hoiab igast plokist kahte koopiat; RAID 5 jaotab andmed ja paarsuse (p) kolmele kettale, nii et ükskõik millise ketta sisu saab ülejäänutest taastada.

**Storage Spaces** koondab kettad **kogumisse** (pool) ja loob sellest virtuaalkettaid tüübiga *simple*, *mirror* (2- või 3-suunaline) või *parity*. **Storage Spaces Direct** (S2D) teeb sama klastri serverite lokaalsete ketastega (hüperkonvergentne taristu).

**BitLocker** krüpteerib terve köite (XTS-AES). Võti on kaitstud TPM-iga, soovi korral lisaks PIN-iga. Taastevõti tuleb hoida turvaliselt (Entra ID, Active Directory, Microsofti konto). Uutel Windows 11 seadmetel lülitub **seadme krüpteerimine** sageli automaatselt sisse – administraator peab teadma, kus taastevõtmed asuvad, enne kui ketast või emaplaati vahetatakse.

### 16.3 Andmete salvestamine serverites

| Lahendus | Kirjeldus | Näide |
| --- | --- | --- |
| DAS (Direct Attached Storage) | kettad otse serveris | serveri NVMe-kettad |
| NAS (Network Attached Storage) | failitaseme jagamine võrgus | SMB- või NFS-ketas |
| SAN (Storage Area Network) | plokitaseme salvestusvõrk | iSCSI, Fibre Channel, NVMe-oF |
| Hüperkonvergentne | arvutus ja salvestus samades sõlmedes | S2D, Azure Local, vSAN |
| Pilvesalvestus | teenusena | Azure Files, OneDrive, SharePoint |

**SMB 3.x** failiteenus pakub krüpteerimist, allkirjastamist, mitmekanalit (multichannel) ja RDMA-d. Windows Server 2025 toob **SMB over QUIC**-i kõigisse väljaannetesse: failijagamine üle interneti ilma VPN-ita, TLS 1.3 krüpteeringuga ([Petri](https://petri.com/windows-server-2025-public-preview/)). **Storage Replica** dubleerib köiteid teise serverisse või saidile.

**Varundus.** Kettapeegel ei ole varukoopia: kustutatud või lunavara poolt krüpteeritud fail peegeldub kohe. Kasuta reeglit **3-2-1-1-0**: 3 koopiat, 2 eri kandjal, 1 väljaspool asutust, 1 võrguühenduseta või muutumatu (immutable), 0 viga taastetestis.

### Näide: kooli failiserveri kolm halba päeva

Failiserveris on neli 4 TB ketast Storage Spaces’i kahesuunalises peeglis (kasutatav maht \~8 TB) ja varundus käib igal ööl NAS-ile ning kord nädalas võrguühenduseta kettale.

1. **Esmaspäev – ketas rikkis.** `Get-PhysicalDisk` näitab üht ketast olekus *Lost Communication*, virtuaalketas on *Degraded*, kuid kasutajad töötavad edasi. Administraator vahetab ketta ja Storage Spaces taastab peegli. **RAID töötas.**
2. **Kolmapäev – lunavara.** Õpetaja arvutist käivitatud pahavara krüpteerib võrguketta failid. Peegel kirjutas krüpteeritud failid hetkega mõlemale kettale ja ka öine NAS-i koopia võib olla rikutud. Taastatakse võrguühenduseta kettalt eelmise nädala seisu ja varikoopiatest. **Aitas ainult 3-2-1-1-0.**
3. **Reede – emaplaadi vahetus.** Pärast hooldust küsib arvuti BitLockeri taastevõtit, sest TPM on uus. Võti leitakse Entra ID-st (või AD-st). Kui seda poleks salvestatud, oleksid andmed kadunud ka omanikule.

**Õppetund:** iga kaitsemeede kaitseb kindla ohu eest – RAID riistvararikke, varundus kustutamise ja lunavara, BitLocker varguse eest.

### Kokkuvõte

- Windows 11 nõuab GPT-ketast ja UEFI-d.
- RAID ja Storage Spaces kaitsevad ketta rikke eest, mitte kustutamise ega lunavara eest.
- BitLocker kaitseb varastatud seadme andmeid; taastevõtme haldus on kriitiline.
- Serverite salvestus liigub hüperkonvergentsi ja pilve suunas; SMB over QUIC lihtsustab kaugligipääsu.

### Kontrollküsimused

1. Mis vahe on HDD-l ja SSD-l salvestamise põhimõttes?
2. Võrdle MBR- ja GPT-partitsioonitabelit.
3. Millised partitsioonid on tavalisel Windows 11 kettal?
4. Selgita RAID 0, 1, 5 ja 10 erinevusi.
5. Mis on Storage Spaces ja Storage Spaces Direct?
6. Kuidas BitLocker andmeid kaitseb ja miks on taastevõti oluline?
7. Mis vahe on NAS-il ja SAN-il?
8. Miks ei ole RAID varukoopia? Selgita 3-2-1-1-0 reeglit.

### Valikküsimused

1. Mis on MBR-ketta maksimaalne suurus? a) 2 TB b) 16 TB c) 256 TB d) piiramatu
2. Milline RAID-tase ei talu ühtegi ketta riket? a) RAID 0 b) RAID 1 c) RAID 5 d) RAID 6
3. Mis protokoll võimaldab SMB-failijagamist üle interneti ilma VPN-ita? a) NFS b) iSCSI c) SMB over QUIC d) FTP
4. Mis kaitseb BitLockeri võtit vaikimisi? a) parool b) TPM c) USB-võti d) Secure Boot

### Praktiline ülesanne

```powershell
Get-Disk | Select-Object Number, FriendlyName, PartitionStyle, Size, BusType
Get-Partition | Select-Object DiskNumber, PartitionNumber, Type, Size, DriveLetter
Get-BitLockerVolume | Select-Object MountPoint, VolumeStatus, EncryptionMethod, KeyProtector
Get-PhysicalDisk | Get-StorageReliabilityCounter | Select-Object DeviceId, Temperature, Wear, ReadErrorsTotal
```

Lisa virtuaalmasinale kolm tühja 10 GB ketast. Loo Serveri halduris või PowerShellis kogum ja 2-suunaline peegel:

```powershell
$k = Get-PhysicalDisk -CanPool $true
New-StoragePool -FriendlyName Kogum1 -StorageSubSystemFriendlyName 'Windows Storage*' -PhysicalDisks $k
New-VirtualDisk -StoragePoolFriendlyName Kogum1 -FriendlyName Peegel -ResiliencySettingName Mirror -Size 8GB |
  Get-Disk | Initialize-Disk -PassThru | New-Partition -AssignDriveLetter -UseMaximumSize | Format-Volume -FileSystem ReFS
```

Eemalda üks ketas virtuaalmasinast ja kontrolli, kas andmed on loetavad. Mida näitab `Get-VirtualDisk`?

### Iseseisvad ülesanded

**1. Varundusplaan.** Koosta väikesele organisatsioonile (nt 15 töötajaga kool, raamatupidamisbüroo või oma pere) varundusplaan reegli 3-2-1-1-0 järgi. Määra: mida varundatakse, kui tihti, kuhu (kolm koopiat), milline koopia on võrguühenduseta või muutumatu, kes vastutab ja kuidas taastamist testitakse. **Esita:** üheleheküljeline plaan ja hinnang, kui palju andmeid võib halvimal juhul kaduda (RPO) ning kui kiiresti töö taastub (RTO).

**2. BitLockeri taaste harjutus.** Lülita õppe-VM-is (vTPM-iga) sisse BitLocker, salvesta taastevõti faili ja vaata seda käsuga `manage-bde -protectors -get C:`. Tekita taasteolukord (nt `manage-bde -forcerecovery C:` ja taaskäivitus) ning ava ketas taastevõtmega. **Esita:** sammude kirjeldus ja vastus: kus peaks organisatsioon taastevõtmeid hoidma ja kes neile ligi pääseb?

### Viited

- Krogh (2017), ptk 16.
- Microsoft Learn: [Storage Spaces overview](https://learn.microsoft.com/windows-server/storage/storage-spaces/overview); [BitLocker overview](https://learn.microsoft.com/windows/security/operating-system-security/data-protection/bitlocker/); [SMB over QUIC](https://learn.microsoft.com/windows-server/storage/file-server/smb-over-quic).

> 📝 **Test:** selle peatüki Google Formsi test luuakse skriptiga [`google-forms/looVormid.gs`](../google-forms/looVormid.gs).

---

[← 15. Failisüsteemid Windowsis](15-failisusteemid.md) · [Sisukord](../README.md) · [17. Võrguvõimalused Windowsis →](17-vork.md)
