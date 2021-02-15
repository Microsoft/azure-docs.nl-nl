---
title: 'ML Studio (klassiek): Power shell-modules-Azure'
description: Power shell gebruiken voor het maken en beheren van Azure Machine Learning Studio (klassieke) werk ruimten, experimenten, webservices en meer.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 684299d61ba6e9e27e16a162c9f226a7ea3b5f58
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518008"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Power shell-modules voor Azure Machine Learning Studio (klassiek)

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


U kunt met behulp van Power shell-modules uw studio-resources en-assets programmatisch beheren, zoals werk ruimten, gegevens sets en webservices.

U kunt werken met Studio-resources (klassiek) met behulp van drie Power shell-modules:

* [Azure PowerShell AZ](#az-rm) uitgebracht in 2018, bevat alle functionaliteit van AzureRM, maar met verschillende cmdlet-namen
* [AzureRM](#az-rm) uitgebracht in 2016, vervangen door Power shell AZ
* [Azure machine learning Power shell Classic](#classic) uitgebracht in 2016

Hoewel deze Power shell-modules enkele overeenkomsten hebben, zijn deze allemaal ontworpen voor bepaalde scenario's. In dit artikel worden de verschillen tussen de Power shell-modules beschreven en kunt u bepalen welke u wilt kiezen.  

Raadpleeg de [ondersteunings tabel](#support-table) hieronder om te zien welke resources door elke module worden ondersteund. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell AZ en AzureRM

AZ is nu de beoogde Power shell-module voor interactie met Azure en bevat alle eerdere functies van AzureRM. AzureRM blijft problemen met oplossingen ontvangen, maar er worden geen nieuwe cmdlets of functies ontvangen.  AZ en AzureRM beheren oplossingen die zijn geïmplementeerd met het **Azure Resource Manager** -implementatie model. Deze resources zijn onder andere studio (klassieke) werk ruimten en Studio (klassieke) ' nieuwe ' webservices. 

Klassieke Power shell kan worden geïnstalleerd naast AZ of AzureRM voor zowel de resource typen ' nieuw ' als ' klassiek '. Het is echter niet raadzaam AZ en AzureRM tegelijkertijd te installeren. Om te kiezen tussen AZ en AzureRM, raadt micro soft AZ aan voor alle toekomstige implementaties.  Meer informatie over AZ versus AzureRM en het migratie traject in [Inleiding tot de Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

Volg de [installatie-instructies voor Azure AZ](/powershell/azure/install-az-ps)om aan de slag te gaan met AZ.

## <a name="powershell-classic"></a><a name="classic"></a> Klassieke Power shell

Met de klassieke [Power shell-module](https://aka.ms/amlps) van Studio (klassiek) kunt u resources beheren die zijn geïmplementeerd met het **klassieke implementatie model**. Deze resources zijn onder andere studio-(klassieke) gebruikers assets, klassieke webservices en ' klassieke ' webservice-eind punten.

Micro soft raadt u echter aan het Resource Manager-implementatie model te gebruiken voor alle toekomstige resources om de implementatie en het beheer van resources te vereenvoudigen. Zie het artikel over de [implementatie van Azure Resource Manager vs. klassiek](../../azure-resource-manager/management/deployment-models.md) als u meer wilt weten over de implementatie modellen.

Down load het [release pakket](https://github.com/hning86/azuremlps/releases) van github en volg de [instructies voor de installatie](https://github.com/hning86/azuremlps/blob/master/README.md)om aan de slag te gaan met Power shell Classic. In de instructies wordt uitgelegd hoe u de blok kering van de gedownloade/ongecomprimeerde DLL kunt opheffen en vervolgens kunt importeren in uw Power shell-omgeving

Klassieke Power shell kan worden geïnstalleerd naast AZ of AzureRM voor zowel de resource typen ' nieuw ' als ' klassiek '.

## <a name="powershell-support-table"></a><a name="support-table"></a> Power shell-ondersteunings tabel


| Taak | **AZ** |  **Klassieke Power shell** |
| --- | --- | --- |
| Werk ruimten maken/verwijderen | [Resource Manager-sjablonen](./deploy-with-resource-manager-template.md) |  |
| Toezeggings plannen voor werk ruimten beheren | [New-AzMlCommitmentPlan](/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Werkruimte gebruikers beheren |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Webservices beheren | [New-AzMlWebService](/powershell/module/az.machinelearning/new-azmlwebservice) <br>(' nieuwe ' webservices)| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>(' klassieke ' webservices) |
| Webservice-eind punten/-sleutels beheren |  [Get-AzMlWebServiceKey](/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Gebruikers gegevens sets/getrainde modellen beheren| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gebruikers experimenten beheren |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Aangepaste modules beheren | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volledige documentatie deze Power shell-module:
* [Klassieke Power shell](https://aka.ms/amlps)
* [Azure PowerShell Az](/powershell/module/az.machinelearning/#machine_learning)