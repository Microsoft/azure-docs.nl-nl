---
title: Azure Active Directory Connect Health-bewerkingen
description: In dit artikel worden aanvullende bewerkingen beschreven die kunnen worden uitgevoerd nadat u de Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 836c7bf9aefd4b2cb7d52c66bbd37e7ba38a467c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377307"
---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Active Directory Connect Health-bewerkingen
In dit onderwerp worden de verschillende bewerkingen beschreven die u kunt uitvoeren met Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>E-mailmeldingen inschakelen
U kunt de service Azure AD Connect Health om e-mailmeldingen te verzenden wanneer waarschuwingen aangeven dat uw identiteitsinfrastructuur niet in orde is. Dit gebeurt wanneer een waarschuwing wordt gegenereerd en wanneer deze wordt opgelost.

![Schermopname van Azure AD Connect Health instellingen voor e-mailmeldingen](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> E-mailmeldingen zijn standaard ingeschakeld.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>E-mailmeldingen Azure AD Connect Health inschakelen
1. Zoek in Azure Portal naar Azure AD Connect Health
2. **Synchronisatiefouten selecteren**
3. Selecteer **Meldingsinstellingen.**
5. Selecteer op de schakelaar voor e-mailmeldingen **de optie AAN.**
6. Schakel het selectievakje in als u wilt dat alle globale beheerders e-mailmeldingen ontvangen.
7. Als u e-mailmeldingen op andere e-mailadressen wilt ontvangen, geeft u deze op in het vak **Extra e-mailontvangers.** Als u een e-mailadres uit deze lijst wilt verwijderen, klikt u met de rechtermuisknop op de vermelding en selecteert u **Verwijderen.**
8. Klik op Opslaan om de wijzigingen te **maken.** Wijzigingen worden pas van kracht nadat u hebt op slaan.

>[!NOTE] 
> Wanneer er problemen zijn met het verwerken van synchronisatieaanvragen in onze back-upservice, stuurt deze service een e-mailmelding met de details van de fout naar de e-mailadressen van de contactpersoon met beheerdersaccounts van uw tenant. We hebben feedback van klanten gehoord dat in bepaalde gevallen het volume van deze berichten te groot is, dus we veranderen de manier waarop we deze berichten verzenden. 
>
> In plaats van steeds een bericht te verzenden voor elke synchronisatiefout, verzenden we een dagelijkse samenvatting van alle fouten die de back-endservice heeft geretourneerd. Hierdoor kunnen klanten deze fouten efficiënter verwerken en het aantal dubbele foutberichten verminderen.

## <a name="delete-a-server-or-service-instance"></a>Een server of service-exemplaar verwijderen

>[!NOTE] 
> Azure AD Premium-licentie is vereist voor de verwijderingsstappen.

In sommige gevallen wilt u mogelijk een server verwijderen om te worden bewaakt. Dit is wat u moet weten om een server te verwijderen uit de Azure AD Connect Health service.

Wanneer u een server wilt verwijderen, moet u rekening houden met het volgende:

* Met deze actie wordt het verzamelen van verdere gegevens van die server gestopt. Deze server wordt verwijderd uit de bewakingsservice. Na deze actie kunt u geen nieuwe waarschuwingen, bewakings- of gebruiksanalysegegevens meer weergeven voor deze server.
* Met deze actie wordt de health-agent niet van uw server verwijderd. Als u de Health-agent nog niet hebt verwijderd voordat u deze stap hebt uitgevoerd, ziet u mogelijk fouten met betrekking tot de health-agent op de server.
* Met deze actie worden de gegevens die al van deze server zijn verzameld, niet verwijderd. Deze gegevens worden verwijderd in overeenstemming met het Azure-beleid voor gegevensretentie.
* Als u na het uitvoeren van deze actie opnieuw wilt beginnen met het bewaken van dezelfde server, moet u de health-agent op deze server verwijderen en opnieuw installeren.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Een server verwijderen uit de Azure AD Connect Health service

>[!NOTE] 
> Azure AD Premium-licentie is vereist voor de verwijderingsstappen.

Azure AD Connect Health voor Active Directory Federation Services (AD FS) en Azure AD Connect (Synchroniseren):

1. Open de blade **Server** op de blade **Serverlijst** door de servernaam te selecteren die moet worden verwijderd.
2. Klik op **de** blade Server in de actiebalk op **Verwijderen.**
![Schermopname van Azure AD Connect Health server verwijderen](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Bevestig dit door de servernaam in het bevestigingsvak te typen.
4. Klik op **Verwijderen**.

Azure AD Connect Health voor Azure Active Directory Domain Services:

1. Open het dashboard **Domeincontrollers.**
2. Selecteer de domeincontroller die u wilt verwijderen.
3. Klik in de actiebalk op **Geselecteerde verwijderen.**
4. Bevestig de actie om de server te verwijderen.
5. Klik op **Verwijderen**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Een service-exemplaar verwijderen uit Azure AD Connect Health service
In sommige gevallen wilt u mogelijk een service-exemplaar verwijderen. Dit is wat u moet weten om een service-exemplaar te verwijderen uit de Azure AD Connect Health service.

Wanneer u een service-exemplaar wilt verwijderen, moet u rekening houden met het volgende:

* Met deze actie wordt het huidige service-exemplaar uit de bewakingsservice verwijderd.
* Met deze actie wordt de health-agent niet verwijderd van een van de servers die zijn bewaakt als onderdeel van dit service-exemplaar. Als u de health-agent niet hebt verwijderd voordat u deze stap hebt uitvoeren, ziet u mogelijk fouten met betrekking tot de statusagent op de servers.
* Alle gegevens van dit service-exemplaar worden verwijderd in overeenstemming met het Azure-gegevensretentiebeleid.
* Als u na het uitvoeren van deze actie wilt beginnen met het bewaken van de service, verwijdert u de Health-agent en installeert u deze opnieuw op alle servers. Als u na het uitvoeren van deze actie opnieuw wilt beginnen met het bewaken van dezelfde server, verwijdert, installeert en registreert u de health-agent op die server.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Een service-exemplaar verwijderen uit de Azure AD Connect Health service
1. Open de blade **Service** op de blade **Servicelijst** door de service-id (farmnaam) te selecteren die u wilt verwijderen. 
2. Klik op **de** blade Service op de actiebalk op **Verwijderen.** 
![Schermopname van Azure AD Connect Health service verwijderen](./media/how-to-connect-health-operations/DeleteServer.png)
3. Bevestig dit door de servicenaam in het bevestigingsvak te typen (bijvoorbeeld: sts.contoso.com).
4. Klik op **Verwijderen**.
   <br><br>

[//]: # (Sectie Start van RBAC)
## <a name="manage-access-with-azure-rbac"></a>Toegang beheren met Azure RBAC
[Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) voor Azure AD Connect Health biedt toegang tot andere gebruikers en groepen dan globale beheerders. Azure RBAC wijst rollen toe aan de beoogde gebruikers en groepen en biedt een mechanisme om de globale beheerders binnen uw directory te beperken.

### <a name="roles"></a>Rollen
Azure AD Connect Health ondersteunt de volgende ingebouwde rollen:

| Rol | Machtigingen |
| --- | --- |
| Eigenaar |Eigenaren kunnen toegang *beheren* (bijvoorbeeld een rol toewijzen aan een gebruiker of *groep),* alle informatie (bijvoorbeeld waarschuwingen weergeven) vanuit de portal bekijken en instellingen *(bijvoorbeeld* e-mailmeldingen) wijzigen in Azure AD Connect Health. <br>Standaard krijgen globale Azure AD-beheerders deze rol toegewezen en dit kan niet worden gewijzigd. |
| Inzender |Inzenders *kunnen alle* informatie (bijvoorbeeld waarschuwingen weergeven) in de portal bekijken en instellingen (bijvoorbeeld e-mailmeldingen) wijzigen in Azure AD Connect Health.  |
| Lezer |Lezers kunnen *alle informatie* (bijvoorbeeld waarschuwingen weergeven) in de portal in Azure AD Connect Health. |

Alle andere rollen (zoals Gebruikerstoegangbeheerders of DevTest Labs-gebruikers) hebben geen invloed op toegang binnen Azure AD Connect Health, zelfs niet als de rollen beschikbaar zijn in de portalervaring.

### <a name="access-scope"></a>Toegangsbereik
Azure AD Connect Health ondersteunt het beheren van toegang op twee niveaus:

* **Alle service-exemplaren:** dit is in de meeste gevallen het aanbevolen pad. Hiermee bepaalt u de toegang voor alle service-exemplaren (bijvoorbeeld een AD FS-farm) voor alle roltypen die worden bewaakt door Azure AD Connect Health.
* **Service-exemplaar:** In sommige gevallen moet u de toegang mogelijk scheiden op basis van roltypen of door een service-exemplaar. In dit geval kunt u de toegang beheren op het niveau van het service-exemplaar.  

Er wordt toestemming verleend als een eindgebruiker toegang heeft op het niveau van de directory of het service-exemplaar.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Gebruikers of groepen toegang geven tot Azure AD Connect Health
De volgende stappen laten zien hoe u toegang toestaat.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Stap 1: selecteer het juiste toegangsbereik
Als u een gebruiker toegang wilt geven op het niveau *van* alle service-exemplaren binnen Azure AD Connect Health, opent u de hoofdblade in Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Stap 2: gebruikers en groepen toevoegen en rollen toewijzen
1. Klik in **de sectie** Configureren op **Gebruikers.**<br>
   ![Schermopname van Azure AD Connect Health resource zijbalk](./media/how-to-connect-health-operations/startRBAC.png)
2. Selecteer **Toevoegen**.
3. Selecteer in **het deelvenster** Een rol selecteren een rol (bijvoorbeeld **Eigenaar**).<br>
   ![Schermopname van Azure AD Connect Health azure RBAC-menu configureren](./media/how-to-connect-health-operations/RBAC_add.png)
4. Typ de naam of id van de beoogde gebruiker of groep. U kunt een of meer gebruikers of groepen tegelijk selecteren. Klik op **Selecteren**.
   ![Schermopname van Azure AD Connect Health en Azure-rollijst](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Selecteer **OK**.<br>
6. Nadat de roltoewijzing is voltooid, worden de gebruikers en groepen weergegeven in de lijst.<br>
   ![Schermopname van Azure AD Connect Health azure RBAC en nieuwe gebruikers gemarkeerd](./media/how-to-connect-health-operations/RBAC_user_list.png)

De vermelde gebruikers en groepen hebben nu toegang op basis van hun toegewezen rollen.

> [!NOTE]
> * Globale beheerders hebben altijd volledige toegang tot alle bewerkingen, maar globale beheerdersaccounts zijn niet aanwezig in de voorgaande lijst.
> * De functie Invite Users wordt niet ondersteund in Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Stap 3: de bladelocatie delen met gebruikers of groepen
1. Nadat u machtigingen hebt toegewezen, heeft een gebruiker toegang Azure AD Connect Health door hier te [gaan.](https://aka.ms/aadconnecthealth)
2. Op de blade kan de gebruiker de blade of verschillende onderdelen ervan vastmaken aan het dashboard. Klik op het **pictogram Vastmaken aan dashboard.**<br>
   ![Schermopname van Azure AD Connect Health en azure RBAC-speldblade, met het speldpictogram gemarkeerd](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Een gebruiker met de toegewezen rol Lezer kan de extensie Azure AD Connect Health de Azure Marketplace. De gebruiker kan de benodigde 'create'-bewerking niet uitvoeren om dit te doen. De gebruiker kan nog steeds toegang krijgen tot de blade door naar de voorgaande koppeling te gaan. Voor later gebruik kan de gebruiker de blade vastmaken aan het dashboard.
>
>

### <a name="remove-users-or-groups"></a>Gebruikers of groepen verwijderen
U kunt een gebruiker of een groep die is toegevoegd aan Azure AD Connect Health en Azure RBAC verwijderen. Klik met de rechtermuisknop op de gebruiker of groep en selecteer **Verwijderen.**<br>
![Schermopname van Azure AD Connect Health en Azure RBAC met Verwijderen gemarkeerd](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Sectie Einde van RBAC)

## <a name="next-steps"></a>Volgende stappen
* [Azure AD Connect Health (Engelstalig)](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health agent installeren](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health gebruiken met AD FS](how-to-connect-health-adfs.md)
* [Een Azure AD Connect Health voor synchronisatie gebruiken](how-to-connect-health-sync.md)
* [Azure AD Connect Health gebruiken met AD DS](how-to-connect-health-adds.md)
* [Veelgestelde vragen over Azure AD Connect Health](reference-connect-health-faq.md)
* [Versiegeschiedenis van Azure AD Connect Health](reference-connect-health-version-history.md)
