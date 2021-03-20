---
title: RabbitMQ integreren met Azure Service Bus
description: Stapsgewijze hand leiding voor het integreren van RabbitMQ met Azure Service Bus
services: Azure Service Bus
author: videlalvaro
ms.topic: how-to
ms.service: service-bus
ms.date: 07/02/2020
ms.author: alvidela
ms.openlocfilehash: 6366824b8dc7f63f99ebda2a542d95d3eb1c6146
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91301102"
---
# <a name="how-to-integrate-rabbitmq-with-azure-service-bus"></a>RabbitMQ integreren met Azure Service Bus

In deze hand leiding wordt beschreven hoe u berichten verzendt van RabbitMQ naar Azure Service Bus.

Hier volgen enkele scenario's waarin we gebruik kunnen maken van deze mogelijkheden:

- **Edge-instellingen**: er is een Edge-instelling waarbij we berichten verzenden naar RabbitMQ, maar we willen deze berichten door sturen naar [Azure service bus](./service-bus-messaging-overview.md) voor verdere verwerking. Daarom kunnen we veel van de [Azure Big Data-mogelijkheden](/azure/architecture/guide/architecture-styles/big-data)gebruiken.
- **Hybride Cloud**: uw bedrijf heeft een derde partij aangeschaft die gebruikmaakt van RabbitMQ voor hun bericht behoeften. Ze bevinden zich in een andere cloud. Tijdens de overgang naar Azure kunt u al gegevens delen door de RabbitMQ met Azure Service Bus te bridgen.
- **Integratie** van derden: een derde partij gebruikt RabbitMQ als een Broker en wil hun gegevens naar ons verzenden, maar ze zijn ook buiten onze organisatie. We kunnen ze voorzien van een SAS-sleutel waarmee ze toegang hebben tot een beperkt aantal Azure Service Bus wacht rijen waarnaar ze hun berichten kunnen door sturen.

De lijst gaat in, maar we kunnen de meeste van deze use cases oplossen door RabbitMQ naar Azure te bridgen.

