---
title: Azure Relay Veelgestelde vragen | Microsoft Docs
description: In dit artikel vindt u antwoorden op enkele veelgestelde vragen over de Azure Relay-service.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 83cbc4b02bb8b63878abd046ed2314728546e87a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98250045"
---
# <a name="azure-relay-faqs"></a>Veelgestelde vragen over Azure Relay

In dit artikel vindt u antwoorden op enkele veelgestelde vragen (FAQ) over [Azure relay](https://azure.microsoft.com/services/service-bus/). Zie [Veelgestelde vragen over Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor algemene informatie over Azure-prijzen en-ondersteuning.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Algemene vragen
### <a name="what-is-azure-relay"></a>Wat is Azure Relay?
De [Azure Relay-service](relay-what-is-it.md) vereenvoudigt uw hybride toepassingen door u te helpen de services die zich in een bedrijfs netwerk bevinden, veilig beschikbaar te stellen aan de open bare Cloud. U kunt de services beschikbaar maken zonder dat u een firewall verbinding hoeft te openen en zonder dat er onopvallende wijzigingen in de infra structuur van een bedrijfs netwerk nodig zijn.

### <a name="what-is-a-relay-namespace"></a>Wat is een relay-naam ruimte?
Een [naam ruimte](relay-create-namespace-portal.md) is een bereik container die u kunt gebruiken om resources in uw toepassing over te sturen. U moet een naam ruimte maken voor het gebruik van relay. Dit is een van de eerste stappen in aan de slag.

### <a name="what-happened-to-service-bus-relay-service"></a>Wat is er gebeurd met Service Bus Relay-service?
De eerder genoemde Service Bus Relay-service heet nu [Azure relay](service-bus-relay-tutorial.md). U kunt deze service op de gebruikelijke manier blijven gebruiken. De functie Hybride verbindingen is een bijgewerkte versie van een service die is gepland vanuit Azure BizTalk Services. WCF Relay en Hybride verbindingen beide blijven worden ondersteund.

## <a name="pricing"></a>Prijzen
In deze sectie vindt u antwoorden op enkele veelgestelde vragen over de prijs structuur voor relay. U kunt ook de [Veelgestelde vragen over Azure-ondersteuning](https://azure.microsoft.com/support/faq/) bekijken voor algemene informatie over Azure-prijzen. Zie [Service Bus prijs informatie][Pricing overview]voor volledige informatie over de prijzen voor relay.

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Hoe worden de kosten in rekening gebracht voor Hybride verbindingen en WCF Relay?
Zie de tabel [met hybride verbindingen en WCF-Relais][Pricing overview] op de pagina met Service Bus prijs informatie voor volledige informatie over de prijzen voor relay. Naast de prijzen die op die pagina worden vermeld, worden er kosten in rekening gebracht voor de bijbehorende gegevens overdrachten voor uitgaand verkeer buiten het Data Center waarin uw toepassing is ingericht.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Hoe word ik gefactureerd voor Hybride verbindingen?
Hier volgen drie voor beelden van facturerings scenario's voor Hybride verbindingen:

*   Scenario 1:
    *   U hebt één listener, zoals een instantie van de Hybride verbindingen manager die is geïnstalleerd en continu wordt uitgevoerd voor de hele maand.
    *   U verzendt voor de maand 3 GB gegevens over de verbinding. 
    *   Uw totale kosten zijn $5.
*   Scenario 2:
    *   U hebt één listener, zoals een instantie van de Hybride verbindingen manager die is geïnstalleerd en continu wordt uitgevoerd voor de hele maand.
    *   U verzendt gedurende de maand 10 GB aan gegevens over de verbinding.
    *   Uw totale kosten zijn $7,50. Dat is $5 voor de verbinding en de eerste 5 GB + $2,50 voor de extra 5 GB aan gegevens.
*   Scenario 3:
    *   Er zijn twee instanties, A en B, van de Hybride verbindingen Manager geïnstalleerd en continu worden uitgevoerd voor de hele maand.
    *   U verzendt voor de maand 3 GB gegevens over verbinding A.
    *   U verzendt gedurende de maand 6 GB aan gegevens in verbinding B.
    *   Uw totale kosten zijn $10,50. Dat is $5 voor verbinding A + $5 voor verbinding B + $0,50 (voor de zesde Gigabyte op verbinding B).


### <a name="how-are-hours-calculated-for-relay"></a>Hoe worden uren berekend voor relay?

WCF Relay is alleen beschikbaar in de Standard-laag naam ruimten. Prijzen en [verbindings quota's](../service-bus-messaging/service-bus-quotas.md) voor relays, anders zijn niet gewijzigd. Dit betekent dat relays nog steeds worden gefactureerd op basis van het aantal berichten (niet bewerkingen) en relay-uren. Zie de tabel [' hybride verbindingen en WCF relays '](https://azure.microsoft.com/pricing/details/service-bus/) op de pagina prijs informatie voor meer informatie.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Wat moet ik doen als ik meer dan één listener heb verbonden met een specifieke relay?
In sommige gevallen kan één relay meerdere verbonden listeners hebben. Een relay wordt als open beschouwd als er ten minste één relay-listener is verbonden. Het toevoegen van listeners aan een open relay resulteert in extra relay-uren. De berekening van de omleidings uren is niet van invloed op het aantal relay-afzenders (clients die berichten aan relays aanroepen of verzenden die zijn verbonden met een relay).

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Hoe wordt de meter voor berichten berekend voor WCF-relays?
(**Dit geldt alleen voor WCF-relays. Berichten zijn geen kosten voor Hybride verbindingen.**)

Over het algemeen worden factureer bare berichten voor relays berekend met behulp van dezelfde methode die wordt gebruikt voor brokered-entiteiten (wacht rijen, onderwerpen en abonnementen), zoals eerder beschreven. Er zijn echter enkele belang rijke verschillen.

Het verzenden van een bericht naar een Azure Relay wordt behandeld als een ' volledige door ' verzenden naar de relay-listener die het bericht ontvangt. Het wordt niet behandeld als een verzend bewerking naar de Azure Relay, gevolgd door een levering aan de relay-listener. Een aanvraag/antwoord stijl service aanroepen (van Maxi maal 64 KB) voor een relay-listener resulteert in twee factureer bare berichten: één Factureerbaar bericht voor de aanvraag en één Factureerbaar bericht voor het antwoord (uitgaande van het antwoord is ook 64 KB of kleiner). Dit wijkt af van het gebruik van een wachtrij om te worden gebruikt tussen een client en een service. Als u een wachtrij gebruikt voor het oplossen van een client en een service, moet voor hetzelfde antwoord patroon voor de aanvraag een aanvraag worden verzonden naar de wachtrij, gevolgd door een wachtrij/levering uit de wachtrij naar de service. Dit wordt gevolgd door een reactie die wordt verzonden naar een andere wachtrij en een wachtrij/levering van die wachtrij naar de client. Met dezelfde grootte hypo Thesen in (tot 64 KB) resulteert het doorgevoerde wachtrij patroon in vier factureer bare berichten. U wordt gefactureerd voor twee maal het aantal berichten dat moet worden gebruikt voor het implementeren van hetzelfde patroon als door sturen. Natuurlijk zijn er voor delen die wacht rijen gebruiken om dit patroon te halen, zoals duurzaamheid en taak verdeling. Deze voor delen kunnen de extra kosten rechtvaardigen.

Relays die worden geopend met behulp van de WCF-binding van **netTCPRelay** behandelen berichten niet als afzonderlijke berichten, maar als een stroom gegevens stroom via het systeem. Wanneer u deze binding gebruikt, hebben alleen de afzender en de listener inzicht in het framing van de afzonderlijke berichten die zijn verzonden en ontvangen. Voor Relais die de **netTCPRelay** -binding gebruiken, worden alle gegevens behandeld als een stroom voor het berekenen van factureer bare berichten. In dit geval berekent Service Bus de totale hoeveelheid gegevens die via elke afzonderlijke relay wordt verzonden of ontvangen, op een basis van vijf minuten. Vervolgens wordt de totale hoeveelheid gegevens met 64 KB gedeeld om het aantal factureer bare berichten voor die relay tijdens die periode te bepalen.

## <a name="quotas"></a>Quota
| Quotumnaam | Bereik |  Notities | Waarde |
| --- | --- | --- | --- |
| Gelijktijdige listeners op een relay |Entiteit (hybride verbinding of WCF-relay) |Volgende aanvragen voor extra verbindingen worden geweigerd en er wordt een uitzonde ring ontvangen door de aanroepende code. |25 |
| Gelijktijdige relay-verbindingen per alle relay-eind punten in een service naam ruimte |Naamruimte |- |5\.000 |
| Relay-eind punten per service naam ruimte |Naamruimte |- |10.000 |
| Bericht grootte voor [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) -en [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) -relays |Naamruimte |Inkomende berichten die groter zijn dan deze quota worden geweigerd en er wordt een uitzonde ring ontvangen door de aanroepende code. |64 kB |
| Bericht grootte voor [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) -en [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) -relays |Naamruimte |Geen limiet voor de bericht grootte. |Onbeperkt |

### <a name="does-relay-have-any-usage-quotas"></a>Heeft relay gebruiks quota's?
Standaard stelt micro soft voor elke Cloud service een cumulatief maandelijks gebruiks quotum in dat wordt berekend op alle abonnementen van een klant. We begrijpen dat het mogelijk is dat uw behoeften deze limieten overschrijden. U kunt op elk gewenst moment contact opnemen met de klanten service, zodat we uw behoeften begrijpen en deze limieten op de juiste wijze aanpassen. Voor Service Bus zijn de statistische gebruiks quota's als volgt:

* 5.000.000.000 berichten
* 2.000.000 relay-uren

Hoewel wij het recht behouden om een account uit te scha kelen dat de maandelijkse gebruiks quota's overschrijdt, bieden we een e-mail melding en maken we meerdere pogingen om contact op te nemen met de klant alvorens actie te ondernemen. Klanten die deze quota overschrijden, zijn nog steeds verantwoordelijk voor overtollige kosten.

### <a name="naming-restrictions"></a>Naamgevings beperkingen
De naam van een relay-naam ruimte moet tussen 6 en 50 tekens lang zijn.

## <a name="subscription-and-namespace-management"></a>Abonnement-en naam ruimte beheer
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hoe kan ik een naam ruimte migreren naar een ander Azure-abonnement?

Als u een naam ruimte van een Azure-abonnement naar een ander abonnement wilt verplaatsen, kunt u de [Azure Portal](https://portal.azure.com) gebruiken of Power shell-opdrachten gebruiken. Als u een naam ruimte naar een ander abonnement wilt verplaatsen, moet de naam ruimte al actief zijn. De gebruiker die de opdrachten uitvoert, moet een beheerders gebruiker zijn op de bron-en doel abonnementen.

#### <a name="azure-portal"></a>Azure Portal

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal)om de Azure portal te gebruiken om Azure relay naam ruimten van een abonnement te migreren naar een ander abonnement. 

#### <a name="powershell"></a>PowerShell

Als u Power shell wilt gebruiken om een naam ruimte van een Azure-abonnement naar een ander abonnement te verplaatsen, gebruikt u de volgende reeks opdrachten. Als u deze bewerking wilt uitvoeren, moet de naam ruimte al actief zijn en de gebruiker die de Power shell-opdrachten uitvoert, moet een Administrator gebruiker zijn op de bron-en doel abonnementen.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Problemen oplossen
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Wat zijn de uitzonde ringen die worden gegenereerd door Azure Relay Api's en voorgestelde acties die u kunt uitvoeren?
Zie [Relay-uitzonde ringen][Relay exceptions]voor een beschrijving van veelvoorkomende uitzonde ringen en voorgestelde acties die u kunt uitvoeren.

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Wat is een Shared Access Signature en welke talen kan ik gebruiken om een hand tekening te genereren?
Shared Access signatures (SAS) zijn een verificatie methode op basis van SHA-256 Secure hashes of Uri's. Zie [Service Bus verificatie met hand tekeningen voor gedeelde toegang][Shared Access Signatures]voor meer informatie over het genereren van uw eigen hand tekeningen in Node.js, PHP, Python, Java, C en C#.

### <a name="is-it-possible-to-allow-only-some-relay-endpoints"></a>Is het mogelijk om alleen bepaalde relay-eind punten toe te staan?
Ja. De relay-client maakt verbinding met de Azure Relay-service door gebruik te maken van volledig gekwalificeerde domein namen. Klanten kunnen een vermelding voor toevoegen `*.servicebus.windows.net` aan firewalls die ondersteuning bieden voor een DNS-goedkeurings vermelding.

## <a name="next-steps"></a>Volgende stappen
* [Een naamruimte maken](relay-create-namespace-portal.md)
* [Aan de slag met .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Aan de slag met knooppunten](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
