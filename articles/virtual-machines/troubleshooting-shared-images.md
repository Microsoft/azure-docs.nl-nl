---
title: Problemen met gedeelde installatie kopieën in azure oplossen
description: Meer informatie over het oplossen van problemen met gedeelde afbeeldings galerieën.
author: olayemio
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: olayemio
ms.reviewer: cynthn
ms.openlocfilehash: 015fa201fe1c31dde2e30c2fe689ac13452b1b01
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607589"
---
# <a name="troubleshoot-shared-image-galleries-in-azure"></a>Problemen met gedeelde afbeeldings galerieën in azure oplossen

Als u problemen ondervindt bij het uitvoeren van bewerkingen in de gedeelde afbeeldings galerieën, afbeeldings definities en afbeeldings versies, voert u de fout opdracht opnieuw uit in de foutopsporingsmodus. U activeert de foutopsporingsmodus door de `--debug` Switch door te geven met de Azure CLI en de `-Debug` Switch met Power shell. Nadat u de fout hebt gevonden, volgt u dit artikel om het probleem op te lossen.


## <a name="creating-or-modifying-a-gallery"></a>Een galerie maken of wijzigen ##

**Bericht**: *Galerie naam is ongeldig. Toegestane tekens zijn Engelse alfanumerieke tekens, met een onderstrepings teken en een aantal punten die in het midden zijn toegestaan, tot 80 tekens in totaal. Alle andere speciale tekens, inclusief streepjes, zijn niet toegestaan.*  
**Oorzaak**: de naam voor de galerie voldoet niet aan de naamgevings vereisten.  
**Tijdelijke oplossing**: Kies een naam die aan de volgende voor waarden voldoet: 
- Heeft een limiet van 80 tekens
- Bevat alleen Engelse letters, cijfers, onderstrepings tekens en punten
- Begint en eindigt met Engelse letters of cijfers

**Bericht**: *de naam van de entiteit is ongeldig volgens de validatie regel: ^ [^ \_ \w] [\w-. \_ ] {0,79} (? <! [-.]) $.*  
**Oorzaak**: de naam van de galerie voldoet niet aan de naamgevings vereisten.  
**Tijdelijke oplossing**: Kies een naam voor de galerie die voldoet aan de volgende voor waarden: 
- Heeft een limiet van 80 tekens
- Bevat alleen Engelse letters, cijfers, onderstrepings tekens en punten
- Begint en eindigt met Engelse letters of cijfers

**Bericht**: *de gegeven resource naam <Gallery name \> heeft deze ongeldige Volg tekens: <teken \> . De naam mag niet eindigen op tekens: <teken \>*  
**Oorzaak**: de naam voor de galerie eindigt met een punt of een onderstrepings teken.  
**Tijdelijke oplossing**: Kies een naam voor de galerie die voldoet aan de volgende voor waarden: 
- Heeft een limiet van 80 tekens
- Bevat alleen Engelse letters, cijfers, onderstrepings tekens en punten
- Begint en eindigt met Engelse letters of cijfers

**Bericht**: *de gegeven locatie <regio \> is niet beschikbaar voor het resource type micro soft. Compute/galerieën. Lijst met beschik bare regio's voor het resource type is...*  
**Oorzaak**: de opgegeven regio voor de galerie is onjuist of vereist een toegangs aanvraag.  
**Tijdelijke oplossing**: Controleer of de naam van de regio juist is. Als de regio naam juist is, dient u [een toegangs aanvraag](/troubleshoot/azure/general/region-access-request-process) in voor de regio.

**Bericht**: *kan de resource niet verwijderen voordat de geneste resources zijn verwijderd.*  
**Oorzaak**: u hebt geprobeerd een galerie te verwijderen die ten minste één bestaande afbeeldings definitie bevat. Een galerie moet leeg zijn voordat deze kan worden verwijderd.  
**Tijdelijke oplossing**: Verwijder alle afbeeldings definities in de galerie en ga vervolgens door met het verwijderen van de galerie. Als de definitie van de installatie kopie installatie kopieën bevat, moet u de installatie kopie versies verwijderen voordat u de afbeeldings definities verwijdert.

**Bericht**: *de galerie naam <Gallery \> is niet uniek binnen het abonnement <subscriptionID> . Kies een andere naam voor de galerie.*  
**Oorzaak**: u hebt een bestaande galerie met dezelfde naam en hebt geprobeerd een andere galerie met dezelfde naam te maken.  
**Tijdelijke oplossing**: Kies een andere naam voor de galerie.

**Bericht**: *de resource <Gallery \> bestaat al in de locatie <regio \_ 1 \> in de resource groep <resourceGroup \> . Een resource met dezelfde naam kan niet worden gemaakt op locatie <regio \_ 2 \> . Selecteer een nieuwe resource naam.*  
**Oorzaak**: u hebt een bestaande galerie met dezelfde naam en hebt geprobeerd een andere galerie met dezelfde naam te maken.  
**Tijdelijke oplossing**: Kies een andere naam voor de galerie.

## <a name="creating-or-modifying-image-definitions"></a>Afbeeldings definities maken of wijzigen ##

**Bericht**: de *eigenschap galleryImage. Properties. <eigenschap \> kan niet worden gewijzigd.*  
**Oorzaak**: u hebt geprobeerd het type besturings systeem, de status van het besturings systeem, de generatie van Hyper-V, de aanbieding, de uitgever of de SKU te wijzigen. Het is niet toegestaan om een van deze eigenschappen te wijzigen.  
**Tijdelijke oplossing**: Maak in plaats daarvan een nieuwe definitie van de installatie kopie.

