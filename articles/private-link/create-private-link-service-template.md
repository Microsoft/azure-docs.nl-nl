---
title: 'Snelstartgids: een persoonlijke koppelings service maken in een persoonlijke Azure-koppeling'
description: In deze quickstart gebruikt u een ARM-sjabloon (Azure Resource Manager-sjabloon) om een Private Link-service te maken.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: 34993ad3d3d0494f89bd264a8b7194f52129ad7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555054"
---
# <a name="quickstart-create-a-private-link-service-by-using-an-arm-template"></a>Quickstart: Een Private Link-service maken met behulp van een ARM-sjabloon

In deze quickstart gebruikt u een ARM-sjabloon (Azure Resource Manager-sjabloon) om een Private Link-service te maken.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

U kunt deze quickstart ook voltooien via de [Azure-portal](create-private-link-service-portal.md), [Azure PowerShell](create-private-link-service-powershell.md) of [Azure CLI](create-private-link-service-cli.md).

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-account met een actief abonnement nodig. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>De sjabloon controleren

Met deze sjabloon maakt u een Private Link-service.

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-privatelink-service/).

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json":::

Er worden meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): Er is één virtueel netwerk voor elke virtuele machine.
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): De load balancer die de virtuele machines beschikbaar maakt waarop de service wordt gehost.
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): Er zijn twee netwerkinterfaces: één voor elke virtuele machine.
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): Er zijn twee virtuele machines, één die als host fungeert voor de service en een die de verbinding met het privé-eindpunt test.
- [**Microsoft.Compute/virtualMachines/extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions): De extensie waarmee een webserver wordt geïnstalleerd.
- [**Microsoft.Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices): De Private Link-service om de service beschikbaar te maken.
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): Er zijn twee openbare IP-adressen: één voor elke virtuele machine.
- [**Microsoft.Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints): Het privé-eindpunt voor toegang tot de service.

## <a name="deploy-the-template"></a>De sjabloon implementeren

U kunt de ARM-sjabloon als volgt implementeren in Azure:

1. Selecteer **Implementeren in Azure** om u aan te melden bij Azure en de sjabloon te openen. Met de sjabloon maakt u een virtuele machine, een standaard-load balancer, een Private Link-service, een privé-eindpunt, netwerken en een virtuele machine om te valideren.

   [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. Selecteer of maak een resourcegroep.
3. Voer de gebruikersnaam en het wachtwoord in van de beheerder van de virtuele machine.
4. Lees de algemene voorwaarden. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden** > **Kopen** als u akkoord gaat.

## <a name="validate-the-deployment"></a>De implementatie valideren

> [!NOTE]
> De ARM-sjabloon genereert een unieke naam voor de virtuele machine myConsumerVm<b>{uniekeid}</b>. Gebruik de voor u gegenereerde waarde in plaats van **{uniekeid}** .

### <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM _myConsumerVm{uniekeid}_ via internet:

1.  Voer op de zoekbalk van de portal _myConsumerVm{uniekeid}_ in.

2.  Selecteer **Verbinden**. Het venster **Verbinding maken met virtuele machine** wordt geopend.

3.  Selecteer **RDP-bestand downloaden**. In Azure wordt een _RDP_-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

4.  Open het gedownloade RDP-bestand.

    a. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    b. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven tijdens het maken van de VM.
    
    > [!NOTE]
    > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.

5.  Selecteer **OK**.

6.  Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

7.  Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.

### <a name="access-the-http-service-privately-from-the-vm"></a>De http-service via een privé-verbinding openen vanaf de VM

U kunt als volgt verbinding maken met de http-service vanaf de VM met behulp van het privé-eindpunt.

1.  Ga naar het externe bureaublad van _myConsumerVm{uniekeid}_ .
2.  Open een browser en voer het adres van het privé-eindpunt in: `http://10.0.0.5/`.
3.  De standaardpagina van IIS wordt weergegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u met de Private Link-service hebt gemaakt niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u de Private Link-service en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen


Zie voor meer informatie over de services die een privé-eindpunt ondersteunen:
> [!div class="nextstepaction"]
> [Beschikbaarheid van Private Link](private-link-overview.md#availability)
