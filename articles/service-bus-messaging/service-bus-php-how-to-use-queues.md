---
title: Azure Service Bus-wachtrijen gebruiken met PHP
description: In deze zelfstudie leert u hoe u PHP-toepassingen kunt maken om berichten te verzenden naar en te ontvangen van een Service Bus-wachtrij.
services: service-bus-messaging
ms.devlang: PHP
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: a7e0d1fa321f1b7c1295b5a640fe78b46adf1c72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85341112"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Quickstart: Service Bus-wachtrijen gebruiken met PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In deze zelfstudie leert u hoe u PHP-toepassingen kunt maken om berichten te verzenden naar en te ontvangen van een Service Bus-wachtrij. 

## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Als u geen wachtrij hebt om te gebruiken, volgt u de stappen in het artikel [De Azure-portal gebruiken om een Service Bus-wachtrij te maken](service-bus-quickstart-portal.md) om een wachtrij te maken.
    1. Lees het beknopte **overzicht** van Service Bus-**wachtrijen**. 
    2. Maak een Service Bus-**naamruimte**. 
    3. Haal de **verbindingsreeks** op. 

        > [!NOTE]
        > In deze zelfstudie maakt u een **wachtrij** in de Service Bus-naamruimte met behulp van PHP. 
3. [Azure-SDK voor PHP](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>Een PHP-toepassing maken
De enige vereiste voor het maken van een PHP-toepassing die toegang heeft tot de Azure Blob-service, is te verwijzen naar klassen in de [Azure-SDK voor PHP](https://github.com/Azure/azure-sdk-for-php) in uw code. U kunt voor het maken van de toepassing elk ontwikkelprogramma, of Kladblok, gebruiken.

> [!NOTE]
> Voor de PHP-installatie moet ook de [OpenSSL-extensie](https://php.net/openssl) zijn geïnstalleerd en ingeschakeld.

In deze handleiding gebruikt u servicefuncties die lokaal kunnen worden aangeroepen vanuit een PHP-toepassing of in de code die wordt uitgevoerd in een Azure-webrol, -werkrol of -website.

## <a name="get-the-azure-client-libraries"></a>De Azure-clientbibliotheken ophalen
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren voor het gebruik van Service Bus
Ga als volgt te werk om de Service Bus-wachtrij-API's te gebruiken:

1. Maak een verwijzing naar het autoloaderbestand met de instructie [require_once][require_once].
2. Maak een verwijzing naar alle klassen die u mogelijk gebruikt.

Het volgende voorbeeld laat zien hoe u het autoloaderbestand opneemt en een verwijzing maakt naar de klasse `ServicesBuilder`.

> [!NOTE]
> In dit voorbeeld (en andere voorbeelden in dit artikel) wordt ervan uitgegaan dat u de PHP-clientbibliotheken voor Azure hebt geïnstalleerd via Composer. Als u de bibliotheken handmatig of als een PEAR-pakket hebt geïnstalleerd, moet u verwijzen naar het **WindowsAzure.php**-autoloaderbestand.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

In de onderstaande voorbeelden wordt de instructie `require_once` altijd weergegeven, maar er wordt alleen verwezen naar de klassen die nodig zijn om het voorbeeld uit te voeren.

## <a name="set-up-a-service-bus-connection"></a>Een Service Bus-verbinding instellen
U moet eerst over een geldige verbindingsreeks in de volgende indeling beschikken voordat u een instantie kunt maken van een Service Bus-client:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Hierbij heeft `Endpoint` doorgaans de indeling `[yourNamespace].servicebus.windows.net`.

Als u een Azure-serviceclient wilt maken, moet u de klasse `ServicesBuilder` gebruiken. U kunt:

* De verbindingsreeks rechtstreeks doorgeven aan deze klasse.
* De **CCM (CloudConfigurationManager)** gebruiken om meerdere externe bronnen te controleren op de verbindingsreeks:
  * Deze wordt standaard geleverd met ondersteuning voor één externe bron: omgevingsvariabelen
  * U kunt nieuwe bronnen toevoegen door de klasse `ConnectionStringSource` uit te breiden

In de voorbeelden die hier worden beschreven, wordt de verbindingsreeks rechtstreeks doorgegeven.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Een wachtrij maken
U kunt beheerbewerkingen uitvoeren voor Service Bus-wachtrijen via de klasse `ServiceBusRestProxy`. Een `ServiceBusRestProxy`-object wordt samengesteld via de Factory-methode `ServicesBuilder::createServiceBusService` met een geschikte verbindingsreeks die de tokenmachtigingen bevat waarmee het wordt beheerd.

In het volgende voorbeeld ziet u hoe u een `ServiceBusRestProxy` wordt geïnstantieerd en `ServiceBusRestProxy->createQueue` wordt aangeroepen om een wachtrij met de naam `myqueue` in te maken een `MySBNamespace`-servicenaamruimte:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> U kunt de methode `listQueues` voor `ServiceBusRestProxy`-objecten gebruiken om te controleren of een wachtrij met een opgegeven naam al bestaat in een servicenaamruimte.
> 
> 

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
Met de toepassing wordt de methode `ServiceBusRestProxy->sendQueueMessage` aangeroepen om een bericht naar een Service Bus-wachtrij te verzenden. De volgende code laat zien hoe u een bericht verzendt naar de `myqueue`-wachtrij die eerder is gemaakt in de naamruimte van de `MySBNamespace`-service.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

De berichten die worden verzonden naar (en ontvangen van) Service Bus-wachtrijen, zijn exemplaren van de klasse [BrokeredMessage][BrokeredMessage]. [BrokeredMessage][BrokeredMessage]-objecten hebben een aantal standaardmethoden en -eigenschappen, die worden gebruikt om aangepaste toepassingseigenschappen te bewaren, en een hoofdtekst met willekeurige toepassingsgegevens.

Service Bus-wachtrijen ondersteunen een maximale berichtgrootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een wachtrij, maar er is een limiet voor de totale grootte van de berichten in een wachtrij. Deze bovengrens voor de grootte van de wachtrij is 5 GB.

## <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen

De beste manier om berichten van een wachtrij te ontvangen is met behulp van een methode `ServiceBusRestProxy->receiveQueueMessage`. Berichten kunnen in twee verschillende modi worden ontvangen: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) en [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). **PeekLock** is de standaardmodus.

Wanneer u de modus [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) gebruikt, wordt het ontvangen in één bewerking uitgevoerd. Dit betekent dat als Service Bus een leesaanvraag voor een bericht in een wachtrij ontvangt, het bericht wordt gemarkeerd als Wordt verbruikt, en dat het bericht naar de toepassing wordt geretourneerd. De [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode)-modus is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing het niet verwerken van een bericht bij een fout kan tolereren. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt. Omdat Service Bus het bericht als verwerkt heeft gemarkeerd, ontbreekt het bericht dat vóór het vastlopen is verwerkt, wanneer de toepassing opnieuw wordt gestart en het verwerken van berichten opnieuw begint.

In de standaardmodus [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) wordt de ontvangst van een bericht een bewerking met twee fasen, waardoor er toepassingen kunnen worden ondersteund die geen ontbrekende berichten tolereren. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden. Het bericht wordt vergrendeld om te voorkomen dat andere consumenten het ontvangen, en vervolgens naar de toepassing geretourneerd. Nadat de toepassing klaar is met de verwerking van het bericht (of nadat het bericht veilig is opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangstproces voltooid door het ontvangen bericht door te geven aan `ServiceBusRestProxy->deleteMessage`. Wanneer Service Bus de aanroep `deleteMessage` waarneemt, wordt het bericht gemarkeerd als verbruikt en uit de wachtrij verwijderd.

In het volgende voorbeeld ziet u hoe u een bericht ontvangt en verwerkt met de [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock)-modus (de standaardmodus).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Het vastlopen van de toepassing en onleesbare berichten afhandelen

Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing het bericht om de een of andere reden niet kan verwerken, kan de methode `unlockMessage` voor het ontvangen bericht worden aangeroepen (in plaats van de methode `deleteMessage`). Dit zorgt ervoor dat Service Bus het bericht in de wachtrij ontgrendelt en het beschikbaar maakt om opnieuw te worden ontvangen, ofwel door dezelfde toepassing die het gebruikt of door een andere toepassing.

Daarnaast is er een time-out gekoppeld aan een bericht dat in de wachtrij is vergrendeld. Als de toepassing het bericht niet kan verwerken voordat de time-out van de vergrendeling verloopt (bijvoorbeeld als de toepassing vastloopt), ontgrendelt Service Bus het bericht automatisch en wordt het opnieuw beschikbaar gemaakt voor ontvangst.

In het geval dat de toepassing is vastgelopen na het verwerken van het bericht, maar voordat de aanvraag `deleteMessage` is uitgegeven, wordt het bericht vervolgens opnieuw bij de toepassing bezorgd wanneer de toepassing opnieuw wordt gestart. Dit wordt vaak *Ten minste één keer* verwerken genoemd, wat wil zeggen dat elk bericht minstens één keer wordt verwerkt, maar dat hetzelfde bericht in sommige situaties opnieuw kan worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, is het raadzaam om extra logica aan de toepassingen toe te voegen om dubbele berichtbezorging af te handelen. Dit wordt vaak bereikt met de methode `getMessageId` van het bericht dat gelijk blijft bij meerdere bezorgingspogingen.

> [!NOTE]
> U kunt resources van Service Bus beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus-naamruimte en berichtenentiteiten op een eenvoudige manier beheren. Het hulpprogramma biedt geavanceerde functies zoals functionaliteit voor importeren/exporteren of de mogelijkheid van het testen van onderwerpen, wachtrijen, abonnementen, relay-services, Notification Hubs en Event Hubs. 

## <a name="next-steps"></a>Volgende stappen
Nu u bekend bent met de basisprincipes van Service Bus-wachtrijen, kunt u [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions] raadplegen voor meer informatie.

Ga naar het [PHP-ontwikkelaarscentrum](https://azure.microsoft.com/develop/php/) voor meer informatie.

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


