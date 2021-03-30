---
title: Azure Service Bus-onderwerpen gebruiken met PHP
description: In deze zelfstudie leert u hoe u Azure Service Bus-onderwerpen en -abonnementen gebruikt vanuit een PHP-toepassing.
ms.devlang: PHP
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 706f523fdfb3c710bb16b048cfc68ce98875adb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88066199"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Quickstart: Service Bus-onderwerpen en -abonnementen gebruiken met PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In dit artikel wordt beschreven hoe u Service Bus-onderwerpen en -abonnementen gebruikt. De voorbeelden zijn geschreven in PHP en maken gebruik van de [Azure-SDK voor PHP](https://github.com/Azure/azure-sdk-for-php). De behandelde scenario's zijn:

- Onderwerpen en abonnementen maken 
- Abonnementsfilters maken 
- Berichten verzenden naar een onderwerp 
- Berichten van een abonnement ontvangen
- Onderwerpen en abonnementen verwijderen

## <a name="prerequisites"></a>Vereisten
1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als Visual Studio- of MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Volg de stappen in de [Quickstart: De Azure-portal gebruiken om een Service Bus-onderwerp en abonnementen te maken voor het onderwerp](service-bus-quickstart-topics-subscriptions-portal.md) om een Service Bus-**naamruimte** te maken en de **verbindingsreeks** op te halen.

    > [!NOTE]
    > In deze quickstart maakt u een **onderwerp** en een **abonnement** voor het onderwerp door **PHP** te gebruiken. 

