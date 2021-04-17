---
title: Waar u experimentbestanden & schrijven
titleSuffix: Azure Machine Learning
description: Ontdek waar u uw invoer- en uitvoerbestanden kunt opslaan om fouten met opslaglimieten en experimentlatentie te voorkomen.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 59955b291ce706a77d0dd5ab052809fe725166d9
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387884"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Waar kunt u bestanden opslaan en schrijven voor Azure Machine Learning experimenten


In dit artikel leert u waar u invoerbestanden kunt opslaan en waar u uitvoerbestanden van uw experimenten kunt schrijven om opslaglimietfouten en experimentlatentie te voorkomen.

Bij het starten van training wordt uitgevoerd op [een rekendoel](concept-compute-target.md), worden ze geïsoleerd van externe omgevingen. Het doel van dit ontwerp is om de reproduceerbaarheid en draagbaarheid van het experiment te garanderen. Als u hetzelfde script twee keer op hetzelfde of een ander rekendoel hebt uitgevoerd, ontvangt u dezelfde resultaten. Met dit ontwerp kunt u rekendoelen behandelen als staatloze rekenbronnen, die elk geen affiniteit hebben met de taken die worden uitgevoerd nadat ze zijn voltooid.

## <a name="where-to-save-input-files"></a>Waar kunt u invoerbestanden opslaan?

Voordat u een experiment op een rekendoel of uw lokale computer kunt starten, moet u ervoor zorgen dat de benodigde bestanden beschikbaar zijn voor dat rekendoel, zoals afhankelijkheidsbestanden en gegevensbestanden die uw code moet uitvoeren.

Azure Machine Learning voert trainingsscripts uit door de volledige bronmap te kopiëren. Als u gevoelige gegevens hebt die u niet wilt uploaden, gebruikt u een [.ignore-bestand](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) of neem u het niet op in de bronmap. In plaats daarvan kunt u toegang krijgen tot uw gegevens met [behulp van een gegevensstore](/python/api/azureml-core/azureml.data).

De opslaglimiet voor momentopnamen van experimenten is 300 MB en/of 2000 bestanden.

Daarom raden we het volgende aan:

* **Het opslaan van uw bestanden in Azure Machine Learning [gegevensset](/python/api/azureml-core/azureml.data).** Dit voorkomt problemen met de latentie van experimenten en biedt de voordelen van toegang tot gegevens vanaf een extern rekendoel, wat betekent dat verificatie en het maken van een verbinding worden beheerd door Azure Machine Learning. Meer informatie over het opgeven van een gegevensset als uw invoergegevensbron in uw trainingsscript met [Trainen met gegevenssets](how-to-train-with-datasets.md).

* **Als u slechts een paar gegevensbestanden** en afhankelijkheidsscripts nodig hebt en geen gegevensstore kunt gebruiken, moet u de bestanden in dezelfde map plaatsen als uw trainingsscript. Geef deze map op als `source_directory` uw rechtstreeks in uw trainingsscript of in de code die uw trainingsscript aanroept.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Opslaglimieten van momentopnamen van experimenten

Voor experimenten maakt Azure Machine Learning automatisch een experimentmomentopname van uw code op basis van de map die u aankondigt wanneer u de run configureert. Dit heeft een totale limiet van 300 MB en/of 2000 bestanden. Als u deze limiet overschrijdt, ziet u de volgende fout:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Sla uw experimentbestanden op in een gegevensopslag om deze fout op te lossen. Als u geen gegevensstore kunt gebruiken, biedt de onderstaande tabel mogelijke alternatieve oplossingen.

Beschrijving &nbsp; van experiment|Oplossing voor opslaglimiet
---|---
Minder dan 2000 bestanden & kunnen geen gegevensstore gebruiken| Groottelimiet voor momentopnamen overschrijven met <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Dit kan enkele minuten duren, afhankelijk van het aantal en de grootte van de bestanden.
Moet een specifieke scriptmap gebruiken| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
Pijplijn|Een andere subdirectory gebruiken voor elke stap
Jupyter Notebooks| Maak een `.amlignore` bestand of verplaats uw notebook naar een nieuwe, lege subdirectory en voer uw code opnieuw uit.

## <a name="where-to-write-files"></a>Waar kunt u bestanden schrijven?

Vanwege de isolatie van trainingsexperimenten worden de wijzigingen in bestanden die plaatsvinden tijdens het uitvoeren niet noodzakelijkerwijs buiten uw omgeving doorgevoerd. Als uw script de lokale bestanden wijzigt om te berekenen, blijven de wijzigingen niet persistent voor de volgende experiment-run en worden ze niet automatisch doorgegeven aan de clientmachine. Daarom hebben de wijzigingen die zijn aangebracht tijdens de eerste experiment-run geen invloed op de wijzigingen in de tweede.

Bij het schrijven van wijzigingen wordt u aangeraden bestanden naar de opslag te schrijven via een Azure Machine Learning gegevensset met een [OutputFileDatasetConfig-object](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig). Bekijk [hoe u een OutputFileDatasetConfig maakt.](how-to-train-with-datasets.md#where-to-write-training-output)

Anders schrijft u bestanden naar de `./outputs` map `./logs` en/of .

>[!Important]
> Twee mappen, uitvoer *en* *logboeken,* krijgen een speciale behandeling door Azure Machine Learning. Tijdens de training, wanneer u bestanden naar mappen en schrijft, worden de bestanden automatisch geüpload naar uw rungeschiedenis, zodat u er toegang toe hebt zodra de `./outputs` `./logs` run is voltooid.

* **Voor uitvoer zoals statusberichten** of scoreresultaten schrijft u bestanden naar de map, zodat deze als artefacten `./outputs` in de uitvoergeschiedenis worden opgeslagen. Let op het aantal en de grootte van bestanden die naar deze map worden geschreven, omdat er latentie kan optreden wanneer de inhoud wordt geüpload naar de geschiedenis van de run. Als latentie een probleem is, wordt het schrijven van bestanden naar een gegevensstore aanbevolen.

* **Als u geschreven bestanden wilt opslaan als logboeken in de run history, schrijft** u bestanden naar de `./logs` map. De logboeken worden in realtime geüpload, dus deze methode is geschikt voor het streamen van live updates vanaf een externe run.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [toegang tot gegevens vanuit opslag](how-to-access-data.md).

* Meer informatie over [Rekendoelen maken voor modeltraining en -implementatie](how-to-create-attach-compute-studio.md)