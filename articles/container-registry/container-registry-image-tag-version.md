---
title: Aanbevolen procedures voor afbeeldings codes
description: Aanbevolen procedures voor het labelen en versie beheer van docker-container installatie kopieën bij het pushen van installatie kopieën naar en het ophalen van installatie kopieën uit een Azure container Registry
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: b483317960409fe1fbea181706f12375606fe659
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75445735"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Aanbevelingen voor het labelen en versie beheer van container installatie kopieën

Wanneer u container installatie kopieën naar een container register pusht en vervolgens implementeert, hebt u een strategie nodig voor het labelen van afbeeldingen en versie beheer. In dit artikel worden twee benaderingen beschreven, waarbij elk in de levens cyclus van de container past:

* **Stabiele Tags** : Tags die u opnieuw gebruikt, bijvoorbeeld om een primaire of secundaire versie aan te geven, zoals *mycontainerimage: 1.0*.
* **Unieke labels** : een andere tag voor elke installatie kopie die u naar een REGI ster pusht, zoals *mycontainerimage: abc123*.

## <a name="stable-tags"></a>Stabiele Tags

**Aanbeveling**: gebruik stabiele Tags voor het onderhouden van **basis installatie kopieën** voor uw container builds. Vermijd implementaties met stabiele Tags, omdat deze Tags updates blijven ontvangen en inconsistenties kunnen introduceren in productie omgevingen.

*Stabiele Tags* betekenen een ontwikkelaar of een build-systeem en kunnen een specifieke tag blijven ophalen, waardoor updates worden voortgezet. Stabiel betekent niet dat de inhoud is bevroren. In plaats daarvan houdt dat in dat de afbeelding stabiel moet zijn voor de bedoeling van die versie. Om "stabiel" te blijven staan, kan het zijn dat er beveiligings patches of Framework-updates worden toegepast.

### <a name="example"></a>Voorbeeld

Een framework-team verzendt versie 1,0. Ze weten dat ze updates kunnen verzenden, met inbegrip van kleine updates. Om stabiele Tags te ondersteunen voor een primaire en secundaire versie, hebben ze twee sets stabiele Tags.

* `:1` : een stabiele tag voor de primaire versie. `1` vertegenwoordigt de versie ' nieuw ' of ' nieuwste ' 1. *.
* `:1.0`-een stabiele tag voor versie 1,0, waardoor een ontwikkelaar verbinding kan maken met de updates van 1,0, en niet wordt doorgevoerd naar 1,1 wanneer deze wordt uitgebracht.

Het team gebruikt ook het `:latest` label, dat verwijst naar de laatste stabiele tag, ongeacht wat de huidige primaire versie is.

Als er updates voor de basis installatie kopie beschikbaar zijn of een type onderhouds release van het Framework, worden installatie kopieën met de stabiele Tags bijgewerkt naar de nieuwste Digest die de meest actuele stabiele versie van de desbetreffende release vertegenwoordigt.

In dit geval worden de primaire en secundaire Tags voortdurend onderhouden. Met een basis installatie kopie scenario kan de eigenaar van de afbeelding geservicede installatie kopieën aanbieden.

### <a name="delete-untagged-manifests"></a>Niet-gecodeerde manifesten verwijderen

Als een afbeelding met een stabiele tag wordt bijgewerkt, is de eerder gelabelde afbeelding niet-gelabeld, wat resulteert in een zwevende afbeelding. Het manifest van de vorige afbeelding en de unieke laag gegevens blijven in het REGI ster. Als u de register grootte wilt behouden, kunt u regel matig niet-gecodeerde manifesten verwijderen die het resultaat zijn van het bijwerken van stabiele beelden. U kunt bijvoorbeeld niet-gecodeerde manifesten die ouder zijn dan een opgegeven duur [automatisch opschonen](container-registry-auto-purge.md) of een [Bewaar beleid](container-registry-retention-policy.md) voor niet-gecodeerde manifesten instellen.

## <a name="unique-tags"></a>Unieke labels

