---
title: 'Zelf studie: Configureer de grammatica voor automatische gebruikers inrichten met Azure Active Directory | Microsoft Docs'
description: Informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts vanuit Azure AD naar een grammaticaal.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: cd2dd9d7-4901-40c8-8888-98850557b072
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2021
ms.author: Zhchia
ms.openlocfilehash: ca01289ce66afe642081e5be17373e640dd1e46d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864802"
---
# <a name="tutorial-configure-grammarly-for-automatic-user-provisioning"></a>Zelf studie: de grammatica voor automatische gebruikers inrichting configureren

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel de grammatica als Azure Active Directory (Azure AD) om het automatisch inrichten van gebruikers te configureren. Wanneer de configuratie is geconfigureerd, worden gebruikers en [groepen door Azure](https://www.grammarly.com/) AD automatisch ingericht en ongedaan gemaakt met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers in een grammatica maken
> * Gebruikers synchroon verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken synchroon tussen Azure AD en Grammaticaën beveiligen

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikersaccount in Azure AD met [machtigingen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* Een grammaticaal zakelijk account met beheerders toegang.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
1. Bepaal wie u wilt opnemen in het [bereik voor inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
1. Bepalen welke gegevens moeten worden [toegewezen tussen Azure AD en de grammatica](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-grammarly-to-support-provisioning-with-azure-ad"></a>Stap 2. Stel Grammaticaën in om het inrichten met Azure AD te ondersteunen

Neem contact op met uw grammatica medewerker of schrijf naar <support@grammarly.com> om uw inrichtings token aan te vragen.

## <a name="step-3-add-grammarly-from-the-azure-ad-application-gallery"></a>Stap 3. Grammaticaën toevoegen vanuit de Azure AD-toepassings galerie

Voeg grammatica toe vanuit de Azure AD-toepassings galerie om het beheer van de inrichting te starten. Als u eerder grammatica voor eenmalige aanmelding hebt ingesteld, kunt u dezelfde toepassing gebruiken. U wordt aangeraden een afzonderlijke app te maken wanneer u de integratie in eerste instantie test. Zie [deze Quick](../manage-apps/add-application-portal.md)start voor meer informatie over het toevoegen van een toepassing uit de galerie.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting

U kunt de Azure AD Provisioning-Service gebruiken voor het bereik dat wordt ingericht op basis van de toewijzing aan de toepassing of op basis van de kenmerken van de gebruiker of groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereik filter gebruiken zoals beschreven in [apps inrichten met bereik filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Wanneer u gebruikers en groepen toewijst aan de grammatica, moet u een andere rol dan de **standaard toegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om meer rollen toe te voegen.

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u deze optie beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven.


## <a name="step-5-configure-automatic-user-provisioning-to-grammarly"></a>Stap 5. Automatische gebruikers inrichting op Grammaticaniveau configureren

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

### <a name="configure-automatic-user-provisioning-for-grammarly-in-azure-ad"></a>Automatische gebruikers inrichting voor Grammaticaën configureren in azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Schermopname van het deelvenster Ondernemingstoepassing.](common/enterprise-applications.png)

1. Selecteer in de lijst met toepassingen de optie **grammaticaf**.

    ![Scherm afbeelding met de grammaticale koppeling in de lijst met toepassingen.](common/all-applications.png)

1. Selecteer het tabblad **Inrichten**.

    ![Schermopname van het tabblad Inrichten.](common/provisioning.png)

1. Stel **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname met Inrichtingsmodus ingesteld op Automatisch.](common/provisioning-automatic.png)

1. Ga naar de sectie **beheerders referenties** en voer in het veld **Tenant-URL** invoeren in `https://sso.grammarly.com/scim/v2` en voer in het veld **geheim token** het token in dat door de grammatica is opgegeven (zie stap 2 hierboven). Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met de grammatica. Als de verbinding mislukt, zorg er dan voor dat uw grammaticale account beheerders machtigingen heeft en probeer het opnieuw.

    ![Scherm opname van de vakken Tenant-URL en geheim token.](common/provisioning-testconnection-tenanturltoken.png)

1. Voer in het vak **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de meldingen voor de inrichtingsfouten moeten ontvangen. Selecteer het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**.

    ![Schermopname van het vak E-mailmelding.](common/provisioning-notification-email.png)

1. Selecteer **Opslaan**.

1. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers op een grammaticaën synchroniseren**.

1. Controleer de gebruikers kenmerken die zijn gesynchroniseerd van Azure AD naar grammatica in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om de gebruikers accounts in de grammatica te vergelijken voor bijwerk bewerkingen. Als u het [overeenkomende doel kenmerk](../app-provisioning/customize-application-attributes.md)wijzigt, moet u ervoor zorgen dat de grammaticaal API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer **Opslaan** om eventuele wijzigingen toe te passen.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |userName|Tekenreeks|&check;|
   |externalId|Tekenreeks|
   |actief|Booleaans|
   |displayName|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|


1. Zie de instructies in de hand leiding voor het filteren op [bereik](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor meer informatie over het configureren van bereik filters.

1. Als u de Azure AD-inrichtings service op een **grammatica wilt inschakelen, moet u de** **inrichtings status** wijzigen in in het gedeelte **instellingen** .

    ![Schermopname van de Inrichtingsstatus ingeschakeld.](common/provisioning-toggle-on.png)

1. Definieer de gebruikers of groepen die u wilt inrichten als grammaticaal door de gewenste waarden in het **bereik** te selecteren in de sectie **instellingen** .

    ![Scherm opname van het inrichtings bereik.](common/provisioning-scope.png)

1. Selecteer **Opslaan** als u klaar bent om in te richten.

    ![Scherm afbeelding met de knop Opslaan.](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De eerste cyclus duurt langer dan volgende cycli, die ongeveer elke 40 minuten in beslag treden, zolang de Azure AD-inrichtings service wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken

Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

* Gebruik de [inrichtings logboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht.
* Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid.
* Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. Zie [toepassings inrichting status van quarantaine](../app-provisioning/application-provisioning-quarantine-status.md)voor meer informatie over quarantaine statussen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccounts inrichten voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
