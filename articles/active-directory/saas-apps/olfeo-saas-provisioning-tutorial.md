---
title: 'Zelf studie: Olfeo SAAS configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar Olfeo SAAS.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f6b0320-dfe7-451c-8cd8-6ba7f2e40434
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2021
ms.author: Zhchia
ms.openlocfilehash: b74175c7847bb19aa9410edd613afbfe1d762d05
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105549295"
---
# <a name="tutorial-configure-olfeo-saas-for-automatic-user-provisioning"></a>Zelf studie: Olfeo SAAS configureren voor het automatisch inrichten van gebruikers

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel Olfeo SAAS als Azure Active Directory (Azure AD) om het automatisch inrichten van gebruikers te configureren. Wanneer de configuratie is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en ongedaan gemaakt voor [OLFEO SaaS](https://www.olfeo.com) met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Olfeo SAAS
> * Gebruikers in Olfeo SAAS verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en Olfeo SAAS
> * Inrichtings groepen en groepslid maatschappen in Olfeo SAAS
> * [Eenmalige aanmelding](olfeo-saas-tutorial.md) voor Olfeo SaaS (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld Toepassingsbeheerder, Cloudtoepassingsbeheerder, Toepassingseigenaar of Globale beheerder). 
* Een [OLFEO SaaS-Tenant](https://www.olfeo.com/).
* Een gebruikers account in Olfeo SAAS met beheerders machtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen

1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
1. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en OLFEO SaaS](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-olfeo-saas-to-support-provisioning-with-azure-ad"></a>Stap 2. Olfeo SAAS configureren ter ondersteuning van het inrichten met Azure AD

1. Meld u aan bij de Olfeo SAAS-beheer console. 
1. Navigeer naar **Configuration > Annuaires**.
1. Maak een nieuwe map en geef deze de naam.
1. Selecteer **Azure** provider en klik vervolgens op **CR** er om de nieuwe map op te slaan. 
1. Navigeer naar het tabblad **synchronisatie** om de TENANT- **URL** en het **jeton-geheim** te bekijken. Deze waarden worden gekopieerd en geplakt in de velden **Tenant-URL** en **geheim** op het tabblad INRICHTEN van uw Olfeo SaaS-toepassing in de Azure Portal.

## <a name="step-3-add-olfeo-saas-from-the-azure-ad-application-gallery"></a>Stap 3. Olfeo SAAS toevoegen vanuit de Azure AD-toepassings galerie

Voeg Olfeo SAAS toe vanuit de Azure AD-toepassings galerie om het beheer van de inrichting van Olfeo SAAS te starten. Als u eerder Olfeo SAAS voor SSO hebt ingesteld, kunt u dezelfde toepassing gebruiken. Het is echter raadzaam dat u een afzonderlijke app maakt wanneer u de integratie in eerste instantie test. Klik [hier](../manage-apps/add-gallery-app.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan Olfeo SAAS, moet u een andere rol dan **standaard toegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om meer rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-olfeo-saas"></a>Stap 5. Automatische gebruikers inrichting configureren voor Olfeo SAAS 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en groepen in Olfeo SAAS-app te maken, bij te werken en uit te scha kelen op basis van gebruikers-en groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-olfeo-saas-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Olfeo SAAS in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer in de lijst toepassingen de optie **OLFEO SaaS**.

    ![De Olfeo SAAS-koppeling in de lijst met toepassingen](common/all-applications.png)

1. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

1.  Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![De inrichtingsmodus ingesteld op Automatisch](common/provisioning-automatic.png)

1. Voer uw Olfeo SAAS- **Tenant-URL** en **geheime token** gegevens in het gedeelte **beheerders referenties** in. Selecteer **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Olfeo SaaS. Als de verbinding mislukt, zorg er dan voor dat uw Olfeo SAAS-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen. Selecteer het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

1. Selecteer **Opslaan**.

1. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Olfeo SaaS**.

1. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Olfeo SAAS in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Olfeo SaaS voor bijwerk bewerkingen. Als u het [overeenkomende doel kenmerk](../app-provisioning/customize-application-attributes.md)wijzigt, moet u ervoor zorgen dat de OLFEO SaaS API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer **Opslaan** om eventuele wijzigingen toe te passen.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |displayName|Tekenreeks|
   |actief|Booleaans|
   |emails[type eq "work"].value|Tekenreeks|
   |preferredLanguage|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |name.formatted|Tekenreeks|
   |externalId|Tekenreeks|  

1. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met Olfeo SaaS**.

1. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Olfeo SAAS in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om de groepen in Olfeo SaaS te vergelijken voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|Ondersteund voor filteren|
      |---|---|---|
      |displayName|Tekenreeks|&check;|
      |externalId|Tekenreeks|
      |leden|Naslaginformatie|

1. Zie de instructies in de hand leiding voor het filteren op [bereik](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor meer informatie over het configureren van bereik filters.

1. Als u de Azure AD-inrichtings service voor Olfeo **SaaS wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

1. Definieer de gebruikers of groepen die u wilt inrichten voor Olfeo SAAS door de gewenste waarden in het **bereik** te selecteren in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

1. Selecteer **Opslaan** als u klaar bent voor het inrichten.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De eerste cyclus duurt langer dan de volgende cycli, die ongeveer elke 40 minuten in beslag neemt, zolang de Azure AD-inrichtings service wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken

Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

* Gebruik de [inrichtings logboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht.
* Controleer de [voortgangs balk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtings cyclus te bekijken en te bepalen hoe het moet worden voltooid.
* Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. Zie [toepassings inrichting status van quarantaine](../app-provisioning/application-provisioning-quarantine-status.md)voor meer informatie over quarantaine statussen.

## <a name="more-resources"></a>Meer bronnen

* [Gebruikersaccounts inrichten voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)