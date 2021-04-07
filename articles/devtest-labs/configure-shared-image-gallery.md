---
title: Een galerie met gedeelde installatie kopieën configureren in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het configureren van een galerie met gedeelde installatie kopieën in Azure DevTest Labs waarmee gebruikers tijdens het maken van Lab-bronnen toegang krijgen tot installatie kopieën van een gedeelde locatie.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: febcff640efc29eb4916250366641635f9d8721e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788418"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Een gedeelde galerie met installatiekopieën configureren in Azure DevTest Labs
DevTest Labs ondersteunt nu de functie [gedeelde installatie kopie galerie](../virtual-machines/shared-image-galleries.md) . Hiermee kunnen Lab-gebruikers toegang krijgen tot installatie kopieën vanaf een gedeelde locatie tijdens het maken van Lab-resources. Het helpt u ook om structuur en organisatie te bouwen rond uw door aangepaste beheerde VM-installatie kopieën. De functie Gedeelde afbeeldingen galerie ondersteunt:

- Beheerde algemene replicatie van installatie kopieën
- Versie beheer en groepering van installatie kopieën voor eenvoudiger beheer
- Maak uw installatie kopieën Maxi maal beschikbaar met ZRS-accounts (zone redundant Storage) in regio's die beschikbaarheids zones ondersteunen. ZRS biedt betere flexibiliteit tegen zonegebonden fouten.
- Meerdere abonnementen delen, en zelfs tussen tenants, met behulp van Azure op rollen gebaseerd toegangs beheer (Azure RBAC).

Zie de documentatie van de [Galerie met gedeelde afbeeldingen](../virtual-machines/shared-image-galleries.md)voor meer informatie. 
 
Als u een groot aantal beheerde installatie kopieën hebt dat u wilt behouden en deze beschikbaar wilt maken in het hele bedrijf, kunt u een galerie met gedeelde afbeeldingen gebruiken als opslag plaats waarmee u gemakkelijk uw installatie kopieën kan bijwerken en delen. Als eigenaar van een lab kunt u een bestaande galerie met gedeelde afbeeldingen koppelen aan uw Lab. Zodra deze galerie is gekoppeld, kunnen Lab-gebruikers computers maken op basis van deze nieuwste installatie kopieën. Een belang rijk voor deel van deze functie is dat DevTest Labs nu het voor deel heeft van het delen van installatie kopieën in Labs, abonnementen en andere regio's. 

