---
title: Licentie plannen voor gebruikers en groepen wijzigen-Azure AD | Microsoft Docs
description: Gebruikers binnen een groep migreren naar verschillende service plannen met behulp van groeps licenties in Azure Active Directory
services: active-directory
keywords: Azure AD-licenties
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f6d472f52fa0acf791cc9b5998bf2ebf4e5951b
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503103"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Licentie toewijzingen voor een gebruiker of groep in Azure Active Directory wijzigen

In dit artikel wordt beschreven hoe u gebruikers en groepen verplaatst tussen service licentie plannen in Azure Active Directory (Azure AD). De doel stelling van Azure AD is om ervoor te zorgen dat de service of de gegevens tijdens de licentie wijziging niet verloren gaan. Gebruikers moeten naadloos overschakelen tussen services. De stappen voor het toewijzen van licentie plannen in dit artikel beschrijven het wijzigen van een gebruiker of groep op Office 365 E1 in Office 365 E3, maar de stappen zijn van toepassing op alle licentie plannen. Wanneer u licentie toewijzingen voor een gebruiker of groep bijwerkt, worden de licentie toewijzing verwijderd en worden nieuwe toewijzingen tegelijk gemaakt zodat gebruikers geen toegang meer verliezen tot hun services tijdens licentie wijzigingen of conflicten tussen abonnementen kunnen zien.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u de licentie toewijzingen bijwerkt, is het belang rijk om te controleren of bepaalde veronderstellingen waar zijn voor alle gebruikers of groepen die moeten worden bijgewerkt. Als de veronderstellingen niet waar zijn voor alle gebruikers in een groep, kan de migratie mislukken. Als gevolg hiervan kunnen sommige gebruikers de toegang tot services of gegevens verliezen. Zorg ervoor dat:

- Gebruikers hebben het huidige licentie plan (in dit geval Office 365 E1) dat is toegewezen aan een groep en door de gebruiker overgenomen en niet rechtstreeks is toegewezen.

- U hebt voldoende beschik bare licenties voor het licentie abonnement dat u toewijst. Als u niet genoeg licenties hebt, zijn sommige gebruikers mogelijk niet het nieuwe licentie plan toegewezen. U kunt het aantal beschik bare licenties controleren.

- Gebruikers hebben geen andere toegewezen service licenties die kunnen conflicteren met de gewenste licentie of waarmee de huidige licentie niet kan worden verwijderd. Bijvoorbeeld een licentie van een service zoals werk plek Analytics of project online met een afhankelijkheid van andere services.

- Als u on-premises groepen beheert en deze synchroniseert met Azure AD via Azure AD Connect, kunt u gebruikers toevoegen of verwijderen door gebruik te maken van uw on-premises systeem. Het kan enige tijd duren voordat de wijzigingen worden gesynchroniseerd met Azure AD om te worden opgehaald door groeps licenties.

- Als u gebruikmaakt van dynamische Azure AD-groepslid maatschappen, kunt u gebruikers toevoegen of verwijderen door hun kenmerken te wijzigen, maar het update proces voor licentie toewijzingen blijft hetzelfde.

## <a name="change-user-license-assignments"></a>Gebruikers licentie toewijzingen wijzigen

Als u ziet dat sommige selectie vakjes niet beschikbaar zijn op de pagina **licentie toewijzingen bijwerken** , worden de Services aangegeven die niet kunnen worden gewijzigd omdat ze zijn overgenomen van een groeps licentie.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een licentie beheerders account in uw Azure AD-organisatie.
1. Selecteer **Azure Active Directory**  >  **gebruikers** en open vervolgens de **profiel** pagina voor een gebruiker.
1. Selecteer **licenties**.
1. Selecteer **toewijzingen** om de licentie toewijzing voor de gebruiker of groep te bewerken. Op de pagina **toewijzingen** kunt u conflicten met licentie toewijzingen oplossen.
1. Schakel het selectie vakje voor Office 365 E3 in en zorg ervoor dat er mini maal alle E1-services die aan de gebruiker zijn toegewezen, zijn geselecteerd.
1. Schakel het selectie vakje voor Office 365 E1 uit.

    ![pagina licentie toewijzingen voor een gebruiker met Office 365 E1 gewist en Office 365 E3 geselecteerd](./media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Selecteer **Opslaan**.

In azure AD worden de nieuwe licenties toegepast en worden de oude licenties tegelijkertijd verwijderd om service continuïteit te bieden.

## <a name="change-group-license-assignments"></a>Toewijzing van groeps licenties wijzigen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een licentie beheerders account in uw Azure AD-organisatie.
1. Selecteer **Azure Active Directory**  >  **groepen** en open vervolgens de pagina **overzicht** voor een groep.
1. Selecteer **licenties**.
1. Selecteer de opdracht **toewijzingen** om de licentie toewijzing voor de gebruiker of groep te bewerken.
1. Schakel het selectie vakje voor Office 365 E3 in. Als u de continuïteit van de service wilt behouden, moet u alle E1-services selecteren die al aan de gebruiker zijn toegewezen.
1. Schakel het selectie vakje voor Office 365 E1 uit.

    ![Selecteer de opdracht toewijzingen op de pagina gebruikers-of groeps licenties](./media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Selecteer **Opslaan**.

Om service continuïteit te bieden, worden de nieuwe licenties door Azure AD toegepast en worden de oude licenties voor alle gebruikers in de groep tegelijkertijd verwijderd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over andere scenario's voor licentie beheer via groepen in de volgende artikelen:

- [Licenties toewijzen aan een groep in Azure Active Directory](licensing-groups-assign.md)
- [Licentieproblemen voor een groep vaststellen en oplossen in Azure Active Directory](licensing-groups-resolve-problems.md)
- [Gebruikers met een afzonderlijke licentie migreren naar groeps licenties in Azure Active Directory](licensing-groups-migrate-users.md)
- [Aanvullende scenario's Azure Active Directory groeps licenties](licensing-group-advanced.md)
- [Power shell-voor beelden voor groeps licenties in Azure Active Directory](licensing-ps-examples.md)
