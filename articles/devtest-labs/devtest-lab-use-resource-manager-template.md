---
title: De Azure Resource Manager-sjabloon van een virtuele machine weer geven en gebruiken
description: Meer informatie over het gebruik van de Azure Resource Manager-sjabloon van een virtuele machine om andere Vm's te maken
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fb7cb38dfb2c1664017f0e342fefecdd464df45f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696002"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Virtuele machines maken met behulp van een Azure Resource Manager sjabloon 

Wanneer u een virtuele machine (VM) maakt in DevTest Labs via de [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), kunt u de Azure Resource Manager sjabloon bekijken voordat u de VM opslaat. De sjabloon kan vervolgens worden gebruikt als basis om meer Lab-Vm's te maken met dezelfde instellingen.

In dit artikel worden meerdere vm's vergeleken met single-VM Resource Manager-sjablonen beschreven en wordt uitgelegd hoe u een sjabloon kunt weer geven en opslaan bij het maken van een virtuele machine.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Meerdere VM'S versus Resource Manager-sjablonen met één VM
Er zijn twee manieren om Vm's in DevTest Labs te maken met behulp van een resource manager-sjabloon: richt de micro soft. DevTestLab/Labs/informatie-resource in of richt de resource micro soft. Compute/informatie in. Deze worden gebruikt in verschillende scenario's en vereisen verschillende machtigingen.

- Resource Manager-sjablonen die gebruikmaken van het resource type micro soft. DevTestLab/Labs/informatie (zoals gedeclareerd in de eigenschap ' resource ' in de sjabloon), kunnen afzonderlijke Lab-Vm's inrichten. Elke VM wordt vervolgens als één item weer gegeven in de lijst met virtuele machines van DevTest Labs:

   ![Scherm opname van de lijst met virtuele machines als afzonderlijke items in de lijst met virtuele DevTest Labs-machines.](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Dit type resource manager-sjabloon kan worden ingericht via de Azure PowerShell opdracht **New-AzResourceGroupDeployment** of via de Azure cli-opdracht **AZ Deployment Group Create**. Hiervoor zijn beheerders machtigingen vereist, zodat gebruikers die zijn toegewezen met een gebruikersrol van DevTest Labs de implementatie niet kunnen uitvoeren. 

- Resource Manager-sjablonen die gebruikmaken van het resource type micro soft. Compute/informatie kunnen meerdere virtuele machines inrichten als één omgeving in de lijst met virtuele machines van DevTest Labs:

   ![Lijst met Vm's als afzonderlijke items in de lijst met virtuele machines van DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Vm's in dezelfde omgeving kunnen samen worden beheerd en dezelfde levens cyclus delen. Gebruikers die zijn toegewezen met een gebruikersrol van DevTest Labs kunnen omgevingen maken met behulp van deze sjablonen, zolang de beheerder het lab op die manier heeft geconfigureerd.

In de rest van dit artikel worden Resource Manager-sjablonen beschreven die gebruikmaken van micro soft. DevTestLab/Labs/informatie. Deze worden gebruikt door Lab-beheerders voor het automatiseren van het maken van Lab-VM'S (bijvoorbeeld claim bare Vm's) of het genereren van gouden installatie kopieën (bijvoorbeeld image Factory).

[Aanbevolen procedures voor het maken van Azure Resource Manager sjablonen](../azure-resource-manager/templates/template-best-practices.md) bieden tal van richt lijnen en suggesties om u te helpen bij het maken van Azure Resource Manager sjablonen die betrouwbaar en gemakkelijk te gebruiken zijn.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>De Resource Manager-sjabloon van een virtuele machine weer geven en opslaan
1. Volg de stappen bij het [maken van uw eerste vm in een Lab](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) om te beginnen met het maken van een virtuele machine.
1. Voer de vereiste gegevens voor uw virtuele machine in en Voeg eventuele artefacten toe die u voor deze VM wilt.
1. Ga naar het tabblad **Geavanceerde instellingen** . 
1. Klik onder aan het venster instellingen configureren op ARM- **sjabloon weer geven**.
1. Kopieer de Resource Manager-sjabloon en sla deze op om later een andere virtuele machine te maken.

   ![Resource Manager-sjabloon om op te slaan voor later gebruik](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Nadat u de Resource Manager-sjabloon hebt opgeslagen, moet u de sectie para meters van de sjabloon bijwerken voordat u deze kunt gebruiken. U kunt een parameter.jsmaken op waarmee alleen de para meters worden aangepast, buiten het huidige resource manager-sjabloon. 

![Para meters aanpassen met behulp van een JSON-bestand](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

De Resource Manager-sjabloon is nu klaar voor gebruik om [een virtuele machine te maken](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Verval datum instellen
In scenario's zoals training, demo's en proef versies kunt u virtuele machines maken en deze automatisch na een vaste duur verwijderen, zodat u geen onnodige kosten hebt. U kunt een Lab-VM met een verval datum maken door de eigenschap **expirationDate** voor de virtuele machine op te geven. Bekijk dezelfde resource manager-sjabloon in [onze github-opslag plaats](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration).



### <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [maken van omgevingen met meerdere vm's met Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md).
* [Een resource manager-sjabloon implementeren om een VM te maken](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Verken meer Quick Start-sjablonen voor DevTest Labs Automation van de [open bare DevTest Labs github opslag plaats](https://github.com/Azure/azure-quickstart-templates).
