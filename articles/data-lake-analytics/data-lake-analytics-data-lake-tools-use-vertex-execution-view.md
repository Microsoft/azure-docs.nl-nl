---
title: Uitvoerings weergave vertex in Data Lake-Hulpprogram Ma's voor Visual Studio
description: In dit artikel wordt beschreven hoe u de vertex-uitvoerings weergave kunt gebruiken om Data Lake Analytics taken te examenen.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/13/2016
ms.openlocfilehash: 3fba7bdaa5db1d812fbcd479e5f1eab50c8d1032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215855"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>De vertex Execution View gebruiken in Data Lake-Hulpprogram Ma's voor Visual Studio
Meer informatie over het gebruik van de vertex-uitvoerings weergave om Data Lake Analytics taken te examen.


## <a name="open-the-vertex-execution-view"></a>De vertex Execution View openen
Open een U-SQL-taak in Data Lake-Hulpprogram Ma's voor Visual Studio. Klik in de linkerbenedenhoek op **vertex Execution View** . U wordt mogelijk gevraagd om eerst profielen te laden. Dit kan enige tijd duren, afhankelijk van de netwerk verbinding.

![Scherm opname van de weer gave Data Lake Analytics tools vertex](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Meer informatie over vertex Execution View
De uitvoerings weergave van het vertex bestaat uit drie delen:

![Scherm opname van de weer gave vertex met de deel Vensters vertex kiezer en gecentreerd bovenaan en gecentreerd onder.](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

Met de **hoek punt kiezer** links kunt u hoek punten selecteren op basis van functies (zoals de Top 10 van gegevens lezen of kiezen op werk gebied). Een van de meest gebruikte filters is het weer geven van de **hoek punten op het kritieke pad**. Het **kritieke pad** is de langste keten van hoek punten van een U-SQL-taak. Meer informatie over het kritieke pad is handig voor het optimaliseren van uw taken door te controleren welk hoek punt de langste tijd kost.
  
![Scherm afbeelding met het bovenste middelste deel venster van de vertex-uitvoerings weergave waarin de ' uitvoerings status van alle hoek punten ' wordt weer gegeven.](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

In het bovenste middelste deel venster wordt de **uitvoerings status van alle hoek punten** weer gegeven.
  
![Scherm afbeelding met het onderste middelste deel venster van de vertex-uitvoerings weergave waarin informatie over elk hoek punt wordt weer gegeven.](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Het onderste middelste deel venster toont informatie over elk hoek punt:
* Proces naam: de naam van het exemplaar van het hoek punt. Het bestaat uit verschillende onderdelen in de naam van de fase | Hoek punt | VertexRunInstance. Zo staat de SV7_Split [62]. v1 vertex voor het tweede uitgevoerde exemplaar (. v1, index beginnend bij 0) van het hoekpunt nummer 62 in het fase SV7_Split.
* Totaal aantal gelezen/geschreven gegevens: de gegevens zijn gelezen/geschreven door dit hoek punt.
* Status/verlaat: de laatste status wanneer het hoek punt is beëindigd.
* Afsluit code/fout type: de fout wanneer het hoek punt is mislukt.
* Reden voor maken: waarom het hoek punt is gemaakt.
* Vertraging van resource latentie/verwerkings latentie/PN-wachtrij: de tijd die nodig is om te wachten op resources, het verwerken van gegevens en het blijven van de wachtrij.
* Process/Creator GUID: GUID voor het huidige actieve hoek punt of de maker.
* Versie: het N-ste exemplaar van het knoop punt dat wordt uitgevoerd (het systeem kan om verschillende redenen nieuwe exemplaren van een hoek punt plannen, bijvoorbeeld failover, reken redundantie, enzovoort)
* Tijdstip waarop de versie is gemaakt.
* Begin tijd/proces van het proces voor het maken van de tijd/het proces in de wachtrij geplaatst/begin tijd/proces is voltooid: wanneer het vertex proces wordt gemaakt. Wanneer het vertex proces begint met de wachtrij; Wanneer het bepaalde vertex proces wordt gestart; Wanneer het bepaalde hoek punt is voltooid.

## <a name="next-steps"></a>Volgende stappen
* Zie [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Diagnostische logboeken openen voor Azure Data Lake Analytics) voor logboekregistratie van diagnostische informatie.
* Zie [Websitelogboeken analyseren met Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) voor een complexere query.
* Zie [taak browser en taak weergave gebruiken voor Azure data Lake Analytics-taken voor](data-lake-analytics-data-lake-tools-view-jobs.md) meer informatie over het weer geven van taken
