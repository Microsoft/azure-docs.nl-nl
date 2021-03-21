---
title: Inhoud vanuit een Cloud-map synchroniseren
description: Meer informatie over het implementeren van uw app naar Azure App Service via inhouds synchronisatie vanuit een Cloud-map, waaronder OneDrive of Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 02/25/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bfee320c7a8b4cbe8439c376350d1234b393bfb5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051217"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Inhoud vanuit een Cloud-map synchroniseren met Azure App Service
In dit artikel wordt beschreven hoe u uw inhoud synchroniseert met [Azure app service](./overview.md) vanuit Dropbox en OneDrive. 

Met de methode voor de synchronisatie van de inhoud kunt u uw app-code en-inhoud in een aangewezen Cloud-map gebruiken om te controleren of deze zich in een kant-en-klaar status bevindt en vervolgens synchroniseert met App Service met één klik op een knop. 

Vanwege onderliggende verschillen in de Api's, wordt **OneDrive voor bedrijven** op dit moment niet ondersteund.

> [!NOTE]
> De pagina **Development Center (klassiek)** in de Azure Portal, die de oude implementatie-ervaring is, wordt in maart 2021 afgeschaft. Deze wijziging is niet van invloed op bestaande implementatie-instellingen in uw app en u kunt de implementatie van apps blijven beheren op de pagina **implementatie centrum** .

## <a name="enable-content-sync-deployment"></a>Implementatie van inhouds synchronisatie inschakelen

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar de beheer pagina voor uw app service-app.

1. Klik in het menu links op instellingen voor het **implementatie centrum**  >  . 

1. Selecteer in **bron** **OneDrive** of **Dropbox**.

1. Klik op **autoriseren** en volg de autorisatie prompts. 

    ![Laat zien hoe u OneDrive of Dropbox kunt autoriseren in het implementatie centrum in de Azure Portal.](media/app-service-deploy-content-sync/choose-source.png)

    U hoeft slechts één keer met OneDrive of Dropbox te autoriseren voor uw Azure-account. Klik op **account wijzigen** om een ander OneDrive-of Dropbox-account voor een app te autoriseren.

1. Selecteer in **map** de map die u wilt synchroniseren. Deze map wordt gemaakt onder het volgende toegewezen inhoudsdistributiepad in OneDrive of Dropbox. 
   
    * **OneDrive**: `Apps\Azure Web Apps`
    * **Dropbox**: `Apps\Azure`
    
1. Klik op **Opslaan**.

## <a name="synchronize-content"></a>Inhoud synchroniseren

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar de beheer pagina voor uw app service-app.

1. Klik in het menu links op **implementatie centrum** opnieuw  >  **implementeren/synchroniseren**. 

    ![Laat zien hoe u uw Cloud-map synchroniseert met App Service.](media/app-service-deploy-content-sync/synchronize.png)
   
1. Klik op **OK** om de synchronisatie te bevestigen.

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Start een synchronisatie door de volgende opdracht uit te voeren en \<group-name> te vervangen en \<app-name> :

```azurecli-interactive
az webapp deployment source sync –-resource-group <group-name> –-name <app-name>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Start een synchronisatie door de volgende opdracht uit te voeren en \<group-name> te vervangen en \<app-name> :

```azurepowershell-interactive
Invoke-AzureRmResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action sync -ApiVersion 2019-08-01 -Force –Verbose
```

-----

## <a name="disable-content-sync-deployment"></a>Implementatie van inhouds synchronisatie uitschakelen

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar de beheer pagina voor uw app service-app.

1. Klik in het linkermenu op instellingen van het **implementatie centrum** om de  >    >  **verbinding te verbreken**. 

    ![Laat zien hoe u de synchronisatie van uw Cloud mappen verbreekt met uw App Service-app in de Azure Portal.](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Implementeren vanuit lokale Git-opslag plaats](deploy-local-git.md)