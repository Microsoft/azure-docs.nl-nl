---
title: Prometheus-exemplaar implementeren in azure Red Hat open Shift-cluster
description: Maak een Prometheus-exemplaar in een Azure Red Hat open Shift-cluster om de metrische gegevens van uw toepassing te controleren.
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 06/17/2019
keywords: Prometheus, Aro, open Shift, metrische gegevens, Red Hat
ms.openlocfilehash: 974096e515401ab5e292dbad4c9beba7f1611a8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633441"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Een zelfstandig Prometheus-exemplaar in een Azure Red Hat open Shift-cluster implementeren

> [!IMPORTANT]
> Azure Red Hat open Shift 3,11 wordt 30 juni 2022 buiten gebruik gesteld. Ondersteuning voor het maken van nieuwe Azure Red Hat open Shift 3,11-clusters loopt voort tot en met 30 november 2020. Na de buiten gebruiks telling worden de resterende Azure Red Hat open Shift 3,11-clusters afgesloten om beveiligings problemen te voor komen.
> 
> Volg deze hand leiding voor het [maken van een Azure Red Hat open Shift 4-cluster](tutorial-create-cluster.md).
> Als u specifieke vragen hebt, [kunt u contact met ons](mailto:arofeedback@microsoft.com)opnemen.

In dit artikel wordt beschreven hoe u een zelfstandig Prometheus-exemplaar configureert waarin Service detectie wordt gebruikt in een Azure Red Hat open Shift-cluster.

> [!NOTE]
> Gebruikers beheerders toegang tot Azure Red Hat open Shift-cluster is niet vereist.

Doel installatie:

- Eén project (Prometheus-project) met Prometheus en Alertmanager.
- Twee projecten (app-Project1 en app-Project2), die de te bewaken toepassingen bevatten.

U gaat een aantal Prometheus-configuratie bestanden lokaal voorbereiden. Maak een nieuwe map om deze op te slaan. Configuratie bestanden worden in het cluster opgeslagen als geheimen, in het geval dat geheime tokens later aan het cluster worden toegevoegd.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Aanmelden bij het cluster met het hulp programma OC

1. Open een webbrowser en ga vervolgens naar de webconsole van uw cluster ( https://openshift .*wille keurige id*. *Region*. azmosa.io).
2. Meld u aan met uw Azure-referenties.
3. Selecteer uw gebruikers naam in de rechter bovenhoek en selecteer vervolgens **aanmeldings opdracht kopiëren**.
4. Plak uw gebruikers naam in de terminal die u gaat gebruiken.

> [!NOTE]
> Voer de opdracht uit om te zien of u bent aangemeld bij het juiste cluster `oc whoami -c` .

## <a name="prepare-the-projects"></a>De projecten voorbereiden

Voer de volgende opdrachten uit om de projecten te maken:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> U kunt de `-n` `--namespace` para meter or gebruiken of een actief project selecteren door de opdracht uit te voeren `oc project` .

## <a name="prepare-the-prometheus-configuration-file"></a>Het Prometheus-configuratie bestand voorbereiden
Maak een Prometheus. yml-bestand door de volgende inhoud in te voeren:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Maak een geheim met de naam PROM door de volgende configuratie in te voeren:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Het Prometheus. yml-bestand is een basis-Prometheus configuratie bestand. Hiermee stelt u de intervallen en configureert u automatische detectie in drie projecten (Prometheus-project, app-Project1, app-Project2). In het vorige configuratie bestand worden de automatisch gedetecteerde eind punten overlopend via HTTP, zonder verificatie.

Zie [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)voor meer informatie over het uitvallen van eind punten.


## <a name="prepare-the-alertmanager-config-file"></a>Het Alertmanager-configuratie bestand voorbereiden
Maak een alertmanager. yml-bestand door de volgende inhoud in te voeren:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Maak een geheim met de naam Prom-Alerts door de volgende configuratie in te voeren:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager. yml is het configuratie bestand van waarschuwings beheer.

> [!NOTE]
> Voer de opdracht uit om de twee vorige stappen te controleren `oc get secret -n prometheus-project` .

## <a name="start-prometheus-and-alertmanager"></a>Prometheus en Alertmanager starten
Ga naar Open [SHIFT/Origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) en down load de sjabloon [Prometheus-standalone. yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) . Pas de sjabloon toe op Prometheus-project door de volgende configuratie in te voeren:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Het Prometheus-standalone. yaml-bestand is een open Shift-sjabloon. Er wordt een Prometheus-exemplaar gemaakt met OAuth-proxy vóór het en een Alertmanager-exemplaar, ook beveiligd met OAuth-proxy. In deze sjabloon is OAuth-proxy zo geconfigureerd dat elke gebruiker die de naam ruimte Prometheus-project kan ophalen (Zie de `-openshift-sar` vlag).

> [!NOTE]
> Voer de opdracht uit om te controleren of de PROM StatefulSet gelijk is aan de gewenste replica's en het huidige aantal `oc get statefulset -n prometheus-project` . Als u alle resources in het project wilt controleren, voert u de `oc get all -n prometheus-project` opdracht uit.

## <a name="add-permissions-to-allow-service-discovery"></a>Machtigingen toevoegen om service detectie toe te staan

Maak een Prometheus-sdrole. yml-bestand door de volgende inhoud in te voeren:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Voer de volgende opdrachten uit om de sjabloon toe te passen op alle projecten van waaruit u service detectie wilt toestaan:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Voer de opdrachten en uit om te controleren of de functie en RoleBinding correct zijn gemaakt `oc get role` `oc get rolebinding` .

## <a name="optional-deploy-example-application"></a>Optioneel: voorbeeld toepassing implementeren

Alles werkt, maar er zijn geen metrische bronnen. Ga naar de Prometheus-URL ( https://prom-prometheus-project.apps .*wille keurige id*. *Region*. azmosa.io/). U kunt deze vinden met de volgende opdracht:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Vergeet niet om het voor voegsel https://toe te voegen aan het begin van de hostnaam.

Op de pagina **Status > service detectie** worden 0/0 actieve doelen weer gegeven.

Voer de volgende opdrachten uit om een voorbeeld toepassing te implementeren, waardoor de basis gegevens van python onder het/Metrics-eind punt worden weer gegeven:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
De nieuwe toepassingen moeten worden weer gegeven als geldige doelen op de service detectie pagina binnen 30 seconden na de implementatie.

Selecteer **status**  >  **doelen** voor meer informatie.

> [!NOTE]
> Voor elke geslaagde, Prometheus een gegevens punt toevoegen aan de metrische waarde. Selecteer **Prometheus** in de linkerbovenhoek **, Voer in** als de expressie en selecteer vervolgens **uitvoeren**.

## <a name="next-steps"></a>Volgende stappen

U kunt aangepaste Prometheus-instrumentatie toevoegen aan uw toepassingen. De Prometheus-client bibliotheek, waarmee de voor bereiding van Prometheus-gegevens wordt vereenvoudigd, is gereed voor verschillende programmeer talen.

Voor meer informatie raadpleegt u de volgende GitHub-bibliotheken:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
