---
title: Externe toegang beveiligen met groepen in Azure Active Directory en Microsoft 365
description: Azure Active Directory en Microsoft 365 groepen kunnen worden gebruikt om de beveiliging te verbeteren wanneer externe gebruikers toegang hebben tot uw resources.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f83e5584f8f9c6823e1259cb5e6034d8b13ae3a6
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222374"
---
# <a name="securing-external-access-with-groups"></a>Externe toegang beveiligen met groepen 

Groepen zijn een essentieel onderdeel van elke strategie voor toegangs beheer. Azure Active Directory-groepen (Azure AD) en Microsoft 365 (M365) kunnen worden gebruikt als basis voor het beveiligen van de toegang tot bronnen.

Groepen zijn de beste optie om te gebruiken als basis voor de volgende toegangs beheer mechanismen:

* Beleid voor voorwaardelijke toegang

* Rechten beheer toegangs pakketten 

* Toegang tot M365-resources, micro soft-teams en share point-sites

Groepen hebben de volgende rollen:

* Eigen aren: groeps eigenaren beheren de groeps instellingen en het bijbehorende lidmaatschap.

* Leden: leden die de machtigingen en toegang overnemen die aan de groep zijn toegewezen.

* Gasten: gasten zijn leden van buiten uw organisatie. 

## <a name="determine-your-group-strategy"></a>Uw groeps strategie bepalen

Wanneer u de groeps strategie ontwikkelt om externe toegang tot uw resources te beveiligen, moet u rekening houden met de [gewenste beveiligings postuur](1-secure-access-posture.md) om de volgende opties te bepalen.

