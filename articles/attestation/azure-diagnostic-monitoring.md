---
title: Diagnostische bewaking van Azure voor Azure Attestation
description: Diagnostische bewaking van Azure voor Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d2773be4bc67e125c18d5d38c951685e4f4fceaf
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168345"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Diagnostische gegevens instellen met een Trusted Platform Module-eind punt (TPM) van Azure Attestation

Dit artikel helpt u bij het maken en configureren van diagnostische instellingen voor het verzenden van platform metrieken en platform logboeken naar verschillende bestemmingen. [Platform logboeken](/azure/azure-monitor/platform/platform-logs-overview) in azure, met inbegrip van het Azure-activiteiten logboek en de resource logboeken, bieden uitgebreide diagnostische gegevens en controle-informatie voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn. De [metrische gegevens](/azure/azure-monitor/platform/data-platform-metrics) van het platform worden standaard verzameld en worden opgeslagen in de data base met Azure monitor metrische gegevens.

Voordat u begint, moet u ervoor zorgen dat u [Azure Attestation met Azure PowerShell hebt ingesteld](quickstart-powershell.md).

De eindpunt service Trusted Platform Module (TPM) is ingeschakeld in de diagnostische instellingen en kan worden gebruikt om activiteiten te bewaken. Stel [Azure-controle](/azure/azure-monitor/overview) in voor het TPM-service-eind punt met behulp van de volgende code.

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

Activiteiten logboeken bevinden zich in de sectie **containers** van het opslag account. Zie [resource logboeken van een Azure-resource verzamelen en analyseren](/azure/azure-monitor/learn/tutorial-resource-logs)voor meer informatie.
