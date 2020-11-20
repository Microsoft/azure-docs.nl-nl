---
title: Azure Active Directory Connect Health bewerkingen
description: In dit artikel worden aanvullende bewerkingen beschreven die kunnen worden uitgevoerd nadat u Azure AD Connect Health hebt geïmplementeerd.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
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
ms.openlocfilehash: 91b7bd6d05ee0b992ee166b9acb13aff2e605a2c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961002"
---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Active Directory Connect Health bewerkingen
In dit onderwerp worden de verschillende bewerkingen beschreven die u kunt uitvoeren met behulp van Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>E-mail meldingen inschakelen
U kunt de Azure AD Connect Health-Service configureren om e-mail meldingen te verzenden wanneer waarschuwingen aangeven dat uw identiteits infrastructuur niet in orde is. Dit doet zich voor wanneer er een waarschuwing wordt gegenereerd en wanneer deze is opgelost.

![Scherm afbeelding van instellingen voor e-mail meldingen van Azure AD Connect Health](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> E-mail meldingen zijn standaard ingeschakeld.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Azure AD Connect Health e-mail meldingen inschakelen
1. Open de Blade **waarschuwingen** voor de service waarvoor u een e-mail melding wilt ontvangen.
2. Klik op de actie balk op **instellingen voor meldingen**.
3. Selecteer aan op de e-mail meldings optie **aan**.
4. Schakel het selectie vakje in als u wilt dat alle globale beheerders e-mail meldingen ontvangen.
5. Als u e-mail meldingen wilt ontvangen op elk gewenst e-mail adres, geeft u deze op in het vak **extra e-mail ontvangers** . Als u een e-mail adres uit deze lijst wilt verwijderen, klikt u met de rechter muisknop op het item en selecteert u **verwijderen**.
6. Klik op **Opslaan** om de wijzigingen te volt ooien. Wijzigingen worden pas van kracht nadat u hebt bespaard.

>[!NOTE] 
> Wanneer er problemen zijn met de verwerking van synchronisatie aanvragen in onze back-end-service, verzendt deze service een e-mail bericht met de details van de fout naar het e-mail adres (sen) van de beheerder van uw Tenant. We hebben feedback van klanten gehoord die in bepaalde gevallen het volume van deze berichten prohibitively groot zijn zodat we de manier wijzigen waarop we deze berichten verzenden. 
>
> In plaats van elke keer dat er een bericht wordt verzonden voor elke synchronisatie fout, verzenden we een dagelijks overzicht van alle fouten die de back-end-service heeft geretourneerd. Hierdoor kunnen klanten deze fouten op een efficiëntere manier verwerken en het aantal dubbele fout berichten verminderen.
>
> We plannen dat deze wijziging wordt geïmplementeerd op 15 januari 2020.

## <a name="delete-a-server-or-service-instance"></a>Een server-of service-exemplaar verwijderen

>[!NOTE] 
> De Azure AD Premium-licentie is vereist voor de stappen voor het verwijderen.

In sommige gevallen wilt u mogelijk een server verwijderen om te worden bewaakt. Dit is wat u moet weten als u een server uit de Azure AD Connect Health-Service wilt verwijderen.

Houd bij het verwijderen van een server rekening met het volgende:

* Met deze actie wordt het verzamelen van verdere gegevens van die server gestopt. Deze server is verwijderd uit de bewakings service. Na deze actie kunt u geen nieuwe waarschuwingen, bewaking of gebruiks analyse gegevens voor deze server weer geven.
* Met deze actie wordt de Health-Agent niet van de server verwijderd. Als u de Health-Agent niet hebt verwijderd voordat u deze stap uitvoert, worden er mogelijk fouten met betrekking tot de Health-Agent op de server weer geven.
* Met deze actie worden de gegevens die al zijn verzameld van deze server niet verwijderd. Deze gegevens worden verwijderd volgens het Azure data retentie beleid.
* Als u deze actie hebt uitgevoerd en u dezelfde server opnieuw wilt bewaken, moet u de Health-Agent op deze server verwijderen en opnieuw installeren.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Een server verwijderen uit de Azure AD Connect Health-Service

>[!NOTE] 
> De Azure AD Premium-licentie is vereist voor de stappen voor het verwijderen.

Azure AD Connect Health voor Active Directory Federation Services (AD FS) en Azure AD Connect (synchronisatie):

1. Open de Blade **Server** op de Blade **server lijst** door de server naam te selecteren die u wilt verwijderen.
2. Op de Blade **Server** klikt u op de actie balk op **verwijderen**.
![Scherm opname van Azure AD Connect Health server verwijderen](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Bevestig door de naam van de server in het bevestigings venster te typen.
4. Klik op **Verwijderen**.

Azure AD Connect Health voor Azure Active Directory Domain Services:

1. Open het dash board **domein controllers** .
2. Selecteer de domein controller die u wilt verwijderen.
3. Klik op de actie balk op **verwijderen geselecteerd**.
4. Bevestig de actie om de server te verwijderen.
5. Klik op **Verwijderen**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Een service-exemplaar verwijderen van Azure AD Connect Health Service
In sommige gevallen wilt u mogelijk een service-exemplaar verwijderen. Dit is wat u moet weten om een service-exemplaar te verwijderen uit de Azure AD Connect Health-Service.

Wanneer u een service-exemplaar verwijdert, moet u rekening houden met het volgende:

* Met deze actie wordt het huidige service-exemplaar verwijderd uit de bewakings service.
* Met deze actie wordt de Health-Agent niet verwijderd of verwijderd uit de servers die als onderdeel van dit service-exemplaar werden bewaakt. Als u de Health-Agent niet hebt verwijderd voordat u deze stap uitvoert, worden er mogelijk fouten met betrekking tot de Health-Agent op de servers weer geven.
* Alle gegevens van dit service-exemplaar worden verwijderd overeenkomstig het Azure-beleid voor gegevens retentie.
* Als u deze actie hebt uitgevoerd en u de service wilt bewaken, moet u de Health-Agent verwijderen en opnieuw installeren op alle servers. Als u deze actie hebt uitgevoerd en u dezelfde server opnieuw wilt bewaken, moet u de Health-Agent op die server verwijderen, opnieuw installeren en registreren.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Een service-exemplaar verwijderen uit de Azure AD Connect Health-Service
1. Open de Blade **service** op de Blade **service lijst** door de Service-id (Farm naam) te selecteren die u wilt verwijderen. 
2. Klik op de Blade **service** op de actie balk op **verwijderen**. 
![Scherm opname van Azure AD Connect Health Service verwijderen](./media/how-to-connect-health-operations/DeleteServer.png)
3. Bevestig door de service naam in het bevestigings venster te typen (bijvoorbeeld: sts.contoso.com).
4. Klik op **Verwijderen**.
   <br><br>

[//]: # (Het gedeelte voor het starten van RBAC)
## <a name="manage-access-with-azure-rbac"></a>Toegang beheren met Azure RBAC
[Toegangs beheer op basis van rollen (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) voor Azure AD Connect Health biedt toegang tot gebruikers en groepen, met uitzonde ring van globale beheerders. Azure RBAC wijst rollen toe aan de beoogde gebruikers en groepen en biedt een mechanisme voor het beperken van de globale beheerders in uw Directory.

### <a name="roles"></a>Rollen
Azure AD Connect Health ondersteunt de volgende ingebouwde rollen:

| Rol | Machtigingen |
| --- | --- |
| Eigenaar |Eigen aren kunnen de *toegang beheren* (bijvoorbeeld een rol toewijzen aan een gebruiker of groep), *alle informatie weer geven* (bijvoorbeeld waarschuwingen weer geven) vanuit de portal en *instellingen wijzigen* (bijvoorbeeld e-mail meldingen) in azure AD Connect Health. <br>Standaard worden aan Azure AD globale beheerders deze rol toegewezen. Dit kan niet worden gewijzigd. |
| Inzender |Inzenders kunnen *alle informatie weer geven* (bijvoorbeeld waarschuwingen weer geven) vanuit de portal en *instellingen wijzigen* (bijvoorbeeld e-mail meldingen) in azure AD Connect Health. |
| Lezer |Lezers kunnen *alle informatie weer geven* (bijvoorbeeld waarschuwingen weer geven) vanuit de portal binnen Azure AD Connect Health. |

Alle andere rollen (zoals beheerders van gebruikers toegang of DevTest Labs-gebruikers) hebben geen invloed op de toegang tot Azure AD Connect Health, zelfs niet als de functies beschikbaar zijn in de portal-ervaring.

### <a name="access-scope"></a>Toegangs bereik
Azure AD Connect Health biedt ondersteuning voor het beheren van toegang op twee niveaus:

* **Alle service-exemplaren**: dit is in de meeste gevallen het aanbevolen pad. Het beheert de toegang voor alle service-exemplaren (bijvoorbeeld een AD FS-Farm) voor alle typen rollen die door Azure AD Connect Health worden bewaakt.
* **Service-exemplaar**: in sommige gevallen moet u de toegang mogelijk scheiden op basis van roltype of een service-exemplaar. In dit geval kunt u de toegang beheren op het niveau van het service-exemplaar.  

Er wordt toestemming verleend als een eind gebruiker toegang heeft tot het niveau van de Directory of het service-exemplaar.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Toestaan dat gebruikers of groepen toegang hebben tot Azure AD Connect Health
De volgende stappen laten zien hoe u toegang kunt toestaan.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Stap 1: Selecteer het juiste toegangs bereik
Als u een gebruiker toegang wilt geven tot het niveau *alle service-exemplaren* binnen Azure AD Connect Health, opent u de hoofd Blade in azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Stap 2: gebruikers en groepen toevoegen en rollen toewijzen
1. Klik in de sectie **configureren** op **gebruikers**.<br>
   ![Scherm opname van Azure AD Connect Health resource zijbalk](./media/how-to-connect-health-operations/startRBAC.png)
2. Selecteer **Toevoegen**.
3. Selecteer in het deel venster **een rol selecteren** een rol (bijvoorbeeld **eigenaar**).<br>
   ![Scherm opname van Azure AD Connect Health en Azure RBAC-menu configureren](./media/how-to-connect-health-operations/RBAC_add.png)
4. Typ de naam of id van de doel gebruiker of-groep. U kunt een of meer gebruikers of groepen tegelijk selecteren. Klik op **Selecteren**.
   ![Scherm opname van Azure AD Connect Health en de lijst met Azure-rollen](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Selecteer **OK**.<br>
6. Nadat de roltoewijzing is voltooid, worden de gebruikers en groepen weer gegeven in de lijst.<br>
   ![Scherm opname van Azure AD Connect Health en Azure RBAC en nieuwe gebruikers gemarkeerd](./media/how-to-connect-health-operations/RBAC_user_list.png)

De vermelde gebruikers en groepen hebben nu toegang tot, volgens hun toegewezen rollen.

> [!NOTE]
> * Globale beheerders hebben altijd volledige toegang tot alle bewerkingen, maar globale beheerders accounts zijn niet aanwezig in de voor gaande lijst.
> * De functie gebruikers uitnodigen wordt niet ondersteund in Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Stap 3: de Blade locatie delen met gebruikers of groepen
1. Nadat u machtigingen hebt toegewezen, kan een gebruiker Azure AD Connect Health gebruiken door [hier](https://aka.ms/aadconnecthealth)te gaan.
2. Op de Blade kan de gebruiker de Blade of verschillende delen ervan vastmaken aan het dash board. Klik op het pictogram **vastmaken aan dash board** .<br>
   ![Scherm afbeelding van de Blade Azure AD Connect Health en Azure RBAC-pincode, met het pictogram pincode gemarkeerd](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Een gebruiker met de toegewezen rol van lezer kan geen Azure AD Connect Health-uitbrei ding ophalen van de Azure Marketplace. De gebruiker kan de benodigde bewerking ' maken ' daarom niet uitvoeren. De gebruiker kan nog steeds toegang krijgen tot de Blade door naar de vorige koppeling te gaan. Voor het volgende gebruik kan de gebruiker de Blade vastmaken aan het dash board.
>
>

### <a name="remove-users-or-groups"></a>Gebruikers of groepen verwijderen
U kunt een gebruiker of een groep die is toegevoegd aan Azure AD Connect Health en Azure RBAC verwijderen. Klik met de rechter muisknop op de gebruiker of groep en selecteer **verwijderen**.<br>
![Scherm afbeelding van Azure AD Connect Health en Azure RBAC met gemarkeerd verwijderen](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Sectie einde van RBAC)

## <a name="next-steps"></a>Volgende stappen
* [Azure AD Connect Health (Engelstalig)](./whatis-azure-ad-connect.md)
* [Installatie van Azure AD Connect Health-Agent](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health gebruiken met AD FS](how-to-connect-health-adfs.md)
* [Azure AD Connect Health gebruiken voor synchronisatie](how-to-connect-health-sync.md)
* [Azure AD Connect Health gebruiken met AD DS](how-to-connect-health-adds.md)
* [Veelgestelde vragen over Azure AD Connect Health](reference-connect-health-faq.md)
* [Versiegeschiedenis van Azure AD Connect Health](reference-connect-health-version-history.md)