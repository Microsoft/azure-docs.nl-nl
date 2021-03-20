---
title: bestand opnemen
description: bestand opnemen
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "79538829"
---
### <a name="the-data-model-of-the-schema"></a>Het gegevens model van het schema

|Veld|Beschrijving|
|----|----|
|**AlertName**|Weergave naam van de waarschuwing|
|**AlertType**|unieke meldings-id|
|**ConfidenceLevel**|Beschrijving Het betrouwbaarheids niveau van deze waarschuwing (hoog/laag)|
|**ConfidenceScore**|Beschrijving Indicator voor numerieke betrouw baarheid van de beveiligings waarschuwing|
|**Beschrijving**|Beschrijvende tekst voor de waarschuwing|
|**DisplayName**|De weergave naam van de waarschuwing|
|**Tijd**|De eind tijd van het effect van de waarschuwing (het tijdstip van de laatste gebeurtenis die aan de waarschuwing is bijgedragen)|
|**Entiteiten**|Een lijst met entiteiten die aan de waarschuwing zijn gerelateerd. Deze lijst kan een combi natie van entiteiten van diverse typen bevatten|
|**ExtendedLinks**|Beschrijving Een Bag voor alle koppelingen met betrekking tot de waarschuwing. Deze Bag kan een combi natie van koppelingen voor diverse typen bevatten|
|**ExtendedProperties**|Een verzameling extra velden die relevant zijn voor de waarschuwing|
|**IsIncident**|Hiermee wordt bepaald of de waarschuwing een incident of een regel matige waarschuwing is. Een incident is een beveiligings waarschuwing waarmee meerdere waarschuwingen worden geaggregeerd in één beveiligings incident|
|**ProcessingEndTime**|De UTC-tijds tempel waarin de waarschuwing is gemaakt|
|**ProductComponentName**|Beschrijving De naam van een onderdeel in het product dat de waarschuwing heeft gegenereerd.|
|**ProductName**|constant (' Azure Security Center ')|
|**ProviderName**|vrij|
|**RemediationSteps**|Hand matige actie-items die moeten worden uitgevoerd om de beveiligings bedreiging te herstellen|
|**ResourceId**|Volledige id van de betrokken resource|
|**Ernst**|De ernst van de waarschuwing (hoog/gemiddeld/laag/informatief)|
|**SourceComputerId**|een unieke GUID voor de betrokken server (als de waarschuwing wordt gegenereerd op de server)|
|**SourceSystem**|vrij|
|**StartTime**|De begin tijd van de waarschuwing (het tijdstip van de eerste gebeurtenis die bijdragen aan de waarschuwing)|
|**SystemAlertId**|Unieke id van dit beveiligings waarschuwings exemplaar|
|**Tenant-ID**|de id van de bovenliggende Azure Active Directory-Tenant van het abonnement waaronder de gescande bron zich bevindt|
|**TimeGenerated**|UTC-tijds tempel waarop de beoordeling plaatsvond (Security Center de scan tijd) (gelijk aan DiscoveredTimeUTC)|
|**Type**|constante (' SecurityAlert ')|
|**Leveranciers naam**|De naam van de leverancier die de waarschuwing heeft gegeven (bijvoorbeeld ' micro soft ')|
|**VendorOriginalId**|vrij|
|**WorkspaceResourceGroup**|Als de waarschuwing wordt gegenereerd op een VM, Server, Schaalset voor virtuele machines of App Service instantie die aan een werk ruimte rapporteert, bevat die werkruimte naam van de resource groep|
|**WorkspaceSubscriptionId**|Als de waarschuwing wordt gegenereerd op een VM, Server, Schaalset voor virtuele machines of App Service instantie die rapporteert aan een werk ruimte, bevat die werk ruimte subscriptionId|
|||
