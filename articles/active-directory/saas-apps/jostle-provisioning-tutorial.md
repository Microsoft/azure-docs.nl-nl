---
title: 'Zelfstudie: Jostle configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en de inrichting van gebruikersaccounts van Azure AD voor Jostle.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6dbb744f-8b8e-4988-b293-ebe079c8c5c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2021
ms.author: Zhchia
ms.openlocfilehash: d2ab0009f036afa38dc9e401223854a034d45e42
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368621"
---
# <a name="tutorial-configure-jostle-for-automatic-user-provisioning"></a>Zelfstudie: Jostle configureren voor het automatisch inrichten van gebruikers

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in jostle en Azure Active Directory (Azure AD) om automatische inrichting van gebruikers te configureren. Wanneer deze configuratie is geconfigureerd, wordt de inrichting van gebruikers en groepen door Azure AD automatisch in- en verwijderd [met](https://www.jostle.me/) behulp van de Azure AD-inrichtingsservice. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Jostle
> * Gebruikers in Jostle verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Jostle
> * [Een aanmelding bij](jostle-tutorial.md) Jostle (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md) 
* Een gebruikersaccount in Azure AD met [machtigingen](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld Toepassingsbeheerder, Cloudtoepassingsbeheerder, Toepassingseigenaar of Globale beheerder). 
* Een [Jostle-tenant.](https://www.jostle.me/)
* Een gebruikersaccount in Jostle met beheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen

1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
1. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Bepaal welke gegevens moeten [worden weergegeven tussen Azure AD en Jostle.](../app-provisioning/customize-application-attributes.md)

## <a name="step-2-configure-jostle-to-support-provisioning-with-azure-ad"></a>Stap 2. Jostle configureren voor ondersteuning van inrichting met Azure AD

### <a name="automation-account"></a>Automation-account

Voordat u begint, moet u een **Automation-gebruiker maken** in uw Jostle-intranet. Dit is het account dat u gebruikt om te configureren met Azure. Automation-gebruikers kunnen worden gemaakt in **Beheerdersinstellingen > gebruikersaccounts en -gegevens > Automation-gebruikers beheren.**

Zie dit artikel voor meer informatie over Automation-gebruikers en hoe u er [een kunt maken.](https://forum.jostle.us/hc/en-us/articles/360057364073)

Zodra het Automation-gebruikersaccount  is gemaakt, moet het worden geactiveerd (dat wil zeggen ten minste één keer aangemeld bij uw intranet) voordat het kan worden gebruikt om Azure te configureren.

### <a name="manage-user-provisioning"></a>Gebruikers inrichten beheren

Voordat u begint, moet u ervoor zorgen dat uw **accountabonnement functies voor SSO/user provisioning bevat.** Als dat niet het resultaat is, kunt u contact opnemen met uw Customer Success Manager en kunnen zij u helpen bij het toevoegen ervan <success@jostle.me> aan uw account.

De volgende stap is het verkrijgen van de **API-URL en** **API-sleutel** van Jostle:

1. Ga naar de hoofdnavigatie en klik op **Beheerinstellingen.**
1. Klik **onder Gebruikersgegevens naar/van andere systemen** op Gebruikers **inrichten beheren.** Als u 'Gebruikers inrichten beheren' hier niet ziet en hebt gecontroleerd of uw account SSO/gebruikers inrichten bevat, neem dan contact op met de ondersteuning om deze pagina in te stellen in uw <support@jostle.me> beheerdersinstellingen).
1. Ga in de sectie Details **van gebruikers inrichten-API** naar het veld Uw basis-URL, klik op de knop Kopiëren en sla de URL ergens op waar u deze later gemakkelijk kunt openen.                                                            

   ![Inrichten](media/jostle-provisioning-tutorial/manage-user-provisioning.png)
                
1. Klik vervolgens op **De nieuwe sleutel toevoegen...** Knop
1. Ga in het volgende scherm naar het **veld Automation-gebruiker** en gebruik de vervolgkeuzelijst om uw Automation-gebruikersaccount te selecteren. 

   ![Integratieaccount](media/jostle-provisioning-tutorial/select-integration-account.png)                                                                                                                                     
1. Geef in **het veld Beschrijving van api-sleutel** inrichten een naam op voor uw sleutel (bijvoorbeeld 'Azure') en klik vervolgens op de **knop** Toevoegen.

1. Zodra uw sleutel is **gegenereerd,** moet u deze meteen kopiëren en opslaan waar u uw URL hebt opgeslagen (omdat dit de enige keer is dat uw sleutel wordt weergegeven).                                                               
1. Vervolgens gebruikt u de **API-URL en** **API-sleutel om** de integratie in Azure te configureren.
## <a name="step-3-add-jostle-from-the-azure-ad-application-gallery"></a>Stap 3. Jostle toevoegen vanuit de Azure AD-toepassingsgalerie

Voeg Jostle toe vanuit de Azure AD-toepassingsgalerie om te beginnen met het beheren van de inrichting voor Jostle. Als u Jostle eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. Het is echter raadzaam om een afzonderlijke app te maken wanneer u de integratie in eerste instantie test. Klik [hier](../manage-apps/add-gallery-app.md) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan Jostle, moet u een andere rol dan **Standaardtoegang selecteren.** Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om meer rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-jostle"></a>Stap 5. Automatische inrichting van gebruikers configureren voor Jostle 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice voor het maken, bijwerken en uitschakelen van gebruikers en groepen in de Jostle-app op basis van gebruikers- en groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-jostle-in-azure-ad"></a>Automatische inrichting van gebruikers configureren voor Jostle in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer **Jostle** in de lijst met toepassingen.

    ![De koppeling Jostle in de lijst met toepassingen](common/all-applications.png)

1. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

1.  Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![De inrichtingsmodus ingesteld op Automatisch](common/provisioning-automatic.png)

1. Voer in **de sectie Beheerdersreferenties** uw Jostle-tenant-URL **en** **geheime tokengegevens** in. Selecteer **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Jostle. Als de verbinding mislukt, controleert u of uw Jostle-account beheerdersmachtigingen heeft en probeert u het opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. Voer in **het veld E-mailadres** voor meldingen het e-mailadres in van een persoon of groep die de meldingen over inrichtingsfouten moet ontvangen. Selecteer het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

1. Selecteer **Opslaan**.

1. Selecteer in **de sectie Toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Jostle.**

1. Bekijk de gebruikerskenmerken die vanuit Azure AD worden gesynchroniseerd met Jostle in de **sectie Kenmerktoewijzing.** De kenmerken die als Overeenkomende eigenschappen **zijn** geselecteerd, worden gebruikt om de gebruikersaccounts in Jostle te vinden voor updatebewerkingen. Als u het [overeenkomende doelkenmerk wijzigt,](../app-provisioning/customize-application-attributes.md)moet u ervoor zorgen dat de Jostle-API ondersteuning biedt voor het filteren van gebruikers op basis van dat kenmerk. Selecteer **Opslaan** om eventuele wijzigingen toe te passen.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |actief|Booleaans|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |emails[type eq "personal"].value|Tekenreeks|
   |emails[type eq "alternate1"].value|Tekenreeks|
   |emails[type eq "alternate2"].value|Tekenreeks|  
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail1Label|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail2Label |Tekenreeks|  

1. Zie de instructies in de zelfstudie Bereikfilter om [bereikfilters te configureren.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

1. Als u de Azure AD-inrichtingsservice voor Jostle wilt inschakelen, wijzigt u **Inrichtingsstatus** in **Aan** in de **sectie** Instellingen.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

1. Definieer de gebruikers of groepen die u wilt inrichten voor Jostle door de gewenste waarden te selecteren in **Bereik** in de **sectie** Instellingen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

1. Selecteer **Opslaan** als u klaar bent voor het inrichten.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan de volgende cycli, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken

Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

* Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers met succes of zonder succes zijn ingericht.
* Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus te zien en hoe dicht deze bij de voltooiing is.
* Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. Zie Inrichtingsstatus van toepassing van quarantaine voor meer informatie [over quarantainestatussen.](../app-provisioning/application-provisioning-quarantine-status.md)

## <a name="more-resources"></a>Meer bronnen

* [Gebruikersaccounts inrichten voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)