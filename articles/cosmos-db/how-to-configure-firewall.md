---
title: Een IP-Firewall configureren voor uw Azure Cosmos DB-account
description: Meer informatie over het configureren van beleid voor IP-toegangs beheer voor firewall ondersteuning op Azure Cosmos-accounts.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/15/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: b4e01375388f12b828d9adcb1e2ed8851061a0bf
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560726"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>IP-firewall in Azure Cosmos DB configureren
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Voor het beveiligen van de gegevens die zijn opgeslagen in uw account, ondersteunt Azure Cosmos DB een op een geheim gebaseerd autorisatie model dat gebruikmaakt van een sterke HMAC (Hash-based Message Authentication Code). Daarnaast ondersteunt Azure Cosmos DB ondersteuning voor toegangs beheer op basis van IP-adressen voor binnenkomende firewalls. Dit model is vergelijkbaar met de firewall regels van een traditioneel database systeem en biedt een extra beveiligings niveau voor uw account. Met firewalls kunt u uw Azure Cosmos-account zodanig configureren dat dit alleen toegankelijk is vanuit een goedgekeurde set machines en/of Cloud Services. Voor toegang tot gegevens die zijn opgeslagen in uw Azure Cosmos-Data Base op basis van deze goedgekeurde sets van machines en services, moet de aanroeper nog steeds een geldig autorisatie token presen teren.

## <a name="ip-access-control"></a><a id="ip-access-control-overview"></a>IP-toegangs beheer

Uw Azure Cosmos-account is standaard toegankelijk via internet, zolang de aanvraag vergezeld gaat van een geldig autorisatie token. Voor het configureren van toegangs beheer op basis van IP-beleid, moet de gebruiker de set IP-adressen of IP-adresbereiken in CIDR-formulier (Classloze Inter-Domain Routing) opgeven dat moet worden opgenomen als de lijst met toegestane client-Ip's om toegang te krijgen tot een bepaald Azure Cosmos-account. Zodra deze configuratie is toegepast, ontvangen aanvragen die afkomstig zijn van computers buiten deze toegestane lijst, het antwoord 403 (verboden). Wanneer u IP-firewall gebruikt, is het raadzaam om Azure Portal toegang te geven tot uw account. Toegang is vereist om het gebruik van Data Explorer toe te staan en om de metrische gegevens voor uw account op te halen die worden weer gegeven op de Azure Portal. Wanneer u Data Explorer gebruikt, moet u niet alleen Azure Portal toegang geven tot uw account, maar ook uw firewall instellingen bijwerken om uw huidige IP-adres toe te voegen aan de firewall regels. Houd er rekening mee dat de wijzigingen in de firewall tot wel 15 minuten kunnen duren en dat de firewall tijdens deze periode een inconsistent gedrag vertoont.

U kunt op IP gebaseerde firewalls combi neren met toegangs beheer voor subnetten en VNET. Door ze te combi neren, kunt u de toegang tot een bron met een openbaar IP-adres en/of van een specifiek subnet in VNET beperken. Zie [toegang Azure Cosmos DB bronnen vanuit virtuele netwerken](./how-to-configure-vnet-service-endpoint.md)voor meer informatie over het gebruik van het subnet en op VNET gebaseerd toegangs beheer.

