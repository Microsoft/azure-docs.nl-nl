---
title: Gegevens die zijn gesynchroniseerd met uw ITSM-product naar de LA-werk ruimte
description: Dit artikel bevat een overzicht van de gegevens die zijn gesynchroniseerd vanuit uw ITSM-product naar de LA-werk ruimte.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: fd570950190ceabac413aca2d68368e5e722a3da
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609532"
---
# <a name="data-synced-from-your-itsm-product"></a>Gegevens die zijn gesynchroniseerd met uw ITSM-product

Incidenten en wijzigings aanvragen worden gesynchroniseerd vanuit uw ITSM-hulp programma naar uw Log Analytics-werk ruimte, op basis van de configuratie van de verbinding (met behulp van het veld synchronisatie gegevens):
* [ServiceNow](./itsmc-connections-servicenow.md)
* [System Center Service Manager](./itsmc-connections-scsm.md)
* [Cherwell](./itsmc-connections-cherwell.md)
* [Provance](./itsmc-connections-provance.md)

## <a name="synced-data"></a>Gesynchroniseerde gegevens

In deze sectie vindt u enkele voor beelden van gegevens die zijn verzameld door ITSMC.

De velden in **ServiceDesk_CL** variëren, afhankelijk van het type werk item dat u in log Analytics importeert. Hier volgt een lijst met velden voor twee typen werk items:

**Werk item:** **incidenten**  
ServiceDeskWorkItemType_s = "incident"

**Fields**

- ServiceDeskConnectionName
- Service Desk-ID
- Staat
- Urgentie
- Impact
- Prioriteit
- Escalatie
- Created By
- Opgelost door
- Gesloten door
- Bron
- Toegewezen aan
- Categorie
- Titel
- Beschrijving
- Gemaakt op
- Datum gesloten
- Datum opgelost
- Datum laatst gewijzigd
- Computer

**Werk item:** **wijzigings aanvragen**

ServiceDeskWorkItemType_s = "ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- Service Desk-ID
- Created By
- Gesloten door
- Bron
- Toegewezen aan
- Titel
- Type
- Categorie
- Staat
- Escalatie
- Conflict status
- Urgentie
- Prioriteit
- Risico
- Impact
- Toegewezen aan
- Gemaakt op
- Datum gesloten
- Datum laatst gewijzigd
- Aangevraagde datum
- Geplande begin datum
- Geplande eind datum
- Begin datum van werk
- Eind datum van werk
- Description
- Computer

## <a name="servicenow-example"></a>ServiceNow-voor beeld 
### <a name="output-data-for-a-servicenow-incident"></a>Uitvoer gegevens voor een ServiceNow-incident

| Log Analytics veld | Het veld ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Aantal |
| IncidentState_s | Staat |
| Urgency_s |Urgentie |
| Impact_s |Impact|
| Priority_s | Prioriteit |
| CreatedBy_s | Geopend door |
| ResolvedBy_s | Opgelost door|
| ClosedBy_s  | Gesloten door |
| Source_s| Type contact |
| AssignedTo_s | Toegewezen aan  |
| Category_s | Categorie |
| Title_s|  Korte beschrijving |
| Description_s|  Notities |
| CreatedDate_t|  Had |
| ClosedDate_t| gesloten|
| ResolvedDate_t|Opgelost|
| Computer  | Configuratie-item |

### <a name="output-data-for-a-servicenow-change-request"></a>Uitvoer gegevens voor een wijzigings aanvraag voor een ServiceNow

| Log Analytics | Het veld ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Aantal |
| CreatedBy_s | Aangevraagd door |
| ClosedBy_s | Gesloten door |
| AssignedTo_s | Toegewezen aan  |
| Title_s|  Korte beschrijving |
| Type_s|  Type |
| Category_s|  Categorie |
| CRState_s|  Staat|
| Urgency_s|  Urgentie |
| Priority_s| Prioriteit|
| Risk_s| Risico|
| Impact_s| Impact|
| RequestedDate_t  | Aangevraagd door datum |
| ClosedDate_t | Gesloten datum |
| PlannedStartDate_t  | Geplande begin datum |
| PlannedEndDate_t  | Geplande eind datum |
| WorkStartDate_t  | Werkelijke begin datum |
| WorkEndDate_t | Werkelijke eind datum|
| Description_s | Description |
| Computer  | Configuratie-item |

## <a name="next-steps"></a>Volgende stappen

* [Problemen oplossen in ITSM-connector](./itsmc-resync-servicenow.md)
