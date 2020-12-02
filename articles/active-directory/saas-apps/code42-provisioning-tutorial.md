---
title: 'Zelfstudie: Code42 configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikersaccounts van Azure AD voor Code42.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ddcb950b-3f9a-4ebb-bf78-4ec42d16d52d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/23/2020
ms.author: Zhchia
ms.openlocfilehash: ffae286437a476d5ebe08c3ee442d0d52af84eb7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96179335"
---
# <a name="tutorial-configure-code42-for-automatic-user-provisioning"></a>Zelfstudie: Code42 configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel Code42 als Azure Active Directory (Azure AD) voor het configureren van automatische inrichting van gebruikers. Wanneer de configuratie is voltooid, wordt inrichting en ongedaan maken van inrichting van gebruikers en groepen door Azure AD automatisch uitgevoerd op [Code42](https://www.crashplan.com/) met behulp van de Azure AD-inrichtingsservice. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Code42
> * Gebruikers uit Code42 verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Code42
> * Groepen en groepslidmaatschappen inrichten in Code42
> * [Eenmalige aanmelding](./code42-tutorial.md) bij Code42 (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md)
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld Toepassingsbeheerder, Cloudtoepassingsbeheerder, Toepassingseigenaar of Globale beheerder).
* Een Code42-tenant met Identiteitsbeheer ingeschakeld.
* Een Code42-gebruikersaccount met de machtiging [Customer Cloud Admin](https://support.code42.com/Administrator/Cloud/Monitoring_and_managing/Roles_reference#Customer_Cloud_Admin) (Cloudbeheerder van klant).

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Code42](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-code42-to-support-provisioning-with-azure-ad"></a>Stap 2. Code42 configureren om ondersteuning te bieden voor inrichting met Azure AD

In deze sectie wordt u begeleid bij de stappen voor het configureren van Azure AD als inrichtingsprovider in de sectie Identity Management (Identiteitsbeheer) van de console van Code42. Als u dit doet, kan Code42 veilig inrichtingsaanvragen ontvangen van Azure AD. U wordt aangeraden de [documentatie voor ondersteuning van Code42](https://support.code42.com/Administrator/Cloud/Configuring/Introduction_to_SCIM_provisioning/How_to_provision_users_to_Code42_from_Azure_AD) te bekijken voordat u het product inricht met Azure AD.

### <a name="to-create-a-provisioning-provider-in-code42s-console"></a>Een inrichtingsprovider maken in de console van Code42:

1. Meld u aan bij uw Code42-console. Selecteer **Administration** (Beheer) om het navigatiemenu uit te vouwen. Selecteer **Settings** (Instellingen) en **Identity Management** (Identiteitsbeheer).
2. Selecteer het tabblad **Inrichten**. Vouw vervolgens het menu **Add provisioning provider** (Inrichtingsprovider toevoegen) uit en selecteer **Add SCIM provider** (SCIM-provider toevoegen).
3. Voer in het veld **Display name** (Weergavenaam) een unieke naam in voor de inrichtingsprovider. Stel **Authentication credential type** (type referenties voor verificatie) in op **OAuth-token**. Selecteer **Next** (Volgende) om referenties te genereren.

> [!NOTE]
>* Houd dit venster open totdat u wordt gevraagd om de **basis-URL** en het **token**. Deze gegevens zijn vereist voor de volgende stappen.
>* U kunt deze informatie ook naar een tijdelijke locatie kopiëren voor later gebruik.

## <a name="step-3-add-code42-from-the-azure-ad-application-gallery"></a>Stap 3. Code42 toevoegen vanuit de Azure AD-toepassingsgalerie

Voeg Code42 toe vanuit de Azure AD-toepassingsgalerie om de inrichting in Code42 te beheren. Als u Code42 eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven.

* Wanneer u gebruikers en groepen toewijst aan Code42, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen.

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven.


## <a name="step-5-configure-automatic-user-provisioning-to-code42"></a>Stap 5. Automatische gebruikersinrichting configureren voor Code42

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in BlogIn te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-code42-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Code42 in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Code42** in de lijst met toepassingen.

    ![De koppeling Code42 in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![De inrichtingsmodus ingesteld op Automatisch](common/provisioning-automatic.png)

5. In het gedeelte **Beheerdersreferenties** voert u de waarden voor **SCIM 2.0 basis-URL en toegangstoken** in die eerder zijn opgehaald uit Code42 in respectievelijk **Tenant-URL** en **Token voor geheim**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Code42. Als de verbinding mislukt, moet u controleren of uw Code42-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Code42**.

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Code42 worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Code42 te vinden voor updatebewerkingen. Als u ervoor kiest om het [overeenkomende doelkenmerk](../app-provisioning/customize-application-attributes.md) te wijzigen, moet u ervoor zorgen dat de API van Code42 het filteren van gebruikers op basis van dat kenmerk kan ondersteunen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |actief|Booleaans|
   |title|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |addresses[type eq "work"].locality|Tekenreeks|
   |addresses[type eq "work"].region|Tekenreeks|
   |addresses[type eq "work"].country|Tekenreeks|
   |externalId|Tekenreeks|
   |userType|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Naslaginformatie|

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Code42**.

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die worden gesynchroniseerd vanuit Azure AD naar Code42. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in Code42 te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |externalId|Tekenreeks|
      |leden|Naslaginformatie|

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Code42.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Code42 wilt toevoegen door de gewenste waarden te kiezen bij **Bereik** in de sectie **Instellingen**.

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
* [Organisatietoewijzingen op basis van SCIM-groepen in Code42 configureren](https://support.code42.com/Administrator/Cloud/Configuring/Introduction_to_SCIM_provisioning/How_to_provision_users_to_Code42_from_Azure_AD#Step_6:_Choose_an_organization_mapping_method)
* [Roltoewijzingen op basis van SCIM-groepen in Code42 configureren](https://support.code42.com/Administrator/Cloud/Configuring/Introduction_to_SCIM_provisioning/How_to_provision_users_to_Code42_from_Azure_AD#Step_7:_Configure_role_mapping)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)