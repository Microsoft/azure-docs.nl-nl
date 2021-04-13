---
title: Wat is Azure Relay? | Microsoft Docs
description: In dit artikel vindt u een overzicht van Azure Relay, een service die is bedoeld voor het ontwikkelen van cloudtoepassingen die on-premises services verbruiken die worden uitgevoerd in uw bedrijfsnetwerk zonder dat er een firewallverbinding hoeft te worden geopend of uw netwerkinfrastructuur ingrijpend hoeft te worden gewijzigd.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 3b222942394b8a83e6f5d4447aa9ccfb33cdec21
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314198"
---
# <a name="what-is-azure-relay"></a>Wat is Azure Relay?
De Azure Relay-service stelt u in staat om services die worden uitgevoerd in uw bedrijfsnetwerk op een veilige manier aan te bieden aan de openbare cloud. U kunt dit doen zonder een poort in de firewall te openen of de infrastructuur van het bedrijfsnetwerk ingrijpend te wijzigen. 

De Relay-service ondersteunt de volgende scenario's tussen on-premises services en toepassingen die worden uitgevoerd in de cloud of in een andere on-premises omgeving. 

- Traditioneel verkeer in één richting, aanvraag/antwoord en peer-to-peer-communicatie 
- Distributie van gebeurtenissen via internet om scenario's voor publiceren/abonneren mogelijk te maken 
- Bidirectionele en niet-gebufferde socket communicatie tussen netwerk grenzen

Azure Relay is anders dan integratietechnologieën op netwerkniveau zoals VPN. Een Azure-relay kan worden gericht op een bepaald eindpunt van een toepassing op één computer. De VPN-technologie is veel ingrijpender, omdat hiervoor de netwerkomgeving moet worden aangepast. 

## <a name="basic-flow"></a>Basisstroom
In het patroon van doorgestuurde gegevensoverdracht zijn dit de basisstappen:

1. Een on-premises service maakt via een uitgaande poort verbinding met de Relay-service. 
2. Er wordt een bidirectionele socket voor communicatie gemaakt die is gekoppeld aan een bepaald adres. 
3. De client kan vervolgens met de on-premises service communiceren door verkeer te verzenden naar de Relay-service die gericht is op dat adres. 
4. De Relay-service *stuurt gegevens vervolgens door* naar de on-premises service via de bidirectionele socket die is toegewezen aan de client. De client heeft geen directe verbinding met de on-premises service nodig. De client hoeft niet te weten waar de service zich bevindt. En de on-premises service hoeft geen inkomende poorten te openen in de firewall.


## <a name="features"></a>Functies 
Azure Relay heeft twee functies:

- [Hybride verbindingen](#hybrid-connections): maakt gebruik van websockets op basis van open standaarden, waardoor scenario's met meerdere platforms worden ingeschakeld.
- WCF-relays: maakt gebruik van WCF (Windows Communication Foundation) om externe procedureaanroepen in te schakelen. WCF-relay is de oude relay-aanbieding die veel klanten mogelijk al gebruiken met hun WCF-programmeringsmodellen.

## <a name="hybrid-connections"></a>Hybride verbindingen

De functie voor hybride verbindingen in Azure Relay is een veilige en open-protocolversie van de eerder bestaande relay-functies. U kunt de functie gebruiken op elk platform en in elke taal. De functie voor hybride verbindingen in Azure Relay is gebaseerd op HTTP- en WebSockets-protocollen. Met behulp van de functie kunt u aanvragen en antwoorden verzenden via websockets of HTTP(S). Deze functie is compatibel met de WebSocket-API in veelgebruikte webbrowsers. 

Zie [Azure Relay Hybrid Connections-protocol](relay-hybrid-connections-protocol.md) voor meer informatie over het protocol voor hybride verbindingen. U kunt hybride verbindingen gebruiken met elke websockets-bibliotheek voor elke runtime/taal.

> [!NOTE]
> De functie voor hybride verbindingen van Azure Relay vervangt de oude functie voor hybride verbindingen van BizTalk Services. De functie voor hybride verbindingen in BizTalk Services was gebaseerd op Azure Service Bus WCF Relay. De voorziening voor hybride verbindingen in Azure Relay is een aanvulling op de eerder bestaande WCF Relay. De mogelijkheden van deze twee services (WCF Relay en Hybride verbindingen) bestaan naast elkaar in de Azure Relay-service. De twee services delen een gateway, maar zijn verder afzonderlijke implementaties.

## <a name="wcf-relay"></a>WCF-relay
WCF Relay werkt met het volledige .NET Framework en voor WCF. U zet een verbinding op tussen uw on-premises service en de Relay-service met behulp van een reeks WCF 'Relay'-bindingen. De Relay-bindingen worden toegewezen aan nieuwe transportbindingselementen die zijn ontworpen om WCF-kanaalonderdelen te maken die kunnen worden geïntegreerd met de Service Bus in de cloud. Zie [Aan de slag met WCF-relay](service-bus-relay-tutorial.md) voor meer informatie.

## <a name="hybrid-connections-vs-wcf-relay"></a>Hybride verbindingen versus WCF Relay
Zowel met hybride verbindingen als met WCF Relay wordt een beveiligde verbinding opgezet met assets die bestaan binnen een bedrijfsnetwerk. Of u de ene of de andere methode gebruikt is afhankelijk van uw specifieke behoeften, zoals wordt beschreven in de volgende tabel:

|  | WCF-relay | Hybride verbindingen |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **Java script/Node.js** | |x |
| **Op standaarden gebaseerd open protocol** | |x |
| **RPC-programmeringsmodellen** | |x |

## <a name="architecture-processing-of-incoming-relay-requests"></a>Architectuur: verwerken van inkomende relay-aanvragen
Het volgende diagram laat zien hoe binnenkomende relay-aanvragen worden verwerkt door de Azure Relay-service:

![Verwerken van inkomende WCF Relay-aanvragen](./media/relay-what-is-it/ic690645.png)

1. Luisterende client stuurt een aanvraag voor luisteren naar de Azure Relay-service. De Azure load balancer stuurt de aanvraag door naar een van de gatewayknooppunten. 
2. De Azure Relay-service maakt een relay in de gateway-store. 
3. Verzendende client stuurt een aanvraag voor verbinding met de luisterende service. 
4. De gateway die de aanvraag ontvangt, zoekt de relay in de gateway-store. 
5. De gateway stuurt de aanvraag voor verbinding door naar de juiste gateway vermeld in de gateway-store. 
6. De gateway stuurt een aanvraag naar de luisterende client voor het opzetten van een tijdelijk kanaal naar het gatewayknooppunt dat zich het dichtst bij de verzendende client bevindt. 
7. De luisterende client maakt een tijdelijk kanaal naar de gateway die zich het dichtst bij de verzendende client bevindt. Nu er via een gateway verbinding is gemaakt tussen de clients, kunnen de clients berichten met elkaar uitwisselen. 
8. De gateway stuurt berichten van de luisterende client door naar de verzendende client. 
9. De gateway stuurt berichten van de verzendende client door naar de luisterende client.  

## <a name="next-steps"></a>Volgende stappen
* [Aan de slag met .NET WebSockets](relay-hybrid-connections-dotnet-get-started.md)
* [Aan de slag met .NET HTTP Requests](relay-hybrid-connections-http-requests-dotnet-get-started.md)
* [Aan de slag met Node WebSockets](relay-hybrid-connections-node-get-started.md)
* [Aan de slag met Node HTTP Requests](relay-hybrid-connections-http-requests-node-get-started.md)
* [Veelgestelde vragen over Relay](relay-faq.yml)

