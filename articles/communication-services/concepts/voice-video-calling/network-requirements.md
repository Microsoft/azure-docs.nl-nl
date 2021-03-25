---
title: Het netwerk van uw organisatie voorbereiden voor Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over de netwerk vereisten voor spraak-en video gesprekken van Azure Communication Services
author: nmurav
manager: jken
services: azure-communication-services
ms.author: nmurav
ms.date: 3/23/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 753bb7a88f8032d6ed9aeac1b1adf4f34d7cec43
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105051339"
---
# <a name="ensure-high-quality-media-in-azure-communication-services"></a>Media van hoge kwaliteit garanderen in azure Communication Services

Dit document bevat een overzicht van de factoren en aanbevolen procedures die moeten worden overwogen bij het bouwen van multimedia communicatie van hoge kwaliteit met Azure Communication Services.

## <a name="factors-that-affect-media-quality-and-reliability"></a>Factoren die van invloed zijn op de kwaliteit en betrouw baarheid van media

Er zijn veel verschillende factoren die bijdragen aan de real-time media van Azure Communication Services (de kwaliteit van audio, video en het delen van toepassingen). Dit zijn onder andere netwerk kwaliteit, band breedte, firewall, host en apparaatconfiguratie.


### <a name="network-quality"></a>Netwerk kwaliteit

De kwaliteit van real-time media via IP wordt aanzienlijk beïnvloed door de kwaliteit van de onderliggende netwerk verbinding, maar met name door de hoeveelheid:
* **Latentie**. Dit is de tijd die nodig is om een IP-pakket te verkrijgen van punt A naar punt B in het netwerk. Deze vertraging voor netwerk doorgifte wordt bepaald door de fysieke afstand tussen de twee punten en eventuele extra overhead die wordt gemaakt door de apparaten die door het verkeer worden doorgevoerd. De latentie wordt gemeten als eenrichtings-of round-trip tijd (RTT).
* **Pakket verlies**. Een percentage van pakketten die verloren zijn gegaan in een bepaald tijd venster. Pakket verlies is rechtstreeks van invloed op de geluids kwaliteit, van kleine, afzonderlijke verloren pakketten die bijna geen invloed hebben op back-to-back burst-verliezen die een volledige audio-uitsnede veroorzaken.
* **Ontvangst-jitter tussen pakketten of eenvoudigweg jitter**. Dit is de gemiddelde wijziging in de vertraging tussen opeenvolgende pakketten. Azure Communication Services kan worden aangepast aan bepaalde niveaus van jitter-door buffers. Het wordt alleen weer gegeven wanneer de jitter de buffer overschrijdt die een deel nemer op zijn effecten zal waarnemen.

### <a name="network-bandwidth"></a>Netwerkbandbreedte

Zorg ervoor dat uw netwerk is geconfigureerd voor de ondersteuning van de band breedte die is vereist voor gelijktijdige media sessies van Azure Communication Services en andere zakelijke toepassingen. Het testen van het end-to-end-netwerkpad voor bandbreedte knelpunten is van cruciaal belang voor de succes volle implementatie van uw multi media Communication Services-oplossing.

Hieronder vindt u de vereisten voor de band breedte voor de Java script-client bibliotheken:

|Bandbreedte|Scenario's|
|:--|:--|
|40 kbps|Peer-to-peer-audio aanroepen|
|500 Kbps|Peer-to-peer-audio aanroepen en scherm delen|
|500 Kbps|Video voor peer-to-peer-kwaliteit die 360p op 30fps aanroept|
|1,2 Mbps|Video die peer-to-peer-HD-kwaliteit aanroept met een resolutie van HD 720p op 30fps|
|500 Kbps|Groeps video die 360p aanroept op 30fps|
|1,2 Mbps|HD Group video met een resolutie van HD 720p op 30fps| 

Hieronder vindt u de vereisten voor band breedte voor de systeem eigen Android-en iOS-client bibliotheken:

