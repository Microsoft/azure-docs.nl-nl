---
title: Versleuteling in Azure Backup
description: Meer informatie over de versleutelings functies in Azure Backup u helpen uw back-upgegevens te beschermen en te voldoen aan de beveiligings behoeften van uw bedrijf.
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: c9b1b2782a34285ae194f2998a7cd053cf3c0c70
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325672"
---
# <a name="encryption-in-azure-backup"></a>Versleuteling in Azure Backup

Al uw back-upgegevens worden automatisch versleuteld wanneer deze worden opgeslagen in de Cloud met behulp van Azure Storage versleuteling, waarmee u kunt voldoen aan de verplichtingen voor beveiliging en naleving. Deze gegevens in rust worden versleuteld met 256-bits AES-versleuteling, een van de krach tigste blok cijfers die beschikbaar zijn en is compatibel met FIPS 140-2. Naast versleuteling in rust worden al uw back-upgegevens overgebracht via HTTPS. Het blijft altijd op het Azure-backbone-netwerk.

## <a name="levels-of-encryption-in-azure-backup"></a>Coderings niveaus in Azure Backup

Azure Backup omvat versleuteling op twee niveaus:

- **Versleuteling van gegevens in de Recovery Services kluis**
  - **Met platform beheerde sleutels**: standaard worden al uw gegevens versleuteld met sleutels die door het platform worden beheerd. U hoeft geen expliciete actie van uw end uit te voeren om deze versleuteling in te scha kelen. Dit is van toepassing op alle werk belastingen waarvan een back-up wordt gemaakt naar uw Recovery Services kluis.
  - **Met door de klant beheerde sleutels**: wanneer u een back-up maakt van uw Azure-virtual machines, kunt u ervoor kiezen uw gegevens te versleutelen met versleutelings sleutels die eigendom zijn van en worden beheerd door u. Met Azure Backup kunt u uw RSA-sleutels gebruiken die zijn opgeslagen in de Azure Key Vault voor het versleutelen van uw back-ups. De versleutelings sleutel die wordt gebruikt voor het versleutelen van back-ups is mogelijk anders dan die voor de bron. De gegevens worden beveiligd met behulp van een AES 256-gegevens versleutelings sleutel (DEK), die op zijn beurt wordt beveiligd met uw sleutels. Hiermee krijgt u volledige controle over de gegevens en de sleutels. Als u versleuteling wilt toestaan, moet u de Recovery Services kluis toegang verlenen tot de versleutelings sleutel in de Azure Key Vault. U kunt de sleutel uitschakelen of toegang intrekken wanneer dit nodig is. U moet echter versleuteling inschakelen met behulp van uw sleutels voordat u de items in de kluis probeert te beveiligen. Meer [informatie vindt u hier](encryption-at-rest-with-cmk.md).
  - **Versleuteling op infrastructuur niveau**: naast het versleutelen van uw gegevens In de Recovery Services kluis met door de klant beheerde sleutels kunt u er ook voor kiezen om een extra laag versleuteling te configureren voor de opslag infrastructuur. Deze infra structuur versleuteling wordt beheerd door het platform. In combi natie met door de klant beheerde sleutels kunt u de gegevens op de rest versleutelen met twee lagen. Infrastructuur versleuteling kan alleen worden geconfigureerd als u ervoor kiest om uw eigen sleutels te gebruiken voor versleuteling in rust. Infrastructuur versleuteling maakt gebruik van door het platform beheerde sleutels voor het versleutelen van gegevens.
- **Versleuteling specifiek voor de werk belasting waarvan een back-up wordt gemaakt**  
  - **Back-up van Azure virtual machine**: Azure Backup ondersteunt het maken van back-ups van vm's met schijven die zijn versleuteld met door het [platform beheerde sleutels](../virtual-machines/disk-encryption.md#platform-managed-keys), en door de [klant beheerde sleutels](../virtual-machines/disk-encryption.md#customer-managed-keys) die eigendom zijn van en worden beheerd door u. Daarnaast kunt u ook een back-up maken van uw virtuele Azure-machines waarvan het besturings systeem of de gegevens schijven zijn versleuteld met behulp van [Azure Disk Encryption](backup-azure-vms-encryption.md#encryption-support-using-ade). ADE maakt gebruik van BitLocker voor Windows-Vm's en DM-Crypt voor Linux-Vm's om in-gast versleuteling uit te voeren.

>[!NOTE]
>Infrastructuur versleuteling is momenteel in beperkte preview en is alleen beschikbaar in de regio's VS-Oost, VS-West2, VS Zuid-Centraal, US Gov-Arizona en VS GOV Virginia. Als u de functie in een van deze regio's wilt gebruiken, vult u [dit formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) in en e-mail ons op [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor inactieve gegevens](../storage/common/storage-service-encryption.md)
- [Azure backup Veelgestelde vragen](backup-azure-backup-faq.md#encryption) over eventuele vragen over versleuteling