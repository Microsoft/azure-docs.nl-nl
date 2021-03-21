---
title: Gegevensverwerking optimaliseren
titleSuffix: Azure Machine Learning
description: Leer de aanbevolen procedures voor het optimaliseren van gegevens verwerkings snelheden en de integratie Azure Machine Learning ondersteunt voor gegevens verwerking op schaal.
services: machine-learning
ms.service: machine-learning
ms.author: sgilley
author: sdgilley
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/26/2020
ms.custom: data4ml
ms.openlocfilehash: 5ab7bac635a0b670087800212727b0d2e2b96934
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103472207"
---
# <a name="optimize-data-processing-with-azure-machine-learning"></a>Gegevens verwerking optimaliseren met Azure Machine Learning

In dit artikel vindt u informatie over de aanbevolen procedures om de snelheid van gegevens verwerking lokaal en op schaal te optimaliseren.

Azure Machine Learning is geïntegreerd met open source-pakketten en frameworks voor gegevens verwerking. Door deze integraties te gebruiken en de best practice aanbevelingen in dit artikel toe te passen, kunt u de snelheid van uw gegevens verwerking zowel lokaal als op schaal verbeteren.

## <a name="parquet-and-csv-file-formats"></a>Bestands indelingen voor Parquet en CSV

Bestanden met door komma's gescheiden waarden (CSV) zijn algemene bestands indelingen voor gegevens verwerking. Parquet-bestands indelingen worden echter aanbevolen voor machine learning taken.

