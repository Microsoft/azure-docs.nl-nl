---
title: 'Zelfstudie: SAP Analytics Cloud configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over de automatische inrichting van gebruikersaccounts van Azure AD in SAP Analytics Cloud, en het ongedaan maken ervan.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 27d12989-efa8-4254-a4ad-8cb6bf09d839
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2020
ms.author: Zhchia
ms.openlocfilehash: 31e5393cb5de627ebf8832e43302583d6eacbf59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181477"
---
# <a name="tutorial-configure-sap-analytics-cloud-for-automatic-user-provisioning"></a>Zelfstudie: SAP Analytics Cloud configureren voor automatische inrichting van gebruikers

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel SAP Analytics Cloud als Azure AD (Azure Active Directory) voor het configureren van automatische inrichting van gebruikers. Wanneer de configuratie is voltooid, wordt inrichting en ongedaan maken van inrichting van gebruikers en groepen in Azure AD automatisch uitgevoerd in [SAP Analytics Cloud](https://www.sapanalytics.cloud/) met behulp van de Azure AD-inrichtingsservice. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in SAP Analytics Cloud
> * Gebruikers verwijderen uit SAP Analytics Cloud wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en SAP Analytics Cloud
> * [Eenmalige aanmelding](sapboc-tutorial.md) bij SAP Analytics Cloud (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* Een SAP Analytics Cloud-tenant
* Een gebruikersaccount met beheerdersmachtigingen in de SAP-beheerconsole voor identiteitsinrichting. Zorg ervoor dat u toegang hebt tot de proxysystemen in de beheerconsole voor identiteitsinrichting. Als u de tegel **Proxysystemen** niet ziet, maakt u een incident voor onderdeel **BC-IAM-IPS** om toegang tot deze tegel aan te vragen.
* Een OAuth-client met clientreferenties voor het verlenen van autorisatie in SAP Analytics Cloud. Zie voor meer informatie: [OAuth-clients en vertrouwde id-providers beheren](https://help.sap.com/viewer/00f68c2e08b941f081002fd3691d86a7/release/en-US/4f43b54398fc4acaa5efa32badfe3df6.html)

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen

1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en SAP Analytics Cloud](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-sap-analytics-cloud-to-support-provisioning-with-azure-ad"></a>Stap 2. SAP Analytics Cloud configureren om ondersteuning te bieden voor inrichten met Azure AD

1. Meld u met uw beheerdersaccount aan bij de [SAP-beheerconsole voor identiteitsinrichting](https://ips-xlnk9v890j.dispatcher.us1.hana.ondemand.com/) en selecteer vervolgens **Proxysystemen**.

   ![SAP-proxysystemen](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems.png.png)

2. Selecteer **Eigenschappen**.

   ![Eigenschappen van SAP-proxysystemen](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-properties.png)

3. Kopieer de **URL** en voeg `/api/v1/scim` toe aan de URL. Sla dit op voor later gebruik in het veld **Tenant-URL**.

   ![Eigenschappen-URL van SAP-proxysystemen](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-url.png)

4. Gebruik [POSTMAN](https://www.postman.com/) om een POST HTTPS-aanroep toe te voegen aan het adres: `<Token URL>?grant_type=client_credentials` waarbij `Token URL` de URL is in het veld **OAuth2TokenServiceURL**. Deze stap is nodig om een toegangstoken te genereren dat kan worden gebruikt in het veld Token voor geheim, bij het configureren van automatische inrichting.

   ![Eigenschappen-OAuth van SAP-proxysystemen](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-oauth.png)

5. Gebruik **Basisverificatie** in Postman, en stel de OAuth-client-id in als de gebruiker en het geheim als het wachtwoord. Met deze aanroep wordt een toegangstoken geretourneerd. Noteer deze ergens voor later gebruik in het veld **Geheim voor token**.

   ![POST-aanvraag in Postman](./media/sap-analytics-cloud-provisioning-tutorial/postman-post-request.png)

## <a name="step-3-add-sap-analytics-cloud-from-the-azure-ad-application-gallery"></a>Stap 3. SAP Analytics Cloud toevoegen vanuit de Azure AD-toepassingsgalerie

Voeg SAP Analytics Cloud toe vanuit de Azure AD-toepassingsgalerie om te beginnen met het inrichten in SAP Analytics Cloud. Als u SAP Analytics Cloud eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan SAP Analytics Cloud, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-sap-analytics-cloud"></a>Stap 5. Automatische inrichting van gebruikers configureren in SAP Analytics Cloud 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in BlogIn te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-sap-analytics-cloud-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor SAP Analytics Cloud in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SAP Analytics Cloud** in de lijst met toepassingen.

    ![De koppeling naar SAP Analytics Cloud in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer in de sectie **Referenties voor beheerder** in het veld **Tenant-URL** de waarde in van de tenant-URL die eerder is opgehaald. Geef de waarde van het toegangstoken dat u eerder hebt opgehaald op in het veld **Token voor geheim**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met InVision. Als de verbinding mislukt, controleert u of uw SAP Analytics Cloud-account beheerdersmachtigingen heeft. Probeer het vervolgens opnieuw.

    ![Schermopname met het dialoogvenster Beheerdersreferenties, waarin u uw Tenant U R L en Token voor geheim kunt invoeren.](./media/sap-analytics-cloud-provisioning-tutorial/provisioning.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers inrichten**.

9. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD zijn gesynchroniseerd met SAP Analytics Cloud. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de overeenkomende gebruikersaccounts in SAP Analytics Cloud te vinden voor updatebewerkingen. Als u ervoor kiest om het [overeenkomende doelkenmerk](../app-provisioning/customize-application-attributes.md) te wijzigen, moet u ervoor zorgen dat de SAP Analytics Cloud API ondersteuning biedt voor het filteren van gebruikers op basis van dit kenmerk. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |actief|Booleaans|
   |emails[type eq "work"].value|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Tekenreeks|

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor SAP Analytics Cloud.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten in SAP Analytics Cloud, door de gewenste waarden te kiezen bij **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

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