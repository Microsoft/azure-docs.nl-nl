---
title: Toewijzingen voor een toegangspakket weergeven, toevoegen en verwijderen in Azure AD-rechtenbeheer - Azure Active Directory
description: Meer informatie over het weergeven, toevoegen en verwijderen van toewijzingen voor een toegangspakket in Azure Active Directory rechtenbeheer.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e00fe3761824462252ce4984beb754385f3eca9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532147"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Toewijzingen voor een toegangspakket weergeven, toevoegen en verwijderen in Azure AD-rechtenbeheer

In Azure AD-rechtenbeheer kunt u zien wie is toegewezen aan toegangspakketten, hun beleid en status. Als een toegangspakket een geschikt beleid heeft, kunt u de gebruiker ook rechtstreeks toewijzen aan een toegangspakket. In dit artikel wordt beschreven hoe u toewijzingen voor toegangspakketten kunt weergeven, toevoegen en verwijderen.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-rechtenbeheer wilt gebruiken en gebruikers wilt toewijzen voor toegang tot pakketten, hebt u een van de volgende licenties nodig:

- Azure AD Premium P2
- Licentie voor Enterprise Mobility + Security (EMS) E5

## <a name="view-who-has-an-assignment"></a>Weergeven wie een toewijzing heeft

**Vereiste rol:** Globale beheerder, Gebruikersbeheerder, Cataloguseigenaar, Toegangspakketbeheer of Toewijzingsmanager toegangspakket

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **Toegangspakketten** en open vervolgens het toegangspakket.

1. Klik **op Toewijzingen** om een lijst met actieve toewijzingen weer te geven.

    ![Lijst met toewijzingen aan een toegangspakket](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Klik op een specifieke toewijzing voor meer informatie.

1. Als u een lijst wilt weergeven met toewijzingen die niet alle resourcerollen correct hebben ingericht, klikt u op de filterstatus en selecteert **u Leveren.**

    U kunt meer informatie over bezorgingsfouten bekijken door de bijbehorende aanvraag van de gebruiker te zoeken op de **pagina** Aanvragen.

1. Als u verlopen toewijzingen wilt zien, klikt u op de filterstatus en selecteert **u Verlopen.**

1. Als u een CSV-bestand uit de gefilterde lijst wilt downloaden, klikt u op **Downloaden.**

### <a name="viewing-assignments-programmatically"></a>Toewijzingen programmatisch weergeven

U kunt ook toewijzingen in een toegangspakket ophalen met behulp van Microsoft Graph.  Een gebruiker met een geschikte rol met een toepassing met de gedelegeerde machtiging kan de API aanroepen om `EntitlementManagement.ReadWrite.All` [accessPackageAssignments weer te geven.](/graph/api/accesspackageassignment-list?view=graph-rest-beta&preserve-view=true)

## <a name="directly-assign-a-user"></a>Rechtstreeks een gebruiker toewijzen

In sommige gevallen wilt u mogelijk specifieke gebruikers rechtstreeks toewijzen aan een toegangspakket, zodat gebruikers het proces van het aanvragen van het toegangspakket niet hoeven te doorlopen. Als u rechtstreeks gebruikers wilt toewijzen, moet het toegangspakket een beleid hebben waarmee beheerders directe toewijzingen kunnen uitvoeren.

**Vereiste rol:** Globale beheerder, Gebruikersbeheerder, Cataloguseigenaar, Toegangspakketbeheer of Toegangspakkettoewijzingsmanager

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **Toegangspakketten** en open vervolgens het toegangspakket.

1. Klik in het menu links op **Toewijzingen.**

1. Klik **op Nieuwe toewijzing** om Add user to access package te openen.

    ![Toewijzingen: gebruiker toevoegen voor toegang tot pakket](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Klik **op Gebruikers toevoegen** om de gebruikers te selecteren aan wie u dit toegangspakket wilt toewijzen.

1. Selecteer in **de lijst** Beleid selecteren een beleid dat de toekomstige aanvragen en levenscyclus van de gebruikers zal beheren en bijhouden. Als u wilt dat de geselecteerde gebruikers verschillende beleidsinstellingen hebben, kunt u op **Nieuw beleid maken klikken** om een nieuw beleid toe te voegen.

1. Stel de datum en tijd in waarop u wilt dat de toewijzing van de geselecteerde gebruikers start en eindigt. Als er geen einddatum wordt opgegeven, worden de levenscyclusinstellingen van het beleid gebruikt.

1. Geef eventueel een reden op voor uw directe toewijzing voor het bewaren van records.

1. Klik **op Toevoegen** om de geselecteerde gebruikers rechtstreeks toe te wijzen aan het toegangspakket.

    Klik na enkele ogenblikken op **Vernieuwen om** de gebruikers in de lijst Toewijzingen weer te geven.

### <a name="directly-assigning-users-programmatically"></a>Gebruikers rechtstreeks toewijzen via een programma

U kunt een gebruiker ook rechtstreeks toewijzen aan een toegangspakket met behulp Microsoft Graph.  Een gebruiker met een geschikte rol met een toepassing met de gedelegeerde machtiging kan de API aanroepen om een `EntitlementManagement.ReadWrite.All` [accessPackageAssignmentRequest te maken.](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true)

## <a name="remove-an-assignment"></a>Een toewijzing verwijderen

**Vereiste rol:** Globale beheerder, Gebruikersbeheerder, Cataloguseigenaar, Toegangspakketbeheer of Toewijzingsmanager toegangspakket

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **Toegangspakketten** en open vervolgens het toegangspakket.

1. Klik in het menu links op **Toewijzingen.**
 
1. Klik op het selectievakje naast de gebruiker van wie u de toewijzing wilt verwijderen uit het toegangspakket. 

1. Klik op **de** knop Verwijderen bovenaan het linkerdeelvenster. 
 
    ![Toewijzingen: gebruiker verwijderen uit toegangspakket](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Er wordt een melding weergegeven met de melding dat de toewijzing is verwijderd. 

## <a name="next-steps"></a>Volgende stappen

- [Aanvraag en instellingen voor een toegangspakket wijzigen](entitlement-management-access-package-request-policy.md)
- [Rapporten en logboeken weergeven](entitlement-management-reports.md)