Om samen te vatten is het autorisatie token altijd vereist voor toegang tot een Azure Cosmos-account. Als de IP-firewall en de VNET-Access Control lijst (Acl's) niet zijn ingesteld, is het Azure Cosmos-account toegankelijk met het autorisatie token. Nadat de IP-firewall-of VNET-Acl's of beide zijn ingesteld op het Azure Cosmos-account, krijgen alleen aanvragen die afkomstig zijn van de bronnen die u hebt opgegeven (en met het autorisatie token) geldige reacties. 

U kunt de gegevens die zijn opgeslagen in uw Azure Cosmos DB-account beveiligen met behulp van IP-firewalls. Azure Cosmos DB ondersteunt toegangs beheer op basis van IP voor binnenkomende firewall ondersteuning. U kunt op een van de volgende manieren een IP-firewall instellen op het Azure Cosmos DB-account:

* Vanuit Azure Portal
* Declaratief met behulp van een Azure Resource Manager-sjabloon
* Programmatisch door de Azure CLI of Azure PowerShell door de eigenschap **ipRangeFilter** bij te werken

## <a name="configure-an-ip-firewall-by-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Een IP-Firewall configureren met behulp van de Azure Portal

Als u het beleid voor IP-toegangs beheer wilt instellen in de Azure Portal, gaat u naar de pagina Azure Cosmos DB account en selecteert u **firewall en virtuele netwerken** in het navigatie menu. Wijzig de optie **toegang vanaf waarde toestaan** voor **geselecteerde netwerken** en selecteer vervolgens **Opslaan**.

:::image type="content" source="./media/how-to-configure-firewall/azure-portal-firewall.png" alt-text="Scherm afbeelding die laat zien hoe u de pagina Firewall opent in de Azure Portal":::

Als IP-toegangs beheer is ingeschakeld, biedt de Azure Portal de mogelijkheid om IP-adressen, IP-adresbereiken en switches op te geven. Switches bieden toegang tot andere Azure-Services en de Azure Portal. De volgende secties bevatten informatie over deze switches.

> [!NOTE]
> Nadat u een beleid voor IP-toegangs beheer voor uw Azure Cosmos DB-account hebt ingeschakeld, worden alle aanvragen voor uw Azure Cosmos DB-account van computers buiten de lijst met toegestane IP-adresbereiken geweigerd. Door de Azure Cosmos DB-resources te bladeren vanuit de portal, wordt ook geblokkeerd om de integriteit van toegangs beheer te garanderen.

### <a name="allow-requests-from-the-azure-portal"></a>Aanvragen van de Azure Portal toestaan

Wanneer u een beleid voor IP-toegangs beheer programmatisch inschakelt, moet u het IP-adres voor de Azure Portal toevoegen aan de eigenschap **ipRangeFilter** om de toegang te behouden. De IP-adressen van de portal zijn:

|Regio|IP-adres|
|------|----------|
|Duitsland|51.4.229.218|
|China|139.217.8.252|
|US Gov|52.244.48.71|
|Alle andere regio's|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

U kunt aanvragen inschakelen voor toegang tot de Azure Portal door de optie **toegang toestaan vanaf Azure Portal** in te scha kelen, zoals wordt weer gegeven in de volgende scherm afbeelding:

:::image type="content" source="./media/how-to-configure-firewall/enable-azure-portal.png" alt-text="Scherm afbeelding die laat zien hoe Azure Portal toegang wordt ingeschakeld":::

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Aanvragen van globale Azure-datacenters of andere bronnen in Azure toestaan

Als u uw Azure Cosmos DB-account opent vanuit Services die geen statisch IP-adres (bijvoorbeeld Azure Stream Analytics en Azure Functions) bieden, kunt u de IP-firewall blijven gebruiken om de toegang te beperken. U kunt toegang vanaf andere bronnen in het Azure inschakelen door de optie **verbindingen accepteren vanuit Azure data centers** te selecteren, zoals wordt weer gegeven in de volgende scherm afbeelding:

:::image type="content" source="./media/how-to-configure-firewall/enable-azure-services.png" alt-text="Scherm afbeelding die laat zien hoe verbindingen van Azure-data centers worden geaccepteerd":::

Wanneer u deze optie inschakelt, wordt het IP-adres `0.0.0.0` toegevoegd aan de lijst met toegestane IP-adressen. Het `0.0.0.0` IP-adres beperkt aanvragen voor uw Azure Cosmos DB-account uit het Azure Data Center IP-bereik. Met deze instelling wordt geen toegang van andere IP-adresbereiken tot uw Azure Cosmos DB-account toegestaan.

> [!NOTE]
> Met deze optie wordt de firewall zodanig geconfigureerd dat alle aanvragen van Azure worden toegestaan, met inbegrip van aanvragen van de abonnementen van andere klanten die in azure zijn geïmplementeerd. De lijst met IP-adressen die worden toegestaan door deze optie is breed, waardoor de effectiviteit van een firewall beleid wordt beperkt. Gebruik deze optie alleen als uw aanvragen afkomstig zijn van statische IP-adressen of subnetten in virtuele netwerken. Als u deze optie kiest, wordt automatisch toegang vanaf het Azure Portal toegestaan omdat de Azure Portal in Azure is geïmplementeerd.

### <a name="requests-from-your-current-ip"></a>Aanvragen van uw huidige IP-adres

Om de ontwikkeling te vereenvoudigen, helpt de Azure Portal u bij het identificeren en toevoegen van het IP-adres van uw client computer aan de lijst met toegestane apps. Apps die uw computer uitvoeren, hebben vervolgens toegang tot uw Azure Cosmos DB-account.

De portal detecteert automatisch het client-IP-adres. Dit kan het client-IP-adres van uw computer zijn of het IP-adres van uw netwerk gateway. Zorg ervoor dat u dit IP-adres verwijdert voordat u uw workloads in productie neemt.

Als u uw huidige IP wilt toevoegen aan de lijst met IP-adressen, selecteert u **mijn huidige IP toevoegen**. Selecteer vervolgens **Opslaan**.

:::image type="content" source="./media/how-to-configure-firewall/enable-current-ip.png" alt-text="Scherm opname van het configureren van Firewall instellingen voor het huidige IP-adres":::

### <a name="requests-from-cloud-services"></a>Aanvragen van Cloud Services

In azure zijn Cloud Services een gemeen schappelijke manier voor het hosten van de service logica van de middelste laag door gebruik te maken van Azure Cosmos DB. Als u toegang tot uw Azure Cosmos DB-account wilt inschakelen vanuit een Cloud service, moet u het open bare IP-adres van de Cloud service toevoegen aan de lijst met toegestane IP-adressen die zijn gekoppeld aan uw Azure Cosmos DB account door [het beleid voor IP-toegangs beheer te configureren](#configure-ip-policy). Dit zorgt ervoor dat alle rolinstanties van Cloud Services toegang hebben tot uw Azure Cosmos DB-account.

U kunt IP-adressen voor uw Cloud Services ophalen in de Azure Portal, zoals wordt weer gegeven in de volgende scherm afbeelding:

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses.png" alt-text="Scherm opname van het open bare IP-adres voor een Cloud service die wordt weer gegeven in de Azure Portal":::

Wanneer u de Cloud service uitbreidt door rolinstanties toe te voegen, hebben deze nieuwe instanties automatisch toegang tot het Azure Cosmos DB-account omdat ze deel uitmaken van dezelfde Cloud service.

### <a name="requests-from-virtual-machines"></a>Aanvragen van virtuele machines

U kunt ook [virtuele machines](https://azure.microsoft.com/services/virtual-machines/) of [virtuele-machine schaal sets](../virtual-machine-scale-sets/overview.md) gebruiken om services van de middelste laag te hosten met behulp van Azure Cosmos db. Als u uw Cosmos DB account zo wilt configureren dat toegang vanaf virtuele machines wordt toegestaan, moet u het open bare IP-adres van de virtuele machine en/of virtuele-machine schaalset configureren als een van de toegestane IP-adressen voor uw Azure Cosmos DB-account door [het beleid voor IP-toegangs beheer te configureren](#configure-ip-policy).

U kunt IP-adressen voor virtuele machines ophalen in de Azure Portal, zoals wordt weer gegeven in de volgende scherm afbeelding:

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses-dns.png" alt-text="Scherm afbeelding met een openbaar IP-adres voor een virtuele machine die wordt weer gegeven in de Azure Portal":::

Wanneer u virtuele-machine-exemplaren aan de groep toevoegt, krijgen ze automatisch toegang tot uw Azure Cosmos DB-account.

### <a name="requests-from-the-internet"></a>Aanvragen van Internet

Wanneer u uw Azure Cosmos DB-account opent vanaf een computer op internet, moet het client-IP-adres of IP-adres bereik van de computer worden toegevoegd aan de lijst met toegestane IP-adressen voor uw account.

## <a name="configure-an-ip-firewall-by-using-a-resource-manager-template"></a><a id="configure-ip-firewall-arm"></a>Een IP-Firewall configureren met behulp van een resource manager-sjabloon

Als u toegangs beheer wilt configureren voor uw Azure Cosmos DB-account, zorgt u ervoor dat in de Resource Manager-sjabloon de eigenschap **ipRules** wordt opgegeven met een matrix met toegestane IP-bereiken. Als u IP-firewall configureert voor een al geïmplementeerd Cosmos-account, moet u ervoor zorgen dat de `locations` matrix overeenkomt met wat er momenteel is geïmplementeerd. U kunt de `locations` matrix en andere eigenschappen niet tegelijkertijd wijzigen. Voor meer informatie en voor beelden van Azure Resource Manager sjablonen voor Azure Cosmos DB Zie, [Azure Resource Manager sjablonen voor Azure Cosmos DB](./templates-samples-sql.md)

> [!IMPORTANT]
> De eigenschap **ipRules** is geïntroduceerd met API-versie 2020-04-01. In de vorige versies werd in plaats daarvan een **ipRangeFilter** -eigenschap weer gegeven. Dit is een lijst met door komma's gescheiden IP-adressen.

In het volgende voor beeld ziet u hoe de eigenschap **ipRules** wordt weer gegeven in API-versie 2020-04-01 of hoger:

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2020-04-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRules": [
      {
        "ipAddressOrRange": "40.76.54.131"
      },
      {
        "ipAddressOrRange": "52.176.6.30"
      },
      {
        "ipAddressOrRange": "52.169.50.45"
      },
      {
        "ipAddressOrRange": "52.187.184.26"
      }
    ]
  }
}
```

Hier volgt een voor beeld van een API-versie van vóór 2020-04-01:

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRangeFilter":"40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a name="configure-an-ip-access-control-policy-by-using-the-azure-cli"></a><a id="configure-ip-firewall-cli"></a>Een beleid voor IP-toegangs beheer configureren met behulp van de Azure CLI

De volgende opdracht laat zien hoe u een Azure Cosmos DB-account maakt met IP-toegangs beheer:

```azurecli-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
ipRangeFilter='192.168.221.17,183.240.196.255,40.76.54.131'

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False \
    --ip-range-filter $ipRangeFilter
