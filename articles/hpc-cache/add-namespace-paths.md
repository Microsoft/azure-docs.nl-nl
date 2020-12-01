---
title: De geaggregeerde naam ruimte voor de Azure HPC-cache configureren
description: Client gerichte paden maken voor back-end-opslag met Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: e525fc0705dffcd4765e6a1f6c5235bdef260fcd
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339673"
---
# <a name="set-up-the-aggregated-namespace"></a>De geaggregeerde naam ruimte instellen

Nadat u opslag doelen hebt gemaakt, moet u ook naam ruimte paden maken. Client machines gebruiken deze virtuele paden voor toegang tot bestanden via de cache in plaats van rechtstreeks verbinding te maken met de back-end-opslag. Met dit systeem kunnen cache beheerders back-end opslag systemen wijzigen zonder de client instructies opnieuw te hoeven schrijven.

Raadpleeg [de geaggregeerde naam ruimte plannen](hpc-cache-namespace.md) voor meer informatie over deze functie.

De **naam ruimte** pagina in het Azure portal toont de paden die clients gebruiken voor toegang tot uw gegevens via de cache. Op deze pagina kunt u naam ruimte paden maken, verwijderen of wijzigen. U kunt ook naam ruimte paden configureren met behulp van de Azure CLI.

Alle bestaande paden die op de client zijn gericht, worden weer gegeven op de pagina **naam ruimte** . Als een opslag doel geen paden heeft, wordt deze niet weer gegeven in de tabel.

U kunt de tabel kolommen sorteren door op de pijlen te klikken en beter inzicht te krijgen in de geaggregeerde naam ruimte van uw cache.

![scherm afbeelding van de pagina Portal naam ruimte met twee paden in een tabel. Kolom koppen: pad naar naam ruimte, opslag doel, exportpad en export-submap. De items in de eerste kolom zijn klikable links. Top knoppen: pad naar naam ruimte toevoegen, vernieuwen, verwijderen](media/namespace-page.png)

## <a name="add-or-edit-client-facing-namespace-paths"></a>Aan client gerichte naam ruimte paden toevoegen of bewerken

U moet ten minste één pad naar de naam ruimte maken voordat clients toegang hebben tot het opslag doel. (Lees [de Azure HPC-cache koppelen](hpc-cache-mount.md) voor meer informatie over client toegang.)

### <a name="blob-namespace-paths"></a>Paden voor BLOB-naam ruimten

Een Azure Blob-opslag doel kan slechts één naam ruimte-pad hebben.

Volg de onderstaande instructies om het pad in te stellen of te wijzigen met de Azure Portal of Azure CLI.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Laad de pagina **naam ruimte** -instellingen vanuit het Azure Portal. Op deze pagina kunt u naam ruimte paden toevoegen, wijzigen of verwijderen.

* **Een nieuw pad toevoegen:** Klik bovenaan op de knop **+ toevoegen** en vul de gegevens in het deel venster bewerken in.

  * Selecteer het opslag doel in de vervolg keuzelijst. (In deze scherm afbeelding kan het Blob-opslag doel niet worden geselecteerd omdat het al een pad voor de naam ruimte heeft.)

    ![Scherm afbeelding van de nieuwe naam ruimte velden bewerken met de opslag doel-selector beschikbaar](media/namespace-select-storage-target.png)

  * Voor een Azure Blob-opslag doel worden de export-en submap paden automatisch ingesteld op ``/`` .

* **Een bestaand pad wijzigen:** Klik op het pad naar de naam ruimte. Het deel venster bewerken wordt geopend en u kunt het pad wijzigen.

  ![Scherm afbeelding van de pagina met de naam ruimte na klikken op een pad naar een BLOB-naam ruimte: de velden bewerken worden in een deel venster aan de rechter kant weer gegeven](media/edit-namespace-blob.png)

* **Een pad naar een naam ruimte verwijderen:** Selecteer het selectie vakje links van het pad en klik op de knop **verwijderen** .

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure cli instellen voor Azure HPC-cache](./az-cli-prerequisites.md).

Wanneer u de Azure CLI gebruikt, moet u een pad naar een naam ruimte toevoegen wanneer u het opslag doel maakt. Lees [een nieuw Azure Blob-opslag doel toevoegen](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) voor meer informatie.

Als u het pad naar de naam ruimte van het doel wilt bijwerken, gebruikt u de opdracht [AZ HPC-cache Blob-Storage-doel update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) . De argumenten voor de opdracht update zijn vergelijkbaar met de argumenten in de opdracht Create, behalve dat u de container naam of het opslag account niet doorgeeft.

Het is niet mogelijk om een pad naar een naam ruimte te verwijderen vanuit een Blob Storage-doel met de Azure CLI, maar u kunt het pad overschrijven met een andere waarde.

