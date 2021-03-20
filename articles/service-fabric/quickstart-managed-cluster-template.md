---
title: Een beheerde Service Fabric-cluster (preview) implementeren met behulp van Azure Resource Manager
description: Ontdek hoe u een beheerde Service Fabric-cluster kunt maken met een Azure Resource Manager-sjabloon
ms.topic: quickstart
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 079e8e3e39bd306332abb43fa2ae1f8cc04e305b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91410391"
---
# <a name="quickstart-deploy-a-service-fabric-managed-cluster-preview-with-an-azure-resource-manager-template"></a>Quickstart: Een beheerde Service Fabric-cluster (preview) implementeren met behulp van een Azure Resource Manager-sjabloon

Beheerde Service Fabric clusters zijn een evolutie van het Azure Service Fabric-clusterresourcemodel dat uw implementatie en clusterbeheer stroomlijnt. Beheerde Service Fabric-clusters zijn een volledig ingekapselde resource waarmee u één Service Fabric-clusterbron kunt implementeren in plaats van alle onderliggende resources die deel uitmaken van een Service Fabric-cluster. In dit artikel wordt beschreven hoe u een beheerde Service Fabric-testcluster in Azure implementeert met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon).

De basis-SKU-cluster met drie knooppunten die in deze zelfstudie wordt geïmplementeerd, is alleen bedoeld voor instructiedoeleinden (en geen productieve workloads). Zie [Beheerde Service Fabric-cluster-SKU's](overview-managed-cluster.md#service-fabric-managed-cluster-skus) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Voor u met deze snelstart begint:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Samples - Service Fabric-clustersjablonen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Basic-SKU-1-NT).

## <a name="create-a-client-certificate"></a>Een clientcertificaat maken

Beheerde Service Fabric-clusters gebruiken een clientcertificaat als sleutel voor toegangsbeheer. Als u al een clientcertificaat hebt dat u wilt gebruiken voor toegangsbeheer voor uw cluster, dan kunt u deze stap overslaan.

Als u een nieuw clientcertificaat wilt maken, volg dan de stappen in [Een certificaat instellen en ophalen vanuit Azure Key Vault](../key-vault/certificates/quick-create-portal.md).

Noteer de vingerafdruk van het certificaat, aangezien deze nodig is om het sjabloon in de volgende stap te implementeren.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen.

      [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fservice-fabric-cluster-templates%2Fmaster%2FSF-Managed-Basic-SKU-1-NT%2Fazuredeploy.json)

2. Typ of selecteer de volgende waarden

    Geef voor deze snelstart uw eigen waarden op voor de volgende sjabloonparameters:

    * **Abonnement**: Selecteer een Azure-abonnement.
    * **Resourcegroep:** Selecteer **Nieuw maken**. Voer een unieke naam in voor de resourcegroep, zoals *myResourceGroup*, en kies vervolgens **OK**.
    * **Locatie**: Selecteer een locatie, zoals **VS-Oost2**. Voorbeelden van ondersteunde regio's voor beheerde Service Fabric Managed-clusters zijn onder andere `centraluseuap`, `eastus2euap`, `eastasia`, `northeurope`, `westcentralus` en `eastus2`.
    * **Clusternaam**: Voer een unieke naam in voor uw cluster, zoals *mysfcluster*.
    * **Gebruikersnaam van beheerder**: Voer een naam in voor de beheerder dat moet worden gebruikt voor RDP op de onderliggende virtuele machines in het cluster.
    * **Wachtwoord voor beheerder**: Voer een wachtwoord in voor de beheerder dat moet worden gebruikt voor RDP op de onderliggende virtuele machines in het cluster.
    * **Vingerafdruk van het clientcertificaat**: Geef de vingerafdruk van het clientcertificaat op die u wilt gebruiken om toegang te krijgen tot uw cluster. Als u geen certificaat hebt, volgt u [een certificaat instellen en ophalen](../key-vault/certificates/quick-create-portal.md) om een zelfondertekend certificaat te maken.
    * **Naam van knooppunttype**: Voer een unieke naam in voor het knooppunttype, bijvoorbeeld *nt1*.
    * **Ik ga akkoord met de bovenstaande voorwaarden**: schakel dit selectievakje in om akkoord te gaan. 

3. Selecteer **Aankoop**.

4. Het duurt enkele minuten voordat uw beheerde Service Fabric-cluster is geïmplementeerd. Wacht tot de implementatie voltooid is voordat u verdergaat met de volgende stappen.

## <a name="validate-the-deployment"></a>De implementatie valideren

### <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Zodra de implementatie is voltooid, gaat u naar de Service Fabric Explorer-waarde in de uitvoer en opent u het adres in een webbrowser om uw cluster in Service Fabric Explorer te bekijken. Wanneer u wordt gevraagd om een certificaat, gebruikt u het certificaat waarvoor de vingerafdruk van de client is opgenomen in het sjabloon.

> [!NOTE]
> U kunt de uitvoer van de implementatie vinden in het Azure-portaal onder het tablad met implementaties van resourcegroepen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resourcegroep voor uw beheerde Service Fabric-cluster. De resourcegroep verwijderen via de portal:

1. Typ de naam van uw resourcegroep in het *zoekvak* bovenaan de portal. Wanneer u de in deze snelstart gebruikte resourcegroep in de zoekresultaten ziet, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Typ in het vak **TYP DE NAAM VAN DE RESOURCEGROEP** de naam van de resourcegroep en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een beheerde Service Fabric-cluster geïmplementeerd. Zie voor meer informatie over het schalen van een cluster:

> [!div class="nextstepaction"]
> [Uitbreiden van een beheerd Service Fabric-cluster](tutorial-managed-cluster-scale.md)
