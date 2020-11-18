---
title: 'Zelfstudie: SolarWinds Service Desk (voorheen Samanage) configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikersaccounts van Azure AD naar SolarWinds Service Desk (voorheen Samanage).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 5cdc36c20cbba148bb68bda700f5fdccbc593caf
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94352996"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Zelfstudie: SolarWinds Service Desk (voorheen Samanage) configureren voor automatische inrichting van gebruikers

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel SolarWinds Service Desk (voorheen Samanage) als Azure Active Directory (Azure AD) voor het configureren van automatische inrichting van gebruikers. Wanneer de configuratie is voltooid, wordt inrichting en ongedaan maken van inrichting van gebruikers en groepen door Azure AD automatisch uitgevoerd op de [SolarWinds Service Desk](https://www.samanage.com/pricing/) met behulp van de Azure AD-inrichtingsservice. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>Migreren naar de nieuwe SolarWinds Service Desk-toepassing

Als u een bestaande integratie met SolarWinds Service Desk hebt, raadpleegt u de volgende sectie over aanstaande wijzigingen. Als u SolarWinds Service Desk voor de eerste keer instelt, kunt u deze sectie overslaan en verder gaan naar **Ondersteunde mogelijkheden**.

#### <a name="whats-changing"></a>Wat wordt gewijzigd?

* Wijzigingen aan de kant van Azure AD: De autorisatiemethode voor het inrichten van gebruikers in SolarWinds Service Desk is altijd **basisverificatie** geweest. Binnenkort ziet u dat de autorisatiemethode is gewijzigd in **Geheim token met lange levensduur**.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Wat moet ik doen om mijn bestaande aangepaste integratie naar de nieuwe toepassing te migreren?

Als u een bestaande SolarWinds Service Desk-integratie met geldige beheerdersreferenties hebt, **is er geen actie vereist**. Klanten worden automatisch naar de nieuwe toepassing gemigreerd. Dit proces wordt volledig achter de schermen uitgevoerd. Als de bestaande referenties verlopen of als u opnieuw toegang tot de toepassing moet verlenen, moet u een geheim token met een lange levensduur genereren. Raadpleeg stap 2 van dit artikel als u een nieuw token wilt genereren.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Hoe kan ik zien of mijn toepassing is gemigreerd? 

Wanneer uw toepassing wordt gemigreerd, worden in de sectie **Beheerdersreferenties** de velden **Gebruikersnaam van beheerder** en **Beheerderswachtwoord** vervangen door één veld **Token voor geheim**.

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden

> [!div class="checklist"]
> * Gebruikers maken in SolarWinds Service Desk
> * Gebruikers uit SolarWinds Service Desk verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en SolarWinds Service Desk
> * Groepen en groepslidmaatschappen inrichten in SolarWinds Service Desk
> * [Eenmalige aanmelding](./samanage-tutorial.md) voor SolarWinds Service Desk (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../users-groups-roles/directory-assign-admin-roles.md) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* Een [SolarWinds Service Desk-tenant](https://www.samanage.com/pricing/) met het Professional-pakket.
* Een gebruikersaccount in de SolarWinds-Service Desk met beheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [koppelen tussen Azure AD en SolarWinds Service Desk](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>Stap 2. SolarWinds Service Desk configureren ter ondersteuning van het inrichten met Azure AD

Zie de [zelfstudie over tokenverificatie voor API-integratie](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657) als u een geheim token voor verificatie wilt genereren.

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>Stap 3. SolarWinds Service Desk toevoegen vanuit de Azure AD-toepassingsgalerie

Voeg SolarWinds Service Desk toe vanuit de galerie met Azure AD-toepassingen om te beginnen met het inrichten voor SolarWinds Service Desk. Als u SolarWinds Service Desk eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan SolarWinds Service Desk, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>Stap 5. Automatische gebruikersinrichting configureren voor SolarWinds Service Desk 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in SolarWinds Service Desk te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor SolarWinds Service Desk in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SolarWinds Service Desk** in de lijst met toepassingen.

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname met het tabblad Inrichten geselecteerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname met Inrichtingsmodus ingesteld op Automatisch.](common/provisioning-automatic.png)

5. Voer onder de sectie **Referenties voor beheerder** `https://api.samanage.com` in **Tenant-URL** in.  Voer de waarde van de token voor het geheim in die eerder in **Token voor geheim** is opgehaald. Selecteer **Verbinding testen** om te controleren of Azure AD verbinding kan maken met SolarWinds Service Desk. Als de verbinding mislukt, moet u controleren of uw SolarWinds Service Desk-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Schermopname met de knop Verbinding testen geselecteerd.](./media/samanage-provisioning-tutorial/provisioning.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met SolarWinds Service Desk**.

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met SolarWinds Service Desk worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in SolarWinds Service Desk te vinden voor updatebewerkingen. Als u ervoor kiest om het [overeenkomende doelkenmerk](../app-provisioning/customize-application-attributes.md) te wijzigen, moet u ervoor zorgen dat de API van SolarWinds Service Desk het filteren van gebruikers op basis van dat kenmerk kan ondersteunen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      ![SolarWinds Service Desk-gebruikerstoewijzingen](./media/samanage-provisioning-tutorial/user-attributes.png)

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met SolarWinds Service Desk**.

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met SolarWinds Service Desk worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in SolarWinds Service Desk te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      ![SolarWinds Service Desk-groepstoewijzingen](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig **Inrichtingsstatus** in **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor SolarWinds Service Desk.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan SolarWinds Service Desk wilt toevoegen door de gewenste waarden in **Bereik** in de sectie **Instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Selecteer **Opslaan** als u klaar bent voor het inrichten.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.

## <a name="connector-limitations"></a>Connectorbeperkingen

Als u de optie **Alle gebruikers en groepen synchroniseren** selecteert en een waarde configureert voor het kenmerk **rollen** van SolarWinds Service Desk, moet de waarde onder het vak **Standaardwaarde indien null (is optioneel)** worden uitgedrukt in de volgende notatie:

- {"displayName":"role"}, waarbij "role" de gewenste standaardwaarde is.

## <a name="change-log"></a>Wijzigingenlogboek

* 14-09-2020: de bedrijfsnaam in twee SaaS-zelfstudies gewijzigd van Samanage in SolarWinds Service Desk (voorheen Samanage) per https://github.com/ravitmorales.
* 22-04-2020: de autorisatiemethode bijgewerkt van basisverificatie in geheim token met een lange levensduur.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)