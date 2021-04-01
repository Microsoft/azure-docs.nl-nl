---
title: 'Problemen met de netwerk verbinding oplossen: Azure'
description: Deze pagina biedt een gestandaardiseerde methode voor het testen van de prestaties van Azure-netwerk koppelingen.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 35e080e0fe45c18ad6a6d5392e0c78b116853c3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98027465"
---
# <a name="troubleshooting-network-performance"></a>Problemen met netwerk prestaties oplossen
## <a name="overview"></a>Overzicht
Azure biedt een stabiele en snelle manier om verbinding te maken vanaf uw on-premises netwerk met Azure. Methoden zoals site-naar-site VPN en ExpressRoute worden met succes toegepast door klanten met kleine of grote ondernemingen om hun bedrijf te exploiteren in Azure. Maar wat gebeurt er wanneer de prestaties niet voldoen aan uw verwachting of een eerdere ervaring? Dit artikel helpt u bij het standaardiseren van de manier waarop u uw specifieke omgeving test en basis lijn.

U leert hoe u de netwerk latentie en band breedte tussen twee hosts eenvoudig en consistent kunt testen. U kunt ook advies geven over manieren om het Azure-netwerk te bekijken om probleem punten te isoleren. Het script en de hulpprogram ma's van Power shell vereisen twee hosts op het netwerk (aan beide uiteinden van de koppeling die wordt getest). Eén host moet een Windows-Server of-bureau blad zijn, de andere kan Windows of Linux zijn. 

>[!NOTE]
>De aanpak van het oplossen van problemen, de hulpprogram ma's en de gebruikte methoden zijn persoonlijke voor keuren. In dit document worden de aanpak en de hulpprogram ma's beschreven die vaak worden gebruikt. Uw benadering wijkt waarschijnlijk af van de verschillende benaderingen van het oplossen van problemen. Als u echter geen benadering hebt, kunt u met dit document aan de slag gaan met het pad om uw eigen methoden, hulpprogram ma's en voor keuren te bouwen om netwerk problemen op te lossen.
>
>

## <a name="network-components"></a>Netwerkonderdelen
Voordat u Blijf spitten in het oplossen van problemen, bespreken we enkele algemene voor waarden en onderdelen. Deze bespreking zorgt ervoor dat er wordt geadviseerd over elk onderdeel in de end-to-end keten dat connectiviteit in azure mogelijk maakt.
![1][1]

Op het hoogste niveau worden er drie grote netwerk routerings domeinen beschreven.

- het Azure-netwerk (blauwe cloud aan de rechter kant)
- Internet of WAN (groene Cloud in het midden)
- het bedrijfs netwerk (perzik-cloud aan de linkerkant)

