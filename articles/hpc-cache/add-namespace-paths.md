---
title: De geaggregeerde naam ruimte voor de Azure HPC-cache configureren
description: Client gerichte paden maken voor back-end-opslag met Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: f45d5710f6feb8af2347ca298e07e8a4870d3d4f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103470456"
---
# <a name="set-up-the-aggregated-namespace"></a>De geaggregeerde naam ruimte instellen

Nadat u opslag doelen hebt gemaakt, moet u ook naam ruimte paden maken. Client machines gebruiken deze virtuele paden voor toegang tot bestanden via de cache in plaats van rechtstreeks verbinding te maken met de back-end-opslag. Met dit systeem kunnen cache beheerders back-end opslag systemen wijzigen zonder de client instructies opnieuw te hoeven schrijven.

Raadpleeg [de geaggregeerde naam ruimte plannen](hpc-cache-namespace.md) voor meer informatie over deze functie.

De **naam ruimte** pagina in het Azure portal toont de paden die clients gebruiken voor toegang tot uw gegevens via de cache. Op deze pagina kunt u naam ruimte paden maken, verwijderen of wijzigen. U kunt ook naam ruimte paden configureren met behulp van de Azure CLI.

Alle client gerichte paden die voor deze cache zijn gedefinieerd, worden weer gegeven op de pagina **naam ruimte** . Opslag doelen waarvoor geen naam ruimte paden zijn gedefinieerd, worden nog niet weer gegeven in de tabel.

U kunt de tabel kolommen sorteren om beter inzicht te krijgen in de geaggregeerde naam ruimte van uw cache. Klik op de pijlen in de kolom koppen om de paden te sorteren.

