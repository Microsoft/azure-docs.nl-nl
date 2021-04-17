---
title: Ondersteunde Kubernetes-versies in Azure Kubernetes Service
description: Inzicht in het ondersteuningsbeleid voor Kubernetes-versies en de levenscyclus van clusters in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/29/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: fac2eb75d210a34f4c5cd50c4649921aadfcd5ee
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588493"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Ondersteunde Kubernetes-versies in AKS (Azure Kubernetes Service)

De Kubernetes-community brengt ongeveer elke drie maanden kleine versies uit. Onlangs heeft de Kubernetes-community het ondersteuningsvenster voor elke versie verhoogd van 9 maanden naar [12 maanden](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/), te beginnen met versie 1.19. 

Secundaire versiereleases bevatten nieuwe functies en verbeteringen. Patchreleases komen vaker voor (soms wekelijks) en zijn bedoeld voor kritieke foutfixes binnen een secundaire versie. Patchreleases bevatten oplossingen voor beveiligingsproblemen of grote fouten.

## <a name="kubernetes-versions"></a>Kubernetes-versies

Kubernetes maakt voor elke versie gebruik [van het standaardversieschema voor Semantic Versioning:](https://semver.org/)

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Elk nummer in de versie geeft algemene compatibiliteit met de vorige versie aan:

* **Belangrijke versies veranderen wanneer** incompatibele API-updates of compatibiliteit met eerdere versies mogelijk worden verbroken.
* **Secundaire versies veranderen** wanneer er functionaliteitsupdates worden gemaakt die achterwaarts compatibel zijn met de andere kleine releases.
* **Patchversies** worden gewijzigd wanneer er met eerdere versies compatibele foutfixes worden gemaakt.

Richt u op het uitvoeren van de nieuwste patchrelade van de secundaire versie die u hebt. Uw productiecluster staat bijvoorbeeld op **`1.17.7`** . **`1.17.8`** is de nieuwste beschikbare patchversie die beschikbaar is voor de *1.17-serie.* U moet zo **`1.17.8`** snel mogelijk upgraden naar om ervoor te zorgen dat uw cluster volledig is gepatcht en ondersteund.

## <a name="kubernetes-version-support-policy"></a>Ondersteuningsbeleid voor Kubernetes-versies

AKS definieert een algemeen beschikbare versie als een versie die is ingeschakeld in alle SLO- of SLA-metingen en beschikbaar is in alle regio's. AKS ondersteunt drie ga-kleine versies van Kubernetes:

* De meest recente secundaire ga-versie die wordt uitgebracht in AKS (die we N noemen).
* Twee eerdere secundaire versies.
    * Elke ondersteunde secundaire versie ondersteunt ook maximaal twee (2) stabiele patches.

AKS biedt mogelijk ook ondersteuning voor preview-versies, die expliciet zijn gelabeld en onderhevig zijn aan [de preview-voorwaarden][preview-terms].

> [!NOTE]
> AKS maakt gebruik van veilige implementatiemethoden die een geleidelijke implementatie van regio's met zich mee brengen. Dit betekent dat het maximaal tien werkdagen kan duren voordat een nieuwe release of een nieuwe versie beschikbaar is in alle regio's.

Het ondersteunde venster van Kubernetes-versies op AKS staat bekend als 'N-2': (N (nieuwste versie) - 2 (secundaire versies)).

Als AKS bijvoorbeeld *1.17.a* introduceert, wordt er ondersteuning geboden voor de volgende versies:

Nieuwe secundaire versie    |    Lijst met ondersteunde versies
-----------------    |    ----------------------
1.17.a               |    1.17.a, 1.17.b, 1.16.c, 1.16.d, 1.15.e, 1.15.f

Waarbij '.letter' representatief is voor patchversies.

Wanneer er een nieuwe secundaire versie wordt geïntroduceerd, worden de oudste secundaire versie en ondersteunde patchreleases afgeschaft en verwijderd. De huidige lijst met ondersteunde versies is bijvoorbeeld:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

AKS brengt 1.18. uit, waardoor alle \* versies van 1.15. binnen 30 dagen niet \* meer worden ondersteund.

> [!NOTE]
> Als klanten een niet-ondersteunde Kubernetes-versie uitvoeren, wordt hen gevraagd om een upgrade uit te voeren wanneer ze ondersteuning voor het cluster aanvragen. Clusters waarop niet-ondersteunde Kubernetes-releases worden uitgevoerd, vallen niet onder [het AKS-ondersteuningsbeleid.](./support-policies.md)

Naast het bovenstaande ondersteunt AKS maximaal  twee patchreleases van een bepaalde secundaire versie. Dus gezien de volgende ondersteunde versies:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Als AKS en publiceert, worden de oudste patchversies afgeschaft en verwijderd en wordt de lijst met ondersteunde `1.17.9` `1.16.11` versies:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>Ondersteunde `kubectl` versies

U kunt ten opzichte van uw kube-apiserver-versie één secundaire versie gebruiken die ouder of nieuwer is, in overeenstemming met het `kubectl` [Kubernetes-ondersteuningsbeleid voor kubectl](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl). 

Als uw *kube-apiserver* bijvoorbeeld *1.17* heeft, kunt u versies *1.16* tot *1.18* van gebruiken met die `kubectl` *kube-apiserver*.

Voer uit om uw versie van te installeren of bij `kubectl` te `az aks install-cli` werken.

## <a name="release-and-deprecation-process"></a>Vrijgave- en afschaffingsproces

U kunt verwijzen naar toekomstige versiereleases en afschaffingen op de [AKS Kubernetes Release Calendar](#aks-kubernetes-release-calendar).

Voor nieuwe **secundaire** versies van Kubernetes:
  * AKS publiceert een voorafgaande aankondiging met de geplande datum van een nieuwe versierele release en de respectieve afschaffing van de oude versie op de opmerkingen bij de [AKS-release](https://aka.ms/aks/releasenotes) ten minste 30 dagen vóór de verwijdering.
  * AKS gebruikt [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) om gebruikers te waarschuwen als een nieuwe versie problemen in hun cluster veroorzaakt vanwege afgeschafte API's. Azure Advisor wordt ook gebruikt om de gebruiker te waarschuwen als deze momenteel niet meer wordt ondersteund.
  * AKS publiceert een statusmelding voor de [service](../service-health/service-health-overview.md) die beschikbaar is voor alle gebruikers met toegang tot AKS en de portal, en stuurt een e-mail naar de abonnementsbeheerders met de geplande verwijderingsdatums voor de versie.

    ````
    To find out who is your subscription administrators or to change it, please refer to [manage Azure subscriptions](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator).
    ````
  * Gebruikers hebben **30 dagen vanaf het** verwijderen van de versie tot een upgrade naar een ondersteunde release van een secundaire versie om ondersteuning te blijven ontvangen.

Voor nieuwe **patchversies** van Kubernetes:
  * Vanwege de urgente aard van patchversies kunnen deze worden geïntroduceerd in de service zodra ze beschikbaar komen.
  * Over het algemeen communiceert AKS niet breed over de release van nieuwe patchversies. AKS bewaakt en valideert echter voortdurend beschikbare CVE-patches om deze tijdig in AKS te ondersteunen. Als er een kritieke patch wordt gevonden of als er een gebruikersactie is vereist, zal AKS gebruikers op de hoogte stellen om een upgrade uit te voeren naar de nieuw beschikbare patch.
  * Gebruikers hebben **30 dagen vanaf het** verwijderen van een patchversie uit AKS om te upgraden naar een ondersteunde patch en ondersteuning te blijven ontvangen.

### <a name="supported-versions-policy-exceptions"></a>Ondersteunde beleids uitzonderingen voor versies

AKS behoudt zich het recht voor om nieuwe/bestaande versies toe te voegen of te verwijderen met een of meer kritieke fouten die invloed hebben op de productie of beveiligingsproblemen, zonder kennisgeving vooraf.

Specifieke patchreleases kunnen worden overgeslagen of versneld worden uitgebracht, afhankelijk van de ernst van de fout of het beveiligingsprobleem.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal en CLI-versies

Wanneer u een AKS-cluster implementeert in de portal of met de Azure CLI, wordt het cluster standaard ingesteld op de secundaire versie van N-1 en de meest recente patch. Als AKS bijvoorbeeld *1.17.a,* *1.17.b,* *1.16.c,* *1.16.d,* *1.15.e* en *1.15.f* ondersteunt, is de geselecteerde standaardversie *1.16.c.*

Als u wilt weten welke versies momenteel beschikbaar zijn voor uw abonnement en regio, gebruikt u [de opdracht az aks get-versions.][az-aks-get-versions] In het volgende voorbeeld worden de beschikbare Kubernetes-versies voor de regio *EASTUS* vermeld:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>Release-agenda van AKS Kubernetes

Zie [Kubernetes](https://en.wikipedia.org/wiki/Kubernetes#History)voor de eerdere releasegeschiedenis.

|  K8s-versie | Upstream-release  | AKS-preview  | AKS GA  | Einde van de levensduur |
|--------------|-------------------|--------------|---------|-------------|
| 1.18  | Mr. 23-20  | Mei 2020   | Aug 2020  | 1.21 GA | 
| 1,19  | 04-20 aug  | Sep 2020   | November 2020  | 1.22 GA | 
| 1,20  | Dec-08-20  | Jan 2021   | Maart 2021  | 1.23 GA |
| 1.21  | Apr-08-21 | Mei 2021   | Juni 2021  | 1.24 GA |



## <a name="faq"></a>Veelgestelde vragen

**Hoe stelt Microsoft mij op de hoogte van nieuwe Kubernetes-versies?**

Het AKS-team publiceert vooraankondigingen met geplande datums van de nieuwe Kubernetes-versies in onze documentatie, onze [GitHub](https://github.com/Azure/AKS/releases) en e-mailberichten naar abonnementsbeheerders die eigenaar zijn van clusters die niet meer worden ondersteund.  Naast aankondigingen maakt AKS ook gebruik van [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) om de klant te waarschuwen in Azure Portal om gebruikers te waarschuwen als ze niet meer worden ondersteund, en om hen te waarschuwen over afgeschafte API's die van invloed zijn op hun toepassing of ontwikkelingsproces. 

**Hoe vaak moet ik verwachten kubernetes-versies bij te werken om ondersteuning te blijven bieden?**

Vanaf Kubernetes 1.19 heeft de open source community de ondersteuning uitgebreid [naar 1 jaar](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/). AKS zet zich in voor het inschakelen van patches en ondersteuning voor het voldoen aan de upstream-toezeggingen. Voor AKS-clusters met versie 1.19 en hoger kunt u minimaal één keer per jaar een upgrade uitvoeren om een ondersteunde versie te blijven gebruiken. 

Voor versies op 1.18 of lager blijft het ondersteuningsvenster 9 maanden, waardoor een upgrade eenmaal per 9 maanden nodig is om een ondersteunde versie te blijven gebruiken. Test regelmatig nieuwe versies en wees voorbereid om te upgraden naar nieuwere versies om de nieuwste stabiele verbeteringen binnen Kubernetes vast te leggen.

**Wat gebeurt er wanneer een gebruiker een Upgrade uitvoeren voor een Kubernetes-cluster met een secundaire versie die niet wordt ondersteund?**

Als u versie *n-3* of ouder hebt, betekent dit dat u buiten ondersteuning bent en wordt gevraagd om een upgrade uit te voeren. Wanneer de upgrade van versie n-3 naar n-2 is geslaagd, bent u terug in ons ondersteuningsbeleid. Bijvoorbeeld:

- Als de oudste ondersteunde AKS-versie *1.15.a* is en u *1.14.b* of ouder hebt, bent u buiten ondersteuning.
- Wanneer u een upgrade van *1.14.b* naar *1.15.a* of hoger hebt uitgevoerd, bent u terug binnen ons ondersteuningsbeleid.

Downgrades worden niet ondersteund.

**Wat betekent 'Buiten ondersteuning'**

'Buiten ondersteuning' betekent dat:
* De versie die u wordt uitgevoerd, staat buiten de lijst met ondersteunde versies.
* U wordt gevraagd om het cluster bij te werken naar een ondersteunde versie wanneer u ondersteuning aanvraagt, tenzij u zich binnen de respijtperiode van 30 dagen na de afschaffing van de versie hebt. 

Bovendien maakt AKS geen runtime of andere garanties voor clusters buiten de lijst met ondersteunde versies.

**Wat gebeurt er wanneer een gebruiker een Kubernetes-cluster schaalt met een secundaire versie die niet wordt ondersteund?**

Voor secundaire versies die niet worden ondersteund door AKS, moet in- of uitschalen blijven werken. Omdat er geen enkele Quality of Service zijn, raden we u aan een upgrade uit te brengen om uw cluster weer te ondersteunen.

**Kan een gebruiker altijd een Kubernetes-versie blijven gebruiken?**

Als een cluster meer dan drie (3) secundaire versies niet meer wordt ondersteund en beveiligingsrisico's blijkt te hebben, neemt Azure proactief contact met u op om uw cluster bij te werken. Als u geen verdere actie onderneemt, behoudt Azure zich het recht voor om uw cluster automatisch namens u te upgraden.

**Welke versie wordt door het besturingsvlak ondersteund als de knooppuntgroep zich niet in een van de ondersteunde AKS-versies?**

Het besturingsvlak moet zich binnen een venster van versies van alle knooppuntgroepen. Ga naar documentatie over het upgraden van knooppuntgroepen voor meer informatie over het upgraden van de besturingsvlak of [knooppuntgroepen.](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)

**Kan ik meerdere AKS-versies overslaan tijdens het upgraden van het cluster?**

Wanneer u een ondersteund AKS-cluster upgradet, kunnen secundaire Kubernetes-versies niet worden overgeslagen. Bijvoorbeeld upgrades tussen:
  * *1.12.x*  ->  *1.13.x:* toegestaan.
  * *1.13.x*  ->  *1.14.x:* toegestaan.
  * *1.12.x*  ->  *1.14.x:* niet toegestaan.

Als u wilt *upgraden van 1.12.x*  ->  *1.14.x:*
1. Upgrade van *1.12.x*  ->  *1.13.x.*
1. Upgrade van *1.13.x*  ->  *1.14.x.*

Het overslaan van meerdere versies kan alleen worden uitgevoerd bij het upgraden van een niet-ondersteunde versie naar een ondersteunde versie. U kunt bijvoorbeeld upgraden van een niet-ondersteunde *versie 1.10.x* naar een *ondersteunde versie van 1.15.x.*

**Kan ik een nieuw 1.xx.x-cluster maken tijdens het ondersteuningsvenster van 30 dagen?**

Nee. Zodra een versie is afgeschaft/verwijderd, kunt u geen cluster maken met die versie. Als de wijziging wordt uitgevoerd, ziet u dat de oude versie uit uw versielijst is verwijderd. Dit proces kan maximaal twee weken duren na de aankondiging, progressief per regio.

**Ik heb een afgeschafte versie. Kan ik nog steeds nieuwe knooppuntgroepen toevoegen? Of moet ik een upgrade uitvoeren?**

Nee. Het is niet toegestaan om knooppuntgroepen van de afgeschafte versie aan uw cluster toe te voegen. U kunt knooppuntgroepen van een nieuwe versie toevoegen. Hiervoor moet u mogelijk echter eerst het besturingsvlak bijwerken. 

## <a name="next-steps"></a>Volgende stappen

Zie Upgrade an Azure Kubernetes Service cluster [(AKS) (Een AKS-cluster upgraden) voor][aks-upgrade]meer informatie over het upgraden van uw cluster.

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
