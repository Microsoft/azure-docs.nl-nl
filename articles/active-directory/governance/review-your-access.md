---
title: Controleer uw toegang tot groepen & apps in toegangs beoordelingen-Azure AD
description: Meer informatie over het controleren van uw eigen toegang tot groepen of toepassingen in Azure Active Directory toegangs Beoordelingen.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3fef2f85ca7e7b4034c8582477796d49446ea44
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97746776"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Toegang tot groepen of toepassingen in azure AD-toegangs beoordelingen bekijken

Azure Active Directory (Azure AD) vereenvoudigt de manier waarop bedrijven toegang beheren tot groepen of toepassingen in azure AD en andere online services van micro soft met een functie die Azure AD-toegangs beoordelingen heet.

In dit artikel wordt beschreven hoe u uw eigen toegang tot een groep of een toepassing kunt controleren.

## <a name="review-your-access-using-my-apps"></a>Uw toegang controleren met mijn apps

De eerste stap voor het uitvoeren van een toegangs beoordeling is het zoeken en openen van de toegangs beoordeling.

>[!IMPORTANT]
> Er kunnen vertragingen optreden bij het ontvangen van e-mail en het kan tot 24 uur duren. Voeg azure-noreply@microsoft.com aan de lijst met veilige geadresseerden toe om ervoor te zorgen dat u alle e-mail berichten ontvangt.

1. Zoek naar een e-mail bericht van micro soft waarin u wordt gevraagd de toegang te controleren. Hier volgt een voor beeld van een e-mail bericht om uw toegang tot een groep te controleren.

    ![Voor beeld van een e-mail bericht van micro soft om de toegang tot een groep te controleren](./media/review-your-access/access-review-email.png)

1. Klik op de koppeling **toegang controleren** om de toegangs beoordeling te openen.

Als u de e-mail niet hebt, kunt u uw openstaande toegangs beoordelingen vinden door de volgende stappen uit te voeren.

1. Meld u aan bij de portal voor mijn apps op [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Mijn apps-Portal bevat apps waarvoor u machtigingen hebt](./media/review-your-access/myapps-access-panel.png)

1. Klik in de rechterbovenhoek van de pagina op het gebruikerssymbool, waar uw naam en standaardorganisatie worden weergegeven. Als er meerdere organisaties vermeld staan, selecteert u de organisatie waarvoor een toegangsbeoordeling is aangevraagd.

1. Klik aan de rechter kant van de pagina op de tegel **toegangs beoordelingen** om een lijst met de openstaande toegangs beoordelingen te bekijken.

    Als de tegel niet zichtbaar is, zijn er geen toegangsbeoordelingen die voor die organisatie moeten worden uitgevoerd en hoeft er momenteel geen actie te worden ondernomen.

    ![Lijst met openstaande toegangs Beoordelingen voor uw apps en groepen](./media/review-your-access/access-reviews-list.png)

1. Klik op de koppeling **controle starten** voor de toegangs beoordeling die u wilt uitvoeren.

### <a name="perform-the-access-review"></a>De toegangs beoordeling uitvoeren

Zodra u de toegangs beoordeling hebt geopend, kunt u uw toegang zien.

1. Controleer uw toegang en beslis of u nog steeds toegang nodig hebt.

    Als de aanvraag de toegang voor anderen wil controleren, ziet de pagina er anders uit. Zie [toegang tot groepen of toepassingen controleren](perform-access-review.md)voor meer informatie.

    ![Scherm opname met een open toegangs beoordeling waarin wordt gevraagd of u nog steeds toegang tot een groep nodig hebt.](./media/review-your-access/perform-access-review.png)

1. Klik op **Ja** om uw toegang te beperken of op **Nee** om uw toegang te verwijderen.

1. Als u op **Ja** klikt, moet u mogelijk een motivering opgeven in het vak **reden** .

    ![Scherm afbeelding met een voltooide toegangs beoordeling waarin wordt gevraagd of u nog steeds toegang tot een groep nodig hebt, waarbij ' ja ' is geselecteerd.](./media/review-your-access/perform-access-review-submit.png)

1. Klik op **Submit**

    Uw selectie wordt verzonden en u keert terug naar de portal mijn apps.

    Als u uw antwoord wilt wijzigen, opent u de pagina toegangs beoordelingen opnieuw en werkt u uw antwoord bij. U kunt uw antwoord op elk gewenst moment wijzigen totdat de toegangs beoordeling is beëindigd.

    > [!NOTE]
    > Als u hebt aangegeven dat u geen toegang meer nodig hebt, wordt u niet onmiddellijk verwijderd. U wordt verwijderd wanneer de controle is beëindigd of wanneer een beheerder de controle stopt.

## <a name="review-your-own-access-using-my-access-new"></a>Uw eigen toegang controleren met behulp van mijn toegang (nieuw)

U kunt de nieuwe ervaring proberen met de bijgewerkte gebruikers interface in mijn toegang een aantal verschillende manieren:

### <a name="my-apps-portal"></a>Portal van mijn apps

1. Meld u aan bij de portal voor mijn apps op [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Mijn apps-Portal bevat apps waarvoor u machtigingen hebt](./media/review-your-access/myapps-access-panel.png)

2. Klik op de tegel **toegangs beoordelingen** om een lijst met openstaande toegangs beoordelingen weer te geven.

    > [!NOTE]
    > Als de tegel **toegangs beoordelingen** niet zichtbaar is, zijn er geen toegangs Beoordelingen voor die organisatie en hoeft u op dit moment geen actie te ondernemen.

3. Klik op **Probeer** het nu! Ga in de banner aan de bovenkant van de pagina naar de nieuwe ervaring voor mijn toegang.

    ![Lijst met openstaande toegangs Beoordelingen voor apps en groepen met de nieuwe banner voor de beschik bare ervaring die wordt weer gegeven tijdens de preview](./media/review-your-access/banner-your-access.png)

4. Door gaan in de sectie **toegangs beoordeling uitvoeren**

### <a name="email"></a>E-mail

>[!IMPORTANT]
> Er kunnen vertragingen optreden bij het ontvangen van e-mail en het kan tot 24 uur duren. Voeg azure-noreply@microsoft.com aan de lijst met veilige geadresseerden toe om ervoor te zorgen dat u alle e-mail berichten ontvangt.

1. Zoek naar een e-mail bericht van micro soft waarin u wordt gevraagd de toegang te controleren. Hieronder ziet u een voor beeld van een e-mail bericht:

 ![Voor beeld van een e-mail bericht van micro soft om de toegang tot een groep te controleren](./media/review-your-access/access-review-email-preview.png)

2. Klik op de koppeling **toegang controleren** om de toegangs beoordeling te openen.

3. Door gaan in de sectie **toegangs beoordeling uitvoeren**

>[!NOTE]
>Als u op Start bekijken klikt, gaat u naar **mijn apps** Volg de stappen in de sectie hierboven met de titel **mijn apps-Portal**.

### <a name="directly-at-my-access"></a>Rechtstreeks bij mijn toegang

U kunt de openstaande toegangs beoordelingen ook weer geven met behulp van uw browser om mijn toegang te openen.

1. Meld u aan bij mijn toegang op https://myaccess.microsoft.com/

2. Selecteer **toegangs beoordelingen** in het menu op de balk aan de linkerkant om een lijst weer te geven met openstaande toegangs beoordelingen die aan u zijn toegewezen.

   ![toegangs beoordelingen in het menu](./media/review-your-access/access-review-menu.png)

### <a name="perform-the-access-review"></a>De toegangs beoordeling uitvoeren

1. Onder groepen en apps ziet u het volgende:
    
    - **Naam** De naam van de toegangs beoordeling.
    - **Verval datum** De verval datum voor de controle. Na deze datum kunnen gebruikers worden verwijderd uit de groep of app die wordt gecontroleerd.
    - **Resource** De naam van de resource die wordt gecontroleerd.
    - **Voortgang** Het aantal gebruikers dat is gecontroleerd over het totale aantal gebruikers dat deel uitmaakt van deze toegangs beoordeling.
    
2. Klik op de naam van een toegangs beoordeling om aan de slag te gaan.

   ![Lijst met openstaande toegangs Beoordelingen voor apps en groepen](./media/review-your-access/access-reviews-list-preview.png)

3. Controleer uw toegang en beslis of u nog steeds toegang nodig hebt.

    Als de aanvraag de toegang voor anderen wil controleren, ziet de pagina er anders uit. Zie [toegang tot groepen of toepassingen controleren](perform-access-review.md)voor meer informatie.

    ![Open toegangs beoordeling waarin u wordt gevraagd of u nog steeds toegang tot een groep nodig hebt](./media/review-your-access/review-access-preview.png)

1. Selecteer **Ja** om uw toegang te beperken of selecteer **Nee** om uw toegang te verwijderen.

1. Als u op **Ja** klikt, moet u mogelijk een motivering opgeven in het vak **reden** .

    ![Voltooide toegangs beoordeling waarin u wordt gevraagd of u nog steeds toegang tot een groep nodig hebt](./media/review-your-access/review-access-yes-preview.png)

1. Klik op **Submit**

    Uw selectie wordt verzonden en u keert terug naar de pagina Mijn toegang.

    Als u uw antwoord wilt wijzigen, opent u de pagina toegangs beoordelingen opnieuw en werkt u uw antwoord bij. U kunt uw antwoord op elk gewenst moment wijzigen totdat de toegangs beoordeling is beëindigd.

    > [!NOTE]
    > Als u hebt aangegeven dat u geen toegang meer nodig hebt, wordt u niet onmiddellijk verwijderd. U wordt verwijderd wanneer de controle is beëindigd of wanneer een beheerder de controle stopt.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangsbeoordeling van groepen of toepassingen voltooien](complete-access-review.md)
