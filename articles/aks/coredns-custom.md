---
title: CoreDNS aanpassen voor Azure Kubernetes service (AKS)
description: Meer informatie over het aanpassen van CoreDNS om subdomeinen toe te voegen of aangepaste DNS-eind punten uit te breiden met behulp van Azure Kubernetes service (AKS)
services: container-service
author: palma21
ms.topic: article
ms.date: 03/15/2019
ms.author: jpalma
ms.openlocfilehash: 5b13931bc6a13d988c21f728b996c51270769e0c
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368678"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>CoreDNS aanpassen met Azure Kubernetes Service

Azure Kubernetes service (AKS) maakt gebruik van het [CoreDNS][coredns] -project voor cluster-DNS-beheer en oplossingen met alle clusters van *1.12. x* en hoger. Voorheen werd het uitvoeren-DNS-project gebruikt. Dit uitvoeren-DNS-project is nu afgeschaft. Zie de [officiële upstream-documentatie][corednsk8s]voor meer informatie over het aanpassen van CoreDNS en Kubernetes.

Omdat AKS een beheerde service is, kunt u de hoofd configuratie voor CoreDNS (een *CoreFile*) niet wijzigen. In plaats daarvan gebruikt u een Kubernetes *ConfigMap* om de standaard instellingen te overschrijven. Als u de standaard AKS CoreDNS ConfigMaps wilt zien, gebruikt u de `kubectl get configmaps --namespace=kube-system coredns -o yaml` opdracht.

Dit artikel laat u zien hoe u ConfigMaps kunt gebruiken voor de basis aanpassings opties van CoreDNS in AKS. Deze aanpak wijkt af van het configureren van CoreDNS in andere contexten, zoals het gebruik van de CoreFile. Controleer de versie van CoreDNS die u gebruikt omdat de configuratie waarden kunnen veranderen tussen versies.

> [!NOTE]
> `kube-dns` verschillende [aanpassings opties][kubednsblog] aangeboden via een Kubernetes-configuratie kaart. CoreDNS is **niet** achterwaarts compatibel met uitvoeren-DNS. Aanpassingen die u eerder hebt gebruikt, moeten worden bijgewerkt voor gebruik met CoreDNS.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

Bij het maken van een configuratie zoals hieronder, moeten uw namen in de sectie *gegevens* eindigen op *. server* of *. override*. Deze naamgevings Conventie wordt gedefinieerd in de standaard AKS CoreDNS-Configmap die u kunt weer geven met behulp van de `kubectl get configmaps --namespace=kube-system coredns -o yaml` opdracht.

## <a name="what-is-supportedunsupported"></a>Wat wordt ondersteund/niet ondersteund

Alle ingebouwde CoreDNS-invoeg toepassingen worden ondersteund. Er worden geen invoeg toepassingen van derden ondersteund.

## <a name="rewrite-dns"></a>DNS opnieuw schrijven

U moet een scenario voor het uitvoeren van een herschrijf bewerking door de DNS-naam. Vervang in het volgende voor beeld door `<domain to be written>` uw eigen Fully Qualified Domain name. Maak een bestand `corednsms.yaml` met de naam en plak de volgende voorbeeld configuratie:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          upstream
          fallthrough in-addr.arpa ip6.arpa
        }
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10, but that server must be able to resolve the rewritten domain name
    }
