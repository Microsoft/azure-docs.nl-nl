---
title: Veelvoorkomende fouten en bekende problemen bij de migratie naar Azure Cloud Services (uitgebreide ondersteuning)
description: Overzicht van veelvoorkomende fouten bij het migreren van Cloud Services (klassiek) naar de Cloud service (uitgebreide ondersteuning)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 58203730793202649c401d96182469fa1eac6ef1
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286941"
---
# <a name="common-errors-and-known-issues-when-migration-to-azure-cloud-services-extended-support"></a>Veelvoorkomende fouten en bekende problemen bij de migratie naar Azure Cloud Services (uitgebreide ondersteuning)

In dit artikel worden bekende problemen beschreven en veelvoorkomende fouten die kunnen optreden wanneer de migratie van Cloud Services (klassiek) naar Cloud Services (uitgebreide ondersteuning). 

## <a name="known-issues"></a>Bekende problemen
De volgende problemen zijn bekend en worden opgelost.

| Bekende problemen | Oplossing | 
|---|---|
| UD worden door UD opnieuw gestart nadat het door voeren is voltooid. | Bij het opnieuw starten van de bewerking wordt dezelfde methode gebruikt als voor de maandelijkse implementaties van gast besturingssystemen. De migratie van Cloud Services met een exemplaar van één rol niet door voeren of wordt beïnvloed door opnieuw op te starten.| 
| Azure Portal kan de migratie status niet lezen na het vernieuwen van de browser. | Voer het valideren en voorbereiden van de bewerking opnieuw uit om terug te gaan naar de oorspronkelijke migratie status. | 
| Certificaat wordt weer gegeven als geheime resource in sleutel kluis. | Nadat de migratie is uitgevoerd, moet u het certificaat opnieuw uploaden als een certificaat bron om de update bewerking op Cloud Services te vereenvoudigen (uitgebreide ondersteuning). | 
| Implementatie-labels worden niet als tags opgeslagen als onderdeel van de migratie. | Maak de labels hand matig na de migratie om deze informatie te onderhouden.
| De naam van de resource groep is in alle hoofd letters. | Geen invloed. De oplossing is nog niet beschikbaar. |
| De naam van de vergren deling op Cloud Services (uitgebreide ondersteuning) is onjuist. | Geen invloed. De oplossing is nog niet beschikbaar. | 
| De naam van het IP-adres is onjuist op de Blade van de Cloud Services-portal (uitgebreide ondersteuning). | Geen invloed. De oplossing is nog niet beschikbaar. | 
| Er is een ongeldige DNS-naam weer gegeven voor het virtuele IP-adres na een update bewerking voor een gemigreerde Cloud service. | Geen invloed. De oplossing is nog niet beschikbaar. | 
| Nadat de voor bereiding is voltooid, is het koppelen van een nieuwe Cloud Services-implementatie (uitgebreide ondersteuning) niet toegestaan. | Koppel een nieuwe Cloud service niet als een verwisselbaar naar een voor bereide Cloud service. | 
| Fout berichten moeten worden bijgewerkt. | Geen invloed. | 

## <a name="common-migration-errors"></a>Algemene migratiefouten
Veelvoorkomende migratie fouten en stappen voor het oplossen van problemen. 

