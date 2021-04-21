---
title: Best practices voor clusterbeveiliging
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de best practices voor clusteroperator voor het beheren van clusterbeveiliging en -upgrades in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 5cb103d843aafbb7f72c03d65b45fe3a84f8d1cd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782977"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Best practices voor clusterbeveiliging en -upgrades in Azure Kubernetes Service (AKS)

Bij het beheren van clusters in Azure Kubernetes Service (AKS) is workload- en gegevensbeveiliging een belangrijke overweging. Wanneer u clusters met meerdere tenants met logische isolatie gebruikt, moet u met name de toegang tot resources en workloads beveiligen. Minimaliseer het risico op aanvallen door de nieuwste beveiligingsupdates voor Kubernetes en knooppuntbesturingssystemen toe te passen.

Dit artikel is gericht op het beveiligen van uw AKS-cluster. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Gebruik Azure Active Directory kubernetes op rollen gebaseerd toegangsbeheer (Kubernetes RBAC) om toegang tot de API-server te beveiligen.
> * Beveiligde containertoegang tot knooppuntresources.
> * Een AKS-cluster upgraden naar de nieuwste Kubernetes-versie.
> * Houd knooppunten up-to-date en pas automatisch beveiligingspatches toe.

U kunt ook de best practices voor het beheer van [containerafbeeldingen en][best-practices-container-image-management] voor [podbeveiliging lezen.][best-practices-pod-security]

U kunt ook [Azure Kubernetes Services-integratie][security-center-aks] met Security Center om bedreigingen te detecteren en aanbevelingen te bekijken voor het beveiligen van uw AKS-clusters.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Beveiligde toegang tot de API-server en clusterknooppunten

> **Richtlijnen voor best practices** 
>
> Een van de belangrijkste manieren om uw cluster te beveiligen, is om de toegang tot de Kubernetes-API-server te beveiligen. Als u de toegang tot de API-server wilt beheren, integreert u Kubernetes RBAC met Azure Active Directory (Azure AD). Met deze besturingselementen beveiligt u AKS op dezelfde manier als u de toegang tot uw Azure-abonnementen beveiligt.

De Kubernetes API-server biedt één verbindingspunt voor aanvragen om acties binnen een cluster uit te voeren. Als u de toegang tot de API-server wilt beveiligen en controleren, beperkt u de toegang en geeft u de laagst mogelijke machtigingsniveaus op. Hoewel deze aanpak niet uniek is voor Kubernetes, is deze vooral belangrijk wanneer u uw AKS-cluster logisch hebt geïsoleerd voor gebruik met meerdere tenants.

Azure AD biedt een oplossing voor identiteitsbeheer die gereed is voor bedrijven en kan worden geïntegreerd met AKS-clusters. Omdat Kubernetes geen oplossing voor identiteitsbeheer biedt, wordt u mogelijk hard ingedrukt om de toegang tot de API-server gedetailleerd te beperken. Met met Azure AD geïntegreerde clusters in AKS gebruikt u uw bestaande gebruikers- en groepsaccounts om gebruikers te verifiëren bij de API-server.

![Azure Active Directory voor AKS-clusters](media/operator-best-practices-cluster-security/aad-integration.png)

Met behulp van Kubernetes RBAC en Azure AD-integratie kunt u de API-server beveiligen en de minimale machtigingen bieden die zijn vereist voor een resourceset met een bereik, zoals één naamruimte. U kunt verschillende Azure AD-gebruikers of -groepen verschillende Kubernetes-rollen verlenen. Met gedetailleerde machtigingen kunt u de toegang tot de API-server beperken en een duidelijk audittrail van uitgevoerde acties bieden.

De aanbevolen best practice is om groepen te *gebruiken* om toegang te bieden tot bestanden en mappen in plaats van afzonderlijke identiteiten. Gebruik bijvoorbeeld een Azure *AD-groepslidmaatschap* om gebruikers te binden aan Kubernetes-rollen in plaats van aan afzonderlijke *gebruikers.* Als het groepslidmaatschap van een gebruiker wordt gewijzigd, worden de toegangsmachtigingen voor het AKS-cluster dienovereenkomstig gewijzigd. 

