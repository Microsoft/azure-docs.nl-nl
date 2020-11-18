---
title: Aanbevolen procedures voor het beheren van identiteiten
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de aanbevolen procedures voor cluster operators voor het beheren van verificatie en autorisatie voor clusters in azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: a63a756448f9c7202c79c3b4625fc99d4a90dc52
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682686"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor verificatie en autorisatie in azure Kubernetes service (AKS)

Bij het implementeren en onderhouden van clusters in azure Kubernetes service (AKS), moet u manieren implementeren voor het beheren van de toegang tot bronnen en services. Zonder deze besturings elementen kunnen accounts toegang hebben tot resources en services die ze niet nodig hebben. Het kan ook lastig zijn om bij te houden welke set referenties is gebruikt om wijzigingen aan te brengen.

In dit artikel Best practices wordt uitgelegd hoe een cluster operator de toegang en identiteit voor AKS-clusters kan beheren. In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * AKS-cluster gebruikers verifiëren met Azure Active Directory
> * Toegang tot resources beheren met Kubernetes op basis van rollen (Kubernetes RBAC)
> * Gebruik Azure RBAC om de toegang tot de AKS-resource en de Kubernetes-API op schaal nauw keurig te beheren, evenals de kubeconfig.
> * Een beheerde identiteit gebruiken om de meeste te verifiëren met andere services

## <a name="use-azure-active-directory"></a>Azure Active Directory gebruiken

**Best Practice-richt lijnen** : implementeer AKS-clusters met Azure AD-integratie. Met Azure AD wordt het Identity Management-onderdeel gecentraliseerd. Wijzigingen in het gebruikers account of de groeps status worden automatisch bijgewerkt in de toegang tot het AKS-cluster. Gebruik rollen of ClusterRoles en bindingen, zoals beschreven in de volgende sectie, om gebruikers of groepen te beperken tot een mini maal aantal benodigde machtigingen.

De ontwikkel aars en toepassings eigenaren van uw Kubernetes-cluster moeten toegang hebben tot verschillende bronnen. Kubernetes biedt geen oplossing voor identiteits beheer om te bepalen welke gebruikers met welke bronnen kunnen communiceren. In plaats daarvan integreert u uw cluster doorgaans met een bestaande identiteits oplossing. Azure Active Directory (AD) biedt een oplossing voor identiteits beheer op bedrijfs niveau en kan worden geïntegreerd met AKS-clusters.

Met Azure AD-geïntegreerde clusters in AKS maakt u *rollen* of *ClusterRoles* die toegangs machtigingen voor bronnen definiëren. Vervolgens *koppelt* u de rollen aan gebruikers of groepen vanuit Azure AD. Deze Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC) worden in de volgende sectie besproken. De integratie van Azure AD en het beheren van de toegang tot bronnen kan worden weer gegeven in het volgende diagram:

![Verificatie op cluster niveau voor de integratie van Azure Active Directory met AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Ontwikkel aars worden geverifieerd met Azure AD.
1. Het Azure AD-token uitgifte-eind punt geeft het toegangs token uit.
1. De ontwikkelaar voert een actie uit met behulp van het Azure AD-token, zoals `kubectl create pod`
1. Kubernetes valideert het token met Azure Active Directory en haalt de groepslid maatschappen van de ontwikkelaar op.
1. Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC) en cluster beleid worden toegepast.
1. De aanvraag van de ontwikkelaar is geslaagd of niet gebaseerd op de vorige validatie van Azure AD-groepslid maatschap en Kubernetes RBAC en beleid.

Zie [Azure Active Directory integreren met AKS][aks-aad]als u een AKS-cluster wilt maken dat gebruikmaakt van Azure AD.

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Kubernetes met op rollen gebaseerd toegangs beheer (Kubernetes RBAC) gebruiken

