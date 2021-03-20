---
title: Best practices voor ontwikkel aars-pod Security in azure Kubernetes Services (AKS)
description: Meer informatie over de aanbevolen procedures voor ontwikkel aars voor het beveiligen van Peul in azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: 1c7143b6d3479cf3083cfc730301c68dcf4eb705
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92900819"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor pod-beveiliging in azure Kubernetes service (AKS)

Als u toepassingen ontwikkelt en uitvoert in azure Kubernetes service (AKS), is de beveiliging van uw peul een belang rijke overweging. Uw toepassingen moeten worden ontworpen voor het principe van het minst vereiste aantal bevoegdheden. Het is belang rijk om persoonlijke gegevens veilig te houden. U wilt niet dat referenties zoals database verbindings reeksen, sleutels of geheimen en certificaten die worden blootgesteld aan de buiten wereld waar een aanvaller kan profiteren van deze geheimen voor schadelijke doel einden. Voeg ze niet toe aan uw code of sluit ze in op uw container installatie kopieën. Deze aanpak maakt een risico voor bloot stelling en beperkt de mogelijkheid om deze referenties te draaien, omdat de container installatie kopieën opnieuw moeten worden opgebouwd.

Dit artikel Best practices is gericht op het beveiligen van AKS. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Pod-beveiligings context gebruiken om de toegang tot processen en services of bevoegdheids escalatie te beperken
> * Verifiëren met andere Azure-resources met behulp van door Pod beheerde identiteiten
> * Referenties aanvragen en ophalen van een digitale kluis, zoals Azure Key Vault

