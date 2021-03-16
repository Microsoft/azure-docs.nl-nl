---
title: 'Zelfstudie: Met Azure Batch en Batch Explorer een Blender-scène weergeven'
description: Zelfstudie - Meerdere frames uit een Blender scène renderen met behulp van Azure Batch en de Batch Explorer-clienttoepassing
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: tutorial
ROBOTS: NOINDEX
ms.openlocfilehash: 11805d2a15e7f369ad38ab7e92409c1b357d2d8e
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490837"
---
# <a name="tutorial-render-a-blender-scene-using-batch-explorer"></a>Zelfstudie: Een Blender-scène met Batch Explorer renderen

Deze zelfstudie laat zien hoe u meerdere frames van een Blender-demoscène rendert. Blender wordt gebruikt voor de zelfstudie omdat de client en het renderen van virtuele machines gratis zijn, maar het proces is vergelijkbaar wanneer andere toepassingen, zoals Maya of 3ds Max, zouden worden gebruikt.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een scène uploaden naar Azure Storage
> * Een Batch-pool maken met meerdere knooppunten om de rendering uit te voeren
> * Meerdere frames renderen
> * De gerenderde framebestanden bekijken en installeren

## <a name="prerequisites"></a>Vereisten

U hebt een abonnement op basis van betalen-per-gebruik of een andere betaalde Azure-optie nodig om renderingtoepassingen in Batch te kunnen gebruiken op basis van betalen per gebruik. Licenties op basis van betalen per gebruik worden niet ondersteund als u gebruikmaakt van een gratis Azure-aanbieding die een financieel tegoed biedt.

U hebt een Azure Batch-account nodig met een gekoppeld opslagaccount.  Bekijk een van de Batch-snelstarts, zoals het [CLI-artikel](./quick-create-cli.md) voor het maken van een Batch-account.

Een kerngeheugenquotum met lage prioriteit van ten minste 50 kernen is vereist voor de VM-grootte en het aantal virtuele machines die zijn opgegeven in deze zelfstudie; het standaardquotum kan worden gebruikt, maar dan moet een kleinere virtuele machine worden gebruikt, waardoor het langer duurt om de installatiekopieën te renderen. Het proces voor het aanvragen van een verbeterd kerngeheugenquotum wordt beschreven in [dit artikel](./batch-quota-limit.md).