**Aanbeveling**: Gebruik unieke labels voor **implementaties**, met name in een omgeving die kan worden geschaald op meerdere knoop punten. Waarschijnlijk wilt u opzettelijk implementaties van een consistente versie van onderdelen. Als uw container opnieuw wordt opgestart of als een Orchestrator meer exemplaren uitbreidt, halen uw hosts niet per ongeluk een nieuwere versie op, inconsistent met de andere knoop punten.

Unieke labels betekenen gewoon dat elke afbeelding die naar een REGI ster is gepusht, een unieke tag heeft. Tags worden niet opnieuw gebruikt. Er zijn verschillende patronen die u kunt volgen om unieke labels te genereren, waaronder:

* **Datum/tijds tempel** : deze benadering is tamelijk gebruikelijk, omdat u duidelijk kunt zien wanneer de installatie kopie is gemaakt. Maar hoe kan ik deze weer correleren aan uw build-systeem? Moet u de build vinden die op hetzelfde moment is voltooid? In welke tijd zone bevindt u zich? Zijn al uw bouw systemen gekalibreerd tot UTC?
* **Git door voeren**  : deze aanpak werkt tot u begint met de ondersteuning van basis installatie kopie-updates. Als er een update voor de basis installatie kopie plaatsvindt, wordt uw build-systeem met dezelfde Git-doorvoer als de vorige build gestart. De basis installatie kopie heeft echter nieuwe inhoud. In het algemeen biedt een Git-doorvoer een *semi*-stabiele tag.
* **Manifest Digest** : elke container installatie kopie die naar een container register is gepusht, is gekoppeld aan een manifest, geïdentificeerd door een unieke SHA-256-Hash, of Digest. Hoewel uniek, is de samen vatting lang, moeilijk te lezen en niet-gecorreleerd met uw build-omgeving.
* **Build-id** : deze optie is mogelijk het beste omdat deze waarschijnlijk incrementeel is, en Hiermee kunt u weer correleren met de specifieke build om alle artefacten en logboeken te vinden. Net als bij een samen vatting van een manifest is het echter mogelijk moeilijk te lezen.

  Als uw organisatie verschillende build-systemen heeft, wordt het voor voegsel van de tag met de naam van het build-systeem een variant voor deze optie: `<build-system>-<build-id>` . U kunt bijvoorbeeld samen stellingen onderscheiden van het Jenkins build-systeem van het API-team en het build-systeem van Azure pipelines van het webteam.

### <a name="lock-deployed-image-tags"></a>Geïmplementeerde afbeeldings Tags vergren delen

Als best practice wordt u aangeraden de code van de geïmplementeerde installatie kopie te [vergren delen](container-registry-image-lock.md) door het `write-enabled` kenmerk ervan in te stellen op `false` . In deze oefening wordt voor komen dat u per ongeluk een installatie kopie uit het REGI ster verwijdert en mogelijk uw implementaties verstoort. U kunt de vergrendelings stap in uw release pijplijn toevoegen.

Als u een geïmplementeerde installatie kopie vergrendelt, kunt u andere, niet-geïmplementeerde installatie kopieën uit het REGI ster verwijderen met Azure Container Registry-functies om uw REGI ster te onderhouden. U kunt bijvoorbeeld niet-gelabelde manifesten of niet-vergrendelde installatie kopieën die ouder zijn dan een opgegeven duur [automatisch opschonen](container-registry-auto-purge.md) of een [Bewaar beleid](container-registry-retention-policy.md) voor niet-gecodeerde manifesten instellen.

## <a name="next-steps"></a>Volgende stappen

Voor een gedetailleerde beschrijving van de concepten in dit artikel, raadpleegt u de blog post [docker tagging: Aanbevolen procedures voor het labelen en versie beheer van docker-installatie kopieën](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/).

Zie [Aanbevolen procedures voor Azure container Registry voor](container-registry-best-practices.md)het optimaliseren van de prestaties en het rendabele gebruik van uw Azure container Registry.

<!-- IMAGES -->


<!-- LINKS - Internal -->

