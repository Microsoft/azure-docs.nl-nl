---
title: Azure Resource Manager-sjablonen
description: Azure Resource Manager-sjablonen voor gebruik bij Recovery Services-kluizen en Azure Backup-functies
ms.topic: sample
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: a4c2f444cb821f7979571b9d777895a59450e7c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96309576"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Azure Resource Manager-sjablonen voor Azure Backup

De volgende tabel bevat koppelingen naar Azure Resource Manager-sjablonen voor gebruik bij Recovery Services-kluizen en Azure Backup-functies. Zie [Microsoft.RecoveryServices resource types](/azure/templates/microsoft.recoveryservices/allversions) (Microsoft.RecoveryServices resourcetypen) voor informatie over de JSON-syntaxis en eigenschappen.

| Template | Beschrijving |
|---|---|
|**Recovery Services-kluis** | |
| [Een Recovery Services-kluis maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Maak een Recovery Services-kluis. De kluis kan worden gebruikt voor Azure Backup en Azure Site Recovery. |
|**Back-up maken van virtuele machines**| |
| [Back-ups maken van Resource Manager-VM's](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Gebruik de bestaande Recovery Services-kluis en het back-upbeleid om back-ups te maken van Resource Manager-virtuele machines in dezelfde resourcegroep.|
| [Back-ups maken van IaaS-VM's naar een Recovery Services-kluis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Sjabloon voor het maken van back-ups van klassieke en Resource Manager-virtuele machines. |
| [Beleid voor wekelijkse back-up voor IaaS VM's maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Met de sjabloon wordt een Recovery Services-kluis en een beleid voor wekelijkse back-ups gemaakt dat wordt gebruikt voor het maken van back-ups van klassieke VM's en Resource Manager-virtuele machines.|
| [Beleid voor dagelijkse back-up voor IaaS VM's maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Met de sjabloon wordt een Recovery Services-kluis en een beleid voor dagelijkse back-ups gemaakt dat wordt gebruikt voor het maken van back-ups van klassieke VM's en Resource Manager-virtuele machines.|
| [Windows Server-VM met back-up implementeren](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | Met de sjabloon wordt een Windows Server-VM en Recovery Services-kluis gemaakt met het standaard back-upbeleid ingeschakeld.|
|**Back-uptaken controleren** |  |
| [Logboeken van Azure Monitor gebruiken met Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Met de sjabloon worden Azure Monitor-logboeken voor Azure Backup geïmplementeerd, waarmee u back-up- en hersteltaken, back-upwaarschuwingen en de cloudopslag in uw Recovery Services-kluizen kunt controleren.|  
|**Een back-up maken van SQL Server op Azure-VM** |  |
| [Een back-up maken van SQL Server op Azure-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | Met de sjabloon wordt een Recovery Services-kluis en een back-upbeleid voor specifieke workloads gemaakt. De virtuele machine wordt geregistreerd bij de Azure Backup-service en de beveiliging wordt op die VM geconfigureerd. Op dit moment werkt dit alleen voor SQL-Galerie-installatiekopieën. |
|**Een back-up maken van Azure-bestandsshares** |  |
| [Een back-up maken van Azure-bestandsshares](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-file-share) | Met deze sjabloon wordt beveiliging geconfigureerd voor een bestaande Azure-bestandsshare door de juiste gegevens op te geven voor de Recovery Services-kluis en het back-upbeleid. Er wordt optioneel een nieuwe Recovery Services-kluis en een nieuw back-upbeleid gemaakt, en het opslagaccount met de bestandsshare wordt geregistreerd bij de Recovery Services-kluis. |
|   |   |
