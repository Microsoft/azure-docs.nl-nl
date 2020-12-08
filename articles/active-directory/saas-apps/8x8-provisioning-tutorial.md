---
title: 'Zelfstudie: 8x8 configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikersaccounts van Azure AD voor 8x8.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/15/2020
ms.author: Zhchia
ms.openlocfilehash: 90e3464ac9ddf1e839c3a731f79ac2c0771c37ea
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532695"
---
# <a name="tutorial-configure-8x8-for-automatic-user-provisioning"></a>Zelfstudie: 8x8 configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel 8x8 Configuration Manager als Azure Active Directory (Azure AD) voor het configureren van automatische inrichting van gebruikers. Wanneer de configuratie is voltooid, wordt inrichting en ongedaan maken van inrichting van gebruikers en groepen door Azure AD automatisch uitgevoerd op [8x8](https://www.8x8.com) met behulp van de Azure AD-inrichtingsservice. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in 8x8
> * Gebruikers uit 8x8 verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en 8x8
> * [Eenmalige aanmelding](./8x8virtualoffice-tutorial.md) bij 8x8 (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder).
* Een abonnement op 8x8 X-serie van elk niveau.
* Een 8x8-gebruikersaccount met beheerdersmachtiging in [Configuration Manager](https://vo-cm.8x8.com).
* [Eenmalige aanmelding met Azure AD](./8x8virtualoffice-tutorial.md) is al geconfigureerd.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en 8x8](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-8x8-to-support-provisioning-with-azure-ad"></a>Stap 2. 8x8 configureren ter ondersteuning van het inrichten met Azure AD

In deze sectie wordt u begeleid bij de stappen voor het configureren van 8x8 om ondersteuning te bieden voor het inrichten met Azure AD.

### <a name="to-configure-a-user-provisioning-access-token-in-8x8-configuration-manager"></a>Een toegangstoken voor het inrichten van gebruikers configureren in 8x8 Configuration Manager gaat als volgt:

1. Meld u aan bij de [beheerconsole](https://admin.8x8.com). Selecteer **Identiteitsbeheer**.

   ![Beheerder](./media/8x8-provisioning-tutorial/8x8-identity-management.png)

2. Klik op de koppeling **Gegevens voor gebruikersinrichting weergeven** om een token te genereren.

   ![Weergeven](./media/8x8-provisioning-tutorial/8x8-show-user-provisioning.png)

3. Kopieer de waarden van **8x8-URL** en **8x8-API-token**. Deze waarden worden ingevoerd in de velden **Tenant-URL** en **Token voor geheim** op het tabblad Inrichten van uw 8x8-toepassing in Azure Portal.

   ![Token](./media/8x8-provisioning-tutorial/8x8-copy-url-token.png)

## <a name="step-3-add-8x8-from-the-azure-ad-application-gallery"></a>Stap 3. 8x8 toevoegen vanuit de galerie met Azure AD-toepassingen

Voeg 8x8 toe vanuit de galerie met Azure AD-toepassingen om te beginnen met het inrichten voor 8x8. Als u 8x8 eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Dit is de eenvoudigste optie en wordt door de meeste mensen gebruikt.

Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan 8x8, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 

## <a name="step-5-configure-automatic-user-provisioning-to-8x8"></a>Stap 5. Automatische gebruikersinrichting configureren voor 8x8 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in 8x8 te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-8x8-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor 8x8 in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](./media/8x8-provisioning-tutorial/enterprise-applications.png)

    ![Blade Alle toepassingen](./media/8x8-provisioning-tutorial/all-applications.png)

2. Selecteer **8x8** in de lijst met toepassingen.

    ![De koppeling naar 8x8 in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**. Klik op **Aan de slag**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

   ![Blade Aan de slag](./media/8x8-provisioning-tutorial/get-started.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Kopieer de **8x8-URL** van Configuration Manager naar **Tenant-URL** in de sectie **Referenties voor beheerder**. Kopieer de **8x8-API-token** van Configuration Manager naar **Token voor geheim**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met 8x8. Als de verbinding mislukt, moet u controleren of uw 8x8-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Schermopname met het dialoogvenster Referenties voor beheerder, waarin u uw tenant-URL en het token voor het geheim kunt invoeren.](./media/8x8-provisioning-tutorial/provisioning.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers inrichten**.

9. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD zijn gesynchroniseerd met 8x8. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in 8x8 te vinden voor updatebewerkingen. Als u ervoor kiest om het [overeenkomende doelkenmerk](../app-provisioning/customize-application-attributes.md) te wijzigen, moet u ervoor zorgen dat de API van 8x8 het filteren van gebruikers op basis van dat kenmerk kan ondersteunen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|Opmerkingen|
   |---|---|---|
   |userName|Tekenreeks|Hiermee wordt de gebruikersnaam en de federatie-id ingesteld|
   |externalId|Tekenreeks||
   |actief|Booleaans||
   |title|Tekenreeks||
   |emails[type eq "work"].value|Tekenreeks||
   |name.givenName|Tekenreeks||
   |name.familyName|Tekenreeks||
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|Nummer van persoonlijk contact|
   |phoneNumbers[type eq "work"].value|Tekenreeks|Nummer van persoonlijk contact|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Tekenreeks||
   |urn:ietf:params:scim:schemas:extension:8x8:1.1:User:site|Tekenreeks|Kan niet worden bijgewerkt nadat de gebruiker is gemaakt|
   |landinstelling|Tekenreeks|Niet standaard toegewezen|
   |timezone|Tekenreeks|Niet standaard toegewezen|

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor 8x8.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u aan 8x8 wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht.
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid.
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)