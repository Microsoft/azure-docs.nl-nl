---
title: Bevoegde containers uitvoeren in een Azure Red Hat open Shift-cluster | Microsoft Docs
description: Voer geprivilegieerde containers uit om de beveiliging en naleving te controleren.
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 12/05/2019
keywords: Aro, open Shift, aquasec, twistlock, Red Hat
ms.openlocfilehash: 77e75232b7f9d23f1d07931cc7dc231174e1312d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633559"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Bevoorrechte containers uitvoeren in een Azure Red Hat OpenShift-cluster

> [!IMPORTANT]
> Azure Red Hat open Shift 3,11 wordt 30 juni 2022 buiten gebruik gesteld. Ondersteuning voor het maken van nieuwe Azure Red Hat open Shift 3,11-clusters loopt voort tot en met 30 november 2020. Na de buiten gebruiks telling worden de resterende Azure Red Hat open Shift 3,11-clusters afgesloten om beveiligings problemen te voor komen.
> 
> Volg deze hand leiding voor het [maken van een Azure Red Hat open Shift 4-cluster](tutorial-create-cluster.md).
> Als u specifieke vragen hebt, [kunt u contact met ons](mailto:arofeedback@microsoft.com)opnemen.

U kunt geen wille keurige, geprivilegieerde containers uitvoeren op Azure Red Hat open Shift-clusters.
Twee beveiligings-en compatibiliteits oplossingen mogen worden uitgevoerd op ARO-clusters.
In dit document worden de verschillen beschreven van de algemene open Shift-implementatie documentatie van de leveranciers van het beveiligings product.


Lees deze instructies door voordat u de instructies van de leverancier volgt.
Sectie titels in productspecifieke stappen hieronder verwijzen rechtstreeks naar sectie titels in de documentatie van de leveranciers.

## <a name="before-you-begin"></a>Voordat u begint

De documentatie van de meeste beveiligings producten veronderstelt dat u cluster beheerders bevoegdheden hebt.
Klant beheerders hebben niet alle bevoegdheden in azure Red Hat open SHIFT. De vereiste machtigingen voor het wijzigen van de cluster bronnen zijn beperkt.

Controleer eerst of de gebruiker is aangemeld bij het cluster als een klant beheerder door uit te voeren `oc get scc` . Alle gebruikers die lid zijn van de groep klant beheerders, hebben machtigingen voor het weer geven van de beveiligings context beperkingen (SCCs) op het cluster.

Zorg er vervolgens voor dat de `oc` binaire versie `3.11.154` .
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Productspecifieke stappen voor de veiligheid van het licht blauw
De basis instructies die u kunt wijzigen, vindt u in de [documentatie over de beveiligings implementatie](https://docs.aquasec.com/docs/openshift-red-hat)van het licht blauw. De stappen die hier worden uitgevoerd, worden in combi natie met de onderhanden implementatie documentatie voor het licht.

De eerste stap is om aantekeningen te maken op de vereiste SCCs die wordt bijgewerkt. Met deze annotaties wordt voor komen dat de synchronisatie van het cluster pod de wijzigingen in deze SSCs herstelt.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Stap 1: vereisten voorbereiden
Vergeet niet om u aan te melden bij het cluster als een ARO-klant beheerder in plaats van de rol cluster beheerder.

Maak het project en het service account.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Wijs, in plaats van de rol cluster lezer toe te wijzen, de rol klant-beheerder-cluster toe aan de zeeblauw-account met de volgende opdracht.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Volg de resterende instructies in stap 1.  In deze instructies wordt beschreven hoe u het geheim instelt voor het onderhanden register.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Stap 2: de blauwe server, data base en gateway implementeren
Volg de stappen in de onderhouds documentatie voor het installeren van de YAML.

Wijzig de gegeven `aqua-console.yaml` .  Verwijder de bovenste twee objecten met het label, `kind: ClusterRole` en `kind: ClusterRoleBinding` .  Deze resources worden niet gemaakt, omdat de beheerder van de klant op dit moment geen toestemming heeft om te wijzigen `ClusterRole` en `ClusterRoleBinding` objecten.

De tweede wijziging is het `kind: Route` gedeelte van de `aqua-console.yaml` . Vervang de volgende YAML voor het `kind: Route` object in het `aqua-console.yaml` bestand.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Volg de resterende instructies.

### <a name="step-3-login-to-the-aqua-server"></a>Stap 3: aanmelden bij de zeeblauwe-server
Deze sectie is op geen enkele manier gewijzigd.  Volg de documentatie bij het licht blauw.

Gebruik de volgende opdracht om het adres van de zeeblauw-console op te halen.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Stap 4: blauwe afdwingers implementeren
Stel de volgende velden in wanneer u afdwingers implementeert:

| Veld          | Waarde         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiceAccount | licht blauw  |
| Project        | licht blauw: beveiliging |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Productspecifieke stappen voor prisma Cloud/Twistlock

De basis instructies die u kunt wijzigen, zijn te vinden in de [prisma-documentatie over Cloud implementatie](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Begin met het installeren van het `twistcli` hulp programma zoals beschreven in de secties ' prisma-Cloud installeren ' en ' de prisma-cloud software downloaden '.

Een nieuw open Shift-project maken
```
oc new-project twistlock
```

Sla de optionele sectie ' push de prisma-Cloud installatie kopieën naar een persoonlijk REGI ster over '. Het werkt niet op Azure Red Hat open SHIFT. Gebruik in plaats daarvan het online REGI ster.

U kunt de officiële documentatie volgen bij het Toep assen van de correcties die hieronder worden beschreven.
Begin met de sectie console installeren.

### <a name="install-console"></a>Console installeren

Tijdens `oc create -f twistlock_console.yaml` stap 2 krijgt u een fout melding bij het maken van de naam ruimte.
U kunt deze gewoon negeren, de naam ruimte is eerder gemaakt met de `oc new-project` opdracht.

Gebruiken `azure-disk` voor opslag type.

### <a name="create-an-external-route-to-console"></a>Een externe route naar de console maken

U kunt de documentatie volgen of de onderstaande instructies als u de voor keur geeft aan de opdracht OC.
Kopieer de volgende route definitie naar een bestand met de naam twistlock_route. yaml op uw computer
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
Voer vervolgens de volgende handelingen uit:
```
oc create -f twistlock_route.yaml
```

Met deze opdracht kunt u de URL ophalen die is toegewezen aan de Twistlock-console: `oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Console configureren

Volg de Twistlock-documentatie.

### <a name="install-defender"></a>Defender installeren

Tijdens `oc create -f defender.yaml` stap 2 krijgt u fouten bij het maken van de cluster functie en de binding van de cluster functie.
U kunt deze negeren.

Verdedigen worden alleen geïmplementeerd op reken knooppunten. U hoeft deze niet te beperken met een knooppunt kiezer.
