---
title: 'Zelfstudie: Inrichting van gebruikers voor ThousandEyes - Azure AD'
description: Ontdek hoe u Azure Active Directory configureert om gebruikersaccounts automatisch in te richten en de inrichting van gebruikersaccounts ongedaan te maken voor ThousandEyes.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: fcb53dc22cfb4bf7308b92ee76e5aaaf3bb0dead
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735084"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Zelfstudie: ThousandEyes configureren voor automatische inrichting van gebruikers

Het doel van deze zelfstudie is om u te laten zien welke stappen u moet uitvoeren in ThousandEyes en Azure AD om automatisch gebruikersaccounts van Azure AD in te richten in ThousandEyes, of de inrichting van gebruikersaccounts ongedaan te maken. 

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende items:

* Een Azure Active Directory-tenant
* Een ThousandEyes-tenant waarvoor het [Standard-plan](https://www.thousandeyes.com/pricing) of beter is ingeschakeld 
* Een gebruikersaccount in ThousandEyes met beheerdersmachtigingen 

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de [ThousandEyes SCIM-API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK), die beschikbaar is voor ThousandEyes SCIM-teams met een Standard-abonnement of een beter abonnement.

## <a name="assigning-users-to-thousandeyes"></a>Gebruikers toewijzen aan ThousandEyes

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikersaccounts worden alleen de gebruikers en groepen gesynchroniseerd die zijn 'toegewezen' aan een toepassing in Azure AD. 

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw ThousandEyes-app. Daarna kunt u deze gebruikers toewijzen aan de ThousandEyes-app door deze instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Belangrijke tips voor het toewijzen van gebruikers aan ThousandEyes

* Het wordt aanbevolen om eerst één Azure AD-gebruiker toe te wijzen aan ThousandEyes om de configuratie van de inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Als u een gebruiker aan ThousandEyes toewijst, moet u de **gebruikersrol** of een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. De rol **Standaardtoegang** werkt niet voor inrichting. Deze gebruikers worden overgeslagen.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>Gebruikersinrichting configureren voor ThousandEyes 

In deze sectie wordt u begeleid bij het verbinden van de API voor het inrichten van uw Azure AD-gebruikersaccounts voor ThousandEyes en het configureren van de inrichtingsservice om toegewezen gebruikersaccounts in ThousandEyes te maken, bij te werken en uit te schakelen op basis van de gebruikers- en groepstoewijzing in Azure AD.

> [!TIP]
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding in te schakelen voor ThousandEyes, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies vormen een aanvulling op elkaar.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Automatische inrichting van gebruikersaccounts in ThousandEyes configureren in Azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Als u ThousandEyes al hebt geconfigureerd voor eenmalige aanmelding, zoekt u met het zoekveld naar uw instantie van ThousandEyes. Selecteer anders **Toevoegen** en zoek in de galerie met toepassingen naar **ThousandEyes**. Selecteer ThousandEyes in de zoekresultaten en voeg de toepassing toe aan uw lijst met toepassingen.

    ![De ThousandEyes-koppeling in de lijst met toepassingen](common/all-applications.png)
    
3. Selecteer uw exemplaar van ThousandEyes en selecteer vervolgens het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

![Schermopname met het tabblad Inrichten voor ThousandEyes met Automatisch geselecteerd voor de modus Inrichting.](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)
    

5. Voer in de sectie **Beheerdersreferenties** het **OAuth Bearer-token** in dat is gegenereerd door uw ThousandEyes-account (u kunt een token vinden of genereren in de sectie **Profiel** van uw ThousandEyes-account).

    ![Schermopname waarin wordt getoond waar u de koppeling Accountinstellingen voor de huidige accountgroep kunt vinden.](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. Klik in Azure Portal op **Verbinding testen** om te controleren of Azure AD verbinding kan maken met uw ThousandEyes-app. Als de verbinding mislukt, controleert u of het ThousandEyes-account beheerdersmachtigingen heeft. Herhaal vervolgens stap 5.

7. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

8. Klik op **Opslaan**.

9. Selecteer in de sectie Toewijzingen de optie **Azure Active Directory-gebruikers synchroniseren met ThousandEyes**.

10. Controleer in de sectie **Kenmerktoewijzing** de gebruikerskenmerken die vanuit Azure AD worden gesynchroniseerd met ThousandEyes. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Parsable te vinden voor updatebewerkingen. Als u ervoor kiest om het [overeenkomende doelkenmerk](../app-provisioning/customize-application-attributes.md) te wijzigen, moet u ervoor zorgen dat de Parsable-API het filteren van gebruikers op basis van dat kenmerk kan ondersteunen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

     |Kenmerk|Type|Ondersteund voor filteren|
     |---|---|---|
     |externalId|Tekenreeks|&check;|
     |userName|Tekenreeks|&check;|
     |actief|Booleaans|
     |displayName|Tekenreeks|
     |emails[type eq "work"].value|Tekenreeks|
     |name.formatted|Tekenreeks|


11. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Wijzig in de sectie **Instellingen** de **Inrichtingsstatus** in **Aan** om de Azure AD-inrichtingsservice in te schakelen voor ThousandEyes.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

13. Definieer de gebruikers en/of groepen die u wilt inrichten in ThousandEyes, door in de sectie **Instellingen** de gewenste waarden te kiezen bij **Bereik**.

    ![Inrichtingsbereik](common/provisioning-scope.png)

14. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

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