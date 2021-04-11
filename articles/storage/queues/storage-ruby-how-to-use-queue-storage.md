---
title: Queue Storage van Ruby gebruiken-Azure Storage
description: Meer informatie over het gebruik van Azure Queue Storage voor het maken en verwijderen van wacht rijen en het invoegen, ophalen en verwijderen van berichten. Voor beelden geschreven in Ruby.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 12/08/2016
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 257b435f0136884e8568f4201794a7ce5cf0c209
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275852"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Queue Storage van Ruby gebruiken

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Overzicht

In deze hand leiding wordt beschreven hoe u algemene scenario's uitvoert met behulp van de Microsoft Azure Queue Storage-service. De voor beelden zijn geschreven met behulp van de ruby Azure API. De gedekte scenario's zijn het **Invoegen**, **inspecteren**, **ophalen** en **verwijderen** van wachtrij berichten, en het **maken en verwijderen van wacht rijen**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Een Ruby-toepassing maken

Maak een ruby-toepassing. Zie [een ruby-toepassing maken in app service in Linux](../../app-service/quickstart-ruby.md)voor instructies.

## <a name="configure-your-application-to-access-storage"></a>Uw toepassing configureren voor toegang tot opslag

Als u Azure Storage wilt gebruiken, moet u het ruby Azure-pakket downloaden en gebruiken, dat een aantal gebruiks vriendelijke bibliotheken bevat die communiceren met de opslag REST-services.

<!-- docutune:ignore Terminal -->

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems gebruiken om het pakket te verkrijgen

1. Gebruik een opdrachtregelinterface zoals PowerShell (Windows), Terminal (Mac) of Bash (Unix).
2. Typ `gem install Azure` in het opdracht venster om de Gem en afhankelijkheden te installeren.

### <a name="import-the-package"></a>Het pakket importeren

Gebruik uw favoriete tekst editor en voeg het volgende toe aan de bovenkant van het ruby-bestand waar u opslag wilt gebruiken:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Een Azure Storage verbinding instellen

In de Azure-module worden de omgevings variabelen `AZURE_STORAGE_ACCOUNT` en de `AZURE_STORAGE_ACCESS_KEY` vereiste informatie voor het maken van verbinding met uw Azure Storage-account gelezen. Als deze omgevings variabelen niet zijn ingesteld, moet u de account gegevens opgeven voordat u `Azure::QueueService` met de volgende code gebruikt:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

U verkrijgt deze waarden als volgt van een klassiek of Resource Manager-opslagaccount op Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Navigeer naar het opslag account dat u wilt gebruiken.
3. Klik op de Blade **instellingen** aan de rechter kant op **toegangs toetsen**.
4. Op de Blade **toegangs sleutels** die wordt weer gegeven, ziet u de toegangs sleutel 1 en de toegangs sleutel 2. U kunt een van beide gebruiken.
5. Klik op het kopieerpictogram om de sleutel naar het klembord kopiëren.

## <a name="how-to-create-a-queue"></a>Procedure: een wachtrij maken

Met de volgende code wordt een `Azure::QueueService` -object gemaakt, waarmee u met wacht rijen kunt werken.

```ruby
azure_queue_service = Azure::QueueService.new
```

Gebruik de `create_queue()` methode voor het maken van een wachtrij met de opgegeven naam.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Procedure: een bericht in een wachtrij invoegen

Als u een bericht in een wachtrij wilt invoegen, gebruikt u de `create_message()` methode om een nieuw bericht te maken en toe te voegen aan de wachtrij.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Procedure: het volgende bericht bekijken

U kunt het bericht aan de voor kant van een wachtrij bekijken zonder het uit de wachtrij te verwijderen door de methode aan te roepen `peek_messages()` . In wordt standaard weer `peek_messages()` gegeven als één bericht. U kunt ook opgeven hoeveel berichten u wilt bekijken.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Procedure: het volgende bericht uit de wachtrij verwijderen

U kunt in twee stappen een bericht verwijderen uit een wachtrij.

1. Wanneer u belt `list_messages()` , wordt standaard het volgende bericht in een wachtrij weer gegeven. U kunt ook opgeven hoeveel berichten u wilt ophalen. De berichten die worden geretourneerd door, `list_messages()` worden onzichtbaar voor andere code die berichten uit deze wachtrij leest. U geeft de time-out voor de zicht baarheid in seconden op als para meter.
2. Als u het verwijderen van het bericht uit de wachtrij wilt volt ooien, moet u ook bellen `delete_message()` .

Dit proces met twee stappen voor het verwijderen van een bericht zorgt ervoor dat wanneer uw code een bericht niet kan verwerken als gevolg van een hardware-of software fout, een ander exemplaar van uw code hetzelfde bericht kan ophalen en het opnieuw proberen. Uw code aanroepen `delete_message()` direct nadat het bericht is verwerkt.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procedure: de inhoud van een bericht in de wachtrij wijzigen

U kunt de inhoud van een bericht in de wachtrij wijzigen. De volgende code gebruikt de `update_message()` methode om een bericht bij te werken. De methode retourneert een tuple die de pop-ontvangst van het wachtrij bericht bevat en een UTC- `DateTime` waarde die aangeeft wanneer het bericht in de wachtrij wordt weer gegeven.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message",
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Procedure: aanvullende opties voor het dequeuing van berichten

Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen.

1. U kunt een batch bericht ontvangen.
2. U kunt een langere of korte time-out voor onzichtbaarheid instellen, waardoor uw code meer of minder tijd nodig is om elk bericht volledig te verwerken.

In het volgende code voorbeeld wordt de `list_messages()` methode gebruikt om 15 berichten in één aanroep op te halen. Vervolgens wordt elk bericht afgedrukt en verwijderd. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Procedure: de lengte van de wachtrij ophalen

U kunt een schatting krijgen van het aantal berichten in de wachtrij. De `get_queue_metadata()` methode retourneert het aantal geschatte berichten en andere meta gegevens van de wachtrij.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Procedure: een wachtrij verwijderen

Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de `delete_queue()` methode aan in het wachtrij object.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van Queue Storage hebt geleerd, volgt u deze koppelingen voor meer informatie over complexere opslag taken.

- Ga naar de blog van het [Azure Storage-team](/archive/blogs/windowsazurestorage/)
- Ga naar de [Azure SDK voor ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) -opslag plaats op github

Voor een vergelijking tussen Azure Queue Storage die in dit artikel worden besproken en Azure Service Bus wacht rijen [die worden beschreven in het gebruik van service bus wacht](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/)rijen, raadpleegt u [Azure Queue Storage en service bus wacht rijen-vergeleken en daarentegen](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
