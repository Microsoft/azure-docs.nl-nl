---
title: Aanbevolen procedures voor het beheren van identiteiten
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de aanbevolen procedures voor cluster operators voor het beheren van verificatie en autorisatie voor clusters in azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 8e0c7324f5b73b3a2ac5e5fd6fa256202035077a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790966"
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

## <a name="use-pod-managed-identities"></a>Door Pod beheerde identiteiten gebruiken

**Richt lijnen voor best practices** : gebruik geen vaste referenties binnen de peulen of container installatie kopieën, omdat deze risico lopen op bloot stelling of misbruik. Gebruik in plaats daarvan pod-identiteiten om automatisch toegang aan te vragen met behulp van een centrale Azure AD-identiteits oplossing. Pod-identiteiten zijn alleen bedoeld voor gebruik met Linux-en container installatie kopieën.

> [!NOTE]
> Ondersteuning voor pod-beheerde identiteiten voor Windows-containers is binnenkort beschikbaar.

Als er voor het eerst toegang nodig is tot andere Azure-Services, zoals Cosmos DB, Key Vault of Blob Storage, moet de pod toegangs referenties hebben. Deze toegangs referenties kunnen worden gedefinieerd met de container installatie kopie of worden geïnjecteerd als een Kubernetes-geheim, maar moeten hand matig worden gemaakt en toegewezen. De referenties worden vaak opnieuw gebruikt en worden niet regel matig geroteerd.

Door Pod beheerde identiteiten voor Azure-resources kunt u automatisch toegang vragen tot services via Azure AD. Pod-beheerde identiteiten zijn nu beschikbaar als preview-versie voor de Azure Kubernetes-service. Raadpleeg de documentatie voor het [gebruik van Azure Active Directory pod-beheerde identiteiten in de Azure Kubernetes-service (preview)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity) om aan de slag te gaan. Met Pod-beheerde identiteiten kunt u niet hand matig referenties voor het eerst definiëren. in plaats daarvan wordt een toegangs token in realtime aangevraagd en kan het worden gebruikt voor toegang tot de toegewezen services. In AKS zijn er twee onderdelen waarmee de bewerkingen kunnen worden uitgevoerd voor het gebruik van de beheerde identiteiten:

* **De node management Identity (NMI)-server** is een pod die als daemonset wordt uitgevoerd op elk knoop punt in het AKS-cluster. De NMI-server luistert naar pod-aanvragen voor Azure-Services.
* **De Azure-resource provider** voert een query uit op de Kubernetes API-server en controleert op een Azure Identity-toewijzing die overeenkomt met een pod.

Wanneer een Peul toegang tot een Azure-service vraagt, worden de netwerk regels het verkeer omgeleid naar de NMI-server (node management Identity). De NMI-server identificeert de peulen die toegang aanvragen tot Azure-Services op basis van hun externe adres en query's uitvoeren op de Azure-resource provider. De Azure resource-provider controleert op Azure Identity-toewijzingen in het AKS-cluster en de NMI-server vraagt vervolgens een toegangs token van Azure Active Directory (AD) op op basis van de identiteits toewijzing van pod. Azure AD biedt toegang tot de NMI-server, die wordt geretourneerd aan de pod. Dit toegangs token kan door de pod worden gebruikt om toegang tot services in azure te vragen.

In het volgende voor beeld maakt een ontwikkelaar een pod die gebruikmaakt van een beheerde identiteit om toegang aan te vragen bij Azure SQL Database:

![Met Pod-identiteiten kan een pod automatisch toegang tot andere services aanvragen](media/operator-best-practices-identity/pod-identities.png)

1. De cluster operator maakt eerst een service account dat kan worden gebruikt voor het toewijzen van identiteiten wanneer per peul toegang tot Services wordt aangevraagd.
1. De NMI-server wordt geïmplementeerd voor het door sturen van pod-aanvragen, samen met de Azure-resource provider, voor toegangs tokens voor Azure AD.
1. Een ontwikkelaar implementeert een pod met een beheerde identiteit die een toegangs token aanvraagt via de NMI-server.
1. Het token wordt geretourneerd naar de Pod en wordt gebruikt voor toegang tot Azure SQL Database

> [!NOTE]
> Pod-beheerde identiteiten bevindt zich momenteel in de preview-status.

Zie [Azure Active Directory pod-Managed Identities gebruiken in azure Kubernetes service (preview)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)voor het gebruik van pod-beheerde identiteiten.

## <a name="next-steps"></a>Volgende stappen

Dit artikel Best practices is gericht op verificatie en autorisatie voor uw cluster en bronnen. Raadpleeg de volgende artikelen voor meer informatie over het implementeren van een aantal van deze aanbevolen procedures:

* [Azure Active Directory integreren met AKS][aks-aad]
* [Azure Active Directory door Pod beheerde identiteiten gebruiken in azure Kubernetes service (preview)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

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
