---
title: Waar opslaan & write experimenten-bestanden
titleSuffix: Azure Machine Learning
description: Meer informatie over waar u uw invoer-en uitvoer bestanden opslaat om te voor komen dat er problemen met de opslag beperking en de latentie van experimenten.
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
ms.openlocfilehash: ad641c2270f94b9d902a25e8d061fb1137a0cdb7
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518599"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Locatie voor het opslaan en schrijven van bestanden voor Azure Machine Learning experimenten


In dit artikel wordt beschreven waar u invoer bestanden opslaat, en waar u uitvoer bestanden van uw experimenten schrijft om opslag limiet fouten en experiment latentie te voor komen.

Wanneer u training uitvoert op een [Compute-doel](concept-compute-target.md), zijn ze geïsoleerd van buiten omgevingen. Het doel van dit ontwerp is om te zorgen voor reproduceer baarheid en draag baarheid van het experiment. Als u hetzelfde script twee keer uitvoert op hetzelfde of een ander Compute-doel, worden dezelfde resultaten weer gegeven. Met dit ontwerp kunt u Compute-doelen behandelen als resources zonder stateless reken kracht, die elk geen affiniteit hebben met de taken die worden uitgevoerd nadat ze zijn voltooid.

## <a name="where-to-save-input-files"></a>Waar de invoer bestanden worden opgeslagen

Voordat u een experiment kunt initiëren op een reken doel of op uw lokale computer, moet u ervoor zorgen dat de benodigde bestanden beschikbaar zijn voor het reken doel, zoals afhankelijkheids bestanden en gegevens bestanden die de code moet uitvoeren.

Azure Machine Learning trainings scripts worden uitgevoerd door de hele bronmap te kopiëren. Als u gevoelige gegevens hebt die u niet wilt uploaden, gebruikt u een [. ignore-bestand](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) of neemt u het niet op in de bron directory. In plaats daarvan opent u uw gegevens met behulp van een gegevens [opslag](/python/api/azureml-core/azureml.data).

De opslaglimiet voor momentopnamen van experimenten is 300 MB en/of 2000 bestanden.

Daarom kunt u het beste het volgende doen:

* **Uw bestanden worden opgeslagen in een Azure Machine Learning [gegevens opslag](/python/api/azureml-core/azureml.data).** Dit voor komt problemen met de latentie van experimenten en heeft de voor delen van het openen van gegevens van een extern Compute-doel, wat betekent dat verificatie en bevestiging wordt beheerd door Azure Machine Learning. Meer informatie over het opgeven van een gegevens opslag als bron directory en het uploaden van bestanden naar uw gegevens opslag in het artikel [toegangs gegevens van uw gegevens opslag](how-to-access-data.md) .

* **Als u alleen een aantal gegevens bestanden en afhankelijkheids scripts nodig hebt en u geen gegevens opslag kunt gebruiken, plaatst u** de bestanden in dezelfde map als uw trainings script. Geef deze map op als uw eigen `source_directory` trainings script of in de code die uw trainings script aanroept.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Opslag limieten van experiment-moment opnamen

Voor experimenten maakt Azure Machine Learning automatisch een Experimenteer moment opname van uw code op basis van de directory die u suggereert bij het configureren van de uitvoering. Dit heeft een totale limiet van 300 MB en/of 2000 bestanden. Als u deze limiet overschrijdt, ziet u de volgende fout:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

U kunt deze fout oplossen door uw experiment-bestanden op te slaan in een gegevens opslag. Als u geen gegevens opslag kunt gebruiken, biedt de onderstaande tabel mogelijke alternatieve oplossingen.

Beschrijving van experiment &nbsp;|Oplossing voor opslag limiet
---|---
Minder dan 2000 bestanden & kan geen gegevens opslag gebruiken| Maximale grootte van moment opname overschrijven met <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Dit kan enkele minuten duren, afhankelijk van het aantal en de grootte van de bestanden.
U moet een specifieke script Directory gebruiken| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
Pijplijn|Voor elke stap een andere submap gebruiken
Jupyter Notebooks| Maak een `.amlignore` bestand of verplaats uw notitie blok naar een nieuwe, lege, submap en voer de code opnieuw uit.

## <a name="where-to-write-files"></a>Locatie voor het schrijven van bestanden

Vanwege de isolatie van trainings experimenten, zijn de wijzigingen aan bestanden die tijdens de uitvoering plaatsvinden, niet noodzakelijkerwijs buiten uw omgeving bewaard. Als uw script de bestanden Local op Compute wijzigt, worden de wijzigingen niet bewaard voor uw volgende experiment en worden ze niet automatisch door gegeven aan de client computer. De wijzigingen die zijn aangebracht tijdens het eerste experiment, worden dus niet in de tweede keer uitgevoerd.

Wanneer u wijzigingen schrijft, raden we u aan om bestanden naar een Azure Machine Learning gegevens opslag te schrijven. Bekijk [de toegang tot gegevens uit uw gegevens opslag](how-to-access-data.md).

Als u geen gegevens opslag nodig hebt, schrijft u de bestanden naar de `./outputs` map en/of `./logs` .

>[!Important]
> Twee mappen, *uitvoer* en *Logboeken*, een speciale behandeling ontvangen door Azure machine learning. Tijdens de training, wanneer u bestanden naar `./outputs` en `./logs` mappen schrijft, worden de bestanden automatisch naar de uitvoerings geschiedenis geüpload, zodat u er toegang tot hebt wanneer de uitvoering is voltooid.

* Schrijf **voor uitvoer, zoals status berichten of Score resultaten,** bestanden naar de `./outputs` map, zodat deze persistent zijn als artefacten in de uitvoerings geschiedenis. Mindful zijn van het aantal en de grootte van bestanden die naar deze map worden geschreven, omdat er een latentie kan optreden wanneer de inhoud wordt geüpload om de geschiedenis uit te voeren. Als latentie een probleem is, wordt het schrijven van bestanden naar een gegevens opslag aanbevolen.

* **Als u geschreven bestanden wilt opslaan als Logboeken in uitvoerings geschiedenis,** moet u bestanden naar de `./logs` map schrijven. De logboeken worden in realtime geüpload, dus deze methode is geschikt voor het streamen van live-updates vanaf een externe uitvoering.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het verkrijgen van toegang tot gegevens uit uw gegevens opslag](how-to-access-data.md).

* Meer informatie over het [maken van Compute-doelen voor model training en-implementatie](how-to-create-attach-compute-studio.md)