**Richt lijnen voor best practices** : gebruik Kubernetes RBAC om de machtigingen te definiëren die gebruikers of groepen hebben voor resources in het cluster. Maak rollen en bindingen die de mini maal vereiste machtigingen toewijzen. Integreer met Azure AD, zodat alle wijzigingen in de gebruikers status of het groepslid maatschap automatisch worden bijgewerkt en de toegang tot cluster bronnen actueel is.

In Kubernetes kunt u gedetailleerde controle over de toegang tot resources in het cluster bieden. Machtigingen worden gedefinieerd op het niveau van het cluster of aan specifieke naam ruimten. U kunt definiëren welke resources kunnen worden beheerd en met welke machtigingen. Deze rollen worden vervolgens toegepast op gebruikers of groepen met een binding. Zie [toegang en identiteits opties voor Azure Kubernetes service (AKS)][aks-concepts-identity]voor meer informatie over *rollen*, *ClusterRoles* en *bindingen*.

U kunt bijvoorbeeld een rol maken die volledige toegang verleent aan resources in de naam ruimte *Finance-App*, zoals wordt weer gegeven in het volgende voor beeld yaml-manifest:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Vervolgens wordt er een RoleBinding gemaakt die de Azure AD-gebruiker *developer1 \@ contoso.com* koppelt aan de RoleBinding, zoals wordt weer gegeven in het volgende YAML-manifest:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Wanneer *developer1 \@ contoso.com* wordt geverifieerd op basis van het AKS-cluster, hebben ze volledige machtigingen voor bronnen in de naam ruimte *Finance-App* . Op deze manier kunt u de toegang tot resources logisch scheiden en beheren. Kubernetes RBAC moet worden gebruikt in combi natie met Azure AD-Integration, zoals beschreven in de vorige sectie.

Zie [toegang tot cluster bronnen beheren met op rollen gebaseerd toegangs beheer en Azure Active Directory-identiteiten in AKS][azure-ad-rbac]voor meer informatie over het gebruik van Azure ad-groepen voor het beheren van de toegang tot Kubernetes-resources met behulp van Kubernetes RBAC.

## <a name="use-azure-rbac"></a>Azure RBAC gebruiken 
**Richt lijnen voor best practices** : gebruik Azure RBAC om de mini maal vereiste machtigingen te definiëren die gebruikers of groepen nodig hebben om resources te AKS in een of meer abonnementen.

Er zijn twee toegangs niveaus nodig om een AKS-cluster volledig te kunnen gebruiken: 
1. Open de AKS-resource in uw Azure-abonnement. Met dit toegangs niveau kunt u bepalen of u uw cluster wilt schalen of upgraden met behulp van de AKS-Api's en hoe u uw kubeconfig ophaalt.
Zie toegang tot het [cluster configuratie bestand beperken](control-kubeconfig-access.md)voor meer informatie over het beheren van de toegang tot de AKS-resource en de kubeconfig.

