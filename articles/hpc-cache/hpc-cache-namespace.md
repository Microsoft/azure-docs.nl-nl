---
title: Meer informatie over de geaggregeerde naam ruimte Azure HPC cache
description: De virtuele naam ruimte voor uw Azure HPC-cache plannen
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 1c28f549cf93d77f6aef6bcde6a2225345a79cc9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91612945"
---
# <a name="plan-the-aggregated-namespace"></a>De geaggregeerde naamruimte plannen

Met de HPC-cache van Azure kunnen clients toegang krijgen tot diverse opslag systemen via een virtuele naam ruimte die de details van het back-end-opslag systeem verbergt.

Nadat u een opslag doel hebt toegevoegd, stelt u een of meer client gerichte naam ruimte paden in voor het opslag doel. Client computers koppelen dit bestandspad en kunnen aanvragen voor het lezen van bestanden naar de cache maken in plaats van het opslag systeem rechtstreeks te koppelen.

Omdat Azure HPC cache dit virtuele bestands systeem beheert, kunt u het opslag doel wijzigen zonder het client gerichte pad te wijzigen. U kunt bijvoorbeeld een hardware-opslag systeem vervangen door de opslag van de Cloud zonder dat de procedures voor de client zijde opnieuw moeten worden geschreven.

## <a name="aggregated-namespace-example"></a>Voor beeld van geaggregeerde naam ruimte

Plan uw geaggregeerde naam ruimte, zodat de client computers de benodigde informatie kunnen bereiken, zodat beheerders en werk stroom engineers de paden eenvoudig kunnen onderscheiden.

Denk bijvoorbeeld aan een systeem waarbij een Azure HPC-cache-exemplaar wordt gebruikt voor het verwerken van gegevens die zijn opgeslagen in Azure Blob. De analyse vereist sjabloon bestanden die zijn opgeslagen in een on-premises Data Center.

De sjabloon gegevens worden opgeslagen in een Data Center en de informatie die nodig is voor deze taak wordt opgeslagen in de volgende submappen:

* */goldline/templates/acme2017/sku798*
* */goldline/templates/acme2017/sku980*

In het Data Center-opslag systeem zijn de volgende export bewerkingen beschikbaar:

* */*
* */goldline*
* */goldline/templates*

De gegevens die moeten worden geanalyseerd, zijn gekopieerd naar een Azure Blob Storage-container met de naam ' sourcecollection ' met het [hulp programma CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Als u eenvoudige toegang via de cache wilt toestaan, kunt u overwegen om opslag doelen met deze virtuele naam ruimte paden te maken:

| Back-end-opslag systeem <br/> (NFS-bestandspad of BLOB-container) | Pad naar virtuele naam ruimte |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source               |

Een NFS-opslag doel kan meerdere paden voor virtuele naam ruimten hebben, zolang elk een pad naar een uniek exportpad verwijst. ( [NFS-naam ruimte paden](add-namespace-paths.md#nfs-namespace-paths) lezen voor meer informatie over het aanbevolen maximum aantal naam ruimte paden per NFS-opslag doel.)

Omdat de NFS-bron paden submappen van dezelfde export zijn, moet u meerdere naam ruimte paden van hetzelfde opslag doel definiëren.

| Hostnaam van opslag doel  | Pad naar NFS-export     | Pad naar submap | Pad naar naam ruimte    |
|--------------------------|---------------------|-------------------|-------------------|
| *IP-adres of hostnaam* | /goldline/templates | acme2017/sku798   | /templates/sku798 |
| *IP-adres of hostnaam* | /goldline/templates | acme2017/sku980   | /templates/sku980 |

Een client toepassing kan de cache koppelen en eenvoudig toegang krijgen tot de geaggregeerde bestands paden van de naam ruimte ``/source`` , ``/templates/sku798`` en ``/templates/sku980`` .

Een alternatieve methode is het maken van een virtueel pad `/templates` , zoals koppelingen naar de bovenliggende map, `acme2017` en de clients vervolgens naar de persoon `sku798` en mappen navigeren `sku980` nadat de cache is gekoppeld. U kunt echter geen pad naar een naam ruimte maken dat een submap van een ander pad van een naam ruimte is. Als u dus een pad naar de map maakt, `acme2017` kunt u ook geen naam ruimte paden maken om rechtstreeks toegang te krijgen tot de submappen.

Op de pagina instellingen van **naam ruimte** Azure HPC-cache wordt het client gerichte bestands systeem weer gegeven en kunt u paden toevoegen of bewerken. Lees [de geaggregeerde naam ruimte instellen](add-namespace-paths.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Nadat u hebt bepaald hoe u het virtuele bestands systeem moet instellen, voert u de volgende stappen uit om het te maken:

* [Opslag doelen maken](hpc-cache-add-storage.md) om uw back-end-opslag systemen toe te voegen aan uw Azure HPC-cache
* [Naam ruimte paden toevoegen](add-namespace-paths.md) om de geaggregeerde naam ruimte te maken die door client computers wordt gebruikt voor toegang tot bestanden
