---
title: 'Zelf studie: inhoud configureren voor het automatisch inrichten van gebruikers met Azure Active Directory'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure Active Directory (Azure AD) naar de inhoud.
services: active-directory
documentationcenter: ''
author: zchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: zhchia
ms.openlocfilehash: c9d19624d90b1228b2a44caeff7d103af3172ed9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516341"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Zelf studie: inhoud configureren voor automatische gebruikers inrichting

In dit artikel worden de stappen beschreven die u moet uitvoeren in content-out en in Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer deze is geconfigureerd, worden gebruikers en groepen [automatisch door Azure](https://www.contentful.com/) AD ingericht en ongedaan gemaakt met behulp van de Azure AD-inrichtings service. Raadpleeg [Gebruikers inrichten en het ongedaan maken van de inrichting van SaaS-toepassingen automatiseren met Azure Active Directory](../app-provisioning/user-provisioning.md) voor belangrijke informatie over wat deze service doet en hoe deze werkt, en voor veelgestelde vragen. 

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden

> [!div class="checklist"]
> * Gebruikers in een inhouds opmaakt
> * Gebruikers in een inhouds opheffen wanneer ze geen toegang meer nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en inhoud
> * Groepen en groepslid maatschappen in een inhouds oprichtende inrichten
> * [Eenmalige aanmelding](contentful-tutorial.md) voor inhoud (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md). 
* Een gebruikers account in azure AD dat is [gemachtigd](../roles/permissions-reference.md) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder). 
* Een inhoudt organisatie account met een abonnement dat ondersteuning biedt voor systeem voor het inrichten van SCIM (Cross-Domain Identity Management). Als u vragen hebt over het abonnement van uw organisatie, kunt u contact opnemen met de [ondersteuning van inhoud](mailto:support@contentful.com).
 
## <a name="plan-your-provisioning-deployment"></a>Implementatie van de inrichting plannen

1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en de inhoud](../app-provisioning/customize-application-attributes.md). 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>Inhoud configureren ter ondersteuning van inrichting met Azure AD

1. Maak in een verantwoording een **Service gebruikers** account. Alle inrichtings machtigingen voor Azure worden via dit account geleverd. U wordt aangeraden om de **eigenaar** voor dit account te kiezen als de rol van de organisatie.

2. Meld u aan bij de **service gebruiker**.

