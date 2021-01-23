---
title: Flexibiliteit door bewaking en analyse met behulp van Azure AD B2C | Microsoft Docs
description: Flexibiliteit door bewaking en analyse met behulp van Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90b2cd4521613a7b449598f0d097a7ec1c2958c6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724539"
---
# <a name="resilience-through-monitoring-and-analytics"></a>Flexibiliteit door middel van bewaking en analyse

Bewaking maximaliseert de beschik baarheid en prestaties van uw toepassingen en services. Het biedt een uitgebreide oplossing voor het verzamelen, analyseren en interactief werken met telemetrie van uw infra structuur en toepassingen. Waarschuwingen geven u proactief op de hoogte wanneer er problemen met uw service of toepassingen worden gevonden. Hiermee kunt u problemen identificeren en oplossen voordat de eind gebruikers van uw service deze melden. Met [Azure AD log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) kunt u de controle logboeken en aanmeld logboeken analyseren, doorzoeken en aangepaste weer gaven maken.

## <a name="monitor-and-get-notified-through-alerts"></a>Controleren en meldingen ontvangen via waarschuwingen

Het controleren van uw systeem en infra structuur is van cruciaal belang om de algehele status van uw services te garanderen. Het begint met de definitie van bedrijfs metrieken, zoals de nieuwe gebruikers aankomst, de verificatie tarieven van de eind gebruiker en de conversie. Deze indica toren zodanig configureren dat ze worden bewaakt. Als u van plan bent om een aanstaande stroom te plannen vanwege promotie-of vakantie verkeer, kunt u uw schattingen herzien voor de gebeurtenis en de bijbehorende Bench Mark voor de metrische gegevens van het bedrijf. Na de gebeurtenis keert u terug naar de vorige Bench Mark.

Op dezelfde manier kunt u een goede basis lijn instellen om fouten of prestatie onderbrekingen te detecteren en vervolgens waarschuwingen te definiëren.

