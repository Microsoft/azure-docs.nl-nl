---
title: Aanbevolen procedures voor cluster beveiliging
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de aanbevolen procedures voor cluster operators voor het beheren van de beveiliging en upgrades van clusters in azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: a56cf35fe3780aa53b12581358bd91fe44e8c8a1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585057"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor de beveiliging en upgrades van het cluster in azure Kubernetes service (AKS)

Wanneer u clusters beheert in azure Kubernetes service (AKS), is de beveiliging van uw workloads en gegevens een belang rijke overweging. Met name wanneer u meerdere Tenant clusters met logische isolatie uitvoert, moet u de toegang tot resources en workloads beveiligen. U moet er ook voor zorgen dat u de meest recente beveiligings updates voor Kubernetes en knoop punten toepast om het risico van een aanval te minimaliseren.

Dit artikel is gericht op het beveiligen van uw AKS-cluster. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Gebruik Azure Active Directory en Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC) om toegang tot de API-server te beveiligen
> * Toegang tot knooppunt bronnen beveiligen container
> * Een AKS-cluster upgraden naar de nieuwste versie van Kubernetes
> * Knoop punten up-to-date houden en automatisch beveiligings patches Toep assen

U kunt ook de aanbevolen procedures voor [container Image Management][best-practices-container-image-management] en voor [pod-beveiliging][best-practices-pod-security]lezen.

U kunt ook [integratie van Azure Kubernetes Services met Security Center][security-center-aks] gebruiken om bedreigingen te detecteren en aanbevelingen voor het beveiligen van uw AKS-clusters te bekijken.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Veilige toegang tot de API-server en cluster knooppunten

**Best Practice-richt lijnen** voor het beveiligen van de toegang tot de Kubernetes API-Server is een van de belangrijkste dingen die u kunt doen om uw cluster te beveiligen. Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC) integreren met Azure Active Directory om de toegang tot de API-server te beheren. Met deze besturings elementen kunt u AKS op dezelfde manier beveiligen als u de toegang tot uw Azure-abonnementen beveiligt.

De Kubernetes API-server biedt één verbindings punt voor aanvragen voor het uitvoeren van acties binnen een cluster. Als u de toegang tot de API-server wilt beveiligen en controleren, beperkt u de toegang en geeft u de mini maal privileged Access permissions vereist. Deze methode is niet uniek voor Kubernetes, maar is vooral belang rijk wanneer het AKS-cluster logisch is geïsoleerd voor gebruik door meerdere tenants.

Azure Active Directory (AD) biedt een oplossing voor identiteits beheer op bedrijfs niveau die kan worden geïntegreerd met AKS-clusters. Net als Kubernetes biedt geen oplossing voor identiteits beheer. het kan ook lastig zijn om de toegang tot de API-server te beperken. Met Azure AD-geïntegreerde clusters in AKS gebruikt u uw bestaande gebruikers-en groeps accounts om gebruikers te verifiëren bij de API-server.

![Azure Active Directory integratie voor AKS-clusters](media/operator-best-practices-cluster-security/aad-integration.png)

Gebruik Kubernetes RBAC en Azure AD-integratie om de API-server te beveiligen en geef het minste aantal machtigingen op dat is vereist voor een bereikset van bronnen, zoals een enkele naam ruimte. Verschillende gebruikers of groepen in azure AD kunnen verschillende Kubernetes-rollen krijgen. Met deze gedetailleerde machtigingen kunt u de toegang tot de API-server beperken en een duidelijke controle spoor van de uitgevoerde acties geven.

De aanbevolen best practice is groepen te gebruiken om toegang te bieden tot bestanden en mappen en afzonderlijke identiteiten, Azure AD- *groepslid* maatschap te gebruiken om gebruikers te binden aan Kubernetes-rollen in plaats van afzonderlijke *gebruikers*. Wanneer het groepslid maatschap van een gebruiker wordt gewijzigd, worden de toegangs machtigingen voor het AKS-cluster dienovereenkomstig gewijzigd. Als u de gebruiker rechtstreeks aan een rol koppelt, kan de taak functie worden gewijzigd. De lidmaatschappen van Azure AD-groepen worden bijgewerkt, maar de machtigingen voor het AKS-cluster komen niet overeen met die. In dit scenario wordt de gebruiker afgemeld dat er meer machtigingen worden verleend dan een gebruiker vereist.

