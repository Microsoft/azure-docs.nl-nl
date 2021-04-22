---
title: Een Azure Spring Cloud implementeren in een virtueel netwerk
description: Azure Spring Cloud implementeren in een virtueel netwerk (VNet-injectie).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/21/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 5a27fdc91f376127f26ba9f207ca72d8ada8a7b9
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107891748"
---
# <a name="deploy-azure-spring-cloud-in-a-virtual-network"></a>Een Azure Spring Cloud implementeren in een virtueel netwerk

**Dit artikel is van toepassing op:** ââ"ï ̧ Java ââ"ï ̧ C #

In deze zelfstudie wordt uitgelegd hoe u een Azure Spring Cloud-exemplaar implementeert in uw virtuele netwerk. Deze implementatie wordt ook wel VNet-injectie genoemd.

De implementatie maakt het volgende mogelijk:

* Isolatie van Azure Spring Cloud apps en serviceruntime van internetâ€™ in uw bedrijfsnetwerkâ€™.
* Azure Spring Cloud interactie met systemen in â€™on-premises datacenters â€™, of Azure-services in andere virtuele netwerken.
* Klanten kunnen de inkomende en uitgaande netwerkcommunicatie voor Azure Spring Cloud beheren.

> [!Note]
> U kunt uw virtuele Azure-netwerk alleen selecteren wanneer u een nieuw Azure Spring Cloud service-exemplaar. U kunt niet wijzigen om een ander virtueel netwerk te gebruiken nadat Azure Spring Cloud is gemaakt.

## <a name="prerequisites"></a>Vereisten

Registreer de Azure Spring Cloud-resourceproviders **Microsoft.AppPlatform** en **Microsoft.ContainerService** volgens de instructies in [Resourceprovider registreren in de Azure-portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) of door de volgende Azure CLI-opdracht uit te voeren:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>Vereisten voor het virtuele netwerk

Het virtuele netwerk waarin u het Azure Spring Cloud-exemplaar implementeert, moet voldoen aan de volgende vereisten:

* **Locatie**: Het virtuele netwerk moet zich op dezelfde locatie bevinden als het Azure Spring Cloud-exemplaar.
* **Abonnement**: Het virtuele netwerk moet zich in hetzelfde abonnement bevinden als het Azure Spring Cloud-exemplaar.
* **Subnetten**: Het virtuele netwerk moet twee subnetten bevatten die zijn toegewezen aan een Azure Spring Cloud-exemplaar:

    * Eén voor de serviceruntime.
    * Eén voor uw Spring Boot-microservicetoepassingen.
    * Er is een een-op-een-relatie tussen deze subnetten en een Azure Spring Cloud-exemplaar. Gebruik een nieuw subnet voor elk service-exemplaar dat u implementeert. Elk subnet kan slechts één service-exemplaar bevatten.
