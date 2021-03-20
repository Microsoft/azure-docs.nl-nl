---
title: Veelgestelde vragen over Azure Service Bus | Microsoft Docs
description: In dit artikel vindt u antwoorden op enkele veelgestelde vragen over Azure Service Bus.
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 3a96cf94ca4a7edd115f12b3e2eded11a5894e04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98693394"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Azure Service Bus-Veelgestelde vragen (FAQ)

In dit artikel worden enkele veelgestelde vragen over Microsoft Azure Service Bus beschreven. U kunt ook de [Veelgestelde vragen over Azure-ondersteuning](https://azure.microsoft.com/support/faq/) bezoeken voor algemene Azure-prijzen en-ondersteunings informatie.


## <a name="general-questions-about-azure-service-bus"></a>Algemene vragen over Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Wat is Azure Service Bus?
[Azure service bus](service-bus-messaging-overview.md) is een asynchroon Cloud platform voor berichten waarmee u gegevens kunt verzenden tussen losgekoppelde systemen. Micro soft biedt deze functie als een service, wat betekent dat u uw eigen hardware niet hoeft te hosten om deze te gebruiken.

### <a name="what-is-a-service-bus-namespace"></a>Wat is een Service Bus naam ruimte?
Een [naam ruimte](service-bus-create-namespace-portal.md) biedt een bereik container voor het adresseren van service bus resources in uw toepassing. Het maken van een naam ruimte is nodig voor het gebruik van Service Bus en is een van de eerste stappen in aan de slag.

### <a name="what-is-an-azure-service-bus-queue"></a>Wat is een Azure Service Bus wachtrij?
Een [Service Bus wachtrij](service-bus-queues-topics-subscriptions.md) is een entiteit waarin berichten worden opgeslagen. Wacht rijen zijn handig wanneer u meerdere toepassingen hebt of meerdere delen van een gedistribueerde toepassing die met elkaar moeten communiceren. De wachtrij is vergelijkbaar met een distributie centrum waarin meerdere producten (berichten) worden ontvangen en vervolgens vanaf die locatie worden verzonden.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Wat zijn Azure Service Bus onderwerpen en abonnementen?
Een onderwerp kan worden gevisualiseerd als een wachtrij en wanneer meerdere abonnementen worden gebruikt, wordt het een rijkere berichten model. in wezen een een-op-veel-communicatie hulpprogramma. Dit model voor publiceren/abonneren (of *pub/sub*) maakt het mogelijk dat een toepassing die een bericht verzendt naar een onderwerp met meerdere abonnementen, het bericht ontvangt dat door meerdere toepassingen wordt ontvangen.

### <a name="what-is-a-partitioned-entity"></a>Wat is een gepartitioneerde entiteit?
Een conventionele wachtrij of onderwerp wordt verwerkt door één Message Broker en opgeslagen in één berichten archief. Wordt alleen ondersteund in de lagen basis en standaard berichten, een [gepartitioneerde wachtrij of een onderwerp](service-bus-partitioning.md) wordt verwerkt door meerdere bericht brokers en opgeslagen in meerdere berichten archieven. Deze functie houdt in dat de algemene door Voer van een gepartitioneerde wachtrij of onderwerp niet langer wordt beperkt door de prestaties van één bericht Broker of berichten archief. Een tijdelijke onderbreking van een berichten archief kan ook geen gepartitioneerde wachtrij of onderwerp niet beschikbaar genereren.

De volg orde van het gebruik van gepartitioneerde entiteiten wordt niet gegarandeerd. In het geval dat een partitie niet beschikbaar is, kunt u nog steeds berichten verzenden en ontvangen van de andere partities.

 Gepartitioneerde entiteiten worden niet meer ondersteund in de [Premium-SKU](service-bus-premium-messaging.md). 

### <a name="where-does-azure-service-bus-store-data"></a><a name="in-region-data-residency"></a>Waar worden gegevens Azure Service Bus opgeslagen?
Azure Service Bus Standard-laag maakt gebruik van Azure SQL Database voor de backend-opslaglaag van de back-uplaag. Voor alle regio's behalve Brazilië-zuid en Zuidoost-Azië wordt de back-up van de data base in een andere regio gehost (meestal de Azure-gekoppelde regio). Voor de regio's van Brazilië-Zuid en Zuidoost-Azië worden database back-ups in dezelfde regio opgeslagen om te voldoen aan de vereisten voor locatie voor deze regio's.

Met Azure Service Bus Premium-laag worden meta gegevens en gegevens opgeslagen in regio's die u selecteert. Als geo-nood herstel is ingesteld voor een Azure Service Bus Premium-naam ruimte, worden de meta gegevens gekopieerd naar de secundaire regio die u selecteert.


### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Welke poorten moet ik op de firewall openen? 
U kunt de volgende protocollen gebruiken met Azure Service Bus voor het verzenden en ontvangen van berichten:

- Advanced Message Queueing Protocol 1,0 (AMQP)
- Hypertext Transfer Protocol 1,1 met TLS (HTTPS)

Zie de volgende tabel voor de uitgaande TCP-poorten die u moet openen om deze protocollen te gebruiken om te communiceren met Azure Service Bus:

| Protocol | Poort | Details | 
| -------- | ----- | ------- | 
| AMQP | 5671 | AMQP met TLS. Zie [AMQP protocol Guide (Engelstalig](service-bus-amqp-protocol-guide.md) ) | 
| HTTPS | 443 | Deze poort wordt gebruikt voor de HTTP/REST API en voor AMQP-over-websockets |

De HTTPS-poort is over het algemeen vereist voor uitgaande communicatie, ook wanneer AMQP wordt gebruikt via poort 5671, omdat verschillende beheer bewerkingen die worden uitgevoerd door de client-Sdk's en het verkrijgen van tokens van Azure Active Directory (indien gebruikt) via HTTPS worden uitgevoerd. 

De officiële Azure-Sdk's gebruiken meestal het AMQP-protocol voor het verzenden en ontvangen van berichten van Service Bus. 

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

Het oudere WindowsAzure. ServiceBus-pakket voor de .NET Framework heeft een optie voor het gebruik van het verouderde ' Service Bus Messa ging Protocol ' (SBMP), ook wel ' netmessa ging ' genoemd. Dit protocol maakt gebruik van TCP-poorten 9350-9354. De standaard modus voor dit pakket is om automatisch te detecteren of deze poorten beschikbaar zijn voor communicatie en om over te scha kelen naar websockets met TLS via poort 443, als dat niet het geval is. U kunt deze instelling negeren en deze modus afdwingen door de `Https` [ConnectivityMode](/dotnet/api/microsoft.servicebus.connectivitymode) in te stellen voor de [`ServiceBusEnvironment.SystemConnectivity`](/dotnet/api/microsoft.servicebus.servicebusenvironment.systemconnectivity) instelling, die wereld wijd van toepassing is op de toepassing.

### <a name="what-ip-addresses-do-i-need-to-add-to-allow-list"></a>Welke IP-adressen moet ik toevoegen aan de acceptatie lijst?
Ga als volgt te werk om de juiste IP-adressen te zoeken die u wilt toevoegen aan de lijst toestaan voor uw verbindingen:

1. Voer de volgende opdracht uit vanaf een opdracht prompt: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Noteer het IP-adres dat is geretourneerd in `Non-authoritative answer` . 

Als u de **zone redundantie** voor uw naam ruimte gebruikt, moet u een aantal extra stappen uitvoeren: 

1. Eerst voert u Nslookup uit op de naam ruimte.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Noteer de naam in de sectie **niet-bindende antwoord** , die een van de volgende indelingen heeft: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Voer nslookup uit voor elk met achtervoegsels S1, S2 en S3 om de IP-adressen te verkrijgen van alle drie de instanties die worden uitgevoerd in drie beschikbaarheids zones, 

    > [!NOTE]
    > Het IP-adres dat door de `nslookup` opdracht wordt geretourneerd, is geen statisch IP-adres. Het blijft echter constant totdat de onderliggende implementatie wordt verwijderd of verplaatst naar een ander cluster.

### <a name="where-can-i-find-the-ip-address-of-the-client-sendingreceiving-messages-tofrom-a-namespace"></a>Waar vind ik het IP-adres van de client die berichten verzendt/ontvangt van een naam ruimte? 
De IP-adressen van clients die berichten verzenden of ontvangen van uw naam ruimte worden niet geregistreerd. Genereer sleutels opnieuw zodat alle bestaande clients geen authenticatie [van op rollen gebaseerde toegangs beheer (Azure RBAC)](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)kunnen verifiëren en controleren om ervoor te zorgen dat alleen toegestane gebruikers of toepassingen toegang hebben tot de naam ruimte. 

Als u een **Premium** -naam ruimte gebruikt, kunt u [IP-filtering](service-bus-ip-filtering.md), [service-eind punten voor virtuele netwerken](service-bus-service-endpoints.md)en [privé-eind punten](private-link-service.md) gebruiken om de toegang tot de naam ruimte te beperken. 

## <a name="best-practices"></a>Aanbevolen procedures
### <a name="what-are-some-azure-service-bus-best-practices"></a>Wat zijn de aanbevolen procedures Azure Service Bus?
Bekijk [Aanbevolen procedures voor prestatie verbeteringen met behulp van service bus][Best practices for performance improvements using Service Bus] : in dit artikel wordt beschreven hoe u de prestaties optimaliseert bij het uitwisselen van berichten.

### <a name="what-should-i-know-before-creating-entities"></a>Wat moet ik weten voordat ik entiteiten Maak?
De volgende eigenschappen van een wachtrij en onderwerp zijn onveranderbaar. Houd rekening met deze beperking bij het inrichten van uw entiteiten, omdat deze eigenschappen niet kunnen worden gewijzigd zonder dat er een nieuwe vervangende entiteit wordt gemaakt.

* Partitionering
* Sessies
* Detectie van duplicaten
* Express-entiteit

## <a name="pricing"></a>Prijzen
In deze sectie vindt u antwoorden op enkele veelgestelde vragen over de Service Bus prijs structuur.

In het artikel [Service Bus prijzen en facturering](https://azure.microsoft.com/pricing/details/service-bus/) worden de facturerings meters in service bus uitgelegd. Zie [Service Bus prijs informatie](https://azure.microsoft.com/pricing/details/service-bus/)voor specifieke informatie over service bus prijs opties.

U kunt ook de [Veelgestelde vragen over Azure-ondersteuning](https://azure.microsoft.com/support/faq/) bezoeken voor algemene informatie over Azure-prijzen. 

### <a name="how-do-you-charge-for-service-bus"></a>Hoe worden de kosten in rekening gebracht voor Service Bus?
Zie [Service Bus prijs informatie][Pricing overview]voor volledige informatie over service bus prijzen. Naast de genoteerde prijzen worden er kosten in rekening gebracht voor de bijbehorende gegevens overdrachten voor uitgaand verkeer buiten het Data Center waarin uw toepassing is ingericht.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-isnt"></a>Welk gebruik van Service Bus is onderhevig aan gegevens overdracht? Wat is er niet?
Elke gegevens overdracht binnen een bepaalde Azure-regio wordt gratis geleverd, evenals een binnenkomende gegevens overdracht. Gegevens overdracht buiten een regio is onderhevig aan uitstaande kosten, die [hier](https://azure.microsoft.com/pricing/details/bandwidth/)kunnen worden gevonden.

### <a name="does-service-bus-charge-for-storage"></a>Worden er Service Bus kosten in rekening gebracht voor opslag?
Nee. Voor opslag Service Bus worden geen kosten in rekening gebracht. Er is echter een quotum dat de maximale hoeveelheid gegevens beperkt die kan worden bewaard per wachtrij/onderwerp. Zie de volgende veelgestelde vragen.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Ik heb een Service Bus standaard naam ruimte. Waarom worden kosten onder resource groep ' $system ' weer geven?
De facturerings onderdelen Azure Service Bus recent bijgewerkt. Als u een Service Bus standaard naam ruimte hebt, ziet u mogelijk regel items voor de resource '/Subscriptions/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system ' onder resource groep $system.

Deze kosten vertegenwoordigen de basis kosten per Azure-abonnement dat een Service Bus standaard naam ruimte heeft ingericht. 

Het is belang rijk te weten dat deze kosten niet nieuw zijn, dat wil zeggen, ze bestonden ook in het vorige facturerings model. De enige wijziging is dat ze nu worden vermeld onder $system. De oplossing wordt uitgevoerd vanwege beperkingen in het nieuwe facturerings systeem waarbij kosten op abonnements niveau worden gegroepeerd, niet zijn gekoppeld aan een specifieke resource, onder de resource-ID $system.

## <a name="quotas"></a>Quota

Zie het [overzicht van service bus quota's][Quotas overview]voor een lijst met Service Bus limieten en quota's.

### <a name="how-to-handle-messages-of-size--1-mb"></a>Hoe kan ik de grootte van een bericht afhandelen > 1 MB?
Service Bus Messa ging Services (wacht rijen en onderwerpen/abonnementen) toestaan dat toepassingen berichten met een grootte van Maxi maal 256 KB (Standard-laag) of 1 MB (Premium-laag) kunnen verzenden. Als u wilt omgaan met berichten van meer dan 1 MB, gebruikt u het claim controle patroon dat in [dit blog bericht](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern)wordt beschreven.

## <a name="troubleshooting"></a>Problemen oplossen
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Waarom kan ik geen naam ruimte maken nadat ik deze heb verwijderd uit een ander abonnement? 
Wanneer u een naam ruimte uit een abonnement verwijdert, wacht u vier uur voordat u deze opnieuw maakt met dezelfde naam in een ander abonnement. Anders wordt het volgende fout bericht weer gegeven: `Namespace already exists` . 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Wat zijn de uitzonde ringen die worden gegenereerd door Azure Service Bus Api's en de voorgestelde acties?
Zie [overzicht van uitzonde ringen][Exceptions overview]voor een lijst met mogelijke service bus uitzonde ringen.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Wat is een Shared Access Signature en welke talen ondersteunen het genereren van een hand tekening?
Shared Access Signatures zijn een verificatie methode op basis van SHA-256 Secure hashes of Uri's. Zie het artikel over [gedeelde toegangs handtekeningen][Shared Access Signatures] voor informatie over het genereren van uw eigen hand tekeningen in Node.js, PHP, Java, python en C#.

## <a name="subscription-and-namespace-management"></a>Abonnement-en naam ruimte beheer
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hoe kan ik een naam ruimte migreren naar een ander Azure-abonnement?

U kunt een naam ruimte van het ene Azure-abonnement naar het andere verplaatsen met behulp van de [Azure Portal](https://portal.azure.com) -of Power shell-opdrachten. De naam ruimte moet al actief zijn om de bewerking uit te voeren. De gebruiker die de opdrachten uitvoert, moet een beheerder zijn voor zowel de bron-als de doel abonnementen.

#### <a name="portal"></a>Portal

Volg de [onderstaande](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal)instructies als u de Azure Portal wilt gebruiken om service bus naam ruimten te migreren naar een ander abonnement. 

#### <a name="powershell"></a>PowerShell

Met de volgende reeks Power shell-opdrachten wordt een naam ruimte van het ene Azure-abonnement naar het andere verplaatst. Als u deze bewerking wilt uitvoeren, moet de naam ruimte al actief zijn. de gebruiker die de Power shell-opdrachten uitvoert, moet een beheerder zijn voor zowel de bron-als de doel abonnementen.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```
## <a name="is-it-possible-to-disable-tls-10-or-11-on-service-bus-namespaces"></a>Is het mogelijk om TLS 1,0 of 1,1 op Service Bus naam ruimten uit te scha kelen?
Nee. Het is niet mogelijk om TLS 1,0 of 1,1 uit te scha kelen voor Service Bus naam ruimten. Gebruik TLS 1,2 of hoger in uw client toepassingen die verbinding maken met Service Bus. Zie [het afdwingen van TLS 1,2-gebruik met Azure service bus-micro soft tech Community](https://techcommunity.microsoft.com/t5/messaging-on-azure/enforcing-tls-1-2-use-with-azure-service-bus/ba-p/370912)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over Service Bus:

* [Inleiding tot Azure Service Bus Premium (blog bericht)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introductie van Azure Service Bus Premium (Channel 9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Overzicht van Service Bus](service-bus-messaging-overview.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
