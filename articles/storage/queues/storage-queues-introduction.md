---
title: Inleiding tot Azure Queue Storage - Azure Storage
description: Bekijk een inleiding tot Azure Queue Storage, een service om grote aantallen berichten op te slaan. Een Queue Storage-service bevat een URL-indeling, opslagaccount, wachtrij en bericht.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 03/18/2020
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 32ce5af5371047ae814602e9118f622ee036bd9c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276209"
---
# <a name="what-is-azure-queue-storage"></a>Wat is Azure Queue Storage?

Azure Queue Storage is een service om grote aantallen berichten op te slaan. U hebt overal ter wereld toegang tot berichten via geverifieerde oproepen met HTTP of HTTPS. Een wachtrijbericht kan maximaal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot aan de totale capaciteitslimiet van een opslagaccount. Wachtrijen worden vaak gebruikt om een voorraad werk te maken dat asynchroon moet worden verwerkt.

## <a name="queue-storage-concepts"></a>Queue Storage-concepten

Queue Storage omvat de volgende onderdelen:

![Een diagram dat de relatie toont tussen een opslagaccount, wachtrijen en berichten.](./media/storage-queues-introduction/queue1.png)

- **URL-indeling:** Wachtrijen kunnen worden opgevraagd met de volgende URL-indeling:

  `https://<storage account>.queue.core.windows.net/<queue>`

  Met de volgende URL wordt een wachtrij in het diagram opgevraagd:

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Opslagaccount:** Alle toegang tot Azure Storage vindt plaats via een opslagaccount. Zie [Schaalbaarheids- en prestatiedoelen voor standaard opslagaccounts](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) voor meer informatie over opslagaccountcapaciteit.

- **Wachtrij:** Een wachtrij bevat een set berichten. De naam van een wachtrij **mag alleen** kleine letters bevatten. Raadpleeg [Wachtrijen en metagegevens een naam geven](/rest/api/storageservices/naming-queues-and-metadata) voor informatie over de naamgeving van wachtrijen.

- **Bericht:** Een bericht in een willekeurige indeling, van maximaal 64 KB. Vóór versie 29-07-2017 is de maximale toegestane time-to-live zeven dagen. Voor versie 29-07-2017 of hoger mag de maximale time-to-live elk positief getal zijn. Of -1 om aan te geven dat het bericht niet verloopt. Als deze parameter wordt weggelaten, is de standaard time-to-live zeven dagen.

## <a name="next-steps"></a>Volgende stappen

- [Een opslagaccount maken](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Aan de slag met Queue Storage met behulp van .NET](storage-dotnet-how-to-use-queues.md)
- [Aan de slag met Queue Storage met behulp van Java](storage-java-how-to-use-queue-storage.md)
- [Aan de slag met Queue Storage met behulp van Python](storage-python-how-to-use-queue-storage.md)
- [Aan de slag met Queue Storage met behulp van Node.js](storage-nodejs-how-to-use-queues.md)
