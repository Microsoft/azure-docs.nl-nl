---
title: Een werk-of school account sluiten in een niet-beheerde Azure AD-organisatie
description: Uw werk-of school account sluiten in een onbeheerde Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17a5042df0c2ebe9f1216ded1354c7beba28c911
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95996777"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-azure-ad-organization"></a>Uw werk-of school account sluiten in een niet-beheerde Azure AD-organisatie

Als u een gebruiker in een niet-beheerde Azure Active Directory-organisatie (Azure AD) bent en u niet langer apps van die organisatie wilt gebruiken of een koppeling ermee wilt behouden, kunt u uw account op elk gewenst moment sluiten. Een onbeheerde organisatie heeft geen globale beheerder. Gebruikers in een onbeheerde organisatie kunnen hun accounts zelf sluiten zonder dat ze contact hoeven op te nemen met een beheerder.

Gebruikers in een onbeheerde organisatie worden vaak gemaakt tijdens het aanmelden door de selfservice. Een voor beeld hiervan is een informatie medewerker in een organisatie die zich aanmeldt voor een gratis service. Zie [Wat is self-service registratie voor Azure Active Directory?](directory-self-service-signup.md)voor meer informatie over selfservice registratie.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Voordat u begint

Voordat u uw account kunt sluiten, moet u de volgende items bevestigen:

* Zorg ervoor dat u een gebruiker bent van een onbeheerde Azure AD-organisatie. U kunt uw account niet sluiten als u deel uitmaakt van een beheerde organisatie. Als u deel uitmaakt van een beheerde organisatie en u uw account wilt sluiten, neemt u contact op met uw beheerder. Zie [de gebruiker verwijderen uit een niet-beheerde Tenant](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)voor informatie over hoe u kunt bepalen of u behoort tot een niet-beheerde organisatie.

* Sla de gegevens op die u wilt bewaren. Zie door het [systeem gegenereerde logboeken voor niet-beheerde tenants openen en exporteren](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)voor meer informatie over het verzenden van een aanvraag voor exporteren.

> [!WARNING]
> Het sluiten van uw account is onomkeerbaar. Wanneer u uw account sluit, worden alle persoonlijke gegevens verwijderd. U hebt geen toegang meer tot uw account en gegevens die aan uw account zijn gekoppeld.

## <a name="close-your-account"></a>Uw account sluiten

Voer de volgende stappen uit om een niet-beheerd werk-of school account te sluiten:

1. Meld u aan om [uw account te sluiten](https://go.microsoft.com/fwlink/?linkid=873123)met het account dat u wilt sluiten.

1. Selecteer bij **mijn gegevens aanvragen** **account sluiten**.

    ![Mijn gegevens aanvragen-account sluiten](./media/users-close-account/close-account.png)

1. Controleer het bevestigings bericht en selecteer vervolgens **Ja**.

    ![Mijn gegevens aanvragen-sluiten bevestigen](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Volgende stappen

- [Wat is de selfservice voor aanmelden voor Azure Active Directory?](directory-self-service-signup.md)
- [De gebruiker uit de onbeheerde tenant verwijderen](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Door het systeem gegenereerde logboeken voor onbeheerde tenants openen en exporteren](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)