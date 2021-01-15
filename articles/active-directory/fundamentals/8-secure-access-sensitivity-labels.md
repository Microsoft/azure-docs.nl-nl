---
title: Beheer externe toegang tot resources in Azure Active Directory met gevoeligheids labels.
description: Gebruik gevoeligheids labels als onderdeel van uw algemene beveiligings plan voor externe toegang.
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
ms.openlocfilehash: 71031c7f5db299fbb1b7c99014c30590fec89f03
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222289"
---
# <a name="control-access-with-sensitivity-labels"></a>Toegang beheren met gevoeligheids labels 

Met [gevoeligheids labels](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels?view=o365-worldwide) kunt u de toegang tot uw inhoud beheren in Office 365-toepassingen en in containers zoals micro soft-Teams, Microsoft 365-groepen en share point-sites. Ze kunnen uw inhoud beschermen zonder de samenwerkings-en productie vaardigheden van uw gebruikers te belemmeren. Met gevoeligheids labels kunt u de inhoud van uw organisatie verzenden naar apparaten, apps en services, terwijl u uw gegevens beveiligt en uw nalevings-en beveiligings beleid beantwoordt. 

Met gevoeligheids labels kunt u het volgende doen:

* **Inhoud classificeren zonder beveiligings instellingen toe te voegen**. U kunt een classificatie toewijzen aan inhoud (zoals een sticker) die persistent wordt gemaakt en de inhoud doorneemt wanneer deze wordt gebruikt en gedeeld. U kunt deze classificatie gebruiken om gebruiksrapporten te genereren en om activiteitsgegevens voor uw gevoelige inhoud te zien.

