---
title: Overzicht van isolatie en beveiliging van virtuele netwerken
titleSuffix: Azure Machine Learning
description: Gebruik een geïsoleerde Azure-Virtual Network met Azure Machine Learning om werkruimte-resources en rekenomgevingen te beveiligen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/02/2021
ms.topic: how-to
ms.custom: devx-track-python, references_regions, contperf-fy21q1
ms.openlocfilehash: e6b8a4bbbe596ec06f7f9b445dbaa439e1207e46
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107888706"
---
# <a name="virtual-network-isolation-and-privacy-overview"></a>Overzicht van isolatie van virtuele netwerken en privacy

In dit artikel leert u hoe u virtuele netwerken (VNets) gebruikt voor het beveiligen van netwerkcommunicatie in Azure Machine Learning. In dit artikel wordt een voorbeeldscenario gebruikt om u te laten zien hoe u een volledig virtueel netwerk configureert.

Dit artikel is deel één van een vijfdelige reeks die u bij het beveiligen van een Azure Machine Learning werkstroom. We raden u ten zeerste aan dit overzichtsartikel te lezen om eerst inzicht te krijgen in de concepten. 

Hier zijn de andere artikelen in deze reeks:

**1. VNet-overzicht**  >  [2. Beveilig de werkruimte](how-to-secure-workspace-vnet.md)  >  [3. Beveilig de trainingsomgeving](how-to-secure-training-vnet.md)  >  [4. Beveilig de deferencing-omgeving](how-to-secure-inferencing-vnet.md)  >  [5. Studio-functionaliteit inschakelen](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgenomen dat u bekend bent met de volgende onderwerpen:
+ [Virtuele Azure-netwerken](../virtual-network/virtual-networks-overview.md)
+ [IP-netwerken](../virtual-network/public-ip-addresses.md)
+ [Azure Private Link](how-to-configure-private-link.md)
+ [Netwerkbeveiligingsgroepen (NSG's)](../virtual-network/network-security-groups-overview.md)
+ [Netwerkfirewalls](../firewall/overview.md)
## <a name="example-scenario"></a>Voorbeeldscenario

In deze sectie leert u hoe een veelvoorkomende netwerkscenario wordt ingesteld voor het beveiligen Azure Machine Learning communicatie met privé-IP-adressen.

In de onderstaande tabel wordt vergeleken hoe services toegang hebben tot verschillende onderdelen van een Azure Machine Learning netwerk, zowel met een VNet als zonder een VNet.

| Scenario | Werkruimte | Gekoppelde resources | Rekenomgeving trainen | Deferencing-rekenomgeving |
|-|-|-|-|-|-|
|**Geen virtueel netwerk**| Openbare IP | Openbare IP | Openbare IP | Openbare IP |
|**Resources in een virtueel netwerk beveiligen**| Privé-IP-adres (privé-eindpunt) | Openbaar IP-adres (service-eindpunt) <br> **- of -** <br> Privé-IP-adres (privé-eindpunt) | Privé IP-adres | Privé IP-adres  | 

* **Werkruimte:** maak een privé-eindpunt van uw VNet om verbinding te maken met Private Link in de werkruimte. Het privé-eindpunt verbindt de werkruimte met het vnet via verschillende privé-IP-adressen.
* **Gekoppelde resource:** gebruik service-eindpunten of privé-eindpunten om verbinding te maken met werkruimteresources zoals Azure Storage, Azure Key Vault en Azure Container Services.
    * **Service-eindpunten bieden** de identiteit van uw virtuele netwerk aan de Azure-service. Zodra u service-eindpunten in uw virtuele netwerk hebt ingeschakeld, kunt u een regel voor een virtueel netwerk toevoegen om de Azure-servicebronnen te beveiligen in uw virtuele netwerk. Service-eindpunten gebruiken openbare IP-adressen.
    * **Privé-eindpunten zijn** netwerkinterfaces die u veilig verbinden met een powered by Azure Private Link. Privé-eindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service effectief in uw VNet wordt gebruikt.
* **Rekentoegang trainen:** krijg toegang tot trainingsrekendoelen zoals Azure Machine Learning Compute Instance en Azure Machine Learning compute-clusters veilig met privé-IP-adressen. 
* **Berekeningstoegang deductie:** toegang tot AKS-rekenclusters (Azure Kubernetes Services) met privé-IP-adressen.


In de volgende vijf secties ziet u hoe u het hierboven beschreven netwerkscenario beveiligt. Als u uw netwerk wilt beveiligen, moet u het volgende doen:

1. Beveilig de [**werkruimte en de bijbehorende resources.**](#secure-the-workspace-and-associated-resources)
1. Beveilig de [**trainingsomgeving.**](#secure-the-training-environment)
1. Beveilig [**de deferencing-omgeving.**](#secure-the-inferencing-environment)
1. Optioneel: schakel [**studiofunctionaliteit in.**](#optional-enable-studio-functionality)
1. Configureer [**firewallinstellingen.**](#configure-firewall-settings)
1. Configureer [DNS-naamresolutie.](#custom-dns)
## <a name="secure-the-workspace-and-associated-resources"></a>De werkruimte en de bijbehorende resources beveiligen

Gebruik de volgende stappen om uw werkruimte en de bijbehorende resources te beveiligen. Met deze stappen kunnen uw services communiceren in het virtuele netwerk.

1. Maak een [werkruimte Private Link ingeschakeld om](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) communicatie tussen uw VNet en werkruimte mogelijk te maken.
1. Voeg de volgende services toe aan het virtuele netwerk met _behulp_ van een __service-eindpunt__ of een __privé-eindpunt.__ U moet vertrouwde gebruikers ook toegang Microsoft-services tot deze services:
    
    | Service | Eindpuntgegevens | Vertrouwde gegevens toestaan |
    | ----- | ----- | ----- |
    | __Azure Key Vault__| [Service-eindpunt](../key-vault/general/overview-vnet-service-endpoints.md)</br>[Privé-eindpunt](../key-vault/general/private-link-service.md) | [Vertrouwde gebruikers toestaan Microsoft-services firewall te omzeilen](how-to-secure-workspace-vnet.md#secure-azure-key-vault) |
    | __Azure Storage-account__ | [Service-eindpunt](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints)</br>[Privé-eindpunt](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints) | [Toegang verlenen aan vertrouwde Azure-services](../storage/common/storage-network-security.md#grant-access-to-trusted-azure-services) |
    | __Azure Container Registry__ | [Service-eindpunt](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)</br>[Privé-eindpunt](../container-registry/container-registry-private-link.md) | [Vertrouwde services toestaan](../container-registry/allow-access-trusted-services.md) |


![Architectuurdiagram waarin wordt getoond hoe de werkruimte en de bijbehorende resources met elkaar communiceren via service-eindpunten of privé-eindpunten in een VNet](./media/how-to-network-security-overview/secure-workspace-resources.png)

Zie Secure an Azure Machine Learning workspace (Een werkruimte beveiligen) voor gedetailleerde instructies over het voltooien [Azure Machine Learning stappen.](how-to-secure-workspace-vnet.md) 

### <a name="limitations"></a>Beperkingen

Voor het beveiligen van uw werkruimte en de bijbehorende resources binnen een virtueel netwerk gelden de volgende beperkingen:
- Het gebruik van Azure Machine Learning werkruimte met een privékoppeling is niet beschikbaar in de regio'Azure Government of Azure China 21Vianet privékoppeling.
- Alle resources moeten zich achter hetzelfde VNet hebben. Subnetten binnen hetzelfde VNet zijn echter toegestaan.

## <a name="secure-the-training-environment"></a>De trainingsomgeving beveiligen

In deze sectie leert u hoe u de trainingsomgeving beveiligt in Azure Machine Learning. U leert ook hoe Azure Machine Learning een trainings job voltooit om te begrijpen hoe de netwerkconfiguraties samenwerken.

Gebruik de volgende stappen om de trainingsomgeving te beveiligen:

1. Maak een Azure Machine Learning [reken-exemplaar en computercluster in het virtuele netwerk om](how-to-secure-training-vnet.md#compute-instance) de trainings job uit te voeren.
1. [Sta binnenkomende communicatie van Azure Batch Service toe,](how-to-secure-training-vnet.md#mlcports) zodat Batch Service taken kan verzenden naar uw rekenbronnen. 

![Architectuurdiagram waarin wordt getoond hoe u beheerde rekenclusters en exemplaren kunt beveiligen](./media/how-to-network-security-overview/secure-training-environment.png)

Zie Een trainingsomgeving beveiligen voor gedetailleerde instructies over het voltooien [van deze stappen.](how-to-secure-training-vnet.md) 

### <a name="example-training-job-submission"></a>Voorbeeld van het indienen van trainings job 

In deze sectie leert u hoe Azure Machine Learning veilig communiceert tussen services om een trainings job te verzenden. Dit laat zien hoe al uw configuraties samenwerken om de communicatie te beveiligen.

1. De client uploadt trainingsscripts en trainingsgegevens naar opslagaccounts die zijn beveiligd met een service of privé-eindpunt.

1. De client verstuurt een trainings job naar de Azure Machine Learning via het privé-eindpunt.

1. Azure Batch-services ontvangen de taak van de werkruimte en verzenden de trainings job naar de rekenomgeving via de openbare load balancer die is ingericht met de rekenresource. 

1. De rekenresource ontvangt de taak en begint met de training. De rekenbronnen hebben toegang tot beveiligde opslagaccounts om trainingsbestanden te downloaden en uitvoer te uploaden.

### <a name="limitations"></a>Beperkingen

- Azure Compute instance en Azure Compute clusters moeten zich in hetzelfde VNet, dezelfde regio en hetzelfde abonnement als de werkruimte en de bijbehorende resources. 

## <a name="secure-the-inferencing-environment"></a>De deferencing-omgeving beveiligen

In deze sectie leert u welke opties beschikbaar zijn voor het beveiligen van een deferencing-omgeving. U wordt aangeraden AKS-clusters (Azure Kubernetes Services) te gebruiken voor grootschalige productie-implementaties.

U hebt twee opties voor AKS-clusters in een virtueel netwerk:

- Implementeer of koppel een standaard AKS-cluster aan uw VNet.
- Koppel een privé-AKS-cluster aan uw VNet.

**Standaard AKS-clusters** hebben een besturingsvlak met openbare IP-adressen. U kunt een standaard AKS-cluster aan uw VNet toevoegen tijdens de implementatie of een cluster koppelen nadat het is gemaakt.

**Privé-AKS-clusters** hebben een besturingsvlak dat alleen toegankelijk is via privé-IP's. Privé-AKS-clusters moeten worden gekoppeld nadat het cluster is gemaakt.

Zie Secure [an inferencing environment](how-to-secure-inferencing-vnet.md)(Een deferencing-omgeving beveiligen) voor gedetailleerde instructies over het toevoegen van standaard- en privéclusters. 

In het volgende netwerkdiagram ziet u een Azure Machine Learning werkruimte met een privé-AKS-cluster dat is gekoppeld aan het virtuele netwerk.

![Architectuurdiagram waarin wordt getoond hoe u een privé-AKS-cluster koppelt aan het virtuele netwerk. Het AKS-besturingsvlak wordt buiten het VNet van de klant geplaatst](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>Beperkingen
- AKS-clusters moeten deel uitmaken van hetzelfde VNet als de werkruimte en de bijbehorende resources. 

## <a name="optional-enable-public-access"></a>Optioneel: Openbare toegang inschakelen

U kunt de werkruimte achter een VNet beveiligen met behulp van een privé-eindpunt en nog steeds toegang via het openbare internet toestaan. De eerste configuratie is hetzelfde als het [beveiligen van de werkruimte en de bijbehorende resources.](#secure-the-workspace-and-associated-resources) 

Nadat u de werkruimte hebt beveiligen met een privékoppeling, kunt u [vervolgens Openbare toegang inschakelen.](how-to-configure-private-link.md#enable-public-access) Hierna hebt u toegang tot de werkruimte via zowel het openbare internet als het VNet.

### <a name="limitations"></a>Beperkingen

- Als u een Azure Machine Learning-studio via het openbare internet, hebben sommige functies, zoals de ontwerpfunctie, mogelijk geen toegang tot uw gegevens. Dit probleem doet zich voor wanneer de gegevens worden opgeslagen op een service die is beveiligd achter het VNet. Bijvoorbeeld een Azure Storage account.
## <a name="optional-enable-studio-functionality"></a>Optioneel: Studio-functionaliteit inschakelen

[De werkruimte beveiligen](#secure-the-workspace-and-associated-resources)  >  [De trainingsomgeving beveiligen](#secure-the-training-environment)  >  [De deferencingomgeving beveiligen](#secure-the-inferencing-environment)  >  **Studio-functionaliteit inschakelen**  >  [Firewallinstellingen configureren](#configure-firewall-settings)

Als uw opslag zich in een VNet, moet u eerst aanvullende configuratiestappen uitvoeren om volledige functionaliteit in de studio in [te schakelen.](overview-what-is-machine-learning-studio.md) De volgende functie is standaard uitgeschakeld:

* Bekijk een voorbeeld van gegevens in de studio.
* Gegevens visualiseren in de ontwerpfunctie.
* Implementeer een model in de ontwerpfunctie.
* Een AutoML-experiment verzenden.
* Start een labelproject.

Zie Use Azure Machine Learning-studio in a virtual network (Een virtuele netwerk gebruiken) als u de volledige studio-functionaliteit in een VNet [wilt inschakelen.](how-to-enable-studio-virtual-network.md#configure-data-access-in-the-studio) De studio ondersteunt opslagaccounts die gebruikmaken van service-eindpunten of privé-eindpunten.

### <a name="limitations"></a>Beperkingen

[Door ML ondersteunde gegevenslabels bieden](how-to-create-labeling-projects.md#use-ml-assisted-data-labeling) geen ondersteuning voor standaardopslagaccounts die zijn beveiligd achter een virtueel netwerk. U moet een niet-standaardopslagaccount gebruiken voor het labelen van door ML ondersteunde gegevens. Let op: het niet-standaard opslagaccount kan worden beveiligd achter het virtuele netwerk. 

## <a name="configure-firewall-settings"></a>Firewallinstellingen configureren

Configureer uw firewall om de toegang tot uw Azure Machine Learning werkruimte-resources en het openbare internet te beheren. Hoewel we u Azure Firewall, moet u andere firewallproducten kunnen gebruiken om uw netwerk te beveiligen. Als u vragen hebt over het toestaan van communicatie via uw firewall, raadpleegt u de documentatie voor de firewall die u gebruikt.

Zie Werkruimte achter een firewall gebruiken voor meer informatie [over firewallinstellingen.](how-to-access-azureml-behind-firewall.md)

## <a name="custom-dns"></a>Aangepaste DNS

Als u een aangepaste DNS-oplossing voor uw virtuele netwerk wilt gebruiken, moet u hostrecords voor uw werkruimte toevoegen.

Zie Een werkruimte gebruiken met een aangepaste DNS-server voor meer informatie over de vereiste [domeinnamen en IP-adressen.](how-to-custom-dns.md)

## <a name="next-steps"></a>Volgende stappen

Dit artikel is deel één van een vijfdelige reeks virtuele netwerken. Zie de rest van de artikelen voor meer informatie over het beveiligen van een virtueel netwerk:

* [Deel 2: Overzicht van virtueel netwerk](how-to-secure-workspace-vnet.md)
* [Deel 3: De trainingsomgeving beveiligen](how-to-secure-training-vnet.md)
* [Deel 4: De deferencing-omgeving beveiligen](how-to-secure-inferencing-vnet.md)
* [Deel 5: Studio-functionaliteit inschakelen](how-to-enable-studio-virtual-network.md)

Zie ook het artikel over het gebruik van [aangepaste DNS voor](how-to-custom-dns.md) naamresolutie.