Stel dat u de afzonderlijke gebruiker rechtstreeks aan een rol verbindt en dat de functie van de taak wordt gewijzigd. Terwijl het lidmaatschap van de Azure AD-groep wordt bijgewerkt, zijn de machtigingen voor het AKS-cluster niet mogelijk. In dit scenario heeft de gebruiker meer machtigingen dan nodig is.

Zie Best practices voor verificatie en autorisatie in AKS voor meer informatie over Azure AD-integratie, Kubernetes RBAC en Azure [RBAC.][aks-best-practices-identity]

## <a name="secure-container-access-to-resources"></a>Containertoegang tot resources beveiligen

> **Richtlijnen voor best practices** 
> 
> Beperk de toegang tot acties die containers kunnen uitvoeren. Geef het minste aantal machtigingen op en vermijd het gebruik van roottoegang of bevoegde escalatie.

Op dezelfde manier als u gebruikers of groepen de minimaal vereiste bevoegdheden moet verlenen, moet u containers ook beperken tot alleen de benodigde acties en processen. Om het risico op aanvallen te minimaliseren, moet u voorkomen dat u toepassingen en containers configureert waarvoor geëscaleerde bevoegdheden of roottoegang zijn vereist. 

Stel bijvoorbeeld in `allowPrivilegeEscalation: false` het podmanifest in. Met deze ingebouwde beveiligingscontexten voor Kubernetes-pods kunt u aanvullende machtigingen definiëren, zoals de gebruiker of groep die moet worden uitgevoerd als of de Linux-mogelijkheden om weer te geven.  Zie Beveiligde toegang tot resources voor [pods voor meer best practices.][pod-security-contexts]

Voor nog gedetailleerdere controle van containeracties kunt u ook ingebouwde Linux-beveiligingsfuncties gebruiken, zoals *AppArmor* en *seccomp*. 
1. Definieer Linux-beveiligingsfuncties op knooppuntniveau.
1. Functies implementeren via een podmanifest. 

Ingebouwde Linux-beveiligingsfuncties zijn alleen beschikbaar op Linux-knooppunten en -pods.

> [!NOTE]
> Momenteel zijn Kubernetes-omgevingen niet volledig veilig voor onveilig gebruik van meerdere tenants. Aanvullende beveiligingsfuncties, zoals *AppArmor,* *seccomp,**Pod Security Policies* of Kubernetes RBAC voor knooppunten, blokkeren op efficiënte wijze aanvallen. 
>
>Voor echte beveiliging bij het uitvoeren van onveilige workloads met meerdere tenants vertrouwt u alleen een hypervisor. Het beveiligingsdomein voor Kubernetes wordt het hele cluster, niet een afzonderlijk knooppunt. 
>
> Voor dit soort onverantwoordelijke workloads met meerdere tenants moet u fysiek geïsoleerde clusters gebruiken.

### <a name="app-armor"></a>App Armor

Als u containeracties wilt beperken, kunt u de [beveiligingsmodule AppArmor][k8s-apparmor] Linux-kernel gebruiken. AppArmor is beschikbaar als onderdeel van het onderliggende besturingssysteem van het AKS-knooppunt en is standaard ingeschakeld. U maakt AppArmor-profielen die lees-, schrijf- of uitvoeracties beperken, of systeemfuncties zoals het aanmaken van bestandssystemen. Standaard AppArmor-profielen beperken de toegang tot verschillende locaties en bieden een manier om containers logisch te isoleren `/proc` `/sys` van het onderliggende knooppunt. AppArmor werkt voor elke toepassing die wordt uitgevoerd in Linux, niet alleen voor Kubernetes-pods.

![AppArmor-profielen die worden gebruikt in een AKS-cluster om containeracties te beperken](media/operator-best-practices-container-security/apparmor.png)

Als u AppArmor in actie wilt zien, wordt in het volgende voorbeeld een profiel gemaakt dat schrijven naar bestanden voorkomt. 
1. [SSH naar][aks-ssh] een AKS-knooppunt.
1. Maak een bestand met de *naam deny-write.profile.*
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

AppArmor-profielen worden toegevoegd met behulp van de `apparmor_parser` opdracht . 
1. Voeg het profiel toe aan AppArmor.
1. Geef de naam op van het profiel dat u in de vorige stap hebt gemaakt:

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    Als het profiel correct is geparseerd en toegepast op AppArmor, ziet u geen uitvoer en keert u terug naar de opdrachtprompt.

