---
title: 'Zelf studie: Playvox configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar Playvox.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 45b84b2531e08233db56f190e2517519744f4eba
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181817"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>Zelf studie: Playvox configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel Playvox als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer de configuratie is geconfigureerd, worden gebruikers en/of groepen door Azure AD automatisch ingericht en [GePlayvoxd](https://www.playvox.com) met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Playvox
> * Gebruikers in Playvox verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en Playvox

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld Toepassingsbeheerder, Cloudtoepassingsbeheerder, Toepassingseigenaar of Globale beheerder). 
* Een gebruikers account in [Playvox](https://www.playvox.com) met super beheerders machtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en Playvox](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-playvox-to-support-provisioning-with-azure-ad"></a>Stap 2. Playvox configureren voor ondersteuning bij het inrichten met Azure AD

1. Meld u aan bij de Playvox-beheer console. Navigeer naar **instellingen > API-sleutels**.

2. Klik op **API-sleutel maken**.

    ![API-sleutel](media/playvox-provisioning-tutorial/create.png)

3. Voer een beschrijvende naam in voor de API-sleutel en klik op **Opslaan**. De API-sleutel wordt gegenereerd. Klik op **Sluiten**.

4. Klik op **Details** in de API-sleutel die u in de vorige stap hebt gemaakt.

    ![Details](media/playvox-provisioning-tutorial/api.png)

5. Kopieer de base64- **sleutel** en sla deze op. Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad inrichten van uw Playvox-toepassing in de Azure Portal.

    ![SCIM-token](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>Stap 3. Playvox toevoegen vanuit de Azure AD-toepassings galerie

Voeg Playvox toe vanuit de Azure AD-toepassings galerie om het beheren van de inrichting van Playvox te starten. Als u eerder Playvox voor SSO hebt ingesteld, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en/of groepen toe te wijzen aan de toepassing. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers toewijst aan Playvox, moet u een andere rol dan **standaard toegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test met een kleine set gebruikers en/of groepen voordat u naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en/of groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>Stap 5. Automatische gebruikers inrichting configureren voor Playvox 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in BlogIn te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-playvox-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Playvox in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Playvox**.

    ![De koppeling Playvox in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![De inrichtingsmodus ingesteld op Automatisch](common/provisioning-automatic.png)

5. Voer in het gedeelte beheerders referenties de Playvox- **Tenant-URL** in als
    * `https://{tenant}.playvox.com/scim/v1`

    Voer de waarde van **Token voor geheim** in die eerder in stap 2 is opgehaald. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Playvox. Als de verbinding mislukt, zorg er dan voor dat uw Playvox-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Playvox**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Playvox in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Playvox voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](../app-provisioning/customize-application-attributes.md)te wijzigen, moet u ervoor zorgen dat de PLAYVOX-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |actief|Booleaans|
   |displayName|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |name.formatted|Tekenreeks|
   |externalId|Tekenreeks|
   

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Als u de Azure AD-inrichtings service voor **Playvox wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor Playvox door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

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