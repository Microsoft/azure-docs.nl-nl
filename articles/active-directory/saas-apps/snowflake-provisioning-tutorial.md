---
title: 'Zelfstudie: Snowflake configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op sneeuw.
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
ms.openlocfilehash: 06f11763498e3e8393d688a71e1c37b466be3f6f
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539532"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Zelfstudie: Snowflake configureren voor automatische gebruikersinrichting

In deze zelf studie ziet u de stappen die u in sneeuw en Azure Active Directory (Azure AD) uitvoert om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en groepen op [sneeuw](https://www.Snowflake.com/pricing/). Zie [Wat is geautomatiseerd SaaS app User Provisioning in azure AD?](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen. 

> [!NOTE]
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de gebruiks voorwaarden.

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Snowflake
> * Gebruikers verwijderen in sneeuw vlokken wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Snowflake
> * Groepen en groepslidmaatschappen inrichten in Snowflake
> * [Eenmalige aanmelding](./snowflake-tutorial.md) toestaan voor sneeuw vlokken (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md)
* Een gebruikers account in azure AD met [toestemming](../roles/permissions-reference.md) voor het configureren van inrichting (toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder)
* [Een sneeuw-Tenant](https://www.Snowflake.com/pricing/)
* Een gebruikers account in sneeuw met beheerders machtigingen

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1: plan uw inrichtings implementatie
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Snowflake](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>Stap 2: sneeuw configureren ter ondersteuning van inrichting met Azure AD

Voordat u sneeuw kunt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u systeem inschakelen voor SCIM-inrichting (Cross-Domain Identity Management) op sneeuw vlokken.

1. Meld u aan bij uw sneeuw-beheer console. Voer de volgende query in het gemarkeerde werk blad in en selecteer vervolgens **uitvoeren**.

    ![Scherm afbeelding van de beheer console van sneeuw met de knop query en uitvoeren.](media/Snowflake-provisioning-tutorial/image00.png)

2.  Er wordt een SCIM-toegangs token gegenereerd voor uw sneeuw-Tenant. Als u deze wilt ophalen, selecteert u de koppeling die is gemarkeerd in de volgende scherm afbeelding.

    ![Scherm afbeelding van een werk blad in de sneeuw U I met het toegangs token S C I M.](media/Snowflake-provisioning-tutorial/image01.png)

3. Kopieer de gegenereerde token waarde en selecteer **gereed**. Deze waarde wordt ingevoerd in het vak **geheime token** op het tabblad **inrichten** van uw sneeuw-toepassing in de Azure Portal.

    ![Scherm afbeelding van de sectie Details, waarin het token wordt gekopieerd naar het tekst veld en de optie gereed is aangeroepen.](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>Stap 3: sneeuw toevoegen vanuit de Azure AD-toepassings galerie

Voeg Snowflake toe vanuit de Azure AD-toepassingsgalerie om de inrichting in Snowflake te beheren. Als u voor het eerst een sneeuw hebt ingesteld voor eenmalige aanmelding (SSO), kunt u dezelfde toepassing gebruiken. We raden u echter aan om een afzonderlijke app te maken wanneer u de integratie voor het eerst test. [Meer informatie over het toevoegen van een toepassing uit de galerie](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4: definiëren wie binnen het bereik van de inrichting valt 

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing, of op basis van kenmerken van de gebruiker of groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de [stappen gebruiken om gebruikers en groepen toe te wijzen aan de toepassing](../manage-apps/assign-user-or-group-access-portal.md). Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u [een bereik filter gebruiken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Houd de volgende tips in acht:

* Wanneer u gebruikers en groepen toewijst aan sneeuw, moet u een andere rol dan standaard toegang selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om meer rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [op kenmerken gebaseerd bereik filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>Stap 5: automatische gebruikers inrichting op sneeuw configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en groepen in sneeuw te maken, bij te werken en uit te scha kelen. U kunt de configuratie baseren op toewijzingen van gebruikers en groepen in azure AD.

Automatische gebruikersinrichting configureren voor Snowflake in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Schermopname van het deelvenster Ondernemingstoepassing.](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **sneeuw vlokken**.

    ![Scherm opname van een lijst met toepassingen.](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel **Inrichtingsmodus** in op **Automatisch**.

    ![Scherm afbeelding van de vervolg keuzelijst voor de inrichtings modus met de automatische optie aangeroepen.](common/provisioning-automatic.png)

5. Voer in de sectie **beheerders referenties** de scim 2,0-basis-URL en het verificatie token in dat u eerder in de vakken **Tenant-URL** en **geheime token** hebt opgehaald. 

   Selecteer **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met sneeuw vlokken. Als de verbinding mislukt, zorg er dan voor dat uw sneeuw-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Scherm opname van de vakken voor Tenant U R L en geheim token, samen met de knop verbinding testen.](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het vak **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de meldingen voor de inrichtingsfouten moeten ontvangen. Schakel vervolgens het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![Scherm afbeelding van de vakken voor de meldings-e-mail.](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met sneeuw vlokken**.

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Snowflake worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Snowflake te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

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

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met sneeuw vlokken**.

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met Snowflake worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in Snowflake te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    |Kenmerk|Type|
    |---|---|
    |displayName|Tekenreeks|
    |leden|Naslaginformatie|

12. Raadpleeg de instructies in de [Zelfstudie bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) als u bereikfilters wilt configureren.

13. Als u de Azure AD-inrichtings service voor sneeuw wilt inschakelen, wijzigt **u de** **inrichtings status** in in het gedeelte **instellingen** .

     ![Scherm opname waarin de inrichtings status wordt weer gegeven.](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en groepen die u wilt inrichten voor sneeuw door de gewenste waarden in het **bereik** in het gedeelte **instellingen** te kiezen. 

    Als deze optie niet beschikbaar is, configureert u de vereiste velden onder **beheerders referenties**, selecteert u **Opslaan** en vernieuwt u de pagina. 

     ![Scherm opname van de opties voor het inrichtings bereik.](common/provisioning-scope.png)

15. Selecteer **Opslaan** als u klaar bent om in te richten.

     ![Scherm afbeelding van de knop voor het opslaan van een inrichtings configuratie.](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. Het uitvoeren van de initiële synchronisatie duurt langer dan bij volgende synchronisaties. Volgende synchronisaties worden ongeveer elke 40 minuten uitgevoerd, zolang de Azure AD-inrichtings service wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6: uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

- Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht.
- Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid.
- Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. Meer [informatie over quarantaine statussen](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Connectorbeperkingen

Door sneeuw gegenereerde SCIM-tokens verlopen over zes maanden. Houd er rekening mee dat u deze tokens moet vernieuwen voordat ze verlopen, zodat de inrichtings synchronisaties kunnen blijven werken. 

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

De Azure AD-inrichtings service werkt momenteel met bepaalde [IP-bereiken](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges). Indien nodig kunt u andere IP-adresbereiken beperken en deze specifieke IP-bereiken toevoegen aan de acceptatie lijst van uw toepassing. Met deze techniek is verkeer stroom van de Azure AD-inrichtings service naar uw toepassing mogelijk.

## <a name="change-log"></a>Wijzigingenlogboek

* 07/21/2020: zacht verwijderen ingeschakeld voor alle gebruikers (via het actieve kenmerk).

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccounts inrichten voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Single sign-on to applications in Azure Active Directory](../manage-apps/what-is-single-sign-on.md) (Eenmalige aanmelding bij toepassingen met Azure Active Directory)

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
