---
title: Aanbevolen procedures voor het gebruik van Azure Key Vault beheerde HSM
description: In dit document worden enkele van de aanbevolen procedures voor het gebruik van Key Vault beschreven.
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 7a30a7ab6689b602bc9ad4f696a6fe54c80f2151
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90995361"
---
# <a name="best-practices-when-using-managed-hsm"></a>Aanbevolen procedures voor het gebruik van beheerde HSM

## <a name="control-access-to-your-managed-hsm"></a>Toegang tot uw beheerde HSM beheren

Beheerde HSM is een Cloud service die versleutelings sleutels beveiligt. Wanneer deze sleutels gevoelig en zakelijk kritiek zijn, moet u ervoor zorgen dat u de toegang tot uw beheerde Hsm's beveiligt door alleen geautoriseerde toepassingen en gebruikers toe te staan. Dit [artikel](access-control.md) bevat een overzicht van het toegangs model. Hierin worden verificatie en autorisatie en op rollen gebaseerd toegangs beheer uitgelegd.
- Maak een [Azure Active Directory beveiligings groep](../../active-directory/fundamentals/active-directory-manage-groups.md) voor de HSM-beheerders (in plaats van de rol beheerder toe te wijzen aan individuen). Hiermee voor komt u dat ' beheer vergrendeling ' wordt voor het verwijderen van afzonderlijke accounts.
- Vergrendel de toegang tot uw beheer groepen, abonnementen, resource groepen en beheerde Hsm's-gebruik Azure RBAC om de toegang tot uw beheer groepen, abonnementen en resource groepen te beheren
- Roltoewijzingen per sleutel maken met behulp van [beheerde HSM lokale RBAC](access-control.md#data-plane-and-managed-hsm-local-rbac)
- Access principal met minimale bevoegdheden gebruiken om rollen toe te wijzen

## <a name="choose-regions-that-support-availability-zones"></a>Regio's kiezen die beschikbaarheids zones ondersteunen

- Als u de beste hoge Beschik baarheid en zone tolerantie wilt garanderen, kiest u Azure-regio's waar [Beschikbaarheidszones](../../availability-zones/az-overview.md) worden ondersteund. Deze regio's worden weer gegeven als ' Aanbevolen regio's ' in de Azure Portal.

## <a name="backup"></a>Backup

- Zorg ervoor dat u regel matig back-ups van uw HSM onderneemt. U kunt back-ups uitvoeren op het HSM-niveau en voor specifieke sleutels. 

## <a name="turn-on-logging"></a>Logboek registratie inschakelen

- [Schakel logboek registratie in](logging.md) voor uw HSM. Stel ook waarschuwingen in.

## <a name="turn-on-recovery-options"></a>Herstel opties inschakelen

- [Voorlopig verwijderen](../general/soft-delete-overview.md) is standaard ingeschakeld.
- Schakel beveiliging opschonen in als u wilt voor komen dat de HSM wordt verwijderd, zelfs nadat het zacht verwijderen is ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

- Zie [volledige back-ups maken/herstellen](backup-restore.md) voor meer informatie over volledige HSM-back-up/herstel.
- Zie [beheerde HSM-logboek registratie](logging.md) voor meer informatie over het gebruik van Azure monitor voor het configureren van logboek registratie
- Zie [beheerde HSM-sleutels](key-management.md) voor sleutel beheer beheren.
- Zie [Managed HSM Role Management](role-management.md) voor het beheren van roltoewijzingen.
