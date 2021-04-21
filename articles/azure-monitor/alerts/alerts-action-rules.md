---
title: Actieregels voor Azure Monitor waarschuwingen
description: Inzicht in wat actieregels in Azure Monitor zijn en hoe u deze kunt configureren en beheren.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 4f54ee7d21d52386bd18921aec33cabe02046852
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772555"
---
# <a name="action-rules-preview"></a>Actieregels (preview)

Met actieregels kunt u de actiegroepen toevoegen aan of onderdrukken voor uw waarschuwingen. Eén regel kan verschillende bereiken van doelresources omvatten, bijvoorbeeld een waarschuwing voor een specifieke resource (zoals een specifieke virtuele machine) of een waarschuwing die wordt gemaakt voor een resource in een abonnement. U kunt eventueel verschillende filters toevoegen om te bepalen welke waarschuwingen onder een regel vallen en een planning voor de waarschuwing definiëren, bijvoorbeeld om deze alleen buiten bedrijfsuren of tijdens een gepland onderhoudsvenster van kracht te laten zijn.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>Waarom en wanneer moet u actieregels gebruiken?

### <a name="suppression-of-alerts"></a>Onderdrukking van waarschuwingen

Er zijn veel scenario's waarin het handig is om de meldingen te onderdrukken die waarschuwingen genereren. Deze scenario's variëren van onderdrukking tijdens een gepland onderhoudsvenster tot onderdrukking tijdens niet-bedrijfsuren. Het team dat verantwoordelijk is  **voor ContosoVM** wil bijvoorbeeld waarschuwingsmeldingen onderdrukken voor het komende weekend, omdat **ContosoVM** gepland onderhoud ondergaat.

Hoewel het team elke waarschuwingsregel kan uitschakelen die handmatig op **ContosoVM** is geconfigureerd (en deze na onderhoud opnieuw kan inschakelen), is het geen eenvoudig proces. Met actieregels kunt u waarschuwingsonderdrukking op schaal definiëren met de mogelijkheid om flexibel de onderdrukkingsperiode te configureren. In het vorige voorbeeld kan het team één actieregel definiëren op **ContosoVM** die alle waarschuwingsmeldingen voor het weekend onderdrukt.

### <a name="actions-at-scale"></a>Acties op schaal

Hoewel waarschuwingsregels u helpen bij het definiëren van de actiegroep die wordt uitgevoerd wanneer de waarschuwing wordt gegenereerd, hebben klanten vaak een gemeenschappelijke actiegroep binnen hun bereik van bewerkingen. Een team dat verantwoordelijk is voor de resourcegroep **ContosoRG** definieert bijvoorbeeld waarschijnlijk dezelfde actiegroep voor alle waarschuwingsregels die zijn gedefinieerd in **ContosoRG.**

Actieregels helpen u dit proces te vereenvoudigen. Door acties op schaal te definiëren, kan een actiegroep worden geactiveerd voor elke waarschuwing die wordt gegenereerd voor het geconfigureerde bereik. In het vorige voorbeeld kan het team één actieregel definiëren in **ContosoRG** die dezelfde actiegroep activeert voor alle waarschuwingen die er in worden gegenereerd.

> [!NOTE]
> Actieregels zijn niet van toepassing op Azure Service Health waarschuwingen.

## <a name="configuring-an-action-rule"></a>Een actieregel configureren

### <a name="portal"></a>[Portal](#tab/portal)

U kunt de functie openen door Acties **beheren te selecteren** op de **landingspagina** Waarschuwingen in Azure Monitor. Selecteer vervolgens **Actieregels (preview)**. U kunt de regels openen door Actieregels **(preview) te selecteren** in het dashboard van de landingspagina voor waarschuwingen.

![Actieregels vanaf de Azure Monitor landingspagina](media/alerts-action-rules/action-rules-landing-page.png)

Selecteer **+ Nieuwe actieregel.**

![Schermopname van de pagina Acties beheren met de knop Nieuwe actieregel gemarkeerd.](media/alerts-action-rules/action-rules-new-rule.png)

U kunt ook een actieregel maken terwijl u een waarschuwingsregel configureert.

![Schermopname van de pagina Regel maken met de knop Actieregel maken gemarkeerd.](media/alerts-action-rules/action-rules-alert-rule.png)

U ziet nu de stroompagina voor het maken van actieregels. Configureer de volgende elementen:

![Stroom voor het maken van nieuwe actieregel](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Bereik

Kies eerst het bereik (Azure-abonnement, resourcegroep of doelresource). U kunt ook een combinatie van scopes binnen één abonnement meervoudig selecteren.

![Bereik van actieregel](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Filtercriteria

U kunt eventueel filters definiëren, zodat de regel van toepassing is op een specifieke subset van de waarschuwingen of op specifieke gebeurtenissen voor elke waarschuwing (bijvoorbeeld alleen 'Fired' of alleen 'Resolved').

De beschikbare filters zijn:

* **Ernst**  
Deze regel is alleen van toepassing op waarschuwingen met de geselecteerde ernst.  
Ernst = **'Sev1'** betekent bijvoorbeeld dat de regel alleen van toepassing is op waarschuwingen met ernst Sev1.
* **Service bewaken**  
Deze regel is alleen van toepassing op waarschuwingen die afkomstig zijn van de geselecteerde bewakingsservices.  
Zo betekent **service bewaken = 'Azure Backup'** dat de regel alleen van toepassing is op back-upwaarschuwingen (afkomstig van Azure Backup).
* **Resourcetype**  
Deze regel is alleen van toepassing op waarschuwingen voor de geselecteerde resourcetypen.  
Resourcetype **= 'Virtual Machines'** betekent bijvoorbeeld dat de regel alleen van toepassing is op waarschuwingen op virtuele machines.
* **Waarschuwingsregel-id**  
Deze regel is alleen van toepassing op waarschuwingen die afkomstig zijn van een specifieke waarschuwingsregel. De waarde moet de Resource Manager id van de waarschuwingsregel zijn.  
**Waarschuwingsregel-id = "/subscriptions/SubId1/resourceGroups/RG1/providers/microsoft.insights/metricalerts/API-Latency"** betekent dat deze regel alleen van toepassing is op waarschuwingen die afkomstig zijn van de metrische waarschuwingsregel API-Latency.  
_OPMERKING: u kunt de juiste waarschuwingsregel-id krijgen door de waarschuwingsregels van de CLI weer te geven of door een specifieke waarschuwingsregel te openen in de portal, op Eigenschappen te klikken en de waarde resource-id te kopiëren._
* **Voorwaarde bewaken**  
Deze regel is alleen van toepassing op waarschuwingsgebeurtenissen met de opgegeven monitorvoorwaarde : **'Fired'** of **'Resolved'.**
* **Beschrijving**  
Deze regel is alleen van toepassing op waarschuwingen die een specifieke tekenreeks in het veld beschrijving van de waarschuwing bevatten. Dat veld bevat de beschrijving van de waarschuwingsregel.  
Beschrijving bevat **bijvoorbeeld 'prod'** betekent dat de regel alleen komt overeen met waarschuwingen die de tekenreeks 'prod' in hun beschrijving bevatten.
* **Waarschuwingscontext (nettolading)**  
Deze regel is alleen van toepassing op waarschuwingen die een of meer specifieke waarden in de contextvelden van de waarschuwing bevatten.  
Waarschuwingscontext (nettolading) bevat bijvoorbeeld **'Computer-01'** betekent dat de regel alleen van toepassing is op waarschuwingen waarvan de nettolading de tekenreeks 'Computer-01' bevat.

> [!NOTE]
> Elk filter kan maximaal vijf waarden bevatten.  
> Een filter op de monitorservice kan bijvoorbeeld maximaal vijf monitorservicenamen bevatten.




Als u meerdere filters in een regel in stelt, zijn deze allemaal van toepassing. Als u bijvoorbeeld **resourcetype = "Virtual Machines"** en **ernst = "Sev0"** in stelt, is de regel alleen van toepassing op Sev0-waarschuwingen op virtuele machines.

![Filters voor actieregel](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Onderdrukking of configuratie van actiegroep

Configureer vervolgens de actieregel voor waarschuwingsonderdrukking of ondersteuning voor actiegroep. U kunt niet beide kiezen. De configuratie fungeert op alle waarschuwings-exemplaren die overeenkomen met het eerder gedefinieerde bereik en de eerder gedefinieerde filters.

#### <a name="suppression"></a>Onderdrukking

Als u onderdrukking **selecteert,** configureert u de duur voor het onderdrukken van acties en meldingen. Kies een van de volgende opties:
* **Vanaf nu (altijd)**: alle meldingen worden voor onbepaalde tijd onderdrukt.
* **Op een gepland tijdstip:** onderdrukt meldingen binnen een gebonden duur.
* **Met een terugkeerpatroon:** onderdrukt meldingen volgens een terugkerend dagelijks, wekelijks of maandelijks schema.

![Onderdrukking van actieregel](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Actiegroep

Als u **Actiegroep selecteert** in de schakelknop, voegt u een bestaande actiegroep toe of maakt u een nieuwe.

> [!NOTE]
> U kunt slechts één actiegroep koppelen aan een actieregel.

![Nieuwe actieregel toevoegen of maken door Actiegroep te selecteren](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Details van actieregel

Configureer ten laatste de volgende details voor de actieregel:
* Name
* Resourcegroep waarin deze wordt opgeslagen
* Description

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt actieregels maken met de Azure CLI met behulp van [de opdracht az monitor action-rule create.](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create)  De `az monitor action-rule` verwijzing is slechts een van de vele Azure [CLI-verwijzingen voor Azure Monitor](/cli/azure/azure-cli-reference-for-monitor).

### <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

1. [Azure CLI installeren](/cli/azure/install-azure-cli)

   Als u dat liever wilt, kunt u ook Azure Cloud Shell om de stappen in dit artikel uit te voeren.  Azure Cloud Shell is een interactieve shell-omgeving die u via uw browser gebruikt.  Begin Cloud Shell met behulp van een van deze methoden:

   - Open Cloud Shell door naar te gaan [https://shell.azure.com](https://shell.azure.com)

   - Selecteer de **Cloud Shell** in de menubalk in de rechterbovenhoek van de [Azure Portal](https://portal.azure.com)

1. Meld u aan.

   Als u een lokale installatie van de CLI gebruikt, meld u zich dan aan met de [opdracht az login.](/cli/azure/reference-index#az_login)  Volg de weergegeven stappen in uw terminal om het verificatieproces te voltooien.

    ```azurecli
    az login
    ```

1. De extensie `alertsmanagement` installeren

   De `az monitor action-rule` opdracht is een experimentele extensie van de Basis-Azure CLI. Meer informatie over extensieverwijzingen in [Extensie gebruiken met Azure CLI](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name alertsmanagement
   ```

   De volgende waarschuwing wordt verwacht.

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-action-rules-with-the-azure-cli"></a>Actieregels maken met de Azure CLI

Zie de Azure CLI-referentie-inhoud [voor az monitor action-rule create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) voor meer informatie over vereiste en optionele parameters.

Maak een actieregel om meldingen in een resourcegroep te onderdrukken.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --scope-type ResourceGroup \
                              --scope /subscriptions/0b1f6471-1bf0-4dda-aec3-cb9272f09590/resourceGroups/MyResourceGroupName \
                              --suppression-recurrence-type Always \
                              --alert-context Contains Computer-01 \
                               --monitor-service Equals "Log Analytics"
```

Maak elk weekend een actieregel om meldingen te onderdrukken voor alle Sev4-waarschuwingen op alle VM's binnen het abonnement.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --severity Equals Sev4 \
                              --target-resource-type Equals Microsoft.Compute/VirtualMachines \
                              --suppression-recurrence-type Weekly \
                              --suppression-recurrence 0 6 \
                              --suppression-start-date 12/09/2018 \
                              --suppression-end-date 12/18/2018 \
                              --suppression-start-time 06:00:00 \
                              --suppression-end-time 14:00:00

```

* * *

## <a name="example-scenarios"></a>Voorbeeldscenario's

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenario 1: Onderdrukking van waarschuwingen op basis van ernst

Contoso wil meldingen onderdrukken voor alle Sev4-waarschuwingen op alle VM's binnen het abonnement **ContosoSub** elk weekend.

**Oplossing:** Maak een actieregel met:
* Bereik = **ContosoSub**
* Filters
    * Ernst = **Sev4**
    * Resourcetype = **Virtual Machines**
* Onderdrukking met terugkeerpatroon ingesteld op wekelijks en **zaterdag** en **zondag** gecontroleerd

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenario 2: Onderdrukking van waarschuwingen op basis van waarschuwingscontext (nettolading)

Contoso wil meldingen onderdrukken voor alle logboekwaarschuwingen die zijn gegenereerd voor **Computer-01** in **ContosoSub,** voor onbepaalde tijd tijdens onderhoud.

**Oplossing:** Maak een actieregel met:
* Bereik = **ContosoSub**
* Filters
    * Service bewaken = **Log Analytics**
    * Waarschuwingscontext (nettolading) **bevat Computer-01**
* Onderdrukking ingesteld op **Vanaf nu (altijd)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenario 3: Actiegroep die is gedefinieerd in een resourcegroep

Contoso heeft een [waarschuwing voor metrische gegevens gedefinieerd op abonnementsniveau.](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) Maar het wil de acties definiëren die specifiek worden triggeren voor waarschuwingen die zijn gegenereerd vanuit de resourcegroep **ContosoRG**.

**Oplossing:** Maak een actieregel met:
* Bereik = **ContosoRG**
* Geen filters
* Actiegroep ingesteld op **ContosoActionGroup**

> [!NOTE]
> *Actiegroepen die zijn gedefinieerd in actieregels en waarschuwingsregels werken onafhankelijk, zonder ontdubbeling.* Als in het eerder beschreven scenario een actiegroep is gedefinieerd voor de waarschuwingsregel, wordt deze triggers in combinatie met de actiegroep die is gedefinieerd in de actieregel.

## <a name="managing-your-action-rules"></a>Uw actieregels beheren

### <a name="portal"></a>[Portal](#tab/portal)

U kunt uw actieregels weergeven en beheren vanuit de lijstweergave:

![Lijstweergave met actieregels](media/alerts-action-rules/action-rules-list-view.png)

Hier kunt u actieregels op schaal inschakelen, uitschakelen of verwijderen door het selectievakje er naast in te schakelen. Wanneer u een actieregel selecteert, wordt de configuratiepagina geopend. De pagina helpt u bij het bijwerken van de definitie van de actieregel en het in- of uitschakelen ervan.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt uw actieregels weergeven en beheren met de [opdracht az monitor action-rule](/cli/azure/ext/alertsmanagement/monitor) van de Azure CLI.

Voordat u actieregels beheert met de Azure CLI, moet u uw omgeving voorbereiden met behulp van de instructies in [Een actieregel configureren.](#configuring-an-action-rule)

```azurecli
# List all action rules for a subscription
az monitor action-rule list

# Get details of an action rule
az monitor action-rule show --resource-group MyResourceGroupName --name MyActionRuleName

# Update an action rule.
az monitor action-rule update --resource-group MyResourceGroupName --name MyActionRuleName --status Disabled

# Delete an action rule.
az monitor action-rule delete --resource-group MyResourceGroupName --name MyActionRuleName
```

* * *

## <a name="best-practices"></a>Aanbevolen procedures

Logboekwaarschuwingen die u [](./alerts-unified-log.md) maakt met de optie aantal resultaten genereren één waarschuwings exemplaar met behulp van het hele zoekresultaat (dat meerdere computers kan bespannen). Als in dit scenario een actieregel gebruikmaakt van het filter Waarschuwingscontext **(nettolading),** wordt er actie ondernomen op het waarschuwings-exemplaar zolang er een overeenkomst is. In scenario 2, dat eerder is beschreven, wordt de volledige melding onderdrukt als de zoekresultaten voor de gegenereerde logboekwaarschuwing zowel **Computer-01** als **Computer-02** bevatten. Er wordt helemaal geen melding gegenereerd voor **Computer-02.**

![Diagram met de actieregels en logboekwaarschuwingen met één waarschuwings exemplaar gemarkeerd.](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Als u logboekwaarschuwingen het beste wilt gebruiken met actieregels, maakt u logboekwaarschuwingen met de [optie voor het meten van metrische](./alerts-unified-log.md) gegevens. Met deze optie worden afzonderlijke waarschuwings exemplaren gegenereerd op basis van het gedefinieerde groepsveld. Vervolgens worden in scenario 2 afzonderlijke waarschuwings exemplaren gegenereerd voor **Computer-01** en **Computer-02.** Vanwege de actieregel die in het scenario wordt beschreven, wordt alleen de melding voor **Computer-01** onderdrukt. De melding voor **Computer-02** blijft gewoon doorgaan.

![Actieregels en logboekwaarschuwingen (aantal resultaten)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Veelgestelde vragen

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Tijdens het configureren van een actieregel wil ik alle mogelijke overlappende actieregels zien, zodat ik dubbele meldingen vermijd. Is het mogelijk om dat te doen?

Nadat u een bereik hebt definieert terwijl u een actieregel configureert, ziet u een lijst met actieregels die overlappen binnen hetzelfde bereik (indien van toepassing). Deze overlapping kan een van de volgende opties zijn:

* Een exacte overeenkomst: De actieregel die u definieren en de overlappende actieregel staan bijvoorbeeld in hetzelfde abonnement.
* Een subset: De actieregel die u definieren, is bijvoorbeeld in een abonnement en de overlappende actieregel is van een resourcegroep binnen het abonnement.
* Een superset: De actieregel die u definieren, is bijvoorbeeld in een resourcegroep en de overlappende actieregel voor het abonnement dat de resourcegroep bevat.
* Een snijpunt: de actieregel die u definieren, is bijvoorbeeld op **VM1** en **VM2** en de overlappende actieregel op **VM2** en **VM3.**

![Schermopname van de pagina Nieuwe actieregel met de overlappende actieregels die worden weergegeven in de actieregels die zijn gedefinieerd in hetzelfde bereikvenster.](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Is het mogelijk om tijdens het configureren van een waarschuwingsregel te weten of er al actieregels zijn gedefinieerd die kunnen reageren op de waarschuwingsregel die ik definieert?

Nadat u de doelresource voor de waarschuwingsregel hebt ingesteld, kunt u de lijst met actieregels zien die op hetzelfde bereik reageren (indien van toepassing) door **Geconfigureerde** acties weergeven te selecteren onder de **sectie** Acties. Deze lijst wordt ingevuld op basis van de volgende scenario's voor het bereik:

* Een exacte overeenkomst: Bijvoorbeeld de waarschuwingsregel die u definieerde en de actieregel zich in hetzelfde abonnement.
* Een subset: De waarschuwingsregel die u definieerde, is bijvoorbeeld in een abonnement en de actieregel is van een resourcegroep binnen het abonnement.
* Een superset: De waarschuwingsregel die u definieerde, staat bijvoorbeeld in een resourcegroep en de actieregel staat in het abonnement dat de resourcegroep bevat.
* Een snijpunt: de waarschuwingsregel die u definieerde, is bijvoorbeeld op **VM1** en **VM2** en de actieregel op **VM2** en **VM3.**

![Overlappende actieregels](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Kan ik de waarschuwingen zien die zijn onderdrukt door een actieregel?

Op de [lijst met waarschuwingen kunt](./alerts-managing-alert-instances.md)u een extra kolom met de naam **Onderdrukkingsstatus kiezen.** Als de melding voor een waarschuwings-exemplaar wordt onderdrukt, wordt die status weergegeven in de lijst.

![Onderdrukte waarschuwings instances](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Wat gebeurt er als er een actieregel is met een actiegroep en een andere met onderdrukking actief in hetzelfde bereik?

Onderdrukking heeft altijd voorrang op hetzelfde bereik.

### <a name="what-happens-if-i-have-a-resource-that-is-covered-by-two-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Wat gebeurt er als ik een resource heb die wordt gedekt door twee actieregels? Krijg ik een of twee meldingen? Bijvoorbeeld **VM2** in het volgende scenario:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with action group AG1`

Voor elke waarschuwing op VM1 en VM3 wordt actiegroep AG1 één keer geactiveerd. Voor elke waarschuwing op **VM2** wordt actiegroep AG1 twee keer geactiveerd, omdat actieregels acties niet ontdubbelen.

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Wat gebeurt er als ik een resource heb die wordt bewaakt in twee afzonderlijke actieregels en een roept om actie aan terwijl een andere om te onderdrukken? Bijvoorbeeld **VM2** in het volgende scenario:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with suppression`

Voor elke waarschuwing op VM1 wordt actiegroep AG1 één keer geactiveerd. Acties en meldingen voor elke waarschuwing op VM2 en VM3 worden onderdrukt.

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Wat gebeurt er als ik een waarschuwingsregel en een actieregel heb gedefinieerd voor dezelfde resource die verschillende actiegroepen aanroept? Bijvoorbeeld **VM1** in het volgende scenario:

   `alert rule rule1 on VM1 with action group AG2`

   `action rule AR1 defined for VM1 with action group AG1`

Voor elke waarschuwing op VM1 wordt actiegroep AG1 één keer geactiveerd. Wanneer de waarschuwingsregel 'rule1' wordt geactiveerd, wordt ook AG2 geactiveerd. Actiegroepen die zijn gedefinieerd in actieregels en waarschuwingsregels, werken onafhankelijk, zonder ontdubbeling.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over waarschuwingen in Azure](./alerts-overview.md)