```

## <a name="configure-an-ip-access-control-policy-by-using-powershell"></a><a id="configure-ip-firewall-ps"></a>Een beleid voor IP-toegangs beheer configureren met behulp van Power shell

In het volgende script ziet u hoe u een Azure Cosmos DB-account maakt met IP-toegangs beheer:

```azurepowershell-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$ipRules = @("192.168.221.17","183.240.196.255","40.76.54.131")

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0; "isZoneRedundant"=False },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=False }
)

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "ipRules"=$ipRules
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2020-04-01" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Problemen met een IP-toegangs beheer beleid oplossen

U kunt problemen met een IP-toegangs beheer beleid oplossen met behulp van de volgende opties:

### <a name="azure-portal"></a>Azure Portal

Door een beleid voor IP-toegangs beheer in te scha kelen voor uw Azure Cosmos DB-account, blokkeert u alle aanvragen voor uw account van computers buiten de lijst met toegestane IP-adresbereiken. Als u Portal gegevenslaag bewerkingen wilt inschakelen, zoals bladeren door containers en documenten opvragen, moet u Azure Portal toegang expliciet toestaan met behulp van het deel venster **firewall** in de portal.

### <a name="sdks"></a>SDK's

Wanneer u Azure Cosmos DB bronnen opent met behulp van Sdk's op computers die zich niet in de lijst met toegestane gegevens bevinden, wordt een algemeen **403 verboden** antwoord geretourneerd zonder aanvullende informatie. Controleer de lijst met toegestane IP-adressen voor uw account en zorg ervoor dat de juiste beleids configuratie wordt toegepast op uw Azure Cosmos DB-account.

