---
title: 'Fonetische ballon sets: spraak service'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van het fonetische alfabet van de speech-service aan het internationale fonetische alfabet (IPA) en wanneer u de set wilt gebruiken.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 580de0112c99eef4ba22ba946f6a2b81bc326001
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102432040"
---
# <a name="speech-service-phonetic-sets"></a>Fonetische sets van spraak service

De speech-service definieert fonetische letters ("telefoon sets" voor korte), bestaande uit zeven talen; `en-US`, `fr-FR` ,,,, en `de-DE` `es-ES` `ja-JP` `zh-CN` `zh-TW` . De telefoon sets van de spraak service worden doorgaans toegewezen aan het <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">internationale fonetische alfabet (IPA) </a>. Telefoon sets voor spraak services worden gebruikt in combi natie met de [SSML (Speech synthese Markup Language)](speech-synthesis-markup.md)als onderdeel van de service aanbieding voor tekst naar spraak. In dit artikel leest u hoe deze telefoon sets worden toegewezen en wanneer u de telefoonset wilt gebruiken.

# <a name="en-us"></a>[en-US](#tab/en-US)

### <a name="english-suprasegmentals"></a>Engels suprasegmentals

| Voor beeld 1 (begin voor medeklinker, woord voor klinker) | Voor beeld 2 (intervocaal-voor-medeklinker, woord in-Nucleus voor klinker) | Voor beeld 3 (CODA voor medeklinker, woord final voor klinker) | Opmerkingen |
|--|--|--|--|
| burger/b er **1** r-g AX r/ | Falafel/f AX-l AA **1** -f AX l/ | gitaar/g IH-t AA **1** r/ | Spraak service telefoon instellen stress na de klinker van de benadrukte letter greep |
| inopportune/IH **2** -n AA-p AX r-t, meer dan 1 n/ | ongelijkheid/d IH-s ih **2**-m AX-l eh 1-r AX-t iy/ | personeel/w er 1 r k-f ao **2** r s/ | Spraak service telefoon instellen stress na de klinker van de sub-benadrukte letter greep |

### <a name="english-vowels"></a>Engelse klinkers

| `sapi` | `ipa` | Voorbeeld 1     | Voorbeeld 2 | Voorbeeld 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| iy     | `i`   | **EA** t       | f **ee** l  | Vall **EY**                  |
| ih     | `ɪ`   | **i** f        | f **i** ll  |                             |
| EY     | `eɪ`  | **een** te       | g **a** te  | d **ay**                     |
| eh     | `ɛ`   | **e** zeer     | p **e** t   | m **eh** (zeldzaam woord ten slotte) |
| AE     | `æ`   | **een** ctive    | c **a** t   | n **AH** (zeldzaam woord ten slotte) |
| aa     | `ɑ`   | **o** bstinate | p **o** ppy | r **AH** (zeldzame woord Finally) |
| ao     | `ɔ`   | **o**-bereik    | c **au** se | UT **AH**                    |
| Wat     | `ʊ`   | b **OO** k      |           |                             |
| &     | `oʊ`  | **o** ld       | LC **o** ne | g **o**                      |
| rug     | `u`   | **U** ber      | b **OO** St | t **OO**                     |
| protocolspecifieke     | `ʌ`   | **u** ncle     | c **u** t   |                             |
| ay     | `aɪ`  | **i** CE       | b **Ik wil**  | FL **y**                     |
| AW     | `aʊ`  | **OE** t       | s **ou**-e | c **OE**                     |
| oy     | `ɔɪ`  | **Oi** l       | j **Oi** n  | t **Oy**                     |
| y-e   | `ju`  | **Yu**, ma      | h **u** man | f **ieuwe**                     |
| AX     | `ə`   | **een** go       | WOM **a** n | zijn **een**                    |

### <a name="english-r-colored-vowels"></a>Engelse R-gekleurde klinkers

