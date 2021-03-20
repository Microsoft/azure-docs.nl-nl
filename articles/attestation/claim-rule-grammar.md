---
title: Grammatica voor Azure Attestation-claimregel
description: Details over Azure Attestation-beleidsclaims en -beleidsregels.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ed5c3f8232047787c6f05628f1eef35a7533999
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91285415"
---
# <a name="claim-and-claim-rules"></a>Claim en claimregels

Voor een goed begrip van de grammatica van claimregels moet u eerst Attestation-beleidsclaims begrijpen.

## <a name="claim"></a>Claim

Een claim is een set eigenschappen die samen worden gegroepeerd om relevante informatie te bieden. Voor Azure Attestation bevat een claim de volgende eigenschappen:

- **type**: Een tekenreekswaarde die het type van de claim vertegenwoordigt.
- **value**: Een Booleaanse waarde, geheel getal of tekenreeks die de waarde van de claim vertegenwoordigt.
- **valueType**: Het gegevenstype van de gegevens die zijn opgeslagen in de eigenschap value. Ondersteunde typen zijn String (tekenreeks), Integer (geheel getal) en Boolean (Booleaanse waarde). Indien niet gedefinieerd, is de standaardwaarde 'String' (tekenreeks).
- **issuer**: Informatie over de uitgever van de claim. De uitgever is een van de volgende typen:
  - **AttestationService**: Azure Attestation stelt de beleidsauteur bepaalde claims ter beschikking die door de auteur van het Attestation-beleid kunnen worden gebruikt om het juiste beleid te maken.
  - **AttestationPolicy**: Het beleid (zoals gedefinieerd door de beheerder) zelf kan claims aan de inkomende bewijzen toevoegen tijdens de verwerking. De issuer (uitgever) wordt in dit geval ingesteld op 'AttestationPolicy'.
  - **CustomClaim**: De attestor (client) kan ook extra claims aan de Attestation-bewijzen toevoegen. De issuer (uitgever) wordt in dit geval ingesteld op 'CustomClaim'.

Indien niet gedefinieerd. is de standaardwaarde 'CustomClaim'.

## <a name="claim-rule"></a>Claimregel

De binnenkomende claimset wordt door de beleidsengine gebruikt om het Attestation-resultaat te berekenen. Een claimregel is een set voorwaarden die wordt gebruikt om de binnenkomende claims te valideren en de gedefinieerde actie uit te voeren.

```
Conditions list => Action (Claim)
```

Azure Attestation evalueert een claimregel met de volgende stappen:

- Als er geen voorwaardenlijst aanwezig is, voer de actie uit met de opgegeven claim 
- Evalueer anders de voorwaarden van de voorwaardenlijst.
- Stop als de voorwaardenlijst wordt geëvalueerd als onwaar. Ga anders verder.

De voorwaarden in een claimregel worden gebruikt om te bepalen of de actie moet worden uitgevoerd. De voorwaardenlijst bestaat uit een reeks voorwaarden die worden gescheiden door de operator '&&'.

De voorwaardenlijst is gestructureerd als:

```
Condition && Condition && ...
```

De voorwaarde is gestructureerd als:

```
Identifier:[ClaimPropertyCondition, ClaimPropertyCondition,…]
```

De lijst met voorwaarden bestaat uit afzonderlijke voorwaarden op verschillende eigenschappen van een claim. Een voorwaarde kan een optionele id hebben, die kan worden gebruikt om de claims te verwijzen die voldoen aan de voorwaarde. Deze verwijzing kan worden gebruikt in de andere voorwaarden of de actie van dezelfde regel.

Bijvoorbeeld

```
F1:[type=="OSName" , issuer=="CustomClaim"] && 
[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issueproperty(type="report_validity_in_minutes", value=1440);

F1:[type=="OSName" , issuer=="CustomClaim"] && 
C2:[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issue(claim = C2);
```

De volgende operatoren kunnen worden gebruikt om voorwaarden te controleren:

| ValueType | Ondersteunde bewerkingen |
|--|--|
| Geheel getal | == (gelijk aan), \!= (niet gelijk aan), <= (kleiner dan of gelijk aan), < (kleiner dan), >= (groter dan of gelijk aan), > (groter dan) |
| Tekenreeks | == (gelijk aan), \!= (niet gelijk aan) |
| Booleaans | == (gelijk aan), \!= (niet gelijk aan) |

Evaluatie van de voorwaardenlijst:

- De aanwezigheid van de operator '&&' impliceert dat een voorwaardenlijst alleen als waar wordt geëvalueerd als alle voorwaarden in de lijst waar zijn.
- Een voorwaarde vertegenwoordigt filtercriteria voor de set met claims. De voorwaarde zelf wordt geëvalueerd als waar als er ten minste één claim wordt gevonden die aan de voorwaarde voldoet.
- Een claim voldoet aan het filtercriterium van de voorwaarde als elk van de eigenschappen ervan voldoet aan de overeenkomstige claimvoorwaarden in de voorwaarde.  

Hieronder wordt de set acties beschreven die in een beleid zijn toegestaan.

| Actiewerkwoord | Beschrijving | Beleidssecties waarop deze van toepassing zijn |
|--|--|--|
| permit() | De binnenkomende claimset kan worden gebruikt om **issuancerules** te berekenen. Gebruikt geen claim als parameter | **authorizationrules** |
| deny() | De binnenkomende claimset mag niet worden gebruikt voor het berekenen van **issuancerules**. Gebruikt geen claim als parameter | **authorizationrules** |
| add(claim) | Voegt de claim toe aan de binnenkomende claimset. Elke claim die wordt toegevoegd aan de binnenkomende claimset is beschikbaar voor de volgende claimregels. |**authorizationrules**, **issuancerules** |
| issue(claim) | Voegt de claim toe aan de inkomende en uitgaande claimset | **issuancerules** |
| issueproperty(claim) | Voegt de claim toe aan de inkomende en uitgaande eigenschapsclaimset | **issuancerules**

## <a name="next-steps"></a>Volgende stappen

- [Een Attestation-beleid ontwerpen en ondertekenen](author-sign-policy.md)
- [Azure Attestation instellen met behulp van PowerShell](quickstart-powershell.md)

