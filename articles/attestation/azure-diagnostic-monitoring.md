---
title: Diagnostische bewaking van Azure - Azure Attestation
description: Diagnostische bewaking van Azure voor Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 40bc76f839cf6757b8f874112504249e611c3e1a
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98605913"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Diagnostische gegevens instellen met Trusted Platform Module (TPM)-eindpunt van Azure Attestation

[Platformlogboeken](/azure/azure-monitor/platform/platform-logs-overview) in Azure, inclusief het Azure-activiteitenlogboek en de Azure-resourcelogboeken, bieden gedetailleerde diagnose- en controlegegevens voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn. [Metrische platformgegevens](/azure/azure-monitor/platform/data-platform-metrics) worden standaard verzameld en gewoonlijk opgeslagen in de database met metrische gegevens van Azure Monitor. In dit artikel vindt u informatie over het maken en configureren van diagnostische instellingen voor het verzenden van metrische platformgegevens en platformlogboeken naar verschillende bestemmingen. 

De TPM-eindpuntservice is ingeschakeld met de diagnostische instelling en kan worden gebruikt om activiteiten te bewaken. Als u [Azure-bewaking](/azure/azure-monitor/overview) wilt instellen voor het TPM-service-eindpunt met behulp van PowerShell, volgt u de onderstaande stappen. 

Azure Attestation-service instellen. 

[Azure Attestation instellen met behulp van Microsoft Azure PowerShell](/azure/attestation/quickstart-powershell#:~:text=%20Quickstart%3A%20Set%20up%20Azure%20Attestation%20with%20Azure,Register%20Microsoft.Attestation%20resource%20provider.%20Register%20the...%20More%20)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
De activiteitenlogboeken vindt u in de sectie Containers van het opslagaccount. Gedetailleerde informatie kunt u vinden op [Resourcelogboeken verzamelen van een Azure-resource en analyseren met Azure Monitor - Azure Monitor](/azure/azure-monitor/learn/tutorial-resource-logs)
