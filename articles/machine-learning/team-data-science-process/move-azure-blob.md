---
title: Gegevens verplaatsen van en naar Azure Blob-opslag-team data Science process
description: Gegevens verplaatsen van en naar Azure Blob-opslag met behulp van Azure Storage Explorer, AzCopy, python en SSIS.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 5148084fa22266b1352046c7d8737b9804c5f4d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93311848"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Gegevens verplaatsen van en naar Azure Blob-opslag

Voor het proces van de team data Science moeten gegevens worden opgenomen of geladen in verschillende opslag omgevingen, zodat ze op de meest geschikte manier in elke fase van het proces kunnen worden verwerkt of geanalyseerd.

## <a name="different-technologies-for-moving-data"></a>Verschillende technologieën voor het verplaatsen van gegevens

In de volgende artikelen wordt beschreven hoe u gegevens kunt verplaatsen van en naar Azure Blob-opslag met behulp van verschillende technologieën.

* [Azure Storage-Verkenner](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](../../storage/common/storage-use-azcopy-v10.md)
* [Python](../../storage/blobs/storage-quickstart-blobs-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Welke methode het beste voor u is, is afhankelijk van uw scenario. De [scenario's voor geavanceerde analyses in azure machine learning](plan-sample-scenarios.md) -artikel helpen u bij het bepalen van de bronnen die u nodig hebt voor diverse data Science-werk stromen die worden gebruikt in het geavanceerde analyse proces.

> [!NOTE]
> Raadpleeg Azure Blob- [basis beginselen](../../storage/blobs/storage-quickstart-blobs-dotnet.md) en [Azure Blob service](/rest/api/storageservices/Blob-Service-Concepts)voor een volledige inleiding tot Azure Blob-opslag.
> 
> 

## <a name="using-azure-data-factory"></a>Azure Data Factory gebruiken

Als alternatief kunt u [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) gebruiken voor het volgende: 

* een pijp lijn maken en plannen die gegevens uit Azure Blob-opslag downloadt, 
* door sturen naar een gepubliceerde Azure Machine Learning-webservice, 
* Ontvang de predictive analytics resultaten en 
* Upload de resultaten naar de opslag. 

Zie [voorspellende pijp lijnen maken met Azure Data Factory en Azure machine learning](../../data-factory/transform-data-using-machine-learning.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u een Azure-abonnement, een opslag account en de bijbehorende opslag sleutel voor dat account hebt. Voordat u gegevens kunt uploaden/downloaden, moet u de account naam en de account sleutel van Azure Storage kennen.

* Zie [een gratis proef versie van één maand](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie over het instellen van een Azure-abonnement.
* Zie [over Azure Storage-accounts](../../storage/common/storage-account-create.md)voor instructies over het maken van een opslag account en voor het ophalen van account-en sleutel gegevens.