> [!NOTE]
> Zie voor meer informatie over de kosten die zijn gekoppeld aan de service voor de galerie met gedeelde afbeeldingen [facturering voor de galerie gedeelde afbeeldingen](../virtual-machines/shared-image-galleries.md#billing).

## <a name="considerations"></a>Overwegingen
- U kunt slechts één gedeelde installatie kopie galerie tegelijk aan een Lab koppelen. Als u een andere galerie wilt koppelen, moet u de bestaande Gallery loskoppelen en er een toevoegen. 
- DevTest Labs biedt momenteel geen ondersteuning voor het uploaden van installatie kopieën naar de galerie via het lab. 
- Bij het maken van een virtuele machine met behulp van een afbeelding in de galerie met gedeelde installatie kopieën, gebruikt DevTest Labs altijd de meest recente gepubliceerde versie van deze installatie kopie. Als een installatie kopie echter meerdere versies heeft, kan de gebruiker ervoor kiezen om een machine te maken op basis van een eerdere versie door te gaan naar het tabblad Geavanceerde instellingen tijdens het maken van virtuele machines.  
- Hoewel in DevTest Labs automatisch een beste poging wordt gedaan om ervoor te zorgen dat de galerie met gedeelde afbeeldingen installatie kopieën repliceert naar de regio waarin het lab zich bevindt, is het niet altijd mogelijk. Om te voor komen dat gebruikers problemen hebben met het maken van Vm's van deze installatie kopieën, moet u ervoor zorgen dat de installatie kopieën al worden gerepliceerd naar de regio van het lab.

## <a name="use-azure-portal"></a>Azure Portal gebruiken
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **alle services** in het navigatie menu links.
1. Selecteer **DevTest Labs** uit de lijst.
1. Selecteer in de lijst met Labs uw **Lab**.
1. Selecteer **configuratie en beleid** in de sectie **instellingen** in het menu links.
1. Selecteer **gedeelde installatie kopie galerieën** onder basis van **virtuele machines** in het menu links.

    ![Menu met galerieën voor gedeelde afbeeldingen](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Koppel een bestaande galerie met gedeelde afbeeldingen aan uw Lab door te klikken op de knop **koppelen** en de galerie te selecteren in de vervolg keuzelijst.

    ![Koppelen](./media/configure-shared-image-gallery/attach-options.png)
1. Nadat de galerie met installatie kopieën is gekoppeld, selecteert u deze om naar de gekoppelde galerie te gaan. Configureer uw galerie om gedeelde installatie kopieën voor het maken van VM'S in **of uit** te scha kelen. Selecteer een galerie met installatie kopieën in de lijst om deze te configureren. 

    Standaard **toestaan dat alle installatie kopieën worden gebruikt als basis van de virtuele machine** is ingesteld op **Ja**. Dit betekent dat alle beschik bare installatie kopieën in de galerie met gedeelde installatie kopieën beschikbaar zijn voor een test gebruiker bij het maken van een nieuwe VM van het lab. Als toegang tot bepaalde installatie kopieën moet worden beperkt, wijzigt u **alle installatie kopieën die moeten worden gebruikt als** **basis voor de** virtuele machine, en selecteert u de installatie kopieën die u wilt toestaan bij het maken van vm's en selecteert u vervolgens de knop **Opslaan** .

    :::image type="content" source="./media/configure-shared-image-gallery/enable-disable.png" alt-text="Installatie kopieën in-of uitschakelen":::

    > [!NOTE]
    > Zowel gegeneraliseerde als gespecialiseerde installatie kopieën in de galerie met gedeelde afbeeldingen worden ondersteund. 
1. Gebruikers met een Lab kunnen vervolgens een virtuele machine maken met behulp van de ingeschakelde installatie kopieën door op **+ toevoegen** te klikken en de installatie kopie te zoeken in de pagina **Kies uw basis** .

    ![Lab-gebruikers](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager sjabloon gebruiken

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Een galerie met gedeelde afbeeldingen aan uw Lab koppelen
Als u een Azure Resource Manager sjabloon gebruikt om een galerie met gedeelde afbeeldingen aan uw Lab toe te voegen, moet u deze toevoegen onder de sectie resources van uw Resource Manager-sjabloon, zoals wordt weer gegeven in het volgende voor beeld:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

Voor een volledig voor beeld van een resource manager-sjabloon raadpleegt u deze resource manager-sjabloon voorbeelden in onze open bare GitHub-opslag plaats: [een galerie met gedeelde afbeeldingen configureren tijdens het maken van een Lab](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-rest-api"></a>REST API gebruiken

### <a name="get-a-list-of-labs"></a>Een lijst met Labs ophalen 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs?api-version= 2018-10-15-preview
```

### <a name="get-the-list-of-shared-image-galleries-associated-with-a-lab"></a>De lijst met gedeelde afbeeldings galerieën ophalen die zijn gekoppeld aan een Lab

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries?api-version= 2018-10-15-preview
   ```

### <a name="create-or-update-shared-image-gallery"></a>Galerie met gedeelde afbeeldingen maken of bijwerken

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="list-images-in-a-shared-image-gallery"></a>Afbeeldingen weer geven in een galerie met gedeelde afbeeldingen

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```



## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen over het maken van een virtuele machine met behulp van een installatie kopie in de galerie met gekoppelde gedeelde afbeeldingen: [een virtuele machine maken met behulp van een gedeelde installatie kopie uit de galerie](add-vm-use-shared-image.md)