[Parquet-bestanden](https://parquet.apache.org/) slaan gegevens op in een binaire kolom indeling. Deze indeling is handig als u de gegevens wilt opsplitsen in meerdere bestanden. Met deze indeling kunt u ook de relevante velden voor uw machine learning experimenten richten. In plaats van een gegevens bestand van 20 GB te hoeven lezen, kunt u de belasting van die gegevens verminderen door de benodigde kolommen te selecteren om uw ML-model te trainen. Parquet-bestanden kunnen ook worden gecomprimeerd om de verwerkings kracht te minimaliseren en minder ruimte in beslag nemen.

CSV-bestanden worden meestal gebruikt voor het importeren en exporteren van gegevens, omdat ze gemakkelijk kunnen worden bewerkt en gelezen in Excel. De gegevens in Csv's worden opgeslagen als teken reeksen in een indeling op basis van een rij en de bestanden kunnen worden gecomprimeerd om de belasting van de gegevens overdracht te verlagen. Ongecomprimeerde Csv's kunnen worden uitgebreid met een factor van ongeveer 2-10 en gecomprimeerde Csv's kan nog verder toenemen. Zodat 5-GB CSV in het geheugen groter wordt dan de 8 GB RAM-geheugen op de computer. Dit compressie gedrag kan de latentie van gegevens overdracht verhogen. Dit is niet ideaal als u grote hoeveel heden gegevens wilt verwerken. 

## <a name="pandas-dataframe"></a>Panda data frame

[Panda dataframes](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) worden vaak gebruikt voor het bewerken en analyseren van gegevens. `Pandas` geschikt voor gegevens groottes die kleiner zijn dan 1 GB, maar verwerkings tijden voor `pandas` dataframes vertragen wanneer de bestands grootte ongeveer 1 GB bereikt. Deze vertraging is omdat de grootte van uw gegevens in de opslag niet gelijk is aan de grootte van de gegevens in een data frame. Zo kunnen gegevens in CSV-bestanden Maxi maal 10 keer worden uitgebreid in een data frame, zodat een CSV-bestand van 1 GB 10 GB kan worden in een data frame.

`Pandas` is één thread, wat betekent dat bewerkingen op één CPU tegelijk worden uitgevoerd. U kunt werk belastingen eenvoudig parallelliserenen op meerdere virtuele Cpu's op een enkele Azure Machine Learning Reken instantie met pakketten zoals [modi](https://modin.readthedocs.io/en/latest/) die teruglopen `Pandas` met behulp van een gedistribueerde back-end.

Als u uw taken wilt parallelliseren `Modin` en [Dask](https://dask.org), wijzigt u deze regel met code `import pandas as pd` in `import modin.pandas as pd` .

## <a name="dataframe-out-of-memory-error"></a>Data frame: fout met onvoldoende geheugen 

Normaal gesp roken treedt er een *geheugen* fout op wanneer uw data frame boven het beschik bare RAM-geheugen op uw computer wordt uitgebreid. Dit concept is ook van toepassing op een gedistribueerd Framework zoals `Modin` of `Dask` .  Dat wil zeggen dat uw bewerking probeert de data frame in het geheugen te laden op elk knoop punt in uw cluster, maar er is onvoldoende RAM-geheugen beschikbaar.

Eén oplossing is het verg Roten van het RAM-geheugen om de data frame in het geheugen aan te passen. Het is raadzaam om de reken grootte en de verwerkings capaciteit twee keer zoveel RAM-geheugen te hebben. Dus als uw data frame 10 GB is, gebruikt u een reken doel met ten minste 20 GB RAM om ervoor te zorgen dat de data frame kan worden aangepast aan het geheugen en worden verwerkt. 

Voor meerdere virtuele Cpu's, vCPU moet u er voor zorgen dat de ene partitie in het RAM-geheugen past dat elke vCPU op de computer kan hebben. Dat wil zeggen, als u 16 GB RAM 4 Vcpu's hebt, wilt u per vCPU ongeveer 2 GB dataframes.

### <a name="local-vs-remote"></a>Lokaal versus extern

U kunt bepaalde Panda-data frame-opdrachten sneller uitvoeren wanneer u werkt op uw lokale PC en een externe virtuele machine die u hebt ingericht met Azure Machine Learning. Op de lokale computer is doorgaans een pagina bestand ingeschakeld, waarmee u meer kunt laden dan in het fysieke geheugen past, dat wil zeggen dat uw harde schijf wordt gebruikt als een uitbrei ding van uw RAM. Op dit moment kunnen Azure Machine Learning Vm's zonder wissel bestand worden uitgevoerd, kan daarom zoveel gegevens worden geladen als beschikbaar fysiek RAM-geheugen. 

Voor Compute-zware taken kunt u het beste een grotere VM kiezen om de verwerkings snelheden te verbeteren.

Meer informatie over de [beschik bare VM-reeksen en-groottes](concept-compute-target.md#supported-vm-series-and-sizes) voor Azure machine learning. 

Zie de bijbehorende pagina's van de VM-reeks, zoals, [dv2-Dsv2 Series](../virtual-machines/dv2-dsv2-series-memory.md) of [NC Series](../virtual-machines/nc-series.md), voor het RAM-geheugen.

### <a name="minimize-cpu-workloads"></a>CPU-workloads minimaliseren

Als u geen RAM-geheugen kunt toevoegen aan uw computer, kunt u de volgende technieken Toep assen om de CPU-workloads te minimaliseren en verwerkings tijden te optimaliseren. Deze aanbevelingen zijn van toepassing op zowel één als gedistribueerde systemen.

Techniek | Beschrijving
----|----
Compressie | Gebruik een andere weer gave voor uw gegevens, op een manier die minder geheugen gebruikt en de resultaten van de berekening niet aanzienlijk beïnvloeden.<br><br>*Voor beeld:* In plaats van vermeldingen op te slaan als een teken reeks met ongeveer 10 bytes of meer per vermelding, slaat u ze op als Boolean, True of False, die u in één byte kunt opslaan.
Segmenterings | Laad gegevens in subsets (segmenten) in het geheugen, waarbij de gegevens één subset op tijdstip worden verwerkt of meerdere subsets parallel zijn. Deze methode werkt het beste als u alle gegevens wilt verwerken, maar niet alle gegevens in een keer in het geheugen hoeft te laden. <br><br>*Voor beeld:* In plaats van de gegevens van een volledig jaar tegelijk te verwerken, moet u de gegevens één maand per keer laden en verwerken.
Indexeren | Een index Toep assen en gebruiken, een samen vatting waarin wordt uitgelegd waar u de gewenste gegevens kunt vinden. Indexeren is handig als u alleen een subset van de gegevens wilt gebruiken, in plaats van de volledige set<br><br>*Voor beeld:* Als u de verkoop gegevens van een volledig jaar per maand hebt gesorteerd, kunt u met een index snel zoeken naar de gewenste maand die u wilt verwerken.

## <a name="scale-data-processing"></a>Schaal van gegevens verwerking

Als de vorige aanbevelingen niet genoeg zijn en u geen virtuele machine kunt ophalen die aan uw gegevens voldoet, 

* Gebruik een framework zoals `Spark` of `Dask` om de gegevens uit het geheugen te verwerken. Bij deze optie wordt de data frame geladen in de RAM-partitie per partitie en verwerkt, waarbij het uiteindelijke resultaat aan het eind wordt verzameld.  

* Uitschalen naar een cluster met behulp van een gedistribueerd Framework. Bij deze optie worden de belasting van gegevens verwerking opgesplitst en verwerkt op meerdere Cpu's die parallel werken, met het uiteindelijke resultaat dat aan het einde is verzameld.

### <a name="recommended-distributed-frameworks"></a>Aanbevolen gedistribueerde Frameworks

In de volgende tabel wordt gedistribueerde Frameworks aanbevolen die zijn geïntegreerd met Azure Machine Learning op basis van uw code voorkeur of gegevens grootte.

Ervaring of gegevens grootte | Aanbeveling
------|------
Als u bekend bent met `Pandas`| `Modin` of `Dask` Data frame
Als u wilt `Spark` | `PySpark`
Voor gegevens die kleiner zijn dan 1 GB | `Pandas` lokaal **of** een externe Azure machine learning reken instantie
Voor gegevens die groter zijn dan 10 GB| Verplaatsen naar een cluster met `Ray` , `Dask` of `Spark`

## <a name="next-steps"></a>Volgende stappen

* [Opties voor gegevens opname met Azure machine learning](concept-data-ingestion.md).
* [Gegevens sets maken en registreren](how-to-create-register-datasets.md).
