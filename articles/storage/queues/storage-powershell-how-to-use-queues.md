---
title: Azure Queue Storage gebruiken vanuit Power shell-Azure Storage
description: Voer bewerkingen uit op Azure Queue Storage via Power shell. Met Azure Queue Storage kunt u grote aantallen berichten opslaan die toegankelijk zijn via HTTP/HTTPS.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 05/15/2019
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fba288f76377e744b1fe21a52e03a43409c505bf
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585612"
---
# <a name="how-to-use-azure-queue-storage-from-powershell"></a>Azure Queue Storage gebruiken vanuit Power shell

Azure Queue Storage is een service voor het opslaan van grote aantallen berichten die overal ter wereld toegankelijk zijn via HTTP of HTTPS. Zie [Inleiding tot Azure Queue Storage](storage-queues-introduction.md)voor meer informatie. In dit artikel wordt beschreven hoe algemene Queue Storage bewerkingen worden uitgevoerd. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
>
> - Een wachtrij maken
> - Een wachtrij ophalen
> - Een bericht toevoegen
> - Een bericht lezen
> - Een bericht verwijderen
> - Een wachtrij verwijderen

Voor deze hand leiding is de Azure PowerShell ( `Az` )-module v 0,7 of later vereist. Voer uit `Get-Module -ListAvailable Az` om de momenteel geïnstalleerde versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps).

Er zijn geen Power shell-cmdlets voor het gegevens vlak voor wacht rijen. Als u gegevenslaag bewerkingen wilt uitvoeren, zoals het toevoegen van een bericht, het lezen van een bericht en het verwijderen van een bericht, moet u de .NET Storage-client bibliotheek gebruiken wanneer deze wordt weer gegeven in Power shell. U maakt een bericht object en vervolgens kunt u opdrachten gebruiken `AddMessage` om bewerkingen op dat bericht uit te voeren. In dit artikel wordt beschreven hoe u dit doet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Lijst met locaties ophalen

Als u niet weet welke locatie u kunt gebruiken, kunt u een lijst met de beschikbare locaties weergeven. Selecteer de gewenste locatie in de lijst. Deze oefening zal gebruiken `eastus` . Sla dit op in de variabele `location` voor toekomstig gebruik.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Sla de naam van de resource groep op in een variabele voor toekomstig gebruik. In dit voorbeeld wordt er een resourcegroep met de naam `howtoqueuesrg` gemaakt in de regio `eastus`.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Een opslagaccount maken

Maak een standaard opslag account voor algemene doel einden met lokaal redundante opslag (LRS) met behulp van [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Haal de context van het opslag account op waarmee het opslag account wordt gedefinieerd dat moet worden gebruikt. Als u werkt met een opslagaccount, verwijst u naar de context in plaats van herhaaldelijk de referenties op te geven.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Een wachtrij maken

In het volgende voor beeld wordt eerst een verbinding tot stand gebracht met Azure Storage met behulp van de context van het opslag account, die de naam van het opslag account en de bijbehorende toegangs sleutel bevat. Vervolgens wordt de cmdlet [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue) aangeroepen om een wachtrij met de naam te maken `howtoqueue` .

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Zie de [naamgeving van wacht rijen en meta gegevens](/rest/api/storageservices/naming-queues-and-metadata)voor meer informatie over naam conventies voor Azure Queue Storage.

## <a name="retrieve-a-queue"></a>Een wachtrij ophalen

U kunt een specifieke wachtrij of een lijst van alle wacht rijen in een opslag account opvragen en ophalen. De volgende voor beelden laten zien hoe u alle wacht rijen in het opslag account en een specifieke wachtrij kunt ophalen. beide opdrachten gebruiken de cmdlet [Get-AzStorageQueue](/powershell/module/az.storage/get-azstoragequeue) .

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Een bericht aan een wachtrij toevoegen

Bewerkingen die van invloed zijn op de daad werkelijke berichten in de wachtrij, gebruiken de client bibliotheek voor .NET-opslag als beschikbaar in Power shell. Als u een bericht aan een wachtrij wilt toevoegen, maakt u een nieuw exemplaar van het bericht object, [`Microsoft.Azure.Storage.Queue.CloudQueueMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage) klasse. Vervolgens roept u de- [`AddMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage) methode aan. A `CloudQueueMessage` kan worden gemaakt op basis van een teken reeks (in UTF-8-indeling) of een byte matrix.

In het volgende voor beeld ziet u hoe u een bericht aan uw wachtrij kunt toevoegen.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Als u de [Azure Storage Explorer](https://storageexplorer.com)gebruikt, kunt u verbinding maken met uw Azure-account en de wacht rijen in het opslag account weer geven en inzoomen op een wachtrij om de berichten in de wachtrij weer te geven.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Een bericht uit de wachtrij lezen en vervolgens verwijderen

Berichten worden in de best mogelijke eerst-try-volg orde gelezen. Dit is niet gegarandeerd. Wanneer u het bericht uit de wachtrij leest, wordt het onzichtbaar voor alle andere processen die in de wachtrij kijken. Dit zorgt ervoor dat als uw code het bericht niet kan verwerken vanwege een hardware-of software fout, een ander exemplaar van uw code hetzelfde bericht kan ophalen en probeer het opnieuw.

Deze **time-out voor onzichtbaarheid** bepaalt hoe lang het bericht onzichtbaar blijft voordat het weer beschikbaar is voor verwerking. De standaardwaarde is 30 seconden.

Uw code leest een bericht uit de wachtrij in twee stappen. Wanneer u de [`Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) -methode aanroept, wordt het volgende bericht in de wachtrij weer gegeven. Een bericht dat wordt geretourneerd van is niet `GetMessage` zichtbaar voor andere code die berichten uit deze wachtrij leest. U kunt de methode aanroepen om het verwijderen van het bericht uit de wachtrij te volt ooien [`Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage) .

In het volgende voor beeld leest u de drie wachtrij berichten en wacht u 10 seconden (de time-out voor onzichtbaarheid). Vervolgens leest u de drie berichten opnieuw, verwijdert u de berichten nadat u ze hebt gelezen door te bellen `DeleteMessage` . Als u de wachtrij probeert te lezen nadat de berichten zijn verwijderd, `$queueMessage` wordt geretourneerd als `$null` .

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Een wachtrij verwijderen

Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de `Remove-AzStorageQueue` cmdlet aan. In het volgende voor beeld ziet u hoe u de specifieke wachtrij verwijdert die in deze oefening wordt gebruikt met behulp van de- `Remove-AzStorageQueue` cmdlet.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle activa die u hebt gemaakt in deze oefening wilt verwijderen, verwijdert u de resource groep. Hiermee verwijdert u ook alle resources binnen de groep. In dit geval worden het opslag account dat is gemaakt en de resource groep zelf verwijderd.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In dit procedure-artikel hebt u geleerd over het basis Queue Storage beheer met Power shell, met inbegrip van het volgende:

> [!div class="checklist"]
>
> - Een wachtrij maken
> - Een wachtrij ophalen
> - Een bericht toevoegen
> - Het volgende bericht lezen
> - Een bericht verwijderen
> - Een wachtrij verwijderen

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Opslag-cmdlets Microsoft Azure PowerShell

- [PowerShell Storage-cmdlets](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

- [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.