| `sapi` | `ipa` | Voorbeeld 1    | Voorbeeld 2      | Voorbeeld 3  |
|--------|-------|--------------|----------------|------------|
| IH r   | `ɪɹ`  | **oor**     | t **IR**-amisu   | n **oor**   |
| eh r   | `ɛɹ`  | **vlieg** tuig | app **AR** ently | SC **AR** e  |
| wat r   | `ʊɹ`  |              |                | c **uw** e   |
| ay r   | `aɪɹ` | **Ierland**-land  | f **IR**-ePlace  | CH **oir**  |
| AW r   | `aʊɹ` | **uur** s    | p **lagere** volledige   | s **onze**   |
| Ao r   | `ɔɹ`  | **of** Ange   | m **of** al      | s **Oar**   |
| AA r   | `ɑɹ`  | **AR** Tist   | St **AR** t      | c **AR**    |
| er r   | `ɝ`   | **oor**    | b **IR** d       | f **uw**    |
| AX r   | `ɚ`   |              | alle **Gy**    | onde **er** |

### <a name="english-semivowels"></a>Engels Semivowels

| `sapi` | `ipa` | Voorbeeld 1           | Voorbeeld 2  | Voorbeeld 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w** i, s **UE** de | al **w** ays |           |
| y      | `j`   | **y**-ARD, f **e** w   | op **i**  |           |

### <a name="english-aspirated-oral-stops"></a>Engels aspirated orale stops

| `sapi` | `ipa` | Voorbeeld 1 | Voorbeeld 2   | Voorbeeld 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p** UT   | ha **en**  | FLA-**p**   |
| b      | `b`   | **b** IG   | num **b** er  | CRA **b**   |
| t      | `t`   | **t** alk  | capi **t** al | sough **t** |
| d      | `d`   | **d** IG   | door Voer **d** om  | ro **d**    |
| k      | `k`   | **c** UT   | Sla-**verzonken** | Ira **q**   |
| g      | `g`   | **g** o    | a **g** o     | DRA **g**   |

### <a name="english-nasal-stops"></a>Engels nasal stopt

| `sapi` | `ipa` | Voorbeeld 1        | Voorbeeld 2  | Voorbeeld 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m** op, Smash   | CA **m** era | Roo **m**    |
| n      | `n`   | **n** tot en met **n toestaan** | te **n** t   | kuiken **n** |
| lopen     | `ŋ`   |                  | Li **n** k   | s **ING**    |

### <a name="english-fricatives"></a>Engels fricatives

| `sapi` | `ipa` | Voorbeeld 1   | Voorbeeld 2        | Voorbeeld 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f** ORK    | Le **f** t         | hal **f**   |
| v      | `v`   | **v**-aarde   | e **v**        | Lo **v** e   |
| th     | `θ`   | **th** in    | EMPA **y**      | ma **e**  |
| DH     | `ð`   | **th** en    | mo       | Smoo **th** |
| s      | `s`   | **s** it     | RI **s** k         | fact **s**  |
| z      | `z`   | **z**-AP     | Bu **s** y         | Kid **s**   |
| &     | `ʃ`   | **v** -e    | abbrevia **Ti** | ru **sh**   |
| zh     | `ʒ`   | **J** acques | ureren     | Gara **g** |
| h      | `h`   | **h** Elp    | en **w**      | a-**h** a!  |

### <a name="english-affricates"></a>Engels affricates

| `sapi` | `ipa` | Voorbeeld 1 | Voorbeeld 2    | Voorbeeld 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **CH** in  | Fu **t** ureren   | Atta-**CH** |
| jh     | `dʒ`  | **j** Oy   | Ori **g** Inal | Oran **g** |

### <a name="english-approximants"></a>Engels approximants

| `sapi` | `ipa` | Voorbeeld 1          | Voorbeeld 2  | Voorbeeld 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**-id, g **l** AD  | PA **l** ACE | Chi **ll** |
| r      | `ɹ`   | **r** Ed, b **r** ING | Bo **RR** | TA **r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Frans suprasegmentals