---

### <a name="nfs-namespace-paths"></a>NFS-naam ruimte paden

Een NFS-opslag doel kan meerdere virtuele paden hebben, zolang elk pad een andere export of submap op hetzelfde opslag systeem vertegenwoordigt.

Houd er bij het plannen van uw naam ruimte voor een NFS-opslag doel rekening mee dat elk pad uniek moet zijn en kan geen submap van een ander pad voor de naam ruimte zijn. Als u bijvoorbeeld een naam ruimte-pad hebt aangeroepen ``/parent-a`` , kunt u ook geen naam ruimte paden maken, zoals ``/parent-a/user1`` en ``/parent-a/user2`` . Deze mappaden zijn al toegankelijk in de naam ruimte als submappen van ``/parent-a`` .

Alle naam ruimte paden voor een NFS-opslag systeem worden op één opslag doel gemaakt. De meeste cache configuraties kunnen Maxi maal tien naam ruimte paden per opslag doel ondersteunen, maar grotere configuraties kunnen Maxi maal 20 ondersteunen.

In deze lijst wordt het maximum aantal naam ruimte paden per configuratie weer gegeven.

* Maxi maal 2 GB/s door Voer:

  * 3 TB cache-10-naam ruimte paden
  * 6 TB cache-10-naam ruimte paden
  * 23 TB cache-20 naam ruimte paden

* Maxi maal 4 GB/s door Voer:

  * 6 TB cache-10-naam ruimte paden
  * 12 TB cache-10-naam ruimte paden
  * 24 TB cache-20 naam ruimte paden

* Maxi maal 8 GB/s door Voer:

  * 12 TB cache-10-naam ruimte paden
  * 24 TB cache-10-naam ruimte paden
  * 48 TB cache-20 naam ruimte paden

Geef voor elk pad van de NFS-naam ruimte het pad naar de client, het exporteren van het opslag systeem en optioneel een export submap op.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Laad de pagina **naam ruimte** -instellingen vanuit het Azure Portal. Op deze pagina kunt u naam ruimte paden toevoegen, bewerken of verwijderen.

* **Een nieuw pad toevoegen:** Klik bovenaan op de knop **+ toevoegen** en vul de gegevens in het deel venster bewerken in.
* **Een bestaand pad wijzigen:** Klik op het pad naar de naam ruimte. Het deel venster bewerken wordt geopend en u kunt het pad wijzigen.
* **Een pad naar een naam ruimte verwijderen:** Selecteer het selectie vakje links van het pad en klik op de knop **verwijderen** .

Vul deze waarden in voor elk pad naar de naam ruimte:

* **Pad naar de naam ruimte** -het pad naar de client.

* **Opslag doel** : als u een nieuw pad naar een naam ruimte maakt, selecteert u een opslag doel in de vervolg keuzelijst.

* **Pad exporteren** : Geef het pad naar de NFS-export op. Zorg ervoor dat u de naam van de export correct typt: de portal valideert de syntaxis voor dit veld, maar controleert de export pas nadat u de wijziging hebt verzonden.

* **Submap exporteren** : als u met dit pad een specifieke submap van de export wilt koppelen, voert u deze hier in. Als dat niet het geval is, laat u dit veld leeg.

![scherm afbeelding van de pagina Portal-naam ruimte met de update pagina aan de rechter kant openen](media/update-namespace-nfs.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure cli instellen voor Azure HPC-cache](./az-cli-prerequisites.md).

Wanneer u de Azure CLI gebruikt, moet u ten minste één pad naar de naam ruimte toevoegen wanneer u het opslag doel maakt. Lees [een nieuw NFS-opslag doel toevoegen](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) voor meer informatie.

Als u het pad naar de naam ruimte van het doel wilt bijwerken of extra paden wilt toevoegen, gebruikt u de opdracht [AZ HPC-cache NFS-Storage-doel update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) . Gebruik de ``--junction`` optie om alle gewenste naam ruimte paden op te geven.

De opties die voor de opdracht update worden gebruikt, zijn vergelijkbaar met de opdracht ' maken ', behalve dat u de gegevens van het opslag systeem (IP-adres of hostnaam) niet doorgeeft en het gebruiks model optioneel is. Lees [een nieuw NFS-opslag doel toevoegen](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) voor meer informatie over de syntaxis van de ``--junction`` optie.

---

## <a name="next-steps"></a>Volgende stappen

Nadat u de geaggregeerde naam ruimte voor uw opslag doelen hebt gemaakt, kunt u clients koppelen aan de cache. Lees deze artikelen voor meer informatie.

* [De Azure HPC Cache koppelen](hpc-cache-mount.md)
* [Gegevens verplaatsen naar Azure Blob-opslag](hpc-cache-ingest.md)