Zie [Aanbevolen procedures voor verificatie en autorisatie in AKS][aks-best-practices-identity]voor meer informatie over Azure AD-integratie, Kubernetes RBAC en Azure RBAC.

## <a name="secure-container-access-to-resources"></a>Toegang tot resources beveiligen met de container

**Richt lijnen voor best practices** : Beperk de toegang tot acties die door containers kunnen worden uitgevoerd. Geef het minste aantal machtigingen op en Vermijd het gebruik van hoofd-en geprivilegieerde escalatie.

Op dezelfde manier als u gebruikers of groepen het minste aantal vereiste bevoegdheden moet verlenen, moeten containers ook worden beperkt tot de acties en processen die ze nodig hebben. Om het risico van een aanval tot een minimum te beperken, moet u geen toepassingen en containers configureren waarvoor escalated privileges of toegang tot de hoofdmap vereist zijn. Stel bijvoorbeeld `allowPrivilegeEscalation: false` in het manifest pod in. Deze *pod-beveiligings contexten* zijn ingebouwd in Kubernetes en stellen u in staat om aanvullende machtigingen te definiëren, zoals de gebruiker of groep die moet worden uitgevoerd als, of welke Linux-mogelijkheden er beschikbaar moeten zijn. Zie [beveiligde pod-toegang tot resources][pod-security-contexts]voor meer aanbevolen procedures.

Voor een gedetailleerdere controle van container acties, kunt u ook ingebouwde Linux-beveiligings functies gebruiken, zoals *AppArmor* en *seccomp*. Deze functies worden gedefinieerd op knooppunt niveau en vervolgens geïmplementeerd via een pod-manifest. Ingebouwde Linux-beveiligings functies zijn alleen beschikbaar voor Linux-knoop punten en van peulen.

> [!NOTE]
> Kubernetes-omgevingen, in AKS of elders, zijn niet volledig veilig voor gebruik van een vijandend multi tenant. Aanvullende beveiligings functies, zoals *AppArmor*, *Seccomp*, *pod Security Policies* of meer fijnere Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC) voor knoop punten maken aanvallen moeilijker. Voor echte beveiliging bij het uitvoeren van vijandelijke multi tenant-workloads is een Hyper Visor echter het enige beveiligings niveau dat u moet vertrouwen. Het beveiligings domein voor Kubernetes wordt het hele cluster, niet een afzonderlijk knoop punt. Voor dit soort vijandelijke multi tenant-workloads moet u fysiek geïsoleerde clusters gebruiken.

### <a name="app-armor"></a>App-beveiliging

Als u de acties wilt beperken die containers kunnen uitvoeren, kunt u de [AppArmor][k8s-apparmor] Linux kernel Security-module gebruiken. AppArmor is beschikbaar als onderdeel van het onderliggende besturings systeem van het AKS-knoop punt en is standaard ingeschakeld. U maakt AppArmor-profielen waarmee acties zoals het lezen, schrijven of uitvoeren of systeem functies, zoals het koppelen van bestands systemen, worden beperkt. Standaard AppArmor-profielen beperken de toegang tot verschillende `/proc` en `/sys` locaties en bieden een manier om containers logisch te isoleren van het onderliggende knoop punt. AppArmor werkt voor elke toepassing die wordt uitgevoerd op Linux, niet alleen Kubernetes peul.

![AppArmor profielen in gebruik in een AKS-cluster om container acties te beperken](media/operator-best-practices-container-security/apparmor.png)

Als u AppArmor in actie wilt zien, wordt in het volgende voor beeld een profiel gemaakt waarmee bestanden niet kunnen worden geschreven. [SSH][aks-ssh] naar een AKS-knoop punt en maak een bestand met de naam *Deny-write. profile* en plak de volgende inhoud:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

AppArmor profielen worden toegevoegd met behulp van de `apparmor_parser` opdracht. Voeg het profiel toe aan AppArmor en geef de naam op van het profiel dat u in de vorige stap hebt gemaakt:

```console
sudo apparmor_parser deny-write.profile
```

