---
title: 'Zelfstudie: Clarizen One configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikersaccounts van Azure AD naar Clarizen One.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: f3a19d3c3bf3e4340bb36fd683453541fa15eb6c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650825"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>Zelfstudie: Clarizen One configureren voor automatische inrichting van gebruikers

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel Clarizen One als Azure Active Directory (Azure AD) voor het configureren van automatische inrichting van gebruikers. Wanneer de configuratie is voltooid, wordt inrichting en ongedaan maken van inrichting van gebruikers en groepen door Azure AD automatisch uitgevoerd op [Clarizen One](https://www.clarizen.com/) met behulp van de Azure AD-inrichtingsservice. Raadpleeg [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen (software-as-a-service) met Azure AD](../app-provisioning/user-provisioning.md) voor informatie over wat deze service doet, hoe het werkt en veelgestelde vragen.

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden

> [!div class="checklist"]
> * Gebruikers maken in Clarizen One.
> * Gebruikers uit Clarizen One verwijderen wanneer ze geen toegang meer nodig hebben.
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Clarizen One.
> * Groepen en groepslidmaatschappen inrichten in Clarizen One.
> * [Eenmalige aanmelding (SSO)](./clarizen-tutorial.md) bij Clarizen One wordt aanbevolen.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md).
* Een gebruikersaccount in Azure AD met [machtiging](../roles/permissions-reference.md) om inrichting te configureren. Voorbeelden zijn Toepassingsbeheerder, Cloudtoepassingsbeheerder, Toepassingseigenaar of Globale beheerder.
* Een gebruikersaccount in Clarizen One met **Integratiegebruikers**- en **Lite-beheerders**-[machtigingen](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support).

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen

