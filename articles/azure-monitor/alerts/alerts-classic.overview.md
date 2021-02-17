---
title: Overzicht van klassieke waarschuwingen in Azure Monitor
description: Klassieke waarschuwingen worden afgeschaft. Met waarschuwingen kunt u de metrische gegevens, gebeurtenissen of logboeken van Azure-resources bewaken en een melding ontvangen wanneer aan de opgegeven voor waarde wordt voldaan.
ms.topic: conceptual
ms.date: 05/19/2018
ms.subservice: alerts
ms.openlocfilehash: 2b1f236255c7cbf073cf7edc5a10df895f9c1095
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100612870"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>Wat zijn klassieke waarschuwingen in Microsoft Azure?

> [!NOTE]
> In dit artikel wordt beschreven hoe u oudere klassieke metrische waarschuwingen maakt. Azure Monitor ondersteunt nu [nieuwere bijna realtime waarschuwingen voor metrische gegevens en een nieuwe meldings ervaring](../platform/alerts-overview.md). Klassieke waarschuwingen worden [buiten gebruik gesteld](../platform/monitoring-classic-retirement.md) voor open bare Cloud gebruikers, maar zijn nog steeds beperkt in beperkte mate voor resources die de nieuwe waarschuwingen nog niet ondersteunen.
>

Met waarschuwingen kunt u voor waarden voor gegevens configureren en op de hoogte worden gesteld wanneer de voor waarden overeenkomen met de meest recente bewakings gegevens.

## <a name="old-and-new-alerting-capabilities"></a>Oude en nieuwe waarschuwings mogelijkheden

In het verleden Azure Monitor, Application Insights, Log Analytics en Service Health afzonderlijke waarschuwings mogelijkheden. Overwerk, Azure heeft de gebruikers interface en verschillende methoden van waarschuwingen verbeterd en gecombineerd. De consolidatie wordt nog steeds uitgevoerd.

U kunt alleen klassieke waarschuwingen weer geven in het scherm klassieke waarschuwingen gebruikers in azure Portal. U krijgt dit scherm te zien via de knop **klassieke waarschuwingen weer geven** in het scherm waarschuwingen. 

 ![Waarschuwings opties in Azure Portal](media/alerts-classic.overview/monitor-alert-screen2.png)

De gebruikers ervaring van nieuwe waarschuwingen heeft de volgende voor delen ten opzichte van de klassieke waarschuwingen:
- **Verbeterd systeem voor meldingen** -alle nieuwere waarschuwingen gebruiken actie groepen, die groepen van meldingen en acties worden genoemd en die opnieuw kunnen worden gebruikt in meerdere waarschuwingen. Bij klassieke metrische waarschuwingen en oudere Log Analytics waarschuwingen worden geen actie groepen gebruikt.
- **Een uniforme ontwerp-ervaring** : elke waarschuwing voor het maken van metrische gegevens, logboeken en activiteiten logboeken over Azure Monitor, Log Analytics en Application Insights bevindt zich op één plek.
- **Geactiveerde log Analytics waarschuwingen weer geven in azure Portal** -u kunt nu ook gebrande log Analytics waarschuwingen in uw abonnement zien. Voorheen waren deze in een afzonderlijke portal.
- **Schei ding van gebrande waarschuwingen en waarschuwings regels** : waarschuwings regels (de definitie van voor waarde waarmee een waarschuwing wordt geactiveerd) en gebrande waarschuwingen (een exemplaar van het starten van de waarschuwings regel) worden gedifferentieerd, waardoor de operationele en configuratie weergaven gescheiden zijn.
- **Betere werk stroom** : de nieuwe ervaring voor het ontwerpen van waarschuwingen begeleidt de gebruiker over het proces van het configureren van een waarschuwings regel, waardoor het eenvoudiger wordt om op de hoogte te worden gesteld van de juiste dingen om te worden gewaarschuwd.
- **Consolidatie van intelligente waarschuwingen** en **instellen van waarschuwings status**  : nieuwe waarschuwingen zijn de functionaliteit voor automatische groepering van vergelijk bare waarschuwingen, zodat de overbelasting in de gebruikers interface wordt verminderd. 

