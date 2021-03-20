---
title: Beleid voor voorwaardelijke toegang migreren met multi-factor Authentication-Azure Active Directory
description: In dit artikel wordt beschreven hoe u een klassiek beleid migreert waarvoor multi-factor Authentication is vereist in de Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc99241f5fa7c09c69db13dcb3f6c3aaa75e376
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87027390"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Een klassiek beleid migreren in de Azure Portal

In dit artikel wordt beschreven hoe u een klassiek beleid migreert waarvoor **multi-factor Authentication** is vereist voor een Cloud-app. Hoewel het geen vereiste is, raden we u aan [het klassieke beleid te migreren in de Azure Portal](policy-migration.md) voordat u begint met het migreren van uw klassieke beleids regels.

![Klassieke beleids Details waarvoor MFA voor de Sales Force-app is vereist](./media/policy-migration/33.png)

Het migratie proces bestaat uit de volgende stappen:

1. [Open het klassieke beleid](#open-a-classic-policy) om de configuratie-instellingen op te halen.
1. Maak een nieuw beleid voor voorwaardelijke toegang voor Azure AD om uw klassieke beleid te vervangen. 
1. Schakel het klassieke beleid uit.

## <a name="open-a-classic-policy"></a>Een klassiek beleid openen

1. Ga in het [Azure Portal](https://portal.azure.com)naar **Azure Active Directory**  >    >  **voorwaardelijke toegang** voor beveiliging.
1. Selecteer **klassiek beleid**.

   ![Klassieke beleids weergave](./media/policy-migration-mfa/12.png)

1. Selecteer in de lijst met klassiek beleid het beleid dat u wilt migreren. Documenteer de configuratie-instellingen, zodat u ze opnieuw kunt maken met een nieuw beleid voor voorwaardelijke toegang.

## <a name="create-a-new-conditional-access-policy"></a>Nieuw beleid voor voorwaardelijke toegang maken

1. Ga in het [Azure Portal](https://portal.azure.com)naar **Azure Active Directory**  >    >  **voorwaardelijke toegang** voor beveiliging.
1. Als u een nieuw beleid voor voorwaardelijke toegang wilt maken, selecteert u **Nieuw beleid**.
1. Typ op de pagina **Nieuw** in het tekstvak **naam** een naam voor uw beleid.
1. Klik in de sectie **toewijzingen** op **gebruikers en groepen**.
   1. Als u alle gebruikers hebt geselecteerd in het klassieke beleid, klikt u op **alle gebruikers**. 
   1. Als u groepen hebt geselecteerd in het klassieke beleid, klikt u op **gebruikers en groepen selecteren** en selecteert u vervolgens de vereiste gebruikers en groepen.
   1. Als u de uitgesloten groepen hebt, klikt u op het tabblad **uitsluiten** en selecteert u vervolgens de vereiste gebruikers en groepen. 
   1. Selecteer **Gereed**
1. Klik in het gedeelte **toewijzing** op **Cloud-apps of-acties**.
1. Voer de volgende stappen uit op de pagina **Cloud-apps of acties** :
   1. Klik op **Apps selecteren**.
   1. Klik op **Selecteren**.
   1. Selecteer uw Cloud-app op de pagina **selecteren** en klik vervolgens op **selecteren**.
   1. Klik op de pagina **Cloud-apps** op **Gereed**.
1. Als u **multi-factor Authentication vereisen** hebt ingeschakeld:
   1. Klik in de sectie **Besturingselementen voor toegang** op **Toekennen**.
   1. Klik op de pagina **Grant** op **toegang verlenen** en klik vervolgens op **multi-factor Authentication vereisen**.
   1. Klik op **Selecteren**.
1. Klik **op aan om uw** beleid in te scha kelen en selecteer vervolgens **Opslaan**.

   ![Beleid voor voorwaardelijke toegang maken](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>Het klassieke beleid uitschakelen

Als u het klassieke beleid wilt uitschakelen, klikt u op **uitschakelen** in de weer gave **Details** .

![Klassiek beleid uitschakelen](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Volgende stappen

- Zie [klassiek beleid migreren in de Azure Portal](policy-migration.md)voor meer informatie over de klassieke beleids migratie.
- [Gebruik de modus alleen rapport voor voorwaardelijke toegang om de impact van nieuwe beleids beslissingen te bepalen.](concept-conditional-access-report-only.md)