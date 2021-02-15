---
title: 'Zelfstudie: Smartsheet configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Smartsheet.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: e9ee994564e175d3c41cfd5ce415ead8c67df353
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103546"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Zelfstudie: Smartsheet configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is het demonstreren van de stappen die moeten worden uitgevoerd in Smartsheet en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor [Smartsheet](https://www.smartsheet.com/pricing). Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Smartsheet
> * Gebruikers uit Smartsheet verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Smartsheet
> * Eenmalige aanmelding bij Smartsheet (aanbevolen)

> [!NOTE]
> Deze connector is momenteel beschikbaar in Openbare preview. Zie voor meer informatie over de algemene Microsoft Azure-gebruiksvoorwaarden voor preview-functies [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md).
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder).
* [Een Smartsheet-tenant](https://www.smartsheet.com/pricing).
* Een gebruikers account op een Smartsheet Enterprise- of Enterprise Premier-abonnement met systeembeheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Smartsheet](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>Stap 2. Smartsheet configureren ter ondersteuning van het inrichten met Azure AD

Voordat u Smartsheet configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM-inrichting inschakelen op Smartsheet.

1. Meld u aan als **SysAdmin** in de **[Smartsheet-portal](https://app.smartsheet.com/b/home)** en navigeer naar **Accountbeheerder**.

    ![Beheerder van Smartsheet-account](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Ga naar **Beveiligingscontroles > Gebruikers automatisch inrichten > Bewerken**.

    ![Beveiligingsmaatregelen van Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. De e-maildomeinen toevoegen en valideren voor de gebruikers die u van plan bent vanuit Azure AD in te richten op Smartsheet. Kies **Niet ingeschakeld** om er zeker van te zijn dat alle inrichtingsacties afkomstig zijn van Azure AD en om er ook voor te zorgen dat uw Smartsheet-gebruikerslijst is gesynchroniseerd met Azure AD-toewijzingen.

    ![Gebruikers inrichten met Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Nadat de validatie is voltooid, moet u het domein activeren. 

    ![Het domein van Smartsheet activeren](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Genereer het **Token voor geheim** vereist voor het configureren van automatische gebruikersinrichting met Azure AD door te navigeren naar **Apps en integraties**.

    ![Schermopname van de beheerpagina van Smartsheet met de optie gebruikersavatar en Apps & integraties gemarkeerd.](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Kies **API-toegang**. Klik op **Nieuw toegangstoken genereren**.

    ![Schermopname van het dialoogvenster Persoonlijke instellingen met de opties API-toegang en Nieuw toegangstoken genereren gemarkeerd.](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. De naam van het API-toegangstoken definiëren. Klik op **OK**.

    ![Schermopname van Stap 1 van 2: Genereer het API-toegangstoken met de optie OK gemarkeerd.](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Kopieer het API-toegangstoken en sla het op omdat dit de enige keer is dat u het kunt bekijken. Dit is vereist in het veld **Token voor geheim** in Azure AD.

    ![Smartsheet-token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>Stap 3. Smartsheet toevoegen vanuit de galerie met Azure AD-toepassingen

Voeg Smartsheet toe vanuit de galerie met Azure AD-toepassingen om te beginnen met het inrichten voor Smartsheet. Als u Smartsheet eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan Smartsheet, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Om ervoor te zorgen dat pariteit in gebruikersroltoewijzingen tussen Smartsheet en Azure AD wordt gebruikt, is het raadzaam om dezelfde roltoewijzingen te gebruiken die zijn ingevuld in de volledige Smartsheet-gebruikerslijst. Als u deze gebruikerslijst van Smartsheet wilt ophalen, navigeert u naar **Accountbeheerder > Gebruikersbeheer > Meer acties > Gebruikerslijst (csv) downloaden**.

* Voor toegang tot bepaalde functies in de app vereist Smartsheet dat een gebruiker meerdere rollen heeft. Ga voor meer informatie over gebruikerstypen en machtigingen in Smartsheet naar [Gebruikerstypen en machtigingen](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Als een gebruiker meerdere rollen heeft toegewezen in Smartsheet, **MOET** u ervoor zorgen dat deze roltoewijzingen worden gerepliceerd in Azure AD om te vermijden dat gebruikers de toegang tot Smartsheet-objecten permanent kunnen kwijtraken. Elke unieke rol in Smartsheet **MOET** worden toegewezen aan een andere groep in Azure AD. De gebruiker **MOET** vervolgens worden toegevoegd aan elk van de groepen die overeenkomen met de gewenste rollen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>Stap 5. Automatische gebruikersinrichting configureren voor Smartsheet 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Smartsheet te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Smartsheet in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Smartsheet** in de lijst met toepassingen.

    ![De Smartsheet-link in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. In het gedeelte **Beheerdersreferenties** voert u de waarden voor **SCIM 2.0 basis-URL en toegangstoken** in die eerder zijn opgehaald uit Smartsheet in respectievelijk **Tenant-URL** en **Token voor geheim**. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Smartsheet. Als de verbinding mislukt, moet u controleren of uw Smartsheet-account SysAdmin-machtigingen heeft. Probeer het daarna opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Smartsheet**.

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Smartsheet worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Smartsheet te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |actief|Booleaans|
   |title|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |phoneNumbers[type eq "work"].value|Tekenreeks|
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|
   |phoneNumbers[type eq "fax"].value|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |externalId|Tekenreeks|
   |rollen|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Tekenreeks|


10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Smartsheet.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u aan Smartsheet wilt toevoegen door de gewenste waarden te kiezen in **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.  

## <a name="connector-limitations"></a>Connectorbeperkingen

* Smartsheet biedt geen ondersteuning voor voorlopig verwijderingen. Wanneer een **actief** kenmerk van een gebruiker is ingesteld op onwaar, wordt de gebruiker in Smartsheet permanent verwijderd.

## <a name="change-log"></a>Wijzigingenlogboek

* 06/16/2020 - Ondersteuning toegevoegd voor bedrijfsextensiekenmerken ‘Kostenplaats’, ‘Divisie’, ‘Manager’ en ‘Afdeling’ voor gebruikers.
* 02/10/2021-ondersteuning toegevoegd voor kern kenmerken "e-mails [type EQ" werk "]" voor gebruikers.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)