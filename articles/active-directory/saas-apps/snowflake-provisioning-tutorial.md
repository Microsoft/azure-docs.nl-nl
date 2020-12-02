---
title: 'Zelfstudie: Snowflake configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Snowflake.
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
ms.openlocfilehash: ac6981d998f25b8df7cea553fe152d68cd43fee5
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181421"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Zelfstudie: Snowflake configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is het demonstreren van de stappen die moeten worden uitgevoerd in Snowflake en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor [Snowflake](https://www.Snowflake.com/pricing/). Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


> [!NOTE]
> Deze connector is momenteel beschikbaar in openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Snowflake
> * Gebruikers uit Snowflake verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Snowflake
> * Groepen en groepslidmaatschappen inrichten in Snowflake
> * [Eenmalige aanmelding](./snowflake-tutorial.md) bij Snowflake (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md).
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder).
* [Een Snowflake-tenant](https://www.Snowflake.com/pricing/).
* Een gebruikersaccount in Snowflake met beheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Snowflake](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>Stap 2. Snowflake configureren om ondersteuning te bieden voor inrichting met Azure AD

Voordat u Snowflake configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM-inrichting inschakelen in Snowflake.

1. Meld u aan bij de beheerconsole van Snowflake. Voer de onderstaande query in het gemarkeerde werkblad in en klik op **Uitvoeren**.

    ![Beheerconsole van Snowflake](media/Snowflake-provisioning-tutorial/image00.png)

2.  Er wordt een SCIM-toegangstoken gegenereerd voor uw Snowflake-tenant. Klik op de gemarkeerde koppeling hieronder om het token op te halen.

    ![Schermopname van een werkblad in de gebruikersinterface van Snowflake met het SCIM-toegangstoken.](media/Snowflake-provisioning-tutorial/image01.png)

3. Kopieer de waarde van het gegenereerde token en klik op **Gereed**. Deze waarde moet u later invoeren in het veld **Token voor geheim** op het tabblad Inrichten van de Snowflake-toepassing in Azure Portal.

    ![Schermopname van de sectie Details waarin het token is gekopieerd naar het tekstveld en de optie Gereed is uitgelicht.](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>Stap 3. Snowflake toevoegen vanuit de Azure AD-toepassingsgalerie

Voeg Snowflake toe vanuit de Azure AD-toepassingsgalerie om de inrichting in Snowflake te beheren. Als u Snowflake eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan Snowflake, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>Stap 5. Automatische gebruikersinrichting configureren voor Snowflake 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Snowflake te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Snowflake in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Snowflake** in de lijst met toepassingen.

    ![De koppeling naar Snowflake in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. In de sectie Referenties voor beheerder voert u de waarden van **SCIM 2.0 basis-URL en Verificatietoken** in die eerder zijn opgehaald in respectievelijk **Tenant-URL** en **Token voor geheim**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Snowflake. Als de verbinding mislukt, moet u controleren of uw Snowflake-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

8. Klik op **Opslaan**.

9. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Snowflake**.

10. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Snowflake worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Snowflake te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |actief|Booleaans|
   |displayName|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |userName|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultRole|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:defaultWarehouse|Tekenreeks|

11. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Snowflake**.

12. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met Snowflake worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in Snowflake te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |leden|Naslaginformatie|

13. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Snowflake.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

15. Definieer de gebruikers en/of groepen die u aan Snowflake wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen**. Als deze optie niet beschikbaar is, configureert u de vereiste velden onder Referenties voor beheerder, klikt u op **Opslaan** en vernieuwt u de pagina. 

    ![Inrichtingsbereik](common/provisioning-scope.png)

16. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

    Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.  

## <a name="connector-limitations"></a>Connectorbeperkingen

* In Snowflake gegenereerde SCIM-tokens verlopen na 6 maanden. Houd er rekening mee dat deze moeten worden vernieuwd voordat deze verlopen om de inrichtingssynchronisaties te kunnen blijven gebruiken. 

## <a name="change-log"></a>Wijzigingslogboek

* 21-07-2020: Voorlopig verwijderen ingeschakeld voor alle gebruikers (via het kenmerk Actief).

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccounts inrichten voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md).