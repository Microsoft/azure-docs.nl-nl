---
title: Uw Red Hat pull-geheim toevoegen of bijwerken op een Azure Red Hat open Shift 4-cluster
description: Uw Red Hat pull-geheim toevoegen of bijwerken op bestaande 4. x ARO-clusters
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/21/2020
keywords: pull-geheim, Aro, open Shift, Red Hat
ms.openlocfilehash: 58c0eb2be3423783a69d005277ffe75aaf59415f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633730"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>Uw Red Hat pull-geheim toevoegen of bijwerken op een Azure Red Hat open Shift 4-cluster

In deze hand leiding wordt beschreven hoe u uw Red Hat pull-geheim toevoegt of bijwerkt voor een bestaand Azure Red Hat open Shift (ARO) 4. x-cluster.

Als u voor de eerste keer een cluster maakt, kunt u uw pull-geheim toevoegen wanneer u uw cluster maakt. Zie [een Azure Red Hat open Shift 4-cluster maken](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional)voor meer informatie over het maken van een Aro-cluster met een Red Hat pull-geheim.

## <a name="before-you-begin"></a>Voordat u begint

In deze hand leiding wordt ervan uitgegaan dat u een bestaand Azure Red Hat open Shift 4-cluster hebt. Zorg ervoor dat u beheerders toegang hebt tot uw cluster.

## <a name="prepare-your-pull-secret"></a>Uw pull-geheim voorbereiden
Wanneer u een ARO-cluster maakt zonder een Red Hat pull-geheim toe te voegen, wordt er automatisch een pull-geheim in het cluster gemaakt. Dit pull-geheim is echter niet volledig ingevuld.

In deze sectie wordt uitgelegd hoe u het pull-geheim bijwerkt met aanvullende waarden van uw Red Hat pull Secret.

1. Haal het geheim op met de naam `pull-secret` in de `openshift-config` naam ruimte en sla het op in een afzonderlijk bestand door de volgende opdracht uit te voeren: 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    De uitvoer moet er ongeveer als volgt uitzien. (Houd er rekening mee dat de werkelijke geheime waarde is verwijderd.)

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. Ga naar uw [Red Hat open Shift cluster manager-Portal](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) en selecteer **pull Secret downloaden**. Uw Red Hat pull-geheim ziet er als volgt uit. (Houd er rekening mee dat de feitelijke geheime waarden zijn verwijderd.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. Bewerk het pull-geheim bestand dat u hebt ontvangen van het cluster door toe te voegen aan de vermeldingen in uw Red Hat pull Secret. 

    > [!IMPORTANT]
    > Met inbegrip `cloud.openshift.com` van de vermelding van uw Red Hat pull-geheim kan uw cluster het verzenden van telemetriegegevens naar Red Hat starten. Neem deze sectie alleen op als u telemetriegegevens wilt verzenden. Als dat niet het geval is, moet u de volgende sectie verlaten.    
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > Verwijder of wijzig u de `arosvc.azurecr.io` vermelding niet vanuit uw pull-geheim. Deze sectie is nodig om het cluster goed te laten functioneren.

    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    Het uiteindelijke bestand moet er als volgt uitzien. (Houd er rekening mee dat de feitelijke geheime waarden zijn verwijderd.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. Zorg ervoor dat het bestand een geldige JSON is. Er zijn veel manieren om uw JSON te valideren. In het volgende voor beeld wordt JQ gebruikt:

    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > Als er een fout optreedt in het bestand, wordt het weer gegeven als `parse error` .

## <a name="add-your-pull-secret-to-your-cluster"></a>Uw pull-geheim aan uw cluster toevoegen

Voer de volgende opdracht uit om uw pull-geheim bij te werken.

> [!NOTE]
> Als u deze opdracht uitvoert, worden de cluster knooppunten één voor één opnieuw opgestart terwijl ze worden bijgewerkt. 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

Nadat het geheim is ingesteld, bent u klaar om Red Hat Certified-Opera tors in te scha kelen.

### <a name="modify-the-configuration-files"></a>De configuratie bestanden wijzigen

Wijzig de volgende objecten om Red Hat-Opera tors in te scha kelen.

Wijzig eerst het configuratie bestand van de voor beelden-operator. Vervolgens kunt u de volgende opdracht uitvoeren om het configuratie bestand te bewerken:

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

Wijzig de `spec.architectures.managementState` `status.architecture.managementState` waarden en van `Removed` in `Managed` . 

In het volgende YAML-fragment worden alleen de relevante secties van het bewerkte YAML-bestand weer gegeven:

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

Voer vervolgens de volgende opdracht uit om het configuratie bestand van de operator-hub te bewerken:  

```console
oc edit operatorhub cluster -o yaml
```

Wijzig de `Spec.Sources.Disabled` `Status.Sources.Disabled` waarden en van `true` in `false` voor alle bronnen die u wilt inschakelen.

In het volgende YAML-fragment worden alleen de relevante secties van het bewerkte YAML-bestand weer gegeven:

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

Sla het bestand op om uw wijzigingen toe te passen.

## <a name="validate-that-your-secret-is-working"></a>Controleren of uw geheim werkt

Nadat u uw pull-geheim hebt toegevoegd en de juiste configuratie bestanden hebt gewijzigd, kan het enkele minuten duren voordat het cluster is bijgewerkt. Als u wilt controleren of uw cluster is bijgewerkt, voert u de volgende opdracht uit om de beschik bare Opera tors en bronnen van Red Hat-Opera tors weer te geven:

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

Als u de operator gecertificeerde Opera tors en Red Hat niet ziet, wacht u een paar minuten en probeert u het opnieuw.

Om ervoor te zorgen dat uw pull-geheim is bijgewerkt en correct werkt, opent u OperatorHub en controleert u of er een operator voor Red Hat is geverifieerd. Controleer bijvoorbeeld of de operator open Shift container Storage beschikbaar is en of u machtigingen hebt om te installeren.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over Red Hat-pull geheimen het [gebruik van image pull Secrets](https://docs.openshift.com/container-platform/4.6/openshift_images/managing_images/using-image-pull-secrets.html).

Zie [Red Hat open Shift container platform documentation](https://docs.openshift.com/container-platform/4.6/welcome/index.html)(Engelstalig) voor meer informatie over Red Hat open Shift 4.