* **Wie moet groepen kunnen maken?** Wilt u alleen beheerders groepen maken of wilt u dat werk nemers en externe gebruikers deze groepen ook maken.

   * *Elk Tenant lid kan standaard Azure AD-beveiligings groepen maken*. 

      * U kunt [de toegang tot de portal beperken voor niet-beheerders en de mogelijkheid tot het](../develop/howto-restrict-your-app-to-a-set-of-users.md) maken van groepen uitschakelen in [Power shell.](../users-groups-roles/groups-troubleshooting.md) 

      * U kunt ook [self-service groeps beheer instellen in azure Active Directory](../users-groups-roles/groups-self-service-management.md). 

   * *Standaard kunnen alle gebruikers M365 groepen en groepen maken zijn geopend voor alle (interne en externe) gebruikers in uw Tenant om lid te* worden. 

      * [U kunt het maken van Microsoft 365 groepen beperken](https://docs.microsoft.com/microsoft-365/solutions/manage-creation-of-groups?view=o365-worldwide) tot de leden van een bepaalde beveiligings groep. Gebruik Windows Power shell om deze instelling te configureren. 

* **Wie moet personen kunnen uitnodigen voor groepen?** Kunnen alle leden van de groep andere leden toevoegen, of kunnen alleen leden van de groep eigen aren toevoegen?

* **Wie kan worden uitgenodigd voor groepen?** Standaard kunnen externe gebruikers worden toegevoegd aan groepen. 

### <a name="assign-users-to-groups"></a>Gebruikers toewijzen aan groepen

Gebruikers kunnen beide hand matig worden toegewezen aan groepen op basis van de gebruikers kenmerken in hun gebruikers object of op andere criteria. Gebruikers kunnen alleen dynamisch aan groepen worden toegewezen op basis van hun kenmerken.

U kunt bijvoorbeeld gebruikers toewijzen aan groepen op basis van hun:

* specifieke functie of afdeling

* partner organisatie waartoe ze behoren (hand matig of via verbonden organisaties)

* gebruikers type (lid of gast)

* deel nemen aan een specifiek project (hand matig)

* location

Dynamische groepen kunnen gebruikers of apparaten bevatten, maar niet beide. U kunt query's toevoegen op basis van gebruikers kenmerken om gebruikers toe te wijzen aan de dynamische groep. In het onderstaande voor beeld ziet u query's waarmee gebruikers aan de groep worden toegevoegd als ze lid zijn (geen gasten) en op de afdeling Financiën.

![Scherm opname van het configureren van dynamische lidmaatschaps regels.](media/secure-external-access/4-dynamic-membership-rules.png)

Zie [een dynamische groep maken of bijwerken in azure Active Directory](../users-groups-roles/groups-create-rule.md) voor meer informatie over dynamische groepen.

### <a name="do-not-use-groups-for-multiple-purposes"></a>Gebruik geen groepen voor meerdere doel einden

Wanneer u groepen gebruikt voor beveiligings-of bron toegang, is het belang rijk dat ze één functie hebben. Als er een groep wordt gebruikt om toegang te verlenen aan bronnen, mag deze niet voor andere doel einden worden gebruikt. Als er een groep wordt gebruikt voor algemene doel einden, zoals het definiëren van de locatie of het lidmaatschap van het team, moet deze ook niet worden gebruikt voor het beveiligen van de toegang. 

We raden u aan een naamgevings Conventie te maken voor beveiligings groepen die het doel duidelijk maken. Bijvoorbeeld:

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>Typen groepen

Zowel Azure AD-beveiligings groepen als Microsoft 365 groepen kunnen worden gemaakt via de Azure AD-portal of de M365-beheer Portal. Beide typen kunnen worden gebruikt als basis voor de beveiliging van externe toegang:

|Overwegingen | Azure AD-beveiligings groepen (hand matig en dynamisch)| Microsoft 365 groepen |
| - | - | - |
| Wat kan de groep bevatten?| Gebruikers<br>Groepen<br>Serviceprincipes<br>Apparaten| Alleen gebruikers |
| Waar wordt de groep gemaakt?| Azure AD-portal<br>M365-Portal (als e-mail adres is ingeschakeld)<br>PowerShell<br>Microsoft Graph<br>Portal voor eind gebruikers| M365-Portal<br>Azure AD-portal<br>PowerShell<br>Microsoft Graph<br>In Microsoft 365 toepassingen |
| Wie maakt standaard het?| Beheerders <br>Eind gebruikers| Beheerders<br>Eind gebruikers |
| Wie kan standaard worden toegevoegd?| Interne gebruikers (leden)| Tenant leden en gasten van elke organisatie |
| Waarvoor wordt toegang verleend?| Alleen resources waaraan deze is toegewezen.| Alle resources met betrekking tot de groep:<br>(Groeps postvak, site, team, chats en andere opgenomen M365-resources)<br>Alle andere resources waaraan de groep is toegevoegd |
| Kan worden gebruikt met| Voorwaardelijke toegang<br>Beheer rechten<br>Groeps licenties| Voorwaardelijke toegang<br>Beheer rechten<br>Vertrouwelijkheidslabels |



Gebruik Microsoft 365 groepen om een set Microsoft 365 resources te maken en te beheren, zoals een team en de gekoppelde sites en inhoud. Ze zijn een uitstekende keuze voor een op een project gebaseerde inspanning. 

 

## <a name="azure-ad-security-groups"></a>Azure AD-beveiligingsgroepen 

[Azure AD-beveiligings groepen](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups) kunnen gebruikers of apparaten bevatten en kunnen worden gebruikt om toegang te beheren tot 

* Azure-resources, zoals Microsoft 365 apps, aangepaste apps en SaaS-apps (Software as a Service), zoals ServiceNow van Dropbox.

* Azure-gegevens en-abonnementen.

* Azure-Services.

Azure AD-beveiligings groepen kunnen ook worden gebruikt voor het volgende:

* Wijs licenties toe voor services zoals M365, Dynamics 365 en Enter prise Mobility en Security. Zie voor meer informatie [op groepen gebaseerde licentie verlening](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal).

* verhoogde machtigingen toewijzen. Zie [Cloud groepen gebruiken voor het beheren van roltoewijzingen (preview-versie](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-groups-concept)) voor meer informatie. 

Als u een groep wilt maken [in de Azure Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) navigeert u naar Azure Active Directory en vervolgens naar groepen. U kunt ook Azure AD-beveiligings groepen maken met behulp van [Power shell-cmdlets](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets). 

> [!NOTE]
> Een beveiligings groep kan worden gebruikt voor het toewijzen van Maxi maal 1500 toepassingen, maar niet meer. 

![Scherm opname van het maken van een beveiligings groep.](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **Als u een beveiligings groep voor E-mail wilt maken, gaat u naar het [Microsoft 365-beheer centrum](https://admin.microsoft.com/)**. U kunt deze niet maken in de Azure AD-Portal. 
<br>U moet een beveiligings groep voor e-mail inschakelen op het moment dat deze wordt gemaakt. U kunt deze later niet meer inschakelen.

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>Hybride organisaties en Azure AD-beveiligings groepen

Hybride organisaties hebben zowel een on-premises infra structuur als een Azure AD-Cloud infrastructuur. Veel hybride organisaties die gebruikmaken van Active Directory hun beveiligings groepen on-premises maken en deze synchroniseren met de Cloud. Met deze methode kunnen alleen gebruikers in de on-premises omgeving worden toegevoegd aan de beveiligings groepen.

**Uw on-premises infra structuur beveiligen tegen inbreuk, omdat een schending on-premises kan worden gebruikt om toegang te krijgen tot uw Microsoft 365-Tenant**. Zie [Microsoft 365 beschermen tegen on-premises aanvallen](https://aka.ms/protectm365) voor hulp.

## <a name="microsoft-365-groups"></a>Microsoft 365 groepen

[Microsoft 365 groepen](https://docs.microsoft.com/microsoft-365/admin/create-groups/office-365-groups?view=o365-worldwide) zijn de basis lidmaatschaps service waarmee alle toegang via M365 wordt geclusterd. Ze kunnen worden gemaakt op basis van de [Azure Portal](https://portal.azure.com/), of de [M365-Portal](https://admin.microsoft.com/). Wanneer een M365-groep is gemaakt, kunt u toegang verlenen tot een groep resources die wordt gebruikt om samen te werken. Zie [overzicht van Microsoft 365 groepen voor beheerders](https://docs.microsoft.com/microsoft-365/admin/create-groups/office-365-groups?view=o365-worldwide) voor een volledige lijst met deze resources.

M365-groepen hebben de volgende nuances voor hun rollen

* **Eigen** aren: groeps eigenaren kunnen leden toevoegen of verwijderen en beschikken over unieke machtigingen zoals de mogelijkheid om gesp rekken uit het gedeelde Postvak in te verwijderen of door de groeps instellingen te wijzigen. Groeps eigenaren kunnen de naam van de groep wijzigen, de beschrijving of afbeelding bijwerken en nog veel meer.

* **Leden** : leden hebben toegang tot alles in de groep, maar kunnen geen groeps instellingen wijzigen. Standaard kunnen groeps leden gasten uitnodigen om lid te worden van uw groep, maar u kunt [deze instelling beheren](https://docs.microsoft.com/microsoft-365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide).

* **Gasten-groep** gasten zijn leden die afkomstig zijn van buiten uw organisatie. Gasten hebben standaard enkele limieten voor de functionaliteit van teams.

 

### <a name="m365-group-settings"></a>Groeps instellingen M365

U selecteert e-mail alias, privacy en of de groep voor teams moet worden ingeschakeld op het moment van installatie. 

![Scherm opname van het bewerken van Microsoft 365 groeps instellingen](media/secure-external-access/4-edit-group-settings.png)

Na de installatie kunt u leden toevoegen en instellingen configureren voor het gebruik van e-mail, enzovoort.

### <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over het beveiligen van externe toegang tot bronnen. U wordt aangeraden de acties in de vermelde volg orde uit te voeren.

1. [Bepaal de gewenste beveiligings postuur voor externe toegang](1-secure-access-posture.md)

2. [Uw huidige status ontdekken](2-secure-access-current-state.md)

3. [Een governance-plan maken](3-secure-access-plan.md)

4. [Gebruik groepen voor beveiliging](4-secure-access-groups.md) (u bent hier.)

5. [Overgang naar Azure AD B2B](5-secure-access-b2b.md)

6. [Veilige toegang met het rechten beheer](6-secure-access-entitlement-managment.md)

7. [Veilige toegang met beleid voor voorwaardelijke toegang](7-secure-access-conditional-access.md)

8. [Veilige toegang met gevoeligheids labels](8-secure-access-sensitivity-labels.md)

9. [Beveiligde toegang tot micro soft teams, OneDrive en share point](9-secure-access-teams-sharepoint.md)