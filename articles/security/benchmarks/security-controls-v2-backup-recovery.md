---
title: Azure Security Bench Mark v2-back-up en herstel
description: Back-up en herstel van Azure Security Bench Mark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 1ed477c6cf069dbb402e5bda9c1f48f7bc208f18
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698798"
---
# <a name="security-control-v2-backup-and-recovery"></a>Beveiligings controle v2: back-up en herstel

Back-up en herstel bestrijkt de controles om ervoor te zorgen dat de gegevens en configuratie back-ups in de verschillende service lagen worden uitgevoerd, gevalideerd en beveiligd.

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: controleren op regel matige automatische back-ups

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

Zorg ervoor dat u een back-up van systemen en gegevens maakt om de bedrijfs continuïteit na een onverwachte gebeurtenis te hand haven. Dit moet worden gedefinieerd op basis van de doel stellingen voor herstel punt doelstelling (RPO) en de beoogde herstel tijd (RTO).

Schakel Azure Backup in en configureer de back-upbron (bijvoorbeeld Azure Vm's, SQL Server, HANA-data bases of bestands shares), evenals de gewenste frequentie en retentie periode.  

Voor een hoger beveiligings niveau kunt u geografisch redundante opslag optie inschakelen om back-upgegevens naar een secundaire regio te repliceren en te herstellen met behulp van een herstel bewerking voor meerdere regio's.

- [Bedrijfscontinuïteit en herstel na noodgevallen](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [Azure Backup inschakelen](../../backup/index.yml)

- [Het terugzetten van meerdere regio's inschakelen](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beleid en standaarden](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Incidentvoorbereiding](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: back-upgegevens versleutelen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| BR-2 | 10,2 | CP-9 |

Zorg ervoor dat uw back-ups worden beschermd tegen aanvallen. Hierbij moet de back-ups worden versleuteld om te worden beveiligd tegen verlies van vertrouwelijkheid.   

Voor on-premises back-ups met Azure Backup, wordt versleuteling op rest aangeboden met de wachtwoordzin die u opgeeft. Voor reguliere back-ups van Azure-Services worden back-upgegevens automatisch versleuteld met door Azure-platforms beheerde sleutels. U kunt ervoor kiezen om de back-ups te versleutelen met de door de klant beheerde sleutel. Zorg er in dit geval voor dat deze door de klant beheerde sleutel in de sleutel kluis zich ook in het back-upbereik bevindt. 

Gebruik Azure op rollen gebaseerd toegangs beheer in Azure Backup, Azure Key Vault of andere bronnen voor het beveiligen van back-ups en door de klant beheerde sleutels. Daarnaast kunt u geavanceerde beveiligings functies inschakelen om MFA te vereisen voordat back-ups kunnen worden gewijzigd of verwijderd.

- [Overzicht van beveiligings functies in Azure Backup](../../backup/security-overview.md)

- [Versleuteling van back-upgegevens met door de klant beheerde sleutels](../../backup/encryption-at-rest-with-cmk.md) 

- [Back-ups maken van Key Vault sleutels in azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

- [Beveiligings functies voor het beveiligen van hybride back-ups van aanvallen](../../backup/backup-azure-security-feature.md#prevent-attacks)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Incidentvoorbereiding](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: alle back-ups valideren, inclusief door de klant beheerde sleutels

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| BR-3 | 10,3 | CP-4, CP-9 |

Maak regel matig gegevens herstel van uw back-up. Zorg ervoor dat u een back-up van door de klant beheerde sleutels kunt herstellen.

- [Bestanden herstellen vanuit back-up van Azure virtual machine](../../backup/backup-azure-restore-files-from-vm.md)

- [Key Vault sleutels herstellen in azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Incidentvoorbereiding](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: risico op verloren sleutels beperken

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| BR-4 | 10.4 | CP-9 |

Zorg ervoor dat u maat regelen hebt om te voor komen en te herstellen van het verlies van sleutels. Schakel de beveiliging van zacht verwijderen en opschonen in Azure Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.  

- [Zacht verwijderen en beveiliging opschonen inschakelen in Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsarchitectuur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Incidentvoorbereiding](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Gegevens beveiliging](/azure/cloud-adoption-framework/organize/cloud-security-data-security)