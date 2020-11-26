---
title: Metrische gegevens over doorvoer en latentie controleren in Azure Portal | Microsoft Docs
description: Informatie over het controleren van metrische gegevens over doorvoer en latentie voor een opslagaccount in de portal.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 708a2369e5ba21f47f3d3fea509654b0385363f7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912311"
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>Metrische gegevens over doorvoer en latentie voor een opslagaccount controleren

Deze zelfstudie is deel vier en het laatste deel van een serie. In de vorige zelfstudies hebt u geleerd hoe u grote hoeveelheden willekeurige gegevens naar een Azure Storage-account kunt uploaden en downloaden. In deze zelfstudie leert u hoe u metrische gegevens kunt gebruiken om doorvoer en latentie te bekijken in Azure Portal.

In deel vier van de serie leert u het volgende:

> [!div class="checklist"]
> * Grafieken configureren in Azure Portal
> * Metrische gegevens over doorvoer en latentie controleren

[Metrische gegevens van Azure Storage](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) gebruikt Azure Monitor om een uniform overzicht weer te geven van de prestaties en beschikbaarheid van uw opslagaccount.

## <a name="configure-metrics"></a>Metrische gegevens configureren

Navigeer naar **Metrische gegevens (preview-versie)** bij **INSTELLINGEN** in uw opslagaccount.

Kies Blob uit de vervolgkeuzelijst **SUBSERVICE**.

Onder **Metrische gegevens** selecteert u een van de metrische gegevens die in de volgende tabel zijn gevonden:

De volgende metrische gegevens geven u een idee van de latentie en de doorvoer van de toepassing. De metrische gegevens die u in de portal configureert, duren gemiddeld één minuut. Als een transactie is voltooid halverwege een minuut, wordt die minuut in tweeën gesplitst om het gemiddelde te bereiken. In de toepassing is de tijd voor de upload- en downloadbewerkingen vastgelegd, en u kon zien hoeveel tijd er werkelijk was om de bestanden te uploaden en downloaden. Deze informatie kan worden gebruikt in combinatie met de metrische gegevens in de portal voor een volledig begrip van de doorvoer.

|Gegevens|Definitie|
|---|---|
|**Success E2E Latency**|De gemiddelde end-to-end-latentie van geslaagde aanvragen aan een opslagservice of de opgegeven API-bewerking. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|
|**Geslaagde serverlatentie**|De gemiddelde tijd die nodig is om een aanvraag door Azure Storage te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency. |
|**Transacties**|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. In het voorbeeld was de blokgrootte ingesteld op 100 MB. In dit geval wordt elk blok van 100 MB gezien als een transactie.|
|**Inkomend verkeer**|De hoeveelheid inkomende gegevens. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure. |
|**Uitgaand verkeer**|De hoeveelheid uitgaande gegevens. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer. |

Selecteer **Afgelopen 24 uur (automatisch)** naast **Tijd**. Kies **Afgelopen uur** en **Minuut** voor **Tijdgranulatie** en klik vervolgens op **Toepassen**.

![Metrische gegevens voor opslagaccount](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

Aan grafieken kunnen meerdere metrische gegevens worden toegewezen, maar als er meerdere metrische gegevens worden toegewezen, kan de functie voor groeperen op dimensies niet worden gebruikt.

## <a name="dimensions"></a>Dimensies

[Dimensies](./monitor-blob-storage-reference.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions) worden gebruikt om grafieken diepgaander te bekijken en meer gedetailleerde informatie te krijgen. Verschillende metrische gegevens hebben verschillende dimensies. Een dimensie die beschikbaar is, is de dimensie **API-naam**. Deze dimensie deelt de grafiek op op basis van elke afzonderlijke API-aanroep. De eerste afbeelding hieronder toont een voorbeeld van een grafiek met het totaalaantal transacties voor een opslagaccount. De tweede afbeelding toont dezelfde grafiek maar met de dimensie API-naam geselecteerd. Zoals u ziet, wordt elke transactie vermeld en krijgt u meer details te zien over het aantal aanvragen dat is gemaakt door API-naam.

![Metrische gegevens over opslagaccount - transacties zonder een dimensie](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![Metrische gegevens voor opslagaccount - transacties](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de virtuele machine niet meer nodig hebt, verwijdert u de resourcegroep, de machine zelf én alle gerelateerde resources. Om dit te doen, selecteert u de resourcegroep voor de VM en klikt u op Verwijderen.

## <a name="next-steps"></a>Volgende stappen

In het vierde deel van de serie, hebt u geleerd metrische gegevens voor de voorbeeldoplossing te bekijken, zodat u het volgende kunt doen:

> [!div class="checklist"]
> * Grafieken configureren in Azure Portal
> * Metrische gegevens over doorvoer en latentie controleren

Volg deze link om vooraf samengestelde opslagvoorbeelden te bekijken.

> [!div class="nextstepaction"]
> [Voorbeelden van Azure Storage-scripts](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md