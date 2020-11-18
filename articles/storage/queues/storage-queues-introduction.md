---
title: Inleiding tot Azure Queues - Azure Storage
description: Bekijk een inleiding tot Azure Queues, een service om grote aantallen berichten op te slaan. Een wachtrijservice bevat een URL-indeling, opslagaccount, wachtrij en bericht.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: dineshm
ms.openlocfilehash: f618b7203cdbf99d79079cf81de38c6d15c91981
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345564"
---
# <a name="what-are-azure-queues"></a>Wat zijn Azure-wachtrijen?

Azure Queue Storage is een service om grote aantallen berichten op te slaan. U hebt overal ter wereld toegang tot berichten via geverifieerde oproepen met HTTP of HTTPS. Een wachtrijbericht kan maximaal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot aan de totale capaciteitslimiet van een opslagaccount. Wachtrijen worden vaak gebruikt om een voorraad werk te maken dat asynchroon moet worden verwerkt.

## <a name="queue-service-concepts"></a>Concepten van Queue-service

De Queue-service bevat de volgende onderdelen:

![Een diagram dat de relatie toont tussen een opslagaccount, wachtrijen en berichten.](./media/storage-queues-introduction/queue1.png)

- **URL-indeling:** Wachtrijen kunnen worden opgevraagd met de volgende URL-indeling:

  `https://<storage account>.queue.core.windows.net/<queue>`

  Met de volgende URL wordt een wachtrij in het diagram opgevraagd:

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Opslagaccount:** Alle toegang tot Azure Storage vindt plaats via een opslagaccount. Zie [Schaalbaarheids- en prestatiedoelen voor standaard opslagaccounts](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) voor meer informatie over opslagaccountcapaciteit.

- **Wachtrij:** Een wachtrij bevat een set berichten. De naam van een wachtrij **mag alleen** kleine letters bevatten. Zie [Naming Queues and Metadata](/rest/api/storageservices/Naming-Queues-and-Metadata) (Wachtrijen en metagegevens een naam geven) voor informatie over de naamgeving van wachtrijen.

- **Bericht:** Een bericht in een willekeurige indeling, van maximaal 64 KB. Vóór versie 29-07-2017 is de maximale toegestane time-to-live zeven dagen. Voor versie 29-07-2017 of hoger mag de maximale time-to-live elk positief getal zijn. Of -1 om aan te geven dat het bericht niet verloopt. Als deze parameter wordt weggelaten, is de standaard time-to-live zeven dagen.

## <a name="next-steps"></a>Volgende stappen

- [Een opslagaccount maken](../common/storage-account-create.md?toc=%252fazure%252fstorage%252fqueues%252ftoc.json)
- [Aan de slag met Queues met behulp van .NET](storage-dotnet-how-to-use-queues.md)
