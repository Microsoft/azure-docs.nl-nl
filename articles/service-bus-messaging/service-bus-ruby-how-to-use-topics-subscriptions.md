---
title: 'Quickstart: Service Bus-onderwerpen gebruiken (Ruby)'
description: 'Quickstart: Informatie over het gebruik van Service Bus-onderwerpen en -abonnementen in Azure. Codevoorbeelden zijn geschreven voor Ruby-toepassingen.'
services: service-bus-messaging
documentationcenter: ruby
ms.devlang: ruby
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: aba326a63558632bee3bf0c48d34e471bbe30886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88067559"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Quickstart: Service Bus-onderwerpen en -abonnementen gebruiken met Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In dit artikel wordt beschreven hoe u Service Bus-onderwerpen en -abonnementen gebruikt met Ruby-toepassingen. De behandelde scenario's zijn:

- Onderwerpen en abonnementen maken 
- Abonnementsfilters maken 
- Berichten verzenden naar een onderwerp 
- Berichten van een abonnement ontvangen
- Onderwerpen en abonnementen verwijderen


## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als Visual Studio- of MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Volg de stappen in de [Quickstart: De Azure-portal gebruiken om een Service Bus-onderwerp en abonnementen te maken voor het onderwerp](service-bus-quickstart-topics-subscriptions-portal.md) om een Service Bus-**naamruimte** te maken en de **verbindingsreeks** op te halen. 

    > [!NOTE]
    > In deze quickstart maakt u een **onderwerp** en een **abonnement** voor het onderwerp door **Ruby** te gebruiken. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Een onderwerp maken
Met het **Azure::ServiceBusService**-object kunt u werken met onderwerpen. Met de volgende code wordt een **Azure::ServiceBusService**-object gemaakt. Als u een onderwerp wilt maken, gebruikt u de methode `create_topic()`. In het volgende voorbeeld wordt een onderwerp gemaakt of worden eventuele fouten afgedrukt.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

U kunt ook een **Azure::ServiceBus::Topic**-object doorgeven met extra opties. Hiermee kunt u de standaardinstellingen voor het onderwerp overschrijven, zoals de TTL-waarde voor een bericht of de maximale wachtrijgrootte. In het volgende voorbeeld ziet u hoe u de maximale wachtrijgrootte instelt op 5 GB en de TTL-waarde op 1 minuut:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Abonnementen maken
Er worden ook onderwerpabonnementen gemaakt met het object **Azure::ServiceBusService**. Abonnementen hebben een naam en kunnen een optioneel filter hebben waarmee de verzameling berichten wordt beperkt die worden bezorgd in de virtuele wachtrij van het abonnement.

Standaard zijn abonnementen permanent. Ze blijven bestaan totdat ze worden verwijderd, of totdat het onderwerp waaraan ze zijn gekoppeld wordt verwijderd. Als uw toepassing logica bevat om een abonnement te maken, moet deze eerst de getSubscription-methode gebruiken om te controleren of het abonnement al bestaat.

U kunt de abonnementen automatisch laten verwijderen door de [eigenschap AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle)in te stellen.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Een abonnement maken met het standaardfilter (MatchAll)
Het **MatchAll**-filter (standaard) wordt gebruikt als er bij het maken van een nieuw abonnement geen filter is opgegeven. Bij gebruik van het **MatchAll**-filter worden alle berichten die naar het onderwerp worden gepubliceerd, in de virtuele wachtrij van het abonnement geplaatst. Met het volgende voorbeeld maakt u een abonnement met de naam 'all-messages' en wordt het standaardfilter **MatchAll** gebruikt.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Abonnementen met filters maken
U kunt ook filters definiëren waarmee u kunt opgeven welke berichten die naar een onderwerp worden verzonden, moeten worden weergegeven in een bepaald abonnement.

Het meest flexibele type filter dat wordt ondersteund door abonnementen is **Azure::ServiceBus::SqlFilter**, waarmee een subset van SQL92 wordt geïmplementeerd. SQL-filters worden uitgevoerd voor de eigenschappen van de berichten die worden gepubliceerd naar het onderwerp. Zie de [SqlFilter](service-bus-messaging-sql-filter.md)-syntaxis voor meer informatie over de expressies die kunnen worden gebruikt met een SQL-filter.

Filters kunnen worden toegevoegd aan een abonnement met behulp van de methode `create_rule()` van het object **Azure::ServiceBusService**. Met deze methode kunt u nieuwe filters toevoegen aan een bestaand abonnement.

Omdat het standaardfilter automatisch wordt toegepast op alle nieuwe abonnementen, moet u eerst het standaardfilter verwijderen. Anders overschrijft **MatchAll** alle andere filters die u eventueel opgeeft. U kunt de standaardregel verwijderen met de methode `delete_rule()` van het object **ServiceBusService**.

In het volgende voorbeeld wordt een abonnement genaamd 'high-messages' gemaakt met een **Azure::ServiceBus::SqlFilter** dat alleen berichten selecteert die een aangepaste `message_number`-eigenschap groter dan 3 hebben:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Op dezelfde manier wordt in het volgende voorbeeld een abonnement met de naam `low-messages` gemaakt met een **Azure::ServiceBus::SqlFilter** waarmee alleen berichten worden geselecteerd die een `message_number`-eigenschap kleiner dan of gelijk aan 3 hebben:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Wanneer er nu een bericht naar `test-topic` wordt verzonden, wordt het altijd bezorgd bij ontvangers die zijn geabonneerd op het `all-messages`-onderwerpabonnement, en selectief bezorgd bij ontvangers die zijn geabonneerd op de onderwerpabonnementen `high-messages` en `low-messages` (afhankelijk van de inhoud van het bericht).

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp
Om een bericht naar een Service Bus-onderwerp te verzenden, moet uw toepassing de methode `send_topic_message()` van het object **Azure::ServiceBusService** aanroepen. Berichten die worden verzonden naar Service Bus-onderwerpen zijn exemplaren van de **Azure::ServiceBus::BrokeredMessage**-objecten. **Azure::ServiceBus::BrokeredMessage**-objecten hebben een aantal standaardeigenschappen (zoals `label` en `time_to_live`), een woordenlijst die wordt gebruikt om aangepaste, toepassingsspecifieke eigenschappen te bewaren en een hoofdtekst met tekenreeksgegevens. Een toepassing kan de hoofdtekst van het bericht instellen door een tekenreekswaarde door te geven aan de methode `send_topic_message()`, en alle vereiste standaardeigenschappen worden gevuld met standaardwaarden.

In het volgende voorbeeld ziet u hoe u vijf testberichten naar `test-topic` verzendt. De aangepaste eigenschapswaarde `message_number` van elk bericht varieert afhankelijk van de herhaling van de lus (deze bepaalt welk abonnement het bericht ontvangt):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een onderwerp, maar er is een limiet voor de totale grootte van de berichten in een onderwerp. De grootte van het onderwerp wordt gedefinieerd tijdens het maken, met een bovengrens van 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Berichten van een abonnement ontvangen
Berichten worden van een abonnement ontvangen met behulp van de methode `receive_subscription_message()` van het object **Azure::ServiceBusService**. Standaard worden berichten gelezen (pieken) en vergrendeld zonder dat ze uit het abonnement worden verwijderd. U kunt het bericht lezen en verwijderen vanuit het abonnement door de `peek_lock`-optie in te stellen op **false**.

Bij het standaardgedrag is het lezen en verwijderen een bewerking met twee fasen, waardoor er toepassingen kunnen worden ondersteund die geen ontbrekende berichten kunnen tolereren. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing het bericht heeft verwerkt (of het veilig heeft opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangstproces voltooid door de methode `delete_subscription_message()` aan te roepen en het te verwijderen bericht door te geven als een parameter. Met de methode `delete_subscription_message()` wordt het bericht gemarkeerd als verwerkt en wordt het uit het abonnement verwijderd.

Als de parameter `:peek_lock` is ingesteld op **false**, wordt het lezen van het bericht en het verwijderen het eenvoudigste model. Dit werkt het beste voor scenario's waarin een toepassing het niet verwerken van een bericht bij een fout kan tolereren. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt. Omdat Service Bus het bericht als verbruikt heeft gemarkeerd, ontbreekt het bericht dat voor het vastlopen is verbruikt wanneer de toepassing opnieuw wordt gestart en het verbruik van berichten opnieuw begint.

Het volgende voorbeeld laat zien hoe berichten kunnen worden ontvangen en verwerkt met behulp van `receive_subscription_message()`. In het voorbeeld wordt eerst een bericht ontvangen en verwijderd vanuit het abonnement `low-messages` met `:peek_lock` ingesteld op **false** en wordt er vervolgens nog een bericht van `high-messages` ontvangen en verwijderd met `delete_subscription_message()`:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de toepassing en onleesbare berichten afhandelen
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing het bericht om de een of andere reden niet kan verwerken, kan deze de `unlock_subscription_message()`-methode aanroepen voor het **Azure::ServiceBusService**-object. Dit zorgt ervoor dat Service Bus het bericht in het abonnement ontgrendelt en het beschikbaar maakt om opnieuw te worden ontvangen, ofwel door dezelfde consumerende toepassing of door een andere consumerende toepassing.

Daarnaast is er een time-out gekoppeld aan een bericht dat in het abonnement is vergrendeld. Als de toepassing het bericht niet kan verwerken voordat de time-out van de vergrendeling verloopt (bijvoorbeeld als de toepassing vastloopt), ontgrendelt Service Bus het bericht automatisch en wordt het beschikbaar gemaakt om opnieuw te worden ontvangen.

Als de toepassing vastloopt na het verwerken van het bericht, maar voordat de methode `delete_subscription_message()` wordt aangeroepen, wordt het bericht opnieuw bij de toepassing bezorgd wanneer deze opnieuw wordt gestart. Dit wordt vaak *ten minste eenmaal verwerken* genoemd; dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt maar dat hetzelfde bericht in sommige situaties opnieuw kan worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, dan moeten toepassingsontwikkelaars extra logica toevoegen aan de toepassing om dubbele berichtbezorging af te handelen. Deze logica wordt vaak bereikt met de `message_id`-eigenschap van het bericht, dat gelijk blijft bij meerdere bezorgingspogingen.

## <a name="delete-topics-and-subscriptions"></a>Onderwerpen en abonnementen verwijderen
Onderwerpen en abonnementen zijn permanent, tenzij de [eigenschap AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) is ingesteld. Ze kunnen programmatisch of via de [Azure-portal][Azure portal] worden verwijderd. In het volgende voorbeeld ziet u hoe u het onderwerp met de naam `test-topic` kunt verwijderen.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Als een onderwerp wordt verwijderd, worden ook alle abonnementen verwijderd die zijn geregistreerd bij het onderwerp. Abonnementen kunnen ook afzonderlijk worden verwijderd. De volgende code laat zien hoe u het abonnement met de naam `high-messages` uit het onderwerp `test-topic` kunt verwijderen:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> U kunt resources van Service Bus beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus-naamruimte en berichtenentiteiten op een eenvoudige manier beheren. Het hulpprogramma biedt geavanceerde functies zoals functionaliteit voor importeren/exporteren of de mogelijkheid van het testen van onderwerpen, wachtrijen, abonnementen, relay-services, Notification Hubs en Event Hubs. 

## <a name="next-steps"></a>Volgende stappen
Nu u de basisprincipes van Service Bus-onderwerpen hebt geleerd, kunt u deze koppelingen volgen voor meer informatie.

* Zie [Wachtrijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md).
* API-naslaginformatie voor [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Ga naar de opslagplaats [Azure-SDK voor Ruby](https://github.com/Azure/azure-sdk-for-ruby) op GitHub.

[Azure portal]: https://portal.azure.com