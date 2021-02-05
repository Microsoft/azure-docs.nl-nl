---
title: Topologieën voor stroom aanroepen in azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over topologieën voor oproep stromen in azure Communication Services.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 12/11/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 1333bd08f8a79969817bcb21aa4580d1994d09ce
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594721"
---
# <a name="call-flow-topologies"></a>Topologieën voor oproep stromen
In dit artikel worden de topologieën van Azure Communication Services-aanroep flow beschreven. Dit is een geweldig artikel om te controleren of u een zakelijke klant bent die communicatie Services integreert in een netwerk dat u beheert. Ga naar de [conceptuele documentatie van oproep stromen](./call-flows.md)voor een inleiding tot communicatie services aanroepen.

## <a name="background"></a>Achtergrond

### <a name="network-concepts"></a>Netwerk concepten

Voordat u de topologie van de aanroep flow bekijkt, definieert u een aantal voor waarden die in het hele document worden genoemd.

Een **klant netwerk** bevat netwerk segmenten die u beheert. Dit kan bekabelde en draadloze netwerken binnen uw kantoor of tussen kant oren, data centers en Internet serviceproviders zijn.

Een klant netwerk heeft meestal verschillende netwerk verbindingen met firewalls en/of proxy servers die het beveiligings beleid van uw organisatie afdwingen. We raden u aan een [uitgebreide netwerk beoordeling](https://docs.microsoft.com/microsoftteams/3-envision-evaluate-my-environment) uit te voeren om optimale prestaties en kwaliteit van uw communicatie oplossing te garanderen.

Het **communicatie Services-netwerk** is het netwerk segment dat Azure Communication Services ondersteunt. Dit netwerk wordt beheerd door micro soft en wordt wereld wijd gedistribueerd met randen dicht bij de meeste netwerken van de klant. Dit netwerk is verantwoordelijk voor transport relay, media verwerking voor groeps aanroepen en andere onderdelen die ondersteuning bieden voor uitgebreide realtime media communicatie.

### <a name="types-of-traffic"></a>Typen verkeer

Communicatie services worden voornamelijk gebouwd op twee soorten verkeer: **realtime media** en **signalering**.

**Realtime media** worden verzonden met behulp van het real-time Transport Protocol (RTP). Dit protocol biedt ondersteuning voor de overdracht van gegevens van audio, video en scherm delen. Deze gegevens zijn gevoelig voor problemen met de netwerk latentie. Hoewel het mogelijk is om realtime media te verzenden met behulp van TCP of HTTP, raden we u aan UDP als het Trans Port-laag protocol te gebruiken om de ervaringen van de eind gebruikers te ondersteunen. Nettoladingen van media die via RTP worden verzonden, worden beveiligd met SRTP.

Gebruikers van uw communicatie Services-oplossing maken verbinding met uw services vanaf hun client apparaten. Communicatie tussen deze apparaten en uw servers wordt verwerkt met **Signa lering**. Bijvoorbeeld: het starten van aanroepen en real-time chat wordt ondersteund door een Signa lering tussen apparaten en uw service. Het meeste signalerings verkeer maakt gebruik van HTTPS REST, hoewel in sommige scenario's SIP kan worden gebruikt als signalerings Traffic-protocol. Hoewel dit type verkeer minder gevoelig is voor latentie, worden de gebruikers van uw oplossing voor een prettige eindgebruikers ervaring gesignaleerd.

### <a name="interoperability-restrictions"></a>Interoperabiliteits beperkingen

Media stromen via communicatie services worden als volgt beperkt:

**Media relays van derden.** Interoperabiliteit met een media relay van derden wordt niet ondersteund. Als een van uw media-eind punten communicatie Services is, kan uw media alleen door micro soft-systeem eigen media relays bladeren, met inbegrip van de apparaten die ondersteuning bieden voor micro soft-teams en Skype voor bedrijven.

Een sessie rand controller (SBC) van derden op de grens met PSTN moet de RTP-RTCP beëindigen, beveiligd met SRTP en de stroom niet door geven aan de volgende hop. Als u de stroom naar de volgende hop doorstuurt, kan deze niet worden begrepen.

**SIP-proxy servers van derden.** Een communicatie service-SIP-dialoog venster met een SBC en/of gateway van derden kan micro soft native SIP-proxy's passeren (net als teams). Interoperabiliteit met SIP-proxy's van derden wordt niet ondersteund.

**B2BUA (of SBC) van derden.** De media stroom van communicatie Services naar en van het PSTN wordt beëindigd door een niet-micro soft-onafhankelijke SBC. Interoperabiliteit met een SBC van derden in het communicatie Services-netwerk (waarbij SBC twee communicatie Services-eind punten heeft) wordt niet ondersteund.

### <a name="unsupported-technologies"></a>Niet-ondersteunde technologieën

**VPN-netwerk.** Communicatie Services biedt geen ondersteuning voor het verzenden van media via Vpn's. Als uw gebruikers VPN-clients gebruiken, moet de client media verkeer splitsen en omleiden via een niet-VPN-verbinding zoals opgegeven in het [inschakelen van Lync-media om een VPN-tunnel over te slaan.](https://techcommunity.microsoft.com/t5/skype-for-business-blog/enabling-lync-media-to-bypass-a-vpn-tunnel/ba-p/620210)

*Eraan. Hoewel de titel Lync aanduidt, is deze van toepassing op Azure Communication Services en teams.*

**Pakket vormers.** Pakket Knip, pakket inspectie of pakket vorm apparaten worden niet ondersteund en de kwaliteit kan aanzienlijk afnemen.

### <a name="call-flow-principles"></a>Stroom principes aanroepen

Er zijn vier algemene principes voor het bouwen van stroom communicatie services-aanroepen:

* **De eerste deel nemer van de aanroep van een communicatie Services-groep bepaalt de regio waarin de aanroep wordt gehost**. Er zijn uitzonde ringen op deze regel in enkele topologieën, die hieronder worden beschreven.
* **Het Media-eind punt dat wordt gebruikt om een communicatie Services-oproep te ondersteunen, wordt geselecteerd op basis van de benodigde media verwerking** en wordt niet beïnvloed door het aantal deel nemers aan een aanroep. Een punt-naar-punt-oproep kan bijvoorbeeld een media-eind punt in de Cloud gebruiken om media te verwerken voor transcriptie of-opnamen, terwijl een oproep met twee deel nemers geen media-eind punten mag gebruiken. Groeps aanroepen gebruiken een media-eind punt voor het mixen en routeren. Dit eind punt wordt geselecteerd op basis van de regio waarin de aanroep wordt gehost. Media verkeer dat vanaf een client naar het Media-eind punt wordt verzonden, kan rechtstreeks worden gerouteerd of kan gebruikmaken van een transport relay in azure als de firewall beperkingen van het klanten netwerk dit vereisen.
* **Media verkeer voor peer-to-peer-aanroepen is de meest direct beschik bare route**, ervan uitgaande dat de aanroep geen media-eind punt in de Cloud nodig heeft. De voorkeurs route is direct naar de externe peer (client). Als er geen directe route beschikbaar is, stuurt een of meer transport Relais verkeer door. Media verkeer mag geen gekantelde servers zijn die fungeren als Packet-vormers, VPN-servers of andere functies die de verwerking van de eind gebruiker kunnen vertragen.
* **Signalerings verkeer gaat altijd naar de server die het dichtst bij de gebruiker ligt.**

Raadpleeg de [conceptuele documentatie van de aanroep stromen](./call-flows.md)voor meer informatie over de details van het Media-pad dat wordt gekozen.


## <a name="call-flows-in-various-topologies"></a>Stroom aanroepen in verschillende topologieën

### <a name="communication-services-internet"></a>Communicatie Services (Internet)

Deze topologie wordt gebruikt door klanten die communicatie services uit de Cloud gebruiken zonder een on-premises implementatie, zoals SIP-interface. In deze topologie loopt verkeer van en naar communicatie Services via internet.

:::image type="content" source="./media/call-flows/detailed-flow-general.png" alt-text="Azure Communication Services-topologie.":::

*Afbeelding 1-topologie van communicatie Services*

De richting van de pijlen in het bovenstaande diagram weerspiegelt de initiatie richting van de communicatie die van invloed is op de connectiviteit op de bedrijfs verbindingen. In het geval van UDP voor media, kan de eerste pakket (en) in omgekeerde richting stromen, maar deze pakketten kunnen worden geblokkeerd totdat pakketten in de andere richting stromen.

Beschrijvingen van de stroom:
* Stroom 2 *: vertegenwoordigt een stroom die door een gebruiker in het netwerk van de klant naar het Internet wordt geïnitieerd als onderdeel van de communicatie Services-ervaring van de gebruiker. Voor beelden van deze stromen zijn DNS en peer-to-peer-verzen ding van media.
* Stroom 2: geeft een stroom aan die wordt geïnitieerd door een externe Mobile Communication Services-gebruiker, met VPN naar het netwerk van de klant.
* Stroom 3: vertegenwoordigt een stroom die door een externe Mobile Communication Services-gebruiker wordt geïnitieerd voor communicatie Services-eind punten.
* Stroom 4: vertegenwoordigt een stroom die door een gebruiker in het klant netwerk is geïnitieerd voor communicatie Services.
* Stroom 5: vertegenwoordigt een peer-to-peer-media stroom tussen een communicatie Services-gebruiker en een andere in het netwerk van de klant.
* Flow 6: vertegenwoordigt een peer-to-peer-media stroom tussen een externe Mobile Communication Services-gebruiker en een andere externe Mobile Communication Services-gebruiker via internet.

### <a name="use-case-one-to-one"></a>Use-case: een-op-een

Een-op-een-aanroepen gebruiken een gemeen schappelijk model waarin de oproepende functie een set kandidaten verkrijgt die bestaat uit IP-adressen/poorten, waaronder lokale, Relay en reflexief (openbaar IP-adres van de client, zoals gezien door de relay) kandidaten. De beller verzendt deze kandidaten naar de aangeroepen partij; de aangeroepen partij verkrijgt ook een vergelijk bare set kandidaten en verzendt deze naar de oproepende functie. STUN-connectiviteits controle berichten worden gebruikt om te bepalen welke media paden van de aanroepende partij worden uitgevoerd en hoe het beste werkende pad wordt geselecteerd. Media (dat wil zeggen, RTP/RTCP-pakketten die zijn beveiligd met SRTP) worden vervolgens verzonden met het geselecteerde kandidaat-paar. De transport relay wordt geïmplementeerd als onderdeel van Azure Communication Services.

Als de lokale IP-adres/poort kandidaten of de reflexieve kandidaten verbinding hebben, wordt het directe pad tussen de clients (of via een NAT) geselecteerd voor media. Als de clients zich in het netwerk van de klant bevinden, moet u het directe pad selecteren. Hiervoor is directe UDP-connectiviteit in het netwerk van de klant vereist. Als de clients zowel Nomadic als Cloud gebruikers zijn, is het mogelijk dat Media rechtstreeks verbinding maken, afhankelijk van de NAT/firewall.

Als een van de clients intern is in het netwerk van de klant en één client extern is (bijvoorbeeld een gebruiker met een mobiele Cloud), is het niet waarschijnlijk dat de directe connectiviteit tussen de lokale of reflexieve kandidaten werkt. In dit geval is het gebruik van een van de kandidaten voor transport Relais van een client (bijvoorbeeld omdat de interne client een relay-kandidaat heeft verkregen van de transport relay in Azure. de externe client moet STUN/RTP/RTCP-pakketten naar de transport relay verzenden). Een andere optie is de interne client die wordt verzonden naar de relay-kandidaat die is verkregen door de mobiele cloud client. Hoewel UDP-connectiviteit voor media sterk wordt aanbevolen, wordt TCP ondersteund.

**Stappen op hoog niveau:**
1.  Communicatie Services gebruiker A lost de URL-domein naam (DNS) op met behulp van Flow 2.
2.  Gebruiker A wijst een media relay-poort toe aan het Trans Port van teams via flow 4.
3.  Communicatie Services gebruiker A verzendt een ' invite ' met ijs-kandidaten met stroom 4 naar communicatie Services.
4.  Communicatie Services waarschuwt gebruiker B met behulp van flow 4.
5.  Gebruiker B wijst een media relay-poort toe aan het Trans Port van teams via flow 4.
6.  Gebruiker B verzendt ' antwoord ' met ICE-kandidaten met behulp van flow 4, die wordt teruggestuurd naar gebruiker A met behulp van flow 4.
7.  Gebruiker A en gebruiker B aanroepen ICE-connectiviteit testen en het beste beschik bare pad voor media is geselecteerd (Zie de diagrammen hieronder voor verschillende use cases).
8.  Beide gebruikers verzenden telemetrie naar communicatie Services met behulp van flow 4.

### <a name="customer-network-intranet"></a>Klanten netwerk (intranet)

:::image type="content" source="./media/call-flows/one-to-one-internal.png" alt-text="Verkeers stroom in het netwerk van de klant.":::

*Afbeelding 2: in het netwerk van de klant*

In stap 7 is peer-to-peer-media stroom 5 geselecteerd.

Deze media overdracht is bidirectionele. De richting van flow 5 geeft aan dat één zijde de communicatie vanuit een connectiviteits perspectief initieert. In dit geval maakt het niet uit welke richting wordt gebruikt omdat beide eind punten binnen het netwerk van de klant zijn.

### <a name="customer-network-to-external-user-media-relayed-by-teams-transport-relay"></a>Klanten netwerk naar externe gebruiker (door middel van door teams transport doorstuur media)

:::image type="content" source="./media/call-flows/one-to-one-via-relay.png" alt-text="Een-op-een-aanroep stroom via een relay.":::

*Afbeelding 3: klant netwerk naar externe gebruiker (door Azure transport relay gestuurde media)*

In stap 7 zijn flow 4 (van het klanten netwerk naar de communicatie Services) en flow 3 (van een externe gebruiker van Mobile Communication Services naar Azure Communication Services) geselecteerd.

Deze stromen worden door teams transporten door gegeven in Azure.

Deze media overdracht is bidirectionele. De richting geeft aan welke zijde de communicatie vanuit een connectiviteits perspectief initieert. In dit geval worden deze stromen gebruikt voor Signa lering en media, met behulp van verschillende transport protocollen en-adressen.

### <a name="customer-network-to-external-user-direct-media"></a>Klant netwerk naar externe gebruiker (directe media)

:::image type="content" source="./media/call-flows/one-to-one-with-extenal.png" alt-text="Een-op-een-aanroep stroom met een externe gebruiker.":::

*Afbeelding 4-klant netwerk naar externe gebruiker (directe media)*

In stap 7 is stroom 2 (van klant netwerk naar de peer via internet) geselecteerd.

Directe media met een externe mobiele gebruiker (niet door gegeven via Azure) is optioneel. Met andere woorden, u kunt dit pad blok keren om een mediapad af te dwingen via een transport relay in Azure.

Deze media overdracht is bidirectionele. De richting van Flow 2 naar externe mobiele gebruiker geeft aan dat één zijde de communicatie vanuit een connectiviteits perspectief initieert.

### <a name="vpn-user-to-internal-user-media-relayed-by-teams-transport-relay"></a>VPN-gebruiker aan interne gebruiker (door middel van door teams transport relay)

:::image type="content" source="./media/call-flows/vpn-to-internal-via-relay.png" alt-text="Een-op-een-aanroep stroom met een VPN-gebruiker via relay.":::

*Afbeelding 5: VPN-gebruiker naar interne gebruiker (door Azure Relay gestuurde media*

Signa lering tussen de VPN-verbinding met het klant netwerk maakt gebruik van stroom 2 *. Signa lering tussen het netwerk van de klant en Azure maakt gebruik van stroom 4. Media omzeilt echter het VPN en wordt doorgestuurd met stromen 3 en 4 via Azure media relay.

### <a name="vpn-user-to-internal-user-direct-media"></a>VPN-gebruiker naar interne gebruiker (directe media)

:::image type="content" source="./media/call-flows/vpn-to-internal-direct-media.png" alt-text="Een-op-een-aanroep stroom met een VPN met directe media":::

*Afbeelding 6: VPN-gebruiker naar interne gebruiker (directe media)*

Signa lering tussen de VPN-verbinding met het klant netwerk maakt gebruik van stroom 2. Signa lering tussen het netwerk van de klant en Azure gebruikt flow 4. Media omzeilt echter het VPN en wordt doorgestuurd met stroom 2 van het netwerk van de klant naar het internet.

Deze media overdracht is bidirectionele. De richting van Flow 2 naar de externe mobiele gebruiker geeft aan dat een zijde de communicatie vanuit een connectiviteits perspectief initieert.

### <a name="vpn-user-to-external-user-direct-media"></a>VPN-gebruiker naar externe gebruiker (directe media)

:::image type="content" source="./media/call-flows/vpn-user-to-external-user.png" alt-text="Een-op-een-aanroep stroom met een VPN met directe media":::

*Afbeelding 7: VPN-gebruiker naar externe gebruiker (directe media)*

Signa lering tussen de VPN-gebruiker en het klant netwerk maakt gebruik van Flow 2 * en flow 4 naar Azure. Media omzeilt echter VPN en wordt doorgestuurd met Flow 6.

Deze media overdracht is bidirectionele. De richting van flow 6 naar de externe mobiele gebruiker geeft aan dat de communicatie vanaf een connectiviteits perspectief door één zijde wordt gestart.

### <a name="use-case-communication-services-client-to-pstn-through-communication-services-trunk"></a>Use-case: communicatie Services-client naar PSTN via communicatie Services-trunk

Met communicatie Services kunt u aanroepen vanuit het open bare telefoonnet werk plaatsen en ontvangen. Als de PSTN-trunk is verbonden met telefoon nummers van communicatie Services, zijn er geen speciale connectiviteits vereisten voor deze use-case. Als u uw eigen on-premises PSTN-trunk wilt verbinden met Azure Communication Services, kunt u SIP-interface gebruiken (beschikbaar in CY2021).

:::image type="content" source="./media/call-flows/acs-to-pstn.png" alt-text="Een-op-een-gesprek met een PSTN-deel nemer":::

*Afbeelding 8: communicatie Services-gebruiker tot PSTN via Azure trunk*

In dit geval maakt u een signaal en medium van het klanten netwerk naar Azure flow 4.

### <a name="use-case-communication-services-group-calls"></a>Use-case: communicatie Services-groeps aanroepen

De service audio/video/scherm delen (VBSS) maakt deel uit van Azure Communication Services. Het heeft een openbaar IP-adres dat bereikbaar moet zijn vanuit het klant netwerk en moet bereikbaar zijn vanaf een Nomadic-cloud client. Elk client/eind punt moet verbinding kunnen maken met de service.

Interne clients krijgen lokale, reflexieve en relay-kandidaten op dezelfde manier als beschreven voor een-op-een-aanroepen. De clients verzenden deze kandidaten naar de service in een uitnodiging. De service maakt geen gebruik van een relay omdat deze een openbaar bereikbaar IP-adres heeft, zodat deze reageert met de lokale IP-adres-kandidaat. De client en de service controleren de connectiviteit op dezelfde manier als beschreven voor een-op-een-oproep.

:::image type="content" source="./media/call-flows/acs-group-calls.png" alt-text="Aanroep van OACS-groep":::

*Afbeelding 9: aanroepen van de groep communicatie Services*

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met oproepen](../quickstarts/voice-video-calling/getting-started-with-calling.md)

De volgende documenten zijn mogelijk interessant voor u:

- Meer informatie over [oproeptypen](../concepts/voice-video-calling/about-call-types.md)
- Meer informatie over [Client-serverarchitectuur](./client-and-server-architecture.md)

