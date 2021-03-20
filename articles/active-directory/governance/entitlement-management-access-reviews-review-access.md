---
title: Toegang tot een toegangs pakket controleren in het beheer van rechten van Azure AD
description: Meer informatie over het volt ooien van een toegangs beoordeling van rechten voor beheer toegangs pakketten in Azure Active Directory toegangs beoordelingen (preview).
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
ms.openlocfilehash: d1115c08214db19227b4b8d7be671ce4da1cf2b1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798594"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Toegang tot een toegangs pakket controleren in het beheer van rechten van Azure AD

Het beheer van rechten van Azure AD vereenvoudigt de manier waarop bedrijven de toegang tot groepen, toepassingen en share point-sites beheren. In dit artikel wordt beschreven hoe u toegangs beoordelingen uitvoert voor andere gebruikers die zijn toegewezen aan een toegangs pakket als aangewezen revisor.

## <a name="prerequisites"></a>Vereisten

Als u de actieve toegangs pakket toewijzingen van gebruikers wilt beoordelen, moet u voldoen aan de vereisten voor een toegangs beoordeling:
- Azure AD Premium P2
- Globale beheerder
- Aangewezen gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

Zie [Licentievereisten](entitlement-management-overview.md#license-requirements) voor meer informatie.


## <a name="open-the-access-review"></a>Open de toegangs beoordeling

Gebruik de volgende stappen om de toegangs beoordeling te zoeken en te openen:

1. U ontvangt mogelijk een e-mail van micro soft waarin u wordt gevraagd de toegang te controleren. Zoek het e-mail bericht om de toegangs beoordeling te openen. Hier volgt een voor beeld van een e-mail bericht om toegang te controleren:
    
    ![E-mail adres van revisor voor toegang](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Klik op de koppeling **gebruikers toegang controleren** om de toegangs beoordeling te openen. 

1. Als u de e-mail niet hebt, kunt u uw openstaande toegangs beoordelingen vinden door rechtstreeks naar te navigeren https://myaccess.microsoft.com .  (Gebruik `https://myaccess.microsoft.us` in plaats daarvan voor Amerikaanse overheid.)

1. Klik op **toegangs beoordelingen** op de linkernavigatiebalk om een lijst weer te geven met openstaande toegangs beoordelingen die aan u zijn toegewezen.
    
    ![Toegangs beoordelingen selecteren voor mijn toegang](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Klik op het beoordeling dat u wilt beginnen.
    
    ![Selecteer de toegangs beoordeling](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>De toegangs beoordeling uitvoeren

Zodra u de toegangs beoordeling hebt geopend, ziet u de namen van de gebruikers die u moet controleren. Er zijn twee manieren waarop u toegang kunt goed keuren of weigeren:
- U kunt de toegang hand matig goed keuren of weigeren voor een of meer gebruikers
- U kunt de systeem aanbevelingen accepteren

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>De toegang voor een of meer gebruikers hand matig goed keuren of weigeren
1. Bekijk de lijst met gebruikers en bepaal welke gebruikers nog steeds toegang moeten hebben.

    ![Lijst met gebruikers die moeten worden beoordeeld](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Als u de toegang wilt goed keuren of weigeren, selecteert u het keuze rondje links van de naam van de gebruiker.

1. Selecteer in de balk boven de gebruikers namen **goed keuren** of **weigeren** .

    ![De gebruiker selecteren](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Als u niet zeker weet, kunt u klikken op de knop **niet kennen** .

    Als u deze selectie maakt, onderhoudt de gebruiker de toegang en wordt deze selectie in de audit Logboeken opgenomen. Het logboek bevat alle andere revisoren die u nog steeds de controle hebt voltooid.

1. Mogelijk moet u een reden opgeven voor uw beslissing. Typ een reden en klik op **verzenden**.

    ![Toegang goed keuren of weigeren](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. U kunt uw beslissing op elk gewenst moment wijzigen vóór het einde van de beoordeling. Als u dit wilt doen, selecteert u de gebruiker in de lijst en wijzigt u de beslissing. U kunt bijvoorbeeld de toegang goed keuren voor een gebruiker die u eerder hebt geweigerd.

Als er meerdere revisoren zijn, wordt de laatste verzonden reactie vastgelegd. Bekijk een voor beeld waarin een beheerder twee revisoren (Alice en Bob) toewijst. Anja opent de beoordeling eerst en keurt de toegang goed. Voordat de controle eindigt, wordt de controle geopend en wordt de toegang geweigerd. In dit geval wordt de laatste beslissing voor het weigeren van toegang vastgelegd.

>[!NOTE]
>Als een gebruiker de toegang wordt geweigerd, worden deze niet direct uit het toegangs pakket verwijderd. De gebruiker wordt verwijderd uit het toegangs pakket wanneer de controle eindigt of een beheerder de controle beëindigt.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Toegang goed keuren of weigeren met de door het systeem gegenereerde aanbevelingen

Als u de toegang voor meerdere gebruikers sneller wilt bekijken, kunt u de door het systeem gegenereerde aanbevelingen gebruiken om de aanbevelingen met één klik te accepteren. De aanbevelingen worden gegenereerd op basis van de aanmeldings activiteit van de gebruiker.

1.  Klik in de balk boven aan de pagina op **aanbevelingen accepteren**.
    
    ![Selecteer aanbevelingen accepteren](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Er wordt een samen vatting van de aanbevolen acties weer gegeven.

1.  Klik op **verzenden** om de aanbevelingen te accepteren.

## <a name="next-steps"></a>Volgende stappen

- [Zelf beoordeling van toegangs pakketten](entitlement-management-access-reviews-self-review.md)