3. Selecteer in het menu links de optie **organisatie-instellingen**  >  **toegangs Programma's**  >  **Gebruikers inrichten**.

   ![Scherm opname van het menu organisatie-instellingen in de richting van de gebruiker, waarbij de gebruikers worden gemarkeerd onder Hulpprogram Ma's voor toegang.](media/contentful-provisioning-tutorial/access.png)

4. Kopieer de scim- **URL** en sla deze op. U voert deze waarde in het Azure Portal in op het tabblad **inrichting** van uw inhouds toepassing.

5. Selecteer **persoonlijk toegangs token genereren**.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. In het modale venster voert u een naam in voor uw persoonlijke toegangs token en selecteert u vervolgens **genereren**.

7. De SCIM-URL en het geheime token worden gegenereerd. Kopieer deze waarden en sla deze op. U voert deze waarden in op het tabblad **inrichten** van uw inhouds toepassing in de Azure Portal.

    ![Scherm opname van het deel venster persoonlijke toegangs token met C F P A T en de naam van de tijdelijke aanduiding voor het token gemarkeerd.](media/contentful-provisioning-tutorial/token.png)


Neem contact op met de [ondersteuning](mailto:support@contentful.com)als u vragen hebt tijdens het configureren van inrichten in de content-out-beheer console.

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>Inhoud toevoegen vanuit de Azure AD-toepassings galerie

Als u het inrichten wilt beheren, moet u inhoud vanuit de Azure AD-toepassings galerie toevoegen. Als u eerder inhoud voor eenmalige aanmelding hebt ingesteld, kunt u dezelfde toepassing gebruiken. We raden u echter aan een afzonderlijke app te maken om de integratie in eerste instantie te testen. Meer informatie over het [toevoegen van een toepassing in de galerie](../manage-apps/add-application-portal.md). 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>Definiëren wie u wilt opnemen in het bereik voor inrichting 

U kunt de Azure AD Provisioning-Service gebruiken voor het bereik dat wordt ingericht op basis van de toewijzing aan de toepassing of op basis van de kenmerken van de gebruiker of groep. 

Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, voert u de stappen uit om [gebruikers en groepen toe te wijzen aan de toepassing](../manage-apps/assign-user-or-group-access-portal.md).

Als u ervoor kiest om het bereik te bepalen dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, gebruikt u een bereik filter om [voorwaardelijke regels voor het inrichten van gebruikers accounts te definiëren](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Wanneer u gebruikers en groepen toewijst aan inhoud, moet u een andere rol dan **standaard toegang** selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van het inrichten en worden aangegeven in de inrichtings Logboeken als niet effectief in aanmerking komen. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om meer rollen toe te voegen. 
* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het inrichtings bereik is ingesteld op toegewezen gebruikers en groepen, kunt u het bereik beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [op kenmerken gebaseerd bereik filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)opgeven. 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>Automatische gebruikers inrichting op inhoud configureren 

In deze sectie wordt u begeleid bij de stappen voor het instellen van de Azure AD-inrichtings service om gebruikers en groepen in een test-app te maken, bij te werken en uit te scha kelen op basis van gebruikers-of groeps toewijzingen in azure AD.

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor inhoud in azure AD

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen** en selecteer vervolgens **alle toepassingen**.

   ![Scherm opname van het menu bedrijfs toepassingen in de Azure Portal, waarbij alle toepassingen zijn gemarkeerd.](common/enterprise-applications.png)

2. Selecteer **Contentful** in de lijst met toepassingen.

   ![Scherm afbeelding met de eerste 20 resultaten die in de lijst met toepassingen worden geretourneerd.](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

   ![Scherm afbeelding van het tabblad inrichten is gemarkeerd in de sectie beheren van het linkermenu.](common/provisioning.png)

4. Stel **Inrichtingsmodus** in op **Automatisch**.

   ![Scherm opname van de opties voor inrichtings modus, met automatisch gemarkeerd.](common/provisioning-automatic.png)

5. Voer in de sectie **beheerders referenties** uw URL voor de versleutelde Tenant en het geheime token in. Om ervoor te zorgen dat Azure AD verbinding kan maken met inhoud, selecteert u **verbinding testen**. Als de verbinding mislukt, zorg er dan voor dat uw account voor inhoud beheerders machtigingen heeft en probeer het opnieuw.

   ![Scherm opname van de tekst vakken Tenant U R L en geheim token, waarbij de knop verbinding testen is gemarkeerd.](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel vervolgens het selectie vakje **een e-mail melding verzenden wanneer een fout optreedt** in.

   ![Scherm opname van het tekstvak voor de meldings-e-mail.](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met inhoud**.

9. Controleer in de sectie **kenmerk toewijzing** de gebruikers kenmerken die vanuit Azure AD worden gesynchroniseerd met beinformatie. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikers accounts in contental te vergelijken voor update bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](../app-provisioning/customize-application-attributes.md)te wijzigen, moet u ervoor zorgen dat de API voor inhoud filtering voor het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen op inhoud synchroniseren**.

11. Controleer in de sectie **kenmerk toewijzing** de groeps kenmerken die vanuit Azure AD worden gesynchroniseerd met beinformatie. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in de richting van de update bewerkingen te vergelijken. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    |Kenmerk|Type|Ondersteund voor filteren|
    |---|---|---|
    |displayName|Tekenreeks|&check;|
    |leden|Naslaginformatie|

12. Als u bereik filters wilt instellen, voert u de stappen uit die worden beschreven in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik.

13. Als u de Azure AD-inrichtings service voor inhoud wilt inschakelen, selecteert u **op** in de sectie **instellingen** voor de **inrichtings status**.

    ![Scherm opname van de inrichtings status in-en uitschakelen.](common/provisioning-toggle-on.png)

14. Als u de gebruikers of groepen wilt definiëren die u wilt inrichten voor inhoud, selecteert u in de sectie **instellingen** voor **bereik** de relevante optie.

    ![Scherm opname van de opties die u kunt selecteren in het deel venster bereik.](common/provisioning-scope.png)

15. Selecteer **Opslaan** als u klaar bent om in te richten.

    ![Scherm afbeelding met de knop Opslaan en de knop Annuleren.](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie cyclus gestart van alle gebruikers en groepen die zijn gedefinieerd binnen het **bereik** onder **instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="monitor-your-deployment"></a>Uw implementatie bewaken

Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

* Bekijk de [inrichtings logboeken](../reports-monitoring/concept-provisioning-logs.md)om te bepalen welke gebruikers correct zijn ingericht of niet geslaagd.
* Controleer de [voortgangs balk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)om de status van de inrichtings cyclus te bekijken en te bepalen hoe dicht deze moet worden voltooid.
* Als de inrichtings configuratie een slechte status heeft, wordt de toepassing in quarantaine geplaatst. Meer informatie over [quarantaine statussen](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
* [Inrichting van gebruikersaccounts beheren voor bedrijfsapps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
