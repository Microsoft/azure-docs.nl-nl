---
title: Automatisch schalen v1
description: Automatisch schalen en App Service Environment v1. Dit document is alleen bedoeld voor klanten die gebruikmaken van de oudere V1-ASE.
author: btardif
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4f071c0d09fc2fa97eeea45bd82228b7eb8434a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021653"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Automatisch schalen en App Service Environment v1

> [!NOTE]
> Dit artikel heeft betrekking op de App Service Environment v1.  Er is een nieuwere versie van de App Service Environment die eenvoudiger is te gebruiken en wordt uitgevoerd op een krachtigere infra structuur. Begin met de [Inleiding tot de app service Environment](intro.md)voor meer informatie over de nieuwe versie.
> 

Azure App Service omgevingen ondersteunen automatisch *schalen*. U kunt afzonderlijke werk groepen automatisch schalen op basis van metrische gegevens of planning.

![Opties voor automatisch schalen voor een werk groep.][intro]

Met automatisch schalen kunt u het resource gebruik optimaliseren door een App Service omgeving te verg Roten of te verkleinen, zodat deze aan uw budget en of het profiel voor het laden voldoet.

## <a name="configure-worker-pool-autoscale"></a>Automatisch schalen van werk groepen configureren
U kunt de functie voor automatisch schalen openen via het tabblad **instellingen** van de werk groep.

![Het tabblad instellingen van de groep werk nemers.][settings-scale]

Van daaruit moet de interface tamelijk bekend zijn, omdat deze dezelfde ervaring heeft als u een App Service plan schaalt. 

![Instellingen voor hand matig schalen.][scale-manual]

U kunt ook een profiel voor automatisch schalen configureren.

![Instellingen voor automatisch schalen.][scale-profile]

Profielen voor automatisch schalen zijn handig voor het instellen van limieten voor uw schaal. Op deze manier kunt u een consistente prestatie-ervaring hebben door een lagere grens waarde (1) en een voorspel bare bestedings limiet in te stellen door een bovengrens (2) in te stellen.

![Schaal instellingen in het profiel.][scale-profile2]

Nadat u een profiel hebt gedefinieerd, kunt u regels voor automatisch schalen toevoegen om het aantal instanties in de werk groep binnen de grenzen die door het profiel zijn gedefinieerd, omhoog of omlaag te schalen. Regels voor automatisch schalen zijn gebaseerd op metrische gegevens.

![Schaal regel.][scale-rule]

 Een werk groep of front-end-metrische gegevens kunnen worden gebruikt voor het definiëren van regels voor automatisch schalen. Deze metrische gegevens zijn dezelfde metrische gegevens die u kunt bewaken in de Blade van de resource en voor het instellen van waarschuwingen voor.

## <a name="autoscale-example"></a>Voor beeld van automatisch schalen
Automatisch schalen van een App Service omgeving kan het beste worden geïllustreerd door een scenario door te lopen.

In dit artikel worden alle benodigde overwegingen uitgelegd bij het instellen van automatisch schalen. Het artikel begeleidt u door de interacties die tijdens het automatisch schalen van App Service omgevingen die worden gehost in App Service Environment worden afgespeeld.

### <a name="scenario-introduction"></a>Scenario Inleiding
Frank is een sysadmin van een onderneming die een deel van de workloads heeft gemigreerd die ze naar een App Service omgeving beheren.

De App Service omgeving wordt als volgt geconfigureerd als hand matig schalen:

* **Front-ends:** 3
* **Werknemers groep 1**: 10
* **Werknemers groep 2**: 5
* **Werknemers groep 3**: 5

Werk groep 1 wordt gebruikt voor werk belastingen voor productie, terwijl werk groep 2 en werk groep 3 worden gebruikt voor kwaliteits controle (QA) en ontwikkelings werk belastingen.

De App Service plannen voor QA en dev zijn geconfigureerd voor hand matige schalen. Het plan voor productie App Service is ingesteld op automatisch schalen om te omgaan met variaties in belasting en verkeer.

Frank is bekend met de toepassing. Ze weten dat de piek uren voor belasting tussen 9:00 uur en 6:00 uur zijn, omdat dit een LOB-toepassing (line-of-Business) is die werk nemers gebruiken terwijl ze zich op kantoor bevinden. Het gebruik gaat verloren wanneer gebruikers voor die dag klaar zijn. Buiten de piek uren is er nog enige belasting, omdat gebruikers op afstand toegang hebben tot de app via hun mobiele apparaten of thuis Pc's. Het plan voor productie App Service is al geconfigureerd om automatisch te schalen op basis van CPU-gebruik met de volgende regels:

![Specifieke instellingen voor de LOB-app.][asp-scale]

