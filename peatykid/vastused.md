[← 31. UUS: Küberturbe õigus- ja juhtimisraamistik](31-oigus-ja-juhtimisraamistik.md) · [Sisukord](../README.md) · [Sõnastik →](sonastik.md)

## Valikküsimuste vastused

| Peatükk | Vastused |
| --- | --- |
| 1. Windowsi ajalugu | 1c, 2b, 3b, 4b |
| 2. OS-i ülesanded | 1b, 2c, 3b |
| 3. Põhimõisted | 1c, 2b, 3c, 4b |
| 4. Arhitektuur | 1b, 2b, 3a, 4b |
| 5. Süsteemimehhanismid | 1c, 2b, 3b |
| 6. Register | 1c, 2b, 3c |
| 7. Protsessid ja lõimed | 1b, 2c, 3b, 4b |
| 8. Sünkroniseerimine | 1b, 2b, 3b |
| 9. Planeerimine ühel protsessoril | 1c, 2b, 3c, 4b |
| 10. Planeerimine mitmel protsessoril | 1c, 2b, 3b |
| 11. Ummikud | 1a, 2b, 3d |
| 12. Mäluhaldus | 1b, 2b, 3b, 4c |
| 13. Vahemälu | 1c, 2b, 3a |
| 14. Sisend ja väljund | 1b, 2c, 3b, 4c |
| 15. Failisüsteemid | 1b, 2b, 3b, 4b |
| 16. Salvestushaldus | 1a, 2a, 3c, 4b |
| 17. Võrk | 1b, 2b, 3b, 4b |
| 18. Turvalisus | 1b, 2b, 3b, 4a, 5b |
| 19. Krahhid | 1b, 2b, 3b, 4c |
| 20. Virtualiseerimine | 1c, 2b, 3b, 4a, 5c, 6d |
| 21. Tehisintellekt | 1c, 2b, 3b, 4c |
| 22. Küberturve | 1b, 2b, 3c, 4a, 5b |
| 23. Windows, Linux ja macOS | 1a, 2c, 3d, 4b, 5c |
| 24. Põhimõisted ja ründevektorid | 1b, 2c, 3a, 4d, 5c |
| 25. Krüptograafia ajalugu | 1c, 2a, 3d, 4b |
| 26. Sümmeetriline krüpteerimine | 1d, 2b, 3a, 4c |
| 27. Räsimine ja paroolid | 1b, 2d, 3a, 4c |
| 28. Asümmeetriline krüptograafia | 1a, 2c, 3d, 4b |
| 29. Digiallkiri, PKI ja TLS | 1c, 2a, 3b, 4d |
| 30. Postkvantkrüptograafia | 1c, 2a, 3b, 4d |
| 31. Õigus- ja juhtimisraamistik | 1b, 2d, 3a, 4c |

**Lisa ülesanne 9.4 vastus (Round Robin, kvant 2 ms, A = 8, B = 4, C = 2, järjekord A, B, C):** täitmise järjekord A(0–2), B(2–4), C(4–6, lõpeb), A(6–8), B(8–10, lõpeb), A(10–14, lõpeb). Ooteajad (lõppaeg miinus töö pikkus): A = 14 − 8 = 6, B = 10 − 4 = 6, C = 6 − 2 = 4; keskmine 5,3 ms.

**Ptk 9 iseseisva ülesande 1 vastus (P1 = 6, P2 = 3, P3 = 8, P4 = 1 ms):**

| Algoritm | Täitmise järjekord | Ooteajad P1 / P2 / P3 / P4 | Keskmine |
| --- | --- | --- | --- |
| FCFS | P1 (0–6), P2 (6–9), P3 (9–17), P4 (17–18) | 0 / 6 / 9 / 17 | 8 ms |
| SJF | P4 (0–1), P2 (1–4), P1 (4–10), P3 (10–18) | 4 / 1 / 10 / 0 | 3,75 ms |
| RR, kvant 2 | P1, P2, P3, P4 (6–7), P1, P2 (9–10), P3, P1 (12–14), P3 (14–18) | 8 / 7 / 10 / 6 | 7,75 ms |

Interaktiivsele arvutile sobib Round Robin (või prioriteetidega mitmetasemeline järjekord): keskmine ooteaeg on suurem kui SJF-il, kuid ükski töö ei oota esimest vastust kauem kui paar kvanti ja tööde pikkust ei pea ette teadma.

**Ptk 11 praktilise ülesande vastus:** T1 → P → T2 → S → T3 → F → T1 on tsükkel, seega tekib ummik. Vältimine: kõik lõimed võtavad ressursid kindlas järjekorras (nt F, P, S) või võtavad kõik vajalikud ressursid korraga.

Märkus õpetajale: õige vastus on sageli variant b. Testide kasutamisel Moodle’is või muus õpikeskkonnas lülita sisse vastusevariantide segamine.

---

[← 31. UUS: Küberturbe õigus- ja juhtimisraamistik](31-oigus-ja-juhtimisraamistik.md) · [Sisukord](../README.md) · [Sõnastik →](sonastik.md)