De telefoonset van de spraak service plaatst stress na de klinker van de benadrukte letter greep. de `fr-FR` set spraak service-telefoonset biedt geen ondersteuning voor de IPA substress ' ˌ '. Als de IPA-subbelasting nodig is, moet u de IPA rechtstreeks gebruiken.

### <a name="french-vowels"></a>Franse klinkers

| `sapi` | `ipa` | Voorbeeld 1     | Voorbeeld 2       | Voorbeeld 3 |
|--------|-------|---------------|-----------------|-----------|
| een      | `a`   | **een** rbre     | p **a** TTE       | IR **a**   |
| aa     | `ɑ`   |               | p **â** te        | p **a** s   |
| AA ~   | `ɑ̃`  | **en** fant    | er ENF **en** t      | t **em** PS |
| AX     | `ə`   |               | p **e** tite      | l **e**    |
| eh     | `ɛ`   | **e** lle      | p **e** RDU       | ét **AI** t |
| eu     | `ø`   | **œu** FS      | CR **EU** ser     | qu-**EU**  |
| EY     | `e`   | ému           | crétin          | ôté       |
| eh ~   | `ɛ̃`  | **im**-poort | p **ein** tleggen    | Passe-**in** |
| iy     | `i`   | **Ik** Dée      | huis dier dat **Ik** te      | ben **Ik**   |
| OE     | `œ`   | **œu** f       | p **EU** r        |           |
| Oh     | `ɔ`   | **o** bstacle  | c **o** RPS       |           |
| Oh ~   | `ɔ̃`  | **op** deze      | r **op** deur     | b **op**   |
| &     | `o`   | **au**-diteur  | b **Eau** versnijden    | p **ô**    |
| OE ~   | `œ̃ ` | **maken**        | l **verwijderen**       | BR **un**  |
| rug     | `u`   | **OE** trage   | intr **OE** vable | **OE**    |
| uy     | `y`   | **u** ne       | p **u** NIR       | él **u**   |

### <a name="french-consonants"></a>Franse mede klinkers

| `sapi` | `ipa` | Voorbeeld 1   | Voorbeeld 2     | Voorbeeld 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b** ête    | ha **b** Ille   | ro **b** e                         |
| d      | `d`   | **d** Ierland    | Loek **d**   | Chau **d** e                       |
| f      | `f`   | **f** Emme   | su **ff** IXe   | Bo **f**                          |
| g      | `g`   | **g** auche  | é **g** ALE     | BA **gu** e                        |
| lopen     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)Park **ING** |
| hy     | `ɥ`   | h **u**/estand   | n **u** Ierland     |                                  |
| k      | `k`   | **c** Arte   | é **c** Aille   | **c** zijn                          |
| l      | `l`   | **l** ong    | é **l** Ierland     | BA **l**                          |
| m      | `m`   | **m** Adam  | AI **m** er     | po **mm** e                        |
| n      | `n`   | **n** organisatie-eenheden    | te **n** IR     | Bo **nn** e                        |
| één     | `ɲ`   |             |               | Pei **GN**-e                       |
| p      | `p`   | **p** Atte   | opnieuw **p** als     | CA-**p**                          |
| r      | `ʁ`   | **r** op     | Cha **r** IOT   | senti **r**                       |
| s      | `s`   | **s** ourir  | een **SS** EZ     | PA **SS** e                        |
| &     | `ʃ`   | hoofd **stuk** | ma **CH** toe   | po **CH** e                        |
| t      | `t`   | **t** ête    | ô **t** er      | nieu **t**                          |
| v      | `v`   | **v**.    | in **v** invoeren  | Rê **v** e                         |
| w      | `w`   | **OE** i     | f-**OE** toe    |                                  |
| y      | `j`   | **y** od     | p **i** étiner  | Mars-**Ille**                    |
| z      | `z`   | * * z * * éro   | Rai **s** onner | ro **s** e                         |
| zh     | `ʒ`   | **j** Ardin  | man **dat** er    | Piè **g**                        |
|        | `n‿`  |             |               | u **n** Arbre                     |
|        | `t‿`  |             |               | Quan **d**                        |
|        | `z‿`  |             |               | di **x**                          |

