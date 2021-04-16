---
title: Best practices voor het gebruik Azure Key Vault beheerde HSM
description: In dit document worden enkele van de best practices voor het gebruik van Key Vault
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 9ef3b19e5064c8a88bf80eebf57539be72747fe4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482514"
---
# <a name="best-practices-when-using-managed-hsm"></a>Best practices bij het gebruik van beheerde HSM

## <a name="control-access-to-your-managed-hsm"></a>Toegang tot uw beheerde HSM bepalen

Beheerde HSM is een cloudservice die versleutelingssleutels beschermt. Omdat deze sleutels gevoelig en bedrijfskritiek zijn, moet u de toegang tot uw beheerde HMS's beveiligen door alleen geautoriseerde toepassingen en gebruikers toe te staan. In [dit artikel](access-control.md) vindt u een overzicht van het toegangsmodel. Er wordt uitleg gegeven over verificatie en autorisatie en op rollen gebaseerd toegangsbeheer.
- Maak een [Azure Active Directory beveiligingsgroep voor](../../active-directory/fundamentals/active-directory-manage-groups.md) de HSM-beheerders (in plaats van beheerdersrol toe te wijzen aan personen). Hierdoor wordt voorkomen dat het beheer wordt vergrendeld in het geval van verwijdering van een afzonderlijk account.
- Toegang tot uw beheergroepen, abonnementen, resourcegroepen en beheerde HMS's vergrendelen: gebruik Azure RBAC om de toegang tot uw beheergroepen, abonnementen en resourcegroepen te beheren
- Maak toewijzingen per sleutelrol met behulp [van lokale RBAC van beheerde HSM.](access-control.md#data-plane-and-managed-hsm-local-rbac)
- Als u de scheiding van taken wilt behouden, moet u voorkomen dat u meerdere rollen toewijst aan dezelfde principals. 
- Gebruik de principal voor toegang met minste bevoegdheden om rollen toe te wijzen.
- Maak een aangepaste roldefinitie met een nauwkeurige set machtigingen.

## <a name="choose-regions-that-support-availability-zones"></a>Regio's kiezen die beschikbaarheidszones ondersteunen

- Voor de beste hoge beschikbaarheid en zone-tolerantie kiest u Azure-regio's [waar Beschikbaarheidszones](../../availability-zones/az-overview.md) worden ondersteund. Deze regio's worden weergegeven als 'Aanbevolen regio's' in Azure Portal.

## <a name="backup"></a>Backup

- Zorg ervoor dat u regelmatig back-ups van uw HSM maakt. Back-ups kunnen worden gemaakt op HSM-niveau en voor specifieke sleutels. 

## <a name="turn-on-logging"></a>Logboekregistratie inschakelen

- [Schakel logboekregistratie in](logging.md) voor uw HSM. Stel ook waarschuwingen in.

## <a name="turn-on-recovery-options"></a>Herstelopties in-

- [Soft Delete](../general/soft-delete-overview.md) is standaard ingeschakeld.
- Schakel beveiliging tegen opsisten in als u zich wilt beschermen tegen het geforceer verwijderen van de HSM, zelfs nadat de soft delete is ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

- Zie [Volledige back-up/herstel voor](backup-restore.md) informatie over volledige back-up/herstel van HSM.
- Zie [Logboekregistratie van beheerde HSM](logging.md) voor meer informatie over het gebruik van Azure Monitor voor het configureren van logboekregistratie
- Zie [Beheerde HSM-sleutels beheren](key-management.md) voor sleutelbeheer.
- Zie [Rolbeheer van beheerde HSM voor](role-management.md) het beheren van roltoewijzingen.
