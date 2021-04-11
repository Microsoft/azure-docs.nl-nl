---
title: Aanbevolen procedures voor cluster beveiliging
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de aanbevolen procedures voor cluster operators voor het beheren van de beveiliging en upgrades van clusters in azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: ea63db2d8868a1333ae264c9cfdf31d0b7397a83
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105150"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor de beveiliging en upgrades van het cluster in azure Kubernetes service (AKS)

Wanneer u clusters beheert in azure Kubernetes service (AKS), is de werk belasting en gegevens beveiliging een belang rijke overweging. Wanneer u multi-tenant clusters met logische isolatie uitvoert, moet u de toegang tot resources en werk belasting vooral beveiligen. Minimaliseer het risico van een aanval door de meest recente beveiligings updates voor Kubernetes en knoop punten van het besturings systeem toe te passen.

Dit artikel is gericht op het beveiligen van uw AKS-cluster. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Gebruik Azure Active Directory en Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC) om toegang tot de API-server te beveiligen.
> * Beveiligde container toegang tot knooppunt bronnen.
> * Voer een upgrade uit van een AKS-cluster naar de meest recente versie van Kubernetes.
> * Bewaar knoop punten up-to-date en pas automatisch beveiligings patches toe.

U kunt ook de aanbevolen procedures voor [container Image Management][best-practices-container-image-management] en voor [pod-beveiliging][best-practices-pod-security]lezen.

U kunt ook [integratie van Azure Kubernetes Services met Security Center][security-center-aks] gebruiken om bedreigingen te detecteren en aanbevelingen voor het beveiligen van uw AKS-clusters te bekijken.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Veilige toegang tot de API-server en cluster knooppunten

> **Richtlijnen voor best practices** 
>
> Een van de belangrijkste manieren om uw cluster te beveiligen, is om de toegang tot de Kubernetes API-server te beveiligen. Als u de toegang tot de API-server wilt beheren, integreert u Kubernetes RBAC met Azure Active Directory (Azure AD). Met deze besturings elementen kunt u AKS op dezelfde manier beveiligen als u de toegang tot uw Azure-abonnementen beveiligt.

De Kubernetes API-server biedt één verbindings punt voor aanvragen voor het uitvoeren van acties binnen een cluster. Als u de toegang tot de API-server wilt beveiligen en controleren, kunt u de toegang beperken en de laagst mogelijke machtigings niveaus opgeven. Hoewel deze methode niet uniek is voor Kubernetes, is het vooral belang rijk wanneer u uw AKS-cluster logisch hebt geïsoleerd voor gebruik door meerdere tenants.

Azure AD biedt een oplossing voor identiteits beheer op bedrijfs niveau die kan worden geïntegreerd met AKS-clusters. Omdat Kubernetes geen oplossing voor identiteits beheer biedt, kunt u de toegang tot de API-server nauw keurig beperken. Met Azure AD-geïntegreerde clusters in AKS gebruikt u uw bestaande gebruikers-en groeps accounts om gebruikers te verifiëren bij de API-server.

![Azure Active Directory integratie voor AKS-clusters](media/operator-best-practices-cluster-security/aad-integration.png)

Met Kubernetes RBAC en Azure AD-integratie kunt u de API-server beveiligen en de mini maal vereiste machtigingen geven aan een resourceset met een bereik, zoals een enkele naam ruimte. U kunt verschillende Azure AD-gebruikers of-groepen verschillende Kubernetes-rollen verlenen. Met gedetailleerde machtigingen kunt u de toegang tot de API-server beperken en een duidelijke controle spoor van de uitgevoerde acties geven.

De aanbevolen best practice is het gebruik van *groepen* om toegang te bieden tot bestanden en mappen in plaats van afzonderlijke identiteiten. Gebruik bijvoorbeeld een Azure AD- *groepslid* maatschap om gebruikers te binden aan Kubernetes-rollen in plaats van afzonderlijke *gebruikers*. Wanneer het groepslid maatschap van een gebruiker wordt gewijzigd, worden de toegangs machtigingen voor het AKS-cluster dienovereenkomstig gewijzigd. 

U kunt er ook voor zorgen dat u de individuele gebruiker rechtstreeks verbindt met een rol en de bijbehorende taak functie wijzigt. Hoewel de lidmaatschappen van de Azure AD-groepslid maatschap worden bijgewerkt, zouden de machtigingen voor het AKS-cluster niet zouden zijn. In dit scenario wordt de gebruiker beëindigd met meer machtigingen dan ze nodig hebben.