* **Adresruimte**: CIDR blokkeert maximaal */28* voor het subnet van de serviceruntime en het subnet van de Spring Boot-microservicetoepassingen.
* **Routetabel:** voor de subnetten zijn standaard geen bestaande routetabellen gekoppeld. U kunt [uw eigen routetabel meenemen.](#bring-your-own-route-table)

In de volgende procedures wordt de installatie van het virtuele netwerk voor het Azure Spring Cloud-exemplaar beschreven.

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Sla stap 1, 2 en 3 over als u al een virtueel netwerk hebt voor het hosten van een Azure Spring Cloud-exemplaar. U kunt beginnen met stap 4 om subnetten voor het virtuele netwerk voor te bereiden.

1. Selecteer **Een resource maken** in het menu van Azure Portal. Selecteer in Azure Marketplace **Netwerken** > **Virtueel netwerk**.

1. Voer in het dialoogvenster **Virtueel netwerk maken** de volgende informatie in of selecteer deze:

    |Instelling          |Waarde                                             |
    |-----------------|--------------------------------------------------|
    |Abonnement     |Selecteer uw abonnement.                         |
    |Resourcegroep   |Selecteer uw resourcegroep of maak een nieuwe.  |
    |Name             |Voer **azure-spring-cloud-vnet** in.                 |
    |Locatie         |Selecteer **VS - oost**.                               |

1. Selecteer **Volgende: IP-adressen**.

1. Voer voor de IPv4-adresruimte **10.1.0.0/16** in.

1. Selecteer **Subnet toevoegen**. Voer vervolgens **service-runtime-subnet** in bij **Subnetnaam**, en voer **10.1.0.0/28** in bij **Subnetadresbereik**. Selecteer vervolgens **Toevoegen**.

1. Selecteer opnieuw **Subnet toevoegen**, en voer vervolgens de **Subnetnaam** en het **Subnetadresbereik** in. Voer bijvoorbeeld **apps-subnet** en **10.1.1.0/28** in. Selecteer vervolgens **Toevoegen**.

1. Selecteer **Controleren en maken**. Laat voor de rest de standaardwaarden staan, en selecteer **Maken**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Machtiging voor service verlenen aan het virtuele netwerk
Azure Spring Cloud heeft **eigenaarsmachtiging** voor uw virtuele netwerk nodig om een toegewezen en dynamische service-principal in het virtuele netwerk te verlenen voor verdere implementatie en onderhoud.

Selecteer het virtuele netwerk **azure-spring-cloud-vnet** dat u eerder hebt gemaakt.

1. Selecteer **Toegangsbeheer (IAM)** en selecteer vervolgens **Toevoegen** > **Roltoewijzing toevoegen**.

    ![Schermopname van het tabblad Toegangsbeheer.](./media/spring-cloud-v-net-injection/access-control.png)

1. Voer in het dialoogvenster **Roltoewijzing toevoegen** de volgende informatie in of selecteer deze:

    |Instelling  |Waarde                                             |
    |---------|--------------------------------------------------|
    |Rol     |Selecteer **Eigenaar**.                                 |
    |Selecteer   |Voer **Azure Spring Cloud-resourceprovider** in.   |

    Selecteer vervolgens **Azure Spring Cloud-resourceprovider** en selecteer **Opslaan**.

    ![Schermopname van het selecteren van de Azure Spring Cloud-resourceprovider.](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

U kunt deze stap ook uitvoeren met behulp van de volgende Azure CLI-opdracht:

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

## <a name="deploy-an-azure-spring-cloud-instance"></a>Azure Spring Cloud-exemplaar implementeren

Een Azure Spring Cloud-exemplaar implementeren in het virtuele netwerk:

1. Open [Azure Portal](https://portal.azure.com).

1. Zoek in het bovenste zoekvak naar **Azure Spring Cloud**. Selecteer **Azure Spring Cloud** in het resultaat.

1. Klik op **+ Toevoegen** op de pagina **Azure Spring Cloud**.

1. Vul het formulier in op de Azure Spring Cloud-pagina **Maken**.

1. Selecteer dezelfde resourcegroep en regio als die van het virtuele netwerk.

1. Bij **Naam** onder **Servicedetails** selecteert u **azure-spring-cloud-vnet**.

1. Selecteer het tabblad **Netwerken** en selecteer de volgende waarden:

    |Instelling                                |Waarde                                             |
    |---------------------------------------|--------------------------------------------------|
    |Implementeren in uw eigen virtuele netwerk     |Selecteer **Ja**.                                   |
    |Virtueel netwerk                        |Selecteer **azure-spring-cloud-vnet**.               |
    |Subnet van serviceruntime                 |Selecteer **service-runtime-subnet**.                |
    |Subnet van Spring Boot-microservice-apps   |Selecteer **apps-subnet**.                           |

    ![Schermopname van het tabblad Netwerken op de pagina Azure Spring Cloud maken.](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Selecteer **Controleren en maken**.

1. Controleer uw specificaties, en selecteer **Maken**.

    ![Schermopname van het controleren van specificaties.](./media/spring-cloud-v-net-injection/verify-specifications.png)

Na de implementatie worden er twee extra resourcegroepen in uw abonnement gemaakt, om de netwerkresources voor het Azure Spring Cloud-exemplaar te hosten. Ga naar **Start** en selecteer **Resourcegroepen** in het bovenste menu om de volgende nieuwe resourcegroepen te vinden.

De resourcegroep met de naam **ap-svc-rt_{naam service-exemplaar}_{regio service-exemplaar}** bevat netwerkresources voor de serviceruntime van het service-exemplaar.

  ![Schermopname van de serviceruntime.](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

De resourcegroep met de naam **ap-app_{naam service-exemplaar}_{regio service-exemplaar}** bevat netwerkresources voor uw Spring Boot-microservicetoepassingen van het service-exemplaar.

  ![Schermopname van de apps-resourcegroep.](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Deze netwerkresources zijn verbonden met het virtuele netwerk dat is gemaakt in de vorige afbeelding.

  ![Schermopname van het virtuele netwerk met verbonden apparaten.](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > De resourcegroepen worden volledig beheerd met de Azure Spring Cloud-service. Verwijder of wijzig resources *niet* handmatig.

## <a name="using-smaller-subnet-ranges"></a>Kleinere subnetbereiken gebruiken

Deze tabel toont het maximum aantal app-exemplaren dat Azure Spring Cloud ondersteuning biedt voor het gebruik van kleinere subnetbereiken.

| CIDR voor app-subnet | Totaal aantal IP's | Beschikbare IP's | Maximum aantal app-exemplaren                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> App met 1 kerngeheugen:  96 <br/> App met 2 kerngeheugens: 48<br/>  App met 3 kerngeheugens: 32 <br/> App met 4 kerngeheugens: 24 </p> |
| /27             | 32        | 24            | <p> App met 1 kerngeheugen:  228<br/> App met 2 kerngeheugens: 144<br/>  App met 3 kerngeheugens: 96 <br/>  App met 4 kerngeheugens: 72</p> |
| /26             | 64        | 56            | <p> App met 1 kerngeheugen:  500<br/> App met 2 kerngeheugens: 336<br/>  App met 3 kerngeheugens: 224<br/>  App met 4 kerngeheugens: 168</p> |
| /25             | 128       | 120           | <p> App met 1 kerngeheugen:  500<br> App met 2 kerngeheugens:  500<br>  App met 3 kerngeheugens:  480<br>  App met 4 kerngeheugens: 360</p> |
| /24             | 256       | 248           | <p> App met 1 kerngeheugen:  500<br/> App met 2 kerngeheugens:  500<br/>  App met 3 kerngeheugens: 500<br/>  App met 4 kerngeheugens: 500</p> |

Voor subnetten zijn vijf IP-adressen gereserveerd in Azure, en er zijn minstens vier adressen vereist voor Azure Spring Cloud. Er zijn minstens negen IP-adressen vereist. /29 en /30 zijn dus niet-operationeel.

Voor een subnet voor een serviceruntime is /28 de minimale grootte. De grootte heeft geen invloed op het aantal app-exemplaren.

## <a name="bring-your-own-route-table"></a>Bring Your Own Route Table

Azure Spring Cloud ondersteunt het gebruik van bestaande subnetten en routetabellen.

Als uw aangepaste subnetten geen routetabellen bevatten, maakt Azure Spring Cloud ze voor elk van de subnetten en voegt u er regels aan toe gedurende de levenscyclus van het exemplaar. Als uw aangepaste subnetten routetabellen bevatten, Azure Spring Cloud de bestaande routetabellen tijdens exemplaarbewerkingen en voegt/updates en/of regels dienovereenkomstig toe voor bewerkingen.

> [!Warning] 
> Aangepaste regels kunnen worden toegevoegd aan de aangepaste routetabellen en worden bijgewerkt. Regels worden echter toegevoegd door Azure Spring Cloud en deze mogen niet worden bijgewerkt of verwijderd. Regels zoals 0.0.0.0/0 moeten altijd aanwezig zijn in een bepaalde routetabel en worden toe te staan aan het doel van uw internetgateway, zoals een NVA of een andere gateway voor een ingang. Wees voorzichtig bij het bijwerken van regels wanneer alleen uw aangepaste regels worden gewijzigd.


### <a name="route-table-requirements"></a>Vereisten voor routetabel

De routetabellen waaraan uw aangepaste vnet is gekoppeld, moeten voldoen aan de volgende vereisten:

* U kunt uw Azure-routetabellen alleen aan uw vnet koppelen wanneer u een nieuw Azure Spring Cloud service-exemplaar maakt. U kunt niet wijzigen om een andere routetabel te gebruiken nadat Azure Spring Cloud is gemaakt.
* Zowel het subnet van de microservicetoepassing als het subnet van de serviceruntime moet zijn verbonden met verschillende routetabellen of aan geen van beide.
* Machtigingen moeten worden toegewezen voordat het exemplaar wordt gemaakt. Zorg ervoor dat u Azure *Spring Cloud-eigenaar toe te* staan aan uw routetabellen.
* De gekoppelde routetabelresource kan niet worden bijgewerkt nadat het cluster is gemaakt. Hoewel de resource van de routetabel niet kan worden bijgewerkt, kunnen aangepaste regels worden gewijzigd in de routetabel.
* U kunt een routetabel met meerdere exemplaren niet opnieuw gebruiken vanwege mogelijke conflicterende routeringsregels.

## <a name="next-steps"></a>Volgende stappen

[Toepassing implementeren naar Azure Spring Cloud in uw VNet](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Zie ook

- [Problemen met Azure Spring Cloud in VNET oplossen](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Verantwoordelijkheden van de klant voor het uitvoeren van Azure Spring Cloud in VNET](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
