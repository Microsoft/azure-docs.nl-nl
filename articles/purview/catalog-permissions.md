---
title: Catalogus machtigingen (preview-versie)
description: Dit artikel bevat een overzicht van het configureren van Role-Based Access Control (RBAC) in azure controle sfeer liggen
author: yarong
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: b351be1e7212dc9923f701599dd951a73254afe0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98610366"
---
# <a name="role-based-access-control-in-azure-purviews-data-plane"></a>Op rollen gebaseerd toegangs beheer in het gegevens vlak van Azure controle sfeer liggen

In dit artikel wordt beschreven hoe Role-Based Access Control (RBAC) wordt geïmplementeerd in het [gegevens vlak](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane)van Azure controle sfeer liggen.

> [!IMPORTANT]
> De principal die een controle sfeer liggen-account heeft gemaakt, krijgt automatisch alle machtigingen voor het gegevens vlak, ongeacht welke gegevenslaag rollen ze al dan niet kunnen hebben. Voor elke andere gebruiker om iets te doen in azure controle sfeer liggen moeten ze zich in ten minste één van de vooraf gedefinieerde gegevenslaag rollen bevinden.

## <a name="azure-purviews-pre-defined-data-plane-roles"></a>Vooraf gedefinieerde gegevenslaag rollen van Azure controle sfeer liggen

Azure controle sfeer liggen definieert een reeks vooraf gedefinieerde gegevenslaag rollen die kunnen worden gebruikt om te bepalen wie toegang heeft tot wat, in azure controle sfeer liggen. Deze rollen zijn:

* **Controle sfeer liggen-functie voor gegevens lezer** : heeft toegang tot de controle sfeer liggen-Portal en kan alle inhoud in azure controle sfeer liggen lezen, met uitzonde ring van scan bindingen
* **Controle sfeer liggen data curator-rol** : heeft toegang tot de controle sfeer liggen-Portal en kan alle inhoud in azure controle sfeer liggen lezen, met uitzonde ring van scan bindingen, kan informatie over assets bewerken, classificatie definities en woordenlijst termen bewerken en kan classificaties en termen voor waarden Toep assen op activa.
* **Controle sfeer liggen-functie voor gegevens bron beheer** : heeft geen toegang tot de controle sfeer liggen-Portal (de gebruiker moet ook zijn opgenomen in de gegevens lezer of gegevens curator rollen) en kan alle aspecten van het scannen van gegevens in azure controle sfeer liggen beheren, maar heeft geen lees-of schrijf toegang tot inhoud in azure controle sfeer liggen dan die met betrekking tot het scannen.

## <a name="understanding-how-to-use-azure-purviews-data-plane-roles"></a>Meer informatie over het gebruik van de gegevenslaag rollen van Azure controle sfeer liggen

Wanneer een Azure controle sfeer liggen-account wordt gemaakt, wordt de Maker behandeld alsof ze zich in de controle sfeer liggen data curator-en controle sfeer liggen-gegevens bron beheerder rollen bevinden. Maar in het rollenarchief zijn deze rollen niet toegewezen aan de maker van het account. Azure Purview erkent dat de principal de maker van het account is, en breidt deze mogelijkheden uit naar deze principals op basis van hun identiteit.

Alle andere gebruikers kunnen het Azure Purview-account alleen gebruiken als minstens één van deze rollen aan hen is toegewezen. Dit betekent dat wanneer een Azure controle sfeer liggen-account wordt gemaakt, niemand, maar de maker toegang heeft tot het account of de Api's kan gebruiken tot ze in een of meer van de eerder gedefinieerde rollen zijn geplaatst.

Houd er rekening mee dat de rol controle sfeer liggen-gegevens bron beheerder twee ondersteunde scenario's heeft. Het eerste scenario is voor gebruikers die al controle sfeer liggen data readers of controle sfeer liggen data Curators zijn die ook scans moeten kunnen maken. Deze gebruikers moeten zich in twee rollen bevinden, ten minste één van de gegevens lezer van controle sfeer liggen of controle sfeer liggen data curator, maar ook worden opgenomen in de beheerdersrol controle sfeer liggen-gegevens bron.

Het andere scenario voor controle sfeer liggen gegevens bron beheer is voor programmatische processen, zoals service-principals, die scans moeten kunnen instellen en controleren, maar geen toegang mogen hebben tot een van de gegevens van de catalogus.

Dit scenario kan worden geïmplementeerd door de Service-Principal in de controle sfeer liggen-functie voor gegevens bron beheer in te stellen zonder dat ze in een van de andere twee rollen worden geplaatst. De principal heeft geen toegang tot de controle sfeer liggen-Portal, maar is o.k. omdat het een programmatische principal is en alleen communiceert via Api's.