| Foutbericht | Details | 
|---|---|
| Kan het resource type niet vinden in de naam ruimte `Microsoft.Compute` voor de API-versie 2020-10-01-preview. | [Registreer het abonnement voor de](in-place-migration-overview.md#setup-access-for-migration) functie vlag CloudServices om toegang te krijgen tot de open bare preview. | 
| Er is een interne fout opgetreden op de server. Voer de aanvraag opnieuw uit. | Voer de bewerking opnieuw uit, gebruik [micro soft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) of neem contact op met de ondersteuning. | 
| Er is een onverwachte fout opgetreden op de server tijdens het toewijzen van netwerk bronnen voor de Cloud service. Voer de aanvraag opnieuw uit. | Voer de bewerking opnieuw uit, gebruik [micro soft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) of neem contact op met de ondersteuning. | 
| Implementatie-implementatie-naam in Cloud Service Cloud-service-naam moet binnen een virtueel netwerk zijn dat moet worden gemigreerd. | De implementatie bevindt zich niet in een virtueel netwerk. Raadpleeg [Dit](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) document voor meer informatie. | 
| Migratie van implementatie implementatie-naam in Cloud Service Cloud-service naam wordt niet ondersteund omdat deze zich in regio regio-naam bevindt. Toegestane regio's: [lijst met beschik bare regio's]. | De regio wordt nog niet ondersteund voor migratie. | 
| De implementatie implementatie naam in Cloud Service Cloud-service-name kan niet worden gemigreerd omdat er geen subnetten zijn gekoppeld aan de rol-name van de rol (len). Koppel alle rollen aan een subnet en voer de migratie van de Cloud service opnieuw uit. | Werk de implementatie van de Cloud service (klassiek) bij door deze in een subnet te plaatsen vóór de migratie. |  
| De implementatie implementatie naam in Cloud Service Cloud-service-name kan niet worden gemigreerd omdat de implementatie ten minste één functie vereist die niet is geregistreerd voor het abonnement in Azure Resource Manager. Registreer alle vereiste functies om deze implementatie te migreren. Ontbrekende functie (s): [lijst met ontbrekende onderdelen]. | Neem contact op met ondersteuning om de functie vlaggen te verkrijgen die zijn geregistreerd. | 
| De implementatie kan niet worden gemigreerd omdat de Cloud service van de implementatie twee bezette sleuven heeft. Migratie van Cloud Services wordt alleen ondersteund voor implementaties die de enige implementatie in hun Cloud service zijn. Verwijder de andere implementatie in de Cloud service om door te gaan met de migratie van deze implementatie. | Raadpleeg de lijst met [niet-ondersteunde scenario's](in-place-migration-overview.md#unsupported-configurations--migration-scenarios) voor meer informatie. | 
| Implementatie-implementatie-naam in HostedService Cloud-service-naam bevindt zich in de bestaans status: status. Migratie niet toegestaan. | De implementatie wordt gemaakt, verwijderd of bijgewerkt. Wacht tot de bewerking is voltooid en probeer het opnieuw. | 
| De implementatie-implementatie naam in de gehoste service cloud-service-name heeft gereserveerde IP (s), maar geen gereserveerde IP-naam. U kunt dit probleem oplossen door de gereserveerde IP-naam bij te werken of contact op te nemen met de Microsoft Azure Service Desk. | Cloud service-implementatie bijwerken. | 
| De implementatie-implementatie naam in de gehoste service cloud-service-name heeft gereserveerde IP (s) gereserveerde IP-naam, maar geen eind punt op het gereserveerde IP-adres. U kunt dit probleem oplossen door ten minste één eind punt toe te voegen aan het gereserveerde IP-adres. | Voeg een eind punt toe aan het gereserveerde IP-adres. | 
| De migratie van {0} de implementatie in HostedService {1} wordt doorgevoerd en kan niet worden gewijzigd totdat het proces is voltooid.  | Wacht of probeer het opnieuw. | 
| De migratie van {0} de implementatie in HostedService {1} wordt afgebroken en kan niet worden gewijzigd totdat het proces is voltooid. | Wacht of probeer het opnieuw. |
| Een of meer virtuele machines in {0} de implementatie in HostedService {1} gaan over een update-bewerking. De migratie kan pas worden gemigreerd als de vorige bewerking is voltooid. Probeer het later opnieuw. | Wacht totdat de bewerking is voltooid. | 
| Migratie wordt niet ondersteund voor implementatie {0} in HostedService {1} omdat deze gebruikmaakt van de volgende functies die nog niet worden ondersteund voor migratie: niet-vnet-implementatie.| De implementatie bevindt zich niet in een virtueel netwerk. Raadpleeg [Dit](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) document voor meer informatie. | 
| De naam van het virtuele netwerk mag niet null of leeg zijn. | Geef de naam van het virtuele netwerk op in het hoofd gedeelte van de REST-aanvraag | 
| De naam van het subnet mag niet null of leeg zijn. | Geef de naam van het subnet op in het hoofd gedeelte van de REST-aanvraag. | 
| DestinationVirtualNetwork moet worden ingesteld op een van de volgende waarden: standaard, nieuw of bestaand. | Geef de eigenschap DestinationVirtualNetwork op in de hoofd tekst van de REST-aanvraag. | 
| De standaard optie voor het VNet-doel is niet geïmplementeerd. | De waarde ' default ' wordt niet ondersteund voor de eigenschap DestinationVirtualNetwork in de tekst van de REST-aanvraag. | 
| De implementatie {0} kan niet worden gemigreerd omdat de CSPKG niet beschikbaar is. | Voer een upgrade uit voor de implementatie en probeer het opnieuw. | 
| Het subnet met ID ' {0} ' bevindt zich op een andere locatie dan de implementatie ' {1} ' in de gehoste service ' {2} '. De locatie van het subnet is ' {3} ' en de locatie voor de gehoste service is {4} .  Geef een subnet op op dezelfde locatie als de implementatie. | Werk de Cloud service bij zodat de subnet-en Cloud service zich op dezelfde locatie bevinden vóór de migratie. | 
| De migratie van {0} de implementatie in HostedService {1} wordt afgebroken en kan niet worden gewijzigd totdat het proces is voltooid. | Wacht totdat het afbreken is voltooid of probeer het opnieuw. Gebruik [micro soft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) of neem anders contact op met de ondersteuning. | 
| De implementatie {0} in HostedService {1} is niet voor bereid voor migratie. | Voer voor bereiding uit voor de Cloud service voordat u de doorvoer bewerking uitvoert. | 
| UnknownExceptionInEndExecute: contract. assert is mislukt: rgName is null of leeg: er is een uitzonde ring ontvangen in EndExecute die geen RdfeException is. |   Gebruik [micro soft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) of neem contact op met de ondersteuning. | 
| UnknownExceptionInEndExecute: een taak is geannuleerd: uitzonde ring ontvangen in EndExecute die geen RdfeException is. | Gebruik [micro soft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) of neem contact op met de ondersteuning. | 
| XrpVirtualNetworkMigrationError: migratie fout van het virtuele netwerk. | Gebruik [micro soft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) of neem contact op met de ondersteuning. | 
| De implementatie {0} in HostedService {1}  behoort tot Virtual Network {2} . Migreer Virtual Network {2} om deze HostedService te migreren {1} . | Raadpleeg [Virtual Network-migratie](in-place-migration-technical-details.md#virtual-network-migration). | 
| Het huidige quotum voor de resource naam in Azure Resource Manager is onvoldoende om de migratie te volt ooien. Huidige quota is {0} , extra nodig is {1} . Een ondersteunings aanvraag indienen om het quotum te verhogen en de migratie opnieuw uit te voeren zodra het quotum is gegenereerd.    | Volg de juiste kanalen om een quotum toename aan te vragen: <br>[Quotum verhoging voor netwerk bronnen](../azure-portal/supportability/networking-quota-requests.md) <br>[Toename van quotum voor reken resources](../azure-portal/supportability/per-vm-quota-requests.md) | 

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over de vereisten van de migratie raadpleegt [u technische details over het migreren naar Azure Cloud Services (uitgebreide ondersteuning)](in-place-migration-technical-details.md)
