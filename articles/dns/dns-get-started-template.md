---
title: Een Azure DNS-zone en -record aanmaken - Azure Resource Manager-sjabloon (ARM)
titleSuffix: Azure DNS
description: Informatie over het maken van een DNS-zone en -record in Azure DNS. Dit is een stapsgewijze snelstartgids voor het maken en beheren van uw eerste DNS-zone en -record met behulp van Azure Resource Manager-sjabloon (ARM).
services: dns
author: duongau
ms.service: dns
ms.topic: quickstart
ms.date: 09/8/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: 24460167e2279e7d3001d0bc16d050beb5b55289
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790998"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>Een Azure DNS-zone en -record maken met behulp van een ARM-sjabloon

In deze quickstart wordt beschreven hoe u een ARM-sjabloon (Azure Resource Manager-sjabloon) gebruikt om een Azure DNS-zone met een `A`-record erin te maken.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone).

In deze quickstart maakt u een unieke DNS-zone aan met het achtervoegsel `azurequickstart.org`. Een `A`-record die naar twee IP-adressen verwijst, wordt ook in de zone geplaatst.

:::code language="json" source="~/quickstart-templates/101-azure-dns-new-zone/azuredeploy.json":::

Er zijn twee Azure-resources gedefinieerd in de sjabloon:

- [**Microsoft.Network/dnsZones**](/azure/templates/microsoft.network/dnsZones)
- [**Microsoft.Network/dnsZones/A**](/azure/templates/microsoft.network/dnsZones/A): wordt gebruikt om een `A`-record in de zone te maken.

Zie [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular) als u meer sjablonen wilt vinden die gerelateerd zijn aan Azure Traffic Manager.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer **Proberen** in het volgende codeblok om Azure Cloud Shell te openen en volg de instructies om u aan te melden bij Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Wacht totdat u de prompt van de console ziet.

1. Selecteer **Kopiëren** in het vorige codeblok om het PowerShell-script te kopiëren.

1. Klik met de rechtermuisknop op het shell-consoledeelvenster en selecteer **Plakken**.

1. Voer de waarden in.

    Met de sjabloonimplementatie wordt een zone gemaakt met één `A`-record dat naar twee IP-adressen verwijst. De naam van de resourcegroep is de naam van het project, maar met **rg** eraan toegevoegd.

    Het implementeren van de sjabloon duurt een paar seconden. Wanneer voltooid is de uitvoer vergelijkbaar met:

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Uitvoer van Azure DNS-zone Resource Manager-sjabloon PowerShell-implementatie":::

Azure PowerShell wordt gebruikt om de sjabloon te implementeren. Naast Azure PowerShell kunt u ook de Azure-portal, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-portal.md) voor meer informatie over andere implementatiemethoden.

## <a name="validate-the-deployment"></a>De implementatie valideren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Resourcegroepen** in het linkerdeelvenster.

1. Selecteer de resourcegroep die u in de vorige sectie hebt gemaakt. De naam van de standaard resourcegroep is de naam van het project, maar met **rg** eraan toegevoegd.

1. De resourcegroep moet de volgende resources bevatten die hier worden weergegeven:

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="Resourcegroep voor de implementatie van DNS-zones":::

1. Selecteer de DNS-zone met het achtervoegsel `azurequickstart.org` om te controleren of de zone correct is gemaakt met een `A`-record die verwijst naar de waarden `1.2.3.4` en `1.2.3.5`.

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="Implementatie van DNS-zone":::

1. Kopieer een van de naamservernamen uit de vorige stap.

1. Open een opdrachtprompt en voer de volgende opdracht uit:

   ```cmd
   nslookup www.<dns zone name> <name server name>
   ```

   Bijvoorbeeld:

   ```cmd
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   Er verschijnt een scherm dat er ongeveer als volgt uitziet:

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="DNS-zone nslookup":::

De hostnaam `www.2lwynbseszpam.azurequickstart.org` wordt omgezet in `1.2.3.4` en `1.2.3.5`, precies zoals u het hebt geconfigureerd. Met dit resultaat wordt gecontroleerd of de naamomzetting juist werkt.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u met de DNS-zone hebt gemaakt, niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u de DNS-zone en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u het volgende gemaakt:

- DNS-zone
- `A`-record

Nu u uw eerste DNS-zone en -record hebt gemaakt met behulp van een ARM-sjabloon, kunt u records voor een web-app maken in een aangepast domein.

> [!div class="nextstepaction"]
> [DNS-records voor een web-app in een aangepast domein maken](./dns-web-sites-custom-domain.md)
