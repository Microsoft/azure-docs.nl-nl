---
title: Toegangs regels voor Firewall
description: Configureer regels voor toegang tot een Azure container Registry van achter een firewall, door toegang toe te staan tot REST API en gegevens eindpunt domein namen of servicespecifieke IP-adresbereiken.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 548d64632c1d726111770dfb49f705d31f5ca714
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935985"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Regels configureren voor toegang tot een Azure container Registry achter een firewall

In dit artikel wordt uitgelegd hoe u regels kunt configureren op uw firewall om toegang te krijgen tot een Azure container Registry. Bijvoorbeeld, een Azure IoT Edge apparaat achter een firewall of proxy server moet mogelijk toegang hebben tot een container register om een container installatie kopie te halen. Het is ook mogelijk dat een vergrendelde server in een on-premises netwerk toegang nodig heeft tot het pushen van een installatie kopie.

Zie [Azure private link configureren voor een Azure-container register](container-registry-private-link.md)als u in plaats daarvan binnenkomende netwerk toegang wilt configureren voor een container register in een virtueel Azure-netwerk.

## <a name="about-registry-endpoints"></a>Over register eindpunten

Om installatie kopieën of andere artefacten te halen of pushen naar een Azure container Registry, moet een client, zoals een docker-daemon, communiceren via HTTPS met twee verschillende eind punten. Voor clients die toegang hebben tot een REGI ster van achter een firewall, moet u toegangs regels voor beide eind punten configureren. Beide eind punten worden bereikt via poort 443.

* **Register-rest API** -verificatie-en register beheer bewerkingen worden verwerkt via het open bare rest API eindpunt van het REGI ster. Dit eind punt is de naam van de aanmeldings server van het REGI ster. Voorbeeld: `myregistry.azurecr.io`

* **Opslag (gegevens)-eind punt** : Azure [wijst de Blob-opslag](container-registry-storage.md) in azure Storage accounts uit naam van elk REGI ster toe om de gegevens voor container installatie kopieën en andere artefacten te beheren. Wanneer een client afbeeldings lagen in een Azure container Registry opent, worden er aanvragen gemaakt met behulp van een eind punt voor het opslag account dat wordt meegeleverd met het REGI ster.

Als uw REGI ster [geo-gerepliceerd](container-registry-geo-replication.md)is, moet een client mogelijk communiceren met het gegevens eindpunt in een bepaalde regio of in meerdere gerepliceerde regio's.

## <a name="allow-access-to-rest-and-data-endpoints"></a>Toegang tot REST-en gegevens eindpunten toestaan

