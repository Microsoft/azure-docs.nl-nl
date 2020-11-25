---
title: 'Zelfstudie: Azure Spring Cloud implementeren in een virtueel netwerk'
description: Azure Spring Cloud implementeren in een virtueel netwerk (v-net-injectie)
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6e2df9168b880e565ea9b70c82c2c0c1b55b4db8
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94737240"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>Zelfstudie: Azure Spring Cloud implementeren in een virtueel Azure-netwerk (VNet-injectie)

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

In deze zelfstudie wordt uitgelegd hoe u een Azure Spring Cloud service-exemplaar implementeert in uw virtuele netwerk. Dit wordt ook wel VNet-injectie genoemd.  

De implementatie maakt het volgende mogelijk:

* Isolatie van Azure Spring Cloud-apps en serviceruntime van internet in uw bedrijfsnetwerk
* Azure Spring Cloud-interactie met systemen in on-premises datacentrums en/of Azure-Services in andere virtuele netwerken
* Klanten kunnen de inkomende en uitgaande netwerkcommunicatie voor Azure Spring Cloud beheren

## <a name="prerequisites"></a>Vereisten
U moet de Azure Spring Cloud-resourceproviders *Microsoft.AppPlatform* en *Microsoft.ContainerService* registreren volgens de instructies voor het [registreren van een resourceprovider in Azure Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) of door de volgende Azure CLI-opdracht uit te voeren:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```
## <a name="virtual-network-requirements"></a>Vereisten voor het virtuele netwerk
Het virtuele netwerk waarnaar u het Azure Spring Cloud-service-exemplaar implementeert, moet voldoen aan de volgende vereisten:

* **Locatie**: Het virtuele netwerk moet zich op dezelfde locatie bevinden als het Azure Spring Cloud-service-exemplaar.
* **Abonnement**: Het virtuele netwerk moet zich in hetzelfde abonnement bevinden als het Azure Spring Cloud-service-exemplaar.
* **Subnetten**: Het virtuele netwerk moet twee subnetten bevatten die zijn toegewezen aan een Azure Spring Cloud-service-exemplaar: 
    * Een voor de runtime van de service
    * Een voor uw Spring Boot-microservicetoepassingen. 
    * Er is een een-op-een-relatie tussen deze subnetten en een Azure Spring Cloud-service-exemplaar. U moet een nieuw subnet gebruiken voor elk service-exemplaar dat u implementeert en elk subnet kan slechts één service-exemplaar bevatten.
* **Adresruimte**: Eén CIDR-blok maximaal /28 voor het subnet van de serviceruntime en een ander CIDR-blok maximaal /24 voor het subnet van de Spring Boot-microservicetoepassingen.
* **Routeringstabel**: Aan de subnetten mag geen bestaande routeringstabel zijn gekoppeld.

In de volgende procedures wordt de installatie van het virtuele netwerk voor het Azure Spring Cloud-exemplaar beschreven.

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Sla stap 1, 2 en 3 over als u al een virtueel netwerk hebt voor het hosten van het Azure Spring Cloud-service-exemplaar. U kunt beginnen met stap 4 om subnetten voor het virtuele netwerk voor te bereiden.

1. Selecteer **Een resource maken** in het menu van de Azure-portal. Selecteer **Netwerk** > **Virtueel netwerk** in Azure Marketplace.

1. Voer in het dialoogvenster **Virtueel netwerk maken** de volgende informatie in of selecteer deze:

    |Instelling          |Waarde                                             |
    |-----------------|--------------------------------------------------|
    |Abonnement     |Selecteer uw abonnement.                         |
    |Resourcegroep   |Selecteer uw resourcegroep of maak een nieuwe.  |
    |Name             |Voer *azure-spring-cloud-vnet* in                   |
    |Locatie         |Selecteer **VS - oost**                                |

1. Klik op **Next: IP-adressen >** . 
 
1. Bij IPv4-adresruimte typt u 10.1.0.0/16.

1. Selecteer **Subnet toevoegen** en typ vervolgens *service-runtime-subnet* bij **Subnetnaam** en 10.1.0.0/24 bij **Subnetadresbereik**. Klik vervolgens op **Toevoegen**.

1. Selecteer opnieuw **Subnet toevoegen** en voer de **subnetnaam** en het **subnetadresbereik** in, bijvoorbeeld *apps-subnet* en 10.1.1.0/24 .  Klik op **Add**.

1. Klik op **Controleren + maken**. Laat de overige standaardinstellingen ongewijzigd en klik op **Maken**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Machtiging voor service verlenen aan het virtuele netwerk

Selecteer het virtuele netwerk *azure-spring-cloud-vnet* dat u eerder hebt gemaakt.

1. Selecteer **Toegangsbeheer (IAM)** en selecteer vervolgens **Toevoegen > Roltoewijzing toevoegen**.

    ![Toegangsbeheer voor v-net](./media/spring-cloud-v-net-injection/access-control.png)

2. Voer in het dialoog venster **Roltoewijzing toevoegen** de volgende informatie in of selecteer deze:

    |Instelling  |Waarde                                             |
    |---------|--------------------------------------------------|
    |Rol     |Selecteer **Eigenaar**                                  |
    |Selecteer   |Voer *Azure Spring Cloud-resourceprovider* in      |

    Selecteer vervolgens *Azure Spring Cloud-resourceprovider* en klik op **Opslaan**.

    ![Azure Spring Cloud-resourceprovider toegang verlenen tot v-net](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

U kunt hetzelfde ook bereiken door de volgende Azure CLI-opdracht uit te voeren

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-azure-spring-cloud-service-instance-in-the-virtual-network"></a>Azure Spring Cloud-service-exemplaar implementeren in het virtuele netwerk

1. Open Azure Portal via https://ms.portal.azure.com.

1. Zoek in het bovenste zoekvak naar **Azure Spring Cloud** en selecteer **Azure Spring Cloud** in de zoekresultaten.

1. Klik op **+ Toevoegen** op de pagina **Azure Spring Cloud**.

1. Vul het formulier in op de Azure Spring Cloud-pagina **Maken**. 

1. Selecteer dezelfde resourcegroep en regio als die van het virtuele netwerk.

1. Bij **Naam** onder **Servicedetails** selecteert u *azure-spring-cloud-vnet*.

1. Selecteer het tabblad **Netwerken** en selecteer het volgende:

    |Instelling                                |Waarde                                             |
    |---------------------------------------|--------------------------------------------------|
    |Implementeren in uw eigen virtuele netwerk     |Selecteer **Ja**                                    |
    |Virtueel netwerk                        |Selecteer *azure-spring-cloud-vnet*                  |
    |Subnet van serviceruntime                 |Selecteer *service-runtime-subnet*                   |
    |Subnet van Spring Boot-microservice-apps   |Selecteer *apps-subnet*                              |

    ![Tabblad voor het maken van netwerken](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Klik op **Controleren en maken**.

1. Controleer uw specificaties en klik op **Maken**.

Na de implementatie worden er twee extra resourcegroepen in uw abonnement gemaakt om de netwerkresources voor het Azure Spring Cloud-service-exemplaar te hosten.  Ga naar **Start** en selecteer **Resourcegroepen** in het bovenste menu om de volgende nieuwe resourcegroepen te vinden.

De resourcegroep met de naam *ap-svc-rt_{naam service-exemplaar}_{regio service-exemplaar}* bevat netwerkresources voor de serviceruntime van het service-exemplaar.

  ![Serviceruntime](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

De resourcegroep met de naam *ap-app_{naam service-exemplaar}_{regio service-exemplaar}* bevat netwerkresources voor uw Spring Boot-microservicetoepassingen van het service-exemplaar.

  ![Resourcegroep voor apps](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Deze netwerkresources zijn verbonden met het hierboven gemaakte virtuele netwerk.

  ![V-net met verbonden apparaat](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > De resourcegroepen worden volledig beheerd door de Azure Spring Cloud-service. Verwijder of wijzig deze resources niet handmatig.

## <a name="next-steps"></a>Volgende stappen

[Toepassing implementeren naar Azure Spring Cloud in uw VNet](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Zie ook

- [Problemen met Azure Spring Cloud in VNET oplossen](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Verantwoordelijkheden van de klant voor het uitvoeren van Azure Spring Cloud in VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)