Bekijk het diagram van rechts naar links om elk onderdeel kort te bespreken:
 - **Virtuele machine** : de server kan meerdere nic's hebben. Zorg ervoor dat alle statische routes, standaard routes en instellingen van het besturings systeem verkeer verzenden en ontvangen zoals u dat wilt. Daarnaast heeft elke VM-SKU een bandbreedte beperking. Als u een kleinere VM-SKU gebruikt, wordt uw verkeer beperkt door de band breedte die beschikbaar is voor de NIC. Normaal gesp roken gebruikt u een DS5v2 voor het testen (en verwijdert u vervolgens eenmaal met testen om geld te besparen) om te zorgen voor voldoende band breedte op de VM.
 - **NIC** : Zorg ervoor dat u weet wat het privé-IP-adres is dat is toegewezen aan de NIC in kwestie.
 - **NIC-NSG** : er is mogelijk specifieke nsg's toegepast op het niveau van de NIC, zodat de NSG-regel is ingesteld op het verkeer dat u probeert door te geven. Zorg er bijvoorbeeld voor dat poort 5201 voor iPerf, 3389 voor RDP of 22 voor SSH is geopend, zodat test verkeer kan worden door gegeven.
 - **VNet-subnet** : de NIC wordt toegewezen aan een specifiek subnet. Zorg ervoor dat u weet welke en welke regels aan dat subnet zijn gekoppeld.
 - **SUBNET NSG** -net als de NIC kan nsg's ook worden toegepast op het subnet. Zorg ervoor dat de regel set NSG geschikt is voor het verkeer dat u probeert door te geven. (Voor verkeer dat binnenkomt voor de NIC, wordt het subnet NSG eerst toegepast, vervolgens de NIC NSG. Wanneer het verkeer van de virtuele machine uitvalt, wordt de NIC-NSG van kracht, waarna het subnet NSG wordt toegepast.
 - **SUBNET UDR** -User-Defined routes kunnen verkeer omleiden naar een tussenliggende hop (zoals een firewall of een load balancer). Zorg ervoor dat u weet of er een UDR is voor uw verkeer. Als dat het geval is, weet u waar het zich bevindt en wat de volgende hop naar uw verkeer doet. Een firewall kan bijvoorbeeld verkeer door lopen en ander verkeer tussen dezelfde twee hosts weigeren.
 - **Gateway subnet/NSG/UDR** -net als het VM-subnet kan het gateway-subnet Nsg's en udr's hebben. Zorg ervoor dat u weet of ze daar zijn en wat het effect hiervan op uw verkeer is.
 - **VNet-gateway (ExpressRoute)** : zodra peering (ExpressRoute) of VPN is ingeschakeld, zijn er geen instellingen die van invloed kunnen zijn op de manier waarop of wanneer er verkeer wordt gerouteerd. Als u een VNet-gateway hebt die is verbonden met meerdere ExpressRoute-circuits of VPN-tunnels, moet u rekening houden met de instellingen voor verbindings gewicht. Het verbindings gewicht heeft gevolgen voor de verbindings voorkeur en bepaalt het pad dat het verkeer in beslag neemt.
 - **Route filter** (niet weer gegeven): er is een route filter nodig bij het gebruik van micro soft-peering via ExpressRoute. Als u geen routes ontvangt, controleert u of het route filter is geconfigureerd en correct is toegepast op het circuit.

U bevindt zich op dit punt in het WAN-gedeelte van de koppeling. Dit routerings domein kan uw service provider, het WAN van uw bedrijf of Internet zijn. Er zijn veel hops, apparaten en bedrijven betrokken bij deze koppelingen, waardoor het moeilijk is om problemen op te lossen. U moet eerst zowel Azure als uw bedrijfs netwerken afregelen voordat u de hops kunt onderzoeken tussen.

In het voor gaande diagram is helemaal links uw bedrijfs netwerk. Afhankelijk van de grootte van uw bedrijf, kunnen dit routerings domein een aantal netwerk apparaten tussen u en de WAN-of meerdere lagen van apparaten in een campus/Enter prise-netwerk zijn.

Gezien de complexiteit van deze drie verschillende netwerk omgevingen op hoog niveau. Het is vaak optimaal om aan de randen te beginnen en te zien waar de prestaties goed zijn en waar deze worden verminderd. Deze aanpak kan u helpen bij het identificeren van het routerings domein van het probleem van de drie. Vervolgens kunt u zich richten op het oplossen van problemen met die specifieke omgeving.

## <a name="tools"></a>Hulpprogramma's
De meeste netwerk problemen kunnen worden geanalyseerd en geïsoleerd met behulp van basis hulpprogramma's als ping en traceroute. Het is vaak nodig om als een pakket analyse te gaan met hulpprogram ma's zoals wireshark. 

Voor hulp bij het oplossen van problemen is de Azure Connectivity Toolkit (AzureCT) ontwikkeld om enkele van deze hulpprogram ma's in een eenvoudig pakket te zetten. Voor het testen van de prestaties kunnen hulpprogram ma's zoals iPerf en PSPing u informatie geven over uw netwerk. iPerf is een veelgebruikt hulp programma voor het testen van elementaire uitvoeringen en is redelijk eenvoudig te gebruiken. PSPing is een ping-hulp programma dat is ontwikkeld door SysInternals. PSPing kunnen zowel ICMP-als TCP-pings uitvoeren om een externe host te bereiken. Beide hulpprogram ma's zijn licht gewicht en worden ' geïnstalleerd ' door de bestanden te omgaan naar een map op de host.

