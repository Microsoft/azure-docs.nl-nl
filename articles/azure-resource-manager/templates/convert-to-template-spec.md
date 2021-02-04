---
title: Portal sjabloon converteren naar sjabloon specificatie
description: Hierin wordt beschreven hoe u een bestaande sjabloon in de Azure Portal galerie converteert naar een sjabloon versie.
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: c59275fca1eb3037b48b7293fc9e507df46b7fcb
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555934"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>Sjabloon galerie in de portal naar sjabloon specificaties converteren

De Azure Portal biedt een manier om Azure Resource Manager sjablonen (ARM-sjablonen) op te slaan in uw account. [Sjabloon specificaties](template-specs.md) bieden echter een gemakkelijke manier om uw sjablonen te delen met gebruikers in uw organisatie en om te koppelen met andere sjablonen. In dit artikel wordt beschreven hoe u bestaande sjablonen in de sjabloon galerie converteert naar sjabloon specificaties.

Als u wilt zien of u sjablonen hebt om te converteren, bekijkt u de [sjabloon galerie in de portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems). Deze sjablonen hebben het resource type `Microsoft.Gallery/myareas/galleryitems` .

## <a name="convert-with-powershell-script"></a>Converteren met Power shell-script

Gebruik voor het vereenvoudigen van het converteren van sjablonen in de sjabloon galerie een Power shell-script uit de Azure Quick Start-sjablonen opslag plaats. Wanneer u het script uitvoert, kunt u een nieuwe sjabloon specificatie maken voor elke sjabloon of een sjabloon downloaden waarmee de sjabloon specificatie wordt gemaakt. Het script verwijdert de sjabloon niet uit de sjabloon galerie.

1. Kopieer het [migratie script](https://github.com/Azure/azure-quickstart-templates/blob/master/201-templatespec-migrate-create/Migrate-GalleryItems.ps1). Sla een lokale kopie op met de naam *Migrate-GalleryItems.ps1*.
1. Als u nieuwe sjabloon specificaties wilt maken, geeft u waarden op voor de `-ResourceGroupName` `-Location` para meters en. 

   Instellen `ItemsToExport` op `MyGalleryItems` om uw sjablonen te exporteren. Stel deze in op `AllGalleryItems` voor het exporteren van alle sjablonen waartoe u toegang hebt.

   In het volgende voor beeld worden nieuwe sjabloon specificaties gemaakt voor elke sjabloon in een resource groep met de naam **migratedRG**. Met het script wordt de resource groep gemaakt als deze nog niet bestaat.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. Voor het downloaden van sjablonen die u kunt gebruiken om de sjabloon specificaties te maken, geeft u geen waarden op voor de resource groep of locatie. Geef in plaats daarvan op `-ExportToFile` . De sjabloon is niet hetzelfde als uw sjabloon in de galerie. In plaats daarvan bevat het een sjabloon specificatie resource die de sjabloon specificatie voor uw sjabloon maakt.

   In het volgende voor beeld worden de sjablonen gedownload zonder sjabloon specificaties te maken.

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   Voor informatie over het implementeren van de sjabloon die de sjabloon specificatie maakt, raadpleegt u [Quick Start: Create and Deploying Temp late spec (preview)](quickstart-create-template-specs.md).

Zie [TemplateSpecs maken op basis van sjabloon galerie sjablonen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-templatespec-migrate-create)voor meer informatie over het script en de bijbehorende para meters.

## <a name="manually-convert-through-portal"></a>Hand matig converteren via Portal

U kunt sjablonen hand matig vanuit de galerie kopiëren naar een nieuwe sjabloon.

1. Open de [sjablonen (preview)](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems) in de portal.
1. Selecteer de sjabloon die u wilt migreren.
1. Selecteer **weergave sjabloon**.
1. Kopieer de inhoud van de sjabloon.
1. Zoek in de portal-zoek balk naar **sjabloon specificaties**. Selecteer deze optie.
1. Selecteer **Sjabloonspecificatie maken**.
1. Geef waarden op voor de naam, het abonnement, de resource groep, de locatie en de versie.
1. Selecteer **volgende: sjabloon bewerken**.
1. Plak de sjabloon die u hebt gekopieerd in de sjabloon galerie voor de inhoud van de sjabloon.
1. Selecteer **Controleren + maken**.
1. Nadat de validatie is voltooid, selecteert u **maken**.

Als u de sjabloon specificatie wilt delen met andere gebruikers in uw organisatie, [stelt u toegangs beheer op basis van rollen](../../role-based-access-control/tutorial-role-assignments-group-powershell.md) in voor de groep of gebruikers die toegang nodig hebben.

## <a name="next-steps"></a>Volgende stappen

Zie [sjabloon Specificaties maken en implementeren](template-specs.md)voor meer informatie over sjabloon specificaties.