2. Toegang tot de Kubernetes-API. Dit toegangs niveau wordt bepaald door [KUBERNETES RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (traditioneel) of door Azure RBAC te integreren met AKS voor Kubernetes-autorisatie.
Zie [Azure RBAC gebruiken voor Kubernetes-autorisatie](manage-azure-rbac.md)voor meer informatie over het nauw keurig toekennen van machtigingen aan de KUBERNETES-API met behulp van Azure RBAC.

## <a name="use-pod-identities"></a>Pod-identiteiten gebruiken

**Richt lijnen voor best practices** : gebruik geen vaste referenties binnen de peulen of container installatie kopieën, omdat deze risico lopen op bloot stelling of misbruik. Gebruik in plaats daarvan pod-identiteiten om automatisch toegang aan te vragen met behulp van een centrale Azure AD-identiteits oplossing. Pod-identiteiten zijn alleen bedoeld voor gebruik met Linux-en container installatie kopieën.

Als er voor het eerst toegang nodig is tot andere Azure-Services, zoals Cosmos DB, Key Vault of Blob Storage, moet de pod toegangs referenties hebben. Deze toegangs referenties kunnen worden gedefinieerd met de container installatie kopie of worden geïnjecteerd als een Kubernetes-geheim, maar moeten hand matig worden gemaakt en toegewezen. De referenties worden vaak opnieuw gebruikt en worden niet regel matig geroteerd.

Met beheerde identiteiten voor Azure-resources (momenteel geïmplementeerd als een gekoppeld AKS open source-project) kunt u automatisch toegang aanvragen tot services via Azure AD. U hoeft niet hand matig referenties voor het eerst te definiëren, in plaats daarvan een toegangs token in realtime aan te vragen en deze alleen te gebruiken voor toegang tot de toegewezen services. In AKS worden twee onderdelen geïmplementeerd door de cluster operator, zodat de beheerde identiteiten kunnen worden gebruikt:

* **De node management Identity (NMI)-server** is een pod die als daemonset wordt uitgevoerd op elk knoop punt in het AKS-cluster. De NMI-server luistert naar pod-aanvragen voor Azure-Services.
* **De Managed Identity controller (MIC)** is een centrale pod met machtigingen voor het opvragen van de KUBERNETES-API-server en voor het controleren van een Azure Identity-toewijzing die overeenkomt met een pod.

Wanneer een Peul toegang tot een Azure-service vraagt, worden de netwerk regels het verkeer omgeleid naar de NMI-server (node management Identity). De NMI-server identificeert de peulen die toegang aanvragen tot Azure-Services op basis van hun externe adres en query's uitvoeren op de beheerde identiteits controller (MIC). De microfoon controleert op Azure Identity-toewijzingen in het AKS-cluster en de NMI-server vraagt vervolgens een toegangs token van Azure Active Directory (AD) op op basis van de identiteits toewijzing van het pod. Azure AD biedt toegang tot de NMI-server, die wordt geretourneerd aan de pod. Dit toegangs token kan door de pod worden gebruikt om toegang tot services in azure te vragen.

In het volgende voor beeld maakt een ontwikkelaar een pod die gebruikmaakt van een beheerde identiteit om toegang aan te vragen bij Azure SQL Database:

![Met Pod-identiteiten kan een pod automatisch toegang tot andere services aanvragen](media/operator-best-practices-identity/pod-identities.png)

1. De cluster operator maakt eerst een service account dat kan worden gebruikt voor het toewijzen van identiteiten wanneer per peul toegang tot Services wordt aangevraagd.
1. De NMI-server en-microfoon worden geïmplementeerd voor het door sturen van pod-aanvragen voor toegangs tokens naar Azure AD.
1. Een ontwikkelaar implementeert een pod met een beheerde identiteit die een toegangs token aanvraagt via de NMI-server.
1. Het token wordt geretourneerd naar de Pod en wordt gebruikt voor toegang tot Azure SQL Database

> [!NOTE]
> Beheerde pod-identiteiten zijn een open-source project en worden niet ondersteund door de technische ondersteuning van Azure.

Zie [Azure Active Directory identiteiten voor Kubernetes-toepassingen voor][aad-pod-identity]het gebruik van pod-identiteiten.

## <a name="next-steps"></a>Volgende stappen

Dit artikel Best practices is gericht op verificatie en autorisatie voor uw cluster en bronnen. Raadpleeg de volgende artikelen voor meer informatie over het implementeren van een aantal van deze aanbevolen procedures:

* [Azure Active Directory integreren met AKS][aks-aad]
* [Beheerde identiteiten gebruiken voor Azure-resources met AKS][aad-pod-identity]

Zie voor meer informatie over cluster bewerkingen in AKS de volgende aanbevolen procedures:

* [Multitenancy en clusterisolatie][aks-best-practices-cluster-isolation]
* [Functies van de Basic Kubernetes scheduler][aks-best-practices-scheduler]
* [Geavanceerde functies van Kubernetes scheduler][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
