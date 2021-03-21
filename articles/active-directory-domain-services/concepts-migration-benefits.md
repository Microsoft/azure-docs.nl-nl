---
title: Voor delen van de migratie van een klassieke implementatie in Azure AD Domain Services | Microsoft Docs
description: Meer informatie over de voor delen van het migreren van een klassieke implementatie van Azure Active Directory Domain Services naar het Resource Manager-implementatie model
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: justinha
ms.openlocfilehash: 8cc5f5ebf389d35df02474d0561dc7827cde4d0b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96620083"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>De voor delen van de migratie van het klassieke naar het Resource Manager-implementatie model in Azure Active Directory Domain Services

Met Azure Active Directory Domain Services (Azure AD DS) kunt u een bestaand beheerd domein migreren dat gebruikmaakt van het klassieke implementatie model naar het Resource Manager-implementatie model. Azure AD DS beheerde domeinen die gebruikmaken van het Resource Manager-implementatie model bieden extra functies, zoals een verfijnd wachtwoord beleid, audit logboeken en beveiliging tegen account vergrendeling.

In dit artikel vindt u een overzicht van de voor delen voor migratie. Zie [Azure AD Domain Services migreren van het klassieke virtuele netwerk model naar Resource Manager][howto-migrate]om aan de slag te gaan.

> [!NOTE]
> In 2017 is Azure AD Domain Services beschikbaar voor de host in een Azure Resource Manager netwerk. Sindsdien hebben we een veiligere service met de moderne mogelijkheden van Azure Resource Manager kunnen bouwen. Omdat Azure Resource Manager implementaties volledig worden vervangen door klassieke implementaties, worden Azure AD DS-implementaties met een klassiek virtueel netwerk ingetrokken op 1 maart 2023.
>
> Zie voor meer informatie de [officiële afschaffing melding](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="migration-benefits"></a>Migratie voordelen

Het migratie proces haalt een bestaand beheerd domein op dat gebruikmaakt van het klassieke implementatie model en verplaatst het Resource Manager-implementatie model. Wanneer u een beheerd domein migreert van het klassieke naar het Resource Manager-implementatie model, voor komt u dat de computers opnieuw lid moeten worden van het beheerde domein of het beheerde domein verwijderen en een volledig nieuwe maken. Vm's blijven aan het eind van het migratie proces lid worden van het beheerde domein.

Na de migratie biedt Azure AD DS veel functies die alleen beschikbaar zijn voor domeinen die gebruikmaken van het Resource Manager-implementatie model, zoals de volgende:

* [Ondersteuning van nauw keurig wachtwoord beleid][password-policy].
* Snellere synchronisatie snelheden tussen Azure AD en Azure AD Domain Services.
* Twee nieuwe [kenmerken die worden gesynchroniseerd vanuit Azure AD][attributes]  -  *Manager* en *employeeID*.
* Toegang tot hogere domein controllers met een hoge voeding wanneer u [de SKU bijwerkt][skus].
* AD-account vergrendelings beveiliging.
* [E-mail meldingen voor waarschuwingen voor uw beheerde domein][email-alerts].
* [Gebruik Azure-werkmappen en Azure monitor om audit logboeken en aanmeldings activiteiten weer te geven][workbooks].
* In ondersteunde regio's, [Azure-beschikbaarheidszones][availability-zones].
* Integraties met andere Azure-producten, zoals [Azure files][azure-files], [HD Insights][hd-insights]en [virtuele Windows-bureau blad][wvd].
* Ondersteuning heeft toegang tot meer telemetrie en kan helpen om effectiever problemen op te lossen.
* Versleuteling bij rest met behulp van [Azure Managed disks][managed-disks] voor de gegevens op de beheerde domein controllers.

Beheerde domeinen die gebruikmaken van een Resource Manager-implementatie model helpen u up-to-date te blijven met de nieuwste nieuwe functies. Er zijn geen nieuwe functies beschikbaar voor beheerde domeinen die gebruikmaken van het klassieke implementatie model.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure AD Domain Services migreren van het klassieke virtuele netwerk model naar Resource Manager][howto-migrate]om aan de slag te gaan.

<!-- LINKS - INTERNAL -->
[password-policy]: password-policy.md
[skus]: change-sku.md
[email-alerts]: notifications.md
[workbooks]: use-azure-monitor-workbooks.md
[azure-files]: ../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md
[hd-insights]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[wvd]: ../virtual-desktop/overview.md
[availability-zones]: ../availability-zones/az-overview.md
[howto-migrate]: migrate-from-classic-vnet.md
[attributes]: synchronization.md#attribute-synchronization-and-mapping-to-azure-ad-ds
[managed-disks]: ../virtual-machines/managed-disks-overview.md
