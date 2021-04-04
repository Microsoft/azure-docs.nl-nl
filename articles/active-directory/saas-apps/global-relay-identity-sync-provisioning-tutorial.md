---
title: 'Zelfstudie: Global Relay Identity Sync configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over de automatische inrichting van gebruikersaccounts van Azure AD in Global Relay Identity Sync, of de inrichting van gebruikersaccounts ongedaan te maken.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 0c4a3bf0-d0a6-4eab-909b-6cf9f9234e4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: Zhchia
ms.openlocfilehash: d003a512ebde626b8726dfccc58110e53f1cd467
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180911"
---
# <a name="tutorial-configure-global-relay-identity-sync-for-automatic-user-provisioning"></a>Zelfstudie: Global Relay Identity Sync configureren voor automatische inrichting van gebruikers

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel Global Relay Identity Sync als Azure AD (Azure Active Directory) voor het configureren van automatische inrichting van gebruikers. Wanneer de configuratie is voltooid, wordt in Azure AD de inrichting en ongedaan maken van de inrichting van gebruikers en groepen automatisch uitgevoerd in Global Relay Identity Sync met behulp van de Azure AD-inrichtingsservice. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Global Relay Identity Sync
> * Gebruikers verwijderen uit Global Relay Identity Sync wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Global Relay Identity Sync
> * Groepen en groepslidmaatschappen inrichten in Global Relay Identity Sync


> [!NOTE]
> Global Relay Identity Sync-inrichting maakt gebruik van een SCIM-autorisatiemodel dat vanwege beveiligingsproblemen niet meer wordt ondersteund. Er wordt momenteel aan gewerkt om Global Relay over te schakelen naar een veiligere autorisatiemethode.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld Toepassingsbeheerder, Cloudtoepassingsbeheerder, Toepassingseigenaar of Globale beheerder).

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Global Relay Identity Sync](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-global-relay-identity-sync-to-support-provisioning-with-azure-ad"></a>Stap 2. Global Relay Identity Sync configureren om ondersteuning te bieden voor inrichting met Azure AD

Neem contact op met de Global Relay Identity Sync-vertegenwoordiger om de tenant-URL te ontvangen. Deze waarde wordt ingevoerd in het veld **Token voor geheim** op het tabblad Inrichten van de Global Relay Identity Sync-toepassing in Azure Portal.

## <a name="step-3-add-global-relay-identity-sync-from-the-azure-ad-application-gallery"></a>Stap 3. Global Relay Identity Sync toevoegen vanuit de Azure AD-toepassingsgalerie

Voeg Global Relay Identity Sync toe vanuit de Azure AD-toepassingsgalerie om de inrichting in Global Relay Identity Sync te beheren. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-global-relay-identity-sync"></a>Stap 5. Automatische inrichting van gebruiker configureren in Global Relay Identity Sync 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen te maken, bij te werken en uit te schakelen in de Global Relay Identity Sync-app, op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-global-relay-identity-sync-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor Global Relay Identity Sync in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Global Relay Identity Sync** in de lijst met toepassingen.

    ![De Global Relay Identity Sync-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichting - Automatisch](common/provisioning-automatic.png)

5. Voer in de sectie **Beheerdersreferenties** de **Tenant-URL** voor Global Relay Identity Sync uit. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Global Relay Identity Sync. Als de verbinding mislukt, controleert u of het Global Relay Identity Sync-account beheerdersmachtigingen heeft en neemt u contact op met Global Relay-vertegenwoordiger om het probleem op te lossen.

    ![Knop Autorisatie](media/global-relay-identity-sync-provisioning-tutorial/authorization.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Global Relay Identity Sync**.

9. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD zijn gesynchroniseerd met Global Relay Identity Sync. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Global Relay Identity Sync te vinden voor updatebewerkingen. Als u ervoor kiest om het [overeenkomende doelkenmerk](../app-provisioning/customize-application-attributes.md) te wijzigen, moet u ervoor zorgen dat de API voor Global Relay Identity Sync ondersteuning biedt voor het filteren van gebruikers op basis van dit kenmerk. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |actief|Booleaans|
   |displayName|Tekenreeks|
   |title|Tekenreeks|
   |preferredLanguage|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |name.formatted|Tekenreeks|
   |addresses[type eq "work"].formatted|Tekenreeks|
   |addresses[type eq "work"].streetAddress|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |addresses[type eq "work"].locality|Tekenreeks|
   |addresses[type eq "work"].region|Tekenreeks|
   |addresses[type eq "work"].postalCode|Tekenreeks|
   |addresses[type eq "work"].country|Tekenreeks|
   |addresses[type eq "other"].formatted|Tekenreeks|
   |phoneNumbers[type eq "work"].value|Tekenreeks|
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|
   |phoneNumbers[type eq "fax"].value|Tekenreeks|
   |externalId|Tekenreeks|
   |name.honorificPrefix|Tekenreeks|
   |name.honorificSuffix|Tekenreeks|
   |nickName|Tekenreeks|
   |userType|Tekenreeks|
   |landinstelling|Tekenreeks|
   |timezone|Tekenreeks|
   |emails[type eq "home"].value|Tekenreeks|
   |emails[type eq "other"].value|Tekenreeks|
   |phoneNumbers[type eq "home"].value|Tekenreeks|
   |phoneNumbers[type eq "other"].value|Tekenreeks|
   |phoneNumbers[type eq "pager"].value|Tekenreeks|
   |addresses[type eq "home"].streetAddress|Tekenreeks|
   |addresses[type eq "home"].locality|Tekenreeks|
   |addresses[type eq "home"].region|Tekenreeks|
   |addresses[type eq "home"].postalCode|Tekenreeks|
   |addresses[type eq "home"].country|Tekenreeks|
   |addresses[type eq "home"].formatted|Tekenreeks|
   |addresses[type eq "other"].streetAddress|Tekenreeks|
   |addresses[type eq "other"].locality|Tekenreeks|
   |addresses[type eq "other"].region|Tekenreeks|
   |addresses[type eq "other"].postalCode|Tekenreeks|
   |addresses[type eq "other"].country|Tekenreeks|
   |roles[primary eq "True"].display|Tekenreeks|
   |roles[primary eq "True"].type|Tekenreeks|
   |roles[primary eq "True"].value|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Naslaginformatie|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:proxyAddresses|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute1|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute2|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute3|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute4|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute5|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute6|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute7|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute8|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute9|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute10|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute11|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute12|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute13|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute14|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:GlobalRelay:2.0:User:extensionAttribute15|Tekenreeks|



10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Global Relay Identity Sync**.

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die zijn gesynchroniseerd vanuit Azure AD naar Global Relay Identity Sync. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in Global Relay Identity Sync te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |leden|Naslaginformatie|

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig in de sectie **Instellingen** de **Inrichtingsstatus** in **Aan** om de Azure AD-inrichtingsservice in te schakelen voor Global Relay Identity Sync.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt toevoegen aan Global Relay Identity Sync, door in de sectie **Instellingen** de gewenste waarden te kiezen bij **Bereik**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.  

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)