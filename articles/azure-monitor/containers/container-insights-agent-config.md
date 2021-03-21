---
title: Container Insights-agent gegevens verzameling configureren | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de container Insights-agent kunt configureren voor het beheren van de logboek verzameling stdout/stderr en omgevings variabelen.
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: d866fec2013daf9b8edfdbfd703c7b1098ae91bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708387"
---
# <a name="configure-agent-data-collection-for-container-insights"></a>Agent gegevens verzameling configureren voor container Insights

Met container Insights worden stdout-, stderr-en omgevings variabelen verzameld van container werkbelastingen die zijn geïmplementeerd op beheerde Kubernetes-clusters vanuit de container agent. U kunt instellingen voor het verzamelen van agent gegevens configureren door een aangepaste Kubernetes ConfigMaps te maken om deze ervaring te beheren. 

In dit artikel wordt beschreven hoe u ConfigMap maakt en hoe u gegevens verzameling kunt configureren op basis van uw vereisten.

>[!NOTE]
>Voor Azure Red Hat open Shift wordt een sjabloon ConfigMap-bestand gemaakt in de werk ruimte *openshift-Azure-logging* . 
>

## <a name="configmap-file-settings-overview"></a>Overzicht van ConfigMap-Bestands instellingen

Er wordt een sjabloon ConfigMap-bestand geleverd waarmee u het eenvoudig kunt bewerken met uw aanpassingen zonder dat u het helemaal opnieuw hoeft te maken. Voordat u begint, raadpleegt u de Kubernetes-documentatie over [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) en leert u hoe u ConfigMaps maakt, configureert en implementeert. Hierdoor kunt u stderr en stdout per naam ruimte of in het hele cluster filteren, en omgevings variabelen voor elke container die wordt uitgevoerd op alle verschillende knoop punten in het cluster.

>[!IMPORTANT]
>De minimale agent versie die wordt ondersteund voor het verzamelen van stdout-, stderr-en omgevings variabelen van container werkbelastingen, is ciprod06142019 of hoger. Als u de versie van de agent wilt controleren, selecteert u een knoop punt op het tabblad **knoop** punt en noteert u in het deel venster Eigenschappen de waarde van de **Agent-installatie kopie label** eigenschap. Zie [release opmerkingen](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)voor de agent voor meer informatie over de agent versies en wat er in elke release is opgenomen.

### <a name="data-collection-settings"></a>Instellingen voor gegevens verzameling

In de volgende tabel worden de instellingen beschreven die u kunt configureren om het verzamelen van gegevens te beheren:

| Sleutel | Gegevenstype | Waarde | Beschrijving |
|--|--|--|--|
| `schema-version` | Teken reeks (hoofdletter gevoelig) | v1 | Dit is de schema versie die wordt gebruikt door de agent<br> bij het parseren van deze ConfigMap.<br> Momenteel ondersteunde schema versie v1.<br> Het wijzigen van deze waarde wordt niet ondersteund en wordt<br> afgewezen wanneer ConfigMap wordt geëvalueerd. |
| `config-version` | Tekenreeks |  | Ondersteunt de mogelijkheid om de versie van dit configuratie bestand in uw bron beheersysteem of opslag plaats bij te houden.<br> Het maximum aantal toegestane tekens is 10 en alle andere tekens worden afgekapt. |
| `[log_collection_settings.stdout] enabled =` | Booleaans | waar of onwaar | Hiermee wordt bepaald of de collectie stdout container log is ingeschakeld. Als deze instelling `true` is ingesteld op en geen naam ruimten worden uitgesloten voor de stdout-logboek verzameling<br> ( `log_collection_settings.stdout.exclude_namespaces` hieronder ingesteld) worden stdout-logboeken verzameld van alle containers op alle knoop punten in het cluster. Indien niet opgegeven in ConfigMaps,<br> de standaard waarde is `enabled = true` . |
| `[log_collection_settings.stdout] exclude_namespaces =` | Tekenreeks | Door komma's gescheiden matrix | Matrix van Kubernetes-naam ruimten waarvoor stdout-logboeken worden niet verzameld. Deze instelling is alleen effectief als<br> `log_collection_settings.stdout.enabled`<br> is ingesteld op `true` .<br> Als niet opgegeven in ConfigMap, is de standaard waarde<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.stderr] enabled =` | Booleaans | waar of onwaar | Hiermee wordt bepaald of de logboek verzameling voor de stderr-container is ingeschakeld.<br> Als deze instelling `true` is ingesteld op en geen naam ruimten worden uitgesloten voor de stdout-logboek verzameling<br> ( `log_collection_settings.stderr.exclude_namespaces` instelling) worden stderr-logboeken verzameld van alle containers op alle knoop punten in het cluster.<br> Als niet opgegeven in ConfigMaps, is de standaard waarde<br> `enabled = true`. |
| `[log_collection_settings.stderr] exclude_namespaces =` | Tekenreeks | Door komma's gescheiden matrix | Matrix van Kubernetes-naam ruimten waarvoor stderr-logboeken niet zullen worden verzameld.<br> Deze instelling is alleen effectief als<br> `log_collection_settings.stdout.enabled` is ingesteld op `true` .<br> Als niet opgegeven in ConfigMap, is de standaard waarde<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` | Booleaans | waar of onwaar | Met deze instelling bepaalt u de verzameling van omgevings variabelen<br> voor alle peulen/knoop punten in het cluster<br> en wordt standaard ingesteld op `enabled = true` wanneer niet opgegeven<br> in ConfigMaps.<br> Als verzameling van omgevings variabelen globaal is ingeschakeld, kunt u deze uitschakelen voor een specifieke container<br> door de omgevings variabele in te stellen<br> `AZMON_COLLECT_ENV`**Onwaar** , met een Dockerfile-instelling of in het [configuratie bestand voor de pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) onder de sectie **env:** .<br> Als verzameling van omgevings variabelen globaal is uitgeschakeld, kunt u het verzamelen van een bepaalde container niet inschakelen (dat wil zeggen, de enige onderdrukking die kan worden toegepast op het niveau van de container om de verzameling uit te scha kelen wanneer deze al is ingeschakeld.) |
| `[log_collection_settings.enrich_container_logs] enabled =` | Booleaans | waar of onwaar | Met deze instelling wordt de uitname van het container logboek gecontroleerd om de naam en eigenschaps waarden van de afbeelding in te vullen<br> voor elke logboek record die wordt geschreven naar de ContainerLog-tabel voor alle container Logboeken in het cluster.<br> De standaard instelling is `enabled = false` wanneer deze niet is opgegeven in ConfigMap. |
| `[log_collection_settings.collect_all_kube_events]` | Booleaans | waar of onwaar | Met deze instelling kunt u de verzameling uitvoeren-gebeurtenissen van alle typen.<br> Standaard worden de uitvoeren-gebeurtenissen met het type *normaal* niet verzameld. Als deze instelling is ingesteld op `true` , worden de *normale* gebeurtenissen niet meer gefilterd en worden alle gebeurtenissen verzameld.<br> Dit is standaard ingesteld op `false` . |