De nieuwere metrische waarschuwingen hebben de volgende voor delen ten opzichte van de klassieke metrische waarschuwingen:
- **Verbeterde latentie**: nieuwere metrische waarschuwingen kunnen per minuut worden uitgevoerd. Oudere metrische waarschuwingen worden altijd uitgevoerd met een frequentie van vijf minuten. Nieuwere waarschuwingen verg Roten de kleinere vertraging van het probleem met de melding of actie (3 tot 5 minuten). Oudere waarschuwingen zijn vijf tot 15 minuten, afhankelijk van het type.  Logboek waarschuwingen hebben doorgaans een vertraging van 10 tot 15 minuten als gevolg van de tijd die nodig is om de logboeken op te nemen, maar nieuwere verwerkings methoden verkorten die tijd. 
- **Ondersteuning voor multidimensionale metrische gegevens**: u kunt een waarschuwing over dimensionale metrieken om een interessant segment van de metriek te bewaken.
- **Meer controle over metrische voor waarden**: u kunt rijkere waarschuwings regels definiëren. De nieuwere waarschuwingen bieden ondersteuning voor het bewaken van het maximum, het minimum, het gemiddelde en de totale waarde van metrische gegevens.
- **Gecombineerde bewaking van meerdere metrische gegevens**: u kunt meerdere metrische gegevens (op dit moment, Maxi maal twee metrische gegevens) bewaken met één regel. Er wordt een waarschuwing geactiveerd als beide metrische gegevens hun respectieve drempel waarden voor de opgegeven periode hebben geschonden.
- **Verbeterd systeem voor meldingen**: voor alle nieuwere waarschuwingen worden [actie groepen](../platform/action-groups.md)gebruikt, die groepen van meldingen en acties worden genoemd en die opnieuw kunnen worden gebruikt in meerdere waarschuwingen.  Bij klassieke metrische waarschuwingen en oudere Log Analytics waarschuwingen worden geen actie groepen gebruikt. 
- **Metrische gegevens uit logboeken** (open bare preview-versie): het registreren van log Analytics kan nu worden oppakt en geconverteerd naar Azure monitor meet gegevens en vervolgens worden gewaarschuwd op dezelfde manier als andere gegevens. Bekijk [waarschuwingen (klassiek)]() voor de terminologie die specifiek is voor klassieke waarschuwingen. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Klassieke waarschuwingen voor Azure Monitor gegevens
Er zijn twee soorten klassieke waarschuwingen beschikbaar: metrische waarschuwingen en waarschuwingen voor activiteiten Logboeken.

* **Klassieke waarschuwingen voor metrische gegevens** : deze waarschuwing wordt geactiveerd wanneer de waarde van een opgegeven metriek een drempel overschrijdt die u toewijst. De waarschuwing genereert een melding wanneer de drempel waarde wordt overschreden en er wordt voldaan aan de status van de waarschuwing. Op dat moment wordt de waarschuwing als ' geactiveerd ' beschouwd. Er wordt nog een melding gegenereerd wanneer het ' opgelost ' is, dat wil zeggen, wanneer de drempel waarde opnieuw wordt overschreden en er niet meer aan de voor waarde wordt voldaan.

* **Waarschuwingen voor klassieke activiteiten logboeken** : een streaming-logboek waarschuwing die wordt geactiveerd door een gebeurtenis vermelding in het activiteiten logboek die overeenkomt met de filter criteria. Deze waarschuwingen hebben slechts één status, "geactiveerd". Met de waarschuwings-engine worden de filter criteria toegepast op elke nieuwe gebeurtenis. Er wordt niet gezocht naar oudere vermeldingen. Deze waarschuwingen kunnen u waarschuwen wanneer een nieuw Service Health incident optreedt of wanneer een gebruiker of toepassing een bewerking in uw abonnement uitvoert, bijvoorbeeld ' virtuele machine verwijderen '.

