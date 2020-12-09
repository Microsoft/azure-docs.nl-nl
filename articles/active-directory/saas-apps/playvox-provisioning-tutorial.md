---
title: 'Zelf studie: Playvox configureren voor het automatisch inrichten van gebruikers met behulp van Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar Playvox.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 3c7efca5e052c2d0680aa7ca3e1b6d27bfdd7d11
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862476"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>Zelf studie: Playvox configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet volgen in zowel Playvox als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer de service is geconfigureerd, worden gebruikers of groepen door Azure AD automatisch ingericht en ongedaan gemaakt op [Playvox](https://www.playvox.com) met behulp van de Azure AD-inrichtings dienst. Zie automatisering van gebruikers inrichten en ongedaan maken van de [inrichting van SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service en hoe deze werkt, en voor veelgestelde vragen.

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Maak gebruikers in Playvox.
> * Gebruikers in Playvox verwijderen wanneer ze niet meer toegang nodig hebben.
> * Behoud gebruikers kenmerken gesynchroniseerd tussen Azure AD en Playvox.

## <a name="prerequisites"></a>Vereisten

In het scenario in deze zelf studie wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md).
* Een gebruikersaccount in Azure AD met [machtiging](../roles/permissions-reference.md) om inrichting te configureren. Een account kan bijvoorbeeld de toepassings beheerder, de beheerder van de Cloud toepassing, de eigenaar van de toepassing of de rol globale beheerder hebben.
* Een gebruikers account in [Playvox](https://www.playvox.com) met super beheerders machtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1: plan uw inrichtings implementatie

1. Meer informatie [over de werking van de inrichtings service](../app-provisioning/user-provisioning.md).

2. Bepaal wie binnen het [bereik van de inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)valt.

3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en Playvox](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>Stap 2: Playvox configureren voor ondersteuning bij het inrichten met behulp van Azure AD

1. Meld u aan bij de Playvox-beheer console en ga naar **instellingen > API-sleutels**.

2. Selecteer **API-sleutel maken**.

    ![Gedeeltelijke scherm opname van de locatie van de knop API-sleutel maken in de Playvox-gebruikers interface.](media/playvox-provisioning-tutorial/create.png)

3. Voer een beschrijvende naam in voor de API-sleutel en selecteer vervolgens **Opslaan**. Nadat de API-sleutel is gegenereerd, selecteert u **sluiten**.

4. Op de API-sleutel die u hebt gemaakt, selecteert u het pictogram **Details** .

    ![Gedeeltelijke scherm opname van de locatie van het detail pictogram, een vergroot glas, in de Playvox-gebruikers interface.](media/playvox-provisioning-tutorial/api.png)

5. Kopieer de base64- **sleutel** waarde en sla deze op. Later typt u in het Azure Portal deze waarde in het tekstvak **geheime token** op het tabblad **inrichten** van uw Playvox-toepassing.

    ![Scherm afbeelding van het bericht met de gegevens-API-sleutel, waarbij de BASE64-sleutel waarde is gemarkeerd.](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>Stap 3: Playvox toevoegen vanuit de Azure AD-toepassings galerie

Voeg Playvox toe aan uw Azure AD-Tenant vanuit de toepassings galerie om te beginnen met het beheren van de inrichting van Playvox. Zie [Quick Start: een toepassing toevoegen aan uw Azure Active Directory-Tenant (Azure AD)](../manage-apps/add-application-portal.md)voor meer informatie.

Als u eerder Playvox voor eenmalige aanmelding (SSO) hebt ingesteld, kunt u dezelfde toepassing gebruiken. We raden u echter aan om een afzonderlijke app te maken wanneer u de integratie in eerste instantie test.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4: definiëren wie binnen het bereik van de inrichting valt

U gebruikt de Azure AD-inrichtings service voor het bereik dat wordt ingericht op basis van de toewijzing aan de toepassing of aan de kenmerken van de gebruiker of groep. Als u het bereik wilt inrichten dat wordt ingericht voor uw app op basis van toewijzing, raadpleegt u [gebruikers toewijzing beheren voor een app in azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) voor informatie over het toewijzen van gebruikers of groepen aan de toepassing. Voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, gebruikt u een bereik filter zoals beschreven in [toepassings inrichting op basis van kenmerken met bereik filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

Onthoud deze punten:

* Wanneer u gebruikers toewijst aan Playvox, moet u een andere rol dan standaard toegang selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als standaard toegang de enige rol die beschikbaar is voor de toepassing, kunt u [het toepassings manifest bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om andere functies toe te voegen.

* Begin klein. Test met een kleine set gebruikers of groepen voordat u naar iedereen uitrolt. Wanneer het inrichten van het bereik is gebaseerd op toegewezen gebruikers of groepen, kunt u de grootte van de set bepalen door slechts één of twee gebruikers of groepen aan de app toe te wijzen. Als het inrichtings bereik alle gebruikers en groepen bevat, kunt u een [op kenmerken gebaseerd bereik filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven om de grootte van de testset te beperken.

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>Stap 5: automatische gebruikers inrichting configureren voor Playvox

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers of groepen te maken, bij te werken en uit te scha kelen op basis van gebruikers-of groeps toewijzingen in azure AD.

Automatische gebruikers inrichting configureren voor Playvox in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![Gedeeltelijke scherm opname van de Azure Portal, met bedrijfs toepassingen en alle toepassingen items gemarkeerd](common/enterprise-applications.png)

2. Zoek in de lijst toepassingen naar en selecteer **Playvox**.

    ![Gedeeltelijke scherm opname van de lijst met toepassingen, waarbij het zoekvak van de toepassing is gemarkeerd.](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Gedeeltelijke scherm opname van het menu-item inrichten.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Gedeeltelijke scherm afbeelding van het tabblad inrichten, met de optie automatisch geselecteerd in de vervolg keuzelijst inrichtings modus.](common/provisioning-automatic.png)

5. Voer in de sectie **beheerders referenties** uw Playvox **Tenant-URL** in als:

    `https://{tenant}.playvox.com/scim/v1`

    Voer het **geheime token** in dat u eerder in stap 2 hebt gekopieerd. Selecteer vervolgens **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Playvox. Als de verbinding mislukt, zorgt u ervoor dat uw Playvox-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Gedeeltelijke scherm opname van de sectie met beheerders referenties, inclusief de tekst vakken Tenant-URL en geheim token, en met de test verbindings koppeling gemarkeerd.](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het tekstvak **e-mail bericht** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen ontvangt. Selecteer vervolgens het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**.

    ![Gedeeltelijke scherm opname van het tekstvak voor het e-mail bericht en het selectie vakje e-mail melding.](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Playvox**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Playvox in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Playvox voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](../app-provisioning/customize-application-attributes.md)te wijzigen, moet u ervoor zorgen dat de PLAYVOX-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer **Opslaan** om eventuele wijzigingen toe te passen.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |actief|Booleaans|
   |displayName|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |name.formatted|Tekenreeks|
   |externalId|Tekenreeks|

10. Raadpleeg de instructies in de [Zelfstudie bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) als u bereikfilters wilt configureren.

11. Als u de Azure AD-inrichtings service voor **Playvox wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Gedeeltelijke scherm opname van de sectie instellingen, waarin de inrichtings status wordt weer gegeven die is ingesteld op aan.](common/provisioning-toggle-on.png)

12. Geef in **instellingen** nog steeds de gebruikers of groepen op die moeten worden ingericht in Playvox door de waarden te kiezen die u in het **bereik** wilt opgeven.

    ![Gedeeltelijke scherm opname van de sectie instellingen, met daarin de vervolg keuzelijst bereik.](common/provisioning-scope.png)

13. Selecteer **Opslaan** als u klaar bent voor het inrichten.

    ![Gedeeltelijke scherm opname van opties voor opslaan en negeren.](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De eerste cyclus duurt langer dan later. Latere cycli worden ongeveer elke 40 minuten uitgevoerd, mits de Azure AD-inrichtings service wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6: uw implementatie bewaken

Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

* Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht.
* Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid.
* Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. Zie [Toepassing inrichten in quarantainestatus](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccounts inrichten voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)