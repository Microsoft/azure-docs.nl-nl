---
title: 'Zelf studie: Britive configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar Britive.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 622688b3-9d20-482e-aab9-ce2a1f01e747
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
ms.openlocfilehash: 8bebcb49bc7bf31614a161c08d33d5910679b614
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225727"
---
# <a name="tutorial-configure-britive-for-automatic-user-provisioning"></a>Zelf studie: Britive configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel Britive als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer de configuratie is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en [GeBritived](https://www.britive.com/) met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Britive
> * Gebruikers in Britive verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en Britive
> * Inrichtings groepen en groepslid maatschappen in Britive
> * [Eenmalige aanmelding](britive-tutorial.md) bij Britive (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld Toepassingsbeheerder, Cloudtoepassingsbeheerder, Toepassingseigenaar of Globale beheerder). 
* Een [Britive](https://www.britive.com/) -Tenant.
* Een gebruikers account in Britive met beheerders machtigingen.


## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
1. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en Britive](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-britive-to-support-provisioning-with-azure-ad"></a>Stap 2. Britive configureren voor ondersteuning bij het inrichten met Azure AD

De toepassing moet hand matig worden geconfigureerd met behulp van de onderstaande stappen:
1. Meld u aan bij de Britive-toepassing met beheerders bevoegdheden
1. Klik op **beheerder->gebruikers beheer->id-providers**
1. Klik op **ID-provider toevoegen**. Voer de naam en beschrijving in. Klik op de knop id-provider toevoegen.

    ![Identiteitsprovider](media/britive-provisioning-tutorial/identity.png)

1. Er wordt een configuratie pagina weer gegeven zoals hieronder wordt weer gegeven.

    ![Configuratie pagina](media/britive-provisioning-tutorial/configuration.png)

1. Klik op het tabblad **scim** . Wijzig de SCIM-provider van algemeen in Azure en sla de wijzigingen op. Kopieer de URL van de SCIM en noteer deze. Deze waarden worden ingevoerd in de vakken **Tenant-URL** op het tabblad inrichten van uw Britive-toepassing in de Azure Portal.

    ![SCIM-pagina](media/britive-provisioning-tutorial/scim.png)

1. Klik op **token maken**. Selecteer de geldigheid van het token, zoals vereist, en klik op de knop token maken.

    ![Token maken](media/britive-provisioning-tutorial/create-token.png)

1. Kopieer het gegenereerde token en noteer het. Klik op OK. De gebruiker kan het token niet opnieuw weer geven. Klik op Re-Create knop om een nieuw token te genereren, indien nodig. Deze waarden worden ingevoerd in de vakken **geheim token** en TENANT-URL op het tabblad inrichten van uw getAbstract-toepassing in de Azure Portal.

    ![Token kopiëren](media/britive-provisioning-tutorial/copy-token.png) 


## <a name="step-3-add-britive-from-the-azure-ad-application-gallery"></a>Stap 3. Britive toevoegen vanuit de Azure AD-toepassings galerie

Voeg Britive toe vanuit de Azure AD-toepassings galerie om het beheren van de inrichting van Britive te starten. Als u eerder Britive voor SSO hebt ingesteld, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan Britive, moet u een andere rol dan **standaard toegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-britive"></a>Stap 5. Automatische gebruikers inrichting configureren voor Britive 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in Britive te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-britive-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Britive in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **Britive** in de lijst met toepassingen.

    ![De koppeling Britive in de lijst met toepassingen](common/all-applications.png)

1. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

1. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![De inrichtingsmodus ingesteld op Automatisch](common/provisioning-automatic.png)

1. Geef in het gedeelte **beheerders referenties** de Britive-TENANT-URL en het geheime token op. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Britive. Als de verbinding mislukt, zorg er dan voor dat uw Britive-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

1. Selecteer **Opslaan**.

1. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Britive**.

1. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Britive in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Britive voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](../app-provisioning/customize-application-attributes.md)te wijzigen, moet u ervoor zorgen dat de BRITIVE-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;
   |actief|Booleaans|
   |displayName|Tekenreeks|
   |title|Tekenreeks|
   |externalId|Tekenreeks|
   |preferredLanguage|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |nickName|Tekenreeks|
   |userType|Tekenreeks|
   |landinstelling|Tekenreeks|
   |timezone|Tekenreeks|
   |emails[type eq "home"].value|Tekenreeks|
   |emails[type eq "other"].value|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |phoneNumbers[type eq "home"].value|Tekenreeks|
   |phoneNumbers[type eq "other"].value|Tekenreeks|
   |phoneNumbers[type eq "pager"].value|Tekenreeks|
   |phoneNumbers[type eq "work"].value|Tekenreeks|
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|
   |phoneNumbers[type eq "fax"].value|Tekenreeks|
   |addresses[type eq "work"].formatted|Tekenreeks|
   |addresses[type eq "work"].streetAddress|Tekenreeks|
   |addresses[type eq "work"].locality|Tekenreeks|
   |addresses[type eq "work"].region|Tekenreeks|
   |addresses[type eq "work"].postalCode|Tekenreeks|
   |addresses[type eq "work"].country|Tekenreeks|
   |addresses[type eq "home"].formatted|Tekenreeks|
   |addresses[type eq "home"].streetAddress|Tekenreeks|
   |addresses[type eq "home"].locality|Tekenreeks|
   |addresses[type eq "home"].region|Tekenreeks|
   |addresses[type eq "home"].postalCode|Tekenreeks|
   |addresses[type eq "home"].country|Tekenreeks|
   |addresses[type eq "other"].formatted|Tekenreeks|
   |addresses[type eq "other"].streetAddress|Tekenreeks|
   |addresses[type eq "other"].locality|Tekenreeks|
   |addresses[type eq "other"].region|Tekenreeks|
   |addresses[type eq "other"].postalCode|Tekenreeks|
   |addresses[type eq "other"].country|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Naslaginformatie|


1. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met Britive**.

1. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Britive in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in Britive te vergelijken voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|Ondersteund voor filteren|
      |---|---|---|
      |displayName|Tekenreeks|&check;
      |externalId|Tekenreeks|
      |leden|Naslaginformatie|

1. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Als u de Azure AD-inrichtings service voor **Britive wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

1. Definieer de gebruikers en/of groepen die u wilt inrichten voor Britive door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

1. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

* Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
* Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
* Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.  

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)