**Bericht**: *de resource <Gallery/imageDefinitionName \> bestaat al in de locatie <regio \_ 1 \> in de resource groep <resourceGroup \> . Een resource met dezelfde naam kan niet worden gemaakt op locatie <regio \_ 2 \> . Selecteer een nieuwe resource naam.*  
**Oorzaak**: u hebt een bestaande definitie van een installatie kopie in dezelfde galerie en resource groep met dezelfde naam. U hebt geprobeerd een andere definitie van een installatie kopie te maken met dezelfde naam en in dezelfde galerie, maar in een andere regio.  
**Tijdelijke oplossing**: gebruik een andere naam voor de definitie van de installatie kopie of plaats de definitie van de installatie kopie in een andere galerie of resource groep.

**Bericht**: *de gegeven resource naam <Gallery \> /<imageDefinitionName \> heeft de volgende ongeldige Volg tekens: <teken \> . De naam mag niet eindigen op tekens: <teken \>*  
**Oorzaak**: de naam van de <imageDefinitionName \> eindigt met een punt of een onderstrepings teken.  
**Tijdelijke oplossing**: Kies een naam voor de definitie van de installatie kopie die voldoet aan de volgende voor waarden: 
- Heeft een limiet van 80 tekens
- Bevat alleen Engelse letters, cijfers, onderstrepings tekens, afbreek streepjes en punten
- Begint en eindigt met Engelse letters of cijfers.

**Bericht**: *de entiteits naam <imageDefinitionName \> is ongeldig volgens de validatie regel: ^ [^ \_ \\ W] [ \\ W-. \_ ] {0,79} (? <! [-.]) $"*  
**Oorzaak**: de naam van de <imageDefinitionName \> eindigt met een punt of een onderstrepings teken.  
**Tijdelijke oplossing**: Kies een naam voor de definitie van de installatie kopie die voldoet aan de volgende voor waarden: 
- Heeft een limiet van 80 tekens
- Bevat alleen Engelse letters, cijfers, onderstrepings tekens, afbreek streepjes en punten
- Begint en eindigt met Engelse letters of cijfers

**Bericht**: *activa naam galleryImage. Properties. id. <eigenschap \> is ongeldig. De waarde mag niet leeg zijn. Toegestane tekens zijn hoofd letters of kleine letters, cijfers, afbreek streepjes (-), punt (.), onderstrepings teken ( \_ ). Namen mogen niet eindigen op een punt (.). De lengte van de naam mag niet langer zijn dan <numerieke \> tekens.*  
**Oorzaak**: de uitgevers-, aanbiedings-of SKU-waarde voldoet niet aan de naamgevings vereisten.  
**Tijdelijke oplossing**: Kies een waarde die voldoet aan de volgende voor waarden: 
- Heeft een limiet van 128 tekens voor Publisher of 64-teken limiet voor aanbieding en SKU
- Bevat alleen Engelse letters, cijfers, afbreek streepjes, onderstrepings tekens en punten
- Eindigt niet met een punt

**Bericht**: *kan de aangevraagde bewerking niet uitvoeren op de geneste resource. Kan de bovenliggende resource <galerienaam \> niet vinden.*  
**Oorzaak**: er is geen galerie met de naam <collagenaam \> in het huidige abonnement en de resource groep.  
**Tijdelijke oplossing**: Controleer of de namen van de galerie, het abonnement en de resource groep juist zijn. Als dat niet het geval is, maakt u een nieuwe galerie met de naam <Gallery name \> .

**Bericht**: *de gegeven locatie <regio \> is niet beschikbaar voor het resource type micro soft. Compute/galerieën. Lijst met beschik bare regio's voor het resource type is...*  
**Oorzaak**: de naam van de <regio \> is onjuist of vereist een toegangs aanvraag.  
**Tijdelijke oplossing**: Controleer of de naam van de regio juist is gespeld. U kunt deze opdracht uitvoeren om te zien welke regio's u hebt geopend. Als de regio zich niet in de lijst bevindt, dient u [een toegangs aanvraag](/troubleshoot/azure/general/region-access-request-process)in.

