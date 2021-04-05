---
title: De gegevens van de Azure AD Connect Health Health-Service zijn niet up-to-date, waarschuwing | Microsoft Docs
description: Dit document beschrijft de oorzaak van de waarschuwing dat de Health-Service gegevens niet up-to-date zijn en hoe u deze kunt oplossen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00518eb91e57efaacb7abc63b6ad4531619be2ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012866"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>De Health Service-gegevens zijn niet up-to-date-waarschuwing

## <a name="overview"></a>Overzicht

De agents op de on-premises machines die Azure AD Connect Health bewaakt regel matig gegevens uploaden naar de Azure AD Connect Health-Service. Als de service geen gegevens van een agent ontvangt, worden de gegevens die de portal presenteert, verouderd. Om het probleem te markeren, **is de status van de service niet up-to-date** . Deze waarschuwing wordt gegenereerd wanneer de service de afgelopen twee uur geen volledige gegevens heeft ontvangen.  

- De **waarschuwing** status waarschuwing wordt geactiveerd als de Health Service in de afgelopen twee uur slechts **gedeeltelijk** gegevens typen heeft ontvangen die vanaf de server zijn verzonden. De waarschuwing status waarschuwing triggert geen e-mail meldingen naar geconfigureerde ontvangers. 
- De **fout** status waarschuwing wordt geactiveerd als de Health Service in de afgelopen twee uur geen gegevens typen van de server heeft ontvangen. Met de fout status waarschuwing worden e-mail meldingen naar geconfigureerde ontvangers gegenereerd.

De service haalt de gegevens op van agents die worden uitgevoerd op de on-premises machines, afhankelijk van het Service type. De volgende tabel geeft een lijst van de agents die op de computer worden uitgevoerd, wat ze doen en de gegevens typen die door de service worden gegenereerd. In sommige gevallen zijn er meerdere services betrokken bij het proces, zodat elk daarvan de culprit kan zijn. 

## <a name="understanding-the-alert"></a>Informatie over de waarschuwing

In de Blade **waarschuwings Details** wordt weer gegeven wanneer de waarschuwing is opgetreden en de laatste keer is gedetecteerd. Met een achtergrond proces dat elke twee uur wordt uitgevoerd, wordt de waarschuwing gegenereerd en opnieuw geëvalueerd. In het volgende voor beeld is de eerste waarschuwing op 03/10 om 9:59 uur. De waarschuwing bevond zich nog op 03/12 om 10:00 uur wanneer de waarschuwing opnieuw werd geëvalueerd. De Blade bevat ook informatie over de tijd waarop de Health Service voor het laatst een bepaald gegevens type heeft ontvangen. 
 
 ![Details van Azure AD Connect Health waarschuwing](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
De volgende tabel wijst service types toe aan de bijbehorende vereiste gegevens typen:

| Servicetype | Agent (Windows-service naam) | Doel | Gegenereerd gegevens type  |
| --- | --- | --- | --- |  
| Azure AD Connect (synchronisatie) | Azure AD Connect Health Sync Insights-service | AAD Connect-specifieke informatie verzamelen (connectors, synchronisatie regels, enz.) | -AadSyncService-SynchronizationRules <br />  -AadSyncService-Connectors <br /> -AadSyncService-GlobalConfigurations  <br />  -AadSyncService-RunProfileResults <br /> -AadSyncService-ServiceConfigurations <br /> -AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring-service | AAD Connect-specifieke prestatie meter items, ETW-traceringen, bestanden verzamelen | Prestatie meter item |
| AD DS | Azure AD Connect Health AD DS Insights-service | Synthetische tests uitvoeren, topologie gegevens verzamelen, meta gegevens voor replicatie |  -Toevoegen-TopologyInfo-JSON <br /> -Common-TestData-JSON (maakt de test resultaten)   | 
|  | Azure AD Connect Health AD DS Monitoring-service | Verzamelen voegt specifieke prestatie meter items, ETW-traceringen, bestanden | -Prestatie meter item  <br /> -Common-TestData-JSON (uploadt de test resultaten)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics-service | Synthetische tests uitvoeren | TestResult (Hiermee worden de test resultaten gemaakt) | 
| | Azure AD Connect Health AD FS Insights-service  | Metrische gegevens over ADFS-gebruik verzamelen | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring-service | AD FS-specifieke prestatie meter items, ETW-traceringen, bestanden verzamelen | TestResult (uploadt de test resultaten) |

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing 

De stappen die nodig zijn om de oorzaak van het probleem vast te stellen, worden hieronder gegeven. De eerste is een set basis controles die gemeen schappelijk zijn voor alle service typen. 

> [!IMPORTANT] 
> Deze waarschuwing volgt het [Bewaar beleid](reference-connect-health-user-privacy.md#data-retention-policy) voor verbindings status gegevens

* Controleer of de nieuwste versies van de agents zijn geïnstalleerd. Bekijk de [releasegeschiedenis](reference-connect-health-version-history.md). 
* Zorg ervoor dat de agents van de Azure AD Connect Health-service worden **uitgevoerd** op de computer. Connect Health voor AD FS moet bijvoorbeeld drie services hebben.
  ![Azure AD Connect Health verifiëren](./media/how-to-connect-health-agent-install/install5.png)

* Zorg ervoor dat u voldoet aan de [sectie vereisten](how-to-connect-health-agent-install.md#requirements).
* Gebruik het [hulp programma](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) voor het testen van connectiviteit om verbindings problemen te detecteren.
* Als u een HTTP-proxy hebt, volgt u deze [Configuratie stappen](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Volgende stappen
Als een van de bovenstaande stappen een probleem heeft vastgesteld, lost u het op en wacht u totdat de waarschuwing is opgelost. Het waarschuwings proces wordt elke 2 uur uitgevoerd, zodat het Maxi maal twee uur duurt om de waarschuwing op te lossen. 

* [Azure AD Connect Health beleid voor het bewaren van gegevens](reference-connect-health-user-privacy.md#data-retention-policy)
* [Veelgestelde vragen over Azure AD Connect Health](reference-connect-health-faq.md)
