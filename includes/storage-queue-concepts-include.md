---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 08/21/2020
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 45df30f4f1444b6148af9f3c7d47b94909ccef3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027176"
---
## <a name="what-is-queue-storage"></a>Wat is Queue Storage?

Azure Queue Storage is een service voor de opslag van grote aantallen berichten die via HTTP of HTTPS overal vandaan kunnen worden opgevraagd met geverifieerde aanroepen. Een enkel wachtrijbericht mag maximaal 64 KB groot zijn en een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount. Queue Storage wordt vaak gebruikt om een achterstand te maken voor het asynchroon verwerken van werk.

## <a name="queue-service-concepts"></a>Concepten van Queue-service

De Azure Queue-service bevat de volgende onderdelen:

![Azure Queue-service-onderdelen](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Opslag account:** Alle toegang tot Azure Storage wordt uitgevoerd via een opslag account. Zie [Overzicht van opslagaccount](../articles/storage/common/storage-account-overview.md) voor meer informatie over opslagaccounts.
* **Wachtrij:** Een wachtrij bevat een set berichten. Alle berichten moeten zich in een wachtrij bevinden. De naam van een wachtrij mag alleen kleine letters bevatten. Zie [Naming Queues and Metadata](/rest/api/storageservices/Naming-Queues-and-Metadata) (Wachtrijen en metagegevens een naam geven) voor informatie over de naamgeving van wachtrijen.
* **Bericht:** Een bericht in een willekeurige indeling, van maximaal 64 KB. Een bericht kan maximaal 7 dagen in de wachtrij blijven staan. Voor versie 29-07-2017 of hoger mag de maximale time-to-live elk positief getal zijn. Of -1 om aan te geven dat het bericht niet verloopt. Als deze parameter wordt weggelaten, is de standaard time-to-live zeven dagen.
* **URL-indeling:** Wacht rijen zijn adresseerbaar met behulp van de volgende URL-indeling: http:// `<storage account>` . Queue.core.Windows.net/`<queue>`

    Met de volgende URL wordt een wachtrij in het diagram opgevraagd:

    `http://myaccount.queue.core.windows.net/incoming-orders`