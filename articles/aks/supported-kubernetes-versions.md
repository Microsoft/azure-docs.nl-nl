---
title: Ondersteunde Kubernetes-versies in Azure Kubernetes Service
description: Meer informatie over het ondersteunings beleid voor Kubernetes-versies en de levens cyclus van clusters in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 03/29/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: ba75e11a067a257c659f8c659f68bb2bba6fa2e0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012082"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Ondersteunde Kubernetes-versies in AKS (Azure Kubernetes Service)

De Kubernetes-Community brengt ongeveer elke drie maanden secundaire versies uit. Onlangs heeft de Kubernetes-community [het ondersteunings venster voor elke versie van 9 maanden naar 12 maanden verhoogd, te](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/)beginnen met versie 1,19. 

Kleine versie releases bevatten nieuwe functies en verbeteringen. Patch releases zijn vaker (soms wekelijks) en zijn bedoeld voor essentiële probleem oplossingen binnen een secundaire versie. Patch releases bevatten oplossingen voor beveiligings problemen of grote fouten.

## <a name="kubernetes-versions"></a>Kubernetes-versies

Kubernetes maakt gebruik van het standaard versie beheer schema van [semantische versie](https://semver.org/) voor elke versie:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Elk nummer in de versie geeft algemene compatibiliteit met de vorige versie aan:

* De **belangrijkste versies** worden gewijzigd wanneer incompatibele API-updates of achterwaartse compatibiliteit mogelijk wordt verbroken.
* **Secundaire versies** worden gewijzigd wanneer er functionaliteits updates worden gemaakt die achterwaarts compatibel zijn met de andere secundaire releases.
* **Patch versies** worden gewijzigd wanneer er achterwaarts compatibele fout oplossingen worden uitgevoerd.

Voer de nieuwste patch versie van de secundaire versie uit die u uitvoert. Uw productie cluster is bijvoorbeeld ingeschakeld **`1.17.7`** . **`1.17.8`** is de meest recente beschik bare patch versie beschikbaar voor de *1,17* -serie. U moet **`1.17.8`** zo snel mogelijk een upgrade uitvoeren naar de volledige patch naar uw cluster en worden ondersteund.

## <a name="kubernetes-version-support-policy"></a>Ondersteunings beleid voor Kubernetes-versie

AKS definieert een algemeen beschik bare versie als een versie die is ingeschakeld in alle SLO-of SLA-metingen en beschikbaar in alle regio's. AKS ondersteunt drie GA secundaire versies van Kubernetes:

* De meest recente GA secundaire versie die is uitgebracht in AKS (waarnaar wordt verwezen als N).
* Twee vorige secundaire versies.
    * Elke ondersteunde secundaire versie ondersteunt ook Maxi maal twee (2) stabiele patches.

AKS kan ook ondersteuning bieden voor Preview-versies, die expliciet worden gelabeld en onderworpen aan de voor waarden van de [Preview][preview-terms]-versie.

> [!NOTE]
> AKS maakt gebruik van veilige implementatie procedures waarbij een geleidelijke implementatie van regio's betrokken is. Dit betekent dat het Maxi maal 10 werk dagen kan duren voordat een nieuwe release of een nieuwe versie beschikbaar is in alle regio's.

Het ondersteunde venster van Kubernetes-versies op AKS staat bekend als ' N-2 ': (N (meest recente versie)-2 (secundaire versies)).

Als bijvoorbeeld AKS introduceert *1.17. a* vandaag, wordt ondersteuning geboden voor de volgende versies:

Nieuwe secundaire versie    |    Lijst met ondersteunde versies
-----------------    |    ----------------------
1.17. a               |    1.17. a, 1.17. b, 1.16. c, 1.16. d, 1.15. e, 1.15. f

Waarbij '. letter ' representatief is voor patch versies.

Wanneer een nieuwe secundaire versie wordt geïntroduceerd, worden de oudste secundaire versie-en patch releases die worden ondersteund, afgeschaft en verwijderd. De lijst met huidige ondersteunde versies is bijvoorbeeld:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

AKS Releases 1,18. \* , waarbij alle 1,15. \* versies van de ondersteuning in 30 dagen worden verwijderd.

> [!NOTE]
> Als klanten een niet-ondersteunde Kubernetes-versie uitvoeren, wordt gevraagd om een upgrade uit te voeren bij het aanvragen van ondersteuning voor het cluster. Clusters met niet-ondersteunde Kubernetes-releases vallen niet onder het [beleid voor AKS-ondersteuning](./support-policies.md).

Naast het bovenstaande, ondersteunt AKS Maxi maal twee **patch** releases van een bepaalde secundaire versie. Hiertoe worden de volgende ondersteunde versies vermeld:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Als AKS wordt vrijgegeven `1.17.9` en `1.16.11` , worden de oudste patch versies afgeschaft en verwijderd, en wordt de lijst met ondersteunde versies:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>Ondersteunde `kubectl` versies

U kunt één secundaire versie ouder of hoger van `kubectl` ten opzichte van uw *uitvoeren-apiserver-* versie gebruiken, consistent met het [ondersteunings beleid voor Kubernetes voor kubectl](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl).

Als uw *uitvoeren-apiserver* bijvoorbeeld *1,17* is, kunt u versie *1,16* gebruiken tot *1,18* van `kubectl` die *uitvoeren-apiserver*.

Voer uit om de versie van te installeren of bij te werken `kubectl` `az aks install-cli` .

## <a name="release-and-deprecation-process"></a>Vrijgave-en afschaffing proces

U kunt naar aanstaande release-en afschaffing-versies van de [AKS-Kubernetes](#aks-kubernetes-release-calendar).

Voor nieuwe **secundaire** versies van Kubernetes:
  * AKS publiceert een pre-aankondiging met de geplande datum van de release van een nieuwe versie en de definitieve afschaffing van een oude versie in de opmerkingen bij de [AKS-release](https://aka.ms/aks/releasenotes) ten minste 30 dagen vóór de verwijdering.
  * AKS publiceert een [service status melding](../service-health/service-health-overview.md) die beschikbaar is voor alle gebruikers met toegang tot AKS en Portal en stuurt een e-mail naar de abonnements beheerders met de geplande versie van de verwijderings datum.

    ````
    To find out who is your subscription administrators or to change it, please refer to [manage Azure subscriptions](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator).
    ````
  * Gebruikers beschikken over **30 dagen** vanaf het verwijderen van de versie om een upgrade naar een ondersteunde versie van de secundaire release te kunnen blijven ontvangen.

Voor nieuwe **patch** versies van Kubernetes:
  * Vanwege de dringende aard van patch versies, kunnen ze worden geïntroduceerd in de service zodra deze beschikbaar komen.
  * Over het algemeen communiceert AKS de release van nieuwe patch versies niet breed. De beschik bare CVE-patches worden door AKS voortdurend gecontroleerd en gevalideerd om ze in AKS op tijd te ondersteunen. Als er een kritieke patch wordt gevonden of als de gebruiker actie vereist is, waarschuwt AKS gebruikers om een upgrade uit te dienen naar de nieuwe beschik bare patch.
  * Gebruikers hebben **30 dagen** vanaf het verwijderen van een patch release van aks een upgrade naar een ondersteunde patch en blijven ondersteuning ontvangen.

### <a name="supported-versions-policy-exceptions"></a>Ondersteunde versies beleids uitzonderingen

AKS behoudt zich het recht voor om nieuwe/bestaande versies toe te voegen of te verwijderen met een of meer essentiële bugs of beveiligings problemen die invloed hebben op de productie zonder voorafgaande kennisgeving.

Specifieke patch releases kunnen worden overgeslagen of de implementatie wordt versneld, afhankelijk van de ernst van de fout of het beveiligings probleem.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal-en CLI-versies

Wanneer u een AKS-cluster implementeert in de portal of met de Azure CLI, wordt het cluster standaard ingesteld op de N-1 secundaire versie en de meest recente patch. Als AKS bijvoorbeeld *1.17. a*, *1.17. b*, *1.16. c*, *1.16. d*, *1.15. e* en *1.15. f* ondersteunt, is de geselecteerde standaard versie *1.16. c*.

Gebruik de opdracht [AZ AKS Get-verse][az-aks-get-versions] voor meer informatie over welke versies momenteel beschikbaar zijn voor uw abonnement en regio. In het volgende voor beeld ziet u een lijst met de beschik bare Kubernetes-versies voor de regio *oostus* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>Release kalender voor AKS-Kubernetes

Zie [Kubernetes](https://en.wikipedia.org/wiki/Kubernetes#History)voor de eerdere release geschiedenis.

|  K8s-versie | Upstream-release  | AKS preview  | AKS GA  | Einde van de levens duur |
|--------------|-------------------|--------------|---------|-------------|
| 1,17  | Dec-09-19  | Jan 2019   | Jul 2020  | 1,20 GA | 
| 1,18  | Mrt-23-20  | Mei 2020   | Aug 2020  | 1,21 GA | 
| 1,19  | Aug-04-20  | Sep 2020   | Nov 2020  | 1,22 GA | 
| 1,20  | Dec-08-20  | Jan 2021   | Mrt 2021  | 1,23 GA |
| 1,21  | Apr-08-21 * | Mei 2021   | Jun 2021  | 1,24 GA |

\* De Kubernetes 1,21-upstream-release kan worden gewijzigd als de upstream-kalender als die nog moet worden voltooid.


## <a name="faq"></a>Veelgestelde vragen

**Hoe vaak moet ik verwachten dat Kubernetes-versies worden bijgewerkt om ondersteuning te blijven bieden?**

Vanaf Kubernetes 1,19 [is de open source-community uitgebreide ondersteuning voor 1 jaar](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/). AKS doorvoer bewerkingen voor het inschakelen van patches en ondersteuning die overeenkomen met de upstream-toezeg gingen. Voor AKS-clusters op 1,19 en hoger kunt u een upgrade uitvoeren van ten minste één keer per jaar om op een ondersteunde versie te blijven. 

Voor versies op 1,18 of lager blijft het venster met ondersteuning tot 9 maanden, waardoor een upgrade een keer per 9 maanden vereist om op een ondersteunde versie te blijven. Test regel matig nieuwe versies en bereid u voor op een upgrade naar nieuwere versies om de laatste stabiele verbeteringen in Kubernetes vast te leggen.

**Wat gebeurt er wanneer een gebruiker een Kubernetes-cluster upgradet met een secundaire versie die niet wordt ondersteund?**

Als u een *n-3-* versie of ouder hebt, betekent dit dat u buiten de ondersteuning bent en wordt u gevraagd om te upgraden. Als uw upgrade van versie n-3 naar n-2 slaagt, wordt u teruggeleid binnen het ondersteunings beleid. Bijvoorbeeld:

- Als de oudste ondersteunde AKS-versie *1.15. a* is en u zich op *1.14. b* of ouder bevindt, hebt u geen ondersteuning meer.
- Wanneer u een upgrade uitvoert van *1.14. b* naar *1.15. a* of hoger, bent u terug in ons ondersteunings beleid.

Downgrades worden niet ondersteund.

**Wat betekent ' buiten het ondersteunings team '**

Buiten de ondersteunings middelen houdt in dat:
* De versie die u uitvoert, bevindt zich buiten de lijst met ondersteunde versies.
* U wordt gevraagd om het cluster bij te werken naar een ondersteunde versie bij het aanvragen van ondersteuning, tenzij u zich binnen de respijt periode van 30 dagen bevindt na de versie van afschaffing. 

Daarnaast doen AKS geen runtime of andere garanties voor clusters buiten de lijst met ondersteunde versies.

**Wat gebeurt er wanneer een gebruiker een Kubernetes-cluster schaalt met een secundaire versie die niet wordt ondersteund?**

Voor secundaire versies die niet worden ondersteund door AKS, moet u de schaal in-of uitschalen. Omdat er geen Quality of Service garanties zijn, wordt u aangeraden om een upgrade uit te voeren om uw cluster weer te ondersteunen.

**Kan een gebruiker een Kubernetes-versie permanent blijven?**

Als er meer dan drie (3) secundaire versies van een cluster worden ondersteund en er beveiligings Risico's zijn gevonden, neemt Azure proactief contact met u op om uw cluster bij te werken. Als u geen verdere actie onderneemt, behoudt Azure het recht om uw cluster automatisch namens u bij te werken.

**Welke versie van het besturings element wordt ondersteund als de knooppunt groep zich niet in een van de ondersteunde AKS-versies bevindt?**

Het besturings vlak moet zich in een venster met versies van alle knooppunt groepen bevinden. Ga voor meer informatie over het bijwerken van het besturings vlak of de knooppunt groepen naar documentatie over het [upgraden van knooppunt groepen](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

**Kan ik meerdere AKS-versies overs Laan tijdens de upgrade van het cluster?**

Wanneer u een ondersteund AKS-cluster bijwerkt, kunnen secundaire versies van Kubernetes niet worden overgeslagen. Bijvoorbeeld upgrades tussen:
  * *1.12. x*  ->  *1.13. x*: toegestaan.
  * *1.13. x*  ->  *1.14. x*: toegestaan.
  * *1.12. x*  ->  *1.14. x*: niet toegestaan.

Een upgrade uitvoeren van *1.12. x*  ->  *1.14. x*:
1. Voer een upgrade uit van *1.12. x*  ->  *1.13. x*.
1. Voer een upgrade uit van *1.13. x*  ->  *1.14. x*.

Het overs laan van meerdere versies kan alleen worden uitgevoerd wanneer u een upgrade uitvoert van een niet-ondersteunde versie terug naar een ondersteunde versie. U kunt bijvoorbeeld een upgrade uitvoeren van een niet-ondersteunde *1,10. x* naar een ondersteunde *1.15. x*.

**Kan ik een nieuw cluster van 1. xx. x maken tijdens het ondersteunings venster van 30 dagen?**

Nee. Zodra een versie is afgeschaft/verwijderd, kunt u geen cluster maken met die versie. Wanneer de wijziging wordt doorgevoerd, ziet u de oude versie die is verwijderd uit de lijst met versies. Dit proces kan Maxi maal twee weken duren vanaf aankondiging, progressief per regio.

**Ik ben aan een vers afgeschafte versie. kan ik nog steeds nieuwe knooppunt groepen toevoegen? Of moet ik een upgrade uitvoeren?**

Nee. U kunt geen knooppunt groepen van de afgeschafte versie toevoegen aan uw cluster. U kunt knooppunt groepen van een nieuwe versie toevoegen. Het is echter mogelijk dat u het besturings vlak eerst moet bijwerken. 

## <a name="next-steps"></a>Volgende stappen

Zie [een Azure Kubernetes service-cluster (AKS) bijwerken][aks-upgrade]voor meer informatie over het upgraden van uw cluster.

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