Zie [Aanbevolen procedures voor verificatie en autorisatie in AKS][aks-best-practices-identity]voor meer informatie over Azure AD-integratie, Kubernetes RBAC en Azure RBAC.

## <a name="secure-container-access-to-resources"></a>Toegang tot resources beveiligen met de container

> **Richtlijnen voor best practices** 
> 
> Beperk de toegang tot acties die door containers kunnen worden uitgevoerd. Geef het minste aantal machtigingen op en Vermijd het gebruik van hoofd toegang of privileged escalation.

Op dezelfde manier als u gebruikers of groepen de vereiste minimum bevoegdheden moet verlenen, moet u ook containers beperken tot alleen de vereiste acties en processen. Om het risico van een aanval tot een minimum te beperken, moet u voor komen dat toepassingen en containers worden geconfigureerd waarvoor escalatie bevoegdheden of hoofd toegang zijn vereist. 

Stel bijvoorbeeld `allowPrivilegeEscalation: false` in het manifest pod in. Met deze ingebouwde Kubernetes *pod-beveiligings contexten* kunt u aanvullende machtigingen definiëren, zoals de gebruiker of groep die moet worden uitgevoerd, of de Linux-mogelijkheden die beschikbaar moeten worden gemaakt. Zie [beveiligde pod-toegang tot resources][pod-security-contexts]voor meer aanbevolen procedures.

Voor een nog gedetailleerdere controle van container acties, kunt u ook ingebouwde Linux-beveiligings functies gebruiken, zoals *AppArmor* en *seccomp*. 
1. Linux-beveiligings functies op knooppunt niveau definiëren.
1. Implementeer functies via een pod-manifest. 

Ingebouwde Linux-beveiligings functies zijn alleen beschikbaar voor Linux-knoop punten en van peulen.

> [!NOTE]
> Op dit moment zijn Kubernetes omgevingen niet volledig veilig voor gebruik met meerdere tenants. Aanvullende beveiligings functies, zoals *AppArmor*, *Seccomp*,*pod Security Policies* of Kubernetes RBAC voor knoop punten, blok keren aanvallen op efficiënte wijze. 
>
>Alleen een Hyper Visor vertrouwen voor echte beveiliging bij het uitvoeren van vijandelijke multi tenant-workloads. Het beveiligings domein voor Kubernetes wordt het hele cluster, niet een afzonderlijk knoop punt. 
>
> Voor dit soort vijandelijke multi tenant-workloads moet u fysiek geïsoleerde clusters gebruiken.

### <a name="app-armor"></a>App-beveiliging

Als u container acties wilt beperken, kunt u de [AppArmor][k8s-apparmor] Linux kernel Security-module gebruiken. AppArmor is beschikbaar als onderdeel van het onderliggende besturings systeem van het AKS-knoop punt en is standaard ingeschakeld. U maakt AppArmor-profielen die lees-, schrijf-of uitvoer bewerkingen beperken, of systeem functies zoals het koppelen van bestands systemen. Standaard AppArmor-profielen beperken de toegang tot verschillende `/proc` en `/sys` locaties en bieden een manier om containers logisch te isoleren van het onderliggende knoop punt. AppArmor werkt voor elke toepassing die wordt uitgevoerd op Linux, niet alleen Kubernetes peul.

![AppArmor profielen in gebruik in een AKS-cluster om container acties te beperken](media/operator-best-practices-container-security/apparmor.png)

Als u AppArmor in actie wilt zien, wordt in het volgende voor beeld een profiel gemaakt waarmee bestanden niet kunnen worden geschreven. 
1. [SSH][aks-ssh] naar een AKS-knoop punt.
1. Maak een bestand met de naam *Deny-write. profile*.
1. Plak de volgende inhoud:

    ```
    #include <tunables/global>
    profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
      #include <abstractions/base>
  
      file,
      # Deny all file writes.
      deny /** w,
    }
    ```

AppArmor profielen worden toegevoegd met behulp van de `apparmor_parser` opdracht. 
1. Voeg het profiel toe aan AppArmor.
1. Geef de naam op van het profiel dat u in de vorige stap hebt gemaakt:

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    Als het profiel correct wordt geparseerd en toegepast op AppArmor, ziet u geen uitvoer en keert u terug naar de opdracht prompt.

