---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "66244640"
---
| Entiteit                                       | Conventies   |
|----------------------------------------------|-----------------------------------------------------|
| Container namen voor blok-Blob en pagina-BLOB | Moet een geldige DNS-naam zijn van 3 tot 63 tekens lang zijn. <br>  Moet beginnen met een letter of cijfer. <br> Mag alleen kleine letters, cijfers en het koppel teken (-) bevatten. <br> Elk koppelteken (-) moet direct worden voorafgegaan en gevolgd door een letter of cijfer. <br> Opeenvolgende afbreek streepjes zijn niet toegestaan in namen. |
| Share namen voor Azure files                  | Hetzelfde als hierboven                                          |
| Map-en bestands namen voor Azure files     |<li> Case: behouden, hoofdletter gevoelig en mag niet langer zijn dan 255 tekens. </li><li> Kan niet eindigen met een slash (/). </li><li>Als dit wordt gegeven, wordt deze automatisch verwijderd. </li><li> De volgende tekens zijn niet toegestaan: <code>" \\ / : \| < > * ?</code></li><li> Gereserveerde tekens voor URL's moeten op de juiste wijze van een escape-teken zijn voorzien. </li><li> Ongeldige tekens voor URL-paden zijn niet toegestaan. Code punten zoals \\ uE000 zijn geen geldige Unicode-tekens. Sommige ASCII-of Unicode-tekens, zoals Stuur codes (0x00 tot 0x1F, \\ u0081, enzovoort), zijn ook niet toegestaan. Zie RFC 2616, Section 2,2: Basic rules and RFC 3987 (Engelstalig) voor regels voor het Unicode-teken reeksen in HTTP/1.1. </li><li> De volgende bestands namen zijn niet toegestaan: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK $, punt teken (.) en twee punten (..).</li>|
| Blobnamen voor blok-blob en pagina-blob      | </li><li>Blobnamen zijn hoofdlettergevoelig en kunnen elke combinatie van tekens bevatten. </li><li>Een blobnaam moet 1 tot 1024 tekens bevatten. </li><li>Gereserveerde tekens voor URL's moeten op de juiste wijze van een escape-teken zijn voorzien. </li><li>Het aantal padsegmenten dat de blobnaam omvat, mag niet meer dan 254 zijn. Een padsegment is de tekenreeks tussen opeenvolgende scheidingstekens (bijvoorbeeld de slash '/') die overeenkomt met de naam van een virtuele map.</li> |
