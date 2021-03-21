---
title: Apache Hadoop clusters maken met behulp van sjablonen-Azure HDInsight
description: Meer informatie over het maken van clusters voor HDInsight met behulp van Resource Manager-sjablonen
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/07/2020
ms.openlocfilehash: 43f9736ce902d4c195d6b07cfdf7011ba9ca0c2c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702739"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Apache Hadoop clusters maken in HDInsight met behulp van Resource Manager-sjablonen

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

In dit artikel leert u verschillende manieren om Azure HDInsight-clusters te maken met behulp van [Azure Resource Manager-sjablonen](../azure-resource-manager/templates/deploy-powershell.md). Voor meer informatie over andere hulpprogram ma's en functies voor het maken van het cluster klikt u op de tab-kiezer boven aan deze pagina. Zie ook methoden voor het [maken van clusters](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="resource-manager-templates"></a>Resource Manager-sjablonen

Met een resource manager-sjabloon kunt u eenvoudig de volgende resources voor uw toepassing maken in een enkele, gecoördineerde bewerking:

* HDInsight-clusters en hun afhankelijke bronnen (zoals het standaard opslag account).
* Andere resources (zoals Azure SQL Database om [Apache Sqoop](https://sqoop.apache.org/)te gebruiken).

In de sjabloon definieert u de resources die nodig zijn voor de toepassing. U geeft ook implementatie parameters op voor invoer waarden voor verschillende omgevingen. De sjabloon bestaat uit JSON en expressies die u gebruikt om waarden voor uw implementatie samen te stellen.

U kunt voor beelden van HDInsight-sjablonen vinden in [Azure Quick](https://azure.microsoft.com/resources/templates/?term=hdinsight)start-sjablonen. Gebruik platformoverschrijdende [Visual Studio-code](https://code.visualstudio.com/#alt-downloads) met de [Resource Manager-extensie](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) of een tekst editor om de sjabloon in een bestand op uw werk station op te slaan.

Voor meer informatie over Resource Manager-sjablonen raadpleegt u de volgende artikelen en voor beelden:

* [Azure Resource Manager sjablonen ontwerpen](../azure-resource-manager/templates/template-syntax.md)
* [Een toepassing implementeren met Azure Resource Manager sjablonen](../azure-resource-manager/templates/deploy-powershell.md)
* Referentie voor sjablonen voor [micro soft. HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions)
* [Sjablonen voor Azure Quick Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Sjablonen genereren

Met Resource Manager kunt u een resource manager-sjabloon uit bestaande resources in uw abonnement exporteren met behulp van verschillende hulpprogram ma's. Deze gegenereerde sjabloon kunt u vervolgens gebruiken om meer te weten te komen over de sjabloonsyntaxis of om desgewenst het hergebruik van uw oplossing te automatiseren. Zie [sjablonen exporteren](../azure-resource-manager/templates/export-template-portal.md)voor meer informatie.

## <a name="deploy-using-the-portal"></a>Implementeren met behulp van de portal

U kunt een resource manager-sjabloon implementeren met behulp van de Azure Portal. Zie [resources implementeren vanuit een aangepaste sjabloon](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)voor meer informatie.

## <a name="deploy-using-powershell"></a>Implementeren met PowerShell

U kunt een resource manager-sjabloon implementeren met behulp van Azure PowerShell. Zie [resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) en een [privé Resource Manager-sjabloon met SAS-token en Azure PowerShell implementeren](../azure-resource-manager/templates/secure-template-with-sas-token.md)voor meer informatie.

## <a name="deploy-using-azure-cli"></a>Implementeren met behulp van Azure CLI

U kunt een resource manager-sjabloon implementeren met behulp van Azure CLI. Zie [resources implementeren met Resource Manager-sjablonen en Azure cli](../azure-resource-manager/templates/deploy-cli.md) en een [persoonlijke Resource Manager-sjabloon implementeren met SAS-token en Azure cli](../azure-resource-manager/templates/secure-template-with-sas-token.md)voor meer informatie.

## <a name="deploy-using-the-rest-api"></a>Implementeren met behulp van de REST API

U kunt een resource manager-sjabloon implementeren met behulp van REST API. Zie [resources implementeren met Resource Manager-sjablonen en resource manager rest API](../azure-resource-manager/templates/deploy-rest.md)voor meer informatie.

## <a name="deploy-with-visual-studio"></a>Implementeren met Visual Studio

 Gebruik Visual Studio om een resource groeps project te maken en te implementeren in azure via de gebruikers interface. U selecteert het type resources dat u wilt toevoegen aan uw project. Deze resources worden automatisch toegevoegd aan de Resource Manager-sjabloon. Het project biedt ook een Power shell-script voor het implementeren van de sjabloon.

Zie voor een inleiding tot het gebruik van Visual Studio met resource groepen [Azure-resource groepen maken en implementeren via Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="troubleshoot"></a>Problemen oplossen

Zie [Vereisten voor toegangsbeheer](hdinsight-hadoop-customize-cluster-linux.md#access-control) als u problemen ondervindt met het maken van HDInsight-clusters.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u verschillende manieren geleerd om een HDInsight-cluster te maken. Lees de volgende artikelen voor meer informatie:

* Zie [Azure Quick](https://azure.microsoft.com/resources/templates/?term=hdinsight)start-sjablonen voor meer aan HDInsight gerelateerde sjablonen.
* Zie [resources implementeren met behulp van .net-bibliotheken en een sjabloon](/previous-versions/azure/virtual-machines/windows/csharp-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor een voor beeld van het implementeren van resources via de .net-client bibliotheek.
* Zie voor een uitgebreid voor beeld van de implementatie van een toepassing micro [services zoals verwacht inrichten en implementeren in azure](../app-service/deploy-complex-application-predictably.md).
* Zie voor instructies over het implementeren van uw oplossing in verschillende omgevingen [Ontwikkeling en testomgevingen in Microsoft Azure](../devtest-labs/devtest-lab-overview.md).
* Zie [ontwerp sjablonen](../azure-resource-manager/templates/template-syntax.md)voor meer informatie over de secties van de sjabloon Azure Resource Manager.
* Zie [sjabloon functies](../azure-resource-manager/templates/template-functions.md)voor een overzicht van de functies die u kunt gebruiken in een Azure Resource Manager sjabloon.