<a id="fr-1"></a>
**1** *alleen voor sommige buitenlandse woorden.*

> [!TIP]
> De `fr-FR` set spraak service-telefoon biedt geen ondersteuning voor de volgende Franse liasions, `n‿` , `t‿` en `z‿` . Als ze nodig zijn, kunt u overwegen om de IPA rechtstreeks te gebruiken.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Duitse suprasegmentals

| Voor beeld 1 (begin voor medeklinker, woord voor klinker) | Voor beeld 2 (intervocaal-voor-medeklinker, woord in-Nucleus voor klinker) | Voor beeld 3 (CODA voor medeklinker, woord final voor klinker) | Opmerkingen |
|--|--|--|--|
| anders/a **1** n-d AX r s/ | Multiplikationszeichen/m wat l-t iy-p l iy-k a-TS y to **1** n-TS ay-c n/ | Biologie/b iy-OE-l to-g iy **1**/ | Spraak service telefoon instellen stress na de klinker van de benadrukte letter greep |
| Allgemeinwissen/a **2** l-g AX-m ay 1 n-v IH-s n/ | Abfallentsorgungsfirma/a 1 p-f a l-^ eh n t-z Oh **2** AX r-g wat ng s-f IH AX r-m a/ | Computertomographie/k Oh m-p y 1-t AX r-t to-m OE-g r a-f iy **2**/ | Spraak service telefoon instellen stress na de klinker van de sub-benadrukte letter greep |

### <a name="german-vowels"></a>Duitse klinkers