1. Maak op uw lokale computer een podmanifest met de *naam aks-apparmor.yaml*. Dit manifest:
    * Hiermee definieert u een aantekening voor `container.apparmor.security.beta.kubernetes` .
    * Verwijst naar *het profiel voor weigeren/schrijven* dat in de vorige stappen is gemaakt.

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

1. Nu de pod is geïmplementeerd, gebruikt u controleren of *de hello-apparmor-pod* wordt *weergeblokkeerd:*

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

Zie [AppArmor-profielen in Kubernetes][k8s-apparmor]voor meer informatie over AppArmor.

### <a name="secure-computing"></a>Beveiligde computing

Hoewel AppArmor werkt voor elke Linux-toepassing, werkt [seccomp (*sec* ure *comp* uting)][seccomp] op procesniveau. Seccomp is ook een Linux-kernelbeveiligingsmodule en wordt systeemeigen ondersteund door de Docker-runtime die wordt gebruikt door AKS-knooppunten. Met seccomp kunt u containerproces-aanroepen beperken. In overeenstemming met de best practice om de container alleen minimale machtigingen te verlenen om te worden uitgevoerd door:
* Definiëren met filters welke acties moeten worden toegestaan of weigeren.
* Aantekeningen maken in een YAML-manifest van een pod om te koppelen aan het seccomp-filter. 

Als u seccomp in actie wilt zien, maakt u een filter dat voorkomt dat machtigingen voor een bestand worden veranderd. 
1. [SSH naar][aks-ssh] een AKS-knooppunt.
1. Maak een seccomp-filter met de naam */var/lib/kubelet/seccomp/prevent-chmod.*
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

    In versie 1.19 en hoger moet u het volgende configureren:

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

1. Maak op uw lokale computer een podmanifest met de *naam aks-seccomp.yaml* en plak de volgende inhoud. Dit manifest:
    * Hiermee definieert u een aantekening voor `seccomp.security.alpha.kubernetes.io` .
    * Verwijst naar *het filter prevent-chmod* dat u in de vorige stap hebt gemaakt.

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

    In versie 1.19 en hoger moet u het volgende configureren:

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

1. Implementeer de voorbeeldpod met behulp van de [opdracht kubectl apply:][kubectl-apply]

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. Bekijk de podstatus met behulp van de [opdracht kubectl get pods.][kubectl-get] 
    * De pod rapporteert een fout. 
    * De `chmod` opdracht kan niet worden uitgevoerd door het seccomp-filter, zoals wordt weergegeven in de volgende voorbeelduitvoer:    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

Zie Beveiligingsprofielen voor Seccomp voor Docker voor meer informatie [over beschikbare filters.][seccomp]

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Regelmatig bijwerken naar de nieuwste versie van Kubernetes

> **Richtlijnen voor best practices** 
> 
> Als u op de hoogte wilt blijven van nieuwe functies en oplossingen voor fouten, moet u regelmatig de Kubernetes-versie in uw AKS-cluster bijwerken.

Kubernetes brengt nieuwe functies sneller uit dan traditionele infrastructuurplatforms. Kubernetes-updates zijn onder andere:
* Nieuwe functies
* Oplossingen voor fouten of beveiliging 

Nieuwe functies worden doorgaans door de *alfa-* en *bètastatus* verplaatst voordat ze stabiel *worden.* Eenmaal stabiel, algemeen beschikbaar en aanbevolen voor productiegebruik. Met de nieuwe releasecyclus van Kubernetes-functies kunt u Kubernetes bijwerken zonder dat er regelmatig wijzigingen in de weg staan of uw implementaties en sjablonen worden aangepast.

AKS ondersteunt drie secundaire versies van Kubernetes. Zodra er een nieuwe secundaire patchversie is geïntroduceerd, worden de oudste secundaire versie en ondersteunde patchreleases ingetrokken. Kleine Kubernetes-updates worden periodiek uitgevoerd. Zorg ervoor dat u een governanceproces hebt om te controleren op benodigde upgrades om binnen de ondersteuning te blijven. Zie Supported [Kubernetes versions AKS (Ondersteunde Kubernetes-versies AKS) voor meer informatie.][aks-supported-versions]

Als u de versies wilt controleren die beschikbaar zijn voor uw cluster, gebruikt u de opdracht [az aks get-upgrades,][az-aks-get-upgrades] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