**Bericht**: *kan waarde niet serialiseren: <waarde \> als type: ISO-8601., ISO8601Error: ISO 8601-time-out ontbreekt. Kan de waarde \> van de datetime-teken reeks niet parseren <*  
**Oorzaak**: de waarde die is gegeven aan de eigenschap heeft niet de juiste notatie als datum.  
**Tijdelijke oplossing**: Geef een datum op in jjjj-mm-dd, jjjj-mm-dd'T'HH: mm: Sszzz of [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-geldige indeling.

**Bericht**: *kan de teken reeks niet converteren naar date time offset: <waarde \> . Pad eigenschappen. <eigenschap \>*  
**Oorzaak**: de waarde die is gegeven aan de eigenschap heeft niet de juiste notatie als datum.  
**Tijdelijke oplossing**: Geef een datum op in jjjj-mm-dd, jjjj-mm-dd'T'HH: mm: Sszzz of [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-geldige indeling.

**Bericht**: *EndOfLifeDate moet zijn ingesteld op een datum in de toekomst.*  
**Oorzaak**: de eigenschap datum van het einde van de levens duur is niet juist ingedeeld als datum die na de huidige datum valt.  
**Tijdelijke oplossing**: Geef een datum op in jjjj-mm-dd, jjjj-mm-dd'T'HH: mm: Sszzz of [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-geldige indeling.

**Bericht**: *argument--<eigenschap \> : ongeldige int-waarde: <\> waarde*  
**Oorzaak**: de eigenschap <\> accepteert alleen gehele getallen en <waarde \> is geen geheel getal.  
**Tijdelijke oplossing**: Kies een geheel getal.

**Bericht**: *de minimum waarde van <eigenschap \> mag niet groter zijn dan de maximum waarde van <eigenschap \> .*  
**Oorzaak**: de minimum waarde die voor de eigenschap <is gegeven, \> is hoger dan de maximum waarde die is ingesteld voor de <eigenschap \> .  
**Tijdelijke oplossing**: Wijzig de waarden zodat het minimum kleiner is dan of gelijk is aan het maximum.

**Bericht**: *galerie afbeelding: <imageDefinitionName \> geïdentificeerd door (Uitgever: <uitgever \> , aanbieding: <aanbieding \> , SKU: <SKU \> ) bestaat al. Kies een andere uitgever, aanbieding, SKU-combi natie.*  
**Oorzaak**: u hebt geprobeerd een nieuwe definitie van een installatie kopie te maken met dezelfde uitgever, aanbieding en SKU triplet als een bestaande definitie van een installatie kopie in dezelfde galerie.  
**Tijdelijke oplossing**: in een galerie moeten alle afbeeldings definities een unieke combi natie van Publisher, aanbieding en SKU hebben. Kies een unieke combi natie of kies een nieuwe galerie en maak de definitie van de installatie kopie opnieuw.

**Bericht**: *kan de resource niet verwijderen voordat de geneste resources zijn verwijderd.*  
**Oorzaak**: u hebt geprobeerd een afbeeldings definitie te verwijderen die afbeeldings versies bevat. Een definitie van een installatie kopie moet leeg zijn voordat deze kan worden verwijderd.  
**Tijdelijke oplossing**: Verwijder alle installatie kopieën in de definitie van de installatie kopie en verwijder vervolgens de definitie van de installatie kopie.

**Bericht**: *kan para meter <eigenschap niet binden \> . Kan waarde <waarde niet converteren \> naar type <Property type \> . Kan de id-naam <waarde niet overeenkomen met \> een geldige inventarisatie naam. Geef een van de volgende inventarisatie namen op en probeer het opnieuw: <Choice \_ 1 \> , <keuze \_ 2 \> ,...*  
**Oorzaak**: de eigenschap heeft een beperkte lijst van mogelijke waarden, en <waarde \> is niet een van beide.  
**Tijdelijke oplossing**: Kies een van de mogelijke <keuze \> waarden.

**Bericht**: *kan para meter <eigenschap niet binden \> . Kan waarde <waarde niet converteren \> naar type &quot; System. &quot; DateTime*  
**Oorzaak**: de waarde die is gegeven aan de eigenschap heeft niet de juiste notatie als datum.  
**Tijdelijke oplossing**: Geef een datum op in jjjj-mm-dd, jjjj-mm-dd'T'HH: mm: Sszzz of [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-geldige indeling.

**Bericht**: *kan para meter <eigenschap niet binden \> . Kan waarde <waarde niet converteren \> naar type &quot; System. &quot; Int32*  
**Oorzaak**: de eigenschap <\> accepteert alleen gehele getallen en <waarde \> is geen geheel getal.  
**Tijdelijke oplossing**: Kies een geheel getal.

**Bericht**: *ZRS Storage-account type wordt niet ondersteund in deze regio.*  
**Oorzaak**: u hebt de standaard zone-redundante opslag (ZRS) gekozen in een regio die deze nog niet ondersteunt.  
**Tijdelijke oplossing**: Wijzig het type opslag account in **Premium \_ LRS** of **Standard \_ LRS**. Raadpleeg onze documentatie voor de meest recente [lijst met regio's](../storage/common/storage-redundancy.md#zone-redundant-storage) waarvoor ZRS preview is ingeschakeld.

## <a name="creating-or-updating-image-versions"></a>Installatie kopie versies maken of bijwerken ##

**Bericht**: *de gegeven locatie <regio \> is niet beschikbaar voor het resource type micro soft. Compute/galerieën. Lijst met beschik bare regio's voor het resource type is...*  
**Oorzaak**: de naam van de <regio \> is onjuist of vereist een toegangs aanvraag.  
**Tijdelijke oplossing**: Controleer of de naam van de regio juist is gespeld. U kunt deze opdracht uitvoeren om te zien welke regio's u hebt geopend. Als de regio zich niet in de lijst bevindt, dient u [een toegangs aanvraag](/troubleshoot/azure/general/region-access-request-process)in.

**Bericht**: *kan de aangevraagde bewerking niet uitvoeren op de geneste resource. De bovenliggende resource <Gallery/imageDefinitionName is \> niet gevonden.*  
**Oorzaak**: er is geen galerie met de naam <Gallery/imageDefinitionName \> in het huidige abonnement en de resource groep.  
**Tijdelijke oplossing**: Controleer of de namen van de galerie, het abonnement en de resource groep juist zijn. Als dat niet het geval is, maakt u een nieuwe galerie met de naam <galerieën \> en/of de definitie van de installatie kopie met de naam <imageDefinitionName \> in de aangegeven resource groep.

**Bericht**: *kan para meter <eigenschap niet binden \> . Kan waarde <waarde niet converteren \> naar type &quot; System. &quot; DateTime*  
**Oorzaak**: de waarde die is gegeven aan de eigenschap heeft niet de juiste notatie als datum.  
**Tijdelijke oplossing**: Geef een datum op in jjjj-mm-dd, jjjj-mm-dd'T'HH: mm: Sszzz of [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-geldige indeling.

**Bericht**: *kan para meter <eigenschap niet binden \> . Kan waarde <waarde niet converteren \> naar type &quot; System. &quot; Int32*  
**Oorzaak**: de eigenschap <\> accepteert alleen gehele getallen en <waarde \> is geen geheel getal.  
**Tijdelijke oplossing**: Kies een geheel getal.

**Bericht**: *de versie van het publicatie Profiel van de galerie afbeelding <publishingRegions \> moet de locatie van de \> installatie kopie versie <sourceRegion bevatten*  
**Oorzaak**: de locatie van de bron afbeelding (<sourceRegion \> ) moet zijn opgenomen in de lijst <publishingRegions \> .  
**Tijdelijke oplossing**: Neem <sourceRegion \> op in de \> lijst <publishingRegions.

**Bericht**: *de waarde <waarde \> van para meter <eigenschap \> valt buiten het bereik. De waarde moet liggen tussen <minValue \> en <maxValue \> , inclusief.*  
**Oorzaak**: <waarde \> ligt buiten het bereik van mogelijke waarden voor de eigenschap <\> .  
**Tijdelijke oplossing**: Kies een waarde die binnen het bereik van <minValue \> en <maxValue \> , inclusief.

**Bericht**: *bron <resourceID \> is niet gevonden. Controleer of de bron bestaat en zich in dezelfde regio bevindt als de versie van de galerie-installatie kopie die wordt gemaakt.*  
**Oorzaak**: er bevindt zich geen bron op <resourceID \> , of de bron op <resourceID \> bevindt zich niet in dezelfde regio als de galerie afbeelding die wordt gemaakt.  
**Tijdelijke oplossing**: Controleer of de <resourceID- \> waarde juist is en of de bron regio van de versie van de galerie afbeelding overeenkomt met de regio van de <resourceID- \> waarde.

**Bericht**: het wijzigen van de *eigenschap galleryImageVersion. Properties. StorageProfile. <disk image \> . source.id is niet toegestaan.*  
**Oorzaak**: de bron-id van een galerie-afbeeldings versie kan niet worden gewijzigd nadat deze is gemaakt.  
**Tijdelijke oplossing**: Zorg ervoor dat de bron-id hetzelfde is als de bestaande bron-id, wijzig het versie nummer van de installatie kopie versie of verwijder de huidige versie van de installatie kopie en probeer het opnieuw.

**Bericht**: *Er zijn dubbele LUN-nummers gedetecteerd op de invoer gegevens schijven. LUN-nummer moet uniek zijn voor elke gegevens schijf.*  
**Oorzaak**: wanneer u een installatie kopie maakt met behulp van een lijst met schijven en/of schijf momentopnamen, hebben twee of meer schijven of moment opnamen van schijven dezelfde LUN.  
**Tijdelijke oplossing**: u kunt dubbele lun's verwijderen of wijzigen.

**Bericht**: *Er zijn dubbele bron-id's gevonden op de invoer schijven. De bron-id moet uniek zijn voor elke schijf.*  
**Oorzaak**: wanneer u een installatie kopie versie maakt met behulp van een lijst met schijven en/of schijf momentopnamen, hebben twee of meer schijven of moment opnamen van schijven dezelfde resource-id.  
**Tijdelijke oplossing**: Verwijder de bron-id's van de dubbele en andere schijven.

**Bericht**: *eigenschaps-id <resourceID \> op pad properties. storageProfile. <diskImages \> . source.id is ongeldig. Een volledig gekwalificeerde resource-id die begint met '/Subscriptions/ <subscriptionID> ' of '/providers/ <resourceProviderNamespace> /' verwacht.*  
**Oorzaak**: de <resourceID- \> waarde heeft een onjuiste indeling.  
**Tijdelijke oplossing**: Controleer of de resource-id juist is.

**Bericht**: *de bron-id: <resourceID \> moet een beheerde installatie kopie, een virtuele machine of een andere versie van de galerie afbeelding zijn*  
**Oorzaak**: de <resourceID- \> waarde heeft een onjuiste indeling.  
**Tijdelijke oplossing**: als u een VM, een beheerde installatie kopie of een galerie met installatie kopieën als de bron afbeelding gebruikt, controleert u of de resource-id van de versie van de virtuele machine, de beheerde installatie kopie of de installatie kopie juist is.

**Bericht**: *de bron-id: <resourceID \> moet een beheerde schijf of moment opname zijn.*  
**Oorzaak**: de <resourceID- \> waarde heeft een onjuiste indeling.  
**Tijdelijke oplossing**: als u schijven en/of schijf momentopnamen als bronnen voor de installatie kopie versie gebruikt, controleert u of de bron-id's van de schijven en/of schijf momentopnamen juist zijn.

**Bericht**: *kan geen versie van de galerie afbeelding maken op basis van: <ResourceID \> sinds de status van het besturings systeem in de bovenliggende galerie-afbeelding (<OsState \_ 1 \> ) niet <OsState \_ 2 \> .*  
**Oorzaak**: de status van het besturings systeem (gegeneraliseerd of gespecialiseerd) komt niet overeen met de status van het besturings systeem dat is opgegeven in de definitie van de installatie kopie.  
**Tijdelijke oplossing**: Kies een bron op basis van een virtuele machine met de status van het besturings systeem <OsState \_ 1 \> of maak een nieuwe definitie van de installatie kopie voor vm's op basis van <OsState \_ 2 \> .

**Bericht**: *de resource met id <resourceID \> heeft een andere Hyper visor generatie [' <V # \_ 1 \> ] dan de bovenliggende galerie installatie kopie van Hyper Visor [' <V # \_ 2 \> ']*  
**Oorzaak**: de Hyper Visor generatie van de installatie kopie versie komt niet overeen met de Hyper Visor generatie die is opgegeven in de definitie van de installatie kopie. Het besturings systeem voor de installatie kopie definitie is <V # \_ 1 \> en het besturings systeem van de installatie kopie versie is <V # \_ 2 \> .  
**Tijdelijke oplossing**: Kies een bron met dezelfde Hyper Visor genereren als de definitie van de installatie kopie of maak/Kies een nieuwe definitie van de installatie kopie die dezelfde Hyper Visor genereert als de versie van de installatie kopie.

**Bericht**: *de resource met de id <resourceID \> heeft een ander type besturings systeem [<OsType \_ 1 \> ] dan de bovenliggende galerie installatie kopie type van het besturings systeem [' <OsType \_ 2 \> ']*  
**Oorzaak**: de Hyper Visor generatie van de installatie kopie versie komt niet overeen met de Hyper Visor generatie die is opgegeven in de definitie van de installatie kopie. Het besturings systeem voor de installatie kopie definitie is <OsType \_ 1 \> en het besturings systeem installatie kopie versie is <OsType \_ 2 \> .  
**Tijdelijke oplossing**: Kies een bron met hetzelfde besturings systeem (Linux/Windows) als de definitie van de installatie kopie of maak/Kies een nieuwe definitie van de installatie kopie die hetzelfde besturings systeem als de installatie kopie versie heeft.

**Bericht**: *virtuele bron machine <resourceID \> kan geen tijdelijke besturingssysteem schijf bevatten.*  
**Oorzaak**: de bron op <resourceID \> bevat een kortstondige besturingssysteem schijf. De galerie met gedeelde afbeeldingen biedt momenteel geen ondersteuning voor tijdelijke besturingssysteem schijven.  
**Tijdelijke oplossing**: Kies een andere bron op basis van een virtuele machine die geen tijdelijke besturingssysteem schijf gebruikt.

**Bericht**: de *virtuele bron machine <resourceID \> mag geen schijf [' <diskID \> '] bevatten die is opgeslagen in een UltraSSD-account type.*  
**Oorzaak**: de schijf <diskID \> is een ultra-SSD schijf. De galerie met gedeelde afbeeldingen biedt momenteel geen ondersteuning voor Ultra-SSD-schijven.  
**Tijdelijke oplossing**: gebruik een bron die alleen Premium-SSD, Standard-SSD en/of Standard-HDD Managed disks bevat.

**Bericht**: *virtuele bron machine <resourceID \> moet worden gemaakt op basis van Managed disks.*  
**Oorzaak**: de virtuele machine in <resourceID \> maakt gebruik van niet-beheerde schijven.  
**Tijdelijke oplossing**: gebruik een bron op basis van een virtuele machine die alleen Premium-SSD, Standard-SSD en/of Standard-HDD Managed disks bevat.

**Bericht**: *te veel aanvragen op bron <resourceID \> . Verminder het aantal aanvragen op de bron of wacht enige tijd voordat u het opnieuw probeert.*  
**Oorzaak**: de bron voor deze installatie kopie versie wordt momenteel beperkt vanwege te veel aanvragen.  
**Tijdelijke oplossing**: Probeer later de installatie kopie versie te maken.

**Bericht**: *de schijf versleuteling set <diskEncryptionSetID \> moet zich in hetzelfde abonnement <subscriptionID bevallen \> als de resource van de galerie.*  
**Oorzaak**: schijf versleutelings sets kunnen alleen worden gebruikt in hetzelfde abonnement en dezelfde regio waarin ze zijn gemaakt.  
**Tijdelijke oplossing**: Maak of gebruik een versleutelings in hetzelfde abonnement en dezelfde regio als de versie van de installatie kopie.

**Bericht**: *versleutelde bron: ' <resourceID \> ' bevindt zich in een andere abonnements-id dan het huidige versie abonnement van de galerie-installatie kopie ' <subscriptionID \_ 1 \> '. Probeer het opnieuw met een niet-versleutelde bron (nen) of gebruik het bron abonnement <subcriptionID \_ 2 \> om de versie van de galerie afbeelding te maken.*  
**Oorzaak**: de galerie met gedeelde afbeeldingen biedt momenteel geen ondersteuning voor het maken van installatie kopieën in een ander abonnement van een andere bron installatie kopie als de bron installatie kopie is versleuteld.  
**Tijdelijke oplossing**: gebruik een niet-versleutelde bron of maak de installatie kopie versie in hetzelfde abonnement als de bron.

**Bericht**: *de schijf versleutelings <diskEncryptionSetID \> is niet gevonden.*  
**Oorzaak**: de schijf versleuteling is mogelijk onjuist.  
**Tijdelijke oplossing**: Controleer of de resource-id van de ingestelde schijf versleuteling juist is.

**Bericht**: *de versie naam van de installatie kopie is ongeldig. De naam van de installatie kopie versie moet volgen op primair (int). Secundair (int). De indeling van de patch (int), bijvoorbeeld: 1.0.0, 2018.12.1 etc.*  
**Oorzaak**: de geldige indeling voor een afbeeldings versie bestaat uit drie gehele getallen, gescheiden door een punt. De versie naam van de installatie kopie voldoet niet aan de geldige indeling.  
**Tijdelijke oplossing**: gebruik een installatie kopie versie naam die de indeling primair (int) volgt. Secundair (int). Patch (int). Bijvoorbeeld: 1.0.0. of 2018.12.1.

**Bericht**: *de waarde van de para meter galleryArtifactVersion. Properties. publishingProfile. targetRegions. encryption. dataDiskImages. diskEncryptionSetId is ongeldig*  
**Oorzaak**: de resource-id van de schijf versleuteling die wordt gebruikt op een installatie kopie van de gegevens schijf gebruikt een ongeldige indeling.  
**Tijdelijke oplossing**: Zorg ervoor dat de resource-id van de ingestelde schijf versleuteling de indeling/Subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.Compute/<diskEncryptionSetName volgt \> .

**Bericht**: *de waarde van de para meter galleryArtifactVersion. Properties. publishingProfile. targetRegions. encryption. osDiskImage. diskEncryptionSetId is ongeldig.*  
**Oorzaak**: de resource-id van de schijf versleuteling die op de besturingssysteem schijf installatie kopie wordt gebruikt, gebruikt een ongeldige indeling.  
**Tijdelijke oplossing**: Zorg ervoor dat de resource-id van de ingestelde schijf versleuteling de indeling/Subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.Compute/<diskEncryptionSetName volgt \> .

**Bericht**: *kan geen nieuwe gegevens schijf opgeven versleutelings-LUN [<nummer \> ] met een schijf versleutelings in regio [<regio \> ] voor de versie aanvraag van de update galerie-installatie kopie. Als u deze versie wilt bijwerken, verwijdert u de nieuwe LUN. Als u de versleutelings instellingen voor de gegevens schijf wilt wijzigen, moet u een nieuwe versie van de galerie-installatie kopie met de juiste instellingen maken.*  
**Oorzaak**: u hebt versleuteling toegevoegd aan de gegevens schijf van een bestaande installatie kopie versie. U kunt geen versleuteling toevoegen aan een bestaande versie van de installatie kopie.  
**Tijdelijke oplossing**: Maak een nieuwe versie van de galerie-afbeelding of verwijder de toegevoegde versleutelings instellingen.

**Bericht**: *de bron van de galerij-artefact versie kan alleen direct onder storageProfile of in een afzonderlijk besturings systeem of gegevens schijven worden opgegeven. Er kan slechts één bron type (gebruikers installatie kopie, moment opname, schijf, virtuele machine) worden gegeven.*  
**Oorzaak**: de bron-ID ontbreekt.  
**Tijdelijke oplossing**: Zorg ervoor dat de bron-id van de bron aanwezig is.

**Bericht**: *bron is niet gevonden: <resourceID \> . Controleer of de bron bestaat.*  
**Oorzaak**: de bron-id van de bron is mogelijk onjuist.  
**Tijdelijke oplossing**: Controleer of de resource-id van de bron juist is.

**Bericht**: *Er is een schijf versleutelings vereist voor de schijf ' galleryArtifactVersion. Properties. publishingProfile. targetRegions. encryption. osDiskImage. diskEncryptionSetId ' in de doel regio ' <regio \_ 1 \> ' omdat de schijf Encryption set ' <diskEncryptionSetId \> ' wordt gebruikt voor de bijbehorende schijf in regio ' <regio \_ 2 \> '*  
**Oorzaak**: versleuteling is gebruikt op de besturingssysteem schijf in <regio \_ 2 \> , maar niet in <regio \_ 1 \> .  
**Tijdelijke oplossing**: als u versleuteling gebruikt op de besturingssysteem schijf, gebruikt u versleuteling in alle regio's.

**Bericht**: *Er is een schijf versleutelings vereist voor de schijf ' LUN <number \> ' in de doel regio <regio \_ 1 \> omdat de set ' <diskEncryptionSetID \> ' van de schijf versleuteling wordt gebruikt voor de bijbehorende schijf in regio ' <regio \_ 2 \> '*  
**Oorzaak**: versleuteling is gebruikt op de gegevens schijf op LUN <nummer \> in <regio \_ 2 \> , maar niet in <regio \_ 1 \> .  
**Tijdelijke oplossing**: als u versleuteling gebruikt voor een gegevens schijf, gebruikt u versleuteling in alle regio's.

**Bericht**: *Er is een ongeldig LUN [<number \> ] opgegeven in encryption. dataDiskImages. Het LUN moet een van de volgende waarden zijn: [' 0, 9 '].*  
**Oorzaak**: de LUN die is opgegeven voor de versleuteling, komt niet overeen met een van de lun's voor schijven die zijn gekoppeld aan de virtuele machine.  
**Tijdelijke oplossing**: Wijzig de LUN in de versleuteling in het LUN van een gegevens schijf die aanwezig is in de virtuele machine.

**Bericht**: er *zijn dubbele lun's <number \> ' opgegeven in doel regio ' <regio \> ' Encryption. dataDiskImages.*  
**Oorzaak**: de versleutelings instellingen die in <regio worden gebruikt, hebben \> ten minste twee keer een LUN opgegeven.  
**Tijdelijke oplossing**: Wijzig de LUN in <regio \> om te controleren of alle lun's uniek zijn in <regio \> .

**Bericht**: *OSDiskImage en DataDiskImage kunnen niet verwijzen naar dezelfde BLOB <\> sourceID*  
**Oorzaak**: de bronnen voor de besturingssysteem schijf en ten minste één gegevens schijf zijn niet uniek.  
**Tijdelijke oplossing**: Wijzig de bron voor de besturingssysteem schijf en/of gegevens schijven om ervoor te zorgen dat de besturingssysteem schijf en elke gegevens schijf uniek zijn.

**Bericht**: *dubbele regio's zijn niet toegestaan in doel publicatie regio's.*  
**Oorzaak**: een regio wordt meer dan eens in de publicatie regio's weer gegeven.  
**Tijdelijke oplossing**: Verwijder de dubbele regio.

**Bericht**: *het toevoegen van nieuwe gegevens schijven of het wijzigen van het LUN van een gegevens schijf in een bestaande installatie kopie is niet toegestaan.*  
**Oorzaak**: een update aanroep naar de versie van de installatie kopie bevat een nieuwe gegevens schijf of heeft een nieuwe LUN voor een schijf.  
**Tijdelijke oplossing**: gebruik de lun's en gegevens schijven van de bestaande installatie kopie versie.

**Bericht**: *de schijf versleutelings <diskEncryptionSetID \> moet zich in hetzelfde abonnement be<subscriptionID \> als de resource van de galerie.*  
**Oorzaak**: de galerie met gedeelde afbeeldingen biedt momenteel geen ondersteuning voor het gebruik van een schijf versleuteling die is ingesteld in een ander abonnement.  
**Tijdelijke oplossing**: Maak de installatie kopie versie en schijf versleuteling die in hetzelfde abonnement zijn ingesteld.

**Bericht**: *replicatie in deze regio is mislukt omdat de bron resource grootte 2048 van de GalleryImageVersion groter is dan de maximale grootte die 1024 ondersteunt.*  
**Oorzaak**: een gegevens schijf in de bron is groter dan 1 TB.  
**Tijdelijke oplossing**: Wijzig de grootte van de gegevens schijf onder 1 TB.

## <a name="creating-or-updating-a-vm-or-scale-sets-from-an-image-version"></a>Een virtuele machine of schaal sets maken of bijwerken op basis van een installatie kopie versie ##

**Bericht**: *Er bestaat geen nieuwste afbeeldings versie voor <imageDefinitionResourceID \>*  
**Oorzaak**: de definitie van de installatie kopie die u hebt gebruikt voor het implementeren van de virtuele machine bevat geen installatie kopieën die in de laatste versie zijn opgenomen.  
**Tijdelijke oplossing**: Zorg ervoor dat er ten minste één installatie kopie versie is ingesteld op ' niet van laatste ' instellen op ONWAAR. 

**Bericht**: *de client is gemachtigd om de actie ' micro soft. computes/afbeeldingen/versies/lezen ' op het bereik <resourceID uit te voeren \> , maar de huidige Tenant <tenantID \> is niet geautoriseerd voor toegang tot het gekoppelde abonnement <subscriptionID \> .*  
**Oorzaak**: de virtuele machine of schaalset is gemaakt met behulp van een SIG-installatie kopie in een andere Tenant. U hebt geprobeerd een wijziging aan te brengen in de virtuele machine of schaalset, maar u hebt geen toegang tot het abonnement dat eigenaar is van de installatie kopie.  
**Tijdelijke oplossing**: Neem contact op met de eigenaar van het abonnement van de versie van de installatie kopie om Lees toegang te verlenen tot de installatie kopie versie.

**Bericht**: *de galerie afbeelding <resourceID \> is niet beschikbaar in <regio regio \> . Neem contact op met de eigenaar van de afbeelding om deze regio te repliceren, of wijzig uw aangevraagde regio.*  
**Oorzaak**: de virtuele machine wordt gemaakt in een regio die zich niet in de lijst met gepubliceerde regio's voor de galerie afbeelding bevindt.  
**Tijdelijke oplossing**: repliceer de installatie kopie naar de regio of maak een virtuele machine in een van de regio's in de publicatie regio's van de galerie-afbeelding.

**Bericht**: de *para meter osProfile is niet toegestaan.*  
**Oorzaak**: gebruikers naam, wacht woord of SSH-sleutels zijn opgegeven voor een virtuele machine die is gemaakt op basis van een gespecialiseerde installatie kopie versie.  
**Tijdelijke oplossing**: Neem geen beheerders naam, wacht woord of SSH-sleutel op als u van plan bent om een virtuele machine te maken op basis van die installatie kopie. Gebruik anders een gegeneraliseerde installatie kopie versie en geef de gebruikers naam, het wacht woord of de SSH-sleutels op.

**Bericht**: de *vereiste para meter osProfile ontbreekt (null).*  
**Oorzaak**: de virtuele machine is gemaakt op basis van een gegeneraliseerde installatie kopie en de gebruikers naam, het wacht woord of de SSH-sleutels van de beheerder ontbreken. Aangezien gegeneraliseerde installatie kopieën de gebruikers naam, het wacht woord of de SSH-sleutels niet behouden, moeten deze velden worden opgegeven tijdens het maken van een virtuele machine of schaalset.  
**Tijdelijke oplossing**: Geef de gebruikers naam, het wacht woord of de SSH-sleutels op, of gebruik een gespecialiseerde installatie kopie versie.

**Bericht**: *kan geen versie van de galerie afbeelding maken op basis van: <ResourceID \> sinds de status van het besturings systeem in de bovenliggende galerie-afbeelding (' gespecialiseerd ') niet ' gegeneraliseerd ' is.*  
**Oorzaak**: de versie van de installatie kopie wordt gemaakt op basis van een gegeneraliseerde bron, maar de bovenliggende definitie is gespecialiseerd.  
**Tijdelijke oplossing**: Maak de installatie kopie versie met behulp van een gespecialiseerde bron of gebruik een bovenliggende definitie die is gegeneraliseerd.

**Bericht**: *kan de Schaalset voor virtuele machines <vmssName niet bijwerken \> omdat de huidige status van het besturings systeem van de VM-schaalset een andere naam heeft dan de bijgewerkte versie van het besturings systeem van de galerie-installatie kopie. deze is gespecialiseerd.*  
**Oorzaak**: de huidige bron installatie kopie voor de schaalset is een gegeneraliseerde bron installatie kopie, maar wordt bijgewerkt met een bron installatie kopie die speciaal is ingesteld. De huidige bron installatie kopie en de nieuwe bron installatie kopie voor een schaalset moeten dezelfde status hebben.  
**Tijdelijke oplossing**: gebruik een algemene versie van de installatie kopie om de schaalset bij te werken.

**Bericht**: *schijf encryption set <diskEncryptionSetID \> in de galerie met gedeelde installatie kopieën <versionID \> behoort tot abonnement <subscriptionID \_ 1 \> en kan niet worden gebruikt met resource ' ' in het abonnement <subscriptionID \_ 2 \>*  
**Oorzaak**: de schijf versleuteling die is gebruikt voor het versleutelen van de versie van de installatie kopie bevindt zich in een ander abonnement dan het abonnement om de installatie kopie versie te hosten.  
**Tijdelijke oplossing**: gebruik hetzelfde abonnement voor de installatie kopie versie en de schijf versleutelings.

**Bericht**: *het maken van de VM of virtuele-machine schaalset duurt lang.*  
**Tijdelijke oplossing**: Controleer of de **OSTYPE** van de versie van de installatie kopie die u probeert te maken van de virtuele machine en de VM-schaalset hetzelfde **OSTYPE** heeft van de bron die u hebt gebruikt voor het maken van de installatie kopie versie. 

**Bericht**: *de resource met de id <vmID \> heeft een ander abonnement [{ \" name \" : \" <name> \" , \" Publisher \" : \" <publisher> \" , \" product \" : \" <product> \" , \" promotionCode \" : \" <promotionCode> \" }] dan het bovenliggende galerie-installatie kopie schema [null].*  
**Oorzaak**: de definitie van de bovenliggende installatie kopie voor de implementatie van de installatie kopie heeft geen informatie over het aankoop plan.  
**Tijdelijke oplossing**: Maak een definitie van een installatie kopie met dezelfde Details van het aankoop plan uit het fout bericht en maak de installatie kopie versie in de definitie van de installatie kopie.


## <a name="creating-a-disk-from-an-image-version"></a>Een schijf maken op basis van een installatie kopie versie ##

**Bericht**: *de waarde van de para meter imageReference is ongeldig.*  
**Oorzaak**: u hebt geprobeerd vanuit een SIG-installatie kopie versie te exporteren naar een schijf, maar u hebt een LUN-positie gebruikt die niet voor komt in de installatie kopie.    
**Tijdelijke oplossing**: Controleer de versie van de installatie kopie om te zien welke LUN-posities in gebruik zijn.

## <a name="sharing-resources"></a>Resources delen

Het delen van de afbeeldings galerie, de definitie van de installatie kopie en de versie van de installatie kopie van de verschillende abonnementen is ingeschakeld met behulp [van Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/rbac-and-directory-admin-roles.md). 

## <a name="replication-speed"></a>Replicatie snelheid

Gebruik de vlag **---expand ReplicationStatus** om te controleren of de replicatie naar alle opgegeven doel regio's is voltooid. Als dat niet het geval is, wacht u tot zes uur voordat de taak is voltooid. Als dit mislukt, moet u de opdracht opnieuw activeren om de installatie kopie versie te maken en te repliceren. Als er veel doel regio's zijn waarnaar de versie van de installatie kopie wordt gerepliceerd, kunt u overwegen om de replicatie in fasen uit te voeren.

## <a name="azure-limits-and-quotas"></a>Limieten en quota in Azure 

[Azure-limieten en-quota](../azure-resource-manager/management/azure-subscription-service-limits.md) zijn van toepassing op alle resources van de gedeelde installatie kopie, de afbeeldings definitie en de versie van de installatie kopie. Zorg ervoor dat u binnen de limieten voor uw abonnementen zit. 


## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Galerie met gedeelde installatie kopieën](./shared-image-galleries.md).