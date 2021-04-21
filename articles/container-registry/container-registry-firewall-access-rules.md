---
title: Firewall-toegangsregels
description: Configureer regels voor toegang tot een Azure-containerregister achter een firewall door toegang te verlenen tot REST API- en gegevens-eindpuntdomeinnamen of servicespecifieke IP-adresbereiken.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 6aea4415468eb21e8d010b74597fc68e4ebf573f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783931"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Regels configureren voor toegang tot een Azure-containerregister achter een firewall

In dit artikel wordt uitgelegd hoe u regels op uw firewall configureert om toegang tot een Azure-containerregister toe te staan. Een apparaat Azure IoT Edge een firewall of proxyserver moet bijvoorbeeld toegang hebben tot een containerregister om een containerafbeelding op te halen. Of een vergrendelde server in een on-premises netwerk heeft mogelijk toegang nodig om een afbeelding te pushen.

Als u in plaats daarvan binnenkomende netwerktoegang tot een containerregister alleen binnen een virtueel Azure-netwerk wilt configureren, zie Azure Private Link configureren [voor een Azure-containerregister.](container-registry-private-link.md)

## <a name="about-registry-endpoints"></a>Over register-eindpunten

Als u afbeeldingen of andere artefacten naar een Azure-containerregister wilt pullen of pushen, moet een client, zoals een Docker-daemon, via HTTPS communiceren met twee afzonderlijke eindpunten. Voor clients die toegang hebben tot een register achter een firewall, moet u toegangsregels configureren voor beide eindpunten. Beide eindpunten worden bereikt via poort 443.

* **Register REST API-eindpunt:** verificatie- en registerbeheerbewerkingen worden verwerkt via het openbare REST API van het register. Dit eindpunt is de naam van de aanmeldingsserver van het register. Voorbeeld: `myregistry.azurecr.io`

* **Eindpunt voor opslag (gegevens)** : Azure wijst namens elk register [blobopslag](container-registry-storage.md) toe aan Azure Storage-accounts om de gegevens voor containerafbeeldingen en andere artefacten te beheren. Wanneer een client toegang heeft tot de laag van de afbeelding in een Azure-containerregister, worden aanvragen ingediend met behulp van een eindpunt van een opslagaccount dat door het register wordt geleverd.

Als uw register [geo-gerepliceerd](container-registry-geo-replication.md)is, moet een client mogelijk communiceren met het gegevens-eindpunt in een specifieke regio of in meerdere gerepliceerde regio's.

## <a name="allow-access-to-rest-and-data-endpoints"></a>Toegang tot REST- en gegevens-eindpunten toestaan