* **Beveiligings instellingen afdwingen, zoals versleuteling, water merken en toegangs beperkingen**. Gebruikers kunnen bijvoorbeeld een vertrouwelijk label op een document of e-mail bericht Toep assen en dat label kan [de inhoud versleutelen](https://docs.microsoft.com/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide) en een ' vertrouwelijk ' water merk toevoegen. Daarnaast kunt u [een gevoeligheids label Toep assen op een container](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide) , zoals een share point-site, en afdwingen of externe gebruikers toegang hebben tot de inhoud die het bevat.

Gevoeligheids labels voor e-mail en andere inhoud die met de inhoud worden getransporteerd. Gevoeligheids labels op containers kunnen de toegang tot de container beperken, maar de inhoud in de container neemt het label niet over. Een gebruiker kan bijvoorbeeld inhoud ophalen van een beveiligde site, deze downloaden en deze vervolgens delen zonder beperkingen, tenzij de inhoud ook een gevoeligheids label had.

 >[!NOTE]
>Voor het Toep assen van gevoelige labels moeten gebruikers zijn aangemeld bij hun micro soft-werk-of school account. 

 
## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>Benodigde machtigingen voor het maken en beheren van gevoeligheids niveaus

Leden van uw nalevings team die gevoeligheids labels maken, hebben machtigingen nodig voor het Microsoft 365 compliance Center, Microsoft 365 Security Center of het Security & compliance Center.

Globale beheerders voor uw Tenant hebben standaard toegang tot deze beheer centra en kunnen nalevings managers en andere personen toegang geven, zonder dat ze alle machtigingen van een Tenant beheerder hebben. Voor deze gedelegeerde beperkte beheerders toegang voegt u gebruikers toe aan de rol groep nalevings gegevens beheerder, nalevings beheerder of beveiligings beheerder.

 

## <a name="determine-your-sensitivity-label-strategy"></a>Uw gevoeligheids label strategie bepalen

Bepaal het volgende als u wilt weten hoe u externe toegang tot uw inhoud bepaalt:

**Voor alle inhoud en containers**

* Hoe kunt u bepalen wat het effect van hoog, gemiddeld of laag bedrijf is (HBI, MBI, LBI)? Houd rekening met de impact van uw organisatie als specifieke typen inhoud op een juiste manier worden gedeeld.

   * Inhoud met specifieke typen [inhoud die vertrouwelijk gevoelig](https://docs.microsoft.com/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)is, zoals credit cards of pass Port-nummers

   * Inhoud die is gemaakt door specifieke groepen of personen (bijvoorbeeld nalevings ambtenaren, financiële functionarissen of leidinggevenden)

   * Inhoud in specifieke bibliotheken of sites. Bijvoorbeeld containers die fungeren als host voor organisatie strategie of persoonlijke financiële gegevens

   * Andere criteria

* Welke categorieën inhoud (bijvoorbeeld HBI-inhoud) moeten worden beperkt van toegang door externe gebruikers?

   * Beperkingen zijn onder andere acties zoals het beperken van de toegang tot containers en het versleutelen van inhoud.

* Welke standaard waarden moeten er worden gebruikt voor HBI-gegevens,-sites of-Microsoft 365 groepen?

* Waar gebruikt u gevoeligheids labels voor [labelen en bewaken](https://docs.microsoft.com/microsoft-365/compliance/label-analytics?view=o365-worldwide), vergeleken met het [afdwingen van versleuteling](https://docs.microsoft.com/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide) of het [afdwingen van toegangs beperkingen voor containers](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)?

**Voor e-mail en inhoud**

* Wilt u [automatisch gevoeligheids labels Toep assen](https://docs.microsoft.com/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide) op inhoud of dit hand matig doen?

   * Als u ervoor kiest om dit hand matig te doen, wilt u [de gebruikers aanbevelen een label toe te passen](https://docs.microsoft.com/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide)?

**Voor containers**

* Welke criteria bepalen of voor M365 groepen, teams of share point-sites toegang moet worden beperkt met behulp van gevoeligheids labels?

* Wilt u alleen inhoud in deze containers naar een andere label verplaatsen, of wilt u de bestaande bestanden [automatisch labelen](https://docs.microsoft.com/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide) in share point en OneDrive?

Bekijk deze [algemene scenario's voor gevoeligheids labels](https://docs.microsoft.com/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide) voor andere ideeën over het gebruik van gevoeligheids labels.

### <a name="sensitivity-labels-on-email-and-content"></a>Gevoeligheids labels voor e-mail en inhoud

Wanneer u een gevoeligheids label toewijst aan een document of e-mail bericht, ziet u een stempel dat wordt toegepast op inhoud die aanpasbaar, duidelijk is en permanent is. 

* **Aanpasbaar** betekent dat u labels kunt maken die geschikt zijn voor uw organisatie en bepalen wat er gebeurt wanneer ze worden toegepast.

* **Lees bare tekst** betekent dat het een deel is van de meta gegevens van het item en kan worden gelezen door toepassingen en services, zodat ze hun eigen beschermende acties kunnen Toep assen.

* **Permanent** betekent dat het label en alle bijbehorende beveiligingen roamen met de inhoud en de basis vormen voor het Toep assen en afdwingen van beleid.

 

> [!NOTE]
> Voor elk item met inhoud kan één gevoeligheids label worden toegepast.


### <a name="sensitivity-labels-on-containers"></a>Gevoeligheids labels op containers

U kunt gevoeligheids labels Toep assen op containers, zoals [Microsoft 365 groepen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-assign-sensitivity-labels), [micro soft teams](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)en [share point-sites](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide). Wanneer u dit gevoeligheids label toepast op een ondersteunde container, past het label de instellingen voor classificatie en beveiliging automatisch toe op de verbonden site of groep. Gevoeligheids labels op deze containers kunnen de volgende aspecten van containers bepalen:

* **Privacy**. U kunt kiezen wie de site kan zien: specifieke gebruikers, alle interne gebruikers of iedereen.

* **Externe gebruikers toegang**. Hiermee wordt bepaald of de groeps eigenaar gasten kan toevoegen aan de groep.

* **Toegang vanaf onbeheerde apparaten**. Hiermee wordt bepaald of en hoe onbeheerde apparaten toegang hebben tot inhoud.

 

![Een scherm afbeelding van het bewerken van gevoeligheids labels](media/secure-external-access/8-edit-label.png)

 

Wanneer u een gevoeligheids label toepast op een container, zoals een share point-site, wordt deze niet toegepast op inhoud daar: gevoeligheids labels op containers bepalen de toegang tot de inhoud in de container. 

* Als u automatisch labels wilt Toep assen op de inhoud van de container, raadpleegt u [automatisch een gevoeligheid Toep assen op inhoud](https://docs.microsoft.com/microsoft-365/compliance/apply-sensitivity-label-automatically?view=o365-worldwide).

* Als u wilt dat gebruikers hand matig labels kunnen Toep assen op deze inhoud, moet u ervoor zorgen dat u de [gevoeligheids labels voor Office-bestanden in share point en OneDrive hebt ingeschakeld](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide).

### <a name="plan-to-implement-sensitivity-labels"></a>De implementatie van gevoeligheids labels plannen

Wanneer u hebt bepaald hoe u de gevoeligheids labels wilt gebruiken en op welke inhoud en sites u deze wilt Toep assen, raadpleegt u de volgende documentatie om u te helpen uw implementatie uit te voeren.

1. [Aan de slag met gevoeligheids labels](https://docs.microsoft.com/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

2. [Een implementatie strategie maken](https://docs.microsoft.com/microsoft-365/compliance/get-started-with-sensitivity-labels?view=o365-worldwide)

3. [Gevoeligheids labels maken en publiceren](https://docs.microsoft.com/microsoft-365/compliance/create-sensitivity-labels?view=o365-worldwide)

4. [Toegang tot inhoud beperken met behulp van gevoeligheids labels om versleuteling toe te passen](https://docs.microsoft.com/microsoft-365/compliance/encryption-sensitivity-labels?view=o365-worldwide)

5. [Gevoeligheids labels gebruiken voor teams, groepen en sites](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide)

6. [Gevoeligheids labels voor Office-bestanden inschakelen in share point en OneDrive](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files?view=o365-worldwide)

### <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over het beveiligen van externe toegang tot bronnen. U wordt aangeraden de acties in de vermelde volg orde uit te voeren.

1. [Bepaal de gewenste beveiligings postuur voor externe toegang](1-secure-access-posture.md)

2. [Uw huidige status ontdekken](2-secure-access-current-state.md)

3. [Een governance-plan maken](3-secure-access-plan.md)

4. [Groepen gebruiken voor beveiliging](4-secure-access-groups.md)

5. [Overgang naar Azure AD B2B](5-secure-access-b2b.md)

6. [Veilige toegang met het rechten beheer](6-secure-access-entitlement-managment.md)

7. [Veilige toegang met beleid voor voorwaardelijke toegang](7-secure-access-conditional-access.md)

8. [Veilige toegang met gevoelige labels](8-secure-access-sensitivity-labels.md) (u bent hier.)

9. [Beveiligde toegang tot micro soft teams, OneDrive en share point](9-secure-access-teams-sharepoint.md)
