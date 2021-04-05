---
title: 'Zelfstudie: Atlassian configureren voor automatische inrichting van gebruikers met Azure Active Directory | Microsoft Docs'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor Atlassian.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 691d35267c255c933a8098b99301fbb795a3cd0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181064"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Zelfstudie: Atlassian configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelfstudie is om de stappen te laten zien die moeten worden uitgevoerd in Atlassian Cloud en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en/of groepen voor [Atlassian Cloud](https://www.atlassian.com/licensing/cloud). Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Atlassian Cloud
> * Gebruikers uit Atlassian Cloud verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Atlassian Cloud
> * Groepen en groepslidmaatschappen inrichten in Atlassian Cloud
> * [Eenmalige aanmelding](./atlassian-cloud-tutorial.md) bij Atlassian Cloud (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md).
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder).
* [Een Atlassian Cloud-tenant](https://www.atlassian.com/licensing/cloud)
* Een gebruikersaccount in Atlassian Cloud met beheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Atlassian Cloud](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-atlassian-cloud-to-support-provisioning-with-azure-ad"></a>Stap 2. Atlassian Cloud configureren ter ondersteuning van het inrichten met Azure AD

1. Ga naar [Atlassian Organization Manager](https://admin.atlassian.com) **> selecteer de org > map**.

    ![Schermopname van de Beheerpagina met de optie Map aangeroepen.](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

2. Klik op **Gebruikers inrichten** en klik op **Een map maken**. Kopieer de **Basis-URL van de map** en het **Bearer-token** die respectievelijk worden ingevoerd in de velden **Tenant-URL** en **Token voor geheim** op het tabblad Inrichten van uw Atlassian Cloud-toepassing in de Azure AD-portal.

    ![Schermopname van de Beheerpagina met de optie Gebruikers inrichten aangeroepen.](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Schermopname van de pagina Een token maken.](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)
    ![Schermopname van de pagina met het token van de map van de demo-tijd.](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)


## <a name="step-3-add-atlassian-cloud-from-the-azure-ad-application-gallery"></a>Stap 3. Atlassian Cloud toevoegen vanuit de galerie met Azure AD-toepassingen

Voeg Atlassian Cloud toe vanuit de galerie met Azure AD-toepassingen om te beginnen met het inrichten voor Atlassian Cloud. Als u Atlassian Cloud eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](../manage-apps/add-application-portal.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan Atlassian Cloud, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Stap 5. Automatische gebruikersinrichting voor Atlassian Cloud configureren 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers en/of groepen in Atlassian Cloud te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Atlassian Cloud in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer **Atlassian Cloud**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Atlassian Cloud** in de lijst met toepassingen.

    ![De koppeling naar Atlassian Cloud in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Schermopname van de opties onder Beheren met de optie Inrichten gemarkeerd.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de vervolgkeuzelijst Inrichtingsmodus met de optie Automatisch gemarkeerd.](common/provisioning-automatic.png)

5. In het gedeelte **Beheerdersreferenties** voert u de **Tenant-URL** en het **Token voor geheim** in die eerder zijn opgehaald uit uw Atlassian Cloud-account. Klik op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Atlassian Cloud. Als de verbinding mislukt, moet u controleren of uw Atlassian Cloud-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Atlassian Cloud**.

9. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Atlassian Cloud worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Atlassian Cloud te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |actief|Booleaans|
   |name.familyName|Tekenreeks|
   |name.givenName|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|   

10. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-groepen synchroniseren met Atlassian Cloud**.

11. Controleer in de sectie **Kenmerktoewijzingen** de groepskenmerken die vanuit Azure AD met Atlassian Cloud worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de groepen in Atlassian Cloud te vinden voor updatebewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |externalId|Tekenreeks|
      |leden|Naslaginformatie|

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Atlassian Cloud.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Atlassian Cloud wilt toevoegen door de gewenste waarden te kiezen bij **Bereik** in de sectie **Instellingen**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

16. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.  

## <a name="connector-limitations"></a>Connectorbeperkingen

* Met Atlassian Cloud kunnen gebruikers alleen worden ingericht vanaf [geverifieerde domeinen](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian Cloud biedt op dit moment geen ondersteuning voor het wijzigen van de namen van groepen. Dit betekent dat wijzigingen in de displayName van een groep in Azure AD niet worden bijgewerkt en niet worden weergegeven in Atlassian Cloud.
* De waarde van het gebruikerskenmerk **e-mail** in Azure AD wordt alleen ingevuld als de gebruiker een Microsoft Exchange-postvak heeft. Als de gebruiker hier niet over beschikt, wordt aanbevolen om een ander gewenst kenmerk toe te wijzen aan het kenmerk **e-mail** in Atlassian Cloud.

## <a name="change-log"></a>Wijzigingenlogboek

* 06/15/2020 - Ondersteuning toegevoegd voor batch PATCH voor groepen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png