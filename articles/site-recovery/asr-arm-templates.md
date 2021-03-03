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
ms.openlocfilehash: efd5c1ab3e23348373be76c79d1b9689bc5f4941
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720525"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Azure Resource Manager sjablonen voor Azure Site Recovery

De volgende tabel bevat koppelingen naar Azure Resource Manager sjablonen voor het gebruik van Azure Site Recovery-functies.

| Template | Beschrijving |
|---|---|
|**Azure naar Azure** | |
| [Een Recovery Services-kluis maken](./quickstart-create-vault-template.md)| Maak een Recovery Services-kluis. De kluis kan worden gebruikt voor Azure Backup en Azure Site Recovery. |
| [Replicatie inschakelen voor virtuele Azure-machines](https://aka.ms/asr-arm-enable-replication) | Schakel replicatie in voor virtuele Azure-machines met behulp van de bestaande kluis en aangepaste doel instellingen.|
| [Failover activeren en opnieuw beveiligen](https://aka.ms/asr-arm-failover-reprotect) | Een failover activeren en de bewerking opnieuw beveiligen voor een set virtuele machines van Azure. |
| [Een end-to-end-DR-stroom uitvoeren voor Azure-Vm's](https://aka.ms/asr-arm-e2e-flow) | Start een volledige end-to-end herstel stroom voor nood gevallen (Schakel replicatie en failover in en voer + failback en opnieuw beveiligen) uit voor Azure-Vm's, ook wel 540 ° flow genoemd.|
|   |   |