### <a name="source-ips-in-blocked-requests"></a>Bron-IP-adressen in geblokkeerde aanvragen

Schakel diagnostische logboek registratie in voor uw Azure Cosmos DB-account. In deze logboeken worden alle aanvragen en antwoorden weer gegeven. De berichten die betrekking hebben op de firewall worden vastgelegd met een retour code van 403. Door deze berichten te filteren, kunt u de bron-Ip's voor de geblokkeerde aanvragen bekijken. Zie [Azure Cosmos DB diagnostische logboek registratie](./monitor-cosmos-db.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Aanvragen van een subnet met een service-eind punt voor Azure Cosmos DB ingeschakeld

Aanvragen van een subnet in een virtueel netwerk met een service-eind punt voor Azure Cosmos DB ingeschakeld, verzenden het virtuele netwerk en de identiteit van het subnet naar Azure Cosmos DB accounts. Deze aanvragen hebben niet het open bare IP-adres van de bron, waardoor IP-filters deze niet kunnen afwijzen. Als u toegang vanaf specifieke subnetten in virtuele netwerken wilt toestaan, voegt u een lijst met toegangs beheer toe, zoals beschreven in [How to configure Virtual Network and subnet-based Access voor uw Azure Cosmos DB-account](how-to-configure-vnet-service-endpoint.md). Het kan tot vijf tien minuten duren voordat firewall regels worden toegepast en de firewall tijdens deze periode een inconsistent gedrag vertoont.

### <a name="private-ip-addresses-in-list-of-allowed-addresses"></a>Privé-IP-adressen in lijst met toegestane adressen

Het maken of bijwerken van een Azure Cosmos-account met een lijst met toegestane adressen met persoonlijke IP-adressen zal mislukken. Zorg ervoor dat er geen privé-IP-adres is opgegeven in de lijst.

## <a name="next-steps"></a>Volgende stappen

Als u een service-eind punt voor een virtueel netwerk wilt configureren voor uw Azure Cosmos DB-account, raadpleegt u de volgende artikelen:

* [Toegangs beheer voor het virtuele netwerk en het subnet voor uw Azure Cosmos DB-account](how-to-configure-vnet-service-endpoint.md)
* [Virtueel netwerk en op subnet gebaseerde toegang voor uw Azure Cosmos DB-account configureren](how-to-configure-vnet-service-endpoint.md)