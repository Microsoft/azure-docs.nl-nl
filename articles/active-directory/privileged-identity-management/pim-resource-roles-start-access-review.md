---
title: Een toegangs beoordeling van Azure-resource rollen maken in PIM-Azure AD | Microsoft Docs
description: Meer informatie over het maken van een toegangs beoordeling van Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 03/09/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4276b48584ecbad91794de58abafd7e3367f6877
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564030"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Een toegangs beoordeling van Azure-resource rollen maken in Privileged Identity Management

De nood zaak om toegang te krijgen tot beschermde Azure-resource rollen door werk nemers wijzigingen in de loop van de tijd. Om het risico te verminderen dat is gekoppeld aan verouderde roltoewijzingen, moet u de toegang regel matig controleren. U kunt Azure Active Directory (Azure AD) Privileged Identity Management (PIM) gebruiken om toegangs beoordelingen te maken voor uitgebreide toegang tot Azure-resource rollen. U kunt ook terugkerende toegangs beoordelingen configureren die automatisch worden uitgevoerd. In dit artikel wordt beschreven hoe u een of meer toegangs beoordelingen maakt.

## <a name="prerequisite-role"></a>Vereiste rol

 Als u toegangs beoordelingen wilt maken, moet u worden toegewezen aan de Azure-rol [eigenaar](../../role-based-access-control/built-in-roles.md#owner) of [beheerder voor gebruikers toegang](../../role-based-access-control/built-in-roles.md#user-access-administrator) voor de resource.

## <a name="open-access-reviews"></a>Toegangs beoordelingen openen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) met een gebruiker die is toegewezen aan een van de vereiste rollen.

1. Open **Azure AD privileged Identity Management**.

1. Selecteer in het linkermenu Azure- **resources**.

1. Selecteer de resource die u wilt beheren, zoals een abonnement.

1. Selecteer onder beheren de optie **toegangs beoordelingen**.

    ![Azure-resources-lijst met toegangs beoordelingen waarin de status van alle beoordelingen wordt weer gegeven](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>De toegangs beoordeling starten

Zodra u de instellingen voor een toegangs beoordeling hebt opgegeven, klikt u op **Start**. De toegangs beoordeling wordt weer gegeven in de lijst met een indicator van de status.

![Lijst met toegangs beoordelingen waarin de status van de gestarte beoordeling wordt weer gegeven](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Standaard verzendt Azure AD een e-mail naar revisoren kort nadat de controle is gestart. Als u ervoor kiest om Azure AD de e-mail niet te laten verzenden, moet u de revisoren informeren dat een toegangs beoordeling wacht totdat deze is voltooid. U kunt de instructies weer geven voor het controleren van de [toegang tot Azure-resource rollen](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>De toegangs beoordeling beheren

U kunt de voortgang volgen zodra de revisoren hun beoordelingen hebben voltooid op de pagina **overzicht** van de toegangs beoordeling. Er worden geen toegangs rechten gewijzigd in de map totdat de [controle is voltooid](pim-resource-roles-complete-access-review.md).

![Overzichts pagina toegangs beoordelingen met details van de beoordeling](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Als dit een eenmalige controle is, nadat de toegangs beoordelings periode is verstreken of de beheerder de toegangs beoordeling heeft gestopt, volgt u de stappen in [een toegangs beoordeling van Azure-resource rollen volt ooien](pim-resource-roles-complete-access-review.md) om de resultaten te bekijken en toe te passen.  

Als u een reeks toegangs beoordelingen wilt beheren, gaat u naar de toegangs beoordeling en gaat u naar de geplande Beoordelingen. vervolgens kunt u de eind datum bewerken of revisoren toevoegen/verwijderen dienovereenkomstig.

Op basis van uw selecties tijdens de **voltooiings instellingen** wordt automatisch Toep assen na de eind datum van de beoordeling of wanneer u de controle hand matig stopt. De status van de beoordeling wordt gewijzigd van **voltooid** met behulp van tussenliggende statussen, zoals **Toep assen** en tot slot op de status **toegepast**. U wordt gewend om geweigerde gebruikers, indien van toepassing, te zien, indien aanwezig, die in een paar minuten worden verwijderd uit rollen.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot Azure-resource rollen controleren](pim-resource-roles-perform-access-review.md)
- [Een toegangs beoordeling van Azure-resource rollen volt ooien](pim-resource-roles-complete-access-review.md)
- [Een toegangs beoordeling van Azure AD-rollen maken](pim-how-to-start-security-review.md)