```

> [!IMPORTANT]
> Als u een omleiding naar een DNS-server, zoals het CoreDNS-service-IP-adres, moet die DNS-server de herschreven domein naam kunnen omzetten.

Maak de ConfigMap met de opdracht [kubectl apply ConfigMap][kubectl-apply] en geef de naam van uw yaml-manifest op:

```console
kubectl apply -f corednsms.yaml
```

Als u wilt controleren of de aanpassingen zijn toegepast, gebruikt u de [kubectl Get configmaps][kubectl-get] en geeft u uw *coredns-aangepaste* ConfigMap op:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Forceer nu CoreDNS om de ConfigMap opnieuw te laden. De opdracht [kubectl delete pod][kubectl delete] is niet destructief en veroorzaakt geen lagere tijd. Het `kube-dns` peul wordt verwijderd en de scheduler Kubernetes wordt vervolgens opnieuw gemaakt. Deze nieuwe peulen bevatten de wijziging in TTL-waarde.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> De bovenstaande opdracht is juist. Terwijl we veranderen `coredns` , is de implementatie onder de **uitvoeren-DNS-** naam.

## <a name="custom-forward-server"></a>Aangepaste doorstuur server

Als u een doorstuur server moet opgeven voor uw netwerk verkeer, kunt u een ConfigMap maken om DNS aan te passen. In het volgende voor beeld werkt `forward` u de naam en het adres bij met de waarden voor uw eigen omgeving. Maak een bestand `corednsms.yaml` met de naam en plak de volgende voorbeeld configuratie:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Net als in de vorige voor beelden maakt u de ConfigMap met de opdracht [kubectl apply ConfigMap][kubectl-apply] en geeft u de naam van uw yaml-manifest op. Vervolgens dwingt u CoreDNS om de ConfigMap opnieuw te laden met behulp van de [kubectl delete pod][kubectl delete] voor de Kubernetes scheduler om ze opnieuw te maken:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Aangepaste domeinen gebruiken

Mogelijk wilt u aangepaste domeinen configureren die alleen intern kunnen worden omgezet. U kunt bijvoorbeeld het aangepaste domein *puglife. local*, dat geen geldig domein op het hoogste niveau is, omzetten. Zonder een aangepast domein ConfigMap kan het AKS-cluster het adres niet omzetten.

In het volgende voor beeld werkt u het aangepaste domein en IP-adres bij naar direct verkeer naar met de waarden voor uw eigen omgeving. Maak een bestand `corednsms.yaml` met de naam en plak de volgende voorbeeld configuratie:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: | # you may select any name here, but it must end with the .server file extension
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Net als in de vorige voor beelden maakt u de ConfigMap met de opdracht [kubectl apply ConfigMap][kubectl-apply] en geeft u de naam van uw yaml-manifest op. Vervolgens dwingt u CoreDNS om de ConfigMap opnieuw te laden met behulp van de [kubectl delete pod][kubectl delete] voor de Kubernetes scheduler om ze opnieuw te maken:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Stub-domeinen

CoreDNS kan ook worden gebruikt om stub-domeinen te configureren. In het volgende voor beeld werkt u de aangepaste domeinen en IP-adressen bij met de waarden voor uw eigen omgeving. Maak een bestand `corednsms.yaml` met de naam en plak de volgende voorbeeld configuratie:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Net als in de vorige voor beelden maakt u de ConfigMap met de opdracht [kubectl apply ConfigMap][kubectl-apply] en geeft u de naam van uw yaml-manifest op. Vervolgens dwingt u CoreDNS om de ConfigMap opnieuw te laden met behulp van de [kubectl delete pod][kubectl delete] voor de Kubernetes scheduler om ze opnieuw te maken:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Host-plugin

Aangezien alle ingebouwde invoeg toepassingen worden ondersteund, betekent dit dat de CoreDNS [hosts][coredns hosts] -invoeg toepassing ook beschikbaar is voor aanpassen.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: | # you may select any name here, but it must end with the .override file extension
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>Logboek registratie inschakelen voor het opsporen van DNS-query's 

Als u de DNS-query logboek registratie wilt inschakelen, past u de volgende configuratie toe in uw coredns-aangepaste ConfigMap:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: | # you may select any name here, but it must end with the .override file extension
        log
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel worden enkele voorbeeld scenario's weer gegeven voor het aanpassen van CoreDNS. Voor informatie over het CoreDNS-project raadpleegt u [de pagina CoreDNS upstream-project][coredns].

Zie [netwerk concepten voor toepassingen in AKS voor][concepts-network]meer informatie over basis concepten van het netwerk.

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
