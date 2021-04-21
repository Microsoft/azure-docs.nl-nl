---
title: Azure Functions implementatiesleuven
description: Meer informatie over het maken en gebruiken van implementatiesleuven met Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 5f003b68283217f7877dc650ae4f07ddc5a31012
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789335"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions implementatiesleuven

Azure Functions implementatiesleuven kunt u uw functie-app verschillende exemplaren met de naam 'sleuven' laten uitvoeren. Sleuven zijn verschillende omgevingen die beschikbaar zijn via een openbaar beschikbaar eindpunt. Er wordt altijd één app-exemplaar toegewezen aan de productiesleuf en u kunt instanties die zijn toegewezen aan een sleuf op aanvraag wisselen. Functie-apps die worden uitgevoerd onder het App Service-plan kunnen meerdere sleuven hebben, terwijl onder het verbruiksplan slechts één sleuf is toegestaan.

Hieronder wordt weergegeven hoe functies worden beïnvloed door het wisselen van sleuven:

- Verkeersomleiding is naadloos; er worden geen aanvragen vanwege een wissel ingetrokken.
- Als een functie wordt uitgevoerd tijdens een wissel, wordt de uitvoering voortgezet en worden de volgende triggers doorgeleid naar het gewisselde app-exemplaar.

## <a name="why-use-slots"></a>Waarom sleuven gebruiken?

Het gebruik van implementatiesleuven biedt een aantal voordelen. In de volgende scenario's worden veelvoorkomende toepassingen voor sleuven beschreven:

- **Verschillende omgevingen voor verschillende doeleinden:** Als u verschillende sleuven gebruikt, hebt u de mogelijkheid om onderscheid te maken tussen app-exemplaren voordat u overwisselt naar productie of een staging-sleuf.
- **Voorverwarmen:** als u implementeert in een sleuf in plaats van rechtstreeks naar productie, kan de app worden opgewarmd voordat deze live gaat. Daarnaast vermindert het gebruik van sleuven de latentie voor door HTTP geactiveerde workloads. Instanties worden opgewarmd vóór de implementatie, waardoor de koude start voor nieuw geïmplementeerde functies wordt beperkt.
- **Eenvoudige terugval: Na** een wissel met productie heeft de sleuf met een eerder gefaseerd app nu de vorige productie-app. Als de wijzigingen die in de productiesleuf zijn gewisseld niet zijn zoals verwacht, kunt u de wisseling onmiddellijk omkeren om uw 'laatst bekende goede exemplaar' terug te krijgen.

## <a name="swap-operations"></a>Wisselbewerkingen

Tijdens een wissel wordt één sleuf beschouwd als de bron en de andere als doel. De bronsleuf heeft het exemplaar van de toepassing dat wordt toegepast op de doelsleuf. De volgende stappen zorgen ervoor dat de doelsleuf geen downtime ervaart tijdens een wisseling:

1. **Instellingen toepassen:** Instellingen van de doelsleuf worden toegepast op alle exemplaren van de bronsleuf. De productie-instellingen worden bijvoorbeeld toegepast op het faserings-exemplaar. De toegepaste instellingen omvatten de volgende categorieën:
    - [Sitespecifieke app-instellingen](#manage-settings) en verbindingsreeksen (indien van toepassing)
    - [Instellingen voor continue](../app-service/deploy-continuous-deployment.md) implementatie (indien ingeschakeld)
    - [App Service (indien](../app-service/overview-authentication-authorization.md) ingeschakeld)

1. **Wacht op opnieuw opstarten en beschikbaarheid:** De wissel wacht tot elk exemplaar in de bronsleuf de herstart heeft voltooid en beschikbaar is voor aanvragen. Als een exemplaar niet opnieuw kan worden opgestart, worden alle wijzigingen in de bronsleuf terugdraaid en wordt de bewerking gestopt.

1. **Routering bijwerken:** Als alle exemplaren op de bronsleuf zijn opgewarmd, worden de twee sleuven voltooid door van routeringsregels te wisselen. Na deze stap heeft de doelsleuf (bijvoorbeeld de productiesleuf) de app die eerder is opgewarmd in de bronsleuf.

1. **Herhaal bewerking:** Nu de bronsleuf de app vooraf heeft gewisseld in de doelsleuf, voltooit u dezelfde bewerking door alle instellingen toe te passen en de exemplaren voor de bronsleuf opnieuw op te starten.

Houd rekening met de volgende belangrijke punten:

- Op elk moment van de wisselbewerking vindt de initialisatie van de gewisselde apps plaats op de bronsleuf. De doelsleuf blijft online terwijl de bronsleuf wordt voorbereid, ongeacht of de wissel slaagt of mislukt.

- Als u een staging-slot wilt wisselen met de productiesleuf, moet u ervoor zorgen dat de productiesleuf *altijd de* doelsleuf is. Op deze manier heeft de wisselbewerking geen invloed op uw productie-app.

- Instellingen met betrekking tot gebeurtenisbronnen en [](#manage-settings) bindingen moeten worden geconfigureerd als instellingen voor implementatiesleuf *voordat u een wissel start.* Door ze van tevoren als 'plakkerig' te markeren, zorgt u ervoor dat gebeurtenissen en uitvoer naar de juiste instantie worden omgeleid.

## <a name="manage-settings"></a>Instellingen beheren

Sommige configuratie-instellingen zijn site-specifiek. Hieronder ziet u welke instellingen worden gewijzigd wanneer u van site wisselt en welke hetzelfde blijven.

**Sitespecifieke instellingen:**

* Eindpunten publiceren
* Aangepaste domeinnamen
* Niet-openbare certificaten en TLS/SSL-instellingen
* Schaalinstellingen
* WebJobs-schedulers
* IP-beperkingen
* AlwaysOn
* Diagnostische instellingen
* CORS (Cross-Origin Resource Sharing, cross-origin-resource delen)

**Niet-sitespecifieke instellingen:**

* Algemene instellingen, zoals frameworkversie, 32/64-bits websockers
* App-instellingen (kunnen zo worden geconfigureerd dat deze zich aan een site houden)
* Verbindingsreeksen (kunnen worden geconfigureerd om aan een sleuf te blijven)
* Handlertoewijzingen
* Openbare certificaten
* WebJobs-inhoud
* Hybride verbindingen *
* Integratie van virtuele netwerken *
* Service-eindpunten *
* Azure Content Delivery Network *

Functies die zijn gemarkeerd met een asterisk (*) zijn gepland om te worden losgeappt. 

> [!NOTE]
> Bepaalde app-instellingen die van toepassing zijn op niet-app-instellingen, worden ook niet gewisseld. Omdat diagnostische instellingen bijvoorbeeld niet worden gewisseld, worden gerelateerde app-instellingen zoals en ook niet gewisseld, zelfs niet als `WEBSITE_HTTPLOGGING_RETENTION_DAYS` `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` site-instellingen.
>

### <a name="create-a-deployment-setting"></a>Een implementatie-instelling maken

U kunt instellingen markeren als een implementatie-instelling, waardoor deze 'sticky' is. Een sticky-instelling wordt niet gewisseld met het app-exemplaar.

Als u een implementatie-instelling in één sleuf maakt, moet u dezelfde instelling maken met een unieke waarde in een andere sleuf die betrokken is bij een wisseling. Op deze manier blijven de instellingsnamen consistent tussen sleuven, hoewel de waarde van een instelling niet verandert. Deze naamconsistentie zorgt ervoor dat uw code geen toegang probeert te krijgen tot een instelling die is gedefinieerd in de ene sleuf, maar niet in een andere.

Gebruik de volgende stappen om een implementatie-instelling te maken:

1. **Navigeer naar Implementatiesleuven** in de functie-app en selecteer vervolgens de naam van de site.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Zoek sleuven in de Azure Portal." border="true":::

1. Selecteer **Configuratie** en selecteer vervolgens de naam van de instelling die u bij de huidige sleuf wilt houden.

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Configureer de toepassingsinstelling voor een sleuf in Azure Portal." border="true":::

1. Selecteer **Implementatiesleufinstelling** en selecteer vervolgens **OK.**

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="Configureer de implementatiesleufinstelling." border="true":::

1. Zodra de instellingssectie is verdwenen, selecteert **u Opslaan** om de wijzigingen te behouden

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="Sla de instelling van de implementatiesleuf op." border="true":::

## <a name="deployment"></a>Implementatie

Sleuven zijn leeg wanneer u een sleuf maakt. U kunt een van de [ondersteunde implementatietechnologieën gebruiken om](./functions-deployment-technologies.md) uw toepassing in een sleuf te implementeren.

## <a name="scaling"></a>Schalen

Alle sleuven worden geschaald naar hetzelfde aantal werksters als de productiesleuf.

- Voor Verbruiksplannen wordt de sleuf geschaald terwijl de functie-app wordt geschaald.
- Voor App Service-abonnementen wordt de app geschaald naar een vast aantal werknemers. Sleuven worden uitgevoerd op hetzelfde aantal werksters als het app-plan.

## <a name="add-a-slot"></a>Een sleuf toevoegen

U kunt een site toevoegen via de [CLI](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_create) of via de portal. In de volgende stappen wordt gedemonstreerd hoe u een nieuwe site maakt in de portal:

1. Navigeer naar uw functie-app.

1. Selecteer **Implementatiesleuven** en selecteer vervolgens **+ Sleuf toevoegen.**

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Voeg Azure Functions implementatiesleuf toe." border="true":::

1. Typ de naam van de sleuf en selecteer **Toevoegen.**

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Noem de Azure Functions implementatiesleuf." border="true":::

## <a name="swap-slots"></a>Wisselsleuven

U kunt sleuven wisselen via de [CLI](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_swap) of via de portal. In de volgende stappen wordt gedemonstreerd hoe u sleuven verwisselt in de portal:

1. Navigeer naar de functie-app.
1. Selecteer **Implementatiesleuven** en selecteer vervolgens **Wisselen.**

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="Schermopname van de pagina Implementatiesleuf met de actie Site toevoegen geselecteerd." border="true":::

1. Controleer de configuratie-instellingen voor uw wissel en selecteer **Wisselen**
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="De implementatiesleuf wisselen." border="true":::

De bewerking kan even duren terwijl de wisselbewerking wordt uitgevoerd.

## <a name="roll-back-a-swap"></a>Een wissel terugdraaien

Als een wissel resulteert in een fout of als u een wisseling gewoon ongedaan wilt maken, kunt u terugdraaien naar de begintoestand. Als u wilt terugkeren naar de vooraf gewisselde status, moet u nog een wisseling doen om de wisseling om te draaien.

## <a name="remove-a-slot"></a>Een sleuf verwijderen

U kunt een site verwijderen via de [CLI](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_delete) of via de portal. In de volgende stappen wordt gedemonstreerd hoe u een site in de portal verwijdert:

1. **Navigeer naar Implementatiesleuven** in de functie-app en selecteer vervolgens de naam van de site.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Zoek sleuven in de Azure Portal." border="true":::

1. Selecteer **Verwijderen**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="Schermopname van de pagina Overzicht met de actie Verwijderen geselecteerd." border="true":::

1. Typ de naam van de implementatiesleuf die u wilt verwijderen en selecteer **vervolgens Verwijderen.**

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Verwijder de implementatiesleuf in de Azure Portal." border="true":::

1. Sluit het bevestigingsvenster voor verwijderen.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="Bevestiging van verwijderen van implementatiesleuf." border="true":::

## <a name="automate-slot-management"></a>Sleufbeheer automatiseren

Met de [Azure CLI](/cli/azure/functionapp/deployment/slot)kunt u de volgende acties voor een sleuf automatiseren:

- [Maken](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_create)
- [verwijderen](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_delete)
- [list](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_list)
- [Swap](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_swap)
- [automatisch wisselen](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_auto_swap)

## <a name="change-app-service-plan"></a>Een App Service wijzigen

Met een functie-app die wordt uitgevoerd onder een App Service-abonnement, kunt u de onderliggende App Service voor een sleuf wijzigen.

> [!NOTE]
> U kunt het abonnement van een sleuf niet App Service onder het verbruiksplan.

Gebruik de volgende stappen om het abonnement van een sleuf App Service wijzigen:

1. **Navigeer naar Implementatiesleuven** in de functie-app en selecteer vervolgens de naam van de site.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Zoek sleuven in de Azure Portal." border="true":::

1. Selecteer **App Service abonnement** wijzigen **App Service abonnement.**

1. Selecteer het abonnement waar u een upgrade naar wilt uitvoeren of maak een nieuw plan.

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Wijzig het App Service abonnement in de Azure Portal." border="true":::

1. Selecteer **OK**.

## <a name="limitations"></a>Beperkingen

Azure Functions implementatiesleuven hebben de volgende beperkingen:

- Het aantal beschikbare sleuven voor een app is afhankelijk van het plan. Voor het verbruiksplan is slechts één implementatiesleuf toegestaan. Er zijn extra sleuven beschikbaar voor apps die worden uitgevoerd onder App Service abonnement.
- Als u een sleuf verwisselt, worden sleutels opnieuw ingesteld voor apps met `AzureWebJobsSecretStorageType` een app-instelling die gelijk is aan `files` .
- Er zijn geen sleuven beschikbaar voor het Linux-verbruik abonnement.

## <a name="support-levels"></a>Ondersteuningsniveaus

Er zijn twee ondersteuningsniveaus voor implementatiesleuven:

- **Algemene beschikbaarheid (GA)**: volledig ondersteund en goedgekeurd voor productiegebruik.
- **Preview:** Wordt nog niet ondersteund, maar wordt in de toekomst verwacht de status Ga te bereiken.

| Os/Hosting-abonnement           | Ondersteuningsniveau     |
| ------------------------- | -------------------- |
| Windows-verbruik       | Algemene beschikbaarheid |
| Windows Premium           | Algemene beschikbaarheid  |
| Windows Dedicated         | Algemene beschikbaarheid |
| Linux-verbruik         | Preview          |
| Linux Premium             | Algemene beschikbaarheid  |
| Linux Dedicated           | Algemene beschikbaarheid |

## <a name="next-steps"></a>Volgende stappen

- [Implementatietechnologieën in Azure Functions](./functions-deployment-technologies.md)