## <a name="putting-users-into-roles"></a>Gebruikers in rollen plaatsen

De eerste volg orde van het bedrijf na het maken van een Azure controle sfeer liggen-account is dus het toewijzen van personen aan deze rollen.

De roltoewijzing wordt beheerd via [de RBAC van Azure](../role-based-access-control/overview.md).

Slechts twee ingebouwde rollen voor besturings elementen in azure kunnen gebruikers rollen toewijzen, die eigen aren zijn of beheerders van gebruikers toegang. Om mensen te voorzien van deze rollen voor Azure controle sfeer liggen moet er dus iemand worden gevonden die een eigenaar of beheerder van de gebruiker is of een oneself wordt.

### <a name="an-example-of-assigning-someone-to-a-role"></a>Een voor beeld van het toewijzen van iemand aan een rol

1. Ga naar https://portal.azure.com en navigeer naar uw Azure controle sfeer liggen-account
1. Klik aan de linkerkant op ' toegangs beheer (IAM) '
1. Volg vervolgens de algemene instructies die [hier](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group) worden gegeven

## <a name="role-definitions-and-actions"></a>Roldefinities en acties

Een rol wordt gedefinieerd als een verzameling acties. Zie [hier](../role-based-access-control/role-definitions.md) voor meer informatie over hoe rollen worden gedefinieerd. En Zie [hier](../role-based-access-control/built-in-roles.md) voor de roldefinities voor rollen van Azure controle sfeer liggen.

## <a name="getting-added-to-a-data-plane-role-in-an-azure-purview-account"></a>Aan een gegevenslaag rol in een Azure controle sfeer liggen-account toevoegen

Als u toegang wilt krijgen tot een Azure controle sfeer liggen-account zodat u de Studio kunt gebruiken of de Api's aanroept die u moet toevoegen aan een Azure controle sfeer liggen data vlak-rol. De enige personen die dit kunnen doen zijn degenen die eigen aren zijn of beheerders van gebruikers toegang hebben in het Azure controle sfeer liggen-account. Voor de meeste gebruikers is de volgende stap het vinden van een lokale beheerder die u kan helpen bij het vinden van de juiste personen die u toegang kunnen geven.

Voor gebruikers die toegang hebben tot de [Azure Portal](https://portal.azure.com) van hun bedrijf, kunnen ze het specifieke account van Azure controle sfeer liggen opzoeken dat ze willen toevoegen, klikt u op het tabblad toegangs beheer (IAM) en ziet u wie de eigen aars of gebruikers toegang (UAAs) zijn. Maar houd er rekening mee dat in sommige gevallen Azure Active Directory groepen of service-principals als eigen aren of UAAs kunnen worden gebruikt. in dat geval is het mogelijk dat het niet mogelijk is om rechtstreeks contact op te nemen met hen. In plaats daarvan moet een beheerder zoeken om te helpen.

## <a name="who-should-be-assigned-to-what-role"></a>Wie moet worden toegewezen aan welke rol?

|Gebruikers scenario|Juiste rol (len)|
|-------------|-----------------|
|Ik hoef alleen assets te zoeken, ik wil niets bewerken|Rol van controle sfeer liggen-gegevens lezer|
|Ik moet informatie over assets bewerken, classificaties op deze activa plaatsen, deze koppelen aan de vermeldingen in de woorden lijst, enzovoort.|Controle sfeer liggen-gegevens curator-rol|
|Ik wil de woorden lijst bewerken of nieuwe classificatie definities instellen|Controle sfeer liggen-gegevens curator-rol|
|De service-principal van mijn toepassing moet gegevens pushen naar Azure controle sfeer liggen|Controle sfeer liggen-gegevens curator-rol|
|Ik wil scans instellen via de controle sfeer liggen Studio|Controle sfeer liggen-functie voor gegevens bron beheer plus ten minste één van de controle sfeer liggen-functie gegevens lezer of controle sfeer liggen data curator|
|Ik moet een service-principal of andere programma-id inschakelen voor het instellen en controleren van scans in azure controle sfeer liggen zonder dat de programmatische identiteit toegang heeft tot de gegevens van de catalogus |Beheerdersrol van controle sfeer liggen-gegevens bron|
|Ik wil gebruikers in azure controle sfeer liggen in hun rol zetten | Beheerder van eigenaar of gebruiker toegang |

Raadpleeg [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md) voor meer informatie over het toevoegen van een beveiligingsprincipal aan een rol.

## <a name="next-steps"></a>Volgende stappen

* [Gegevensinzichten](concept-insights.md)
