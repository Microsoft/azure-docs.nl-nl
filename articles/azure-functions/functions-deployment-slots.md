---
title: Implementatie sleuven Azure Functions
description: Meer informatie over het maken en gebruiken van implementatie sleuven met Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 2dbf7c31e6b0b40fa9dc2d59e86c0ecc731657e1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172406"
---
# <a name="azure-functions-deployment-slots"></a>Implementatie sleuven Azure Functions

Met Azure Functions implementatie sleuven kan uw functie-app verschillende instanties met de naam ' sleuven ' uitvoeren. Sleuven zijn verschillende omgevingen die worden weer gegeven via een openbaar beschikbaar eind punt. Een app-exemplaar wordt altijd toegewezen aan de productie sleuf en u kunt instanties die zijn toegewezen aan een sleuf op aanvraag, vervangen. Functie-apps die onder het service plan apps worden uitgevoerd, kunnen meerdere sleuven hebben, terwijl het verbruiks abonnement slechts één sleuf is toegestaan.

Hieronder ziet u hoe de functies worden beïnvloed door de wisselende sleuven:

- Het omleiden van verkeer is naadloos; Er worden geen aanvragen verwijderd vanwege een wissel.
- Als tijdens een swap een functie wordt uitgevoerd, wordt de uitvoering voortgezet en worden de volgende triggers doorgestuurd naar het geswapde app-exemplaar.

> [!NOTE]
> Er zijn momenteel geen sleuven beschikbaar voor het verbruiks abonnement voor Linux.

## <a name="why-use-slots"></a>Waarom sleuven gebruiken?

Er zijn een aantal voor delen voor het gebruik van implementatie sites. In de volgende scenario's worden veelvoorkomende toepassingen voor sleuven beschreven:

- **Verschillende omgevingen voor verschillende doel einden**: het gebruik van verschillende sleuven biedt u de mogelijkheid om app-exemplaren te onderscheiden voordat u naar de productie-of staging-sleuf overgaat.
- Voor bereiding: als u in plaats van rechtstreeks naar productie een sleuf implementeert, kan de app worden **opgewarmd** voordat u live gaat. Daarnaast vermindert het gebruik van sleuven de latentie voor HTTP-geactiveerde workloads. Instanties worden vóór de implementatie opwarmd, waardoor het koud starten voor nieuwe geïmplementeerde functies wordt verminderd.
- **Eenvoudige terugvals**: na een swap met productie heeft de sleuf met een eerder gefaseerde app nu de vorige productie-app. Als de wijzigingen die zijn gewisseld naar de productie sleuf, niet naar verwachting zijn, kunt u de wisseling onmiddellijk omkeren om de laatste bekende juiste instantie terug te halen.

## <a name="swap-operations"></a>Swap bewerkingen

Tijdens een swap wordt één sleuf beschouwd als de bron en het andere doel. De bron sleuf heeft het exemplaar van de toepassing die wordt toegepast op de doel site. De volgende stappen zorgen ervoor dat de doel sleuf geen downtime ondervindt tijdens een wissel:

1. **Instellingen Toep assen:** Instellingen van de doel sleuf worden toegepast op alle exemplaren van de bron sleuf. De productie-instellingen worden bijvoorbeeld toegepast op het staging-exemplaar. De toegepaste instellingen zijn onder andere de volgende categorieën:
    - [Sleuf-specifieke app-](#manage-settings) instellingen en verbindings reeksen (indien van toepassing)
    - Instellingen voor [continue implementatie](../app-service/deploy-continuous-deployment.md) (indien ingeschakeld)
    - [Verificatie](../app-service/overview-authentication-authorization.md) -instellingen app service (indien ingeschakeld)

1. **Wachten op opnieuw opstarten en beschik baarheid:** De wissel wordt gewacht op elke instantie in de bron sleuf om de computer opnieuw op te starten en voor aanvragen beschikbaar te stellen. Als een exemplaar niet opnieuw wordt opgestart, worden alle wijzigingen in de bron sleuf door de wissel bewerking hersteld en wordt de bewerking gestopt.

1. **Route ring bijwerken:** Als alle exemplaren op de bron sleuf zijn opgewarmd, wordt de wisseling door de twee sleuven voltooid door middel van routerings regels te scha kelen. Na deze stap heeft de doel sleuf (bijvoorbeeld het productie gebied) de app die eerder is opgewarmd in de bron sleuf.

1. **Herhaal bewerking:** Nu de bron sleuf de preswap-app eerder in de doel sleuf heeft, voltooit u dezelfde bewerking door alle instellingen toe te passen en de instanties voor de bron sleuf opnieuw te starten.

Houd rekening met de volgende belangrijke punten:

- Op een wille keurig punt van de wissel bewerking wordt de initialisatie van de verwisselde apps uitgevoerd op de bron site. De doel sleuf blijft online terwijl de bron sleuf is voor bereid, of de wisseling slaagt of mislukt.

- Als u een faserings sleuf wilt vervangen door de productie sleuf, moet u ervoor zorgen dat de productie sleuf *altijd* de doel sleuf is. Op deze manier heeft de swap bewerking geen invloed op uw productie-app.

- Instellingen die betrekking hebben op gebeurtenis bronnen en bindingen, moeten worden geconfigureerd als [implementatie site-instellingen](#manage-settings) *voordat u een wissel start*. Als u deze voor het eerst markeert, zorgt u ervoor dat gebeurtenissen en uitvoer naar de juiste instantie worden gestuurd.

## <a name="manage-settings"></a>Instellingen beheren

Sommige configuratie-instellingen zijn specifiek voor een bepaalde sleuf. De volgende lijst geeft een overzicht van de instellingen die veranderen wanneer u sleuven verwisselt en die hetzelfde blijven.

**Instellingen voor een specifieke sleuf**:

* Eind punten publiceren
* Aangepaste domeinnamen
* Niet-open bare certificaten en TLS/SSL-instellingen
* Schaal instellingen
* Webjobs-planners
* IP-beperkingen
* AlwaysOn
* Diagnostische instellingen
* CORS (Cross-Origin Resource Sharing, cross-origin-resource delen)

**Niet-sleuf-specifieke instellingen**:

* Algemene instellingen, zoals Framework versie, 32/64 bits, Web Sockets
* App-instellingen (kan worden geconfigureerd om naar een sleuf te worden gestickt)
* Verbindings reeksen (kan zodanig worden geconfigureerd dat ze naar een sleuf worden gestickt)
* Handlertoewijzing
* Openbare certificaten
* Inhoud van webjobs
* Hybride verbindingen *
* Integratie van virtueel netwerk *
* Service-eind punten *
* Azure Content Delivery Network *

Functies die zijn gemarkeerd met een sterretje (*) zijn gepland om ongewisseld te worden. 

> [!NOTE]
> Bepaalde app-instellingen die van toepassing zijn op niet-Verwissel bare instellingen, worden ook niet omgewisseld. Omdat de diagnostische instellingen bijvoorbeeld niet worden gewisseld, worden gerelateerde app-instellingen, zoals `WEBSITE_HTTPLOGGING_RETENTION_DAYS` en `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` ook niet omgewisseld, zelfs niet weer gegeven als sleuf instellingen.
>

### <a name="create-a-deployment-setting"></a>Een implementatie-instelling maken

U kunt instellingen markeren als een implementatie-instelling, waardoor het ' Plak ' is. Een plak instelling wordt niet verwisseld met het app-exemplaar.

Als u een implementatie-instelling in één sleuf maakt, moet u ervoor zorgen dat u dezelfde instelling maakt met een unieke waarde in een andere sleuf die betrokken is bij een wissel. Op die manier blijft de naam van de instelling consistent tussen sleuven, terwijl de waarde van een instelling niet verandert. Deze naam consistentie zorgt ervoor dat uw code geen toegang probeert te krijgen tot een instelling die in een sleuf is gedefinieerd, maar niet een andere.

Gebruik de volgende stappen om een implementatie-instelling te maken:

1. Navigeer naar **implementatie sleuven** in de functie-app en selecteer vervolgens de naam van de sleuf.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Zoek naar sleuven in de Azure Portal." border="true":::

1. Selecteer **configuratie** en selecteer vervolgens de naam van de instelling die u met de huidige sleuf wilt houden.

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Configureer de toepassings instelling voor een sleuf in het Azure Portal." border="true":::

1. Selecteer **implementatie site-instelling** en selecteer vervolgens **OK**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="Configureer de implementatie site-instelling." border="true":::

1. Zodra de sectie is ingesteld op verdwijnt, selecteert u **Opslaan** om de wijzigingen te bewaren

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="Sla de implementatie site-instelling op." border="true":::

## <a name="deployment"></a>Implementatie

Sleuven zijn leeg wanneer u een sleuf maakt. U kunt een van de [ondersteunde implementatie technologieën](./functions-deployment-technologies.md) gebruiken om uw toepassing in een sleuf te implementeren.

## <a name="scaling"></a>Schalen

Alle sleuven worden geschaald naar hetzelfde aantal werk rollen als de productie site.

- Voor verbruiks abonnementen wordt de sleuf geschaald als de functie-app wordt geschaald.
- Voor App Service plannen wordt de app geschaald naar een vast aantal werk rollen. Sleuven worden uitgevoerd op hetzelfde aantal werk rollen als het app-plan.

## <a name="add-a-slot"></a>Een sleuf toevoegen

U kunt een sleuf toevoegen via de [cli](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create) of via de portal. De volgende stappen laten zien hoe u een nieuwe sleuf maakt in de portal:

1. Navigeer naar uw functie-app.

1. Selecteer **implementatie sleuven** en selecteer vervolgens **+ sleuf toevoegen**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Azure Functions implementatie site toevoegen." border="true":::

1. Typ de naam van de sleuf en selecteer **toevoegen**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Noem de Azure Functions implementatie sleuf." border="true":::

## <a name="swap-slots"></a>Wisselings sleuven

U kunt sleuven wisselen via de [cli](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap) of via de portal. De volgende stappen laten zien hoe u sleuven in de portal kunt wisselen:

1. Navigeer naar de functie-app.
1. Selecteer **implementatie sleuven** en selecteer vervolgens **wisselen**.

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="Scherm opname van de pagina implementatie site waarop de actie ' sleuf toevoegen ' is geselecteerd." border="true":::

1. Controleer de configuratie-instellingen voor de wisseling en selecteer **wisselen**
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="De implementatie site wisselen." border="true":::

De bewerking kan even duren wanneer de wissel bewerking wordt uitgevoerd.

## <a name="roll-back-a-swap"></a>Een swap terugdraaien

Als een wissel resulteert in een fout of als u simpelweg een wissel wilt herstellen, kunt u teruggaan naar de begin status. Als u wilt terugkeren naar de voorgewisselde status, voert u een andere swap uit om de wisseling om te keren.

## <a name="remove-a-slot"></a>Een sleuf verwijderen

U kunt een sleuf verwijderen via de [cli](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete) of via de portal. De volgende stappen laten zien hoe u een sleuf in de portal kunt verwijderen:

1. Navigeer naar **implementatie sleuven** in de functie-app en selecteer vervolgens de naam van de sleuf.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Zoek naar sleuven in de Azure Portal." border="true":::

1. Selecteer **Verwijderen**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="Scherm opname waarin de pagina overzicht wordt weer gegeven waarop de actie verwijderen is geselecteerd." border="true":::

1. Typ de naam van de implementatie site die u wilt verwijderen en selecteer vervolgens **verwijderen**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Verwijder de implementatie site in de Azure Portal." border="true":::

1. Sluit het bevestigings venster voor verwijdering.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="Bevestiging implementatie sleuf verwijderen." border="true":::

## <a name="automate-slot-management"></a>Sleuf beheer automatiseren

Met de [Azure cli](/cli/azure/functionapp/deployment/slot)kunt u de volgende acties voor een sleuf automatiseren:

- [creëren](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create)
- [verwijderen](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete)
- [list](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-list)
- [ruil](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap)
- [automatisch wisselen](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>App Service plan wijzigen

Met een functie-app die wordt uitgevoerd onder een App Service-abonnement, kunt u het onderliggende App Service plan voor een sleuf wijzigen.

> [!NOTE]
> U kunt het App Service plan van een sleuf onder het verbruiks abonnement niet wijzigen.

Gebruik de volgende stappen om het App Service plan van een sleuf te wijzigen:

1. Navigeer naar **implementatie sleuven** in de functie-app en selecteer vervolgens de naam van de sleuf.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Zoek naar sleuven in de Azure Portal." border="true":::

1. Selecteer onder **app service plan** **wijzigen app service plan**.

1. Selecteer het abonnement dat u wilt bijwerken of maak een nieuw abonnement.

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Wijzig het App Service plan in de Azure Portal." border="true":::

1. Selecteer **OK**.

## <a name="limitations"></a>Beperkingen

Azure Functions implementatie sleuven hebben de volgende beperkingen:

- Het aantal beschik bare sleuven voor een app is afhankelijk van het plan. Het verbruiks abonnement is slechts één implementatie sleuf toegestaan. Er zijn extra sleuven beschikbaar voor apps die worden uitgevoerd onder het App Service-abonnement.
- Als u een sleuf verwisselt, worden sleutels voor apps waarvan de `AzureWebJobsSecretStorageType` app-instelling gelijk is aan ingesteld `files` .
- Sleuven zijn niet beschikbaar voor het verbruiks abonnement voor Linux.

## <a name="support-levels"></a>Ondersteunings niveaus

Er zijn twee ondersteunings niveaus voor implementatie sites:

- **Algemene Beschik baarheid (ga)**: volledig ondersteund en goedgekeurd voor productie gebruik.
- **Preview**: nog niet ondersteund, maar er wordt naar verwachting de Ga-status in de toekomst bereikt.

| Besturings systeem/hosting abonnement           | Ondersteunings niveau     |
| ------------------------- | -------------------- |
| Windows-verbruik       | Algemene beschikbaarheid |
| Windows Premium           | Algemene beschikbaarheid  |
| Windows toegewezen         | Algemene beschikbaarheid |
| Linux-verbruik         | Niet ondersteund          |
| Linux Premium             | Algemene beschikbaarheid  |
| Speciaal voor Linux           | Algemene beschikbaarheid |

## <a name="next-steps"></a>Volgende stappen

- [Implementatie technologieën in Azure Functions](./functions-deployment-technologies.md)
