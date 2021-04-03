---
title: 'Problemen oplossen: door SDK gemaakte werk ruimten kunnen niet worden gestart in Synapse Studio'
description: Stappen voor het oplossen van door SDK gemaakte werk ruimten kunnen niet worden gestart in Synapse Studio
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: workspace
ms.date: 11/24/2020
ms.openlocfilehash: f5d0aae975d476da47510a1f2fd164cbc5f32945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466429"
---
# <a name="troubleshoot-azure-synapse-analytics-workspaces-created-using-sdk"></a>Problemen met Azure Synapse Analytics-werk ruimten die zijn gemaakt met SDK oplossen

Dit artikel bevat stappen voor het oplossen van problemen met het starten van Synapse Studio vanuit een Synapse-werk ruimte die is gemaakt met een Software Development Kit (SDK).


## <a name="prerequisites"></a>Vereisten

- Synapse-werk ruimte gemaakt met SDK

## <a name="workaround"></a>Tijdelijke oplossing

Voer de volgende stappen uit om Synapse Studio vanuit uw door SDK gemaakte werk ruimte te starten: 
  1.    Maak een werkruimte door `az synapse workspace create` uit te voeren.
  2.    Extraheer de id van de beheerde identiteit door `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` uit te voeren.
  3.    De rol voor het overdragen van BLOB-gegevens verlenen aan het account voor beheerde identiteits opslag door uit te voeren ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}.`
  4.    Voeg een firewallregel toe door ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` uit te voeren.

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure Synapse?](../overview-what-is.md)
* [Aan de slag](../get-started.md)
