---
title: Uitvoer van tabel opslag van Azure Stream Analytics
description: In dit artikel wordt Azure Table Storage als uitvoer voor Azure Stream Analytics beschreven.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 558ddf065d33a552034c5b129ea70bc144c494ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98013886"
---
# <a name="table-storage-output-from-azure-stream-analytics"></a>Uitvoer van tabel opslag van Azure Stream Analytics

[Azure Table Storage](../storage/common/storage-introduction.md) biedt een Maxi maal beschik bare, zeer schaal bare opslag, zodat een toepassing automatisch kan worden geschaald om te voldoen aan de vraag van de gebruiker. Table Storage is de NoSQL sleutel/kenmerk opslag van micro soft, die u kunt gebruiken voor gestructureerde gegevens met minder beperkingen voor het schema. Azure-tabel opslag kan worden gebruikt om gegevens op te slaan voor persistentie en efficiënt ophalen.

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van een tabel uitvoer.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias |Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze tabel opslag te sturen. |
| Storage-account |De naam van het opslag account waarin u uw uitvoer wilt verzenden. |
| Sleutel van het opslag account |De toegangs sleutel die aan het opslag account is gekoppeld. |
| Tabelnaam |De naam van de tabel. De tabel wordt gemaakt als deze nog niet bestaat. |
| Partitiesleutel |De naam van de uitvoer kolom die de partitie sleutel bevat. De partitie sleutel is een unieke id voor de partitie in een tabel die het eerste deel vormt van de primaire sleutel van een entiteit. Dit is een teken reeks waarde die Maxi maal 1 KB groot kan zijn. |
| Rij-sleutel |De naam van de uitvoer kolom die de rij sleutel bevat. De rij is een unieke id voor een entiteit binnen een partitie. Het vormt het tweede deel van de primaire sleutel van een entiteit. De rij is een teken reeks waarde die Maxi maal 1 KB groot kan zijn. |
| Batchgrootte |Het aantal records voor een batch bewerking. De standaard waarde (100) is voldoende voor de meeste taken. Zie de [tabel batch bewerking spec](/java/api/com.microsoft.azure.storage.table.tablebatchoperation) voor meer informatie over het wijzigen van deze instelling. |

## <a name="partitioning"></a>Partitionering

De partitie sleutel is een uitvoer kolom. Het aantal schrijvers van de uitvoer volgt de invoer partities voor [volledig parallelle query's](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Grootte van uitvoer batch

Zie [Azure Storage limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)voor de maximale bericht grootte. De standaard instelling is 100 entiteiten per trans actie, maar u kunt deze zo instellen op een kleinere waarde als nodig is.

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)
* [Quickstart: een Azure Stream Analytics-taak maken via de Azure CLI](quick-create-azure-cli.md)
* [Quickstart: Een Azure Stream Analytics-taak maken via een ARM-sjabloon](quick-create-azure-resource-manager.md)
* [Quickstart: Een Stream Analytics-taak maken met behulp van Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Een Azure Stream Analytics-taak maken met behulp van Visual Studio](stream-analytics-quick-create-vs.md)
* [Snelstartgids: een Azure Stream Analytics-taak maken in Visual Studio code](quick-create-visual-studio-code.md)