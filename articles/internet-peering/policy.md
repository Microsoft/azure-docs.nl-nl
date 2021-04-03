---
title: Beleid voor micro soft-peering
titleSuffix: Azure
description: Beleid voor micro soft-peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bee41bb8e5beb4df3086ab50499cb185a83e4efe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592326"
---
# <a name="peering-policy"></a>Peeringbeleid
Micro soft onderhoudt een selectief beleid voor peering dat is ontworpen om te zorgen voor de best mogelijke klant ervaring die wordt ondersteund door de industrie normen en aanbevolen procedures, schalen voor toekomstige vraag en strategische plaatsing van peering. Micro soft behoudt zich het recht voor om uitzonde ringen op het beleid te maken als dat nodig is. De algemene vereisten van micro soft van uw netwerk worden uitgelegd in de volgende secties. Deze zijn van toepassing op aanvragen voor directe peering en uitwisseling van peering. 

## <a name="technical-requirements"></a>Technische vereisten

* Een volledig redundant netwerk met voldoende capaciteit voor het uitwisselen van verkeer zonder congestie.
* Peer heeft een openbaar routeerbaar autonoom systeem nummer (ASN).
* Zowel IPv4 als IPv6 worden ondersteund en micro soft verwacht sessies van beide typen op elke peering-locatie te maken.
* MD5 wordt niet ondersteund.
* **ASN-Details:**

    * Micro soft beheert AS8075 samen met de volgende Asn's: AS8068, AS8069, AS12076. Voor een volledige lijst met Asn's met AS8075-peering, verwijzen naar micro soft.
    * Alle partijen die met micro soft overeenkomen, accepteren geen routes van AS12076 (Express route) onder geen enkele omstandigheden en moeten AS12076 op alle peers uitfilteren.

* **Routerings beleid:**
    * De peer heeft ten minste één openbaar routeerbaar/24.
    * Micro soft overschrijft ontvangen multi-exit Discriminators (MED).
    * Micro soft geeft de voor keur aan het ontvangen van BGP Community-Tags van peers om de route oorsprong aan te geven.
    * Het is aan te raden peers een max-prefix van 2000 (IPv4) en 500 (IPv6)-routes in te stellen op peering-sessies met micro soft.
    * Tenzij specifiek vooraf overeengekomen, moeten peers naar verwachting consistente routes aankondigen op alle locaties waar ze met micro soft worden gepeerd.
    * Over het algemeen worden peering-sessies met AS8075 alle routes van micro soft aangekondigd. Micro soft kan sommige regionale specifieke regio's aankondigen.
    * Geen van beide partijen stelt een statische route, een route van de laatste redmiddel of het verzenden van verkeer naar de andere partij in voor een route die niet via BGP is aangekondigd.
    * Peer is verplicht hun routes te registreren in een IR-data base (open bare Internet Routing) voor het filteren van het doel en deze gegevens actueel te houden.      
    * Peers voldoen aan de MANRS industrie normen voor route beveiliging.  Micro soft kan op zijn eigen wijze kiezen voor: 1.) geen peering tot stand brengen met bedrijven die geen routes hebben ondertekend en geregistreerd. 2.) als u ongeldige RPKI-routes wilt verwijderen; 3.) geen routes accepteren van gevestigde peers die niet zijn geregistreerd en ondertekend. 

## <a name="operational-requirements"></a>Operationele vereisten
* Een volledig personeel van 24x7 netwerk Operations Center (NOC), dat kan helpen bij de oplossing van alle technische en prestatie problemen, beveiligings schendingen, denial of service-aanvallen of andere misbruik die afkomstig is van de peer of hun klanten.
* Peers wordt verwacht een volledig en bijgewerkt profiel op [PeeringDB](https://www.peeringdb.com) te hebben, inclusief een 24x7 NOCse-mail van het bedrijfs domein en telefoon nummer. We gebruiken deze informatie om de details van de peer te valideren, zoals NOC-informatie, technische contact gegevens en hun aanwezigheid bij de peering-faciliteiten, enzovoort. Persoonlijke Yahoo-, Gmail-en Hotmail-accounts worden niet geaccepteerd.

## <a name="physical-connection-requirements"></a>Vereisten voor fysieke verbinding
* De locaties waar u verbinding kunt maken met micro soft voor directe peering of Exchange-peering, worden weer gegeven in [PeeringDB](https://www.peeringdb.com/net/694)

* **Exchange-peering:**
    * Voor peers wordt verwacht dat ze mini maal een 10 GB-verbinding met de Exchange hebben.
    * Bij peers wordt verwacht dat hun poorten worden bijgewerkt wanneer het piek gebruik 50% overschrijdt.
    * Micro soft moedigt peers aan om verschillende connectiviteit met Exchange te onderhouden om failover-scenario's te ondersteunen.

* **Directe peering:**
    * Interconnectie moet groter zijn dan 1-glas vezel met 100 Gbps optische modus.
    * Micro soft brengt alleen directe peering tot stand met ISP-of netwerk serviceproviders.
    * Bij peers wordt verwacht dat hun poorten worden bijgewerkt wanneer het piek gebruik 50% overschrijdt en de diverse capaciteit in elke metro lijn op één locatie of op verschillende locaties in een metro punt wordt gehandhaafd.
    * Elke directe peering bestaat uit twee verbindingen met twee micro soft Edge-routers van de routers van de peer die zich in de rand van de peer bevinden. Micro soft vereist twee BGP-sessies over deze verbindingen. De peer kan ervoor kiezen om geen redundante apparaten te implementeren.


## <a name="traffic-requirements"></a>Verkeers vereisten

* Peers via Exchange peering moeten mini maal 500 MB aan verkeer hebben en minder dan 2 GB. Voor verkeer van meer dan 2 GB moet direct gelijkwaardig worden gemaakt.
* Micro soft vereist mini maal 2 GB voor directe peering. Elke onderling overeengekomen locatie van peering moet failover ondersteunen die ervoor zorgt dat peering tijdens een failover-scenario lokaal blijft. 

## <a name="next-steps"></a>Volgende stappen

* Voor meer informatie over de stappen om direct peering in te stellen met micro soft, volgt u het [scenario voor directe peering](walkthrough-direct-all.md).
* Voor meer informatie over de stappen voor het instellen van Exchange-peering met micro soft, volgt u het [scenario voor Exchange-peering](walkthrough-exchange-all.md).
