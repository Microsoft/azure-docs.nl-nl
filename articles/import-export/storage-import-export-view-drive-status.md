---
title: De status van Azure import/export-taken weer geven | Microsoft Docs
description: Meer informatie over het weer geven van de status van Azure import/export-taken en de gebruikte stations. Begrijp de factoren die van invloed zijn op hoe lang het duurt om een taak te verwerken.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/14/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 8333745b802f41b5a1b3dc07663870299800e3f6
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98706297"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>De status van de Import/Export-taken in Azure bekijken

Dit artikel bevat informatie over het weer geven van de status van het station en de taak voor Azure import/export-taken. Azure import/export-service wordt gebruikt voor het veilig overdragen van grote hoeveel heden gegevens naar Azure-blobs en Azure Files. De service wordt ook gebruikt voor het exporteren van gegevens uit Azure Blob-opslag.  

## <a name="view-job-and-drive-status"></a>Taak en de status van het station weer geven
U kunt de status van uw import-of export taken volgen vanuit de Azure Portal door het tabblad **importeren/exporteren** te selecteren. Er wordt een lijst met taken op de pagina weer gegeven.

![Taak status weer geven](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Taakstatus weergeven

U ziet een van de volgende taak statussen, afhankelijk van waar uw station zich in het proces bevindt.

| Taak status | Beschrijving |
|:--- |:--- |
| Maken | Nadat een taak is gemaakt, wordt de status ingesteld op **maken**. Terwijl de taak de status **bezig met maken** heeft, neemt de import/export-service aan dat de stations niet naar het Data Center zijn verzonden. Een taak kan Maxi maal twee weken in deze status blijven, waarna deze automatisch door de service wordt verwijderd. |
| Verzending | Nadat u het pakket hebt verzonden, moet u de tracerings gegevens in het Azure Portal bijwerken.  Als u dit doet, wordt de **Verzend** status ingeschakeld. De taak blijft in de **Verzend** status voor Maxi maal twee weken. 
| Ontvangen | Nadat alle stations in het Data Center zijn ontvangen, wordt de status van de taak ingesteld op **ontvangen**. |
| Overbrengen | Als ten minste één station wordt verwerkt, wordt de taak status ingesteld op **overdragen**. Ga naar de status van het [station](#view-drive-status)voor meer informatie. |
| Verpakking | Nadat de verwerking van alle stations is voltooid, wordt de taak in de **verpakkings** status geplaatst totdat de stations naar u worden teruggestuurd. |
| Voltooid | Als de taak is voltooid zonder fouten, wordt de taak ingesteld op **voltooid** nadat alle stations naar u zijn teruggestuurd. De taak wordt automatisch verwijderd na 90 dagen met de status **voltooid** . |
| Gesloten | Nadat alle stations naar u zijn teruggestuurd, wordt de taak ingesteld op **gesloten** als er fouten zijn opgetreden tijdens de verwerking van het project. De taak wordt automatisch verwijderd na 90 dagen met de status **gesloten** . |

## <a name="view-drive-status"></a>Status van station weergeven

In de volgende tabel wordt de levens cyclus van een afzonderlijk station beschreven tijdens de overgang naar een import-of export taak. De huidige status van elke schijf in een taak wordt weer gegeven in de Azure Portal.

In de volgende tabel wordt elke status beschreven waarin elke schijf van een taak kan passeren.

| Status van station | Beschrijving |
|:--- |:--- |
| Opgegeven | Voor een import taak wordt de begin status voor een station **opgegeven** wanneer de taak wordt gemaakt op basis van de Azure Portal. Voor een export taak, omdat er geen station is opgegeven wanneer de taak wordt gemaakt, wordt de eerste status van het station **ontvangen**. |
| Ontvangen | De schijf wordt overgezet naar de status **ontvangen** wanneer de import/export-service de stations heeft verwerkt die zijn ontvangen van het verzend bedrijf voor een import taak. Voor een export taak is de eerste status van het station de status **ontvangen** . |
| NeverReceived | Het station wordt verplaatst naar de **NeverReceived** -status wanneer het pakket voor een taak arriveert, maar het-pakket het station niet bevat. Een station wordt ook verplaatst naar deze status als het pakket nog niet is ontvangen door het Data Center en de service ten minste twee weken geleden de verzend gegevens heeft ontvangen. |
| Overbrengen | Een station wordt verplaatst naar de **overdrachts** status wanneer de service begint met het overdragen van gegevens van het station naar Azure Storage. |
| Voltooid | Een station wordt verplaatst naar de status **voltooid** wanneer de service alle gegevens zonder fouten heeft overgebracht.
| CompletedMoreInfo | Een station wordt verplaatst naar de **CompletedMoreInfo** -status wanneer de service problemen heeft ondervonden bij het kopiëren van gegevens van of naar het station. De informatie kan fouten, waarschuwingen of informatieve berichten bevatten over het overschrijven van blobs.
| ShippedBack | Een station wordt verplaatst naar de status **ShippedBack** wanneer het is verzonden van het Data Center terug naar het retour adres. |

In deze installatie kopie van de Azure Portal wordt de status van het station van een voorbeeld taak weer gegeven:

![Status van station weer geven](./media/storage-import-export-service/drivestate.png)

In de volgende tabel worden de fout statussen van het station en de acties voor elke status beschreven.

| Status van station | Gebeurtenis | Oplossing/volgende stap |
|:--- |:--- |:--- |
| NeverReceived | Een station dat is gemarkeerd als **NeverReceived** (omdat het niet is ontvangen als onderdeel van de verzen ding van de taak) arriveert bij een andere verzen ding. | Het operations-team verplaatst het station naar **ontvangen**. |
| N.v.t. | Een station dat geen deel uitmaakt van een taak, arriveert bij het Data Center als onderdeel van een andere taak. | Het station is gemarkeerd als een extra station. Het wordt aan u geretourneerd wanneer de taak die is gekoppeld aan het oorspronkelijke pakket is voltooid. |

## <a name="time-to-process-job"></a>Taak voor het verwerken van tijd
De hoeveelheid tijd die nodig is voor het verwerken van een import/export-taak is afhankelijk van een aantal factoren, zoals:

-  Verzend tijd
-  Laden in het Data Center
-  Taak type en grootte van de gegevens die worden gekopieerd
-  Aantal schijven in een taak. 

De import/export-service heeft geen SLA, maar de service streeft naar het volt ooien van de kopie in 7 tot 10 dagen nadat de schijven zijn ontvangen. Naast de status die is gepost op Azure Portal, kunt u REST-Api's gebruiken om de voortgang van de taak bij te houden. Gebruik de para meter percentage voltooid in de API-aanroep van de [lijst Jobs](/previous-versions/azure/dn529083(v=azure.100)) om de voortgang van het percentage kopiëren weer te geven.


## <a name="next-steps"></a>Volgende stappen

* [Gegevens overdragen met AzCopy-opdracht regel programma](../storage/common/storage-use-azcopy-v10.md)
* [Voor beeld van Azure import-export REST API](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)