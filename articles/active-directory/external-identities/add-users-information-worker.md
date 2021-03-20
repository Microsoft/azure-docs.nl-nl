---
title: B2B-samenwerkings gebruikers toevoegen als informatie medewerker-Azure AD
description: B2B-samen werking staat informatie medewerkers en app-eigen aren toe om gast gebruikers toe te voegen aan Azure AD voor toegang | Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 345cbe2efb644fe2b84d4a6021ff962337e29285
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219909"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Hoe gebruikers in uw organisatie gast gebruikers kunnen uitnodigen voor een app

Nadat een gast gebruiker is toegevoegd aan de Directory in azure AD, kan de eigenaar van een toepassing de gast gebruiker een directe koppeling sturen naar de app die ze willen delen. Azure AD-beheerders kunnen ook Self-Service beheer instellen voor galerie-of op SAML gebaseerde apps in hun Azure AD-Tenant. Op deze manier kunnen toepassings eigenaren hun eigen gast gebruikers beheren, zelfs als de gast gebruikers nog niet aan de Directory zijn toegevoegd. Wanneer een app is geconfigureerd voor Self-service, gebruikt de eigenaar van de toepassing hun toegangs venster om een gast gebruiker aan een app uit te nodigen of een gast gebruiker toe te voegen aan een groep die toegang heeft tot de app. Voor het beheer van self-service apps voor de galerie en op SAML gebaseerde apps is een eerste installatie van een beheerder vereist. Hier volgt een samen vatting van de installatie stappen (Zie voor meer informatie [vereisten](#prerequisites) verderop op deze pagina):

 - Self-service groeps beheer inschakelen voor uw Tenant
 - Een groep maken om aan de app toe te wijzen en de gebruiker een eigenaar te maken
 - De app configureren voor Self-service en de groep toewijzen aan de app

> [!NOTE]
> * In dit artikel wordt beschreven hoe u Self-Service beheer instelt voor galerie-en op SAML gebaseerde apps die u hebt toegevoegd aan uw Azure AD-Tenant. U kunt ook [selfservice Microsoft 365 groepen instellen](../enterprise-users/groups-self-service-management.md) , zodat uw gebruikers de toegang tot hun eigen Microsoft 365 groepen kunnen beheren. Zie [gast toegang in Microsoft 365 groepen](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) en [share Point-bestanden of-mappen delen](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c)voor meer manieren waarop gebruikers Office-bestanden en-apps kunnen delen met gast gebruikers.
> * Gebruikers kunnen gasten alleen uitnodigen als ze de rol **gast uitnodiging** hebben.
## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Een gast gebruiker uitnodigen voor een app vanuit het toegangs venster

Nadat een app is geconfigureerd voor Self-service, kunnen toepassings eigenaren hun eigen toegangs venster gebruiken om een gast gebruiker uit te nodigen voor de app die ze willen delen. De gast gebruiker hoeft niet noodzakelijkerwijs aan Azure AD vooraf te worden toegevoegd. 

1. Open uw toegangs venster door naar te gaan `https://myapps.microsoft.com` .
2. Wijs de app aan, selecteer de weglatings tekens (**...**) en selecteer vervolgens **app beheren**.
 
   ![Scherm opname van het submenu voor het beheren van de app voor de Sales Force-app](media/add-users-iw/access-panel-manage-app.png)
 
3. Selecteer boven aan de lijst met gebruikers de optie **+** .
   
   ![Scherm afbeelding met het plus teken voor het toevoegen van leden aan de app](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. Typ in het zoekvak **leden toevoegen** het e-mail adres voor de gast gebruiker. U kunt optioneel een welkomstbericht toevoegen.
   
   ![Scherm opname van het venster leden toevoegen voor het toevoegen van een gast](media/add-users-iw/access-panel-invitation.png)
   
5. Selecteer **toevoegen** om een uitnodiging naar de gast gebruiker te verzenden. Nadat u de uitnodiging verzendt, wordt het gebruikersaccount automatisch toegevoegd aan de map als gast.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Iemand uitnodigen om lid te worden van een groep die toegang heeft tot de app
Nadat een app is geconfigureerd voor Self-service, kunnen de eigen aren van toepassingen gast gebruikers uitnodigen voor de groepen die ze beheren die toegang hebben tot de apps die ze willen delen. De gast gebruikers hoeven zich niet al in de Directory te bevinden. De eigenaar van de toepassing volgt deze stappen voor het uitnodigen van een gast gebruiker aan de groep, zodat deze toegang heeft tot de app.

1. Zorg ervoor dat u eigenaar bent van de self-service groep die toegang heeft tot de app die u wilt delen.
2. Open uw toegangs venster door naar te gaan `https://myapps.microsoft.com` .
3. Selecteer de **groeps** -app.
   
   ![Scherm opname van de app groepen in het toegangs venster](media/add-users-iw/access-panel-groups.png)
   
4. Selecteer onder **groepen waarvan ik eigenaar ben** de groep die toegang heeft tot de app die u wilt delen.
   
   ![Scherm afbeelding die laat zien waar u een groep selecteert onder de groepen waarvan ik eigenaar ben](media/add-users-iw/access-panel-groups-i-own.png)
   
5. Selecteer boven aan de lijst groeps leden de optie **+** .
   
   ![Scherm afbeelding met het plus teken voor het toevoegen van leden aan de groep](media/add-users-iw/access-panel-groups-add-member.png)
   
6. Typ in het zoekvak **leden toevoegen** het e-mail adres voor de gast gebruiker. U kunt optioneel een welkomstbericht toevoegen.
   
   ![Scherm opname van het venster leden toevoegen voor het toevoegen van een gast](media/add-users-iw/access-panel-invitation.png)
   
7. Selecteer **toevoegen** om de uitnodiging automatisch naar de gast gebruiker te verzenden. Nadat u de uitnodiging verzendt, wordt het gebruikersaccount automatisch toegevoegd aan de map als gast.


## <a name="prerequisites"></a>Vereisten

Voor Self-service app-beheer is een aantal initiële installaties vereist door een globale beheerder en een Azure AD-beheerder. Als onderdeel van deze installatie configureert u de app voor Self-service en wijst u een groep toe aan de app die de eigenaar van de toepassing kan beheren. U kunt de groep ook zo configureren dat iedereen het lidmaatschap kan aanvragen, maar dat de goed keuring van een groeps eigenaar verplicht is. (Meer informatie over [self-service groeps beheer](../enterprise-users/groups-self-service-management.md).) 

> [!NOTE]
> U kunt geen gast gebruikers toevoegen aan een dynamische groep of aan een groep die is gesynchroniseerd met on-premises Active Directory.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Self-service groeps beheer inschakelen voor uw Tenant
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder.
2. Selecteer **Azure Active Directory** in het navigatie venster.
3. Selecteer **Groepen**.
4. Selecteer onder **instellingen** de optie **Algemeen**.
5. Onder **self-service groeps beheer**, naast **eigen aren, kunnen aanvragen voor groepslid maatschappen beheren in het toegangs venster**, selecteert u **Ja**.
6. Selecteer **Opslaan**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Een groep maken om aan de app toe te wijzen en de gebruiker een eigenaar te maken
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een Azure AD-beheerder of globale beheerder.
2. Selecteer **Azure Active Directory** in het navigatie venster.
3. Selecteer **Groepen**.
4. Selecteer **Nieuwe groep**.
5. Onder **groeps type**, selecteer **beveiliging**.
6. Geef een **groepsnaam** en een **beschrijving** voor de groep op.
7. Selecteer **toegewezen** onder **lidmaatschaps type**.
8. Selecteer **maken** en sluit de pagina **groep** .
9. Open de groep op de pagina **groepen-alle groepen** . 
10. Selecteer onder **beheren** de optie **eigen** aren  >  **toevoegen eigen aars**. Zoek naar de gebruiker die de toegang tot de toepassing moet beheren. Selecteer de gebruiker en klik vervolgens op **selecteren**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>De app configureren voor Self-service en de groep toewijzen aan de app
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een Azure AD-beheerder of globale beheerder.
2. Selecteer in het navigatiedeelvenster de service **Azure Active Directory**.
3. Selecteer onder **beheren** de optie **bedrijfs toepassingen**  >  **alle toepassingen**.
4. Zoek en open de app in de lijst met toepassingen.
5. Selecteer onder **beheren** de optie **eenmalige aanmelding** en configureer de toepassing voor eenmalige aanmelding. (Zie [eenmalige aanmelding beheren voor zakelijke apps](../manage-apps/add-application-portal-setup-sso.md)(Engelstalig) voor meer informatie.
6. Selecteer onder **beheren** de optie **self-service** en stel selfservice app-toegang in. (Zie [self-service app-toegang gebruiken](../manage-apps/access-panel-manage-self-service-access.md)voor meer informatie.) 

    > [!NOTE]
    > Voor de instelling **waaraan gebruikers moeten worden toegevoegd** , moet u de groep selecteren die u in de vorige sectie hebt gemaakt.
7. Selecteer onder **beheren** de optie **gebruikers en groepen** en controleer of de self-service groep die u hebt gemaakt, wordt weer gegeven in de lijst.
8. Selecteer **gebruikers**  >  **en groepen** toevoegen om de app toe te voegen aan het toegangs venster van de groeps eigenaar. Zoek naar de groeps eigenaar en selecteer de gebruiker, klik op **selecteren** en klik vervolgens op **toewijzen** om de gebruiker toe te voegen aan de app.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over Azure AD B2B-samen werking:

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Hoe kunnen Azure Active Directory beheerders gebruikers voor B2B-samen werking toevoegen?](add-users-administrator.md)
- [Inwisseling uitnodiging voor B2B-samen werking](redemption-experience.md)
- [Prijzen van externe identiteiten](external-identities-pricing.md)