![Afbeelding bevat bewakings-en analyse onderdelen](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>Bewakings-en waarschuwings functies implementeren

- **Bewaking**: gebruik [Azure monitor](../../active-directory-b2c/azure-monitor.md) om de status voortdurend te controleren op basis van de belangrijkste serviceniveau doelstellingen (SLO) en ontvang meldingen wanneer een kritieke wijziging optreedt. Begin door Azure AD B2C beleid of een toepassing te identificeren als een essentieel onderdeel van uw bedrijf waarvan de status moet worden bewaakt om SLO te onderhouden. Bepaal de belangrijkste indica toren die met uw Slo's worden uitgelijnd.
U kunt bijvoorbeeld de volgende metrische gegevens volgen, omdat een plotselinge daling in het bedrijf leidt tot verlies.

  - **Totaal aantal aanvragen**: het totale n-nummer van aanvragen dat is verzonden naar Azure AD B2C beleid.

  - **Voltooiings percentage (%)**: geslaagde aanvragen/totaal aantal aanvragen.

  Toegang krijgen tot de [belangrijkste indica toren](../../active-directory-b2c/view-audit-logs.md) in [application Insights](../../active-directory-b2c/analytics-with-application-insights.md) waarbij Azure AD B2C op beleid gebaseerde logboeken, [audit logboeken](../../active-directory-b2c/analytics-with-application-insights.md)en aanmeld logboeken worden opgeslagen.  

   - **Visualisaties**: het maken van Dash boards van log Analytics om de belangrijkste indica toren visueel te bewaken.

   - **Huidige periode**: tijdelijke grafieken maken voor het weer geven van wijzigingen in het totaal aantal aanvragen en het succes percentage (%) in de huidige periode, bijvoorbeeld de huidige week.

   - **Vorige periode**: tijdelijke grafieken maken voor het weer geven van wijzigingen in het totaal aantal aanvragen en het succes percentage (%) gedurende een vorige periode voor referentie doeleinden, bijvoorbeeld afgelopen week.

- **Waarschuwing**: gebruik log Analytics om [waarschuwingen](../../azure-monitor/platform/alerts-log.md) te definiëren die worden geactiveerd wanneer er onverwachte wijzigingen in de belangrijkste indica toren zijn. Deze wijzigingen kunnen een negatieve invloed hebben op de Slo's. Waarschuwingen gebruiken verschillende vormen van meldings methoden, waaronder e-mail, SMS en webhooks. Begin met het definiëren van een criterium dat fungeert als drempel waarde waarmee de waarschuwing wordt geactiveerd. Bijvoorbeeld:
  - Waarschuwing voor plotseling neerzetten in totaal aantal aanvragen: een waarschuwing activeren wanneer het aantal aanvragen abrupt neer komt. Als er bijvoorbeeld een 25%-verwijdering is in het totaal aantal aanvragen vergeleken met de vorige periode, moet u een waarschuwing genereren.  
  - Waarschuwing tegen een aanzienlijke daling van het slagings percentage (%): er wordt een waarschuwing geactiveerd wanneer het succes percentage van het geselecteerde beleid aanzienlijk daalt.
  - Wanneer u een waarschuwing ontvangt, moet u het probleem oplossen met behulp van [log Analytics](../reports-monitoring/howto-install-use-log-analytics-views.md), [Application Insights](../../active-directory-b2c/troubleshoot-with-application-insights.md)en de [uitbrei ding VS code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) voor Azure AD B2C. Na het oplossen van het probleem en het implementeren van een bijgewerkte toepassing of beleid, blijft de sleutel indicatoren bewaken tot het normale bereik wordt teruggegeven.

- **Service waarschuwingen**: gebruik de [Azure AD B2C service level waarschuwingen](../../service-health/service-health-overview.md) om op de hoogte te worden gesteld van Service problemen, gepland onderhoud, status advies en beveiligings advies.

- **Rapportage**: [door gebruik te maken van log Analytics](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), maakt u rapporten waarmee u meer inzicht krijgt in gebruikers inzichten, technische uitdagingen en groei kansen.
  - **Status dashboard**: [aangepaste Dash boards maken met behulp van de Azure-dashboard](../../azure-monitor/learn/tutorial-app-dashboards.md) functie, die ondersteuning biedt voor het toevoegen van grafieken met log Analytics query's. U kunt bijvoorbeeld het patroon van geslaagde en mislukte aanmeldingen identificeren, fout oorzaken en telemetrie over apparaten die worden gebruikt voor het maken van de aanvragen.
  - **Azure AD B2C trajecten verlaten**: gebruik de [werkmap](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys) om de lijst met achtergelaten Azure AD B2C trajecten bij te houden waarbij de gebruiker zich aanmeldt of zich heeft aangemeld, maar nooit is voltooid. Het bevat informatie over de beleids-ID en de uitsplitsing van de stappen die door de gebruiker worden uitgevoerd voordat de rit wordt verlaten.
  - **Azure AD B2C bewaking van werkmappen**: gebruik de [bewakings werkmappen](https://github.com/azure-ad-b2c/siem), waaronder Azure AD B2C dash board, multi-factor Authentication (MFA)-bewerkingen, rapporten voor voorwaardelijke toegang en zoek logboeken door correlationId, zodat u beter inzicht krijgt in de status van uw Azure AD B2C omgeving.
  
## <a name="next-steps"></a>Volgende stappen

- [Flexibiliteits bronnen voor Azure AD B2C-ontwikkel aars](resilience-b2c.md)
  - [Flexibele ervaring voor eind gebruikers](resilient-end-user-experience.md)
  - [Robuuste interfaces met externe processen](resilient-external-processes.md)
  - [Flexibiliteit door middel van aanbevolen procedures voor ontwikkel aars](resilience-b2c-developer-best-practices.md)
- [Flexibiliteit in uw verificatie-infra structuur maken](resilience-in-infrastructure.md)
- [Verhoog de flexibiliteit van verificatie en autorisatie in uw toepassingen](resilience-app-development-overview.md)
