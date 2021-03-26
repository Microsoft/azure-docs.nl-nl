---
title: 'Quickstart: Een Azure DB for PostgreSQL maken met een ARM-sjabloon'
description: In deze quickstart krijgt u informatie over het maken van een enkele Azure Database for PostgreSQL-server met behulp van een Azure Resource Manager-sjabloon.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 02/11/2021
ms.openlocfilehash: db65a4bb5137da23f125d67920a2ff9a1a801141
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606717"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---single-server"></a>Quickstart: Een ARM-sjabloon gebruiken om een Azure Database for PostgreSQL te maken - enkele server

Azure Database for PostgreSQL is een beheerde service waarmee u PostgreSQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. In deze quickstart gebruikt u een Azure Resource Manager-sjabloon (ARM-sjabloon) om een Azure Database for PostgreSQL - enkele server te maken in Azure Portal, PowerShell of Azure CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementeren in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-postgresql-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

# <a name="portal"></a>[Portal](#tab/azure-portal)

Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/).
* Als u de code lokaal wilt uitvoeren, [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[CLI](#tab/CLI)

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/).
* Als u de code lokaal wilt uitvoeren, [Azure CLI](/cli/azure/).

---

## <a name="review-the-template"></a>De sjabloon controleren

U maakt een Azure Database for PostgreSQL-server met een geconfigureerde set reken- en opslagresources. Zie [Prijscategorieën in Azure Database for PostgreSQL - enkele server](concepts-pricing-tiers.md) voor meer informatie. De server wordt gemaakt binnen een [Azure-resourcegroep](../azure-resource-manager/management/overview.md).

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-managed-postgresql-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-postgresql-with-vnet/azuredeploy.json":::

In de sjabloon zijn vijf Azure-resources gedefinieerd:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforPostgreSQL/servers**](/azure/templates/microsoft.dbforpostgresql/servers)
* [**Microsoft.DBforPostgreSQL/servers/virtualNetworkRules**](/azure/templates/microsoft.dbforpostgresql/servers/virtualnetworkrules)
* [**Microsoft.DBforPostgreSQL/servers/firewallRules**](/azure/templates/microsoft.dbforpostgresql/servers/firewallrules)

Meer Azure Database for PostgreSQL-sjabloonvoorbeelden vindt u in [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbforpostgresql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>De sjabloon implementeren

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer de volgende koppeling om de Azure Database for PostgreSQL-serversjabloon te implementeren in de Azure-portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementeren in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-postgresql-with-vnet%2fazuredeploy.json)

Doe het volgende op de pagina **Azure Database for PostgreSQL implementeren met VNet**:

1. Selecteer voor **Resourcegroep** de optie **Nieuwe maken**, voer een naam in voor de nieuwe resourcegroep en selecteer **OK**.

2. Als u een nieuwe resourcegroep hebt gemaakt, selecteert u een **Locatie** voor de resourcegroep en de nieuwe server.

3. Voer gegevens bij **Servernaam**, **Aanmelden beheerder** en  **Wachtwoord aanmelden beheerder** in.

    :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-arm-template/deploy-azure-database-for-postgresql-with-vnet.png" alt-text="Azure Database for PostgreSQL implementeren met VNet-venster, Azure-snelstart-sjabloon, Azure-portal":::

4. Wijzig desgewenst de andere standaardinstellingen:

    * **Abonnement**: het Azure abonnement dat u wilt gebruiken voor de server.
    * **SKU-capaciteit**: de capaciteit van de vCore, die *2* (de standaardinstelling), *4*, *8*, *16*, *32* of *64* kan zijn.
    * **SKU-naam**: het voorvoegsel van de SKU-laag, de SKU-familie en de SKU-capaciteit, verbonden door middel van onderstrepingstekens, zoals *B_Gen5_1*, *GP_Gen5_2* (de standaardinstelling) of *MO_Gen5_32*.
    * **SKU-grootte MB**: de opslaggrootte, in megabytes, van de Azure Database for PostgreSQL-server (standaard *51200*).
    * **SKU-laag**: de implementatielaag, zoals *Basic*, *GeneralPurpose* (de standaardinstelling) of *MemoryOptimized*.
    * **SKU-familie**: *Gen4* of *Gen5* (de standaardinstelling), wat de hardwaregeneratie voor de serverimplementatie aangeeft.
    * **Postgresql-versie**: de versie van de PostgreSQL-server die u wilt implementeren, zoals *9,5*, *9,6*, *10* of *11* (de standaardinstelling).
    * **Retentiedagen van back-ups**: de gewenste periode voor het bewaren van geo-redundante back-ups, in dagen (standaard *7*).
    * **Geografisch redundante back-up**: *Ingeschakeld* of *Uitgeschakeld* (de standaardinstelling), afhankelijk van de vereisten voor geo-herstel na noodgevallen (geo-DR).
    * **Naam van virtueel netwerk**: de naam van het virtuele netwerk (standaard *azure_postgresql_vnet*).
    * **Subnetnaam**: de naam van het subnet (standaard *azure_postgresql_subnet*).
    * **Naam van regel van het virtuele netwerk**: de naam van de regel van het virtuele netwerk waarmee het subnet wordt toegestaan (standaard *AllowSubnet*).
    * **Adresvoorvoegsel VNet**: het adresvoorvoegsel voor het virtuele netwerk (standaard *10.0.0.0/16*).
    * **Subnetvoorvoegsel**: het adresvoorvoegsel voor het subnet (standaard *10.0.0.0/16*).

