---
title: 'Zelfstudie: Tic-Tac Mobile configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikersaccounts van Azure AD naar Tic-Tac Mobile.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: 91ae51b9a2785dbc40c55fa58b26763916e8d16c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644575"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>Zelfstudie: Tic-Tac Mobile configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel Tic-Tac Mobile als Azure Active Directory (Azure AD) voor het configureren van automatische inrichting van gebruikers. Wanneer de configuratie is voltooid, wordt inrichting en ongedaan maken van inrichting van gebruikers en groepen door Azure AD automatisch uitgevoerd op [Tic-Tac Mobile](https://www.tictacmobile.com/) met behulp van de Azure AD-inrichtingsservice. Raadpleeg [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen (software-as-a-service) met Azure AD](../app-provisioning/user-provisioning.md) voor informatie over wat deze service doet, hoe het werkt en veelgestelde vragen.


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden

> [!div class="checklist"]
> * Gebruikers maken Tic-Tac Mobile.
> * Gebruikers uit Tic-Tac Mobile verwijderen wanneer ze geen toegang meer nodig hebben.
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Tic-Tac Mobile.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](../develop/quickstart-create-new-tenant.md).
* Een gebruikersaccount in Azure AD met [machtiging](../roles/permissions-reference.md) om inrichting te configureren. Voorbeelden zijn Toepassingsbeheerder, Cloudtoepassingsbeheerder, Toepassingseigenaar of Globale beheerder.
* Een [Tic-Tac Mobile](https://www.tictacmobile.com/)-account met de rol superbeheerder.


## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen

1. Lees [hoe de inrichtingsservice werkt](../app-provisioning/user-provisioning.md).
1. Bepaal wie u wilt opnemen in het [bereik voor inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Tic-Tac Mobile](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>Stap 2. Tic-Tac Mobile configureren om ondersteuning te bieden voor inrichting met Azure AD

Neem contact op met support@tictacmobile.com om uw **Tenant-URL** en **Token voor geheim** te verkrijgen. U moet een superbeheerdersrol hebben in Tic-Tac Mobile om een token te ontvangen. Dit token wordt ingevoerd in het vak **Token voor geheim** op het tabblad **Inrichten** van uw Tic-Tac Mobile-toepassing in Azure Portal.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>Stap 3. Tic-Tac Mobile toevoegen vanuit de galerie met Azure AD-toepassingen

Voeg Tic-Tac Mobile toe vanuit de galerie met Azure AD-toepassingen om te beginnen met het inrichten voor Tic-Tac Mobile. Als u Tic-Tac Mobile eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. Wanneer u de integratie in eerste instantie wilt testen, maakt u een afzonderlijke app. Zie voor meer informatie over het toevoegen van een toepassing uit de galerie [Pp kenmerken gebaseerde toepassing inrichten met bereikfilters](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing of op basis van kenmerken van de gebruiker of groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, volgt u de stappen in [Gebruikerstoewijzing voor een app beheren in Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, gebruikt u een bereikfilter zoals beschreven in [Op kenmerk gebaseerde toepassingsinrichting met bereikfilters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Wanneer u gebruikers en groepen toewijst aan Tic-Tac Mobile, moet u een andere rol dan **Standaardtoegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](../develop/howto-add-app-roles-in-azure-ad-apps.md) om meer rollen toe te voegen.
* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit blijven beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) opgeven.

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>Stap 5. Automatische gebruikersinrichting configureren voor Tic-Tac Mobile

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtingsservice om gebruikers of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Tic-Tac Mobile in Azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Schermopname van het deelvenster Ondernemingstoepassing.](common/enterprise-applications.png)

1. Selecteer **Tic-Tac Mobile** in de lijst met toepassingen.

    ![Schermopname van de Tic-Tac Mobile-link in de lijst Toepassingen.](common/all-applications.png)

1. Selecteer het tabblad **Inrichten**.

    ![Schermopname van het tabblad Inrichten.](common/provisioning.png)

1. Stel **Inrichtingsmodus** in op **Automatisch**.

    ![Schermopname van de optie Automatisch op het tabblad Inrichten.](common/provisioning-automatic.png)

1. Voer in de sectie **Beheerdersreferenties** de **Tenant-URL** en het **token voor geheim** van uw Tic-Tac Mobile in. Selecteer **Verbinding testen** om te controleren of Azure AD verbinding kan maken met Tic-Tac Mobile. Als de verbinding mislukt, moet u controleren of uw Tic-Tac Mobile-account beheerdersmachtigingen heeft. Probeer het daarna opnieuw.

    ![Schermopname met het vak Geheim token.](common/provisioning-testconnection-tenanturltoken.png)

1. Voer in het vak **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de meldingen voor de inrichtingsfouten moeten ontvangen. Selecteer het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**.

    ![Schermopname van het vak E-mailmelding.](common/provisioning-notification-email.png)

1. Selecteer **Opslaan**.

1. Selecteer in de sectie **Toewijzingen** de optie **Azure Active Directory-gebruikers synchroniseren met Tic-Tac Mobile**.

1. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die vanuit Azure AD met Tic-Tac Mobile worden gesynchroniseerd. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikersaccounts in Tic-Tac Mobile te vinden voor updatebewerkingen. Als u het [overeenkomende doelkenmerk](../app-provisioning/customize-application-attributes.md) wijzigt, moet u ervoor zorgen dat de API van Tic-Tac Mobile het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |externalId|Tekenreeks|
   |title|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |preferredLanguage|Tekenreeks|
   |externalId|Tekenreeks|
   |userType|Tekenreeks|
   |landinstelling|Tekenreeks|
   |timezone|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Tekenreeks|

1. Raadpleeg de instructies in de [Zelfstudie bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) als u bereikfilters wilt configureren.

1. Wijzig **Inrichtingsstatus** naar **Aan** in de sectie **Instellingen** om de Azure AD-inrichtingsservice in te schakelen voor Tic-Tac Mobile.

    ![Schermopname van de Inrichtingsstatus ingeschakeld.](common/provisioning-toggle-on.png)

1. Definieer de gebruikers of groepen die u aan Tic-Tac Mobile wilt toevoegen door de gewenste waarden te selecteren in **Bereik** in de sectie **Instellingen**.

    ![Schermopname van het inrichtingsbereik.](common/provisioning-scope.png)

1. Selecteer **Opslaan** als u klaar bent voor het inrichten.

    ![Schermopname van het opslaan van de inrichtingsconfiguratie.](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken

Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken.

1. Gebruik de [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md) om te bepalen welke gebruikers al dan niet met succes zijn ingericht.
1. Controleer de [voortgangsbalk](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid.
1. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. Zie [Toepassing inrichten in quarantainestatus](../app-provisioning/application-provisioning-quarantine-status.md) voor meer informatie over quarantainestatussen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccounts inrichten voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)