---
title: Versleuteling in Azure Backup
description: Meer informatie over hoe versleutelingsfuncties in Azure Backup u helpen uw back-upgegevens te beveiligen en te voldoen aan de beveiligingsbehoeften van uw bedrijf.
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: 28d165ccc8a966091a96fc433660899d8eef1595
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518470"
---
# <a name="encryption-in-azure-backup"></a>Versleuteling in Azure Backup

Al uw back-upgegevens worden automatisch versleuteld wanneer ze in de cloud worden opgeslagen met behulp van Azure Storage versleuteling, waardoor u kunt voldoen aan uw beveiligings- en nalevingsverplichtingen. Deze data-at-rest wordt versleuteld met 256-bits AES-versleuteling, een van de sterkste blokversleutelingen die beschikbaar is en voldoet aan FIPS 140-2. Naast versleuteling-at-rest worden al uw back-upgegevens die worden overgedragen via HTTPS overgedragen. Deze blijft altijd in het backbone-netwerk van Azure.

## <a name="levels-of-encryption-in-azure-backup"></a>Versleutelingsniveaus in Azure Backup

Azure Backup omvat versleuteling op twee niveaus:

- **Versleuteling van gegevens in de Recovery Services-kluis**
  - **Met behulp van door het platform beheerde** sleutels: standaard worden al uw gegevens versleuteld met behulp van door het platform beheerde sleutels. U hoeft geen expliciete actie van uw kant uit te voeren om deze versleuteling in teschakelen. Dit geldt voor alle workloads van uw Recovery Services-kluis.
  - **Door de klant beheerde sleutels gebruiken:** wanneer u een back-up maakt van uw Azure Virtual Machines, kunt u ervoor kiezen om uw gegevens te versleutelen met behulp van versleutelingssleutels die eigendom zijn van en worden beheerd door u. Azure Backup kunt u uw RSA-sleutels gebruiken die zijn opgeslagen in de Azure Key Vault voor het versleutelen van uw back-ups. De versleutelingssleutel die wordt gebruikt voor het versleutelen van back-ups, kan verschillen van de versleutelingssleutel die wordt gebruikt voor de bron. De gegevens worden beveiligd met een op AES 256 gebaseerde gegevensversleutelingssleutel (DEK), die op zijn beurt wordt beveiligd met uw sleutels. Dit geeft u volledige controle over de gegevens en de sleutels. Als u versleuteling wilt toestaan, moet u de Recovery Services-kluis toegang verlenen tot de versleutelingssleutel in de Azure Key Vault. U kunt de sleutel uitschakelen of de toegang zo nodig intrekken. U moet echter versleuteling inschakelen met behulp van uw sleutels voordat u items naar de kluis probeert te beveiligen. [Klik hier voor meer informatie](encryption-at-rest-with-cmk.md).
  - **Versleuteling** op infrastructuurniveau: naast het versleutelen van uw gegevens in de Recovery Services-kluis met behulp van door de klant beheerde sleutels, kunt u er ook voor kiezen om een extra versleutelingslaag te configureren in de opslaginfrastructuur. Deze infrastructuurversleuteling wordt beheerd door het platform. Samen met versleuteling-at-rest met behulp van door de klant beheerde sleutels, kunt u uw back-upgegevens in twee lagen versleutelen. Infrastructuurversleuteling kan alleen worden geconfigureerd als u er eerst voor kiest om uw eigen sleutels te gebruiken voor versleuteling in rust. Infrastructuurversleuteling maakt gebruik van door het platform beheerde sleutels voor het versleutelen van gegevens.
- **Versleuteling die specifiek is voor de workload waar een back-up van wordt maken**  
  - **Back-up van virtuele Azure-machines:** Azure Backup ondersteunt [back-ups](../virtual-machines/disk-encryption.md#customer-managed-keys) van virtuele machines met schijven die zijn versleuteld met behulp van door het [platform](../virtual-machines/disk-encryption.md#platform-managed-keys)beheerde sleutels, evenals door de klant beheerde sleutels die eigendom zijn van en door u worden beheerd. Daarnaast kunt u ook een back-up maken van uw virtuele Azure-machines met hun besturingssysteem of gegevensschijven die zijn versleuteld [met behulp van Azure Disk Encryption](backup-azure-vms-encryption.md#encryption-support-using-ade). ADE maakt gebruik van BitLocker voor Windows-VM's en DM-Crypt voor linux-VM's om versleuteling in gast uit te voeren.

>[!NOTE]
>Infrastructuurversleuteling is momenteel in beperkte preview en is alleen beschikbaar in de regio's US - oost, US - west2, US - zuid-centraal, US Gov - Arizona en US GOV - Virginia. Als u de functie in een van deze regio's wilt gebruiken, vult u dit [formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) in en stuur ons een e-mail op [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor inactieve gegevens](../storage/common/storage-service-encryption.md)
- [Azure Backup veelgestelde](/backup-azure-backup-faq.yml#encryption) vragen over versleuteling