1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
1. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Clarizen One](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>Stap 2. Clarizen One configureren ter ondersteuning van het inrichten met Azure AD

1. Selecteer een van de vier volgende tenant-URL's volgens uw Clarizen One-omgeving en datacentrum:
      * Productiedatacentrum VS: https://servicesapp2.clarizen.com/scim/v2
      * Productiedatacentrum EU: https://serviceseu1.clarizen.com/scim/v2
      * Sandbox-datacentrum VS: https://servicesapp.clarizentb.com/scim/v2
      * Sandbox-datacentrum EU: https://serviceseu.clarizentb.com/scim/v2

1. Genereer een [API-sleutel](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support). Deze waarde wordt ingevoerd in het vak **Token voor geheim** op het tabblad **Inrichten** van uw Clarizen One-toepassing in Azure Portal.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>Stap 3. Clarizen One toevoegen vanuit de galerie met Azure AD-toepassingen

Voeg Clarizen One toe vanuit de galerie met Azure AD-toepassingen om te beginnen met het inrichten voor Clarizen One. Als u Clarizen One eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. Wanneer u de integratie in eerste instantie wilt testen, maakt u een afzonderlijke app. Zie [Een toepassing toevoegen aan uw Azure AD-tenant](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing vanuit de galerie.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing of op basis van kenmerken van de gebruiker of groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, volgt u de stappen in [Gebruikerstoewijzing voor een app beheren in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, gebruikt u een bereikfilter zoals beschreven in [Op kenmerk gebaseerde toepassingsinrichting met bereikfilters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Wanneer u gebruikers en groepen toewijst aan Clarizen One, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om meer rollen toe te voegen.
* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit blijven beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven.

## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>Stap 5. Automatische gebruikersinrichting configureren voor Clarizen One

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

### <a name="configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor Clarizen One in Azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

      ![Schermopname van het deelvenster Ondernemingstoepassing.](common/enterprise-applications.png)

1. Selecteer **Clarizen One** in de lijst met toepassingen.

      ![Schermopname van de Clarizen One-link in de lijst Toepassingen.](common/all-applications.png)

1. Selecteer het tabblad **Inrichten**.

      ![Schermopname van het tabblad Inrichten.](common/provisioning.png)

1. Stel **Inrichtingsmodus** in op **Automatisch**.

      ![Schermopname van de optie Automatisch op het tabblad Inrichten.](common/provisioning-automatic.png)

1. Voer in de sectie **Beheerdersreferenties** de **Tenant-URL** en het **geheime token** van uw Clarizen One in. Selecteer **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Clarizen One. Als de verbinding mislukt, moet u controleren of uw Clarizen One-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Schermopname met het vak Geheim token.](common/provisioning-testconnection-tenanturltoken.png)

1. Voer in het vak **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de meldingen voor de inrichtingsfouten moeten ontvangen. Selecteer het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**.

    ![Schermopname van het vak E-mailmelding.](common/provisioning-notification-email.png)

1. Selecteer **Opslaan**.

1. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Clarizen One**.

1. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Clarizen One worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Clarizen One te vinden voor updatebewerkingen. Als u het [overeenkomende doelkenmerk](../app-provisioning/customize-application-attributes.md) wijzigt, moet u ervoor zorgen dat de API van Clarizen One het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |displayName|Tekenreeks|
   |actief|Booleaans|
   |title|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |emails[type eq "home"].value|Tekenreeks|
   |emails[type eq "other"].value|Tekenreeks|
   |preferredLanguage|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |name.formatted|Tekenreeks|
   |name.honorificPrefix|Tekenreeks|
   |name.honorificSuffix|Tekenreeks|
   |addresses[type eq "other"].formatted|Tekenreeks|
   |addresses[type eq "work"].formatted|Tekenreeks|
   |addresses[type eq "work"].country|Tekenreeks|
   |addresses[type eq "work"].region|Tekenreeks|
   |addresses[type eq "work"].locality|Tekenreeks|
   |addresses[type eq "work"].postalCode|Tekenreeks|
   |addresses[type eq "work"].streetAddress|Tekenreeks|
   |phoneNumbers[type eq "work"].value|Tekenreeks|
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|
   |phoneNumbers[type eq "fax"].value|Tekenreeks|
   |phoneNumbers[type eq "home"].value|Tekenreeks|
   |phoneNumbers[type eq "other"].value|Tekenreeks|
   |phoneNumbers[type eq "pager"].value|Tekenreeks|
   |externalId|Tekenreeks|
   |nickName|Tekenreeks|
   |landinstelling|Tekenreeks|
   |rollen [Primary EQ "True". type]|Tekenreeks|
   |rollen [Primary EQ "True". waarde]|Tekenreeks|
   |timezone|Tekenreeks|
   |userType|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Naslaginformatie|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Tekenreeks|

1. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Clarizen One**.

1. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met Clarizen One worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in Clarizen One te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |externalId|Tekenreeks|
      |leden|Naslaginformatie|

1. Raadpleeg de instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) als u bereikfilters wilt configureren.

1. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Clarizen One.

      ![Schermopname van de Inrichtingsstatus ingeschakeld.](common/provisioning-toggle-on.png)

1. Definieer de gebruikers of groepen die u aan Clarizen One wilt toevoegen door de gewenste waarden te selecteren in **Bereik** in de sectie **Instellingen**.

      ![Schermopname van het inrichtingsbereik.](common/provisioning-scope.png)

1. Selecteer **Opslaan** als u klaar bent voor het inrichten.

      ![Schermopname van het opslaan van de inrichtingsconfiguratie.](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken

Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken.

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht.
1. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid.
1. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. Zie [Toepassing inrichten in quarantainestatus](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

Wanneer u een gebruiker toewijst aan de Clarizen One-galerie-app, selecteert u alleen de rol **Gebruiker**. De volgende rollen zijn ongeldig:

* Beheerder (administrator)
* Gebruiker van e-mailrapporten
* Externe gebruiker
* Financiële gebruiker
* Sociale gebruiker
* Superuser
* Gebruiker Tijd & onkosten

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccounts inrichten voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)