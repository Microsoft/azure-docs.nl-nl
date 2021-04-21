---
title: Diagnostische bewaking van Azure voor Azure Attestation
description: Diagnostische bewaking van Azure voor Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b0cd0402348e4aa45b291f30b677fc9e4bbdb98
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833630"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Diagnostische gegevens instellen met een Trusted Platform Module -eindpunt (TPM) van Azure Attestation

Dit artikel helpt u bij het maken en configureren van diagnostische instellingen voor het verzenden van metrische platformgegevens en platformlogboeken naar verschillende bestemmingen. [Platformlogboeken](/azure/azure-monitor/platform/platform-logs-overview) in Azure, waaronder het Azure-activiteitenlogboek en de resourcelogboeken, bieden gedetailleerde diagnostische en controle-informatie voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn. [Metrische platformgegevens](/azure/azure-monitor/platform/data-platform-metrics) worden standaard verzameld en worden opgeslagen in de Azure Monitor Metrics-database.

Voordat u begint, moet u ervoor zorgen dat u [de Azure Attestation hebt Azure PowerShell.](quickstart-powershell.md)

De Trusted Platform Module -eindpuntservice (TPM) is ingeschakeld in de diagnostische instellingen en kan worden gebruikt om de activiteit te bewaken. Stel [Azure Monitoring in](/azure/azure-monitor/overview) voor het TPM-service-eindpunt met behulp van de volgende code.

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

Activiteitenlogboeken staan in **de sectie Containers** van het opslagaccount. Zie Resourcelogboeken van [een Azure-resource verzamelen en](/azure/azure-monitor/learn/tutorial-resource-logs)analyseren voor meer informatie.
