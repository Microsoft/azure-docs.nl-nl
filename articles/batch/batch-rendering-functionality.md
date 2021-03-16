---
title: Weergave mogelijkheden
description: Standaard Azure Batch mogelijkheden worden gebruikt voor het uitvoeren van werk belastingen en apps voor rendering. Batch bevat specifieke functies voor het ondersteunen van rendering-workloads.
author: mscurrell
ms.author: markscu
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: a2e2cfb71999bd5ab83591448342d4bac1dabdd5
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496334"
---
# <a name="azure-batch-rendering-capabilities"></a>Mogelijkheden voor Azure Batch Rendering

Standaard Azure Batch mogelijkheden worden gebruikt voor het uitvoeren van werk belastingen en toepassingen voor rendering. Batch bevat ook specifieke functies ter ondersteuning van het weer geven van werk belastingen.

Zie [dit artikel](./batch-service-workflow-features.md)voor een overzicht van batch concepten, inclusief Pools, taken en taken.

## <a name="batch-pools-using-custom-vm-images-and-standard-application-licensing"></a>Batch-Pools met behulp van aangepaste VM-installatie kopieën en standaard toepassings licenties

Net als bij andere werk belastingen en typen toepassingen, kan een aangepaste VM-installatie kopie worden gemaakt met de vereiste rendering-toepassingen en-modules. De aangepaste VM-installatie kopie wordt in de [Galerie met gedeelde afbeeldingen](../virtual-machines/shared-image-galleries.md) geplaatst en [kan worden gebruikt voor het maken van batch-Pools](batch-sig-images.md).

De opdracht regel teken reeksen van de taak moeten verwijzen naar de toepassingen en paden die worden gebruikt bij het maken van de aangepaste VM-installatie kopie.

De meeste rendering-toepassingen vereisen licenties die zijn verkregen van een licentie server. Als er een bestaande on-premises licentie server is, moeten zowel de groep als de licentie server zich in hetzelfde [virtuele netwerk](../virtual-network/virtual-networks-overview.md)bevinden. Het is ook mogelijk om een licentie server op een virtuele Azure-machine uit te voeren, waarbij de batch-pool en de VM van de licentie server zich in hetzelfde virtueel netwerk bevindt.

## <a name="batch-pools-using-rendering-vm-images"></a>Batch-Pools met rendering van VM-installatie kopieën

