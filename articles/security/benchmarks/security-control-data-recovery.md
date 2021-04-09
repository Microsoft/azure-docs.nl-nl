---
title: Azure-beveiligings beheer-gegevens herstel
description: Herstel van gegevens van Azure-beveiligings beheer
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 5006a592b410685ad808ff01112234bc48f5d267
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728093"
---
# <a name="security-control-data-recovery"></a>Beveiligings beheer: gegevens herstellen

Zorg ervoor dat er regel matig een back-up wordt gemaakt van alle systeem gegevens, configuraties en geheimen.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 9.1 | 10.1 | Klant |

Schakel Azure Backup in en configureer de back-upbron (Azure Vm's, SQL Server of bestands shares), evenals de gewenste frequentie en bewaar periode.

- [Azure Backup inschakelen](../../backup/index.yml)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 9.2 | 10,2 | Klant |

Schakel Azure Backup en doel-VM ('s) in, evenals de gewenste frequentie en retentie perioden. Back-ups van door de klant beheerde sleutels binnen Azure Key Vault.

- [Azure Backup inschakelen](../../backup/index.yml)

- [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 9.3 | 10.3 | Klant |

Zorg ervoor dat het gegevens herstel van inhoud in Azure Backup regel matig wordt uitgevoerd. Herstel van back-ups van door de klant beheerde sleutels testen.

- [Bestanden herstellen vanuit back-up van Azure virtual machine](../../backup/backup-azure-restore-files-from-vm.md)

- [Sleutel kluis sleutels herstellen in azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 9.4 | 10.4 | Klant |

Voor on-premises back-ups wordt versleuteling in rust geboden met de wachtwoordzin die u opgeeft wanneer u een back-up maakt in Azure. Voot Azure-VM’s worden gegevens in rust versleuteld met behulp van SSE (Storage Service Encryption). Gebruik Azure op rollen gebaseerd toegangs beheer voor het beveiligen van back-ups en door de klant beheerde sleutels.  

Schakel Soft-Delete in en verwijder de beveiliging in Key Vault om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.  Als Azure Storage wordt gebruikt voor het opslaan van back-ups, schakelt u de optie voor het tijdelijk verwijderen in om uw gegevens op te slaan en te herstellen wanneer blobs of BLOB-moment opnamen worden verwijderd. 

- [Meer informatie over Azure RBAC](../../role-based-access-control/overview.md)

- [Soft-Delete inschakelen en beveiliging opschonen in Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Voorlopig verwijderen voor Azure Storage-blobs](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)


## <a name="next-steps"></a>Volgende stappen

- Zie het volgende beveiligings beheer:  [respons op incidenten](security-control-incident-response.md)