* **Rest-eind punt** : toegang tot de volledige gekwalificeerde naam van het REGI ster-server, `<registry-name>.azurecr.io` of een gekoppeld IP-adres bereik toestaan
* **Storage-eind punt (Data)** : Hiermee staat u toegang toe tot alle Azure Blob Storage-accounts met behulp van het Joker teken `*.blob.core.windows.net` of een gekoppeld IP-adres bereik.
> [!NOTE]
> Azure Container Registry zijn [speciale gegevens eindpunten](#enable-dedicated-data-endpoints)geïntroduceerd, zodat u de firewall regels voor de client nauw keurig kunt bepalen voor uw register opslag. Schakel eventueel gegevens eindpunten in in alle regio's waar het REGI ster zich bevindt of repliceert met behulp van het formulier `<registry-name>.<region>.data.azurecr.io` .

## <a name="allow-access-by-ip-address-range"></a>Toegang toestaan op basis van IP-adres bereik

Als uw organisatie beleids regels heeft die alleen toegang tot specifieke IP-adressen of adresbereiken toestaan, down load dan [Azure IP-bereiken en service Tags – open bare Cloud](https://www.microsoft.com/download/details.aspx?id=56519).

Zoek naar **AzureContainerRegistry** in het JSON-bestand om de IP-bereiken van het ACR rest endpoint te vinden waarvoor u toegang wilt toestaan.

> [!IMPORTANT]
> IP-adresbereiken voor Azure-Services kunnen worden gewijzigd en updates worden wekelijks gepubliceerd. Down load het JSON-bestand regel matig en breng de benodigde updates in uw toegangs regels. Als uw scenario bestaat uit het configureren van regels voor netwerk beveiligings groepen in een Azure Virtual Network of als u Azure Firewall gebruikt, gebruikt u in plaats daarvan de **AzureContainerRegistry** - [service label](#allow-access-by-service-tag) .
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

Zoek naar de specifieke regio, bijvoorbeeld **AzureContainerRegistry. AustraliaEast**.

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

### <a name="storage-ip-addresses-for-specific-regions"></a>IP-adressen van opslag voor specifieke regio's

Zoek naar de specifieke regio, zoals **Storage. AustraliaCentral**.

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

## <a name="allow-access-by-service-tag"></a>Toegang per service-tag toestaan

Gebruik in een virtueel Azure-netwerk netwerk beveiligings regels voor het filteren van verkeer van een bron, zoals een virtuele machine, naar een container register. Gebruik het **AzureContainerRegistry** - [service label](../virtual-network/network-security-groups-overview.md#service-tags)om het maken van de regels van het Azure-netwerk te vereenvoudigen. Een servicetag vertegenwoordigt een groep IP-adres voorvoegsels voor de toegang tot een Azure-service wereld wijd of per Azure-regio. Het label wordt automatisch bijgewerkt wanneer adressen worden gewijzigd. 

Maak bijvoorbeeld een regel voor een uitgaande netwerk beveiligings groep met het doel **AzureContainerRegistry** om verkeer naar een Azure container Registry toe te staan. Als u alleen toegang tot de servicetag wilt toestaan in een specifieke regio, geeft u de regio op in de volgende indeling: **AzureContainerRegistry**. [*regio naam*].

## <a name="enable-dedicated-data-endpoints"></a>Dedicated data-eind punten inschakelen 

> [!WARNING]
> Als u eerder client firewall toegang tot de bestaande `*.blob.core.windows.net` eind punten hebt geconfigureerd, is het overschakelen naar toegewezen gegevens eindpunten van invloed op client connectiviteit, waardoor pull-fouten optreden. Om ervoor te zorgen dat clients consistente toegang hebben, voegt u de regels voor het nieuwe gegevens eindpunt toe aan de firewall regels van de client. Wanneer u klaar bent, kunt u speciale gegevens eindpunten voor uw registers inschakelen met behulp van Azure CLI of andere hulpprogram ma's.

Toegewezen gegevens eindpunten is een optionele functie van de service-laag van het **Premium** -container register. Zie [Azure container Registry service lagen](container-registry-skus.md)voor meer informatie over de service lagen en limieten van het REGI ster. 

U kunt speciale gegevens eindpunten inschakelen met behulp van de Azure Portal of de Azure CLI. De gegevens eindpunten volgen een regionaal patroon, `<registry-name>.<region>.data.azurecr.io` . In een geo-gerepliceerd REGI ster kunnen eind punten in alle replica regio's worden ingeschakeld door gegevens eindpunten in te scha kelen.

### <a name="portal"></a>Portal

Gegevens eindpunten inschakelen met behulp van de portal:

1. Navigeer naar het container register.
1. Selecteer   >  **open bare netwerk toegang**.
1. Schakel het selectie vakje **toegewezen gegevens eindpunt inschakelen in** .
1. Selecteer **Opslaan**.

Het gegevens eindpunt of eind punt wordt weer gegeven in de portal.

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="Toegewezen data-eind punten in de portal":::

### <a name="azure-cli"></a>Azure CLI

Gebruik Azure CLI-versie 2.4.0 of hoger om gegevens eindpunten in te scha kelen met behulp van de Azure CLI. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Met de volgende opdracht [AZ ACR update][az-acr-update] kunt u speciale gegevens eindpunten in een REGI ster *myregistry*. 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

Als u de gegevens eindpunten wilt weer geven, gebruikt u de opdracht [AZ ACR show-endpoints][az-acr-show-endpoints] :

```azurecli
az acr show-endpoints --name myregistry
```

Uitvoer voor demonstratie doeleinden bevat twee regionale eind punten

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

Nadat u toegewezen gegevens eindpunten hebt ingesteld voor uw REGI ster, kunt u de firewall regels voor client toegang inschakelen voor de gegevens eindpunten. Toegangs regels voor gegevens eindpunt inschakelen voor alle vereiste register regio's.

## <a name="configure-client-firewall-rules-for-mcr"></a>Firewall regels voor clients configureren voor MCR

Zie de richt lijnen voor het configureren van [MCR-firewall regels](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)voor micro soft container Registry (MCR) van achter een firewall. MCR is het primaire REGI ster voor alle door micro soft gepubliceerde docker-installatie kopieën, zoals Windows Server-installatie kopieën.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Aanbevolen procedures voor Azure voor netwerk beveiliging](../security/fundamentals/network-best-practices.md)

* Meer informatie over [beveiligings groepen](../virtual-network/network-security-groups-overview.md) in een virtueel Azure-netwerk

* Meer informatie over het instellen van een [persoonlijke koppeling](container-registry-private-link.md) voor een container register

* Meer informatie over [toegewezen gegevens eindpunten](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) voor Azure container Registry



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints