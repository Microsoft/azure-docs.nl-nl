---
title: Azure Resource Manager-sjablonen
description: Azure Resource Manager sjablonen voor het gebruik van Azure Site Recovery-functies.
services: site-recovery
author: rishjai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2021
ms.author: rishjai
ms.openlocfilehash: 0477045ac48ee2746e3d6a1dd95051673412ffee
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551258"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Azure Resource Manager sjablonen voor Azure Site Recovery

De volgende tabel bevat koppelingen naar Azure Resource Manager sjablonen voor het gebruik van Azure Site Recovery-functies.

| Template | Beschrijving |
|---|---|
|**Azure naar Azure** | |
| [Een Recovery Services-kluis maken](https://docs.microsoft.com/azure/site-recovery/quickstart-create-vault-template)| Maak een Recovery Services-kluis. De kluis kan worden gebruikt voor Azure Backup en Azure Site Recovery. |
| [Replicatie inschakelen voor virtuele Azure-machines](https://aka.ms/asr-arm-enable-replication) | Schakel replicatie in voor virtuele Azure-machines met behulp van de bestaande kluis en aangepaste doel instellingen.|
| [Failover activeren en opnieuw beveiligen](https://aka.ms/asr-arm-failover-reprotect) | Een failover activeren en de bewerking opnieuw beveiligen voor een set virtuele machines van Azure. |
| [Een end-to-end-DR-stroom uitvoeren voor Azure-Vm's](https://aka.ms/asr-arm-e2e-flow) | Start een volledige end-to-end herstel stroom voor nood gevallen (Schakel replicatie en failover in en voer + failback en opnieuw beveiligen) uit voor Azure-Vm's, ook wel 540 ° flow genoemd.|
|   |   |