Er wordt geen uitvoer geretourneerd als het profiel correct wordt geparseerd en toegepast op AppArmor. U keert terug naar de opdracht prompt.

Maak nu een pod-manifest van uw lokale computer met de naam *AKS-apparmor. yaml* en plak de volgende inhoud. In dit manifest wordt een aantekening gedefinieerd voor `container.apparmor.security.beta.kubernetes` add-verwijzingen naar het *Deny-write-* profiel dat in de vorige stappen is gemaakt:

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

Implementeer de voor beeld-pod met behulp van de opdracht [kubectl apply][kubectl-apply] :

```console
kubectl apply -f aks-apparmor.yaml
```

Als de Pod is geïmplementeerd, gebruikt u controleren of de pod van *Hello-apparmor* als *geblokkeerd* wordt weer gegeven:

```
$ kubectl get pods

NAME             READY   STATUS    RESTARTS   AGE
aks-ssh          1/1     Running   0          4m2s
hello-apparmor   0/1     Blocked   0          50s
```

Zie [AppArmor-profielen in Kubernetes][k8s-apparmor]voor meer informatie over AppArmor.

### <a name="secure-computing"></a>Veilig computing

Hoewel AppArmor werkt voor een Linux-toepassing, werkt [seccomp (*SEC* ureren *comp* uting)][seccomp] op proces niveau. Seccomp is ook een Linux kernel Security-module en wordt standaard ondersteund door de docker-runtime die wordt gebruikt door AKS-knoop punten. Met seccomp kan het proces aanroepen dat containers kunnen worden uitgevoerd. U maakt filters waarmee wordt gedefinieerd welke acties moeten worden toegestaan of geweigerd. vervolgens gebruikt u annotaties in een pod YAML-manifest om te koppelen aan het seccomp-filter. Hiermee wordt de best practice voor het verlenen van de container de minimale machtigingen die nodig zijn om te kunnen worden uitgevoerd, en niet meer.

Als u seccomp in actie wilt zien, maakt u een filter waarmee u geen machtigingen voor een bestand kunt wijzigen. [SSH][aks-ssh] naar een AKS-knoop punt en maak vervolgens een seccomp-filter met de naam */var/lib/kubelet/seccomp/Prevent-chmod* en plak de volgende inhoud:

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

Maak nu een pod-manifest van uw lokale computer met de naam *AKS-seccomp. yaml* en plak de volgende inhoud. Dit manifest definieert een aantekening voor `seccomp.security.alpha.kubernetes.io` en verwijst naar het *chmod-filter dat* u in de vorige stap hebt gemaakt:

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

Implementeer de voor beeld-pod met behulp van de opdracht [kubectl apply][kubectl-apply] :

```console
kubectl apply -f ./aks-seccomp.yaml
```

Bekijk de status van het peul met de opdracht [kubectl Get peul][kubectl-get] . De pod rapporteert een fout. De `chmod` opdracht kan niet worden uitgevoerd door het seccomp-filter, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Zie [Seccomp-beveiligings profielen voor docker][seccomp]voor meer informatie over beschik bare filters.

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Regel matig bijwerken naar de meest recente versie van Kubernetes

**Richt lijnen voor best practices** : als u op de hoogte wilt blijven van nieuwe functies en probleem oplossingen, voert u de Kubernetes-versie in uw AKS-cluster regel matig bij.

Met Kubernetes worden nieuwe functies in een sneller tempo vrijgegeven dan traditionele infrastructuur platforms. Kubernetes-updates bevatten nieuwe functies en oplossingen voor fouten en beveiliging. Nieuwe functies passeren meestal een *alpha* en vervolgens de *bèta* status voordat ze *stabiel* worden en zijn algemeen beschikbaar en worden aanbevolen voor productie gebruik. In deze release cyclus moet u Kubernetes bijwerken zonder dat er regel matig wijzigingen optreden of uw implementaties en sjablonen worden aangepast.

AKS ondersteunt drie secundaire versies van Kubernetes. Dit betekent dat als er een nieuwe secundaire patch versie wordt geïntroduceerd, de oudste secundaire versie-en patch releases buiten gebruik worden gesteld. Kleine updates voor Kubernetes gebeuren periodiek. Zorg ervoor dat u een beheer proces hebt om naar behoefte te controleren en bij te werken, zodat u niet meer ondersteuning hebt. Zie [ondersteunde Kubernetes-versies AKS][aks-supported-versions]voor meer informatie.

