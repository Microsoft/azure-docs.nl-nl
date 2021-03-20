---
title: Een virtuele machine toevoegen met behulp van een gedeelde installatie kopie in Azure DevTest Labs | Microsoft Docs
description: Informatie over het toevoegen van een virtuele machine (VM) met behulp van een installatie kopie uit de galerie met gekoppelde gedeelde afbeeldingen in Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 9421a1e21be9446b0e59328bd9a3730b57655274
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483853"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Een virtuele machine toevoegen met behulp van een installatie kopie uit de gekoppelde galerie met gedeelde afbeeldingen
Met Azure DevTest Labs kunt u een galerie met gedeelde afbeeldingen aan uw Lab koppelen en vervolgens de installatie kopieën in de galerie gebruiken als basis voor de virtuele machines die u in het Lab maakt. Zie de [Galerie gedeelde afbeeldingen configureren](configure-shared-image-gallery.md)voor meer informatie over het koppelen van een galerie met gedeelde afbeeldingen aan uw Lab. In dit artikel wordt beschreven hoe u een virtuele machine aan uw Lab toevoegt met behulp van een installatie kopie uit de gekoppelde gedeelde installatie kopie galerie als basis. 

## <a name="azure-portal"></a>Azure Portal
In deze sectie leert u hoe u de Azure Portal kunt gebruiken om een virtuele machine toe te voegen aan uw Lab op basis van een installatie kopie uit de gekoppelde gedeelde installatie kopie galerie. Deze sectie bevat geen gedetailleerde stapsgewijze instructies voor het maken van een virtuele machine met behulp van de Azure Portal. Zie [een virtuele machine maken-Azure Portal](devtest-lab-add-vm.md)voor meer informatie. Het markeert alleen de stappen waarbij u een afbeelding selecteert in de galerie met gekoppelde gedeelde afbeeldingen en een versie selecteert van de afbeelding die u wilt gebruiken. 

Wanneer u een virtuele machine toevoegt aan uw Lab, kunt u een installatie kopie selecteren in de galerie met toegevoegde gedeelde afbeeldingen als basis installatie kopie: 

![Een gedeelde installatie kopie voor de basis selecteren](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

Op het tabblad **Geavanceerde instellingen** van de pagina **Lab-resource maken** kunt u de versie van de installatie kopie selecteren die u wilt gebruiken als basis installatie kopie:

![Afbeeldings versie selecteren](./media/add-vm-use-shared-image/select-version-shared-image.png)

U kunt overschakelen naar het gebruik van een andere versie van de installatie kopie nadat de virtuele machine is gemaakt. 

## <a name="resource-manager-template"></a>Resource Manager-sjabloon
Als u een Azure Resource Manager sjabloon gebruikt om een virtuele machine te maken met behulp van een afbeelding met een gedeelde installatie kopie galerie, geeft u een waarde op voor de **sharedImageId** in de sectie **Eigenschappen** . Zie het volgende voorbeeld: 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

Voor een volledig voor beeld van een resource manager-sjabloon raadpleegt [u een virtuele machine maken met behulp van een voor beeld van een gedeelde installatie kopie galerie](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) in onze github-opslag plaats. 

## <a name="rest-api"></a>REST-API

1. Eerst moet u de ID van de installatie kopie in de galerie met gedeelde afbeeldingen ophalen. Eén manier is het weer geven van alle installatie kopieën in de gekoppelde gedeelde installatie kopie galerie met behulp van de volgende GET-opdracht. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Roep de PUT-methode op virtuele machines aan door de ID van de gedeelde installatie kopie die u hebt ontvangen van de vorige aanroep aan te geven `properties.SharedImageId` .

## <a name="next-steps"></a>Volgende stappen
Zie de [Galerie gedeelde afbeeldingen configureren](configure-shared-image-gallery.md)voor meer informatie over het koppelen van een galerie met gedeelde afbeeldingen aan een lab en om deze te configureren.