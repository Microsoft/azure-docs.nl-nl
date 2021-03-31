---
title: Toegang tot groepen & toepassingen in toegangs beoordelingen bekijken-Azure AD
description: Meer informatie over het controleren van toegang tot groeps leden of toegang tot toepassingen in Azure Active Directory toegangs Beoordelingen.
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
ms.openlocfilehash: b5cd7a5737b2d13e63eabbbddd076cfc7aff83ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746793"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Toegang tot groepen en toepassingen in azure AD-toegangs beoordelingen controleren

Azure Active Directory (Azure AD) vereenvoudigt de manier waarop bedrijven de toegang tot groepen en toepassingen in azure AD en andere online services van micro soft beheren met een functie die Azure AD-toegangs beoordelingen heet. In dit artikel wordt uitgelegd hoe een aangewezen revisor een toegangs beoordeling uitvoert voor leden van een groep of gebruikers met toegang tot een toepassing. Als u wilt controleren, hebt u toegang tot een pakket Lees [beoordeling toegang tot een toegangs pakket in het beheer recht van Azure AD](entitlement-management-access-reviews-review-access.md)

## <a name="perform-access-review-using-my-apps"></a>Toegangs beoordeling uitvoeren met mijn apps

U kunt het toegangs beoordelings proces starten vanuit de e-mail melding of door rechtstreeks naar de site te gaan.

- **E-mail**:

>[!IMPORTANT]
> Er kunnen vertragingen optreden bij het ontvangen van e-mail en het kan tot 24 uur duren. Voeg azure-noreply@microsoft.com aan de lijst met veilige geadresseerden toe om ervoor te zorgen dat u alle e-mail berichten ontvangt.

1. Zoek naar een e-mail bericht van micro soft waarin u wordt gevraagd de toegang te controleren. Hier volgt een voor beeld van een e-mail bericht om de toegang tot een groep te controleren.

    ![Scherm afbeelding met een voor beeld van een e-mail bericht van micro soft om de toegang tot een groep te controleren.](./media/perform-access-review/access-review-email.png)

1. Klik op de koppeling **controle starten** om de toegangs beoordeling te openen.