## <a name="create-a-php-application"></a>Een PHP-toepassing maken
De enige vereiste voor het maken van een PHP-toepassing die toegang heeft tot de Azure Blob-service, is te verwijzen naar klassen in de [Azure-SDK voor PHP](https://github.com/Azure/azure-sdk-for-php) vanuit uw code. U kunt voor het maken van de toepassing elk ontwikkelprogramma of Kladblok gebruiken.

> [!NOTE]
> Voor de PHP-installatie moet ook de [OpenSSL-extensie](https://php.net/openssl) zijn geïnstalleerd en ingeschakeld.
> 
> 

In dit artikel wordt beschreven hoe u servicefuncties gebruikt die lokaal kunnen worden aangeroepen vanuit een PHP-toepassing of in de code die wordt uitgevoerd in een Azure-webrol, -werkrol of -website.

## <a name="get-the-azure-client-libraries"></a>De Azure-clientbibliotheken ophalen

### <a name="install-via-composer"></a>Installeren via Composer
1. Maak een bestand met de naam **composer.json** in de hoofdmap van uw project en voeg de volgende code toe:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Download **[composer.phar][composer-phar]** naar de hoofdmap van uw project.
3. Open een opdrachtprompt en voer de volgende opdracht uit in de hoofdmap van uw project
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren voor het gebruik van Service Bus
De Service Bus-API's gebruiken:

1. Maak een verwijzing naar het autoloaderbestand met de instructie [require_once][require-once].
2. Maak een verwijzing naar alle klassen die u mogelijk gebruikt.

In het volgende voorbeeld wordt weergegeven hoe u het autoloaderbestand opneemt en een verwijzing maakt naar de klasse **ServiceBusService**.

> [!NOTE]
> In dit voorbeeld (en andere voorbeelden in dit artikel) wordt ervan uitgegaan dat u de PHP-clientbibliotheken voor Azure hebt geïnstalleerd via Composer. Als u de bibliotheken handmatig of als een PEAR-pakket hebt geïnstalleerd, moet u verwijzen naar het **WindowsAzure.php**-autoloaderbestand.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

In de volgende voorbeelden wordt de instructie `require_once` altijd weergegeven, maar er wordt alleen verwezen naar de klassen die nodig zijn om het voorbeeld uit te voeren.

## <a name="set-up-a-service-bus-connection"></a>Een Service Bus-verbinding instellen
U moet eerst over een geldige verbindingsreeks in de volgende indeling beschikken voordat u een instantie kunt maken van een Service Bus-client:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Hierbij heeft `Endpoint` doorgaans de indeling `https://[yourNamespace].servicebus.windows.net`.

Als u een Azure-serviceclient wilt maken, moet u de klasse `ServicesBuilder` gebruiken. U kunt:

* De verbindingsreeks rechtstreeks doorgeven aan deze klasse.
* De **CloudConfigurationManager (CCM)** gebruiken om meerdere externe bronnen te controleren op de verbindingsreeks:
  * Deze wordt standaard geleverd met ondersteuning voor één externe bron: omgevingsvariabelen.
  * U kunt nieuwe bronnen toevoegen door de klasse `ConnectionStringSource` uit te breiden.

In de voorbeelden die hier worden beschreven, wordt de verbindingsreeks rechtstreeks doorgegeven.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Een onderwerp maken
U kunt beheerbewerkingen uitvoeren voor Service Bus-onderwerpen via de `ServiceBusRestProxy`-klasse. Een `ServiceBusRestProxy`-object wordt samengesteld via de `ServicesBuilder::createServiceBusService`-factory-methode met een geschikte verbindingsreeks waarmee de tokenmachtigingen worden ingekapseld, om het te beheren.

In het volgende voorbeeld ziet u hoe u een `ServiceBusRestProxy` wordt geïnstantieerd en `ServiceBusRestProxy->createTopic` wordt aangeroepen om een onderwerp met de naam `mytopic` in een `MySBNamespace`-naamruimte te maken:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
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
> U kunt de `listTopics`-methode voor `ServiceBusRestProxy`-objecten gebruiken om te controleren of een onderwerp met een opgegeven naam al in een servicenaamruimte bestaat.
> 
> 

## <a name="create-a-subscription"></a>Een abonnement maken
Met de `ServiceBusRestProxy->createSubscription`-methode worden ook onderwerpabonnementen gemaakt. Abonnementen hebben een naam en kunnen een optioneel filter hebben waarmee de verzameling berichten wordt beperkt die aan de virtuele wachtrij van het abonnement wordt doorgegeven.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Een abonnement maken met het standaardfilter (MatchAll)
Het **MatchAll**-filter (standaard) wordt gebruikt als er bij het maken van een nieuw abonnement geen filter is opgegeven. Bij gebruik van het **MatchAll**-filter worden alle berichten die naar het onderwerp worden gepubliceerd, in de virtuele wachtrij van het abonnement geplaatst. Met het volgende voorbeeld maakt u een abonnement met de naam `mysubscription` en wordt het standaardfilter **MatchAll** gebruikt.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
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

### <a name="create-subscriptions-with-filters"></a>Abonnementen met filters maken
U kunt ook filters instellen waarmee u kunt opgeven welke berichten die naar een onderwerp worden verzonden, moeten worden weergegeven in een bepaald onderwerpabonnement. Het meest flexibele type filter dat wordt ondersteund door abonnementen is [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), waarmee een subset van SQL92 wordt geïmplementeerd. SQL-filters worden uitgevoerd voor de eigenschappen van de berichten die worden gepubliceerd naar het onderwerp. Zie [Eigenschap SqlFilter.SqlExpression][sqlfilter] voor meer informatie over SqlFilters.

> [!NOTE]
> Met elke regel voor een abonnement worden inkomende berichten onafhankelijk verwerkt en worden de bijbehorende resultaatberichten toegevoegd aan het abonnement. Daarnaast heeft elk nieuw abonnement een standaardobject **Regel** met een filter waarmee alle berichten van het onderwerp aan het abonnement worden toegevoegd. Als u alleen berichten wilt ontvangen die overeenkomen met uw filter, moet u de standaardregel verwijderen. U kunt de standaardregel verwijderen met de `ServiceBusRestProxy->deleteRule`-methode.
> 
> 

In het volgende voorbeeld wordt een abonnement met de naam `HighMessages` gemaakt met een **SqlFilter** waarmee alleen berichten worden geselecteerd die een aangepaste `MessageNumber`-eigenschap groter dan 3 hebben. Zie [Berichten verzenden naar een onderwerp](#send-messages-to-a-topic) voor informatie over het toevoegen van aangepaste eigenschappen aan berichten.

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Voor deze code is het gebruik van een extra naamruimte vereist: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Op dezelfde manier wordt in het volgende voorbeeld een abonnement met de naam `LowMessages` gemaakt met een `SqlFilter` waarmee alleen berichten worden geselecteerd die een `MessageNumber`-eigenschap kleiner dan of gelijk aan 3 hebben.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Wanneer er nu een bericht naar het `mytopic`-onderwerp wordt verzonden, wordt het altijd bezorgd bij ontvangers die zijn geabonneerd op het `mysubscription`-abonnement, en selectief bezorgd bij ontvangers die zijn geabonneerd op de abonnementen `HighMessages` en `LowMessages` (afhankelijk van de inhoud van het bericht).

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp
Uw toepassing roept de `ServiceBusRestProxy->sendTopicMessage`-methode aan om een bericht naar een Service Bus-onderwerp te verzenden. De volgende code laat zien hoe u een bericht verzendt naar het `mytopic`-onderwerp dat eerder in de naamruimte van de `MySBNamespace`-service is gemaakt.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
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

De berichten die worden verzonden naar Service Bus-onderwerpen, zijn exemplaren van de klasse [BrokeredMessage][BrokeredMessage]. [BrokeredMessage][BrokeredMessage]-objecten hebben een aantal standaardeigenschappen en -methoden, evenals eigenschappen die kunnen worden gebruikt om aangepaste toepassingsspecifieke eigenschappen te bewaren. In het volgende voorbeeld ziet u hoe u vijf testberichten verzendt naar het `mytopic`-onderwerp dat u eerder hebt gemaakt. De `setProperty`-methode wordt gebruikt om een aangepaste eigenschap (`MessageNumber`) aan elk bericht toe te voegen. De waarde van de `MessageNumber`-eigenschap varieert per bericht (u kunt deze waarde gebruiken om te bepalen welke abonnementen het bericht moeten ontvangen, zoals wordt weergegeven in de sectie [Een abonnement maken](#create-a-subscription)):

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Service Bus-onderwerpen ondersteunen een maximale grootte van 256 kB in de [Standard-laag](service-bus-premium-messaging.md) en 1 MB in de [Premium-laag](service-bus-premium-messaging.md). De koptekst, die de standaard- en aangepaste toepassingseigenschappen bevat, kan maximaal 64 kB groot zijn. Er is geen limiet voor het aantal berichten in een onderwerp, maar er is een limiet voor de totale grootte van de berichten in een onderwerp. Deze bovengrens voor de grootte van het onderwerp is 5 GB. Zie [Service Bus-quota][Service Bus quotas] voor meer informatie over quota.

## <a name="receive-messages-from-a-subscription"></a>Berichten van een abonnement ontvangen
De beste manier om berichten van een abonnement te ontvangen is met behulp van een `ServiceBusRestProxy->receiveSubscriptionMessage`-methode. Berichten kunnen in twee verschillende modi worden ontvangen: [*ReceiveAndDelete* en *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** is de standaardmodus.

Wanneer u de [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode)-modus gebruikt, wordt het ontvangen in één bewerking uitgevoerd; dat wil zeggen als Service Bus een leesaanvraag voor een bericht in een abonnement ontvangt, wordt voor het bericht aangegeven dat het is verbruikt en wordt het bericht naar de toepassing geretourneerd. De [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode)-modus* is het eenvoudigste model en werkt het beste voor scenario's waarin een toepassing het niet verwerken van een bericht bij een fout kan tolereren. Neem bijvoorbeeld een scenario waarin de consument de ontvangstaanvraag uitgeeft en het systeem vervolgens vastloopt voordat de aanvraag wordt verwerkt. Omdat Service Bus het bericht als verbruikt heeft gemarkeerd, ontbreekt het bericht dat voor het vastlopen is verbruikt wanneer de toepassing opnieuw wordt gestart en het verbruik van berichten opnieuw begint.

In de standaardmodus [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) wordt de ontvangst van een bericht een bewerking met twee fasen, waardoor er toepassingen kunnen worden ondersteund die geen ontbrekende berichten kunnen tolereren. Als Service Bus een aanvraag ontvangt, wordt het volgende te verbruiken bericht gevonden, wordt het bericht vergrendeld om te voorkomen dat andere consumenten het ontvangen en wordt het bericht vervolgens naar de toepassing geretourneerd. Nadat de toepassing klaar is met de verwerking van het bericht (of het bericht veilig heeft opgeslagen voor toekomstige verwerking), wordt de tweede fase van het ontvangstproces voltooid door het ontvangen bericht door te geven aan `ServiceBusRestProxy->deleteMessage`. Wanneer Service Bus de aanroep `deleteMessage` waarneemt, wordt het bericht gemarkeerd als verbruikt en wordt het uit de wachtrij verwijderd.

In het volgende voorbeeld ziet u hoe u een bericht ontvangt en verwerkt met de [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode)-modus (de standaardmodus). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Procedure: het vastlopen van de toepassing en onleesbare berichten afhandelen
Service Bus biedt functionaliteit om netjes te herstellen bij fouten in uw toepassing of problemen bij het verwerken van een bericht. Als een ontvangende toepassing het bericht om de een of andere reden niet kan verwerken, kan de methode `unlockMessage` voor het ontvangen bericht worden aangeroepen (in plaats van de methode `deleteMessage`). Dit zorgt ervoor dat Service Bus het bericht in de wachtrij ontgrendelt en het beschikbaar maakt om opnieuw te worden ontvangen, ofwel door dezelfde consumerende toepassing of door een andere consumerende toepassing.

Daarnaast is er een time-out gekoppeld aan een bericht dat in de wachtrij is vergrendeld. Als de toepassing het bericht niet kan verwerken voordat de time-out van de vergrendeling verloopt (bijvoorbeeld als de toepassing vastloopt), ontgrendelt Service Bus het bericht automatisch en wordt het beschikbaar gemaakt om opnieuw te worden ontvangen.

In het geval dat de toepassing is vastgelopen na het verwerken van het bericht, maar voordat de aanvraag `deleteMessage` is uitgegeven, wordt het bericht opnieuw bij de toepassing bezorgd wanneer de toepassing opnieuw wordt gestart. Dit type verwerking wordt vaak *Ten minste eenmaal verwerken* genoemd; dat wil zeggen dat elk bericht ten minste één keer wordt verwerkt maar dat hetzelfde bericht in sommige situaties opnieuw kan worden bezorgd. Als in het scenario dubbele verwerking niet wordt getolereerd, moeten toepassingsontwikkelaars extra logica toevoegen aan de toepassingen om dubbele berichtbezorging af te handelen. Dit wordt vaak bereikt met de methode `getMessageId` van het bericht dat gelijk blijft bij meerdere bezorgingspogingen.

## <a name="delete-topics-and-subscriptions"></a>Onderwerpen en abonnementen verwijderen
Als u een onderwerp of een abonnement wilt verwijderen, gebruikt u respectievelijk de methode `ServiceBusRestProxy->deleteTopic` of `ServiceBusRestProxy->deleteSubscripton`. Als een onderwerp wordt verwijderd, worden ook alle abonnementen verwijderd die zijn geregistreerd bij het onderwerp.

In het volgende voorbeeld ziet u hoe u een onderwerp met de naam `mytopic` en de bijbehorende geregistreerde abonnementen verwijdert.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
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

Met de `deleteSubscription`-methode kunt u een abonnement onafhankelijk verwijderen:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> U kunt resources van Service Bus beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus-naamruimte en berichtenentiteiten op een eenvoudige manier beheren. Het hulpprogramma biedt geavanceerde functies zoals functionaliteit voor importeren/exporteren of de mogelijkheid van het testen van onderwerpen, wachtrijen, abonnementen, relay-services, Notification Hubs en Event Hubs. 

## <a name="next-steps"></a>Volgende stappen
Zie [Wachtrijen, onderwerpen en abonnementen][Queues, topics, and subscriptions] voor meer informatie.

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md