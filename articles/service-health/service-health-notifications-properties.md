---
title: Wat zijn status meldingen van Azure service?
description: Met service status meldingen kunt u service status berichten weer geven die zijn gepubliceerd door Microsoft Azure.
ms.topic: conceptual
ms.date: 4/12/2018
ms.openlocfilehash: 4f2b4a0779d775ed5be5bfa28831cccc68c33caf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86529009"
---
# <a name="use-the-azure-portal-to-view-service-health-notifications"></a>De Azure Portal gebruiken om service status meldingen weer te geven

Service status meldingen worden gepubliceerd door Azure en bevatten informatie over de resources in uw abonnement. Deze meldingen zijn een subklasse van activiteiten logboek gebeurtenissen en kunnen ook worden gevonden in het activiteiten logboek. Service status meldingen kunnen informatie of actief zijn, afhankelijk van de klasse.

Er zijn verschillende klassen van service status meldingen:  

- **Actie vereist:** Mogelijk ziet u dat er een ongebruikelijk probleem is opgetreden in uw account en hoe u dit kunt verhelpen. Azure stuurt u een melding, met een gedetailleerde beschrijving van de acties die u moet ondernemen of hoe u contact opneemt met Azure engineering of ondersteuning.  
- **Incident:** Een gebeurtenis die van invloed is op de service, is momenteel van invloed op een of meer resources in uw abonnement.  
- **Onderhoud:** Een geplande onderhouds activiteit die van invloed kan zijn op een of meer van de resources in uw abonnement.  
- **Informatie:** Mogelijke optimalisaties die u kunnen helpen bij het verbeteren van het gebruik van resources. 
- **Beveiliging:** Urgente beveiligings informatie met betrekking tot uw oplossingen die worden uitgevoerd op Azure.

Elke service status melding bevat details over het bereik en de invloed op uw resources. Details zijn onder andere:

Naam van eigenschap | Beschrijving
-------- | -----------
detailhandelkanalen | Een van de volgende waarden: **beheerder** of **bewerking**.
correlationId | Meestal een GUID in de teken reeks indeling. Gebeurtenissen die tot dezelfde actie behoren, delen meestal dezelfde correlatie.
eventDataId | De unieke id van een gebeurtenis.
eventName | De titel van een gebeurtenis.
niveau | Het niveau van een gebeurtenis
resourceProviderName | De naam van de resource provider voor de betrokken resource.
resourceType| Het type resource van de betrokken resource.
subStatus | Doorgaans de HTTP-status code van de bijbehorende REST-aanroep, maar kan ook andere teken reeksen bevatten die een substatus beschrijven. Bijvoorbeeld: OK (HTTP-status code: 200), gemaakt (HTTP-status code: 201), goedgekeurd (HTTP-status code: 202), geen inhoud (HTTP-status code: 204), ongeldige aanvraag (HTTP-status code: 400), niet gevonden (HTTP-status code: 404), conflict (HTTP-status code: 409), interne server fout (HTTP-status code: 500), service niet beschikbaar (HTTP-status code: 503) en time-out voor gateway
eventTimestamp | Tijds tempel wanneer de gebeurtenis is gegenereerd door de Azure-service voor het verwerken van de aanvraag die overeenkomt met de gebeurtenis.
submissionTimestamp | Tijds tempel wanneer de gebeurtenis beschikbaar werd voor het uitvoeren van query's.
subscriptionId | Het Azure-abonnement waarin deze gebeurtenis is vastgelegd.
status | De teken reeks die de status van de bewerking beschrijft. Enkele veelvoorkomende waarden zijn: **gestart**, **in uitvoering**, **geslaagd**, **mislukt**, **actief** en **opgelost**.
operationName | De naam van de bewerking.
category | Deze eigenschap is altijd **ServiceHealth**.
resourceId | De resource-ID van de betrokken resource.
Eigenschappen. title | De gelokaliseerde titel voor deze communicatie. Engels is de standaard instelling.
Eigenschappen. communicatie | De gelokaliseerde Details van de communicatie met HTML-opmaak. Engels is de standaard instelling.
Eigenschappen. incidentType | Een van de volgende waarden: **handeling vereist**, **informatief**, **incident**, **onderhoud** of **beveiliging**.
Eigenschappen. trackingId | Het incident waaraan deze gebeurtenis is gekoppeld. Gebruik deze om de gebeurtenissen met betrekking tot een incident te correleren.
Eigenschappen. impactedServices | Een JSON-blob met escape-teken waarmee de services en regio's worden beschreven die worden beïnvloed door het incident. De eigenschap bevat een lijst met Services, elk met een **ServiceName** en een lijst met beïnvloede regio's, die elk een **regio** naam hebben.
Eigenschappen. defaultLanguageTitle | De communicatie in het Engels.
Eigenschappen. defaultLanguageContent | De communicatie in het Engels als HTML-opmaak of tekst zonder opmaak.
Eigenschappen. fase | De mogelijke waarden voor **incidenten** en de **beveiliging** zijn **actief,** **opgelost** of **RCA**. Voor **handeling vereist** of **informatieve** is de enige waarde **actief.** Voor **onderhoud** zijn dit: **actief**, **gepland**, **InProgress**, **geannuleerd**, **opnieuw gepland**, **opgelost** of **voltooid**.
Eigenschappen. communicationId | De communicatie waaraan deze gebeurtenis is gekoppeld.

### <a name="details-on-service-health-level-information"></a>Details van informatie over het service status niveau

**Actie vereist** (eigenschappen. incidentType = = handeling vereist)
- Informatief: beheerders actie is vereist om te voor komen dat de bestaande services worden beïnvloed.
    
**Onderhoud** (Properties. incidentType = = Maintenance)
- Waarschuwing: nood onderhoud
- Informatie-standaard gepland onderhoud

**Informatie** (Properties. incidentType = = Information)
- Informatief: de beheerder kan worden verplicht om te voor komen dat de bestaande services worden beïnvloed.

**Beveiliging** (eigenschappen. incidentType = = beveiliging)
- Waarschuwing: beveiligings advies die van invloed is op bestaande services en waarvoor beheerders actie kan worden uitgevoerd.
- Informatief-beveiligings advies die van invloed is op bestaande services.

**Service problemen** (eigenschappen. incidentType = = incident)
- Fout: algemene problemen bij het openen van meerdere services in meerdere regio's zijn van invloed op een grote set klanten.
- Waarschuwing: problemen bij het openen van specifieke services en/of specifieke regio's zijn van invloed op een subset van klanten.
- Informatie: problemen die invloed hebben op beheer bewerkingen en/of latentie, geen invloed op de beschik baarheid van de service.
