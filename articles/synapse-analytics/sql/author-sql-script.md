---
title: SQL-scripts in Synapse Studio
description: Inleiding tot Synapse Studio SQL-scripts in azure Synapse Analytics.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 4ed02901aa0d6948e9c6443e5bbcf4ebfbc872f7
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118690"
---
# <a name="synapse-studio-sql-scripts-in-azure-synapse-analytics"></a>Synapse Studio SQL-scripts in azure Synapse Analytics 

Synapse Studio biedt een SQL script web-interface waarmee u SQL-query's kunt schrijven. 

## <a name="begin-authoring-in-sql-script"></a>Beginnen met ontwerpen in SQL-script 

Er zijn verschillende manieren om de ontwerp ervaring in SQL-script te starten. U kunt een nieuw SQL-script maken via een van de volgende methoden.

1. Selecteer in het menu ontwikkelen het pictogram **' + '** en kies **SQL-script**.

2. Kies in het menu **acties** de optie **Nieuw SQL-script**.

3. Kies **importeren** in het menu **acties** onder SQL-scripts ontwikkelen. Selecteer een bestaand SQL-script uit uw lokale opslag.
![nieuwe SQL script 3-acties](media/author-sql-script/new-sql-script-3-actions.png)

## <a name="create-your-sql-script"></a>Een SQL-script maken

1. Kies een naam voor uw SQL-script door de knop **Eigenschappen** te selecteren en de standaard naam te vervangen die aan het SQL-script is toegewezen. 
![nieuwe SQL-script naam wijzigen](media/author-sql-script/new-sql-script-rename.png)

2. Kies de specifieke exclusieve SQL-groep of serverloze SQL-groep in de vervolg keuzelijst **verbinding maken met** . Of kies, indien nodig, de data base uit **Data Base gebruiken**. 
![nieuwe SQL-groep kiezen](media/author-sql-script/new-sql-choose-pool.png)

3. Begin met het ontwerpen van uw SQL-script met behulp van de IntelliSense-functie.

## <a name="run-your-sql-script"></a>Uw SQL-script uitvoeren

Selecteer de knop uitvoeren om het SQL-script **uit** te voeren. De resultaten worden standaard weer gegeven in een tabel.

![tabel met nieuwe SQL-script resultaten](media/author-sql-script/new-sql-script-results-table.png)

## <a name="export-your-results"></a>Uw resultaten exporteren

U kunt de resultaten exporteren naar uw lokale opslag in verschillende indelingen (waaronder CSV, Excel, JSON en XML) door ' resultaten exporteren ' te selecteren en de uitbrei ding te kiezen.

U kunt de resultaten van het SQL-script ook visualiseren in een grafiek door de knop **grafiek** te selecteren. Selecteer de kolom grafiek type en **categorie**. U kunt de grafiek exporteren naar een afbeelding door **Opslaan als afbeelding** te selecteren. 

![grafiek met nieuwe SQL-script resultaten](media/author-sql-script/new-sql-script-results-chart.png)

## <a name="explore-data-from-a-parquet-file"></a>Gegevens uit een Parquet-bestand verkennen

U kunt Parquet-bestanden in een opslag account verkennen met behulp van SQL script om de inhoud van het bestand te bekijken.

![nieuw script sqlod Parquet](media/author-sql-script/new-script-sqlod-parquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL-tabellen, externe tabellen, weer gaven

Als u het menu **acties** onder gegevens selecteert, kunt u verschillende acties selecteren, zoals:

- Nieuw SQL-script
- BOVENSTE 1000 rijen selecteren
- CREATE
- NEERZETten en maken 
 
Bekijk de beschik bare penbeweging door met de rechter muisknop te klikken op de knoop punten van SQL-data bases.
 
![nieuwe script database](media/author-sql-script/new-script-database.png)

## <a name="create-folders-and-move-sql-scripts-into-a-folder"></a>Mappen maken en SQL-scripts naar een map verplaatsen

Kies in het menu acties onder SQL-scripts ontwikkelen de optie nieuwe map in het menu acties onder SQL-scripts ontwikkelen. En typ de naam van de nieuwe map in het pop-upvenster. 

> [!div class="mx-imgBorder"] 
> ![Scherm afbeelding met een voor beeld van een SQL-script waarbij ' nieuwe map ' is geselecteerd.](./media/author-sql-script/new-sql-script-create-folder.png)

Als u een SQL-script wilt verplaatsen naar een map, kunt u het SQL-script selecteren en ' verplaatsen naar ' kiezen in het menu acties. Zoek vervolgens de doelmap in het nieuwe venster en verplaats het SQL-script naar de geselecteerde map. U kunt het SQL-script ook snel slepen en neerzetten in een map.  

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-move-folder.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Synapse Analytics](../index.yml)voor meer informatie over het ontwerpen van een SQL-script.