U kunt ook de aanbevolen procedures voor [cluster beveiliging][best-practices-cluster-security] lezen en voor [container installatie kopie beheer][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Pod toegang tot bronnen beveiligen

**Richt lijnen voor best practices** : als u wilt uitvoeren als een andere gebruiker of groep en de toegang tot de onderliggende knooppunt processen en-services wilt beperken, definieert u pod security context Settings. Wijs het minste vereiste aantal bevoegdheden toe.

Als u wilt dat uw toepassingen correct worden uitgevoerd, moet het Peul worden uitgevoerd als een gedefinieerde gebruiker of groep en niet als *basis*. `securityContext`Met de for a Pod of container kunt u instellingen zoals *RunAsUser* of *fsGroup* definiëren om de juiste machtigingen aan te nemen. Wijs alleen de vereiste machtigingen van de gebruiker of groep toe en gebruik de beveiligings context niet als middel om aanvullende machtigingen te nemen. De instellingen voor *runAsUser*, bevoegdheids escalatie en andere Linux-capaciteiten zijn alleen beschikbaar op Linux-knoop punten en peulen.

Wanneer u als niet-hoofd gebruiker wordt uitgevoerd, kunnen containers niet worden gebonden aan de geprivilegieerde poorten onder 1024. In dit scenario kan Kubernetes services worden gebruikt om het feit te verhullen dat een app wordt uitgevoerd op een bepaalde poort.

Een pod-beveiligings context kan ook aanvullende mogelijkheden of machtigingen voor het verkrijgen van toegang tot processen en services definiëren. De volgende algemene beveiligings context definities kunnen worden ingesteld:

* **allowPrivilegeEscalation** definieert of de pod de *root* -bevoegdheden kan aannemen. Ontwerp uw toepassingen zodat deze instelling altijd is ingesteld op *Onwaar*.
* Met de **Linux-mogelijkheden** kan pod toegang krijgen tot onderliggende knooppunt processen. Wees voorzichtig met het toewijzen van deze mogelijkheden. Wijs het minste aantal benodigde bevoegdheden toe. Zie [Linux-mogelijkheden][linux-capabilities]voor meer informatie.
* **Selinux labels** is een Linux-kernel-beveiligings module waarmee u toegangs beleid kunt definiëren voor services, processen en toegang tot het bestands systeem. Wijs opnieuw het minste aantal benodigde bevoegdheden toe. Zie [selinux-opties in Kubernetes][selinux-labels] voor meer informatie.

In het volgende voor beeld wordt pod YAML-manifest ingesteld op het definiëren van beveiligings context instellingen:

* Pod wordt uitgevoerd als gebruikers-ID *1000* en deel van groeps-id *2000*
* Kan geen bevoegdheden escaleren voor gebruik `root`
* Biedt Linux-mogelijkheden toegang tot netwerk interfaces en de real-time (hardware)-klok van de host

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    fsGroup: 2000
  containers:
    - name: security-context-demo
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        runAsUser: 1000
        allowPrivilegeEscalation: false
        capabilities:
          add: ["NET_ADMIN", "SYS_TIME"]
```

Werk samen met uw cluster operator om te bepalen welke beveiligings context instellingen u nodig hebt. Probeer uw toepassingen te ontwerpen om extra machtigingen te minimaliseren en de toegang tot de Pod vereist. Er zijn aanvullende beveiligings functies om de toegang te beperken met behulp van AppArmor en seccomp (veilig Computing) die kunnen worden geïmplementeerd door cluster operators. Zie [Secure container Access to resources][apparmor-seccomp](Engelstalig) voor meer informatie.

## <a name="limit-credential-exposure"></a>Blootstelling van de referentie beperken

**Richt lijnen voor best practices** : Geef geen referenties in de toepassings code op. Gebruik beheerde identiteiten voor Azure-resources om ervoor te zorgen dat uw Pod toegang tot andere bronnen kan aanvragen. Een digitale kluis, zoals Azure Key Vault, moet ook worden gebruikt om digitale sleutels en referenties op te slaan en op te halen. Beheerde identiteiten van pod zijn alleen bedoeld voor gebruik met Linux-en container installatie kopieën.

Vermijd het gebruik van vaste of gedeelde referenties om het risico te beperken dat de referenties worden weer gegeven in de toepassings code. Referenties of sleutels mogen niet rechtstreeks in uw code worden opgenomen. Als deze referenties worden weer gegeven, moet de toepassing worden bijgewerkt en opnieuw worden geïmplementeerd. Een betere benadering is het opgeven van een eigen identiteit en manier om zichzelf te verifiëren of automatisch referenties op te halen van een digitale kluis.

### <a name="use-azure-container-compute-upstream-projects"></a>De upstream-projecten van Azure container Compute gebruiken

> [!IMPORTANT]
> Gerelateerde AKS open-source projecten worden niet ondersteund door de technische ondersteuning van Azure. Ze worden door gebruikers verschaft om zelf in clusters te worden geïnstalleerd en kunnen feedback van onze community verzamelen.

Met de volgende [gekoppelde open-source projecten van AKS][aks-associated-projects] kunt u automatisch peul verifiëren of referenties en sleutels van een digitale kluis aanvragen. Deze projecten worden onderhouden door het Azure container Compute upstream-team en maken deel uit van een [breder overzicht van projecten die beschikbaar zijn voor gebruik](https://github.com/Azure/container-compute-upstream/blob/master/README.md#support).

 * [Pod-identiteit van Azure Active Directory][aad-pod-identity]
 * [Azure Key Vault provider voor geheimen Store CSI-stuur programma](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)

#### <a name="use-pod-managed-identities"></a>Door Pod beheerde identiteiten gebruiken

Met een beheerde identiteit voor Azure-resources kan een pod zichzelf verifiëren voor Azure-Services die dit ondersteunen, zoals opslag of SQL. Aan de Pod wordt een Azure-identiteit toegewezen waarmee ze kunnen worden geverifieerd voor Azure Active Directory en een digitaal token kan worden ontvangen. Dit digitale token kan worden gepresenteerd aan andere Azure-Services die controleren of de Pod is gemachtigd om toegang te krijgen tot de service en de vereiste acties uit te voeren. Deze aanpak houdt in dat er geen geheimen vereist zijn voor database verbindings reeksen, bijvoorbeeld. De vereenvoudigde werk stroom voor pod beheerde identiteit wordt weer gegeven in het volgende diagram:

:::image type="content" source="media/developer-best-practices-pod-security/basic-pod-identity.svg" alt-text="Vereenvoudigde werk stroom voor pod beheerde identiteit in azure":::

Met een beheerde identiteit hoeft uw toepassings code geen referenties op te geven voor toegang tot een service, zoals Azure Storage. Elke pod verifieert met zijn eigen identiteit, zodat u de toegang kunt controleren en controleren. Als uw toepassing verbinding maakt met andere Azure-Services, moet u beheerde identiteiten gebruiken om het hergebruik van referenties en het risico op bloot stelling te beperken.

Zie [Configure a AKS cluster to use pod Managed Identities and with your Applications][aad-pod-identity] (Engelstalig) voor meer informatie over pod-identiteiten.

#### <a name="use-azure-key-vault-with-secrets-store-csi-driver"></a>Azure Key Vault gebruiken met geheimen Store CSI-stuur programma

Met behulp van het Pod-identiteits project kan verificatie worden uitgevoerd op ondersteuning van Azure-Services. Voor uw eigen services of toepassingen zonder beheerde identiteiten voor Azure-resources kunt u nog steeds verifiëren met behulp van referenties of sleutels. Een digitale kluis kan worden gebruikt om deze geheime inhoud op te slaan.

Wanneer toepassingen een referentie nodig hebben, kunnen ze communiceren met de digitale kluis, de laatste geheime inhoud ophalen en vervolgens verbinding maken met de vereiste service. Azure Key Vault kan deze digitale kluis zijn. De vereenvoudigde werk stroom voor het ophalen van een referentie van Azure Key Vault met behulp van pod Managed Identities wordt weer gegeven in het volgende diagram:

:::image type="content" source="media/developer-best-practices-pod-security/basic-key-vault.svg" alt-text="Vereenvoudigde werk stroom voor het ophalen van een referentie van Key Vault met behulp van een door Pod beheerde identiteit":::

Met Key Vault kunt u geheimen, zoals referenties, opslag account sleutels of certificaten, opslaan en regel matig draaien. U kunt Azure Key Vault integreren met een AKS-cluster met behulp [van de Azure Key Vault provider voor het stuur programma geheimen Store CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage). Met het stuur programma voor geheimen Store CSI kan het AKS-cluster systeem eigen geheime inhoud ophalen uit Key Vault en deze alleen veilig aanbieden aan de aanvraag-pod. Werk samen met uw cluster operator om de geheimen Store CSI-stuur programma te implementeren op AKS worker-knoop punten. U kunt een beheerde pod-id gebruiken om toegang aan te vragen voor Key Vault en de geheime inhoud op te halen die nodig is via het stuur programma geheimen opslaan CSI.

Azure Key Vault met geheimen Store CSI stuur programma kan worden gebruikt voor Linux-knoop punten en peulen waarvoor een Kubernetes-versie van 1,16 of hoger is vereist. Voor Windows-knoop punten is een Kubernetes-versie van 1,18 of hoger vereist.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is gericht op hoe u uw peul kunt beveiligen. Raadpleeg de volgende artikelen voor meer informatie over het implementeren van sommige van deze gebieden:

* [Beheerde identiteiten gebruiken voor Azure-resources met AKS][aad-pod-identity]
* [Azure Key Vault integreren met AKS][aks-keyvault-csi-driver]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#selinuxoptions-v1-core
[aks-associated-projects]: https://awesomeopensource.com/projects/aks?categoryPage=11

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
