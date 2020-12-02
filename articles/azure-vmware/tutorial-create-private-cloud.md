---
title: Zelfstudie - vSphere-cluster implementeren in Azure
description: Meer informatie over het implementeren van een vSphere-cluster in Azure met behulp van Azure VMware Solution
ms.topic: tutorial
ms.date: 11/19/2020
ms.openlocfilehash: 93937f8ca0918494810885f5cb45de571a6e1529
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966306"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>Zelfstudie: Een Azure VMware Solution-privécloud implementeren in Azure

Met Azure VMware Solution kunt u een vSphere-cluster in Azure implementeren. De minimale initiële implementatie is drie hosts. Extra hosts kunnen een voor een worden toegevoegd, maximaal 16 hosts per cluster. 

Met Azure VMware Solution kunt u bij het starten uw privécloud niet met uw on-premises vCenter beheren. Daarom moet u aanvullende configuratie uitvoeren. Deze procedures en gerelateerde vereisten worden behandeld in deze zelfstudie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure VMware Solution-privécloud maken
> * Controleer of de privécloud is geïmplementeerd

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- De juiste beheerdersrechten en machtigingen voor het maken van een privécloud.
- Zorg ervoor dat de juiste netwerken zijn geconfigureerd zoals beschreven in [ Zelfstudie: Controlelijst voor netwerken](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>De resourceprovider registreren

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>Een privécloud maken

U kunt een Azure VMware Solution-privécloud maken met behulp van [Azure Portal](#azure-portal) of door de [Azure CLI](#azure-cli) te gebruiken.

### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

U kunt ook een Azure VMware Solution-privécloud maken met de Azure CLI met behulp van de Azure Cloud Shell in plaats van met Azure Portal.  Zie [Azure VMware-opdrachten](/cli/azure/ext/vmware/vmware) voor een lijst met opdrachten die u kunt gebruiken met Azure VMware Solution.

#### <a name="open-azure-cloud-shell"></a>Azure Cloud Shell openen

Selecteer **Nu proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/bash](https://shell.azure.com/bash) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op **Enter** om de code uit te voeren.

#### <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht `[az group create](/cli/azure/group)`. Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Een privécloud maken

Geef een naam voor de resourcegroep op en een naam voor de privécloud, een locatie en de grootte van het cluster.

| Eigenschap  | Beschrijving  |
| --------- | ------------ |
| **-g** (Naam van resourcegroep)     | De naam van de resourcegroep voor uw privécloud-resources.        |
| **-n** (Naam van privécloud)     | De naam van uw Azure VMware Solution-privécloud.        |
| **--location**     | De locatie die voor uw privécloud wordt gebruikt.         |
| **--cluster-size**     | De grootte van de cluster. De minimumwaarde is 3.         |
| **--network-block**     | Het netwerkblok voor het CIDR IP-adres dat moet worden gebruikt voor uw privécloud. Het adresblok mag geen overlap vertonen met de in andere virtuele netwerken gebruikte adresblokken die zich in uw abonnement en on-premises netwerken bevinden.        |
| **--sku** | De SKU-waarde: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-an-azure-vmware-solution-private-cloud"></a>Een Azure VMware Solution-privécloud verwijderen

Als u een Azure VMware Solution-privécloud hebt die u niet meer nodig hebt, kunt u deze verwijderen. Een privécloud van Azure VMware Solution bevat een geïsoleerd netwerkdomein, een of meer ingerichte vSphere-clusters op toegewezen serverhosts, en meerdere virtuele machines. Als een privécloud wordt verwijderd, worden alle virtuele machines, hun gegevens en clusters verwijderd. De toegewezen bare-metalhosts worden veilig gewist en geretourneerd naar de gratis pool. Het netwerkdomein dat is ingericht voor de klant wordt verwijderd.  

> [!CAUTION]
> Het verwijderen van de privécloud is een onomkeerbare bewerking. Zodra de privécloud is verwijderd, kunnen de gegevens niet worden hersteld. Dit komt doordat alle actieve workloads en onderdelen worden beëindigd en alle privécloudgegevens en configuratie-instellingen, met inbegrip van openbare IP-adressen, worden vernietigd.

### <a name="prerequisites"></a>Vereisten

Zodra een privécloud is verwijderd, is er geen manier om de virtuele machines en de bijbehorende gegevens te herstellen. Als de gegevens van de virtuele machine later vereist zijn, moet de beheerder eerst een back-up van alle gegevens maken voordat de privécloud wordt verwijderd.

### <a name="steps-to-delete-an-azure-vmware-solution-private-cloud"></a>Stappen om een Azure VMware Solution-privécloud te verwijderen

1. Open de pagina van Azure VMware Solutions in de Azure Portal.

2. Selecteer de privécloud die u wilt verwijderen.
 
3. Voer de naam van de privécloud in en selecteer **Ja**. Binnen een paar uur wordt het verwijderingsproces voltooid.  

## <a name="azure-vmware-commands"></a>Azure VMware-opdrachten

Zie [Azure VMware-opdrachten](/cli/azure/ext/vmware/vmware) voor een lijst met opdrachten die u kunt gebruiken met Azure VMware Solution.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een Azure VMware Solution-privécloud maken
> * Controleer of de privécloud is geïmplementeerd
> * Een Azure VMware Solution-privécloud verwijderen

Ga door naar de volgende zelfstudie voor informatie over het maken van een jumpbox. U gebruikt de jumpbox om verbinding te maken met uw omgeving, zodat u uw privécloud lokaal kunt beheren.


> [!div class="nextstepaction"]
> [Toegang tot een privécloud van Azure VMware Solution](tutorial-access-private-cloud.md)