| **Profiel voor automatisch schalen – werk dagen-App Service plan** | **Profiel voor automatisch schalen: weekends-App Service plan** |
| --- | --- |
| **Naam:** Weekdag profiel |**Naam:** Weekend-profiel |
| **Schalen op:** Regels voor planning en prestaties |**Schalen op:** Regels voor planning en prestaties |
| **Profiel:** Week dagen |**Profiel:** Week |
| **Type:** Optreden |**Type:** Optreden |
| **Doel bereik:** 5 tot 20 exemplaren |**Doel bereik:** 3 tot 10 instanties |
| **Dagen:** Maandag, dinsdag, woensdag, donderdag, vrijdag |**Dagen:** Zaterdag, zondag |
| **Begin tijd:** 9:00 uur |**Begin tijd:** 9:00 uur |
| **Tijd zone:** UTC-08 |**Tijd zone:** UTC-08 |
|  | |
| **Regel voor automatisch schalen (omhoog schalen)** |**Regel voor automatisch schalen (omhoog schalen)** |
| **Resource:** Productie (App Service Environment) |**Resource:** Productie (App Service Environment) |
| **Metriek:** VERBRUIK |**Metriek:** VERBRUIK |
| **Bewerking:** Meer dan 60% |**Bewerking:** Meer dan 80% |
| **Duur:** 5 minuten |**Duur:** 10 minuten |
| **Tijd aggregatie:** Evenredig |**Tijd aggregatie:** Evenredig |
| **Actie:** Aantal verhogen met 2 |**Actie:** Aantal verhogen met 1 |
| **Afkoelen (minuten):** 15 |**Afkoelen (minuten):** 20 |
|  | |
| **Regel voor automatisch schalen (omlaag schalen)** |**Regel voor automatisch schalen (omlaag schalen)** |
| **Resource:** Productie (App Service Environment) |**Resource:** Productie (App Service Environment) |
| **Metriek:** VERBRUIK |**Metriek:** VERBRUIK |
| **Bewerking:** Minder dan 30% |**Bewerking:** Minder dan 20% |
| **Duur:** 10 minuten |**Duur:** 15 minuten |
| **Tijd aggregatie:** Evenredig |**Tijd aggregatie:** Evenredig |
| **Actie:** Aantal verlagen met 1 |**Actie:** Aantal verlagen met 1 |
| **Afkoelen (minuten):** 20 |**Afkoelen (minuten):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Inflatie frequentie App Service plan
App Service abonnementen die zijn geconfigureerd om automatisch te worden geschaald, hebben een maximum snelheid per uur. Dit aantal kan worden berekend op basis van de waarden die zijn opgenomen in de regel voor automatisch schalen.

Het is belang rijk App Service dat de inflatie van het *app service plan wordt beoordeeld* en berekend, omdat schaal wijzigingen in een werk groep niet onmiddellijk worden geschaald.

De inflatie voor het App Service plan wordt als volgt berekend:

![De berekening van de inflatie van App Service plannen.][ASP-Inflation]

Op basis van de regel voor automatisch schalen: omhoog schalen voor het weekdag Profiel van het productie App Service plan:

![App Service de inflatie voor werk dagen te plannen op basis van de regel voor automatisch schalen.][Equation1]

In het geval van de regel voor automatisch schalen – opschalen voor het weekend Profiel van de productie-App Service plan, wordt de formule omgezet in:

![App Service de inflatie voor weekends te plannen op basis van de regel voor automatisch schalen.][Equation2]

Deze waarde kan ook worden berekend voor schaal bewerkingen.

Op basis van de regel voor automatisch schalen-omlaag schalen voor het weekdag Profiel van het productie App Service plan ziet u het volgende:

![App Service de inflatie voor werk dagen te plannen op basis van de regel voor automatisch schalen.][Equation3]

In het geval van de regel voor automatisch schalen-omlaag schalen voor het weekend Profiel van de productie-App Service plan, wordt de formule omgezet in:  

![App Service de inflatie voor weekends te plannen op basis van de regel voor automatisch schalen-omlaag schalen.][Equation4]

Het productie App Service plan kan worden uitgebreid met Maxi maal acht exemplaren per uur tijdens de week en vier exemplaren per uur tijdens het weekend. Het kan instanties vrijgeven met een maximum snelheid van vier exemplaren per uur tijdens de week en zes exemplaren/uur tijdens het weekend.

Als er meerdere App Service plannen worden gehost in een werknemers groep, moet u het *totale inflatie percentage* berekenen als de som van het inflatie percentage voor alle app service plannen die worden gehost in die werknemers groep.