Eerst moet u een gratis Azure-account maken door u [hier](https://azure.microsoft.com/free/) aan te melden

Zodra u bent aangemeld bij uw account, gaat u naar de [Azure Portal](https://portal.azure.com/) en maakt u een nieuwe Azure service bus [naam ruimte](./service-bus-create-namespace-portal.md). Naam ruimten zijn de bereik containers waar de Messa ging-onderdelen Live zullen zijn, zoals wacht rijen en onderwerpen.

## <a name="adding-a-new-azure-service-bus-namespace"></a>Een nieuwe Azure Service Bus naam ruimte toevoegen

Klik in Azure Portal op de knop grote plus om een nieuwe resource toe te voegen

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="Bron maken":::

Selecteer vervolgens integratie en klik op Azure Service Bus om een bericht naam ruimte te maken:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="Azure service bus selecteren":::

U wordt gevraagd de naam ruimte gegevens in te voeren. Selecteer het Azure-abonnement dat u wilt gebruiken. Als u geen [resource groep](../azure-resource-manager/management/manage-resource-groups-portal.md)hebt, kunt u een nieuwe maken.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="Een naamruimte maken":::

Gebruiken `rabbitmq` voor `Namespace name` , maar dit is mogelijk alles wat u wilt. Vervolgens ingesteld `East US` voor de locatie. Kies `Basic` als prijs categorie.

Als alles goed is, ziet u het volgende bevestigings scherm:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="Naam ruimte bevestiging maken":::

Vervolgens ziet u de nieuwe naam ruimte in het Azure Portal weer `rabbitmq` gegeven. Klik erop om toegang te krijgen tot de resource zodat u er een wachtrij aan kunt toevoegen.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="Resource lijst met nieuwe naam ruimte":::

## <a name="creating-our-azure-service-bus-queue"></a>De wachtrij voor Azure Service Bus maken

Nu u uw Azure Service Bus-naam ruimte hebt, klikt u op de `Queues` knop aan de linkerkant, `Entities` zodat u een nieuwe wachtrij kunt toevoegen:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="Wachtrij maken":::

De naam van de wachtrij is `from-rabbitmq` net zo als een herinnering dat de berichten afkomstig zijn van. U kunt alle andere opties als standaard instellingen laten staan, maar u kunt ze aanpassen aan de behoeften van uw app.

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>De RabbitMQ Shovel-invoeg toepassing inschakelen

Als u berichten van RabbitMQ naar Azure Service Bus wilt verzenden, gaan we de [Shovel-invoeg toepassing](https://www.rabbitmq.com/shovel.html) gebruiken die wordt geleverd met RabbitMQ. U kunt de invoeg toepassing en de bijbehorende Visual-Interface inschakelen met deze opdracht:

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>Mogelijk moet u deze opdracht uitvoeren als root.

Het is nu tijd om de referenties op te halen die nodig zijn voor het verbinden van RabbitMQ met Azure.

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>RabbitMQ verbinden met Azure Service Bus

U moet een [Shared Access Policy](../storage/common/storage-sas-overview.md) (SAS) voor uw wachtrij maken, zodat RabbitMQ er berichten naar kan publiceren. Met een SAS-beleid kunt u opgeven wat externe partij mag doen met uw resource. Het idee is dat RabbitMQ berichten kan verzenden, maar de wachtrij niet luistert of beheert.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="SAS-beleid toevoegen":::

Tik op het `Send` vak en klik vervolgens op `Create` om ons SAS-beleid te laten staan.

Wanneer het beleid is gemaakt, klikt u erop om de **primaire verbindings reeks** te zien. We gaan deze gebruiken om RabbitMQ te laten praten met Azure Service Bus:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="SAS-beleid ophalen":::

Voordat u deze connection string kunt gebruiken, moet u deze converteren naar de AMQP-verbindings indeling van RabbitMQ. Ga dus naar het [hulp programma Connection String Converter](https://red-mushroom-0f7446a0f.azurestaticapps.net/) en plak uw Connection String in het formulier, klikt u op converteren. U krijgt een connection string dat RabbitMQ klaar is. (Die website voert alle lokale in uw browser uit zodat uw gegevens niet via het netwerk worden verzonden). U hebt toegang tot de bron code op [github](https://github.com/videlalvaro/connstring_to_amqp).

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="connection string converteren":::

Open nu de RabbitMQ Management-invoeg toepassing in de browser `http://localhost:15672/#/dynamic-shovels` en ga naar `Admin -> Shovel Management` , waar u de nieuwe Shovel kunt toevoegen waarmee berichten van een RabbitMQ-wachtrij naar uw Azure service bus wachtrij worden verzonden.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="RabbitMQ Shovel toevoegen":::

Bel uw Shovel `azure` en kies `AMQP 0.9.1` als het bron protocol. In de scherm opname hebben we `amqp://` de standaard-URI die ons verbindt met een lokale RabbitMQ-server. Zorg ervoor dat u past bij uw huidige implementatie.

Op de wachtrij kant kunt u gebruiken `azure` als de naam van uw wachtrij. Als deze wachtrij niet bestaat, wordt deze voor u gemaakt met RabbitMQ. U kunt ook de naam van een wachtrij kiezen die al bestaat. U kunt de andere opties als standaard laten staan.

Kies vervolgens aan de `destination` zijkant van dingen `AMQP 1.0` het protocol. In het `URI` veld voert u de verbindings reeks in die u hebt ontvangen van de vorige stap, hebt u uw Azure-Connection String geconverteerd naar de RabbitMQ-indeling. Dit ziet er als volgt uit:

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

In het `Address` veld voert u de naam in van uw **Azure service bus wachtrij**. in dit geval is deze aangeroepen `from-rabbitmq` . Klik op `Add Shovel` om te beginnen met het ontvangen van berichten.

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>Berichten publiceren van RabbitMQ naar Azure Service Bus

Ga in de RabbitMQ-beheer interface naar `Queues` , selecteer de `azure` wachtrij en zoek het `Publish message` paneel. Er wordt een formulier weer gegeven waarin u berichten rechtstreeks naar uw wachtrij kunt publiceren. In ons voor beeld gaan we het volgende toevoegen `fist message` als `Payload` en `Publish Message` :

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="Eerste bericht publiceren":::

Ga terug naar Azure en controleer uw wachtrij. Klik `Service Bus Explorer` in het linkerdeel venster en klik vervolgens op de knop _Peek_ . Als alles goed is, ziet u de wachtrij nu één bericht. Yay, gefeliciteerd!

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="Azure Service Bus wachtrij":::

U moet er wel voor zorgen dat het bericht wordt verzonden via RabbitMQ. Selecteer het `Peek` tabblad en klik op de `Peek` knop om de laatste berichten in uw wachtrij op te halen. Klik op het bericht om de inhoud ervan te controleren. De onderstaande afbeelding ziet er ongeveer uit zoals in de `first message` lijst.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="Wachtrij bekijken":::

## <a name="lets-recap"></a>Laten we samen vatting

Gefeliciteerd! U hebt een hoop! U hebt de volgende stappen uitgevoerd om uw berichten van RabbitMQ naar Azure Service Bus te halen:

1. Een Azure Service Bus naam ruimte maken
2. Een wachtrij toevoegen aan de naam ruimte
3. Een SAS-beleid aan uw wachtrij toevoegen
4. De wachtrij connection string ophalen
5. De RabbitMQ Shovel-invoeg toepassing inschakelen & de beheer interface
6. Azure Service Bus connection string converteren naar de AMQP-indeling van RabbitMQ
7. Voeg een nieuwe Shovel toe aan RabbitMQ & verbind deze met Azure Service Bus
8. Berichten publiceren

Door de vorige stappen te volgen, hebt u gedeelten van uw organisatie geïntegreerd die zich buiten Azure bevonden. Met de shovel-invoeg toepassing kunt u berichten verzenden van RabbitMQ naar Azure Service Bus. Dit heeft enorm voor delen omdat u nu vertrouwde derden toestaat hun apps te verbinden met uw Azure-implementatie.

In het eind is de Messa ging over het inschakelen van verbindingen en met deze techniek hebben we zojuist een nieuw item geopend.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure service bus](./service-bus-messaging-overview.md)
- Meer informatie over [ondersteuning voor AMQP 1,0 in service bus](./service-bus-amqp-overview.md)
