---
title: 'Zelfstudie: Fuze configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Fuze.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2fcb85b2c16bc2dd418c2260420733dd8663382a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96179249"
---
# <a name="tutorial-configure-fuze-for-automatic-user-provisioning"></a>Zelfstudie: Fuze configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Fuze en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor [Fuze](https://www.fuze.com/). Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Fuze
> * Gebruikers verwijderen uit Fuze wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Fuze
> * [Eenmalige aanmelding](./fuze-tutorial.md) bij Fuze (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md).
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder).
* [Een Fuze-tenant](https://www.fuze.com/).
* Een gebruikersaccount in Fuze met beheerdersmachtigingen


## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Fuze](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-fuze-to-support-provisioning-with-azure-ad"></a>Stap 2. Fuze configureren ter ondersteuning van het inrichten met Azure AD

Voordat u Fuze configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM-inrichting inschakelen in Fuze. 

1. Neem contact op met uw accountmanager van Fuze voor de volgende vereiste informatie:

    * Lijst met SKU's van Fuze-producten die momenteel in gebruik zijn in uw bedrijf.
    * Lijst met locatiecodes voor de locaties van uw bedrijf.
    * Lijst met afdelingscodes voor uw bedrijf.

2. U kunt deze SKU's en codes vinden in het contract dat met Fuze is afgesloten en in de configuratiedocumenten. Desgewenst kunt u ook contact opnemen met uw accountmanager van Fuze.

3. Zodra u over de juiste informatie beschikt, krijgt u van uw accountmanager het Fuze-verificatietoken dat vereist is om de integratie in te schakelen. Dit token moet u later invoeren in het veld Token voor geheim op het tabblad Inrichten van uw Fuze-toepassing in Azure Portal.

## <a name="step-3-add-fuze-from-the-azure-ad-application-gallery"></a>Stap 3. Fuze toevoegen vanuit de galerie met Azure AD-toepassingen

Voeg Fuze toe vanuit de galerie met Azure AD-toepassingen om te beginnen met het inrichten voor Fuze. Als u Fuze eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers toewijst aan Fuze, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test met een kleine set gebruikers voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers, kunt u dit beheren door een of twee gebruikers aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 

## <a name="step-5-configuring-automatic-user-provisioning-to-fuze"></a>Stap 5. Automatische gebruikersinrichting voor Fuze configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Fuze te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Fuze in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Fuze** in de lijst met toepassingen.

    ![Fuze in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. In het gedeelte **Referenties voor beheerder** voert u in de velden **Tenant-URL** en **Token voor geheim** respectievelijk de **basis-URL en het toegangstoken voor SCIM 2.0** in die u eerder hebt opgehaald uit Fuze. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Fuze. Als de verbinding mislukt, moet u controleren of uw Fuze-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Fuze**.

9. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD worden gesynchroniseerd met Fuze. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Fuze te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |actief|Booleaans|

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Fuze.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u aan Fuze wilt toevoegen door de gewenste waarden te kiezen bij **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.

## <a name="connector-limitations"></a>Connectorbeperkingen

* Fuze ondersteunt aangepaste SCIM-kenmerken met de naam **Entitlements**. Deze kenmerken kunnen alleen worden gemaakt en niet worden bijgewerkt. 

## <a name="change-log"></a>Wijzigingenlogboek

* 15-06-2020 Limiet van integratie aangepast naar 10 aanvragen/seconde.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccounts inrichten voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md).