Deze hulpprogram ma's en methoden worden verpakt in een Power shell-module (AzureCT) die u kunt installeren en gebruiken.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT-de Azure Connectivity Toolkit
De AzureCT Power shell-module heeft twee [Beschik baarheid][Availability Doc] van onderdelen en [prestatie testen][Performance Doc]. Dit document is alleen gericht op prestatie tests, zodat u zich kunt richten op de twee opdrachten voor koppelings prestaties in deze Power shell-module.

Er zijn drie basis stappen voor het gebruik van deze Toolkit voor het testen van de prestaties. 1) de Power shell-module installeren, 2) Installeer de ondersteunende toepassingen iPerf en PSPing 3) de prestatie test uitvoeren.

1. De Power shell-module installeren

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Met deze opdracht wordt de Power shell-module gedownload en lokaal geïnstalleerd.

2. De ondersteunende toepassingen installeren
    ```powershell
    Install-LinkPerformance
    ```
    Met deze AzureCT-opdracht worden iPerf en PSPing in een nieuwe directory "C:\ACTTools" geïnstalleerd, wordt ook de Windows Firewall poorten geopend om verkeer van ICMP-en poort 5201 (iPerf) toe te staan.

3. De prestatie test uitvoeren

    Eerst moet u op de externe host iPerf installeren en uitvoeren in de server modus. Zorg er ook voor dat de externe host op 3389 (RDP voor Windows) of 22 (SSH voor Linux) luistert en verkeer op poort 5201 voor iPerf toestaat. Als de externe host Windows is, installeert u de AzureCT en voert u de Install-LinkPerformance opdracht uit. Met de opdracht worden iPerf en de firewall regels ingesteld die nodig zijn om iPerf in de server modus te kunnen starten. 
    
    Zodra de externe computer klaar is, opent u Power shell op de lokale computer en start u de test:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Met deze opdracht voert u een reeks gelijktijdige laad-en latentie tests uit om de bandbreedte capaciteit en latentie van uw netwerk koppeling te schatten.

4. Bekijk de uitvoer van de tests

    De Power shell-uitvoer indeling ziet er ongeveer als volgt uit:

    ![4][4]

    De gedetailleerde resultaten van alle iPerf-en PSPing-tests vindt u in afzonderlijke tekst bestanden in de map hulpprogram ma's van AzureCT op ' C:\ACTTools. '

## <a name="troubleshooting"></a>Problemen oplossen
Als de prestatie test niet de verwachte resultaten oplevert, moet u nagaan waarom een progressief stapsgewijs proces is. Gezien het aantal onderdelen in het pad, biedt een systematische benadering een sneller pad naar de oplossing dan het probleem. Door systematisch problemen op te lossen, kunt u voor komen dat u meerdere onnodige tests uitvoert.

>[!NOTE]
>Het scenario hier is een prestatie probleem, geen connectiviteits probleem. De stappen zijn anders als verkeer helemaal niet is door gegeven.
>
>