### <a name="metric-collection-settings"></a>Instellingen metrische verzameling

In de volgende tabel worden de instellingen beschreven die u kunt configureren om metrische verzameling te beheren:

| Sleutel | Gegevenstype | Waarde | Beschrijving |
|--|--|--|--|
| `[metric_collection_settings.collect_kube_system_pv_metrics] enabled =` | Booleaans | waar of onwaar | Met deze instelling worden metrische gegevens over het gebruik van permanente volumes (HW) in de uitvoeren-naam ruimte verzameld. Standaard worden metrische gegevens over het gebruik van permanente volume claims in de uitvoeren-naam ruimte niet verzameld. Als deze instelling is ingesteld op `true` , worden metrische gegevens over het gebruik van het PV voor alle naam ruimten verzameld. Dit is standaard ingesteld op `false` . |

ConfigMaps is een globale lijst en er kan slechts één ConfigMap op de agent worden toegepast. U kunt de verzamelingen niet overConfigMapsen.

## <a name="configure-and-deploy-configmaps"></a>ConfigMaps configureren en implementeren

Voer de volgende stappen uit om uw ConfigMap-configuratie bestand te configureren en te implementeren in uw cluster.

1. Down load het [sjabloon bestand CONFIGMAP yaml](https://aka.ms/container-azm-ms-agentconfig) en sla het op als container-AZM-MS-agentconfig. yaml. 

   > [!NOTE]
   > Deze stap is niet vereist bij het werken met Azure Red Hat open Shift omdat de ConfigMap-sjabloon al in het cluster bestaat.

2. Bewerk het ConfigMap yaml-bestand met uw aanpassingen om stdout-, stderr-en/of omgevings variabelen te verzamelen. Als u het ConfigMap yaml-bestand voor Azure Red Hat open Shift bewerkt, voert u eerst de opdracht uit `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` om het bestand in een tekst editor te openen.

    - Als u specifieke naam ruimten wilt uitsluiten voor de stdout-logboek verzameling, configureert u de sleutel/waarde met behulp van het volgende voor beeld: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]` .
    
    - Als u de verzameling van omgevings variabelen voor een specifieke container wilt uitschakelen, stelt u de sleutel/waarde `[log_collection_settings.env_var] enabled = true` in om variabelen verzameling globaal [](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) in te scha kelen en volgt u de onderstaande stappen om de configuratie voor de specifieke container te volt ooien.
    
    - Voor het uitschakelen van het hoofdletter gebruik van de logboek verzameling van stderr kunt u de sleutel/waarde configureren met het volgende voor beeld: `[log_collection_settings.stderr] enabled = false` .

3. Voor andere clusters dan Azure Red Hat open Shift, maakt u ConfigMap door de volgende kubectl-opdracht uit te voeren: `kubectl apply -f <configmap_yaml_file.yaml>` op andere clusters dan Azure Red Hat open SHIFT. 
    
    Bijvoorbeeld: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Sla uw wijzigingen op in de editor voor Azure Red Hat open SHIFT.

Het kan een paar minuten duren voordat de configuratie wijziging is doorgevoerd en alle omsagent in het cluster opnieuw worden opgestart. Het opnieuw opstarten is een rolling start voor alle omsagent-peulen, niet allemaal tegelijk opnieuw opstarten. Wanneer het opnieuw opstarten is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat: `configmap "container-azm-ms-agentconfig" created` .

## <a name="verify-configuration"></a>Configuratie controleren

Als u wilt controleren of de configuratie is toegepast op een ander cluster dan Azure Red Hat open Shift, gebruikt u de volgende opdracht om de logboeken te controleren vanuit een agent Pod: `kubectl logs omsagent-fdf58 -n kube-system` . Als er configuratie fouten zijn van de omsagent Peul, worden de volgende fouten weer gegeven in de uitvoer:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Fouten met betrekking tot het Toep assen van configuratie wijzigingen zijn ook beschikbaar voor controle. De volgende opties zijn beschikbaar voor extra probleem oplossing van configuratie wijzigingen:

- Vanuit een agent pod logboeken die dezelfde `kubectl logs` opdracht gebruiken. 

    >[!NOTE]
    >Deze opdracht is niet van toepassing op het Azure Red Hat open Shift-cluster.
    > 

- Vanuit Live-Logboeken. In Live logboeken worden fouten weer gegeven die vergelijkbaar zijn met de volgende:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Vanuit de **KubeMonAgentEvents** -tabel in uw log Analytics-werk ruimte. Gegevens worden elk uur verzonden met *fout* code voor configuratie fouten. Als er geen fouten zijn, heeft de vermelding in de tabel gegevens met *informatie* over de ernst, die geen fouten rapporteert. De eigenschap **Tags** bevat meer informatie over de Pod en de container-id waarop de fout is opgetreden en ook de eerste instantie, het laatste exemplaar en het aantal voor het afgelopen uur.

- Raadpleeg de omsagent-Logboeken in de **ContainerLog** -tabel om te controleren of de logboek verzameling openshift-Azure-logging is ingeschakeld met Azure Red Hat open SHIFT.

Nadat u de fout (en) in ConfigMap op andere clusters dan Azure Red Hat open Shift hebt gecorrigeerd, slaat u het yaml-bestand op en past u de bijgewerkte ConfigMaps toe door de opdracht uit te voeren: `kubectl apply -f <configmap_yaml_file.yaml` . Voor Azure Red Hat open Shift kunt u de bijgewerkte ConfigMaps bewerken en opslaan door de opdracht uit te voeren:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Bijgewerkte ConfigMap Toep assen

Als u al een ConfigMap hebt geïmplementeerd op andere clusters dan Azure Red Hat open Shift en u deze wilt bijwerken met een nieuwe configuratie, kunt u het ConfigMap-bestand dat u eerder hebt gebruikt, bewerken en vervolgens Toep assen met dezelfde opdracht als voorheen `kubectl apply -f <configmap_yaml_file.yaml` . Voor Azure Red Hat open Shift kunt u de bijgewerkte ConfigMaps bewerken en opslaan door de opdracht uit te voeren:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Het kan een paar minuten duren voordat de configuratie wijziging is doorgevoerd en alle omsagent in het cluster opnieuw worden opgestart. Het opnieuw opstarten is een rolling start voor alle omsagent-peulen, niet allemaal tegelijk opnieuw opstarten. Wanneer het opnieuw opstarten is voltooid, wordt een bericht weer gegeven dat er ongeveer als volgt uitziet en het resultaat bevat: `configmap "container-azm-ms-agentconfig" updated` .

## <a name="verifying-schema-version"></a>De schema versie wordt gecontroleerd

Ondersteunde configuratie schema versies zijn beschikbaar als pod-aantekening (schema-versies) in de omsagent-pod. U kunt deze weer geven met de volgende kubectl-opdracht: `kubectl describe pod omsagent-fdf58 -n=kube-system`

De uitvoer ziet er ongeveer als volgt uit met het aantekening schema-versies:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Volgende stappen

- Container Insights bevat geen vooraf gedefinieerde set met waarschuwingen. Bekijk de [prestatie waarschuwingen maken met container Insights](./container-insights-log-alerts.md) voor meer informatie over het maken van aanbevolen waarschuwingen voor hoog CPU-en geheugen gebruik ter ondersteuning van uw DevOps-of operationele processen en procedures.

- Als controle is ingeschakeld voor het verzamelen van het status-en resource gebruik van uw AKS-of hybride cluster en werk belastingen die hierop worden uitgevoerd, leert [u hoe u container Insights kunt gebruiken](container-insights-analyze.md) .

- Bekijk de [voor beelden van logboek query's](container-insights-log-search.md#search-logs-to-analyze-data) om vooraf gedefinieerde query's en voor beelden te bekijken voor het evalueren of aanpassen van waarschuwingen, het visualiseren of analyseren van uw clusters.