U kunt vervolgens uw AKS-cluster upgraden met de [opdracht az aks upgrade.][az-aks-upgrade] Het upgradeproces is veilig:
* Cordons en één knooppunt per keer wordt leeg.
* Plan pods op resterende knooppunten.
* Implementeert een nieuw knooppunt met de nieuwste versies van het besturingssysteem en Kubernetes.

>[!IMPORTANT]
> Test nieuwe secundaire versies in een dev-testomgeving en controleer of uw workload in orde blijft met de nieuwe Kubernetes-versie. 
>
> Kubernetes kan API's (zoals in versie 1.16) die afhankelijk zijn van uw workloads, afvangen. Wanneer u nieuwe versies in [](use-multiple-node-pools.md) productie brengt, kunt u overwegen om meerdere knooppuntgroepen in afzonderlijke versies te gebruiken en afzonderlijke pools één voor één bij te werken om de update progressief in een cluster te zetten. Als u meerdere clusters gebruikt, moet u één cluster tegelijk upgraden om progressief te controleren op impact of wijzigingen.
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

Zie Ondersteunde [Kubernetes-versies in AKS][aks-supported-versions] en Een [AKS-cluster upgraden][aks-upgrade]voor meer informatie over upgrades in AKS.

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Updates van Linux-knooppunt verwerken en opnieuw opstarten met behulp van geseed

> **Richtlijnen voor best practices** 
> 
> Hoewel AKS automatisch beveiligingsfixes downloadt en installeert op elk Linux-knooppunt, wordt het knooppunt niet automatisch opnieuw opgestart. 
> 1. Gebruik `kured` om te kijken of opnieuw opstarten in behandeling is.
> 1. Het knooppunt veilig snoeren en leeglaten, zodat het knooppunt opnieuw kan worden opgestart.
> 1. Pas de updates toe.
> 1. Wees zo veilig mogelijk met betrekking tot het besturingssysteem. 

Voor Windows Server-knooppunten voert u regelmatig een AKS-upgradebewerking uit om pods veilig aan te sluiten en te verwijderen en bijgewerkte knooppunten te implementeren.

Elke avond krijgen Linux-knooppunten in AKS beveiligingspatches via hun distributie-updatekanaal. Dit gedrag wordt automatisch geconfigureerd wanneer de knooppunten worden geïmplementeerd in een AKS-cluster. Om onderbrekingen en mogelijke gevolgen voor het uitvoeren van workloads te minimaliseren, worden knooppunten niet automatisch opnieuw opgestart als dit vereist is voor een beveiligingspatch of kernelupdate.

Het opensource-project [(KJenetes REboot Daemon)][kured] van Weaveworks kijkt of het knooppunt opnieuw moet worden opgestart. Wanneer op een Linux-knooppunt updates worden toegepast waarvoor opnieuw moet worden opgestart, wordt het knooppunt veilig gekabeld en verwijderd om de pods op andere knooppunten in het cluster te verplaatsen en te plannen. Zodra het knooppunt opnieuw is opgestart, wordt het weer toegevoegd aan het cluster en hervat Kubernetes de podplanning. Om onderbrekingen te minimaliseren, mag slechts één knooppunt tegelijk opnieuw worden opgestart door `kured` .

![Het proces voor het opnieuw opstarten van het AKS-knooppunt met behulp van :](media/operator-best-practices-cluster-security/node-reboot-process.png)

Als u nog meer controle wilt over opnieuw opstarten, kunt u integreren met Prometheus om opnieuw opstarten te voorkomen als er andere onderhoudsgebeurtenissen of `kured` clusterproblemen worden uitgevoerd. Deze integratie vermindert de problemen door knooppunten opnieuw op te starten terwijl u actief andere problemen oplost.

Zie Beveiligings- en kernelupdates toepassen op knooppunten in AKS voor meer informatie over het afhandelen van het opnieuw opstarten van [knooppunten.][aks-kured]

## <a name="next-steps"></a>Volgende stappen

Dit artikel is gericht op het beveiligen van uw AKS-cluster. Zie de volgende artikelen voor het implementeren van een aantal van deze gebieden:

* [Integratie Azure Active Directory met AKS][aks-aad]
* [Een AKS-cluster upgraden naar de nieuwste versie van Kubernetes][aks-upgrade]
* [Beveiligingsupdates verwerken en knooppunt opnieuw opstarten met geseed][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
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
