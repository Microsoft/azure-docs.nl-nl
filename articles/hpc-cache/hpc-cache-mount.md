---
title: Een HPC-cache van Azure koppelen
description: Clients verbinden met een Azure HPC cache-service
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 7f1d8d34d6351fc344fdb101ac8e9a96678df9d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91651425"
---
# <a name="mount-the-azure-hpc-cache"></a>De Azure HPC Cache koppelen

Nadat de cache is gemaakt, hebben NFS-clients toegang tot de server met een eenvoudige `mount` opdracht. Met de opdracht wordt een specifiek doelpad voor opslag op de Azure HPC-cache verbonden met een lokale map op de client computer.

De koppelings opdracht bestaat uit de volgende elementen:

* Een van de koppel adressen van de cache (vermeld op de pagina overzicht van cache)
* Een pad naar een virtuele naam ruimte dat u instelt voor het opslag doel (vermeld op de pagina met de cache naam ruimte)
* Het lokale pad dat op de client moet worden gebruikt
* Opdracht parameters die het succes van dit type NFS-koppeling optimaliseren

De pagina **koppelings instructies** voor uw cache verzamelt de gegevens en de aanbevolen opties voor u en maakt een prototype koppelings opdracht die u kunt kopiëren. Lees [het hulp programma koppelings instructies gebruiken](#use-the-mount-instructions-utility) voor meer informatie.

## <a name="prepare-clients"></a>Clients voorbereiden

Zorg ervoor dat uw clients de Azure HPC-cache kunnen koppelen door de richt lijnen in deze sectie te volgen.

### <a name="provide-network-access"></a>Netwerk toegang bieden

De client computers moeten netwerk toegang hebben tot het virtuele netwerk en het particuliere subnet van de cache.

Maak bijvoorbeeld client-Vm's binnen hetzelfde virtuele netwerk, of gebruik een eind punt, gateway of andere oplossing in het virtuele netwerk voor toegang vanaf buiten. (Houd er rekening mee dat niets anders dan de cache zelf moet worden gehost in het subnet van de cache.)

### <a name="install-utilities"></a>Hulpprogram ma's installeren

Installeer de juiste Linux-hulpprogram ma's voor de ondersteuning van de NFS-koppelings opdracht:

* Voor Red Hat Enterprise Linux of SuSE: `sudo yum install -y nfs-utils`
* Voor Ubuntu of Debian: `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Een lokaal pad maken

Maak een pad naar een lokale map op elke client om verbinding te maken met de cache. Maak een pad voor elk naam ruimte-pad dat u wilt koppelen.

Voorbeeld: `sudo mkdir -p /mnt/hpc-cache-1/target3`

De pagina [koppelings instructies](#use-the-mount-instructions-utility) in het Azure Portal bevat een prototype opdracht die u kunt kopiëren.

Wanneer u de client computer verbindt met de cache, koppelt u dit pad aan een pad naar een virtuele naam ruimte dat staat voor het exporteren van een opslag doel. Mappen maken voor elk van de paden van de virtuele naam ruimte die de client gaat gebruiken.

## <a name="use-the-mount-instructions-utility"></a>Het hulp programma koppelings instructies gebruiken

U kunt de **koppelings instructies** pagina in het Azure Portal gebruiken om een Kopieer bare koppelings opdracht te maken. Open de pagina in het gedeelte **configureren** van de cache weergave in de portal.

Voordat u de opdracht op een client gebruikt, moet u ervoor zorgen dat de client voldoet aan de vereisten en beschikt over de software die nodig is voor het gebruik van de NFS- `mount` opdracht zoals hierboven beschreven in [clients voorbereiden](#prepare-clients).

![scherm afbeelding van een Azure HPC-cache-exemplaar in de portal, met de pagina > koppelings instructies configureren geladen](media/mount-instructions.png)

Volg deze procedure om de koppelings opdracht te maken.

1. Pas het veld **client-pad** aan. Dit veld bevat een voor beeld van een opdracht die u kunt gebruiken voor het maken van een lokaal pad op de client. De client opent de inhoud van de Azure HPC-cache lokaal in deze map.

   Klik op het veld en bewerk de opdracht om de gewenste mapnaam te bevatten. De naam wordt weer gegeven aan het einde van de teken reeks na `sudo mkdir -p`

   ![scherm opname van het veld client-pad met de cursor aan het einde](media/mount-edit-client.png)

   Nadat u klaar bent met het bewerken van het veld, wordt de koppelings opdracht onder aan de pagina bijgewerkt met het nieuwe pad van de client.

1. Kies het **cache koppelings adres** in de lijst. Dit menu bevat alle [client koppel punten](#find-mount-command-components)van de cache.

   Het verdelen van client belasting over alle beschik bare koppel adressen voor betere cache prestaties.

   ![scherm afbeelding van het veld cache koppeling, met selector drie IP-adressen waaruit u kunt kiezen](media/mount-select-ip.png)

1. Kies het **pad van de virtuele naam ruimte** dat u voor de client wilt gebruiken. Deze paden zijn gekoppeld aan exports op het back-end-opslag systeem.

   ![Scherm opname van het veld ' virtuele naam ruimte-pad ', waarbij selector geopend is.](media/mount-select-target.png)

   U kunt de virtuele naam ruimte paden bekijken en wijzigen op de pagina **naam ruimte** Portal. Lees [de geaggregeerde naam ruimte instellen](add-namespace-paths.md) om te zien hoe u dit kunt doen.

   Lees [de geaggregeerde naam ruimte plannen](hpc-cache-namespace.md)voor meer informatie over de geaggregeerde namespace-functie van de Azure HPC-cache.

1. Het **koppelings** veld in stap drie wordt automatisch gevuld met een aangepaste koppelings opdracht die gebruikmaakt van het koppel adres, het pad naar de virtuele naam ruimte en het pad van de client dat u in de vorige velden hebt ingesteld.

   Klik op het symbool kopiëren aan de rechter kant van het veld om het automatisch naar het klem bord te kopiëren.

   ![scherm afbeelding van het opdracht veld voor het koppelen van het prototype, met de knop tekst voor het kopiëren naar het klem bord](media/mount-command-copy.png)

1. Gebruik de gekopieerde koppeling opdracht op de client computer om deze te verbinden met de Azure HPC-cache. U kunt de opdracht rechtstreeks vanaf de client opdracht regel geven of de koppelings opdracht in een client installatie script of sjabloon toevoegen.

## <a name="understand-mount-command-syntax"></a>Syntaxis van koppelings opdracht

De opdracht mount heeft de volgende vorm:

> sudo-koppeling {*Options*} *cache_mount_address*:/*namespace_path* *local_path*

Voorbeeld:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

Nadat deze opdracht is voltooid, wordt de inhoud van de opslag export weer gegeven in de ``hpccache`` map op de client.

### <a name="mount-command-options"></a>Opdracht Opties koppelen

Voor een robuuste client koppeling geeft u deze instellingen en argumenten door in de opdracht Mount:

> mount-o hard, proto = TCP, mountproto = TCP, retry = 30 $ {CACHE_IP_ADDRESS}:/$ {NAMESPACE_PATH} $ {LOCAL_FILESYSTEM_MOUNT_POINT}

| Aanbevolen opdracht instellingen voor koppelen | Beschrijving |
--- | ---
``hard`` | Zachte koppelingen naar de Azure HPC-cache zijn gekoppeld aan toepassings fouten en mogelijke gegevens verlies.
``proto=tcp`` | Deze optie ondersteunt de juiste verwerking van NFS-netwerkfouten.
``mountproto=tcp`` | Deze optie ondersteunt de juiste verwerking van netwerkfouten voor koppelingsbewerkingen.
``retry=<value>`` | Stel ``retry=30`` in om tijdelijke koppelingsfouten te voorkomen. (Een andere waarde wordt aanbevolen in voorgrondkoppelingen.)

### <a name="find-mount-command-components"></a>Koppelings onderdelen zoeken

Als u een koppelings opdracht wilt maken zonder de **koppelings instructies** pagina te gebruiken, kunt u de koppelings adressen vinden op de pagina cache- **overzicht** en de virtuele naam ruimte paden op de pagina **naam** ruimte.

![scherm afbeelding van de overzichts pagina van het Azure HPC-cache-exemplaar, met een markeer stift rond de lijst met koppelings adressen op de rechter benedenhoek](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> De cache koppel adressen komen overeen met netwerk interfaces in het subnet van de cache. In een resource groep worden deze Nic's vermeld met namen die eindigen op `-cluster-nic-` en een getal. Wijzig of verwijder deze interfaces niet, of de cache is niet meer beschikbaar.

De virtuele naam ruimte paden worden weer gegeven op de pagina **naam ruimte** -instellingen van de cache.

![scherm afbeelding van de pagina Portal-instellingen > naam ruimte met een selectie vakje rondom de eerste kolom van de tabel: "pad van de naam"](media/view-namespace-paths.png)

## <a name="next-steps"></a>Volgende stappen

* Als u gegevens naar de opslag doelen van de cache wilt verplaatsen, leest u [nieuwe Azure Blob-opslag vullen](hpc-cache-ingest.md).