> [!IMPORTANT]
> De rendering van VM-installatie kopieën en licenties voor betalen voor gebruik zijn [afgeschaft en zullen op 29 februari 2024 worden ingetrokken](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/). Als u batch voor rendering wilt gebruiken, [moet u een aangepaste VM-installatie kopie en Standard-toepassings licentie gebruiken.](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

### <a name="rendering-application-installation"></a>De installatie van een toepassing weer geven

Een Azure Marketplace-rendering VM-installatie kopie kan worden opgegeven in de groeps configuratie als alleen de vooraf geïnstalleerde toepassingen moeten worden gebruikt.

Er is een Windows-installatie kopie en een CentOS-installatie kopie.  In [Azure Marketplace](https://azuremarketplace.microsoft.com)kunt u de VM-installatie kopieën vinden door te zoeken naar batch rendering.

Zie de [zelf studie over Azure cli-rendering](./tutorial-rendering-cli.md)voor een voor beeld van een pool configuratie.  De Azure Portal en Batch Explorer bieden GUI-hulpprogram ma's voor het selecteren van een VM-installatie kopie die wordt weer gegeven wanneer u een groep maakt.  Als u een batch-API gebruikt, geeft u voor [ImageReference](/rest/api/batchservice/pool/add#imagereference) de volgende eigenschaps waarden op bij het maken van een groep:

| Uitgever | Aanbieding | Sku | Versie |
|---------|---------|---------|--------|
| batch | Rendering-centos73 | aanwijzer | meest recente |
| batch | Rendering-windows2016 | aanwijzer | meest recente |

Er zijn andere opties beschikbaar als er extra toepassingen vereist zijn voor de virtuele machines van de groep:

* Een aangepaste installatie kopie uit de galerie met gedeelde afbeeldingen:
  * Met deze optie kunt u uw virtuele machine configureren met de exacte toepassingen en specifieke versies die u nodig hebt. Zie [een groep maken met de galerie gedeelde afbeeldingen](batch-sig-images.md)voor meer informatie. Auto Desk-en chaos-groep hebben respectievelijk Arnold en V-Ray aangepast om te valideren op basis van een Azure Batch Licensing-service. Zorg ervoor dat u beschikt over de versies van deze toepassingen met deze ondersteuning, anders werkt de licentie voor betalen per gebruik niet. Voor de huidige versies van Maya of 3ds Max is geen licentie server vereist bij het uitvoeren van headless (in batch/opdracht regel modus). Neem contact op met de ondersteuning van Azure als u niet zeker weet hoe u kunt door gaan met deze optie.
* [Toepassings pakketten](./batch-application-packages.md):
  * Verpakt de toepassings bestanden met een of meer ZIP-bestanden, upload via de Azure Portal en geef het pakket op in de groeps configuratie. Wanneer u groeps-Vm's maakt, worden de ZIP-bestanden gedownload en worden de bestanden uitgepakt.
* Bron bestanden:
  * Toepassings bestanden worden geüpload naar Azure Blob-opslag en u geeft bestands verwijzingen op in de [taak groep starten](/rest/api/batchservice/pool/add#starttask). Wanneer u groeps-Vm's maakt, worden de bron bestanden gedownload op elke virtuele machine.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Licentie verlening voor gebruik voor vooraf geïnstalleerde toepassingen

De toepassingen die worden gebruikt en waarvoor licentie kosten gelden, moeten worden opgegeven in de groeps configuratie.

* Geef de `applicationLicenses` eigenschap op bij het [maken van een groep](/rest/api/batchservice/pool/add#request-body).  De volgende waarden kunnen worden opgegeven in de matrix met teken reeksen: "Vray", "Arnold", "3dsmax", "Maya".
* Wanneer u een of meer toepassingen opgeeft, worden de kosten van deze toepassingen toegevoegd aan de kosten van de Vm's.  Toepassings prijzen worden vermeld op de [pagina met Azure batch prijzen](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Als u in plaats daarvan verbinding maakt met een licentie server voor het gebruik van de rendering-toepassingen, geeft u de eigenschap niet op `applicationLicenses` .

U kunt de Azure Portal of Batch Explorer gebruiken om toepassingen te selecteren en de prijzen van de toepassing weer te geven.

Als er een poging wordt gedaan om een toepassing te gebruiken, maar de toepassing niet is opgegeven in de `applicationLicenses` eigenschap van de groeps configuratie of als er geen licentie server is bereikt, mislukt de uitvoering van de toepassing met een licentie fout en afsluit code die niet gelijk is aan nul.

### <a name="environment-variables-for-pre-installed-applications"></a>Omgevings variabelen voor vooraf geïnstalleerde toepassingen

Als u de opdracht regel voor het weer geven van taken wilt maken, moet u de installatie locatie van de uitvoer bare bestanden van de rendering-toepassing opgeven.  Er zijn systeem omgevingsvariabelen gemaakt op de VM-installatie kopieën van Azure Marketplace. deze kunnen worden gebruikt in plaats van dat ze daad werkelijke paden moeten opgeven.  Deze omgevings variabelen zijn een aanvulling op de [standaard batch omgevings variabelen](./batch-compute-node-environment-variables.md) die voor elke taak zijn gemaakt.

|Toepassing|Uitvoer bare toepassing|Omgevings variabele|
|---------|---------|---------|
|Auto Desk 3ds Max 2021|3dsmaxcmdio.exe|3DSMAX_2021_EXEC|
|Autodesk Maya 2020|render.exe|MAYA_2020_EXEC|
|Chaos-groeps V-Ray-zelfstandig|vray.exe|VRAY_4 VRAY_4.10.03_EXEC|
|Arnold 2020-opdracht regel|kick.exe|ARNOLD_2020_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

## <a name="azure-vm-families"></a>Azure VM-families

Net als bij andere werk belastingen kunnen de systeem vereisten voor het renderen van toepassingen verschillen en de prestatie vereisten verschillen voor taken en projecten.  Een groot aantal verschillende VM-families is beschikbaar in azure, afhankelijk van uw vereisten: de laagste kosten, de beste prijs/prestaties, de beste prestaties, enzovoort.
Sommige rendering-toepassingen, zoals Arnold, zijn gebaseerd op een CPU. andere, zoals V-Ray en overvloei cycli, kunnen Cpu's en/of Gpu's gebruiken.
[Zie VM-typen en-grootten](../virtual-machines/sizes.md)voor een beschrijving van de beschik bare VM-families en VM-grootten.

## <a name="low-priority-vms"></a>Virtuele machines met lage prioriteit

Net als bij andere workloads kunnen virtuele machines met lage prioriteit worden gebruikt in batch-Pools voor rendering.  Virtuele machines met lage prioriteit hebben hetzelfde als normale, specifieke Vm's, maar gebruiken de Azure-capaciteit van overschot en zijn beschikbaar voor een grote korting.  Het saldo voor het gebruik van virtuele machines met lage prioriteit is dat deze Vm's mogelijk niet kunnen worden toegewezen of op elk gewenst moment kunnen worden gebruikt, afhankelijk van de beschik bare capaciteit. Daarom zullen Vm's met lage prioriteit niet geschikt zijn voor alle rendering-taken. Als afbeeldingen bijvoorbeeld veel uur duren om weer te geven, is het waarschijnlijk dat de rendering van die installatie kopieën wordt onderbroken en opnieuw wordt gestart, omdat de Vm's die worden afgebroken niet acceptabel zijn.

Zie [virtuele machines met lage prioriteit gebruiken met batch](./batch-low-pri-vms.md)voor meer informatie over de kenmerken van vm's met lage prioriteit en verschillende manieren om ze te configureren met behulp van batch.

## <a name="jobs-and-tasks"></a>Taken

Er is geen rendering-specifieke ondersteuning vereist voor taken en taken.  Het hoofd configuratie-item is de opdracht regel van de taak, die moet verwijzen naar de vereiste toepassing.
Wanneer de VM-installatie kopieën van Azure Marketplace worden gebruikt, is het best practice om de omgevings variabelen te gebruiken om het pad en het uitvoer bare bestand van de toepassing op te geven.

## <a name="next-steps"></a>Volgende stappen

Voor voor beelden van batch rendering kunt u de twee zelf studies uitproberen:

* [Rendering met behulp van Azure CLI](./tutorial-rendering-cli.md)
* [Weergeven met Batch Explorer](./tutorial-rendering-batchexplorer-blender.md)