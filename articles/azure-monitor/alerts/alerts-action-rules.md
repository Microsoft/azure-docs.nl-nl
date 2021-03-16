---
title: Actie regels voor Azure Monitor waarschuwingen
description: Meer informatie over actie regels in Azure Monitor zijn en hoe u deze kunt configureren en beheren.
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: f70d798270ad82193f7ae5935d34f8f418d35e05
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471677"
---
# <a name="action-rules-preview"></a>Actie regels (preview-versie)

Met actie regels kunt u acties definiëren of onderdrukken voor elk Azure Resource Manager bereik (Azure-abonnement, resource groep of doel resource). Ze hebben verschillende filters waarmee u de specifieke subset van waarschuwings instanties kunt beperken waarop u wilt handelen.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>Waarom en wanneer moet u actie regels gebruiken?

### <a name="suppression-of-alerts"></a>Waarschuwingen onderdrukken

Er zijn veel scenario's waarin het nuttig is om de meldingen te onderdrukken die worden gegenereerd door waarschuwingen. Deze scenario's lopen uiteen van onderdrukking tijdens het geplande onderhouds venster tot onderdrukking tijdens niet-kantoor uren. Het team dat verantwoordelijk is voor  **ContosoVM** wil bijvoorbeeld waarschuwings meldingen onderdrukken voor het komende weekend, omdat **ContosoVM** het gepland onderhoud ondergaat.

Hoewel het team elke waarschuwings regel die is geconfigureerd voor **ContosoVM** hand matig kan uitschakelen (en deze opnieuw inschakelt na onderhoud), is het geen eenvoudig proces. Met actie regels kunt u waarschuwings onderdrukking op schaal definiëren, zodat de periode van onderdrukking flexibel kan worden geconfigureerd. In het vorige voor beeld kan het team één actie regel definiëren voor **ContosoVM** die alle waarschuwings meldingen voor het weekend onderdrukt.

### <a name="actions-at-scale"></a>Acties op schaal

Hoewel waarschuwings regels u helpen om de actie groep te definiëren die wordt geactiveerd wanneer de waarschuwing wordt gegenereerd, hebben klanten vaak een gemeen schappelijke actie groep binnen hun bereik van bewerkingen. Een team dat verantwoordelijk is voor de **ContosoRG** van de resource groep, definieert bijvoorbeeld waarschijnlijk dezelfde actie groep voor alle waarschuwings regels die zijn gedefinieerd in **ContosoRG**.

Met actie regels kunt u dit proces vereenvoudigen. Door acties op schaal te definiëren, kan een actie groep worden geactiveerd voor elke waarschuwing die wordt gegenereerd voor het geconfigureerde bereik. In het vorige voor beeld kan het team één actie regel definiëren voor **ContosoRG** die dezelfde actie groep activeren voor alle waarschuwingen die erin worden gegenereerd.

> [!NOTE]
> Actie regels zijn momenteel niet van toepassing op Azure Service Health-waarschuwingen.

## <a name="configuring-an-action-rule"></a>Een actieregel configureren

### <a name="portal"></a>[Portal](#tab/portal)

U kunt de functie openen door **acties beheren** te selecteren op de pagina **waarschuwings signalen** in azure monitor. Selecteer vervolgens **actie regels (preview)**. U kunt de regels openen door **actie regels (preview)** te selecteren in het dash board van de landings pagina voor waarschuwingen.

![Actie regels van de Azure Monitor landings pagina](media/alerts-action-rules/action-rules-landing-page.png)

Selecteer **+ nieuwe actie regel**.

![Scherm afbeelding toont de pagina acties beheren met de knop nieuwe actie regel gemarkeerd.](media/alerts-action-rules/action-rules-new-rule.png)

U kunt ook een actie regel maken tijdens het configureren van een waarschuwings regel.

![Scherm afbeelding toont de pagina regel maken met de knop actie regel maken gemarkeerd.](media/alerts-action-rules/action-rules-alert-rule.png)

U ziet nu de pagina stroom voor het maken van actie regels. Configureer de volgende elementen:

![Stroom voor maken van nieuwe actie regel](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Bereik

Kies eerst het bereik (Azure-abonnement, resource groep of doel resource). U kunt ook meerdere combi Naties van bereiken binnen één abonnement selecteren.

![Bereik van actie regel](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Filter criteria

U kunt desgewenst filters definiëren zodat de regel van toepassing is op een specifieke subset van de waarschuwingen of op specifieke gebeurtenissen op elke waarschuwing (bijvoorbeeld alleen ' geactiveerd ' of alleen ' opgelost ').

De beschikbare filters zijn:

* **Ernst**  
Deze regel is alleen van toepassing op waarschuwingen met de geselecteerde ernst.  
Bijvoorbeeld ' **Ernst = Sev1** ' betekent dat de regel alleen van toepassing is op waarschuwingen met de ernst Sev1.
* **Service bewaken**  
Deze regel is alleen van toepassing op waarschuwingen die afkomstig zijn van de geselecteerde bewakings Services.  
Bijvoorbeeld: de **controle service = "Azure backup"** betekent dat de regel alleen van toepassing is op back-upwaarschuwingen (afkomstig van Azure backup).
* **Resourcetype**  
Deze regel is alleen van toepassing op waarschuwingen voor de geselecteerde resource typen.  
Bijvoorbeeld, **resource type = "virtual machines"** betekent dat de regel alleen van toepassing is op waarschuwingen op virtuele machines.
* **ID van waarschuwings regel**  
Deze regel is alleen van toepassing op waarschuwingen die afkomstig zijn van een specifieke waarschuwings regel. De waarde moet de Resource Manager-ID van de waarschuwings regel zijn.  
Bijvoorbeeld: **waarschuwings regel-id = "/Subscriptions/SubId1/resourceGroups/Rg1/providers/Microsoft.Insights/metricalerts/API-latency"** betekent dat deze regel alleen van toepassing is op waarschuwingen die afkomstig zijn van de WAARSCHUWINGS regel API-latentie.  
_Opmerking: u kunt de juiste waarschuwings regel-ID ophalen door uw waarschuwings regels van de CLI op te geven of door een specifieke waarschuwings regel te openen in de portal, op Eigenschappen te klikken en de waarde Resource-ID te kopiëren._
* **Bewakings voorwaarde**  
Deze regel is alleen van toepassing op waarschuwings gebeurtenissen met de opgegeven bewakings voorwaarde-ofwel **geactiveerd** of **opgelost**.
* **Beschrijving**  
Deze regel is alleen van toepassing op waarschuwingen die een specifieke teken reeks in het veld Beschrijving van waarschuwing bevatten. Dit veld bevat de beschrijving van de waarschuwings regel.  
De **Beschrijving bevat bijvoorbeeld ' Prod '** , zodat de regel alleen overeenkomt met waarschuwingen die de teken reeks ' Prod ' in de beschrijving bevatten.
* **Waarschuwings context (Payload)**  
Deze regel is alleen van toepassing op waarschuwingen die een of meer specifieke waarden bevatten in de context velden van de waarschuwing.  
Bijvoorbeeld: **waarschuwings context (Payload) bevat ' computer-01 '** betekent dat de regel alleen van toepassing is op waarschuwingen waarvan de payload de teken reeks ' computer-01 ' bevat.

Als u meerdere filters in een regel instelt, zijn deze allemaal van toepassing. Als u bijvoorbeeld **resource type = virtual machines** en **Ernst = Sev0** instelt, is de regel alleen van toepassing op Sev0-waarschuwingen op virtuele machines.

![Actie regel filters](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Configuratie van onderdrukking of actie groep

Vervolgens configureert u de actie regel voor de ondersteuning van waarschuwingen of actie groepen. U kunt niet beide kiezen. De configuratie reageert op alle waarschuwings instanties die overeenkomen met het eerder gedefinieerde bereik en filters.

#### <a name="suppression"></a>Onderdrukking

Als u **onderdrukking** selecteert, configureert u de duur voor het onderdrukken van acties en meldingen. Kies een van de volgende opties:
* **Vanaf nu (altijd)**: onderdrukt alle meldingen voor onbepaalde tijd.
* **Op een gepland tijdstip**: onderdrukt meldingen binnen een gebonden duur.
* **Met een terugkeer patroon**: onderdrukt meldingen op een terugkerende dag, wekelijks of maandelijks schema.

![Onderdrukking van actie regel](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Actiegroep

Als u in de wissel knop **actie groep** selecteert, moet u een bestaande actie groep toevoegen of een nieuwe maken.

> [!NOTE]
> U kunt slechts één actie groep koppelen aan een actie regel.

![Nieuwe actie regel toevoegen of maken door actie groep te selecteren](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Details van de actie regel

Configureer als laatste de volgende Details voor de actie regel:
* Naam
* De resource groep waarin deze is opgeslagen
* Beschrijving

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt actie regels maken met de Azure CLI met behulp van de opdracht [AZ monitor Action-Rule Create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) .  De `az monitor action-rule` verwijzing is slechts een van de vele [Azure cli-verwijzingen voor Azure monitor](/cli/azure/azure-cli-reference-for-monitor).

### <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

1. [Azure CLI installeren](/cli/azure/install-azure-cli)

   Als u wilt, kunt u ook Azure Cloud Shell gebruiken om de stappen in dit artikel uit te voeren.  Azure Cloud Shell is een interactieve shell-omgeving die u via uw browser gebruikt.  Start Cloud Shell met een van de volgende methoden:

   - Open Cloud Shell door naar te gaan [https://shell.azure.com](https://shell.azure.com)

   - Selecteer de knop **Cloud shell** in de menu balk in de rechter bovenhoek van de [Azure Portal](https://portal.azure.com)

1. Meld u aan.

   Als u een lokale installatie van de CLI gebruikt, meldt u zich aan met de opdracht [AZ login](/cli/azure/reference-index#az-login) .  Volg de weergegeven stappen in uw terminal om het verificatieproces te voltooien.

    ```azurecli
    az login
    ```

1. De extensie `alertsmanagement` installeren

   De `az monitor action-rule` opdracht is een experimentele uitbrei ding van de Azure cli-kern. Meer informatie over extensie Verwijzingen in [use extension with Azure cli](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name alertsmanagement
   ```

   De volgende waarschuwing wordt verwacht.

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-action-rules-with-the-azure-cli"></a>Actie regels maken met Azure CLI

Zie de Azure CLI-referentie-inhoud voor [AZ monitor Action-Rule Create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) voor meer informatie over de vereiste en optionele para meters.

Maak een actie regel om meldingen in een resource groep te onderdrukken.

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

Maak een actie regel om meldingen voor alle Sev4-waarschuwingen op alle virtuele machines in het abonnement elk weekend te onderdrukken.

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

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenario 1: waarschuwingen onderdrukken op basis van Ernst

Contoso wil meldingen onderdrukken voor alle Sev4-waarschuwingen op alle virtuele machines binnen het abonnement **ContosoSub** elk weekend.

**Oplossing:** Een actie regel maken met:
* Scope = **ContosoSub**
* Filters
    * Ernst = **Sev4**
    * Resource type = **virtual machines**
* Onderdrukking met terugkeer patroon ingesteld op wekelijks, **zaterdag** en **zondag** gecontroleerd

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenario 2: waarschuwingen onderdrukken op basis van waarschuwings context (Payload)

Contoso wil meldingen onderdrukken voor alle logboek waarschuwingen die zijn gegenereerd voor **computer-01** in **ContosoSub** voor onbepaalde tijd tijdens het onderhoud.

**Oplossing:** Een actie regel maken met:
* Scope = **ContosoSub**
* Filters
    * Service bewaken = **log Analytics**
    * Waarschuwings context (Payload) bevat **computer-01**
* De onderdrukking is ingesteld op **vanaf nu (altijd)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenario 3: een actie groep die is gedefinieerd voor een resource groep

Contoso heeft [een metrische waarschuwing gedefinieerd op abonnements niveau](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Maar het wil ook bepalen van de acties die specifiek worden geactiveerd voor waarschuwingen die zijn gegenereerd op basis van de resource groep **ContosoRG**.

**Oplossing:** Een actie regel maken met:
* Scope = **ContosoRG**
* Geen filters
* De actie groep is ingesteld op **ContosoActionGroup**

> [!NOTE]
> *Actie groepen die zijn gedefinieerd in actie regels en waarschuwings regels, worden onafhankelijk van elkaar uitgevoerd, zonder ontdubbeling.* Als er in het eerder beschreven scenario een actie groep is gedefinieerd voor de waarschuwings regel, wordt deze geactiveerd in combi natie met de actie groep die is gedefinieerd in de actie regel.

## <a name="managing-your-action-rules"></a>Uw actie regels beheren

### <a name="portal"></a>[Portal](#tab/portal)

U kunt uw actie regels weer geven en beheren in de lijst weergave:

![Lijst weergave actie regels](media/alerts-action-rules/action-rules-list-view.png)

Hier kunt u actie regels op schaal inschakelen, uitschakelen of verwijderen door het selectie vakje ernaast in te scha kelen. Wanneer u een actie regel selecteert, wordt de bijbehorende configuratie pagina geopend. De pagina helpt u de definitie van de actie regel bij te werken en in of uit te scha kelen.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt uw actie regels weer geven en beheren met behulp van de opdracht [AZ monitor Action-Rule](/cli/azure/ext/alertsmanagement/monitor) van de Azure cli.

Voordat u actie regels met de Azure CLI beheert, moet u uw omgeving voorbereiden met behulp van de instructies in [een actie regel configureren](#configuring-an-action-rule).

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

Logboek waarschuwingen die u met de optie [aantal resultaten](./alerts-unified-log.md) maakt, genereren één waarschuwings exemplaar met behulp van het hele Zoek resultaat (dat mogelijk meerdere computers kan omvatten). Als in dit scenario een actie regel gebruikmaakt van het filter van de **waarschuwings context (Payload)** , wordt het op de waarschuwings instantie gereageerd zolang er een overeenkomst is. In scenario 2, dat eerder beschreven, wordt de volledige melding onderdrukt als de zoek resultaten voor de gegenereerde logboek waarschuwing zowel **computer-01** als **computer-02** bevatten. Er is helemaal geen melding voor **computer-02** gegenereerd.

![In het diagram worden de actie regels en logboek waarschuwingen weer gegeven met één waarschuwings exemplaar gemarkeerd.](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Als u het beste logboek waarschuwingen met actie regels wilt gebruiken, maakt u logboek waarschuwingen met de optie [metrische meet](./alerts-unified-log.md) waarde. Afzonderlijke waarschuwings instanties worden gegenereerd door deze optie op basis van het gedefinieerde groeps veld. In scenario 2 worden afzonderlijke waarschuwings instanties gegenereerd voor **computer-01** en **computer-02**. Als gevolg van de actie regel die in het scenario wordt beschreven, wordt alleen de melding voor **computer-01** onderdrukt. De melding voor **computer-02** blijft gewoon actief.

![Actie regels en logboek waarschuwingen (aantal resultaten)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Veelgestelde vragen

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Ik wil een actie regel configureren, maar ik wil alle mogelijke overlappende actie regels zien, zodat er geen dubbele meldingen worden weer geven. Is het mogelijk dat u dit kunt doen?

Nadat u een bereik hebt gedefinieerd tijdens het configureren van een actie regel, ziet u een lijst met actie regels die overlappen op hetzelfde bereik (indien van toepassing). Deze overlap ping kan een van de volgende opties zijn:

* Een exacte overeenkomst: de actie regel die u definieert en de regel voor de overlappende actie bevinden zich op hetzelfde abonnement.
* Een subset: de actie regel die u definieert, bevindt zich bijvoorbeeld op een abonnement en de overlappende actie regel bevindt zich op een resource groep binnen het abonnement.
* Een omvattend: de actie regel die u definieert, bevindt zich bijvoorbeeld in een resource groep en de overlappende actie regel bevindt zich op het abonnement dat de resource groep bevat.
* Een door snede: de actie regel die u definieert, bevindt zich bijvoorbeeld op **VM1** en **VM2**, en de overlappende actie regel bevindt zich op **VM2** en **VM3**.

![Scherm afbeelding toont de nieuwe actie regel pagina met de overlappende actie regels die worden weer gegeven in de actie regels die zijn gedefinieerd in hetzelfde bereik venster.](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Terwijl ik een waarschuwings regel Configureer, is het mogelijk te weten of er al actie regels zijn gedefinieerd die kunnen worden toegepast op de waarschuwings regel die ik Definieer?

Nadat u de doel resource voor uw waarschuwings regel hebt gedefinieerd, kunt u de lijst met actie regels die op hetzelfde bereik handelen (indien van toepassing) bekijken door **geconfigureerde acties weer geven** te selecteren in de sectie **acties** . Deze lijst wordt ingevuld op basis van de volgende scenario's voor de Scope:

* Een exacte overeenkomst: bijvoorbeeld de waarschuwings regel die u definieert en de actie regel bevinden zich op hetzelfde abonnement.
* Een subset: de waarschuwings regel die u definieert, bevindt zich bijvoorbeeld op een abonnement en de actie regel bevindt zich op een resource groep in het abonnement.
* Een hoofd verzameling: de waarschuwings regel die u definieert, bevindt zich bijvoorbeeld in een resource groep en de actie regel bevindt zich op het abonnement dat de resource groep bevat.
* Een snij punt: de waarschuwings regel die u definieert, bevindt zich bijvoorbeeld op **VM1** en **VM2**, en de actie regel bevindt zich op **VM2** en **VM3**.

![Overlappende actie regels](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Kan ik de waarschuwingen zien die zijn onderdrukt door een actie regel?

Op de [pagina waarschuwingen lijst](./alerts-managing-alert-instances.md)kunt u een aanvullende kolom met de naam **status onderdrukken** kiezen. Als de melding voor een waarschuwings exemplaar is onderdrukt, wordt deze status in de lijst weer gegeven.

![Onderdrukte waarschuwings instanties](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Wat gebeurt er als er een actie regel met een actie groep en een andere met onderdrukking actief is op hetzelfde bereik?

Onderdrukking heeft altijd voor rang op hetzelfde bereik.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Wat gebeurt er als er een resource is die in twee afzonderlijke actie regels wordt bewaakt? Ontvang ik een of twee meldingen? Bijvoorbeeld, **VM2** in het volgende scenario:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with action group AG1`

Voor elke waarschuwing voor VM1 en VM3 wordt de actie groeps AG1 één keer geactiveerd. Voor elke waarschuwing op **VM2** wordt de AG1 van de actie groep twee keer geactiveerd, omdat actie regels geen acties ontdubbelen.

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Wat gebeurt er als er een resource wordt gevolgd in twee afzonderlijke actie regels en één aanroepen voor actie terwijl een andere wordt onderdrukt? Bijvoorbeeld, **VM2** in het volgende scenario:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with suppression`

Voor elke waarschuwing op VM1 wordt de AG1 van de actie groep eenmaal geactiveerd. Acties en meldingen voor elke waarschuwing in VM2 en VM3 worden onderdrukt.

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Wat gebeurt er als er een waarschuwings regel en een actie regel zijn gedefinieerd voor dezelfde resource die verschillende actie groepen aanroept? Bijvoorbeeld, **VM1** in het volgende scenario:

   `alert rule rule1 on VM1 with action group AG2`

   `action rule AR1 defined for VM1 with action group AG1`

Voor elke waarschuwing op VM1 wordt de AG1 van de actie groep eenmaal geactiveerd. Wanneer waarschuwings regel "firewallregel1" wordt geactiveerd, wordt ook AG2 geactiveerd. Actie groepen die zijn gedefinieerd in actie regels en waarschuwings regels, worden onafhankelijk van elkaar uitgevoerd, zonder ontdubbeling.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over waarschuwingen in azure](./alerts-overview.md)