- **Als u de e-mail niet hebt**, kunt u uw openstaande toegangs beoordelingen vinden door de volgende stappen uit te voeren.

    1. Meld u aan bij de portal voor mijn apps op [https://myapps.microsoft.com](https://myapps.microsoft.com) .

        ![Mijn apps-Portal bevat apps waarvoor u machtigingen hebt](./media/perform-access-review/myapps-access-panel.png)

    1. Klik in de rechter bovenhoek van de pagina op de gebruiker naast uw naam en de standaard organisatie. Als er meerdere organisaties vermeld staan, selecteert u de organisatie waarvoor een toegangsbeoordeling is aangevraagd.

    1. Klik op de tegel **toegangs beoordelingen** om een lijst met openstaande toegangs beoordelingen weer te geven.

        > [!NOTE]
        > Als de tegel **toegangs beoordelingen** niet zichtbaar is, zijn er geen toegangs Beoordelingen voor die organisatie en hoeft u op dit moment geen actie te ondernemen.

        ![Scherm opname van de lijst met openstaande toegangs Beoordelingen voor apps en groepen.](./media/perform-access-review/access-reviews-list.png)

    1. Klik op de koppeling **controle starten** voor de toegangs beoordeling die u wilt uitvoeren.

Zodra u de toegangs beoordeling hebt geopend, ziet u de namen van gebruikers die hun toegang moeten hebben gecontroleerd.

Als de aanvraag uw eigen toegang moet controleren, ziet de pagina er anders uit. Zie voor meer informatie [toegang tot groepen of toepassingen controleren](review-your-access.md).

![Open Access-beoordeling met een lijst met de gebruikers die moeten worden beoordeeld](./media/perform-access-review/perform-access-review.png)

Er zijn twee manieren waarop u toegang kunt goed keuren of weigeren:

- U kunt de toegang voor een of meer gebruikers hand matig goed keuren of weigeren door de juiste actie te kiezen voor elke gebruikers aanvraag.
- U kunt de systeem aanbevelingen accepteren.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Toegang voor een of meer gebruikers goed keuren of weigeren

1. Bekijk de lijst met gebruikers en beslis of u de permanente toegang wilt goed keuren of weigeren.

    - Als u de toegang voor één gebruiker wilt goed keuren of weigeren, klikt u op de rij om een venster te openen om de te ondernemen actie op te geven. 
    - Als u de toegang voor meerdere gebruikers wilt goed keuren of weigeren, voegt u de selectie vakjes naast de gebruikers toe en klikt u op de knop **X-gebruiker (s) controleren** om een venster te openen waarin u de te ondernemen actie kunt opgeven.

1. Klik op **goed keuren** of **weigeren**. 

    ![Actie venster met opties goed keuren, weigeren en niet bekend](./media/perform-access-review/approve-deny.png)
    >[!NOTE]
    > Als u niet zeker weet, kunt u klikken op **niet bekend**. en de gebruiker krijgt de toegang te houden en uw keuze wordt vastgelegd in de audit Logboeken.

1. De beheerder van de toegangs beoordeling vereist mogelijk dat u een reden opgeeft in het vak **reden** voor uw beslissing. Zelfs wanneer een reden niet vereist is. U kunt nog steeds een reden opgeven voor uw beslissing en de informatie die u opneemt, is beschikbaar voor andere revisoren.

1. Wanneer u de actie hebt opgegeven die u wilt uitvoeren, klikt u op **Opslaan**.

    >[!NOTE]
    > U kunt uw antwoord op elk gewenst moment wijzigen voordat de toegangs beoordeling is beëindigd. Als u uw antwoord wilt wijzigen, selecteert u de rij en werkt u het antwoord bij. U kunt bijvoorbeeld een eerder geweigerde gebruiker goed keuren of een eerder goedgekeurde gebruiker weigeren.

    >[!IMPORTANT]
    > - Als een gebruiker de toegang wordt geweigerd, worden ze niet onmiddellijk verwijderd. Ze worden verwijderd wanneer de beoordelings periode is beëindigd of wanneer een beheerder de controle stopt als [automatisch Toep assen](complete-access-review.md#apply-the-changes) is ingeschakeld.
    > - Als er meerdere revisoren zijn, wordt de laatste verzonden reactie vastgelegd. Bekijk een voor beeld waarin een beheerder twee revisoren (Alice en Bob) toewijst. Anja opent eerst de toegangs beoordeling en keurt de toegangs aanvraag van een gebruiker goed. Voordat de beoordelings periode eindigt, opent Bob de toegangs beoordeling en weigert hij de toegang tot de aanvraag die eerder door Alice is goedgekeurd. De laatste beslissing voor het weigeren van de toegang is de reactie die wordt vastgelegd.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Toegang goed keuren of weigeren op basis van aanbevelingen

Om toegangs beoordelingen gemakkelijker en sneller te laten verlopen, bieden we ook aanbevelingen die u met één klik kunt accepteren. De aanbevelingen worden gegenereerd op basis van de aanmeldings activiteit van de gebruiker.

1. Klik in de blauwe balk onder aan de pagina op **aanbevelingen accepteren**.

    ![Scherm afbeelding met de knop voor het openen van de toegangs beoordeling met de button aanbevelingen accepteren geselecteerd.](./media/perform-access-review/accept-recommendations.png)

    Er wordt een samen vatting van de aanbevolen acties weer gegeven.

    ![Venster waarin een samen vatting van de aanbevolen acties wordt weer gegeven](./media/perform-access-review/accept-recommendations-summary.png)

1. Klik op **OK** om de aanbevelingen te accepteren.

## <a name="perform-access-review-using-my-access-new"></a>Toegangs beoordeling uitvoeren met behulp van mijn toegang (nieuw)

U kunt op verschillende manieren toegang krijgen tot de nieuwe ervaring van de revisor met de bijgewerkte gebruikers interface:

### <a name="my-apps-portal"></a>Portal van mijn apps

1. Meld u aan bij mijn apps op [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![Mijn apps-Portal bevat apps waarvoor u machtigingen hebt](./media/perform-access-review/myapps-access-panel.png)

2. Klik op de tegel **toegangs beoordelingen** om een lijst met openstaande toegangs beoordelingen weer te geven.

    > [!NOTE]
    > Als de tegel **toegangs beoordelingen** niet zichtbaar is, zijn er geen toegangs Beoordelingen voor die organisatie en hoeft u op dit moment geen actie te ondernemen.

![Lijst met openstaande toegangs Beoordelingen voor apps en groepen met de nieuwe banner voor de beschik bare ervaring die wordt weer gegeven tijdens de preview](./media/perform-access-review/banner.png)

3. Klik op **Probeer** het nu! in de banner aan de bovenkant van de pagina. Hiermee gaat u naar de nieuwe ervaring voor mijn toegang.
  
### <a name="email"></a>E-mail

  >[!IMPORTANT]
> Er kunnen vertragingen optreden bij het ontvangen van e-mail en het kan tot 24 uur duren. Voeg azure-noreply@microsoft.com aan de lijst met veilige geadresseerden toe om ervoor te zorgen dat u alle e-mail berichten ontvangt.

   1. Zoek naar een e-mail bericht van micro soft waarin u wordt gevraagd de toegang te controleren. Hieronder ziet u een voor beeld van een e-mail bericht:

   ![Voor beeld van een e-mail bericht van micro soft om de toegang tot een groep te controleren](./media/perform-access-review/access-review-email-preview.png)

   2. Klik op de koppeling **controle starten** om de toegangs beoordeling te openen.

>[!NOTE]
>Als u op Start bekijken klikt, gaat u naar **mijn apps** Volg de stappen in de sectie hierboven met de titel **mijn apps-Portal**.

### <a name="navigate-to-my-access-directly"></a>Rechtstreeks naar mijn toegang navigeren

U kunt de openstaande toegangs beoordelingen ook weer geven met behulp van uw browser om mijn toegang te openen.

1. Meld u aan bij mijn toegang op https://myaccess.microsoft.com/

2. Selecteer **toegangs beoordelingen** in het menu op de balk aan de linkerkant om een lijst weer te geven met openstaande toegangs beoordelingen die aan u zijn toegewezen.

   ![toegangs beoordelingen in het menu](./media/perform-access-review/access-review-menu.png)

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Toegang voor een of meer gebruikers goed keuren of weigeren

Nadat u mijn toegang hebt geopend onder groepen en apps, kunt u het volgende zien:

- **Naam** De naam van de toegangs beoordeling.
- **Verval datum** De verval datum voor de controle. Na deze datum kunnen gebruikers worden verwijderd uit de groep of app die wordt gecontroleerd.
- **Resource** De naam van de resource die wordt gecontroleerd.
- **Voortgang** Het aantal gebruikers dat is gecontroleerd over het totale aantal gebruikers dat deel uitmaakt van deze toegangs beoordeling.

Klik op de naam van een toegangs beoordeling om aan de slag te gaan.

![Lijst met openstaande toegangs Beoordelingen voor apps en groepen](./media/perform-access-review/access-reviews-list-preview.png)

Zodra de toegangs beoordeling is geopend, ziet u de lijst met gebruikers in het bereik. Als de aanvraag uw eigen toegang moet controleren, ziet de pagina er anders uit. Zie voor meer informatie [toegang tot groepen of toepassingen controleren](review-your-access.md).

Er zijn twee manieren waarop u toegang kunt goed keuren of weigeren:

- U kunt de toegang hand matig goed keuren of weigeren voor een of meer gebruikers.
- U kunt de systeem aanbevelingen accepteren.

#### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>De toegang voor een of meer gebruikers hand matig goed keuren of weigeren

1. Bekijk de lijst met gebruikers en beslis of u de permanente toegang wilt goed keuren of weigeren.
2. Selecteer een of meer gebruikers door te klikken op de cirkel naast hun namen.
3. Selecteer **goed keuren** of **weigeren** op de bovenstaande balk.
    - Als u niet zeker weet, kunt u klikken op **niet bekend**. De gebruiker krijgt de toegang te houden en uw keuze wordt vastgelegd in de audit Logboeken. Het is belang rijk dat u de informatie die u verstrekt, beschikbaar maakt voor andere revisoren. Ze kunnen uw opmerkingen lezen en er rekening mee houden wanneer ze de aanvraag bekijken.

    ![Open Access-beoordeling met een overzicht van de gebruikers die moeten worden beoordeeld](./media/perform-access-review/user-list-preview.png)

4. De beheerder van de toegangs beoordeling vereist mogelijk dat u een reden opgeeft in het vak **reden** voor uw beslissing. Zelfs wanneer een reden niet vereist is. U kunt nog steeds een reden opgeven voor uw beslissing en de informatie die u opneemt, is voor controle beschikbaar voor andere goed keurders.

5. Klik op **Submit**
    - U kunt uw antwoord op elk gewenst moment wijzigen totdat de toegangs beoordeling is beëindigd. Als u uw antwoord wilt wijzigen, selecteert u de rij en werkt u het antwoord bij. U kunt bijvoorbeeld een eerder geweigerde gebruiker goed keuren of een eerder goedgekeurde gebruiker weigeren.

 >[!IMPORTANT]
 > - Als een gebruiker de toegang wordt geweigerd, worden ze niet onmiddellijk verwijderd. Ze worden verwijderd wanneer de beoordelings periode is beëindigd of wanneer een beheerder de controle stopt. 
 > - Als er meerdere revisoren zijn, wordt de laatste verzonden reactie vastgelegd. Bekijk een voor beeld waarin een beheerder twee revisoren (Alice en Bob) toewijst. Anja opent eerst de toegangs beoordeling en keurt de toegangs aanvraag van een gebruiker goed. Voordat de beoordelings periode eindigt, opent Bob de toegangs beoordeling en weigert hij de toegang tot de aanvraag die eerder door Alice is goedgekeurd. De laatste beslissing voor het weigeren van de toegang is de reactie die wordt vastgelegd.

#### <a name="approve-or-deny-access-based-on-recommendations"></a>Toegang goed keuren of weigeren op basis van aanbevelingen

Om toegangs beoordelingen gemakkelijker en sneller te laten verlopen, bieden we ook aanbevelingen die u met één klik kunt accepteren. De aanbevelingen worden gegenereerd op basis van de aanmeldings activiteit van de gebruiker.

1. Selecteer een of meer gebruikers en klik vervolgens op **aanbevelingen accepteren**.

    ![Open de toegangs beoordelings vermelding met de knop aanbevelingen accepteren](./media/perform-access-review/accept-recommendations-preview.png)

1. Klik op **verzenden** om de aanbevelingen te accepteren.

Als u aanbevelingen voor alle gebruikers wilt accepteren, moet u ervoor zorgen dat er niemand is geselecteerd en klikt u op de knop **aanbevelingen accepteren** op de bovenste balk.

>[!NOTE]
>Wanneer u de aanbevelingen accepteert, worden eerdere beslissingen niet gewijzigd.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangsbeoordeling van groepen of toepassingen voltooien](complete-access-review.md)
