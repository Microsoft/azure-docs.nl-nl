---
title: Gebruikers profiel informatie toevoegen of bijwerken-Azure AD
description: Instructies voor het toevoegen van informatie aan het profiel van een gebruiker in Azure Active Directory, met inbegrip van een afbeeldings-en taak gegevens.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27896bac6f5436fb6717e4bc1ab0a1f9381904ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370913"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>De profielgegevens van een gebruiker toevoegen of bijwerken met behulp van Azure Active Directory
Gebruikers profiel gegevens toevoegen, met inbegrip van een profiel afbeelding, specifieke informatie over de taak en enkele instellingen met behulp van Azure Active Directory (Azure AD). Voor meer informatie over het toevoegen van nieuwe gebruikers raadpleegt [u gebruikers toevoegen of verwijderen in azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Profiel gegevens toevoegen of wijzigen
Zoals u ziet, is er meer informatie beschikbaar in het profiel van een gebruiker dan wat u tijdens het maken van de gebruiker kunt toevoegen. Al deze aanvullende informatie is optioneel en kan worden toegevoegd als nodig door uw organisatie.

## <a name="to-add-or-change-profile-information"></a>Profiel gegevens toevoegen of wijzigen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) als een gebruikers beheerder voor de organisatie.

2. Selecteer **Azure Active Directory**, selecteer **gebruikers** en selecteer vervolgens een gebruiker. Bijvoorbeeld _Alain Charon_.

    De pagina **Alain Charon-profiel** wordt weer gegeven.

    ![De profiel pagina van de gebruiker, met inbegrip van Bewerk bare informatie](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Selecteer **bewerken** om eventueel de informatie in elk van de beschik bare secties toe te voegen of bij te werken.

    ![De profiel pagina van de gebruiker, waarin de Bewerk bare gebieden worden weer gegeven](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Profiel afbeelding.** Selecteer een miniatuur afbeelding voor het account van de gebruiker. Deze afbeelding wordt weer gegeven in Azure Active Directory en op de persoonlijke pagina's van de gebruiker, zoals de pagina myapps.microsoft.com.

    - **Persoon.** Een extra identiteits waarde voor de gebruiker toevoegen of bijwerken, zoals een getrouwde achternaam. U kunt deze naam onafhankelijk van de waarden voor voor naam en achternaam instellen. U kunt dit bijvoorbeeld gebruiken om initialen, een bedrijfs naam op te geven of om de weer gegeven volg orde van de namen te wijzigen. Een ander voor beeld: voor twee gebruikers met de naam Chris Green kunt u de teken reeks voor identiteiten gebruiken om de namen in te stellen op Chris B. Green ' Chris R. Green (Contoso). '

    - **Taak info.** Voeg informatie over de taak toe, zoals de functie, afdeling of manager van de gebruiker.

    - **Instellingen.** Bepaal of de gebruiker zich kan aanmelden bij Azure Active Directory Tenant. U kunt ook de globale locatie van de gebruiker opgeven.

    - **Contact gegevens.** Voeg relevante contact gegevens voor de gebruiker toe, met uitzonde ring van de telefoon-of mobiele contact gegevens van een bepaalde gebruiker (alleen een globale beheerder kan worden bijgewerkt voor gebruikers in beheerders rollen).

    - **Contact gegevens voor authenticatie.** Controleer deze informatie om te controleren of er een actief telefoon nummer en e-mail adres voor de gebruiker zijn. Deze informatie wordt gebruikt door Azure Active Directory om ervoor te zorgen dat de gebruiker tijdens het aanmelden echt de gebruiker is. Contact gegevens voor verificatie kunnen alleen worden bijgewerkt door een globale beheerder.

4. Selecteer **Opslaan**.

    Al uw wijzigingen worden voor de gebruiker opgeslagen.

    >[!Note]
    >U moet Windows Server Active Directory gebruiken om de identiteit, contact gegevens of taak gegevens bij te werken voor gebruikers waarvan de bron van de autoriteit Windows Server Active Directory is. Nadat u de update hebt voltooid, moet u wachten tot de volgende synchronisatie cyclus is voltooid voordat u de wijzigingen ziet.

## <a name="next-steps"></a>Volgende stappen
Nadat u de profielen van uw gebruikers hebt bijgewerkt, kunt u de volgende basis processen uitvoeren:

- [Gebruikers toevoegen of verwijderen](add-users-azure-active-directory.md)

- [Rollen toewijzen aan gebruikers](active-directory-users-assign-role-azure-portal.md)

- [Een basisgroep maken en leden toevoegen](active-directory-groups-create-azure-portal.md)

U kunt ook andere gebruikers beheer taken uitvoeren, zoals het toewijzen van gemachtigden, het gebruiken van beleid en het delen van gebruikers accounts. Zie [Azure Active Directory-documentatie voor gebruikers beheer](../enterprise-users/index.yml)voor meer informatie over andere beschik bare acties.
