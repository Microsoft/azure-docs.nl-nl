---
title: 'Zelfstudie: Insight4GRC configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Lees hier meer over het automatisch inrichten en ongedaan maken van de inrichting van gebruikersaccounts van Azure AD voor Insight4GRC.
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: Zhchia
ms.openlocfilehash: 7d5904461d642f47e691d0a1aaa1f1faf439df1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96178136"
---
# <a name="tutorial-configure-insight4grc-for-automatic-user-provisioning"></a>Zelfstudie: Insight4GRC configureren voor automatische inrichting van gebruikers

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel Insight4GRC als Azure Active Directory (Azure AD) voor het configureren van automatische inrichting van gebruikers. Wanneer de configuratie is voltooid, wordt inrichting en ongedaan maken van inrichting van gebruikers en groepen door Azure AD automatisch uitgevoerd op [Insight4GRC](https://www.rsmuk.com/) met behulp van de Azure AD-inrichtingsservice. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Insight4GRC
> * Gebruikers verwijderen uit Insight4GRC wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Insight4GRC
> * Groepen en groepslidmaatschappen inrichten in Insight4GRC
> * [Eenmalige aanmelding](./insight4grc-tutorial.md) voor Insight4GRC (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* Een gebruikersaccount in Insight4GRC met beheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Insight4GRC](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-insight4grc-to-support-provisioning-with-azure-ad"></a>Stap 2. Insight4GRC configureren om ondersteuning te bieden voor inrichting met Azure AD

Voordat u Insight4GRC configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM-inrichting inschakelen in Insight4GRC.

1. Om het Bearer-token te verkrijgen, moet de eindgebruiker contact opnemen met het [ondersteuningsteam](mailto:support.ss@rsmuk.com).
2. Als u de URL van het SCIM-eindpunt wilt ophalen, hebt u de Insight4GRC-domeinnaam nodig, omdat deze wordt gebruikt om de URL van uw SCIM-eindpunt te maken. U kunt uw Insight4GRC-domeinnaam ophalen als onderdeel van de oorspronkelijke software-aankoop met Insight4GRC.

## <a name="step-3-add-insight4grc-from-the-azure-ad-application-gallery"></a>Stap 3. Insight4GRC toevoegen vanuit de galerie met Azure AD-toepassingen

Voeg Insight4GRC toe vanuit de galerie met Azure AD-toepassingen om te beginnen met het inrichten voor Insight4GRC. Als u Insight4GRC eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan Insight4GRC, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-insight4grc"></a>Stap 5. Automatische gebruikersinrichting configureren voor Insight4GRC 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in BlogIn te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-insight4grc-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Insight4GRC in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Insight4GRC** in de lijst met toepassingen.

    ![De koppeling naar Insight4GRC in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer onder de sectie **Beheerdersreferenties** de URL van het SCIM-eindpunt bij **Tenant-URL** in. De eindpunt-URL moet de indeling `https://<Insight4GRC Domain Name>.insight4grc.com/public/api/scim/v2 ` hebben, waarbij de **Insight4GRC-domeinnaam** de waarde is die in de vorige stappen is opgehaald. Voer de waarde van het Bearer-token in die eerder is opgehaald in **Token van geheim**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Insight4GRC. Als de verbinding mislukt, controleert u of uw Insight4GRC-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Schermopname met het dialoogvenster Beheerdersreferenties, waarin u uw Tenant U R L en Token voor geheim kunt invoeren.](./media/insight4grc-provisioning-tutorial/provisioning.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Insight4GRC**.

9. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD worden gesynchroniseerd met Insight4GRC. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikersaccounts in Insight4GRC te vinden voor updatebewerkingen. Als u ervoor kiest om het [overeenkomende doelkenmerk](../app-provisioning/customize-application-attributes.md) te wijzigen, moet u ervoor zorgen dat de API van Insight4GRC het filteren van gebruikers op basis van dat kenmerk kan ondersteunen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |externalId|Tekenreeks|
   |actief|Booleaans|
   |title|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |phoneNumbers[type eq "work"].value|Tekenreeks|

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Insight4GRC**.

11. Controleer in de sectie **Kenmerktoewijzing** de groepskenmerken die vanuit Azure AD worden gesynchroniseerd met Insight4GRC. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in Insight4GRC te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |externalId|Tekenreeks|
      |leden|Naslaginformatie|

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Insight4GRC.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Insight4GRC wilt toevoegen door de gewenste waarden te kiezen bij **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

* Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht.
* Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid.
* Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccounts inrichten voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md).