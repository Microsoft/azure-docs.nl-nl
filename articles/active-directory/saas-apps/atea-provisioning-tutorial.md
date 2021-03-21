---
title: 'Zelf studie: Atea configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar ATEA.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b788328b-10fd-4eaa-a4bc-909d738d8b8b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 1991c27ee992a08c3f31cd90df016bf19405f56a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653181"
---
# <a name="tutorial-configure-atea-for-automatic-user-provisioning"></a>Zelf studie: Atea configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel Atea als Azure Active Directory (Azure AD) om het automatisch inrichten van gebruikers te configureren. Wanneer de configuratie is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en [GeAtead](https://www.atea.com/) met behulp van de Azure AD-inrichtings service. Voor belang rijke informatie over de werking van deze service kunt u zien hoe deze werkt en hoe Veelgestelde vragen gebruikers inrichten en het ongedaan maken [van de inrichting van SaaS-toepassingen met Azure Active Directory automatiseren](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Atea
> * Gebruikers in Atea verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en Atea

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld Toepassingsbeheerder, Cloudtoepassingsbeheerder, Toepassingseigenaar of Globale beheerder). 
* Een gebruikers account in Atea met beheerders machtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en Atea](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-atea-to-support-provisioning-with-azure-ad"></a>Stap 2. Atea configureren voor ondersteuning bij het inrichten met Azure AD

Als u Atea wilt configureren voor ondersteuning bij het inrichten met Azure AD, schrijft u een e-mail naar het ondersteunings team van Atea <SSO.Support@atea.com>

## <a name="step-3-add-atea-from-the-azure-ad-application-gallery"></a>Stap 3. Atea toevoegen vanuit de Azure AD-toepassings galerie

Voeg Atea toe vanuit de Azure AD-toepassings galerie om het beheren van de inrichting van Atea te starten. Als u eerder Atea voor SSO hebt ingesteld, kunt u dezelfde toepassing gebruiken. Het is echter raadzaam dat u een afzonderlijke app maakt wanneer u de integratie in eerste instantie test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing en of op basis van de kenmerken van de gebruiker en groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan Atea, moet u een andere rol dan **standaard toegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om andere functies toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-atea"></a>Stap 5. Automatische gebruikers inrichting configureren voor Atea 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en groepen in Atea te maken, bij te werken en uit te scha kelen, op basis van gebruikers-en groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-atea-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Atea in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Atea**.

    ![De koppeling Atea in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![De inrichtingsmodus ingesteld op Automatisch](common/provisioning-automatic.png)

5. Selecteer **machtigen** in de sectie **beheerders referenties** . Hiermee opent u een dialoog venster voor aanmelding bij ATEA in een nieuw browser venster.

     ![Atea autoriseren](media/atea-provisioning-tutorial/provisioning-authorize.png)

6. Meld u aan bij de Tenant van uw Atea in het dialoog venster Aanmelden bij de Atea en controleer uw identiteit.
       
      ![Dialoog venster Atea-aanmelding](media/atea-provisioning-tutorial/atea-login.png)

7. Klik na het volt ooien van stap 5 en 6 op **verbinding testen** om te controleren of Azure AD verbinding kan maken met ATEA. Als de verbinding mislukt, zorg er dan voor dat uw Atea beheerders machtigingen heeft en probeer het opnieuw.
        
      ![Atea-test verbinding](media/atea-provisioning-tutorial/test-connection.png)

8. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen. En schakel vervolgens het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

9. Selecteer **Opslaan**.

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Atea**.

11. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Atea in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Atea voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](../app-provisioning/customize-application-attributes.md)te wijzigen, moet u ervoor zorgen dat de Atea-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|Ondersteund voor filteren|
      |---|---|---|
      |userName|Tekenreeks|&check;|
      |actief|Booleaans|
      |emails[type eq "work"].value|Tekenreeks|
      |name.givenName|Tekenreeks|
      |name.familyName|Tekenreeks|
      |name.formatted|Tekenreeks|
      |phoneNumbers[type eq "mobile"].value|Tekenreeks|
      |landinstelling|Tekenreeks|
      |nickName|Tekenreeks|

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Als u de Azure AD-inrichtings service voor **Atea wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en groepen die u wilt inrichten voor Atea door de relevante waarde in **Scope** te kiezen in het gedeelte **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De eerste cyclus duurt langer dan de volgende cycli, die ongeveer elke 40 minuten plaatsvinden, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

* Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht.
* Controleer de [voortgangs balk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtings cyclus te bekijken en te bepalen hoe het moet worden voltooid.
* Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.  

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)