Voor resource logboek gegevens die beschikbaar zijn via Azure Monitor, de gegevens routeren naar Log Analytics en een waarschuwing voor een logboek query gebruiken. Log Analytics maakt nu gebruik van de [nieuwe methode voor waarschuwingen](../platform/alerts-overview.md) 

In het volgende diagram vindt u een overzicht van de gegevens bronnen in Azure Monitor en, in het algemeen, hoe u een melding kunt ontvangen van die gegevens.

![Uitleg bij waarschuwingen](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taxonomie van waarschuwingen (klassiek)
Azure gebruikt de volgende termen om klassieke waarschuwingen en de bijbehorende functies te beschrijven:
* **Waarschuwing** : een definitie van criteria (een of meer regels of voor waarden) die worden geactiveerd wanneer wordt voldaan.
* **Actief** : de status wanneer wordt voldaan aan de criteria die zijn gedefinieerd door een klassieke waarschuwing.
* **Opgelost** : de status wanneer er eerder is voldaan aan de criteria die zijn gedefinieerd door een klassieke waarschuwing.
* **Melding** : de actie die is uitgevoerd op basis van een klassieke waarschuwing, wordt actief.
* **Actie** : een specifieke aanroep die wordt verzonden naar een ontvanger van een melding (bijvoorbeeld een e-mail adres of een bericht naar een webhook-URL). Meldingen kunnen doorgaans meerdere acties activeren.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Hoe kan ik een melding ontvangen van een Azure Monitor klassieke waarschuwing?
In het verleden hebben Azure-waarschuwingen van verschillende services hun eigen ingebouwde meldings methoden gebruikt. 

Azure Monitor een herbruikbare meldings groepering met de naam *actie groepen* gemaakt. Actie groepen geven een set ontvangers op voor een melding. Telkens wanneer een waarschuwing wordt geactiveerd die verwijst naar de actie groep, ontvangen alle ontvangers deze melding. Met actie groepen kunt u een groep van ontvangers (bijvoorbeeld uw lijst met technici op aanroepen) opnieuw gebruiken voor verschillende waarschuwings objecten. Actie groepen bieden ondersteuning voor meldingen door te posten naar een webhook-URL naast e-mail adressen, SMS-nummers en een aantal andere acties.  Zie [actie groepen](../platform/action-groups.md)voor meer informatie. 

Eerdere waarschuwingen voor klassieke activiteiten Logboeken gebruiken actie groepen.

De oudere metrische waarschuwingen gebruiken echter geen actie groepen. In plaats daarvan kunt u de volgende acties configureren: 
- E-mail meldingen verzenden naar de service beheerder, naar mede beheerders of naar extra e-mail adressen die u opgeeft.
- Een webhook aanroepen, waarmee u extra automatiserings acties kunt starten.

Webhooks maken automatisering en herstel mogelijk, bijvoorbeeld met:
- Azure Automation Runbook
- Azure-functie
- Azure Logic-app
- een service van derden

## <a name="next-steps"></a>Volgende stappen
Informatie over waarschuwings regels ophalen en deze configureren met behulp van:

* Meer informatie over [metrische gegevens](../platform/data-platform.md)
* [Klassieke metrische waarschuwingen configureren via Azure Portal](alerts-classic-portal.md)
* [Klassieke waarschuwingen voor metrische gegevens](alerts-classic-portal.md) configureren
* [Klassieke metrische waarschuwingen configureren opdracht regel interface (CLI)](alerts-classic-portal.md)
* [Klassieke waarschuwingen voor metrische gegevens Azure Monitor rest API](/rest/api/monitor/alertrules) configureren
* Meer informatie over [activiteiten logboek](../platform/platform-logs-overview.md)
* [Waarschuwingen voor activiteiten logboek configureren via Azure Portal](../platform/activity-log-alerts.md)
* [Waarschuwingen voor activiteiten logboek configureren via Resource Manager](../platform/alerts-activity-log.md)
* Het [webhook-schema voor waarschuwingen in het activiteiten logboek](activity-log-alerts-webhook.md) bekijken
* Meer informatie over [actie groepen](../platform/action-groups.md)
* [Nieuwere waarschuwingen](alerts-metric.md) configureren

