---
title: Een PostgreSQL grootschalige-Server groep maken met Kubernetes-hulpprogram ma's
description: Een PostgreSQL grootschalige-Server groep maken met Kubernetes-hulpprogram ma's
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: fd1b74d33793c06e586a92cc8b2e8d2d36f4827a
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519959"
---
# <a name="create-a-postgresql-hyperscale-server-group-using-kubernetes-tools"></a>Een PostgreSQL grootschalige-Server groep maken met Kubernetes-hulpprogram ma's

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Vereisten

U moet al een Azure- [Arc-gegevens controller](./create-data-controller.md)hebben gemaakt.

Als u een PostgreSQL grootschalige-Server groep wilt maken met behulp van Kubernetes-hulpprogram ma's, moet u de Kubernetes-hulpprogram ma's hebben geïnstalleerd.  De voor beelden in dit artikel gebruiken `kubectl` , maar vergelijk bare benaderingen kunnen worden gebruikt met andere Kubernetes-hulpprogram ma's zoals het dash board Kubernetes, `oc` of `helm` Als u bekend bent met deze Program ma's en Kubernetes yaml/JSON.

[Het hulp programma kubectl installeren](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Overzicht

Als u een PostgreSQL grootschalige-Server groep wilt maken, moet u een Kubernetes-geheim maken om de aanmelding en het wacht woord van uw post gres-beheerder op te slaan en een aangepaste PostgreSQL grootschalige-Server groep op basis van de aangepaste resource definities postgresql-12 of postgresql-11.

## <a name="create-a-yaml-file"></a>Een yaml-bestand maken

U kunt het [sjabloon yaml](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/postgresql.yaml) -bestand gebruiken als uitgangs punt voor het maken van uw eigen aangepaste postgresql grootschalige Server Group yaml-bestand.  Down load dit bestand naar uw lokale computer en open het in een tekst editor.  Het is handig om een tekst editor te gebruiken, zoals [VS code](https://code.visualstudio.com/download) , waarmee syntaxis markeringen en linting voor YAML-bestanden worden ondersteund.

Dit is een voor beeld van een yaml-bestand:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
kind: Secret
metadata:
  name: pg1-login-secret
type: Opaque
---
apiVersion: arcdata.microsoft.com/v1alpha1
kind: postgresql-12
metadata:
  generation: 1
  name: pg1
spec:
  engine:
    extensions:
    - name: citus
  scale:
    shards: 3
  scheduling:
    default:
      resources:
        limits:
          cpu: "4"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>De aanmelding en het wacht woord aanpassen.
Een Kubernetes-geheim wordt opgeslagen als een base64-gecodeerde teken reeks: een voor de gebruikers naam en een voor het wacht woord.  U moet een base64-aanmelding en een wacht woord voor beheerders coderen en deze op de locatie van de tijdelijke aanduiding plaatsen op `data.password` en `data.username` .  Neem niet de `<` symbolen en `>` op in de sjabloon.

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

### <a name="customizing-the-name"></a>De naam aanpassen

De sjabloon heeft de waarde ' PG1 ' voor het naam kenmerk.  U kunt dit wijzigen, maar dit moet tekens zijn die de DNS-naamgevings standaarden volgen.  U moet ook de naam van het geheim wijzigen zodat deze overeenkomt.  Als u bijvoorbeeld de naam van de PostgreSQL grootschalige-Server groep wijzigt in ' PG2 ', moet u de naam van het geheim wijzigen van ' PG1-login-Secret ' in ' PG2-login-Secret '

### <a name="customizing-the-engine-version"></a>De engine versie aanpassen

U kunt de engine versie wijzigen in postgresql-11 of postgresql-12 door het kenmerk te bewerken `kind` .

### <a name="customizing-the-resource-requirements"></a>De resource vereisten aanpassen

U kunt de resource vereisten wijzigen: het RAM-geheugen en de kern limieten en aanvragen, indien nodig.  

> [!NOTE]
> Meer informatie over [Kubernetes resource governance](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)vindt u in.

Vereisten voor resource limieten en-aanvragen:
- De limiet waarde voor kern geheugens is **vereist** voor facturerings doeleinden.
- De overige resource aanvragen en-limieten zijn optioneel.
- De limieten voor kernen en aanvragen moeten een positief geheel getal zijn, indien opgegeven.
- Het minimum van 1 kern geheugen is vereist voor de kernen aanvraag, indien opgegeven.
- De indeling van de geheugen waarde volgt de Kubernetes notatie.  

### <a name="customizing-service-type"></a>Service type aanpassen

Het Service type kan indien gewenst worden gewijzigd in NodePort.  Er wordt een wille keurig poort nummer toegewezen.

### <a name="customizing-storage"></a>Opslag ruimte aanpassen

U kunt de opslag klassen voor opslag aanpassen zodat deze overeenkomen met uw omgeving.  Als u niet zeker weet welke opslag klassen beschikbaar zijn, kunt u de opdracht uitvoeren `kubectl get storageclass` om ze weer te geven.  De sjabloon heeft de standaard waarde standaard.  Dit betekent dat er een opslag klasse met de _naam_ ' default ' is dat er zich een opslag klasse bevindt die _standaard is._  U kunt desgewenst ook de grootte van uw opslag wijzigen.  U kunt meer lezen over de [opslag configuratie](./storage-configuration.md).

## <a name="creating-the-postgresql-hyperscale-server-group"></a>De PostgreSQL grootschalige-Server groep maken

Nu u het yaml-bestand van de PostgreSQL grootschalige-Server groep hebt aangepast, kunt u de PostgreSQL grootschalige-Server groep maken door de volgende opdracht uit te voeren:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\postgres.yaml
```


## <a name="monitoring-the-creation-status"></a>De aanmaak status bewaken

Het duurt enkele minuten voordat de Server groep PostgreSQL grootschalige is gemaakt. U kunt de voortgang in een ander Terminal venster bewaken met de volgende opdrachten:

> [!NOTE]
>  In de onderstaande voor beelden wordt ervan uitgegaan dat u een PostgreSQL grootschalige-Server groep hebt gemaakt met de naam ' PG1 ' en Kubernetes naam ruimte met de name ' Arc '.  Als u de naam van een andere naam ruimte/PostgreSQL grootschalige-Server groep hebt gebruikt, kunt u ' Arc ' en ' PG1 ' vervangen door uw namen.

```console
kubectl get postgresql-12/pg1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

U kunt ook de status van de aanmaak van een bepaalde pod controleren door een opdracht als hieronder uit te voeren.  Dit is met name nuttig voor het oplossen van problemen.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/pg1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Problemen bij het maken oplossen

Raadpleeg de [hand leiding](troubleshoot-guide.md)voor het oplossen van problemen als u problemen ondervindt bij het maken.