5. Lees de voorwaarden en schakel vervolgens het selectievakje **Ik ga akkoord met de bovenstaande voorwaarden** in.

6. Selecteer **Aankoop**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Gebruik de volgende interactieve code om een nieuwe Azure Database for PostgreSQL-server te maken met behulp van de sjabloon. De code vraagt u om de naam van de nieuwe server, de naam en de locatie van een nieuwe resourcegroep en een naam en wachtwoord voor een beheerdersaccount op te geven.

Als u de code in Azure Cloud Shell wilt uitvoeren, selecteert u **Probeer het nu** in de linkerbovenhoek van een willekeurig codeblok.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Gebruik de volgende interactieve code om een nieuwe Azure Database for PostgreSQL-server te maken met behulp van de sjabloon. De code vraagt u om de naam van de nieuwe server, de naam en de locatie van een nieuwe resourcegroep en een naam en wachtwoord voor een beheerdersaccount op te geven.

Als u de code in Azure Cloud Shell wilt uitvoeren, selecteert u **Probeer het nu** in de linkerbovenhoek van een willekeurig codeblok.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for PostgreSQL server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for PostgreSQL server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

# <a name="portal"></a>[Portal](#tab/azure-portal)

Volg deze stappen om een overzicht van uw nieuwe Azure Database for PostgreSQL-server te bekijken:

1. Zoek en selecteer **Azure Database for PostgreSQL-servers** in de [Azure-portal](https://portal.azure.com).

2. Selecteer uw nieuwe server in de lijst met databases. De pagina **Overzicht** voor uw nieuwe Azure Database for PostgreSQL-server wordt weergegeven.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Voer de volgende interactieve code uit om details te bekijken van uw Azure Database for PostgreSQL-server. U moet de naam van de nieuwe server invoeren.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DbForPostgreSQL/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Voer de volgende interactieve code uit om details te bekijken van uw Azure Database for PostgreSQL-server. U moet de naam en de resourcegroep van de nieuwe server opgeven.

```azurecli-interactive
read -p "Enter your Azure Database for PostgreSQL server name: " serverName &&
read -p "Enter the resource group where the Azure Database for PostgreSQL server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForPostgreSQL/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="exporting-arm-template-from-the-portal"></a>ARM-sjabloon vanuit de portal exporteren
U kunt [een arm-sjabloon exporteren](../azure-resource-manager/templates/export-template-portal.md) vanuit de Azure Portal. Er zijn twee manieren om een sjabloon te exporteren:

- [Exporteren uit resource groep of resource](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource). Met deze optie wordt een nieuwe sjabloon gegenereerd op basis van bestaande resources. De geëxporteerde sjabloon is een moment opname van de huidige status van de resource groep. U kunt een volledige resource groep exporteren of specifieke resources binnen die resource groep.
- [Exporteren vóór implementatie of van geschiedenis](../azure-resource-manager/templates/export-template-portal.md#export-template-before-deployment). Met deze optie wordt een exacte kopie van een sjabloon opgehaald die wordt gebruikt voor de implementatie.

Wanneer u de sjabloon exporteert, ```"properties":{ }```  ziet u in de sectie van de postgresql-server resource dat u ```administratorLogin``` ```administratorLoginPassword``` geen beveiligings redenen zult zien. U **moet** deze para meters aan uw sjabloon toevoegen voordat u de sjabloon implementeert, anders mislukt de sjabloon.

```
"resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "name": "[parameters('servers_name')]",
      "location": "southcentralus",
      "sku": {
                "name": "B_Gen5_1",
                "tier": "Basic",
                "family": "Gen5",
                "capacity": 1
            },
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
```



## <a name="clean-up-resources"></a>Resources opschonen

Als de resourcegroep niet meer nodig is, verwijdert u deze. Hierdoor worden ook de resources in de resourcegroep verwijderd.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zoek en selecteer [Resourcegroepen](https://portal.azure.com) in **Azure Portal**.

2. Kies in de lijst met resourcegroepen de naam van uw resourcegroep.

3. Selecteer op de pagina **Overzicht** van uw resourcegroep de optie **Resourcegroep verwijderen**.

4. Typ de naam van de resourcegroep in het bevestigingsvenster. Selecteer vervolgens **Verwijderen**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the Resource Group name: " resourceGroupName &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Volgende stappen

Zie voor een stapsgewijze zelfstudie die u door het proces van het maken van een sjabloon leidt:

> [!div class="nextstepaction"]
> [Zelfstudie: Uw eerste ARM-sjabloon maken en implementeren](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
