---
title: Azure-resource rollen activeren in PIM-Azure AD | Microsoft Docs
description: Meer informatie over het activeren van uw Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4e3fe72492ef92fe65572c75bf9f88b5f9ffda3
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653415"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Mijn Azure-resource rollen in Privileged Identity Management activeren

Gebruik Privileged Identity Management (PIM) om in aanmerking komende leden voor Azure-resources in staat te stellen activeringen te plannen voor een toekomstige datum en tijd. Ze kunnen ook een specifieke activerings duur binnen het maximum (geconfigureerd door beheerders) selecteren.

Dit artikel is voor leden die hun Azure-resource functie moeten activeren in Privileged Identity Management.

## <a name="activate-a-role"></a>Een rol activeren

Wanneer u een Azure-resource functie moet uitvoeren, kunt u de activering aanvragen met de navigatie optie **mijn rollen** in privileged Identity Management.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**. Zie voor meer informatie over het toevoegen van de Privileged Identity Management tegel aan uw dash board [beginnen met privileged Identity Management](pim-getting-started.md).

1. Selecteer **mijn rollen**.

    ![Pagina mijn rollen met rollen die u kunt activeren](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Selecteer **Azure-resource rollen** om een lijst met uw in aanmerking komende Azure-resource rollen weer te geven.

   ![Mijn rollen-pagina Azure-resource rollen](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png)

1. Zoek in de lijst met **Azure-resource rollen** de rol die u wilt activeren.

    ![Azure-resource rollen: mijn in aanmerking komende rollen lijst](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Selecteer **activeren** om de pagina activeren te openen.

    ![Het deel venster openen met het bereik, de begin tijd, de duur en de reden](./media/pim-resource-roles-activate-your-roles/azure-role-eligible-activate.png)

1. Als voor uw rol multi-factor Authentication is vereist, selecteert u **uw identiteit verifiëren voordat u doorgaat**. U hoeft slechts één keer per sessie te authenticeren.

    ![Mijn identiteit met MFA verifiëren voordat de functie wordt geactiveerd](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Selecteer **Mijn identiteit verifiëren** en volg de instructies voor aanvullende beveiligings verificatie.

    ![Scherm om beveiligings verificatie te bieden, zoals een pincode](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Als u een kleiner bereik wilt opgeven, selecteert u **bereik** om het deel venster Resource filter te openen.

    Het is een best practice om alleen toegang te vragen voor de resources die u nodig hebt. In het deel venster Resource filter kunt u de resource groepen of bronnen opgeven waartoe u toegang nodig hebt.

    ![Activeren-resource filter venster om het bereik op te geven](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Geef indien nodig een aangepaste begin tijd voor de activering op. Het lid wordt na de geselecteerde tijd geactiveerd.

1. Voer in het vak **reden** de reden voor de activerings aanvraag in.

    ![Deel venster activeren met bereik, start tijd, duur en reden is voltooid](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Selecteer **Activate**.

    Als voor de [rol goed keuring moet](pim-resource-roles-approval-workflow.md) worden geactiveerd, wordt in de rechter bovenhoek van uw browser gemeld dat de aanvraag goed keuring in behandeling is.

    ![Goedkeurings melding in afwachting van activerings aanvraag](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>De status van uw aanvragen weer geven

U kunt de status van de in behandeling zijnde aanvragen weer geven om te activeren.

1. Open Azure AD Privileged Identity Management.

1. Selecteer **mijn aanvragen** om een lijst weer te geven met uw Azure AD-rol en aanvragen voor Azure-resource rollen.

    ![Mijn aanvragen-Azure-resource pagina met uw aanvragen in behandeling](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Schuif naar rechts om de kolom Status van de **aanvraag** weer te geven.

## <a name="cancel-a-pending-request"></a>Een aanvraag in behandeling annuleren

Als u geen activering van een rol vereist waarvoor goed keuring is vereist, kunt u een aanvraag in behandeling op elk gewenst moment annuleren.

1. Open Azure AD Privileged Identity Management.

1. Selecteer **mijn aanvragen**.

1. Selecteer de koppeling **Annuleren** voor de functie die u wilt annuleren.

    Wanneer u annuleren selecteert, wordt de aanvraag geannuleerd. Als u de functie opnieuw wilt activeren, moet u een nieuwe aanvraag indienen voor activering.

   ![Mijn aanvraag lijst met de actie annuleren gemarkeerd](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Machtigingen worden niet toegekend na het activeren van een rol

Wanneer u een rol in Privileged Identity Management activeert, wordt de activering mogelijk niet onmiddellijk door gegeven aan alle portals waarvoor de rol met privileges is vereist. Zelfs als de wijziging is doorgevoerd, kan het soms gebeuren dat webcaching in een portal tot gevolg heeft dat de wijziging niet onmiddellijk van kracht gaat. Als de activering is vertraagd, is dit wat u moet doen.

1. Meld u af bij de Azure-portal en meld u weer aan.
1. Controleer in Privileged Identity Management of u wordt vermeld als lid van de rol.

## <a name="next-steps"></a>Volgende stappen

- [Azure-resource rollen in Privileged Identity Management uitbreiden of vernieuwen](pim-resource-roles-renew-extend.md)
- [Mijn Azure AD-rollen in Privileged Identity Management activeren](pim-how-to-activate-role.md)
