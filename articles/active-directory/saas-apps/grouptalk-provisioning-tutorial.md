---
title: 'Zelf studie: GroupTalk configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar GroupTalk.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: e537d393-2724-450f-9f5b-4611cdc9237c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 0af41127577c172cdab74ae908f0645733d49a42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735755"
---
# <a name="tutorial-configure-grouptalk-for-automatic-user-provisioning"></a>Zelf studie: GroupTalk configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel GroupTalk als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer de configuratie is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en [GeGroupTalkd](https://www.grouptalk.com/) met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in GroupTalk
> * Gebruikers in GroupTalk verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en GroupTalk
> * Inrichtings groepen en groepslid maatschappen in GroupTalk

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld Toepassingsbeheerder, Cloudtoepassingsbeheerder, Toepassingseigenaar of Globale beheerder). 
* Een gebruikers account in GroupTalk met beheerders machtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en GroupTalk](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-grouptalk-to-support-provisioning-with-azure-ad"></a>Stap 2. GroupTalk configureren voor ondersteuning bij het inrichten met Azure AD

1. Neem contact op met de GroupTalk support@grouptalk.com -ondersteuning met de **Tenant naam** en **-id** die u wilt integreren met Azure AD.
2. Wanneer u een melding krijgt dat de benodigde configuratie voor uw Azure AD-integratie is voltooid, meldt u zich aan bij de GroupTalk-beheerder en navigeert u naar de weer gave van uw organisatie. 
3. Een configuratie-item van Azure AD-integratie moet zichtbaar zijn. Klik erop om de naam en **id** van de **Tenant** te controleren voor het verkrijgen van een **JWT (geheim token)**. 
4. De GroupTalk-Tenant-URL is `https://api.grouptalk.com/api/scim/` . De **Tenant-URL** en het **geheime token** dat u in de vorige stap hebt opgehaald, worden ingevoerd op het tabblad inrichten van uw GroupTalk-toepassing in de Azure Portal. 

## <a name="step-3-add-grouptalk-from-the-azure-ad-application-gallery"></a>Stap 3. GroupTalk toevoegen vanuit de Azure AD-toepassings galerie

Voeg **GroupTalk** toe vanuit de Azure AD-toepassings galerie om het beheren van de inrichting van GroupTalk te starten.

1. Klik op de knop **registreren voor GroupTalk** , waarmee u wordt doorgestuurd naar de beheer toepassing GroupTalk.
2. Als u al bent aangemeld bij GroupTalk, meldt u zich af om naar het aanmeldings scherm te gaan. Selecteer het tabblad Azure AD en klik op de knop **Aanmelden** .

    ![GroupTalk](media/grouptalk-provisioning-tutorial/login.png)

3. Meld u aan met uw AD-beheerders account en accepteer de toegangs rechten van de GroupTalk-toepassing. Er wordt een fout bericht weer gegeven nadat u dit hebt gedaan, wat betekent dat de gebruiker niet aanwezig is. Dit wordt verwacht omdat uw gebruiker nog niet is ingericht voor GroupTalk, maar u nu GroupTalk hebt toegevoegd aan uw Tenant.
4. Ga terug naar de Azure Portal en controleer of **GroupTalk** nu is toegevoegd aan uw bedrijfs toepassingen.

Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan GroupTalk, moet u een andere rol dan **standaard toegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-grouptalk"></a>Stap 5. Automatische gebruikers inrichting configureren voor GroupTalk 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-grouptalk-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor GroupTalk in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **GroupTalk**.

    ![De koppeling GroupTalk in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![De inrichtingsmodus ingesteld op Automatisch](common/provisioning-automatic.png)

5. Voer in het gedeelte **beheerders referenties** de GroupTalk-TENANT-URL en het geheime token in dat u eerder uit stap 2 hebt opgehaald. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met GroupTalk. Als de verbinding mislukt, zorg er dan voor dat uw GroupTalk-account beheerders machtigingen heeft en probeer het opnieuw. U kunt altijd een nieuw geheim token verkrijgen, zoals beschreven in stap 2.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met GroupTalk**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar GroupTalk in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in GroupTalk voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](../app-provisioning/customize-application-attributes.md)te wijzigen, moet u ervoor zorgen dat de GROUPTALK-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|&check;|
   |emails[type eq "work"].value|Tekenreeks|&check;|
   |actief|Booleaans|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |externalId|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|Tekenreeks|
   |urn: IETF: params: scim: schemas: extension: grouptalk: 2.0: gebruiker: Label1|Tekenreeks|
   |urn: IETF: params: scim: schemas: extension: grouptalk: 2.0: User: Label2|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: grouptalk: 2.0: gebruiker: Label3|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: grouptalk: 2.0: gebruiker: Label4|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: grouptalk: 2.0: gebruiker: Label5|Tekenreeks|


10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met GroupTalk**.

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar GroupTalk in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in GroupTalk te vergelijken voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|Ondersteund voor filteren|
      |---|---|---|
      |displayName|Tekenreeks|&check;|
      |leden|Naslaginformatie|
      |externalId|Tekenreeks|      
      |urn: IETF: params: scim: schemas: extensie: grouptalk: 2.0: groep: beschrijving|Tekenreeks|

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Als u de Azure AD-inrichtings service voor **GroupTalk wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor GroupTalk door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.
4. U kunt contact opnemen met GroupTalk-ondersteuning voor aanvullende inrichtings logboeken die zijn ingesteld als aangepaste rapporten binnen GroupTalk admin. Dit kan extra hints geven waarom gebruikers en groepen niet op de juiste wijze zijn ingericht.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)