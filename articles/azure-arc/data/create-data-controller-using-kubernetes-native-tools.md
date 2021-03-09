---
title: Een gegevens controller maken met behulp van Kubernetes-hulpprogram ma's
description: Een gegevens controller maken met behulp van Kubernetes-hulpprogram ma's
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 500587dc6564aa55eb430365eb67bb958bbd2482
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519976"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Azure Arc data controller maken met behulp van Kubernetes-hulpprogram ma's

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Vereisten

Raadpleeg het onderwerp [de Azure Arc-gegevens controller maken](create-data-controller.md) voor overzichts informatie.

Als u de Azure-Arc-gegevens controller wilt maken met behulp van Kubernetes-hulpprogram ma's, moet u de Kubernetes-hulpprogram ma's geïnstalleerd hebben.  De voor beelden in dit artikel gebruiken `kubectl` , maar vergelijk bare benaderingen kunnen worden gebruikt met andere Kubernetes-hulpprogram ma's zoals het dash board Kubernetes, `oc` of `helm` Als u bekend bent met deze Program ma's en Kubernetes yaml/JSON.

[Het hulp programma kubectl installeren](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> Enkele van de stappen voor het maken van de Azure-Arc-gegevens controller die hieronder worden aangegeven, vereisen Kubernetes-cluster beheerders machtigingen.  Als u geen Kubernetes-cluster beheerder bent, moet u de Kubernetes-cluster beheerder deze stappen namens u laten uitvoeren.

### <a name="cleanup-from-past-installations"></a>Opschonen uit eerdere installaties

Als u in het verleden Azure Arc data controller hebt geïnstalleerd en u de Azure Arc-gegevens controller hebt verwijderd met behulp van de `azdata arc dc delete` opdracht, zijn er mogelijk een aantal objecten op cluster niveau die nog steeds moeten worden verwijderd. Voer de volgende opdrachten uit om Azure Arc data controller-cluster niveau objecten te verwijderen:

```console
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
```

## <a name="overview"></a>Overzicht

Het maken van de Azure Arc-gegevens controller heeft de volgende stappen op hoog niveau:
1. Maak de aangepaste resource definities voor de Arc-gegevens controller, Azure SQL Managed instance en PostgreSQL grootschalige. **[Vereist Kubernetes-cluster beheerder machtigingen]**
2. Maak een naam ruimte waarin de gegevens controller wordt gemaakt. **[Vereist Kubernetes-cluster beheerder machtigingen]**
3. Maak de Boots Trapper-service met inbegrip van de replicaset, het service account, de rol en de functie binding.
4. Maak een geheim voor de gebruikers naam en het wacht woord van de beheerder van de gegevens controller.
5. De gegevens controller maken.
   
## <a name="create-the-custom-resource-definitions"></a>De aangepaste resource definities maken

Voer de volgende opdracht uit om de aangepaste resource definities te maken.  **[Vereist Kubernetes-cluster beheerder machtigingen]**

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>Een naam ruimte maken waarin de gegevens controller wordt gemaakt

Voer een opdracht uit die vergelijkbaar is met de volgende om een nieuwe, toegewezen naam ruimte te maken waarin de gegevens controller wordt gemaakt.  In dit voor beeld en de rest van de voor beelden in dit artikel wordt een naam ruimte van `arc` gebruikt. Als u ervoor kiest een andere naam te gebruiken, kunt u dezelfde naam gebruiken in.

```console
kubectl create namespace arc
```

Als andere personen deze naam ruimte gebruiken die niet cluster beheerders zijn, raden we u aan om een naam ruimte beheerder te maken en die rol toe te kennen aan deze gebruikers via een functie binding.  De naam ruimte beheerder moet volledige machtigingen voor de naam ruimte hebben.  Meer informatie vindt u later over het bieden van meer gedetailleerde op rollen gebaseerde toegang tot gebruikers.

## <a name="create-the-bootstrapper-service"></a>De Boots Trapper-service maken

De Boots Trapper-service verwerkt binnenkomende aanvragen voor het maken, bewerken en verwijderen van aangepaste resources, zoals een gegevens controller, een SQL Managed instance of een PostgreSQL grootschalige-Server groep.

Voer de volgende opdracht uit om een Boots Trapper-service, een service account voor de Boots Trapper-service en een rol en rol voor het Boots Trapper-service account te maken.

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/bootstrapper.yaml
```

Controleer of de Boots Trapper pod wordt uitgevoerd met behulp van de volgende opdracht.  Mogelijk moet u deze enkele keren uitvoeren totdat de status wordt gewijzigd in `Running` .

```console
kubectl get pod --namespace arc
```

Het sjabloon bestand Boots Trapper. yaml wordt standaard ingesteld op het ophalen van de installatie kopie van de Boots Trapper-container uit de micro soft-Container Registry (MCR).  Als uw omgeving geen rechtstreekse toegang tot de micro soft-Container Registry heeft, kunt u het volgende doen:
- Volg de stappen om [de container installatie kopieën uit de micro soft container Registry te halen en deze naar een persoonlijk container register te pushen](offline-deployment.md).
- [Maak een installatie kopie-pull-geheim](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin) voor uw persoonlijke container register.
- Voeg een installatie kopie pull Secret toe aan de Boots Trapper-container. Zie onderstaand voorbeeld.
- Wijzig de locatie van de installatie kopie voor de installatie kopie van de Boots Trapper. Zie onderstaand voorbeeld.

In het onderstaande voor beeld wordt ervan uitgegaan dat u een installatie kopie met een geheime naam hebt gemaakt `regcred` zoals aangegeven in de Kubernetes-documentatie.

```yaml
#just showing only the relevant part of the bootstrapper.yaml template file here
containers:
      - env:
        - name: ACCEPT_EULA
          value: "Y"
        #image: mcr.microsoft.com/arcdata/arc-bootstrapper:public-preview-dec-2020  <-- template value to change
        image: <your registry DNS name or IP address>/<your repo>/arc-bootstrapper:<your tag>
        imagePullPolicy: IfNotPresent
        name: bootstrapper
        resources: {}
        securityContext:
          runAsUser: 21006
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: regcred
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: sa-mssql-controller
      serviceAccountName: sa-mssql-controller
      terminationGracePeriodSeconds: 30

```

## <a name="create-a-secret-for-the-data-controller-administrator"></a>Een geheim maken voor de beheerder van de gegevens controller

De gebruikers naam en het wacht woord van de beheerder van de gegevens controller worden gebruikt voor de verificatie van de data controller-API om beheer functies uit te voeren.  Kies een veilig wacht woord en deel het met alleen de gebruikers die cluster beheerder bevoegdheden nodig hebben.

Een Kubernetes-geheim wordt opgeslagen als een base64-gecodeerde teken reeks: een voor de gebruikers naam en een voor het wacht woord.

U kunt een online hulp programma gebruiken om uw gewenste gebruikers naam en wacht woord te coderen of u kunt ingebouwde CLI-hulpprogram ma's gebruiken, afhankelijk van uw platform.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

Zodra u de gebruikers naam en het wacht woord hebt gecodeerd, kunt u een bestand maken op basis van het [sjabloon bestand](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/controller-login-secret.yaml) en de gebruikers naam en het wacht woord vervangen door uw eigen waarden.

Voer vervolgens de volgende opdracht uit om het geheim te maken.

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-data-controller"></a>De gegevens controller maken

U bent nu klaar om de gegevens controller zelf te maken.

Maak eerst een kopie van het [sjabloon bestand](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/data-controller.yaml) lokaal op uw computer zodat u een aantal instellingen kunt wijzigen.

Bewerk het volgende naar behoefte:

**Vereist**
- **locatie**: Wijzig dit in de Azure-locatie waar de _meta gegevens_ over de gegevens controller worden opgeslagen.  U kunt de lijst met beschik bare Azure-locaties bekijken in het artikel overzicht van het [maken van gegevens controllers](create-data-controller.md) .
- **resourceGroup**: de Azure-resource groep waar u de gegevens controller Azure-resource wilt maken in azure Resource Manager.  Normaal gesp roken moet deze resource groep al bestaan, maar dit is niet vereist tot de tijd dat u de gegevens uploadt naar Azure.
- **abonnement**: de GUID van het Azure-abonnement voor het abonnement waarin u de Azure-resources wilt maken.

**AANBEVOLEN OM DE STANDAARD INSTELLINGEN TE CONTROLEREN EN MOGELIJK TE WIJZIGEN**
- **opslag.. ClassName**: de opslag klasse die moet worden gebruikt voor de gegevens controller gegevens en logboek bestanden.  Als u niet zeker bent van de beschik bare opslag klassen in uw Kubernetes-cluster, kunt u de volgende opdracht uitvoeren: `kubectl get storageclass` .  De standaard instelling is dat `default` er een opslag klasse is die bestaat en een naam heeft die niet is dat `default` er een opslag klasse is die de standaard _is_ .  Opmerking: er zijn twee instellingen voor ClassName die moeten worden ingesteld op de gewenste opslag klasse: één voor gegevens en één voor Logboeken.
- **Service type: Wijzig** het servicesapparaat in `NodePort` Als u geen Load Balancer gebruikt.  Opmerking: er zijn twee instellingen voor service type die moeten worden gewijzigd.
- Op Azure Red Hat open Shift of Red Hat open Shift container platform moet u de beveiligings context beperking Toep assen voordat u de gegevens controller maakt. Volg de instructies in [een beveiligings context beperking Toep assen voor Azure Arc enabled Data Services op open Shift](how-to-apply-security-context-constraint.md).
- **Beveiliging** Voor Azure Red Hat open Shift of Red Hat open Shift container platform vervangt u de `security:` instellingen door de volgende waarden in het yaml-bestand van de gegevens controller. 

```yml
  security:
    allowDumps: true
    allowNodeMetricsCollection: false
    allowPodMetricsCollection: false
    allowRunAsRoot: false
```

**Beschrijving**
- **naam**: de standaard naam van de gegevens controller is `arc` , maar u kunt deze desgewenst wijzigen.
- **DisplayName**: Stel dit in op dezelfde waarde als het naam kenmerk boven aan het bestand.
- **REGI ster**: de micro soft-container Registry is de standaard instelling.  Als u de installatie kopieën uit de micro soft-Container Registry haalt en [deze naar een persoonlijk container register pusht](offline-deployment.md), voert u hier het IP-adres of de DNS-naam van het REGI ster in.
- **dockerRegistry**: het pull-geheim van de installatie kopie die wordt gebruikt om de installatie kopieën uit een persoonlijk container register te halen, indien nodig.
- **opslag plaats**: de standaard opslagplaats op de micro soft-container Registry is `arcdata` .  Als u een persoonlijk container register gebruikt, voert u het pad in naar de map/opslag plaats die de container installatie kopieën van Azure Arc enabled Data Services bevat.
- **imageTag**: de huidige meest recente versie code wordt standaard in de sjabloon gebruikt, maar u kunt deze wijzigen als u een oudere versie wilt gebruiken.

In het volgende voor beeld wordt een volledig yaml-bestand van de gegevens controller weer gegeven. Werk het voor beeld voor uw omgeving bij op basis van uw vereisten en de bovenstaande informatie.

```yaml
apiVersion: arcdata.microsoft.com/v1alpha1
kind: datacontroller
metadata:
  generation: 1
  name: arc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    #dockerRegistry: arc-private-registry - optional if you are using a private container registry that requires authentication using an image pull secret
    serviceAccount: sa-mssql-controller
  docker:
    imagePullPolicy: Always
    imageTag: public-preview-dec-2020 
    registry: mcr.microsoft.com
    repository: arcdata
  security:
    allowDumps: true
    allowNodeMetricsCollection: true
    allowPodMetricsCollection: true
    allowRunAsRoot: false
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer
  - name: serviceProxy
    port: 30777
    serviceType: LoadBalancer
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: Indirect
      location: eastus
      resourceGroup: myresourcegroup
      subscription: c82c901a-129a-435d-86e4-cc6b294590ae
    controller:
      displayName: arc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default
      size: 10Gi
```

Sla het bewerkte bestand op uw lokale computer op en voer de volgende opdracht uit om de gegevens controller te maken:

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>De aanmaak status bewaken

Het duurt enkele minuten voordat de controller is gemaakt. U kunt de voortgang in een ander Terminal venster bewaken met de volgende opdrachten:

> [!NOTE]
>  In de onderstaande voor beelden wordt ervan uitgegaan dat u een gegevens controller en Kubernetes-naam ruimte hebt gemaakt met de naam `arc` .  Als u de naam van een andere naam ruimte/gegevens controller hebt gebruikt, kunt u `arc` deze vervangen door uw naam.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

U kunt ook de status van de aanmaak van een bepaalde pod controleren door een opdracht als hieronder uit te voeren.  Dit is met name nuttig voor het oplossen van problemen.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

De Azure Arc-extensie voor Azure Data Studio biedt een notitie blok om u te helpen bij het instellen van de manier waarop u de Kubernetes van Azure-Arc instelt en configureert om een Git-opslag plaats te bewaken die een voor beeld van een SQL Managed instance yaml-bestand bevat. Wanneer alles is verbonden, wordt een nieuw SQL-beheerd exemplaar geïmplementeerd in uw Kubernetes-cluster.

Zie de **implementatie een door SQL beheerd exemplaar implementeren met behulp van Azure Arc enabled Kubernetes en een stroom** notitieblok in de Azure Arc-extensie voor Azure Data Studio.

## <a name="troubleshooting-creation-problems"></a>Problemen bij het maken oplossen

Raadpleeg de [hand leiding](troubleshoot-guide.md)voor het oplossen van problemen als u problemen ondervindt bij het maken.

## <a name="next-steps"></a>Volgende stappen

- [Een door SQL beheerd exemplaar maken met behulp van Kubernetes-systeem eigen hulpprogram ma's](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Een PostgreSQL grootschalige-Server groep maken met Kubernetes-systeem eigen hulpprogram ma's](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