|Bandbreedte|Scenario's|
|:--|:--|
|30 kbps|Peer-to-peer-audio aanroepen |
|130 kbps|Peer-to-peer-audio aanroepen en scherm delen|
|500 Kbps|Video voor peer-to-peer-kwaliteit die 360p op 30fps aanroept|
|1,2 Mbps|Video die peer-to-peer-HD-kwaliteit aanroept met een resolutie van HD 720p op 30fps|
|1,5 Mbps|Video die peer-to-peer-HD-kwaliteit aanroept met resolutie van HD 1080p op 30fps |
|500kbps/1Mbps|Groeps video aanroepen|
|1Mbps/2Mbps|HD-groep video-aanroepen (540p-Video's op 1080p-scherm)|

### <a name="firewalls-configuration"></a>Configuratie van Firewall (s)

Azure Communication Services-verbindingen vereisen Internet connectiviteit met specifieke poorten en IP-adressen om multimedia ervaringen van hoge kwaliteit te kunnen leveren. Als u geen toegang hebt tot deze poorten en IP-adressen, kan Azure Communication Services nog steeds werken. De optimale ervaring wordt echter wel gegeven wanneer de aanbevolen poorten en IP-bereiken open zijn.

| Categorie | IP-adresbereiken of FQDN | Poorten | 
| :-- | :-- | :-- |
| Media verkeer | [Bereik van IP-adressen van open bare Azure-Cloud](https://www.microsoft.com/download/confirmation.aspx?id=56519) | UDP 3478 tot en met 3481, TCP-poorten 443 |
| Signa lering, telemetrie, registratie| *. skype.com, *. microsoft.com, *. azure.net, *. azureedge.net, *. office.com, *. trouter.io | TCP 443, 80 |

### <a name="network-optimization"></a>Netwerk optimalisatie

De volgende taken zijn optioneel en zijn niet vereist voor het implementeren van Azure Communication Services. Gebruik deze richt lijnen om de prestaties van uw netwerk en Azure Communication Services te optimaliseren of als u weet dat er beperkingen voor het netwerk zijn.
U kunt het beste verder optimaliseren als:
* Azure Communication Services wordt traag uitgevoerd (misschien hebt u onvoldoende band breedte)
* Aanroepen blijvend neergezet (mogelijk vanwege firewall-of proxy blok keringen)
* Aanroepen hebben statisch en uitgesneden of stemmen goed in als robots (kunnen jitter of pakket verlies zijn)

| Netwerk optimalisatie taak | Details |
| :-- | :-- |
| Uw netwerk plannen | In deze documentatie vindt u minimale vereisten voor uw netwerk voor aanroepen. Raadpleeg het [voor beeld van teams voor het plannen van uw netwerk](https://docs.microsoft.com/microsoftteams/tutorial-network-planner-example) |
| Externe naam omzetting | Zorg ervoor dat alle computers waarop de client bibliotheken van Azure Communications Services worden uitgevoerd, externe DNS-query's kunnen oplossen om de services te detecteren die worden geboden door Azure Communication Services en dat uw firewalls geen toegang verhinderen. Controleer of de client bibliotheken adressen kunnen omzetten *. skype.com, *. microsoft.com, *. azure.net, *. azureedge.net, *. office.com, *. trouter.io  |
| Sessie persistentie onderhouden | Zorg ervoor dat uw firewall de toegewezen NAT-adressen (Network Address Translation) of-poorten voor UDP niet wijzigt
Grootte van NAT-groep valideren | Valideer de grootte van de groep voor de Network Address Translation (NAT) die vereist is voor de connectiviteit van de gebruiker. Als meerdere gebruikers en apparaten Azure Communication Services gebruiken met [NAT (Network Address Translation) of Pat (netwerkadresomzetting)](https://docs.microsoft.com/office365/enterprise/nat-support-with-office-365), zorg er dan voor dat de apparaten verborgen achter elk openbaar routeerbaar IP-adres niet groter zijn dan het ondersteunde aantal. Zorg ervoor dat er voldoende open bare IP-adressen zijn toegewezen aan de NAT-groepen om te voor komen dat de poort wordt uitgeput. Poort uitputting draagt bij aan interne gebruikers en apparaten die geen verbinding kunnen maken met de Azure Communication Services |
| Richt lijnen voor detectie en preventie van inbraak | Als uw omgeving een [Indringings detectie](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools) of preventie systeem (ID'S/ip's) heeft die zijn geïmplementeerd voor een extra beveiligingslaag voor uitgaande verbindingen, alle Azure Communication Services-url's toestaan |
| VPN voor gesplitste tunnel configureren | Het is raadzaam dat u een alternatief pad voor teams verkeer opgeeft dat het virtuele particuliere netwerk (VPN) omzeilt, ook wel [gesplitste tunnel VPN](https://docs.microsoft.com/windows/security/identity-protection/vpn/vpn-routing)genoemd. Gesplitste tunneling houdt in dat verkeer voor Azure Communications Services niet via het VPN verloopt, maar rechtstreeks naar Azure gaat. Het omzeilen van uw VPN heeft een positieve invloed op de kwaliteit van de media en vermindert de belasting van de VPN-apparaten en het netwerk van de organisatie. Als u een split-tunnel VPN wilt implementeren, moet u samen werken met uw VPN-leverancier. Andere redenen voor het omzeilen van het VPN wordt aangeraden: <ul><li> Vpn's zijn normaal gesp roken niet ontworpen of geconfigureerd voor de ondersteuning van real-time media.</li><li> Vpn's bieden mogelijk ook geen ondersteuning voor UDP (wat vereist is voor Azure Communication Services)</li><li>Vpn's introduceren ook een extra laag versleuteling boven op het media verkeer dat al is versleuteld.</li><li>De connectiviteit met Azure Communication Services is mogelijk niet efficiënt vanwege het vastmaken van verkeer via een VPN-apparaat.</li></ul>|
| QoS implementeren | [Gebruik Quality of service (QoS)](https://docs.microsoft.com/microsoftteams/qos-in-teams) voor het configureren van de pakket prioriteit. Dit verbetert de gespreks kwaliteit en helpt u bij het controleren en oplossen van gespreks kwaliteit. QoS moet worden geïmplementeerd op alle segmenten van een beheerd netwerk. Zelfs wanneer een netwerk voldoende is ingericht voor band breedte, biedt QoS een risico beperking in het geval van onverwachte netwerk gebeurtenissen. Met QoS hebben spraak verkeer prioriteit, zodat deze onverwachte gebeurtenissen geen negatieve invloed hebben op de kwaliteit. | 
| WiFi optimaliseren | Net als bij VPN zijn WiFi-netwerken niet noodzakelijkerwijs ontworpen of geconfigureerd voor het ondersteunen van real-time media. Het plannen van of het optimaliseren van een WiFi-netwerk ter ondersteuning van Azure Communication Services is een belang rijke overweging voor de implementatie van een hoge kwaliteit. Houd rekening met de volgende factoren: <ul><li>Implementeer QoS of WiFi Multi Media (WMM) om ervoor te zorgen dat media verkeer op de juiste wijze wordt opgehaald over uw WiFi-netwerken.</li><li>De WiFi-Bande rollen en de plaatsing van toegangs punten plannen en optimaliseren. Het bereik van 2,4 GHz kan een voldoende ervaring bieden, afhankelijk van de plaatsing van toegangs punten, maar toegangs punten worden vaak beïnvloed door andere consumenten apparaten die in dat bereik werken. Het bereik van 5 GHz is beter geschikt voor realtime media vanwege het compacte bereik, maar er zijn meer toegangs punten nodig om voldoende dekking te verkrijgen. Eind punten moeten ook het bereik ondersteunen en moeten zo worden geconfigureerd dat deze stroken dienovereenkomstig kunnen worden gebruikt.</li><li>Als u dual-band WiFi-netwerken gebruikt, kunt u overwegen om een band besturing te implementeren. Band Stuur module is een techniek die door WiFi-leveranciers wordt geïmplementeerd om de dual-band clients te laten werken met het 5 GHz-bereik.</li><li>Wanneer de toegangs punten van hetzelfde kanaal te dicht bij elkaar staan, kan dit leiden tot een signaal overlapping en een onbedoelde concurrentie positie, wat resulteert in een gedegradeerde gebruikers ervaring. Zorg ervoor dat toegangs punten die zich naast elkaar bevinden op kanalen die elkaar niet overlappen.</li></ul> Elke draadloze leverancier heeft zijn eigen aanbevelingen voor het implementeren van de draadloze oplossing. Neem contact op met uw WiFi-leverancier voor specifieke richt lijnen.|



### <a name="operating-system-and-browsers-for-javascript-client-libraries"></a>Besturings systeem en browsers (voor Java script-client bibliotheken)

Client bibliotheken voor spraak/video van Azure Communication Services ondersteunen bepaalde besturings systemen en browsers.
Meer informatie over de besturings systemen en browsers die door de aanroepende client bibliotheken worden ondersteund in de [conceptuele documentatie](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features).

## <a name="next-steps"></a>Volgende stappen

De volgende documenten zijn mogelijk interessant voor u:

- Meer informatie over het [aanroepen van bibliotheken](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features)
- Meer informatie over [Client-serverarchitectuur](https://docs.microsoft.com/azure/communication-services/concepts/client-and-server-architecture)
- Meer informatie over [topologieën voor oproep stromen](https://docs.microsoft.com/azure/communication-services/concepts/call-flows)