1. Maak vanaf uw lokale computer een pod-manifest met de naam *AKS-apparmor. yaml*. Dit manifest:
    * Hiermee definieert u een aantekening voor `container.apparmor.security.beta.kubernetes` .
    * Verwijst naar het *Deny-write-* profiel dat in de vorige stappen is gemaakt.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: hello-apparmor
      annotations:
        container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
    spec:
      containers:
      - name: hello
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
    ```

1. Als de Pod is geïmplementeerd, gebruikt u controleren of de pod van *Hello-apparmor* als *geblokkeerd* wordt weer gegeven:

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

Zie [AppArmor-profielen in Kubernetes][k8s-apparmor]voor meer informatie over AppArmor.

### <a name="secure-computing"></a>Veilig computing

Hoewel AppArmor werkt voor een Linux-toepassing, werkt [seccomp (*SEC* ureren *comp* uting)][seccomp] op proces niveau. Seccomp is ook een Linux kernel Security-module en wordt standaard ondersteund door de docker-runtime die wordt gebruikt door AKS-knoop punten. Met seccomp kunt u aanvragen voor container processen beperken. Uitlijnen met de best practice van het verlenen van de container minimale machtiging alleen om uit te voeren door:
* Definiëren met filters welke acties moeten worden toegestaan of geweigerd.
* Aantekeningen maken in een pod YAML-manifest om te koppelen aan het seccomp-filter. 

Als u seccomp in actie wilt zien, maakt u een filter waarmee u geen machtigingen voor een bestand kunt wijzigen. 
1. [SSH][aks-ssh] naar een AKS-knoop punt.
1. Maak een seccomp-filter met de naam */var/lib/kubelet/seccomp/Prevent-chmod*.
1. Plak de volgende inhoud:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "name": "chmod",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "fchmodat",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "chmodat",
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

    In versie 1,19 en hoger moet u het volgende configureren:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "names": ["chmod","fchmodat","chmodat"],
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

1. Maak vanaf uw lokale computer een pod-manifest met de naam *AKS-seccomp. yaml* en plak de volgende inhoud. Dit manifest:
    * Hiermee definieert u een aantekening voor `seccomp.security.alpha.kubernetes.io` .
    * Verwijst naar het *chmod-filter dat* in de vorige stap is gemaakt.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
      annotations:
        seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
    spec:
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

    In versie 1,19 en hoger moet u het volgende configureren:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
    spec:
      securityContext:
        seccompProfile:
          type: Localhost
          localhostProfile: prevent-chmod
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

1. Implementeer de voor beeld-pod met behulp van de opdracht [kubectl apply][kubectl-apply] :

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. Bekijk de status van Pod met behulp van de opdracht [kubectl Get peul][kubectl-get] . 
    * De pod rapporteert een fout. 
    * De `chmod` opdracht kan niet worden uitgevoerd door het seccomp-filter, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

Zie [Seccomp-beveiligings profielen voor docker][seccomp]voor meer informatie over beschik bare filters.

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Regel matig bijwerken naar de meest recente versie van Kubernetes

> **Richtlijnen voor best practices** 
> 
> Als u op de hoogte wilt blijven van nieuwe functies en probleem oplossingen, voert u regel matig een upgrade uit van de Kubernetes-versie in uw AKS-cluster.

Met Kubernetes worden nieuwe functies in een sneller tempo vrijgegeven dan traditionele infrastructuur platforms. Kubernetes-updates zijn onder andere:
* Nieuwe functies
* Problemen met bug of beveiligingsfixes 

Nieuwe functies gaan doorgaans over op de *alpha* -en *bèta* status voordat ze *stabiel* worden. Zodra het stabiel is, zijn deze algemeen beschikbaar en aanbevolen voor productie gebruik. Met de nieuwe functie release cycle van Kubernetes kunt u Kubernetes bijwerken zonder dat er regel matig wijzigingen optreden of uw implementaties en sjablonen aanpassen.

AKS ondersteunt drie secundaire versies van Kubernetes. Zodra een nieuwe secundaire patch versie is geïntroduceerd, worden de oudste secundaire versie-en patch releases buiten gebruik gesteld. Secundaire Kubernetes-updates worden periodiek uitgevoerd. Zorg ervoor dat u een beheer proces hebt om te controleren op de benodigde upgrades om te blijven beschikken over de ondersteuning. Zie [ondersteunde Kubernetes-versies AKS][aks-supported-versions]voor meer informatie.

Als u de beschik bare versies voor uw cluster wilt controleren, gebruikt u de opdracht [AZ AKS Get-upgrades][az-aks-get-upgrades] , zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

U kunt vervolgens een upgrade uitvoeren van uw AKS-cluster met behulp van de opdracht [AZ AKS upgrade][az-aks-upgrade] . Het upgrade proces is veilig:
* Cordons en afvoer één knoop punt per keer.
* Plant de peuling op de resterende knoop punten.
* Hiermee implementeert u een nieuw knoop punt waarop de meest recente versies van besturings systemen en Kubernetes worden uitgevoerd.

>[!IMPORTANT]
> Test nieuwe secundaire versies in een ontwikkel test omgeving en controleer of de werk belasting in orde is met de nieuwe Kubernetes-versie. 
>
> Kubernetes kan Api's afleiden (zoals in versie 1,16) waarvan uw workloads afhankelijk zijn. Wanneer u nieuwe versies in productie neemt, overweeg dan om [meerdere knooppunt groepen te gebruiken in afzonderlijke versies](use-multiple-node-pools.md) en afzonderlijke Pools een voor een te upgraden om de update in een cluster progressief uit te rollen. Als er meerdere clusters worden uitgevoerd, moet u één cluster tegelijk upgraden naar progressief controleren op impact of wijzigingen.
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

Zie [ondersteunde Kubernetes-versies in AKS][aks-supported-versions] en [een AKS-cluster upgraden][aks-upgrade]voor meer informatie over upgrades in AKS.

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Updates voor Linux-knoop punten verwerken en opnieuw opstarten met behulp van kured

> **Richtlijnen voor best practices** 
> 
> Terwijl AKS automatisch beveiligings oplossingen downloadt en installeert op elk Linux-knoop punt, wordt het niet automatisch opnieuw opgestart. 
> 1. Gebruik `kured` om te controleren of opnieuw opstarten in behandeling is.
> 1. Cordon het knoop punt veilig op en verbergt dit zodat het knoop punt opnieuw wordt opgestart.
> 1. De updates Toep assen.
> 1. Zo veilig mogelijk zijn met betrekking tot het besturings systeem. 

Voor Windows Server-knoop punten voert u regel matig een AKS-upgrade bewerking uit om het meest veilig te Cordon en af te zuigen en bijgewerkte knoop punten te implementeren.

Elke avond halen Linux-knoop punten in AKS beveiligings patches via hun distributie-update kanaal. Dit gedrag wordt automatisch geconfigureerd wanneer de knoop punten worden geïmplementeerd in een AKS-cluster. Om onderbrekingen en mogelijke gevolgen voor het uitvoeren van werk belastingen tot een minimum te beperken, worden knoop punten niet automatisch opnieuw opgestart als dit nodig is voor een beveiligings patch of kernel-update.

Het project van de open-source- [kured (KUbernetes start daemon)][kured] door Weaveworks houdt in dat het knoop punt opnieuw wordt opgestart. Wanneer een Linux-knoop punt updates toepast waarvoor opnieuw moet worden opgestart, is het knoop punt veilig afgebakend en verwerkte om het peul te verplaatsen en te plannen op andere knoop punten in het cluster. Zodra het knoop punt opnieuw is opgestart, wordt het weer toegevoegd aan het cluster en Kubernetes wordt de pod-planning hervat. Om onderbrekingen te minimaliseren, mag er slechts één knoop punt tegelijk worden opgestart door `kured` .

![Het AKS-knoop punt opnieuw opstarten met behulp van kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Als u nog meer controle over opnieuw opstarten wilt, `kured` kunt u integreren met Prometheus om het opnieuw opstarten te voor komen als er andere onderhouds gebeurtenissen of cluster problemen worden uitgevoerd. Deze integratie vermindert de complicatie door knoop punten opnieuw op te starten terwijl u geen andere problemen meer oplost.

Zie [beveiliging en kernel-updates Toep assen op knoop punten in AKS][aks-kured]voor meer informatie over het opnieuw opstarten van knoop punten.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is gericht op het beveiligen van uw AKS-cluster. Raadpleeg de volgende artikelen voor meer informatie over het implementeren van sommige van deze gebieden:

* [Azure Active Directory integreren met AKS][aks-aad]
* [Een AKS-cluster upgraden naar de nieuwste versie van Kubernetes][aks-upgrade]
* [Beveiligings updates en het opnieuw opstarten van knoop punten verwerken met kured][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: ./azure-ad-integration-cli.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: ../security-center/defender-for-kubernetes-introduction.md