![Totale inflatie berekening voor meerdere App Service plannen die worden gehost in een werknemers groep.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Gebruik het App Service plan inflatie tarieven voor het definiëren van regels voor automatisch schalen van werk groepen
Werk groepen die App Service plannen hosten die zijn geconfigureerd voor automatisch schalen, moeten een buffer capaciteit toewijzen. De buffer maakt het mogelijk om automatisch te schalen en zo nodig het App Service plan te verkleinen. De minimale buffer is de berekende totale App Service het inflatie percentage van het plan.

Omdat App Service omgevings schaal bewerkingen enige tijd in beslag nemen, moet elke wijziging rekening houden met verdere wijzigingen in de vraag die kunnen optreden terwijl een schaal bewerking wordt uitgevoerd. Voor deze latentie raden we u aan om de berekende totale App Service-inflatie verhouding te gebruiken als het minimum aantal exemplaren dat voor elke bewerking voor automatisch schalen is toegevoegd.

Met deze informatie kan Frank het volgende profiel voor automatisch schalen en regels definiëren:

![Profiel regels automatisch schalen voor het LOB-voor beeld.][Worker-Pool-Scale]

| **Profiel voor automatisch schalen: werk dagen** | **Profiel voor automatisch schalen: weekends** |
| --- | --- |
| **Naam:** Weekdag profiel |**Naam:** Weekend-profiel |
| **Schalen op:** Regels voor planning en prestaties |**Schalen op:** Regels voor planning en prestaties |
| **Profiel:** Week dagen |**Profiel:** Week |
| **Type:** Optreden |**Type:** Optreden |
| **Doel bereik:** 13 tot 25 exemplaren |**Doel bereik:** 6 tot 15 exemplaren |
| **Dagen:** Maandag, dinsdag, woensdag, donderdag, vrijdag |**Dagen:** Zaterdag, zondag |
| **Begin tijd:** 7:00 uur |**Begin tijd:** 9:00 uur |
| **Tijd zone:** UTC-08 |**Tijd zone:** UTC-08 |
|  | |
| **Regel voor automatisch schalen (omhoog schalen)** |**Regel voor automatisch schalen (omhoog schalen)** |
| **Resource:** Werknemers groep 1 |**Resource:** Werknemers groep 1 |
| **Metriek:** WorkersAvailable |**Metriek:** WorkersAvailable |
| **Bewerking:** Minder dan 8 |**Bewerking:** Minder dan 3 |
| **Duur:** 20 minuten |**Duur:** 30 minuten |
| **Tijd aggregatie:** Evenredig |**Tijd aggregatie:** Evenredig |
| **Actie:** Aantal verhogen met 8 |**Actie:** Aantal verhogen met 3 |
| **Afkoelen (minuten):** 180 |**Afkoelen (minuten):** 180 |
|  | |
| **Regel voor automatisch schalen (omlaag schalen)** |**Regel voor automatisch schalen (omlaag schalen)** |
| **Resource:** Werknemers groep 1 |**Resource:** Werknemers groep 1 |
| **Metriek:** WorkersAvailable |**Metriek:** WorkersAvailable |
| **Bewerking:** Groter dan 8 |**Bewerking:** Groter dan 3 |
| **Duur:** 20 minuten |**Duur:** 15 minuten |
| **Tijd aggregatie:** Evenredig |**Tijd aggregatie:** Evenredig |
| **Actie:** Aantal verlagen met 2 |**Actie:** Aantal verlagen met 3 |
| **Afkoelen (minuten):** 120 |**Afkoelen (minuten):** 120 |

Het doel bereik dat in het profiel is gedefinieerd, wordt berekend op basis van het minimum aantal exemplaren dat is gedefinieerd in het profiel voor de App Service plan +-buffer.

Het maximum bereik is de som van alle maximale bereiken voor alle App Service plannen die worden gehost in de werk groep.

Het aantal verg Roten voor de regels voor omhoog schalen moet worden ingesteld op ten minste 1X het App Service plan inflatie voor omhoog schalen.

Aantal verminderingen kan worden aangepast aan iets tussen 1/2X, of de App Service de inflatie van het schema voor omlaag schalen.

### <a name="autoscale-for-front-end-pool"></a>Automatisch schalen voor front-end-groep
Regels voor het automatisch schalen van de front-end zijn eenvoudiger dan voor werk groepen. In de eerste plaats moet u  
Zorg ervoor dat de duur van de meting en de cooldown-timers overwegen dat schaal bewerkingen op een App Service plan niet onmiddellijk zijn.

In dit scenario weet Frank dat de fout frequentie toeneemt nadat de front-ends een CPU-gebruik hebben bereikt van 80% en wordt de regel voor automatisch schalen ingesteld om instanties te verhogen:

![Instellingen voor automatisch schalen voor de front-end-pool.][Front-End-Scale]

| **Profiel voor automatisch schalen: front-ends** |
| --- |
| **Naam:** Automatisch schalen: front-ends |
| **Schalen op:** Regels voor planning en prestaties |
| **Profiel:** Elke dag |
| **Type:** Optreden |
| **Doel bereik:** 3 tot 10 instanties |
| **Dagen:** Elke dag |
| **Begin tijd:** 9:00 uur |
| **Tijd zone:** UTC-08 |
|  |
| **Regel voor automatisch schalen (omhoog schalen)** |
| **Resource:** Front-end-pool |
| **Metriek:** VERBRUIK |
| **Bewerking:** Meer dan 60% |
| **Duur:** 20 minuten |
| **Tijd aggregatie:** Evenredig |
| **Actie:** Aantal verhogen met 3 |
| **Afkoelen (minuten):** 120 |
|  |
| **Regel voor automatisch schalen (omlaag schalen)** |
| **Resource:** Werknemers groep 1 |
| **Metriek:** VERBRUIK |
| **Bewerking:** Minder dan 30% |
| **Duur:** 20 minuten |
| **Tijd aggregatie:** Evenredig |
| **Actie:** Aantal verlagen met 3 |
| **Afkoelen (minuten):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