Vraag eerst uw veronderstellingen af. Is uw verwachting redelijk? Bijvoorbeeld als u een ExpressRoute-circuit van 1 Gbps en een latentie van 100 MS hebt. Het is niet redelijk om de volledige 1 Gbps aan verkeer te verwachten, gezien de prestatie kenmerken van TCP via koppelingen met een hoge latentie. Zie het [gedeelte met verwijzingen](#references) voor meer informatie over prestatie aannames.

Vervolgens raden we u aan om te beginnen bij de randen tussen routerings domeinen en proberen het probleem te isoleren met één groot routerings domein. U kunt beginnen met het bedrijfs netwerk, het WAN of het Azure-netwerk. Mensen liggen vaak het ' Black Box ' in het pad. Hoewel blaming het zwarte vak eenvoudig is, kan de resolutie aanzienlijk worden vertraagd. Met name als het probleem zich in een gebied bevindt dat u wijzigingen kunt aanbrengen om het probleem op te lossen. Zorg ervoor dat u uw terdege toeneemt voordat u uw service provider of Internet provider uitlevert.

Zodra u het grote routerings domein hebt geïdentificeerd dat het probleem bevat, moet u een diagram van het betreffende gebied maken. Door een diagram te tekenen in een White Board, Klad blok of Visio, kunt u methodically door werken en het probleem isoleren. U kunt test punten plannen en de kaart bijwerken wanneer u gebieden wist of dieper gaat naarmate de tests worden uitgevoerd.

Nu u een diagram hebt, begint u met het verdelen van het netwerk in segmenten en beperkt u het probleem. Ontdek waar het werkt en waar dat niet het geval is. Blijf uw test punten verplaatsen om ze te isoleren tot het conflicterende onderdeel.

Vergeet ook niet om andere lagen van het OSI-model te bekijken. Het is eenvoudig om te richten op het netwerk en de lagen 1-3 (fysieke, gegevens en netwerk lagen), maar de problemen kunnen ook op laag 7 in de toepassingslaag worden weer gegeven. Houd een open gedachte en controleer hypo theses.

## <a name="advanced-expressroute-troubleshooting"></a>Geavanceerde ExpressRoute problemen oplossen
Als u niet zeker weet waar de rand van de Cloud zich daad werkelijk bevindt, kan het isoleren van de onderdelen van Azure een uitdaging zijn. Wanneer ExpressRoute wordt gebruikt, is de rand een netwerk onderdeel dat de micro soft Enter prise Edge (MSEE) wordt genoemd. **Wanneer u ExpressRoute gebruikt**, is de MSEE het eerste contact punt in het netwerk van micro soft en de laatste hop wanneer u het micro soft-netwerk verlaat. Wanneer u een verbindings object tussen uw VNet-gateway en het ExpressRoute-circuit maakt, maakt u een verbinding met de MSEE. Het herkennen van de MSEE als de eerste of laatste hop, afhankelijk van de richting waarin het verkeer van cruciaal belang is voor het isoleren van een Azure-netwerk probleem. Wanneer het probleem zich in azure of verder in het WAN of in het bedrijfs netwerk bevindt, wordt er in de richting van de benadering aangegeven. 

![2][2]

>[!NOTE]
> U ziet dat de MSEE zich niet in de Azure-Cloud bevindt. ExpressRoute is daad werkelijk aan de rand van het micro soft-netwerk niet echt in Azure. Zodra u met ExpressRoute bent verbonden met een MSEE, hebt u verbinding met het netwerk van micro soft en kunt u vervolgens naar een van de Cloud Services gaan, zoals Microsoft 365 (met micro soft-peering) of Azure (met persoonlijke en/of micro soft-peering).
>
>

Als twee VNets zijn verbonden met **hetzelfde** ExpressRoute-circuit, kunt u een reeks tests uitvoeren om het probleem in azure te isoleren.
 
### <a name="test-plan"></a>Test plan
1. Voer de Get-LinkPerformance test uit tussen VM1 en VM2. Deze test geeft inzicht in het geval dat het probleem lokaal is of niet. Als deze test acceptabele latentie en bandbreedte resultaten produceert, kunt u het lokale VNet-netwerk markeren als goed.
2. Ervan uitgaande dat het lokale VNet-verkeer goed is, voert u de Get-LinkPerformance test uit tussen VM1 en VM3. Met deze test wordt de verbinding via het micro soft-netwerk tot stand gebracht met de MSEE en weer in Azure. Als deze test acceptabele latentie en bandbreedte resultaten produceert, kunt u het Azure-netwerk markeren als goed.
3. Als Azure wordt uitgesloten, kunt u een vergelijk bare reeks testen op uw bedrijfs netwerk uitvoeren. Als dat ook goed wordt getest, is het tijd om samen te werken met uw service provider of provider om uw WAN-verbinding te diagnosticeren. Voor beeld: Voer deze test uit tussen twee filialen of tussen uw bureau en een Data Center-Server. Afhankelijk van wat u wilt testen, kunt u eind punten zoeken, zoals servers en client-Pc's die dit pad kunnen uitoefenen.

>[!IMPORTANT]
> Het is essentieel dat voor elke test het tijdstip waarop u de test uitvoert, de resultaten op een algemene locatie (zoals OneNote of Excel) vastlegt. Elke test uitvoering moet een identieke uitvoer hebben, zodat u de resulterende gegevens in de test uitvoeringen kunt vergelijken en geen ' gaten ' in de gegevens hebt. Consistentie tussen meerdere tests is de belangrijkste reden waarom ik de AzureCT gebruik voor het oplossen van problemen. Het Magic is niet in de exacte werk scenario's die ik uitvoer, maar in plaats *daarvan is het* een *consistente test en gegevens uitvoer* van elke test. Het vastleggen van de tijd en het hebben van consistente gegevens elke enige tijd is met name handig als u later merkt dat het probleem sporadisch is. Wees zo snel mogelijk met uw gegevens verzameling, zodat u kunt voor komen dat dezelfde scenario's opnieuw worden getest (ik heb deze vaste weg al jaren geleden geleerd).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Is het probleem geïsoleerd, nu wat?
Hoe meer u het probleem isoleert, des te sneller de oplossing kan worden gevonden. Op dit moment bereikt u een punt waar u niet verder kunt gaan met het oplossen van problemen. U ziet bijvoorbeeld dat de koppeling over uw service provider hops maakt via Europa, maar u verwacht dat het pad in Azië blijft. Op dit moment moet u iemand helpen om hulp te krijgen. Wie u vraagt, is afhankelijk van het routerings domein waar u het probleem isoleert. Als u het kunt beperken tot een specifiek onderdeel dat nog beter zou zijn.

Bij problemen met het bedrijfs netwerk kan uw interne IT-afdeling of service provider u helpen bij het configureren van de apparaatconfiguratie of het herstellen van hardware.

Voor het WAN kunt u de test resultaten met uw service provider of Internet provider delen, zodat ze aan de slag kunnen. Als u dat wel doet, vermijdt u ook het dupliceren van het werk dat u al hebt uitgevoerd. Als u uw resultaten zelf wilt verifiëren, weet u dit niet. ' Vertrouwen, maar controleren ' is een goede motto bij het oplossen van problemen op basis van de gerapporteerde resultaten van andere gebruikers.

Wanneer u het probleem met Azure hebt geïsoleerd, is het tijd om de documentatie van het [Azure-netwerk][Network Docs] te bekijken en vervolgens als u nog steeds [een ondersteunings ticket hebt geopend][Ticket Link].

## <a name="references"></a>Referenties
### <a name="latencybandwidth-expectations"></a>Verwachtingen voor latentie/band breedte
>[!TIP]
> Een geografische latentie (mijl of kilo meter) tussen de eind punten die u wilt testen, is het grootste deel van de latentie. Hoewel er sprake is van een latentie van apparatuur (fysieke en virtuele onderdelen, het aantal hops enz.), is de geografie bewezen als het grootste onderdeel van de totale latentie bij het omgaan met WAN-verbindingen. Het is ook belang rijk om te weten dat de afstand de afstand van de fiber-uitvoering is en niet de lijn afstand van de lineaire of de weg. Deze afstand is hard moeilijk om een nauw keurigheid te bereiken. Als gevolg hiervan gebruiken we doorgaans een lokale reken machine op internet en weet u dat deze methode een nagenoeg onnauwkeurige meting is, maar voldoende is om een algemene verwachting in te stellen.
>
>

Ik heb een ExpressRoute-installatie in Seattle, Washington in de Verenigde Staten. De volgende tabel toont de latentie en band breedte die ik heb getest op verschillende Azure-locaties. Ik heb een schatting van de geografische afstand tussen elk einde van de test.

Setup testen:
 - Een fysieke server met Windows Server 2016 met een NIC van 10 Gbps, verbonden met een ExpressRoute-circuit.
 - Een 10 Gbps Premium ExpressRoute-circuit in de locatie die is geïdentificeerd met persoonlijke peering ingeschakeld.
 - Een Azure VNet met een Ultra Performance-gateway in de opgegeven regio.
 - Een DS5v2-VM met Windows Server 2016 op het VNet. De virtuele machine is toegevoegd aan een ander domein, gebouwd op basis van de standaard installatie kopie van Azure (geen optimalisatie of aanpassing) met AzureCT geïnstalleerd.
 - Bij alle tests wordt gebruikgemaakt van de AzureCT-Get-LinkPerformance opdracht met een belasting test van 5 minuten voor elk van de zes test uitvoeringen. Bijvoorbeeld:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - De gegevens stroom voor elke test had de belasting van de on-premises fysieke server (iPerf-client in Seattle) tot de Azure VM (iPerf-server in de vermelde Azure-regio).
 - De gegevens van de latentie kolom zijn afkomstig uit de test geen belasting (een TCP-latentie test zonder iPerf).
 - De kolom maximale band breedte is van de 16 TCP-stroom belasting test met een window-grootte van 1 MB.

![3][3]

### <a name="latencybandwidth-results"></a>Resultaten van latentie/band breedte
>[!IMPORTANT]
> Deze nummers gelden alleen voor algemene naslag informatie. Veel factoren beïnvloeden latentie, en hoewel deze waarden in de loop van de tijd doorgaans consistent zijn, kunnen de voor waarden in azure of het netwerk van de service providers verkeer via verschillende paden op elk gewenst moment verzenden, zodat latentie en band breedte kan worden beïnvloed. Over het algemeen zijn de gevolgen van deze wijzigingen niet van belang rijke verschillen.
>
>

| ExpressRoute<br/>Locatie|Azure<br/>Region | Bepaald<br/>Afstand (km) | Latentie|1 sessie<br/>Bandbreedte | Maximum<br/>Bandbreedte |
| ------------------------------------------ | --------------------------- |  - | - | - | - |
| Seattle | VS - west 2        |    191 km |   5 MS | 262,0 Mbit per seconde |  3,74 Gbits per seconde |
| Seattle | VS - west          |  1.094 km |  18 MS |  82,3 Mbit per seconde |  3,70 Gbits per seconde |
| Seattle | VS - centraal       |  2.357 km |  40 MS |  38,8 Mbit per seconde |  2,55 Gbits per seconde |
| Seattle | VS - zuid-centraal |  2.877 km |  51 MS |  30,6 Mbit per seconde |  2,49 Gbits per seconde |
| Seattle | VS - noord-centraal |  2.792 km |  55 MS |  27,7 Mbit per seconde |  2,19 Gbits per seconde |
| Seattle | VS - oost 2        |  3.769 km |  73 MS |  21,3 Mbit per seconde |  1,79 Gbits per seconde |
| Seattle | VS - oost          |  3.699 km |  74 MS |  21,1 Mbit per seconde |  1,78 Gbits per seconde |
| Seattle | Japan - oost       |  7.705 km | 106 MS |  14,6 Mbit per seconde |  1,22 Gbits per seconde |
| Seattle | Verenigd Koninkrijk Zuid         |  7.708 km | 146 MS |  10,6 Mbit per seconde |   896 Mbit per seconde |
| Seattle | Europa -west      |  7.834 km | 153 MS |  10,2 Mbit per seconde |   761 Mbit per seconde |
| Seattle | Australië - oost   | 12.484 km | 165 MS |   9,4 Mbit per seconde |   794 Mbit per seconde |
| Seattle | Azië - zuidoost   | 12.989 km | 170 MS |   9,2 Mbit per seconde |   756 Mbit per seconde |
| Seattle | Brazilië-zuid *   | 10.930 km | 189 MS |   8,2 Mbit per seconde |   699 Mbit per seconde |
| Seattle | India - zuid      | 12.918 km | 202 MS |   7,7 Mbit per seconde |   634 Mbit per seconde |

\* De latentie van Brazilië is een goed voor beeld waarbij de lineaire afstand sterk afwijkt van de afstand van de fiber-uitvoering. De verwachte latentie bevindt zich in de groep van 160 MS, maar is in werkelijkheid 189 MS. Het verschil in latentie lijkt ergens op een netwerk probleem te wijzen. Maar de realiteit is dat de fiber lijn niet in een rechte lijn naar Brazilië gaat. Daarom zou u een extra 1.000 km of meer kunnen verwachten om vanuit Seattle van Rotterdam te gaan.

## <a name="next-steps"></a>Volgende stappen
1. Down load de Azure Connectivity Toolkit vanuit GitHub op [https://aka.ms/AzCT][ACT]
2. Volg de instructies voor het testen van de [koppelings prestaties][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Azure-netwerk onderdelen"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute problemen oplossen"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Prestatie test omgeving"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "Power shell-uitvoer"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: ../index.yml
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT