---
title: Azure Service Bus-wachtrijen gebruiken met Ruby
description: In deze zelfstudie leert u hoe u Ruby-toepassingen kunt maken om berichten te verzenden naar en te ontvangen van een Service Bus-wachtrij.
documentationcenter: ruby
ms.devlang: ruby
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 16dda6fc4637f052514a0e78a0804bf4702ed20b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85336642"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Quickstart: Service Bus-wachtrijen gebruiken met Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In deze zelfstudie leert u hoe u Ruby-toepassingen kunt maken om berichten te verzenden naar en te ontvangen van een Service Bus-wachtrij. De voorbeelden zijn geschreven in Ruby en maken gebruik van de Azure-gem.

## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Volg de stappen in het artikel [Azure Portal gebruiken om een Service Bus-wachtrij te maken](service-bus-quickstart-portal.md).
    1. Lees het beknopte **overzicht** van Service Bus-**wachtrijen**. 
    2. Maak een Service Bus-**naamruimte**. 
    3. Haal de **verbindingsreeks** op. 

        > [!NOTE]
        > In deze zelfstudie maakt u een **wachtrij** in de Service Bus-naamruimte met behulp van Ruby. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Een wachtrij maken
Met het **Azure::ServiceBusService**-object kunt u werken met wachtrijen. Als u een wachtrij wilt maken, gebruikt u de methode `create_queue()`. In het volgende voorbeeld wordt een wachtrij gemaakt of worden eventuele fouten afgedrukt.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

U kunt ook een **Azure::ServiceBus::Queue**-object doorgeven met extra opties. Hiermee kunt u de standaardinstellingen voor de wachtrij overschrijven, zoals de TTL-waarde voor een bericht of de maximale wachtrijgrootte. In het volgende voorbeeld ziet u hoe u de maximale wachtrijgrootte instelt op 5 GB en de TTL-waarde op 1 minuut:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Berichten naar een wachtrij verzenden
Als u een bericht naar een Service Bus-wachtrij wilt verzenden, wordt door de toepassing de `send_queue_message()`-methode voor het **Azure::ServiceBusService**-object aangeroepen. Berichten die worden verzonden naar (en ontvangen van) Service Bus-wachtrijen zijn **Azure::ServiceBus::BrokeredMessage**-objecten en hebben enkele standaardeigenschappen (zoals `label` en `time_to_live`), een woordenlijst die wordt gebruikt om aangepaste toepassingsspecifieke eigenschappen te bewaren en hoofdtekst met willekeurige toepassingsgegevens. Een toepassing kan de hoofdtekst van het bericht instellen door een tekenreekswaarde als het bericht door te geven. Alle vereiste standaardeigenschappen worden gevuld met standaardwaarden.

In het volgende voorbeeld ziet u hoe u een testbericht naar de wachtrij met de naam `test-queue` verzendt met behulp van `send_queue_message()`:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een wachtrij, maar er is een limiet voor de totale grootte van de berichten in een wachtrij. De grootte van de wachtrij wordt gedefinieerd tijdens het aanmaken, met een bovengrens van 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen
Berichten worden van een wachtrij ontvangen met behulp van de methode `receive_queue_message()` voor het **Azure::ServiceBusService**-object. Berichten worden standaard gelezen en vergrendeld zonder dat ze uit de wachtrij worden verwijderd. U kunt echter berichten uit de wachtrij verwijderen wanneer ze zijn gelezen door de optie `:peek_lock` op **false** in te stellen.

Bij het standaardgedrag is het lezen en verwijderen een bewerking met twee fasen, waardoor er toepassingen kunnen worden ondersteund die geen ontbrekende berichten kunnen tolereren. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing het bericht heeft verwerkt (of het veilig heeft opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangstproces voltooid door de methode `delete_queue_message()` aan te roepen en het te verwijderen bericht door te geven als een parameter. Met de methode `delete_queue_message()` wordt het bericht gemarkeerd als verbruikt en wordt het uit de wachtrij verwijderd.

Als de parameter `:peek_lock` is ingesteld op **false**, wordt het lezen van het bericht en het verwijderen het eenvoudigste model. Dit werkt het beste voor scenario's waarin een toepassing het niet verwerken van een bericht bij een fout kan tolereren. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt. Omdat Service Bus het bericht als verbruikt heeft gemarkeerd, ontbreekt het bericht dat voor het vastlopen is verbruikt wanneer de toepassing opnieuw wordt gestart en het verbruik van berichten opnieuw begint.

Het volgende voorbeeld laat zien hoe berichten kunnen worden ontvangen en verwerkt met behulp van `receive_queue_message()`. In het voorbeeld wordt eerst een bericht ontvangen en verwijderd met `:peek_lock` ingesteld op **false** en wordt er vervolgens nog een bericht ontvangen en verwijderd met `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de toepassing en onleesbare berichten afhandelen
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing het bericht om de een of andere reden niet kan verwerken, kan deze de `unlock_queue_message()`-methode aanroepen voor het **Azure::ServiceBusService**-object. Deze aanroep zorgt ervoor dat Service Bus het bericht in de wachtrij ontgrendelt en het beschikbaar maakt om opnieuw te worden ontvangen, ofwel door dezelfde consumerende toepassing of door een andere consumerende toepassing.

Daarnaast is er een time-out gekoppeld aan een bericht dat in de wachtrij is vergrendeld. Als de toepassing het bericht niet kan verwerken voordat de time-out van de vergrendeling verloopt (bijvoorbeeld als de toepassing vastloopt), ontgrendelt Service Bus het bericht automatisch en wordt het beschikbaar gemaakt om opnieuw te worden ontvangen.

Als de toepassing vastloopt na het verwerken van het bericht, maar voordat de methode `delete_queue_message()` wordt aangeroepen, wordt het bericht opnieuw bij de toepassing bezorgd wanneer deze opnieuw wordt gestart. Dit proces wordt vaak *Ten minste eenmaal verwerken* genoemd; dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt maar dat hetzelfde bericht in sommige situaties opnieuw kan worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, dan moeten toepassingsontwikkelaars extra logica toevoegen aan de toepassing om dubbele berichtbezorging af te handelen. Dit wordt vaak bereikt met de `message_id`-eigenschap van het bericht die gelijk blijft bij meerdere bezorgingspogingen.

> [!NOTE]
> U kunt resources van Service Bus beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus-naamruimte en berichtenentiteiten op een eenvoudige manier beheren. Het hulpprogramma biedt geavanceerde functies zoals functionaliteit voor importeren/exporteren of de mogelijkheid van het testen van onderwerpen, wachtrijen, abonnementen, relay-services, Notification Hubs en Event Hubs. 

## <a name="next-steps"></a>Volgende stappen
Nu u de basisprincipes van Service Bus-wachtrijen hebt geleerd, kunt u deze koppelingen volgen voor meer informatie.

* Overzicht van [wachtrijen, onderwerpen en abonnementen](service-bus-queues-topics-subscriptions.md).
* Ga naar de opslagplaats [Azure-SDK voor Ruby](https://github.com/Azure/azure-sdk-for-ruby) op GitHub.

Voor een vergelijking van de Azure Service Bus-wachtrijen die in dit artikel worden besproken en Azure-wachtrijen die worden beschreven in het artikel [Queue Storage gebruiken vanuit Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) raadpleegt u [Azure-wachtrijen en Azure Service Bus-wachtrijen: overeenkomsten en verschillen](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

