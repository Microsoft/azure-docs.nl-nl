---
title: Een door SQL beheerd exemplaar maken met behulp van Kubernetes-hulpprogram ma's
description: Een door SQL beheerd exemplaar maken met behulp van Kubernetes-hulpprogram ma's
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: cade888d951c2071f8f40c145e28eed3c3a5d27c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384245"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>Azure SQL Managed instance maken met behulp van Kubernetes-hulpprogram ma's

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Vereisten

U moet al een Azure- [Arc-gegevens controller](./create-data-controller.md)hebben gemaakt.

Als u een SQL Managed instance wilt maken met behulp van Kubernetes-hulpprogram ma's, moet u de Kubernetes-hulpprogram ma's hebben geïnstalleerd.  De voor beelden in dit artikel gebruiken `kubectl` , maar vergelijk bare benaderingen kunnen worden gebruikt met andere Kubernetes-hulpprogram ma's zoals het dash board Kubernetes, `oc` of `helm` Als u bekend bent met deze Program ma's en Kubernetes yaml/JSON.

[Het hulp programma kubectl installeren](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Overzicht

Als u een SQL Managed instance wilt maken, moet u een Kubernetes-geheim maken om de aanmelding en het wacht woord van de systeem beheerder op te slaan en een aangepaste SQL Managed Instance-bron op basis van de aangepaste resource definitie van sqlmanagedinstance.

## <a name="create-a-yaml-file"></a>Een yaml-bestand maken

U kunt het [sjabloon yaml](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/sqlmi.yaml) -bestand als uitgangs punt gebruiken om uw eigen aangepaste SQL Managed instance yaml-bestand te maken.  Down load dit bestand naar uw lokale computer en open het in een tekst editor.  Het is handig om een tekst editor te gebruiken, zoals [VS code](https://code.visualstudio.com/download) , waarmee syntaxis markeringen en linting voor YAML-bestanden worden ondersteund.

Dit is een voor beeld van een yaml-bestand:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded user name. 'sa' is not allowed>
kind: Secret
metadata:
  name: sql1-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
  name: sql1
spec:
  limits:
    memory: 4Gi
    vcores: "4"
  requests:
    memory: 2Gi
    vcores: "1"
  service:
    type: LoadBalancer
  storage:
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>De aanmelding en het wacht woord aanpassen

Een Kubernetes-geheim wordt opgeslagen als een base64-gecodeerde teken reeks: een voor de gebruikers naam en een voor het wacht woord.  U moet een systeem beheerder aanmelden en een wacht woord met Base64 coderen en deze op de locatie van de tijdelijke aanduiding plaatsen op `data.password` en `data.username` .  Neem niet de `<` symbolen en `>` op in de sjabloon.

> [!NOTE]
> Voor een optimale beveiliging is het gebruik van de waarde ' sa ' niet toegestaan voor de aanmelding.
> Volg het [beleid voor wachtwoord complexiteit](/sql/relational-databases/security/password-policy#password-complexity).

U kunt een online hulp programma gebruiken om uw gewenste gebruikers naam en wacht woord te coderen of u kunt ingebouwde CLI-hulpprogram ma's gebruiken, afhankelijk van uw platform.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

### <a name="customizing-the-name"></a>De naam aanpassen

De sjabloon heeft de waarde ' sql1 ' voor het naam kenmerk.  U kunt dit wijzigen, maar dit moet tekens zijn die de DNS-naamgevings standaarden volgen.  U moet ook de naam van het geheim wijzigen zodat deze overeenkomt.  Als u bijvoorbeeld de naam van het SQL Managed instance wijzigt in ' sql2 ', moet u de naam van het geheim wijzigen van ' sql1-login-Secret ' in ' sql2-login-Secret '

### <a name="customizing-the-resource-requirements"></a>De resource vereisten aanpassen

U kunt de resource vereisten wijzigen: het RAM-geheugen en de kern limieten en aanvragen, indien nodig.  

> [!NOTE]
> Meer informatie over [Kubernetes resource governance](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)vindt u in.

Vereisten voor resource limieten en-aanvragen:
- De limiet waarde voor kern geheugens is **vereist** voor facturerings doeleinden.
- De overige resource aanvragen en-limieten zijn optioneel.
- De limieten voor kernen en aanvragen moeten een positief geheel getal zijn, indien opgegeven.
- Het minimum van twee kern geheugens is vereist voor de kernen aanvraag, indien opgegeven.
- De indeling van de geheugen waarde volgt de Kubernetes notatie.  
- Er is mini maal 2Gi vereist voor de geheugen aanvraag, indien opgegeven.
- Als algemene richt lijn hebt u 4 GB RAM-geheugen voor elke 1 kern voor productie-use cases.

### <a name="customizing-service-type"></a>Service type aanpassen

Het Service type kan indien gewenst worden gewijzigd in NodePort.  Er wordt een wille keurig poort nummer toegewezen.

### <a name="customizing-storage"></a>Opslag ruimte aanpassen

U kunt de opslag klassen voor opslag aanpassen zodat deze overeenkomen met uw omgeving.  Als u niet zeker weet welke opslag klassen beschikbaar zijn, kunt u de opdracht uitvoeren `kubectl get storageclass` om ze weer te geven.  De sjabloon heeft de standaard waarde standaard.  Dit betekent dat er een opslag klasse met de _naam_ ' default ' is dat er zich een opslag klasse bevindt die _standaard is._  U kunt desgewenst ook de grootte van uw opslag wijzigen.  U kunt meer lezen over de [opslag configuratie](./storage-configuration.md).

## <a name="creating-the-sql-managed-instance"></a>Het beheerde exemplaar van SQL wordt gemaakt

Nu u het SQL Managed instance yaml-bestand hebt aangepast, kunt u het SQL Managed instance maken door de volgende opdracht uit te voeren:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```


## <a name="monitoring-the-creation-status"></a>De aanmaak status bewaken

Het duurt enkele minuten voordat de SQL Managed instance is gemaakt. U kunt de voortgang in een ander Terminal venster bewaken met de volgende opdrachten:

> [!NOTE]
>  In de onderstaande voor beelden wordt ervan uitgegaan dat u een SQL Managed instance hebt gemaakt met de naam ' sql1 ' en de Kubernetes-naam ruimte.  Als u een andere naam ruimte/SQL Managed instance name hebt gebruikt, kunt u ' Arc ' en ' sqlmi ' vervangen door uw namen.

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

U kunt ook de status van de aanmaak van een bepaalde pod controleren door een opdracht als hieronder uit te voeren.  Dit is met name nuttig voor het oplossen van problemen.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Problemen bij het maken oplossen

Raadpleeg de [hand leiding](troubleshoot-guide.md)voor het oplossen van problemen als u problemen ondervindt bij het maken.

## <a name="next-steps"></a>Volgende stappen

[Verbinding maken met Azure Arc enabled SQL Managed instance](connect-managed-instance.md)
