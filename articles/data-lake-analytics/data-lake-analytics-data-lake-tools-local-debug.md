---
title: Fout opsporing Azure Data Lake Analytics code lokaal
description: Meer informatie over het gebruik van Azure Data Lake-Hulpprogram Ma's voor Visual Studio voor het opsporen van fouten in U-SQL-taken op uw lokale werk station.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 1c7218deac9efba6df6c1284f2578a744e768284
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221023"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Fout opsporing Azure Data Lake Analytics code lokaal

U kunt Azure Data Lake-Hulpprogram Ma's voor Visual Studio gebruiken om Azure Data Lake Analytics code op uw lokale werk station uit te voeren en fouten op te lossen, net zoals u dat in de Azure Data Lake Analytics-service kunt doen.

Meer informatie over het [uitvoeren van u-SQL-script op uw lokale computer](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Lokaal fouten opsporen in scripts en C#-assembly's

U kunt fouten opsporen in C#-assembly's zonder ze te verzenden en te registreren bij de Azure Data Lake Analytics-service. U kunt onderbrekings punten instellen in zowel het code-behind-bestand als in een C#-project waarnaar wordt verwezen.

### <a name="debug-local-code-in-a-code-behind-file"></a>Fouten opsporen in lokale code in een code-behind-bestand

1. Stel onderbrekings punten in het code-behind-bestand in.
2. Selecteer **F5** om lokaal fouten op te sporen in het script.

> [!NOTE]
   > De volgende procedure werkt alleen in Visual Studio 2015. In oudere Visual Studio-versies moet u mogelijk de **PDB** -bestanden hand matig toevoegen.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Fouten opsporen in lokale code in een C#-project waarnaar wordt verwezen

1. Maak een C#-assembly-project en bouw het voor het genereren van het uitvoer- **DLL** -bestand.
2. Registreer het **DLL** -bestand met behulp van een U-SQL-instructie:

   ```sql
   CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
   ```
   
3. Stel onderbrekingspunten in in de C#-code.
4. Selecteer **F5** om fouten op te sporen in het script door lokaal naar het **DLL** -bestand van C# te verwijzen.


## <a name="next-steps"></a>Volgende stappen

- Zie [website logboeken analyseren met Azure data Lake Analytics](data-lake-analytics-analyze-weblogs.md)voor een voor beeld van een complexere query.
- Zie [taak browser en taak weergave gebruiken voor Azure data Lake Analytics taken voor](data-lake-analytics-data-lake-tools-view-jobs.md)het weer geven van taak Details.
- Zie [de weer gave vertex Execution gebruiken in data Lake-Hulpprogram ma's voor Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)voor meer informatie over het gebruik van de vertex Execution-weer gave.