* **REST-eindpunt:** toegang toestaan tot de volledig gekwalificeerde aanmeldingsservernaam van het register, `<registry-name>.azurecr.io` of een gekoppeld IP-adresbereik
* **Eindpunt voor opslag (gegevens)** : hiermee staat u toegang tot alle Azure Blob Storage-accounts toe met behulp van het jokerteken of een `*.blob.core.windows.net` gekoppeld IP-adresbereik.
> [!NOTE]
> Azure Container Registry introductie van toegewezen [gegevens](#enable-dedicated-data-endpoints)eindpunten, zodat u de firewallregels van de client voor uw registeropslag nauw kunt instellen. U kunt eventueel gegevens eindpunten inschakelen in alle regio's waar het register zich bevindt of gerepliceerd, met behulp van het formulier `<registry-name>.<region>.data.azurecr.io` .

## <a name="allow-access-by-ip-address-range"></a>Toegang toestaan op IP-adresbereik

Als uw organisatie beleidsregels heeft om alleen toegang te verlenen tot specifieke IP-adressen of adresbereiken, downloadt u [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519).

Als u de IP-bereiken van het ACR REST-eindpunt wilt vinden waarvoor u toegang moet toestaan, zoekt u naar **AzureContainerRegistry** in het JSON-bestand.

> [!IMPORTANT]
> IP-adresbereiken voor Azure-services kunnen worden gewijzigd en updates worden wekelijks gepubliceerd. Download het JSON-bestand regelmatig en maak de benodigde updates in uw toegangsregels. Als uw scenario betrekking heeft op het configureren van regels voor netwerkbeveiligingsgroep in een virtueel Azure-netwerk of als u Azure Firewall, gebruikt u in plaats daarvan de [servicetag](#allow-access-by-service-tag) **AzureContainerRegistry.**
>

### <a name="rest-ip-addresses-for-all-regions"></a>REST IP-adressen voor alle regio's

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>REST IP-adressen voor een specifieke regio

Zoek naar de specifieke regio, zoals **AzureContainerRegistry.AustraliaEast.**

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>IP-adressen voor opslag voor alle regio's

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>IP-adressen voor opslag voor specifieke regio's

Zoek naar de specifieke regio, zoals **Storage.AustraliaCentral.**

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Toegang via servicetag toestaan

Gebruik in een virtueel Azure-netwerk netwerkbeveiligingsregels om verkeer van een resource zoals een virtuele machine naar een containerregister te filteren. Gebruik de servicetag **AzureContainerRegistry** om het maken van de Azure-netwerkregels [te vereenvoudigen.](../virtual-network/network-security-groups-overview.md#service-tags) Een servicetag vertegenwoordigt een groep IP-adres voorvoegsels voor toegang tot een Azure-service wereldwijd of per Azure-regio. De tag wordt automatisch bijgewerkt wanneer adressen worden gewijzigd. 

Maak bijvoorbeeld een regel voor uitgaande netwerkbeveiligingsgroep met de bestemming **AzureContainerRegistry** om verkeer naar een Azure-containerregister toe te staan. Als u alleen toegang wilt toestaan tot de servicetag in een specifieke regio, geeft u de regio op in de volgende indeling: **AzureContainerRegistry.** [*regionaam*].

## <a name="enable-dedicated-data-endpoints"></a>Toegewezen gegevens-eindpunten inschakelen 

> [!WARNING]
> Als u eerder clientfirewalltoegang tot de bestaande eindpunten hebt geconfigureerd, is het overschakelen naar toegewezen gegevens eindpunten van invloed op `*.blob.core.windows.net` de clientconnectiviteit, wat pull-fouten veroorzaakt. Om ervoor te zorgen dat clients consistente toegang hebben, voegt u de nieuwe regels voor gegevens-eindpunten toe aan de firewallregels van de client. Zodra dit is voltooid, kunt u toegewezen gegevens eindpunten inschakelen voor uw registers met behulp van de Azure CLI of andere hulpprogramma's.

Toegewezen gegevens eindpunten is een optionele functie van de **servicelaag Premium** Container Registry. Zie Azure Container Registry servicelagen voor meer [informatie over registerservicelagen en -limieten.](container-registry-skus.md) 

U kunt toegewezen gegevens eindpunten inschakelen met behulp van de Azure Portal of de Azure CLI. De gegevens eindpunten volgen een regionaal patroon, `<registry-name>.<region>.data.azurecr.io` . Als u gegevens-eindpunten in een geo-gerepliceerd register inschakelen, worden eindpunten in alle replicaregio's mogelijk.

### <a name="portal"></a>Portal

Gegevens-eindpunten inschakelen met behulp van de portal:

1. Navigeer naar het containerregister.
1. Selecteer **Netwerken**  >  **Openbare toegang.**
1. Schakel het **selectievakje Toegewezen gegevens-eindpunt inschakelen** in.
1. Selecteer **Opslaan**.

Het gegevens-eindpunt of de gegevens eindpunten worden weergegeven in de portal.

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="Toegewezen gegevens-eindpunten in de portal":::

### <a name="azure-cli"></a>Azure CLI

Gebruik Azure CLI versie 2.4.0 of hoger om gegevens eindpunten in teschakelen met behulp van de Azure CLI. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Met de [volgende az acr update-opdracht][az-acr-update] kunt u toegewezen gegevens eindpunten in een register *myregistry gebruiken.* 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

Als u de gegevens eindpunten wilt weergeven, gebruikt u [de opdracht az acr show-endpoints:][az-acr-show-endpoints]

```azurecli
az acr show-endpoints --name myregistry
```

Uitvoer voor demonstratiedoeleinden toont twee regionale eindpunten

```
{
    "loginServer": "myregistry.azurecr.io",
    "dataEndpoints": [
        {
            "region": "eastus",
            "endpoint": "myregistry.eastus.data.azurecr.io",
        },
        {
            "region": "westus",
            "endpoint": "myregistry.westus.data.azurecr.io",
        }
    ]
}
```

Nadat u toegewezen gegevens eindpunten voor uw register hebt ingesteld, kunt u clientfirewalltoegangsregels inschakelen voor de gegevens eindpunten. Schakel toegangsregels voor gegevens eindpunt in voor alle vereiste registerregio's.

## <a name="configure-client-firewall-rules-for-mcr"></a>Clientfirewallregels configureren voor MCR

Als u toegang nodig hebt tot Microsoft Container Registry (MCR) achter een firewall, bekijkt u de richtlijnen voor het configureren van [mcr-clientfirewallregels.](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md) MCR is het primaire register voor alle door Microsoft gepubliceerde Docker-afbeeldingen, zoals Windows Server-afbeeldingen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [best practices van Azure voor netwerkbeveiliging](../security/fundamentals/network-best-practices.md)

* Meer informatie over [beveiligingsgroepen](../virtual-network/network-security-groups-overview.md) in een virtueel Azure-netwerk

* Meer informatie over het instellen [van Private Link](container-registry-private-link.md) voor een containerregister

* Meer informatie over [toegewezen gegevens-eindpunten](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) voor Azure Container Registry



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az_acr_update
[az-acr-show-endpoints]: /cli/azure/acr#az_acr_show_endpoints