[![scherm afbeelding van de pagina Portal naam ruimte met twee paden in een tabel. Kolom koppen: pad naar naam ruimte, opslag doel, exportpad en export-submap en beleid voor client toegang. De namen van de paden in de eerste kolom zijn klikable links. Top knoppen: pad naar naam ruimte toevoegen, vernieuwen, verwijderen ](media/namespace-page.png)](media/namespace-page.png#lightbox)

## <a name="add-or-edit-namespace-paths"></a>Naam ruimte paden toevoegen of bewerken

U moet ten minste één pad naar de naam ruimte maken voordat clients toegang hebben tot het opslag doel. (Lees [de Azure HPC-cache koppelen](hpc-cache-mount.md) voor meer informatie over client toegang.)

### <a name="blob-namespace-paths"></a>Paden voor BLOB-naam ruimten

Een Azure Blob-opslag doel kan slechts één naam ruimte-pad hebben.

Volg de onderstaande instructies om het pad in te stellen of te wijzigen met de Azure Portal of Azure CLI.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Laad de pagina **naam ruimte** -instellingen vanuit het Azure Portal. Op deze pagina kunt u naam ruimte paden toevoegen, wijzigen of verwijderen.

* **Een nieuw pad toevoegen:** Klik bovenaan op de knop **+ toevoegen** en vul de gegevens in het deel venster bewerken in.

  ![Scherm opname van de velden naam ruimte toevoegen bewerken met een Blob-opslag doel geselecteerd. De export-en submap paden worden ingesteld op/en niet bewerkbaar.](media/namespace-add-blob.png)

  * Voer het pad in dat clients gebruiken voor toegang tot dit opslag doel.

  * Selecteer welk toegangs beleid moet worden gebruikt voor dit pad. Meer informatie over het aanpassen van client toegang in het [gebruik van beleids regels voor client toegang](access-policies.md).

  * Selecteer het opslag doel in de vervolg keuzelijst. Als een Blob-opslag doel al een pad naar een naam ruimte heeft, kan deze niet worden geselecteerd.

  * Voor een Azure Blob-opslag doel worden de export-en submap paden automatisch ingesteld op ``/`` .

* **Een bestaand pad wijzigen:** Klik op het pad naar de naam ruimte. Het deel venster bewerken wordt geopend. U kunt het pad en toegangs beleid wijzigen, maar u kunt niet overschakelen naar een ander opslag doel.

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
  * 12 TB cache-20 naam ruimte paden

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

* **Beleid voor client toegang** : Selecteer welk toegangs beleid moet worden gebruikt voor dit pad. Meer informatie over het aanpassen van client toegang in het [gebruik van beleids regels voor client toegang](access-policies.md).

* **Opslag doel** : als u een nieuw pad naar een naam ruimte maakt, selecteert u een opslag doel in de vervolg keuzelijst.

* **Pad exporteren** : Geef het pad naar de NFS-export op. Zorg ervoor dat u de naam van de export correct typt: de portal valideert de syntaxis voor dit veld, maar controleert de export pas nadat u de wijziging hebt verzonden.

* **Submap exporteren** : als u met dit pad een specifieke submap van de export wilt koppelen, voert u deze hier in. Als dat niet het geval is, laat u dit veld leeg.

![scherm afbeelding van de pagina Portal naam ruimte met de pagina bewerken aan de rechter kant. Het bewerkings formulier bevat instellingen voor het pad van een NFS-opslag doel naam ruimte](media/namespace-edit-nfs.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure cli instellen voor Azure HPC-cache](./az-cli-prerequisites.md).

Wanneer u de Azure CLI gebruikt, moet u ten minste één pad naar de naam ruimte toevoegen wanneer u het opslag doel maakt. Lees [een nieuw NFS-opslag doel toevoegen](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) voor meer informatie.

Als u het pad naar de naam ruimte van het doel wilt bijwerken of extra paden wilt toevoegen, gebruikt u de opdracht [AZ HPC-cache NFS-Storage-doel update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) . Gebruik de ``--junction`` optie om alle gewenste naam ruimte paden op te geven.

De opties die voor de opdracht update worden gebruikt, zijn vergelijkbaar met de opdracht ' maken ', behalve dat u de gegevens van het opslag systeem (IP-adres of hostnaam) niet doorgeeft en het gebruiks model optioneel is. Lees [een nieuw NFS-opslag doel toevoegen](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) voor meer informatie over de syntaxis van de ``--junction`` optie.

---

### <a name="adls-nfs-namespace-paths-preview"></a>ADLS-NFS-naam ruimte paden (PREVIEW-versie)

Net als bij een reguliere Blob Storage-doel heeft een ADLS-opslag doel slechts één export, zodat het slechts één naam ruimte-pad kan hebben.

Volg de onderstaande instructies om het pad in te stellen of te wijzigen met behulp van de Azure Portal.

Laad de pagina **naam ruimte** -instellingen.

* **Een nieuw pad toevoegen:** Klik bovenaan op de knop **+ toevoegen** en vul de gegevens in het deel venster bewerken in.

  ![Scherm afbeelding van de velden naam ruimte toevoegen bewerken met een ADLS-NFS-opslag doel geselecteerd. De export-en submap paden worden ingesteld op/en niet bewerkbaar.](media/namespace-add-adls.png)

  * Voer het pad in dat clients gebruiken voor toegang tot dit opslag doel.

  * Selecteer welk toegangs beleid moet worden gebruikt voor dit pad. Meer informatie over het aanpassen van client toegang in het [gebruik van beleids regels voor client toegang](access-policies.md).

  * Selecteer het opslag doel in de vervolg keuzelijst. Als een ADLS-NFS-opslag doel al een pad naar de naam ruimte heeft, kan dit niet worden geselecteerd.

  * Voor een ADLS-NFS-opslag doel worden de paden voor exporteren en mappen automatisch ingesteld op ``/`` .

* **Een bestaand pad wijzigen:** Klik op het pad naar de naam ruimte. Het deel venster bewerken wordt geopend. U kunt het pad en toegangs beleid wijzigen, maar u kunt niet overschakelen naar een ander opslag doel.

* **Een pad naar een naam ruimte verwijderen:** Selecteer het selectie vakje links van het pad en klik op de knop **verwijderen** .

## <a name="next-steps"></a>Volgende stappen

Nadat u de geaggregeerde naam ruimte voor uw opslag doelen hebt gemaakt, kunt u clients koppelen aan de cache. Lees deze artikelen voor meer informatie.

* [De Azure HPC Cache koppelen](hpc-cache-mount.md)
* [Gegevens verplaatsen naar Azure Blob-opslag](hpc-cache-ingest.md)