Als u de beschik bare versies voor uw cluster wilt controleren, gebruikt u de opdracht [AZ AKS Get-upgrades][az-aks-get-upgrades] , zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

U kunt vervolgens een upgrade uitvoeren van uw AKS-cluster met behulp van de opdracht [AZ AKS upgrade][az-aks-upgrade] . Het upgrade proces is veilig cordons en verbergt één knoop punt tegelijk, plant de peuling op de resterende knoop punten en implementeert vervolgens een nieuw knoop punt waarop de meest recente versies van het besturings systeem en de Kubernetes worden uitgevoerd.

Het wordt ten zeerste aanbevolen om nieuwe secundaire versies te testen in een test omgeving voor ontwikkel aars, zodat u kunt controleren of uw workload in orde is voor een goede werking met de nieuwe Kubernetes-versie. Kubernetes kan Api's afleiden, zoals in versie 1,16, die kunnen worden vertrouwd door uw workloads. Wanneer u nieuwe versies in productie neemt, overweeg dan om [meerdere knooppunt groepen te gebruiken in afzonderlijke versies](use-multiple-node-pools.md) en afzonderlijke Pools een voor een te upgraden om de update in een cluster progressief uit te rollen. Als er meerdere clusters worden uitgevoerd, moet u één cluster tegelijk upgraden naar progressief controleren op impact of wijzigingen.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

Zie [ondersteunde Kubernetes-versies in AKS][aks-supported-versions] en [een AKS-cluster upgraden][aks-upgrade]voor meer informatie over upgrades in AKS.

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Updates voor Linux-knoop punten verwerken en opnieuw opstarten met behulp van kured

**Richt lijnen voor best practices** : aks downloadt en installeert automatisch beveiligingsfixes op elke Linux-knoop punten, maar wordt niet automatisch opnieuw opgestart als dat nodig is. Gebruik `kured` om te controleren of opnieuw opstarten in behandeling is, en druk het knoop punt veilig af en af om het knoop punt opnieuw op te starten, de updates toe te passen en zo veilig mogelijk te maken met betrekking tot het besturings systeem. Voor Windows Server-knoop punten voert u regel matig een AKS-upgrade bewerking uit om het meest veilig te Cordon en af te zuigen en bijgewerkte knoop punten te implementeren.

Elke avond halen Linux-knoop punten in AKS beveiligings patches op die beschikbaar zijn via het distributie-update kanaal. Dit gedrag wordt automatisch geconfigureerd wanneer de knoop punten worden geïmplementeerd in een AKS-cluster. Om onderbrekingen en mogelijke gevolgen voor het uitvoeren van werk belastingen tot een minimum te beperken, worden knoop punten niet automatisch opnieuw opgestart als dit nodig is voor een beveiligings patch of kernel-update.

Het project van de open-source- [kured (KUbernetes start daemon)][kured] door Weaveworks houdt in dat het knoop punt opnieuw wordt opgestart. Wanneer een Linux-knoop punt updates toepast waarvoor opnieuw moet worden opgestart, is het knoop punt veilig afgebakend en verwerkte om het peul te verplaatsen en te plannen op andere knoop punten in het cluster. Zodra het knoop punt opnieuw is opgestart, wordt het weer toegevoegd aan het cluster en Kubernetes wordt de planning van de peulen opnieuw uitgevoerd. Om onderbrekingen te minimaliseren, mag er slechts één knoop punt tegelijk worden opgestart door `kured` .

![Het AKS-knoop punt opnieuw opstarten met behulp van kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Als u de nauw keurigheid van het besturings element wilt bepalen wanneer het opnieuw opstarten plaatsvindt, `kured` kan de integratie met Prometheus worden geïntegreerd om het opnieuw opstarten te voor komen als er andere onderhouds gebeurtenissen of cluster problemen worden uitgevoerd. Deze integratie minimaliseert extra complicaties door knoop punten opnieuw op te starten terwijl u niet actief problemen met andere problemen ondervindt.

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
