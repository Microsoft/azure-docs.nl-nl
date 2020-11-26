---
title: 'Zelf studie: bizagi Studio configureren voor het uitvoeren van een digitale proces automatisering voor automatische gebruikers inrichting met Azure Active Directory | Microsoft Docs'
description: Informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts vanuit Azure AD naar bizagi Studio voor het automatiseren van digitale processen.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: d177931429642436ceccae9c9051106ba5880ada
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180263"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>Zelf studie: bizagi Studio configureren voor digitale proces automatisering voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel bizagi Studio for Digital Process Automation als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer het is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en ongedaan gemaakt [voor bizagi Studio](https://www.bizagi.com/) met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers in bizagi Studio maken voor digitale proces automatisering
> * Gebruikers in bizagi Studio verwijderen voor het uitvoeren van digitale processen wanneer ze geen toegang meer nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en bizagi Studio voor het automatiseren van digitale processen
> * [Eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) voor bizagi Studio voor het automatiseren van digitale processen (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikersaccount in Azure AD met [machtigingen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld Toepassingsbeheerder, Cloudtoepassingsbeheerder, Toepassingseigenaar of Globale beheerder). 
* Bizagi Studio voor Digital Process Automation versie 11.2.4.2 X of hoger.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en bizagi Studio voor het automatiseren van digitale processen](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-bizagi-studio-for-digital-process-automation-to-support-provisioning-with-azure-ad"></a>Stap 2. Bizagi Studio configureren voor digitale proces automatisering ter ondersteuning van het inrichten met Azure AD


1. Meld u aan bij uw werk portal als gebruiker met **beheerders machtigingen**.

2. Ga naar **beheer-> beveiliging-> OAuth 2-toepassingen**.

   ![instellingen](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. Klik op de knop toevoegen.
4. Kies **Bearer-token** in het toekennings type. Kies **API** en **gebruikers synchronisatie** in toegestaan bereik en klik op opslaan.

   ![api](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. Kopieer het **client geheim** en sla het op. Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad inrichten van uw bizagi Studio for Digital Process Automation-toepassing in de Azure Portal.

   ![token](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="step-3-add-bizagi-studio-for-digital-process-automation-from-the-azure-ad-application-gallery"></a>Stap 3. Bizagi Studio voor digitale proces automatisering toevoegen vanuit de Azure AD-toepassings galerie

Voeg bizagi Studio toe voor het digitaal proces automatisering vanuit de Azure AD-toepassings galerie om het beheer van de inrichting te starten voor bizagi Studio voor het digitaal proces automatisering. Als u eerder bizagi Studio hebt ingesteld voor het uitvoeren van een digitale proces automatisering voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan bizagi Studio voor het automatiseren van digitale processen, moet u een andere rol dan **standaard toegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-bizagi-studio-for-digital-process-automation"></a>Stap 5. Automatische gebruikers inrichting voor bizagi Studio configureren voor digitale proces automatisering 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in BlogIn te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor bizagi Studio voor het digitaal proces automatiseren in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **bizagi Studio for Digital Process Automation**.

    ![De koppeling bizagi Studio voor Digital Process Automation in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![De inrichtingsmodus ingesteld op Automatisch](common/provisioning-automatic.png)

5. Geef in de sectie **beheerders referenties** de bizagi Studio op voor de TENANT-URL van de digitale proces automatisering en het geheime token. 

      * **Tenant-URL:** Voer het bizagi SCIM-eind punt in met deze structuur: <Your_Bizagi_Project>/scim/v2/-voor beeld: `https://my-company.bizagi.com/scim/v2/`

      * **Geheim token:** Deze waarde wordt eerder uit stap 2 opgehaald.

      Klik op **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met bizagi Studio voor het automatiseren van digitale processen. Als de verbinding mislukt, zorgt u ervoor dat uw bizagi Studio voor het account voor Digital Process Automation beheerders machtigingen heeft en probeer het opnieuw.

   ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met bizagi Studio voor digitale proces automatisering**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar bizagi Studio voor het automatiseren van digitale processen in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in bizagi Studio voor het maken van een digitale proces automatisering voor update bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de bizagi Studio voor de API voor digitale proces automatisering het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |actief|Booleaans|
   |emails[type eq "work"].value|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |name.formatted|Tekenreeks|
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|
   
10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Als u de Azure AD-inrichtings service voor bizagi Studio voor digitale proces automatisering wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor bizagi Studio voor digitale proces automatisering door de gewenste waarden in het **bereik** te kiezen in het gedeelte **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) voor meer informatie over quarantainestatussen.  

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../manage-apps/check-status-user-account-provisioning.md)