| `sapi` | `ipa`     | Voorbeeld 1                             | Voorbeeld 2     | Voorbeeld 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| één     | `aː`      | **Ber**                              | Maßst **a** b   | Schem **a**                         |
| een      | `a`       | **Een** bfall                            | B **a** CH      | Agath **a**                         |
| Oh     | `ɔ`       | **O** sten                             | PF **o** sten   |                                    |
| eh:    | `ɛː`      | **Ä** hnlichkeit                       | B **ä** r       | [<sup>1</sup>](#de-v-1) Fasci **AE** |
| eh     | `ɛ`       | **ä** ndern                            | ProZ **e** NT   | Amygdal **AE**                      |
| AX     | `ə`       | [<sup>2</sup>](#de-v-2)' v **e** rstauen | Aach **e** n    | Frag **e**                          |
| iy     | `iː`      | **Ik heb** uitgevoerd                              | ABB **IE** gt   | Relativitätstheor **IE**            |
| ih     | `ɪ`       | **Ik** nnung                            | s **Ik** ngen    | Hout **y**                          |
| eu     | `øː`      | **Ö** afzen                              | ABL **ö** sten  | Malm **ö**                          |
| &     | `o`, `oː` | **o** hne                              | Balk **o** n    | Trept                        |
| OE     | `œ`       | **Ö** ffnung                           | BEF **ö** rdern |                                    |
| EY     | `e`, `eː` | **E** Berhard                          | ABF-**e** gt    | b                                  |
| rug     | `uː`      | **U** doen                               | H **u** t       | Akk **u**                           |
| Wat     | `ʊ`       | **U** nterschiedes                     | b **u** NT      |                                    |
| UE     | `yː`      | **Ü** bermut                           | PFL **ü** gt    | Mannen **ü**                           |
| uy     | `ʏ`       | **ü** ppig                             | S **y**-stengel    |                                    |

<a id="de-v-1"></a>
**1** *alleen in woorden van vreemde oorsprong, zoals: fasci **AE**.*<br>
<a id="de-v-2"></a>
**2** *woord-Intially alleen in woorden van vreemde oorsprong, zoals **een** ppointment. Letter greep-in eerste instantie: ' v **e** rstauen.*

### <a name="german-diphthong"></a>Duitse diphthong

| `sapi` | `ipa`       | Voorbeeld 1    | Voorbeeld 2          | Voorbeeld 3 |
|--------|-------------|--------------|--------------------|-----------|
| ay     | `ai`        | **ei** nsam   | Unabhängigk **ei** | Abte **ei** |
| AW     | `au`        | **au**-ßen    | ABB **au** St        | St **au**  |
| oy     | `ɔy`, `ɔʏ̯` | **EU**-phorie | TR **äU** mt         | SCH-**EU** |

### <a name="german-semivowels"></a>Duitse semivowels

| `sapi` | `ipa` | Voorbeeld 1 | Voorbeeld 2    | Voorbeeld 3  |
|--------|-------|-----------|--------------|------------|
| AX r   | `ɐ`   |           | abänd **er** n | Er is **een vergren** deling |

### <a name="german-consonants"></a>Duitse mede klinkers

| `sapi` | `ipa` | Voorbeeld 1 | Voorbeeld 2 | Voorbeeld 3 |
|--|--|--|--|--|
| b | `b` | **B** Ank |  | [<sup>1</sup>](#de-c-1) PU **b** |  |
| c | `ç` | **CH** emie | mögli **CH** St | [<sup>2</sup>](#de-c-2)i/**CH** |
| d | `d` | **d** anken | [<sup>3</sup>](#de-c-3) Len **d** l | [<sup>4</sup>](#de-c-4) Clau **d** e |  |
| jh | `ʤ` | **J** eff | gemana **g** t | [<sup>5</sup>](#de-c-5) Kanaal **g** e |
| f | `f` | **F** ahrtdauer | Angri **ff** slustig | abbruchrei **f** |  |
| g | `g` | **g** UT |  | [<sup>6</sup>](#de-c-6) GRE **g** |  |
| h | `h` | **H** ausanbau |  |  |  |
| y | `j` | **J** od | Reakt | hu |  |
| k | `k` | **K** oma | Aspe **k** t | FLEC **k** |  |
| l | `l` | **l** au | ähne **l** n | zuvie **l** |  |
| m | `m` | **M** UT | A **m** t | Leh **m** |  |
| n | `n` | **n** niet verwijderen | u **n** d | Toch **n** |  |
| lopen | `ŋ` | [<sup>7</sup>](#de-c-7)**ng** Uyen | **Lege** van schwa | R **ING** |  |
| p | `p` | **P** artner | abru **p** t | TI **p** |  |
| PF | `pf` | **PF**-nood herstel | Dam **PF** t | Naar **PF** |  |
| r | `ʀ`, `r`, `ʁ` | **R** Eise | knu **RR** t | Haa **r** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**S** taccato | bi **s** t | Mie **s** |  |
| & | `ʃ` | **Sch** regel | Mi **sch** t | Lappi **sch** |  |
| t | `t` | **T** Raum | S **t** raße | MU **t** |  |
| Terminal | `ts` | **Z** mg | AR **z** t | Wit **z** |  |
| ch | `tʃ` | **Tsch** echien | aufgepu **tsch** t | bundesdeu **tsch** |  |
| v | `v` | **w** inken | Q **u** alle | [<sup>9</sup>](#de-c-9) Go **OO** ve |  |
| x | [<sup>10</sup>](#de-c-10) `x` ,[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12) BA **CH** erach | Ma **CH** t mögli **CH**& St | Schma **CH** ' i **CH** |
| z | `z` | **s** Super |  |  |  |
| zh | `ʒ` | **G** Enre | B ezinski **opnieuw** | Edvi **g** |

<a id="de-c-1"></a>
**1** *alleen in woorden van vreemde oorsprong, zoals: PU **b**.*<br>
<a id="de-c-2"></a>
**2** *zacht "ch" na "e" en "i"*<br>
<a id="de-c-3"></a>
**3** *alleen in woorden van vreemde oorsprong, zoals: len **d** l.*<br>
<a id="de-c-4"></a>
**4** *alleen in woorden van vreemde oorsprong, zoals: Clau **d** e.*<br>
<a id="de-c-5"></a>
**5** *alleen in woorden van vreemde oorsprong, zoals: kanaal **g** e.*<br>
<a id="de-c-6"></a>
**6** *woord-terminalend alleen in woorden van vreemde oorsprong zoals GRE **g**.*<br>
<a id="de-c-7"></a>
**7** *alleen in woorden van vreemde oorsprong, zoals: **ng** Uyen.*<br>
<a id="de-c-8"></a>
**8** *alleen in woorden van vreemde oorsprong, zoals: **S** taccato.*<br>
<a id="de-c-9"></a>
**9** *alleen in woorden van vreemde oorsprong, zoals: go **OO** ve.*<br>
<a id="de-c-10"></a>
**10** *de IPA `x` is een harde ' Ch ' na alle niet-voorste klinkers (a, AA, Oh, wat, en de diphthong AW).*<br>
<a id="de-c-11"></a>
**11** *de IPA `ç` is een zacht ' Ch ' na de voor-klinker (IH, iy, eh, AE, uy, UE, OE, EU ook in diphthongs ay, Oy) en mede klinkers*<br>
<a id="de-c-12"></a>
**12** *woord: in eerste instantie alleen in woorden van vreemde oorsprong, zoals **: J** UAN. Letter greep-in eerste instantie ook in woorden als: BA **CH** erach.*<br>

### <a name="german-oral-consonants"></a>Duitse mondelinge mede klinkers

| `sapi` | `ipa` | Voorbeeld 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich/b AX-^ a 1 x t-l IH c/ |

> [!NOTE]
> We moeten een [GS- \] telefoon toevoegen tussen twee afzonderlijke klinkers, met uitzonde ring van de twee klinkers zijn een authentiek diphthong. Deze orale medeklinker is een glottis slag. Zie voor meer informatie <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank"> glottis slag <span class="docon docon-navigate-external x-hidden-focus"> </a> </a> .

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>Spaanse klinkers

| `sapi` | `ipa` | Voorbeeld 1    | Voorbeeld 2     | Voorbeeld 3    |
|--------|-------|--------------|---------------|--------------|
| een      | `a`   | **een** LTO     | c **a** ntar    | CAS **a**     |
| i      | `i`   | **Ik** bérica  | AV **i** Spa    | belastingen     |
| e      | `e`   | **e** LeFante | bij **e** nKlik    | Elefant **e** |
| o      | `o`   | **o** caso    | ENC **o** ntrar | ocasenc **o** |
| h      | `u`   | **u** sted    | p **u** NTA     | Juanl **u**   |

### <a name="spanish-consonants"></a>Spaanse mede klinkers

| `sapi` | `ipa`      | Voorbeeld 1  | Voorbeeld 2      | Voorbeeld 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b** aobab |                | am **b**        |
|        | `β`        |            | Bao **b** AB     | baoba **b**     |
| ch     | `tʃ`       | **CH** eque | Co **CH** e      | Marraque-**CH** |
| d      | `d`        | **d** Edo   |                | portlan **d**   |
|        | `ð`        |            | de **d** o       | Verda **d**     |
| f      | `f`        | **f** ácil  | ele **vooraf**   | PU **f**        |
| g      | `g`        | **g** Anga  |                | dópin **g**     |
|        | `ɣ`        |            | een **g** UA       | tuare **g**     |
| v      | `j`        | **Ik** Odo   | CAL **-** Ente   | opnieuw **y**        |
| JJ     | `j.j` `jj` |            | VI **ll** a      |                |
| k      | `k`        | **c** oche  | Bo **c** a       | titáni **c**    |
| l      | `l`        | **l** ápiz  | a **l** a        | koord **l**     |
| ll     | `ʎ`        | **ll** Laan  | desarro **ll** o |                |
| m      | `m`        | **m**-volg orde | a **m** AR       | álbu **m**      |
| n      | `n`        | **n** Ada   | CE **n** a       | rató **n**      |
| één     | `ɲ`        | **ñ** aña   | Ara **ñ** azo    |                |
| p      | `p`        | **p** Oca   | naar **p** o       | waarschuwingsd **p**       |
| r      | `ɾ`        |            | CA-**r** a       | Abri **r**      |
| RR     | `r`        | **r** adio  | Co **RR** e      | PU **RR**       |
| s      | `s`        | **s** accountable Care Organization   | VA **s** o       | pelo **s**      |
| t      | `t`        | **t** oldo  | a **t** AR       | Disque **t**    |
| th     | `θ`        | **z** ebra  | een **z** UL       | lápi **z**      |
| w      | `w`        | h **u** ESO  | AG **u** a       | Gua **u**       |
| x      | `x`        | **j** OTA   | een **j** o        | relo **j**      |

> [!TIP]
> De `es-ES` set spraak service-telefoon biedt geen ondersteuning voor de volgende Spaanse IPA, `β` , `ð` en `ɣ` . Als ze nodig zijn, kunt u overwegen om de IPA rechtstreeks te gebruiken.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

Het telefoon nummer van de spraak service voor `zh-CN` is gebaseerd op de ingebouwde telefoon- <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">pinyinset </a> .

### <a name="tone"></a>Toon

| Pinyin Toon | `sapi` | Teken voorbeeld |
|-------------|--------|-------------------|
| mā          | MA 1  | 妈                 |
| má          | ma 2  | 麻                 |
| mǎ          | Ma 3  | 马                 |
| mà          | ma 4  | 骂                 |
| VG          | ma 5  | 嘛                 |

#### <a name="example"></a>Voorbeeld

| Teken | Speech Service                |
|-----------|-------------------------------|
| 组织关系      | zu 3-Zhi 1-Guan 1-XI 5 |
| 累进        | lei 3-Jin 4                 |
| 西宅巷       | XI 1-Zhai 2-Xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

Het telefoon nummer van de spraak service voor `zh-TW` is gebaseerd op de systeem eigen telefoon- <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">Bopomofo </a> -set.

### <a name="tone"></a>Toon

| Toon van Speech-Service | Bopomofo Toon | Voor beeld (woord) | Spraak service-telefoons | Bopomofo | Pinyin (拼音) |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | leeg         | 偵              | ㄓㄣˉ                   | ㄓㄣ       | zhēn        |
| ˊ                   | ˊ             | 察              | ㄔㄚˊ                   | ㄔㄚˊ      | chá         |
| ˇ                   | ˇ             | 打              | ㄉㄚˇ                   | ㄉㄚˇ      | dǎ          |
| ˋ                   | ˋ             | 望              | ㄨㄤˋ                   | ㄨㄤˋ      | wàng        |
| ˙                   | ˙             | 影子             | 一ㄥˇ ㄗ˙               | 一ㄥˇ ㄗ˙  | yǐng Zi    |

#### <a name="example"></a>Voorbeeld

| Teken | `sapi`   |
|-----------|----------|
| 狗         | ㄍㄡˇ      |
| 然后        | ㄖㄢˊㄏㄡˋ   |
| 剪掉        | ㄐㄧㄢˇㄉㄧㄠˋ |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

Het telefoon nummer van de spraak service voor `ja-JP` is gebaseerd op de systeem eigen telefoon <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">Kana </a> -set.

### <a name="stress"></a>Uitvoert

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ` mainstress |
| `+`    | `ˌ` substress  |

#### <a name="example"></a>Voorbeeld

| Teken | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | ゴ'ウセ    | Ga naar ˈ wɯseji   |
| 所有者       | ショュ'ウ?ャ | ɕjojɯˈwɯɕja |
| 最適化       | サィテキカ +  | sajitecikaˌ |

***