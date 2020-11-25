---
title: 'Zelfstudie: IDEO configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u automatisch gebruikersaccounts van Azure AD inricht in IDEO, of de inrichting ervan ongedaan maakt.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: Zhchia
ms.openlocfilehash: 10b7b79ba5081e6133f2e5e94840eddfe403caef
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013170"
---
# <a name="tutorial-configure-ideo-for-automatic-user-provisioning"></a>Zelfstudie: IDEO configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in IDEO en Azure AD (Azure Active Directory) om automatisch gebruikers en/of groepen van Azure AD in te richten in IDEO, of de inrichting ervan ongedaan te maken. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in IDEO
> * Gebruikers verwijderen uit IDEO wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en IDEO
> * Groepen en groepslidmaatschappen inrichten in IDEO
> * Eenmalige aanmelding bij IDEO (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md).
* Een gebruikersaccount in Azure AD met [machtigingen](../users-groups-roles/directory-assign-admin-roles.md) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder).
* [Een IDEO-tenant](https://www.shape.space/product/pricing)
* Een gebruikersaccount in IDEO | Shape met beheerdersmachtigingen.


## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en IDEO](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-ideo-to-support-provisioning-with-azure-ad"></a>Stap 2. IDEO configureren om ondersteuning te bieden voor inrichting met Azure AD

Voordat u IDEO configureert voor automatische inrichting van gebruikers met Azure AD, moet u bepaalde inrichtingsgegevens ophalen bij IDEO.

* Neem voor het **Token voor geheim** contact op met het ondersteuningsteam van IDEO op productsupport@ideo.com. Deze waarde wordt ingevoerd in het veld **Token voor geheim** op het tabblad Inrichten van de IDEO-toepassing in Azure Portal. 

## <a name="step-3-add-ideo-from-the-azure-ad-application-gallery"></a>Stap 3. IDEO toevoegen vanuit de Azure AD-toepassingsgalerie

Voeg IDEO toe vanuit de Azure AD-toepassingsgalerie om de inrichting in IDEO te beheren. Als u IDEO eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers toewijst aan IDEO, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-ideo"></a>Stap 5. Automatische inrichting van gebruiker configureren in IDEO 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen te maken, bij te werken en uit te schakelen in IDEO, op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ideo-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor IDEO in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **IDEO** in de lijst met toepassingen.

    ![De IDEO-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties voor Beheren, met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer in de sectie **Beheerdersreferenties** de waarden bij **Basis-URL en toegangstoken voor SCIM 2.0** die eerder zijn opgehaald bij het ondersteuningsteam van IDEO, in bij respectievelijk de velden **Tenant-URL** en **Token voor geheim**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met IDEO. Als de verbinding mislukt, controleert u of het IDEO-account beheerdersmachtigingen heeft. Probeer het vervolgens opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met IDEO**.

9. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD zijn gesynchroniseerd met IDEO. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in IDEO te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |actief|Booleaans|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met IDEO**.
   
11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die zijn gesynchroniseerd vanuit Azure AD naar IDEO. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in IDEO te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |leden|Naslaginformatie|

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig in de sectie **Instellingen** de **Inrichtingsstatus** in **Aan** om de Azure AD-inrichtingsservice in te schakelen voor IDEO.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt toevoegen aan IDEO, door in de sectie **Instellingen** de gewenste waarden te kiezen bij **Bereik**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.  

## <a name="change-log"></a>Wijzigingenlogboek

* 15-6-2020 - Toegevoegde ondersteuning voor het gebruik van PATCH-bewerkingen voor groepen, in plaats van PUT-bewerkingen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)