Ten slotte moet [Batch Explorer](https://azure.github.io/BatchExplorer/) zijn geïnstalleerd; deze is beschikbaar voor Windows, OSX en Linux. Dit is optioneel, maar als [Blender](https://www.blender.org/download/) is geïnstalleerd kan vervolgens het voorbeeldbestand voor het model worden weergegeven.

## <a name="download-the-demo-scene"></a>Download de demoscène

Download het ZIP-bestand 'Klaslokaal' voor Blender via de [webpagina Blender-demobestanden ](https://www.blender.org/download/demo-files/) en pak het uit naar een lokaal station.

## <a name="upload-a-scene-to-azure-storage"></a>Een scène uploaden naar Azure Storage

Maak een container van het opslagaccount voor de demoscènebestanden:

* Start Batch Explorer op
* Selecteer de menuopdracht 'Gegevens' in het hoofdmenu aan de linkerkant.
* Zorg ervoor dat 'Bestandsgroepen' is geselecteerd in de keuzelijst.
* Selecteer de knop '+' en maak een nieuwe 'Lege bestandsgroep' met de naam 'blender-klaslokaal'
  * Een groep is gewoon een blob-container van Azure Storage met een fgrp-voorvoegsel; dit is een conventie gebruikt voor het filteren van andere containers in het opslagaccount

![Bestandsgroep voor de scènebestanden](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup.png)

De scènebestanden uploaden:

* Selecteer de nieuwe container en sleep de inhoud van de map 'klaslokaal' naar de container in Batch Explorer.

![Geüploade scènebestanden](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup_uploaded.png)

## <a name="create-azure-storage-container-for-output-images"></a>Azure Storage-container voor uitvoer van beelden maken

Maak een container van het opslagaccount voor de demo-uitvoerbestanden:

* Selecteer de menuopdracht 'Gegevens' in het hoofdmenu aan de linkerkant.
* Selecteer de knop '+' en maak een nieuwe 'Lege bestandsgroep' met de naam 'render-uitvoer'

![Bestandsgroep voor uitvoerbestanden](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_filegroup.png)

## <a name="create-a-pool-of-vms-for-rendering"></a>Maak een pool van virtuele machines voor rendering

Maak een Batch-pool met rendering van de Microsoft Azure Marketplace VM-installatiekopie die de toepassing Blender bevat:

* Selecteer de menuopdracht 'Galerie' in het hoofdmenu aan de linkerkant.
* Selecteer het item 'Blender' voor een lijst van toepassingsitems.
* Selecteer de items voor het renderen van frames op Windows Server
* Het linkpictogram aan de rechterkant van het item kan eventueel worden geselecteerd om de sjabloonbestanden te zien die worden gebruikt voor het maken van een pool en een taak.

![Blender-galerie-items](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_gallery_item.png)

* Selecteer de knop 'Pool maken voor later gebruik'
  * Laat de naam van de pool staan als 'blender-windows'
  * Stel het 'Toegewezen aantal vm's' in op '0'
  * Stel het 'Aantal VM's met lage prioriteit' in op '3'
  * Stel de grootte van het knooppunt in op 'Standard_F16': een andere VM-grootte kan worden geselecteerd, maar de tijd om een frame te renderen is hoofdzakelijk afhankelijk van het aantal kernen.
* Selecteer de groene knop om de pool te maken
  * De pool zal vrijwel onmiddellijk worden gemaakt, maar het duurt een paar minuten voor de virtuele machines zijn toegewezen en gestart.

![Sjabloon voor pools voor Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_template.png)

> [!WARNING]
> Houd er rekening mee dat wanneer virtuele machines aanwezig zijn in een groep, de kosten van deze virtuele machines in rekening worden gebracht op uw Azure-abonnement. De groep of de virtuele machines moeten worden verwijderd om het in rekening brengen van de kosten te stoppen. Verwijder de groep aan het einde van deze zelfstudie om te voorkomen dat er nog kosten in rekening worden gebracht.

De status van de pool van toepassingen en virtuele machines kan worden gecontroleerd in de weergave 'Pools'. In het volgende voorbeeld ziet u dat alle drie de VM's zijn toegewezen, twee zijn gestart en niet actief, een wordt nog steeds opgestart: ![Pool-heatmap](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap.png)

## <a name="create-a-rendering-job"></a>Een renderingtaak maken

Maak een renderingtaak voor het renderen van een aantal frames met behulp van de groep die is gemaakt:

* Selecteer de menuopdracht 'Galerie' in het hoofdmenu aan de linkerkant.
* Selecteer het item 'Blender' voor een lijst van toepassingsitems.
* Selecteer de items voor het weergeven van frames op Windows Server.
* Selecteer de knop 'Taak uitvoeren met bestaande toepassingen'
* Selecteer de pool 'blender-windows'
* Stel de 'Naam van de taak' in op 'blender-render-zelfstudie1'
* Selecteer 'fgrp-blender-leslokaal' voor 'Invoer gegevens'
* Selecteer het bestandspictogram voor het 'Blend-bestand' en selecteer 'leslokaal.blend'
* Laat de 'Begin van frame' staan op '1' en stel 'Einde van frame' in op '5'
* Stel 'Uitvoer' in op 'fgrp-render-uitvoer'
* Selecteer de groene knop voor het maken van de taak; een taak wordt gemaakt met vijf opdrachten, één voor elk frame

![Sjabloon voor taken voor Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_template.png)

Zodra de job en alle taken zijn gemaakt, wordt de taak wordt weergegeven, samen met de opdrachten van de taak: ![Lijst met taken](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_task_list.png)

Wanneer een taak voor de eerste keer start met uitvoeren op een VM-pool, wordt een voorbereidingsopdracht voor de Batch-taak uitgevoerd waarvan de scènebestanden uit de bestandsopslaggroep naar de virtuele machine worden gekopieerd, zodat ze kunnen worden geopend door Blender.
De status van de weergave kan worden bepaald door het logboekbestand voor stdout.txt te bekijken dat wordt gemaakt door Blender.  Selecteer een taak. 'Taakuitvoer' wordt standaard weergegeven en het bestand 'stdout.txt' kan worden geselecteerd en bekeken.
![stdout-bestand](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_stdout.png)

Als de 'blender-windows'-pool is geselecteerd, is de groep virtuele machines zichtbaar in een uitvoerende status: ![Pool-heatmap met actieve knooppunten](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap_running.png)

Het duurt enkele minuten om de gerenderde installatiekopieën te produceren, afhankelijk van de geselecteerde VM-grootte.  Met behulp van de eerder opgegeven VM F16 duurde het ongeveer 16 minuten om frames te renderen.

## <a name="view-the-rendering-output"></a>De gerenderde uitvoer bekijken

Wanneer frames klaar zijn met renderen, worden deze taken weergegeven als voltooid: ![Taken aan het voltooien](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_tasks_complete.png)

De gerenderde installatiekopie wordt eerst naar de virtuele machine geschreven en kan worden weergegeven door het selecteren van de map 'wd': ![Gerenderde installatiekopie het poolknooppunt](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image.png)

Het sjabloon van de taak geeft ook aan dat de uitvoerbestanden en logboekbestanden worden teruggeschreven naar de opgegeven Microsoft Azure Storage-accountgroep wanneer de taak is gemaakt.  De 'Gegevens'-gebruikersinterface kan worden gebruikt om de logboeken en de uitvoerbestanden weer te geven. Deze kan ook worden gebruikt om de bestanden te downloaden: ![Gerenderde afbeelding in bestandsgroep voor opslag](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image_storage.png)

Wanneer alle taken zijn voltooid, wordt de taak gemarkeerd als voltooid: ![Elk taak is voltooid](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_alltasks_complete.png)

## <a name="clean-up-resources"></a>Resources opschonen

> [!WARNING]
> De pool moet worden verwijderd (deze kan ook worden verkleind naar nul knooppunten) om te voorkomen dat kosten in rekening worden gebracht voor de virtuele machines in het Azure-abonnement.

* Selecteer 'Pools'
* Selecteer de pool 'blender-windows'
* Klik met de rechtermuisknop en selecteer 'Verwijderen' of selecteer het prullenbakpictogram boven de groep

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een scène uploaden naar Azure Storage
> * Een Batch-pool maken met meerdere knooppunten om de rendering uit te voeren
> * Meerdere frames renderen
> * De gerenderde framebestanden bekijken en installeren

Ga door met verkennen van de rendering-toepassingen die beschikbaar zijn via Batch Explorer in de sectie **Galerie**. Er zijn voor elke toepassing verschillende sjablonen beschikbaar, die na verloop van tijd worden uitgebreid. Voor Blender bestaan bijvoorbeeld sjablonen die één installatiekopie in tegels opsplitsen, dus delen van een installatiekopie kunnen parallel worden gerenderd.

Voor meer informatie over rendering met de kracht van de cloud raadpleegt u de opties voor de Batch-renderingservice.

> [!div class="nextstepaction"]
> [Service voor batch-rendering](batch-rendering-service.md)
