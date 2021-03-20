---
title: Rendering-toepassingen gebruiken
description: Rendering van toepassingen gebruiken met Azure Batch. In dit artikel vindt u een korte beschrijving van het uitvoeren van elke rendering-toepassing.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: ace3fe7aee6b9ffc7226448b455bcfea1f931458
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85964869"
---
# <a name="rendering-applications"></a>Rendering van toepassingen

Rendering van toepassingen wordt gebruikt door batch taken en taken te maken. Met de opdracht regel eigenschap taak geeft u de juiste opdracht regel en para meters op.  De eenvoudigste manier om de taak taken te maken, is door de Batch Explorer-sjablonen te gebruiken zoals opgegeven in [dit artikel](./batch-rendering-using.md#using-batch-explorer).  De sjablonen kunnen worden weer gegeven en gewijzigde versies die worden gemaakt, indien nodig.

In dit artikel vindt u een korte beschrijving van het uitvoeren van elke rendering-toepassing.

## <a name="rendering-with-autodesk-3ds-max"></a>Rendering met auto Desk 3ds Max

### <a name="renderer-support"></a>Ondersteuning voor renderer

Naast de renderers die zijn ingebouwd in 3ds Max, zijn de volgende renderers beschikbaar op de VM-installatie kopieën voor rendering en kan ernaar worden verwezen door het bestand 3ds Max scène:

* Autodesk Arnold
* Chaos-groep V-Ray

### <a name="task-command-line"></a>Opdracht regel van taak

Roep de `3dsmaxcmdio.exe` toepassing aan om de opdracht regel weergave op een groeps knooppunt uit te voeren.  Deze toepassing bevindt zich op het pad wanneer de taak wordt uitgevoerd. De `3dsmaxcmdio.exe` toepassing heeft dezelfde beschik bare para meters als de `3dsmaxcmd.exe` toepassing, die wordt beschreven in de sectie [Help-documentatie van 3ds Max](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (rendering | Command-Line rendering).

Bijvoorbeeld:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Opmerkingen:

* Zorg ervoor dat u zeker weet dat de Asset-bestanden worden gevonden.  Zorg ervoor dat de paden juist en relatief zijn met behulp van het venster voor het bijhouden van de **activa** , of gebruik de `-bitmapPath` para meter op de opdracht regel.
* Kijk of er problemen zijn met de weer gave, zoals het onvermogen om assets te vinden, door het bestand te controleren dat `stdout.txt` is geschreven door 3ds Max wanneer de taak wordt uitgevoerd.

### <a name="batch-explorer-templates"></a>Batch Explorer sjablonen

Groeps-en taak sjablonen kunnen worden geopend vanuit de **Galerie** in batch Explorer.  De bron bestanden van de sjabloon zijn beschikbaar in de [batch Explorer gegevens opslagplaats op github](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax).

## <a name="rendering-with-autodesk-maya"></a>Rendering met Autodesk Maya

### <a name="renderer-support"></a>Ondersteuning voor renderer

Naast de renderers die zijn ingebouwd in Maya, zijn de volgende renderers beschikbaar op de rendering-VM-installatie kopieën en kan ernaar worden verwezen door het bestand 3ds Max scène:

* Autodesk Arnold
* Chaos-groep V-Ray

### <a name="task-command-line"></a>Opdracht regel van taak

De `renderer.exe` opdracht regel weergave wordt gebruikt op de opdracht regel van de taak. De opdracht regel weergave wordt beschreven in [Maya Help](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

In het volgende voor beeld wordt de taak voor het voorbereiden van een taak gebruikt om de scène bestanden en-assets te kopiëren naar de werkmap van de taak voorbereiding, een uitvoermap wordt gebruikt voor het opslaan van de afbeelding voor rendering en frame 10 wordt weer gegeven.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Voor een V-Ray-rendering geeft het Maya-scène bestand doorgaans V-Ray op als de renderer.  Het kan ook worden opgegeven op de opdracht regel:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Voor Arnold-rendering geeft het Maya-scène bestand doorgaans Arnold als de renderer op.  Het kan ook worden opgegeven op de opdracht regel:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Batch Explorer sjablonen

Groeps-en taak sjablonen kunnen worden geopend vanuit de **Galerie** in batch Explorer.  De bron bestanden van de sjabloon zijn beschikbaar in de [batch Explorer gegevens opslagplaats op github](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya).

## <a name="next-steps"></a>Volgende stappen

Gebruik de groeps-en taak sjablonen van de [Gegevens opslagplaats in github](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) met behulp van batch Explorer.  Indien nodig kunt u nieuwe sjablonen maken of een van de opgegeven sjablonen wijzigen.
