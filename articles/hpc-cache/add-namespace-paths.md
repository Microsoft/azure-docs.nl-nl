---
title: De Azure HPC Cache geaggregeerde naamruimte configureren
description: Client-gerichte paden voor back-endopslag maken met Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: 2cb8db4e73a8f4fa299031070bffc15a2b754d7e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870369"
---
# <a name="set-up-the-aggregated-namespace"></a>De geaggregeerde naamruimte instellen

Nadat u opslagdoelen hebt maken, moet u er ook naamruimtepaden voor maken. Clientmachines gebruiken deze virtuele paden voor toegang tot bestanden via de cache in plaats van rechtstreeks verbinding te maken met de back-endopslag. Met dit systeem kunnen cachebeheerders back-endopslagsystemen wijzigen zonder dat ze clientinstructies opnieuw moeten schrijven.

Lees [De geaggregeerde naamruimte plannen](hpc-cache-namespace.md) voor meer informatie over deze functie.

Op **de pagina Naamruimte** in Azure Portal worden de paden weergegeven die clients gebruiken voor toegang tot uw gegevens via de cache. Gebruik deze pagina om naamruimtepaden te maken, te verwijderen of te wijzigen. U kunt ook naamruimtepaden configureren met behulp van de Azure CLI.

Alle client-gerichte paden die voor deze cache zijn gedefinieerd, worden vermeld op de **pagina Naamruimte.** Opslagdoelen die nog geen naamruimtepaden hebben gedefinieerd, worden niet weergegeven in de tabel.

U kunt de tabelkolommen sorteren om meer inzicht te krijgen in de geaggregeerde naamruimte van uw cache. Klik op de pijlen in de kolomkoppen om de paden te sorteren.

[![schermopname van de portalnaamruimtepagina met twee paden in een tabel. Kolomkoppen: Naamruimtepad, Opslagdoel, Exportpad en Export-subdirectory en Beleid voor clienttoegang. De padnamen in de eerste kolom zijn koppelingen die kunnen worden geklikt. Bovenste knoppen: pad naar naamruimte toevoegen, vernieuwen, verwijderen ](media/namespace-page.png)](media/namespace-page.png#lightbox)

## <a name="add-or-edit-namespace-paths"></a>Naamruimtepaden toevoegen of bewerken

U moet ten minste één naamruimtepad maken voordat clients toegang hebben tot het opslagdoel. (Lees [De Azure HPC Cache](hpc-cache-mount.md) voor meer informatie over clienttoegang.)

Als u onlangs een opslagdoel hebt toegevoegd of een toegangsbeleid hebt aangepast, kan het een paar minuten duren voordat u een naamruimtepad kunt maken.

### <a name="blob-namespace-paths"></a>Blob-naamruimtepaden

Een Azure Blob Storage-doel kan slechts één naamruimtepad hebben.

Volg de onderstaande instructies om het pad in te stellen of te wijzigen met de Azure Portal of Azure CLI.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Laad vanuit Azure Portal pagina **Naamruimteinstellingen.** U kunt naamruimtepaden toevoegen, wijzigen of verwijderen vanaf deze pagina.

* **Voeg een nieuw pad toe:** Klik bovenaan **op de** knop + Toevoegen en vul de gegevens in het bewerkingsvenster in.

  ![Schermopname van de velden voor het bewerken van naamruimten toevoegen met een blobopslagdoel geselecteerd. De export- en subdirectorypaden zijn ingesteld op / en kunnen niet worden bewerkt.](media/namespace-add-blob.png)

  * Voer het pad in dat clients gebruiken om toegang te krijgen tot dit opslagdoel.

  * Selecteer welk toegangsbeleid voor dit pad moet worden gebruikt. Meer informatie over het aanpassen van clienttoegang in [Clienttoegangsbeleid gebruiken.](access-policies.md)

  * Selecteer het opslagdoel in de vervolgkeuzelijst. Als een blobopslagdoel al een naamruimtepad heeft, kan dit niet worden geselecteerd.

  * Voor een Azure Blob Storage-doel worden de export- en subdirectorypaden automatisch ingesteld op ``/`` .

* **Een bestaand pad wijzigen:** Klik op het naamruimtepad. Het deelvenster Bewerken wordt geopend. U kunt het pad en het toegangsbeleid wijzigen, maar u kunt niet wijzigen in een ander opslagdoel.

* **Een naamruimtepad verwijderen:** Schakel het selectievakje links van het pad in en klik op **de knop** Verwijderen.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure CLI instellen voor Azure HPC Cache](./az-cli-prerequisites.md).

Wanneer u de Azure CLI gebruikt, moet u een naamruimtepad toevoegen wanneer u het opslagdoel maakt. Lees [Een nieuw Azure Blob Storage-doel toevoegen](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) voor meer informatie.

Gebruik de opdracht [az hpc-cache blob-storage-target update](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update) om het pad naar de naamruimte van het doel bij te werken. De argumenten voor de opdracht update zijn vergelijkbaar met de argumenten in de opdracht create, behalve dat u de containernaam of het opslagaccount niet door te geven.

U kunt een naamruimtepad niet verwijderen uit een blobopslagdoel met de Azure CLI, maar u kunt het pad overschrijven met een andere waarde.

---

### <a name="nfs-namespace-paths"></a>NFS-naamruimtepaden

Een NFS-opslagdoel kan meerdere virtuele paden hebben, zolang elk pad een andere export of subdirectory in hetzelfde opslagsysteem vertegenwoordigt.

Houd er bij het plannen van uw naamruimte voor een NFS-opslagdoel rekening mee dat elk pad uniek moet zijn en geen subdirectory van een ander naamruimtepad kan zijn. Als u bijvoorbeeld een naamruimtepad hebt met de naam , kunt u niet ook ``/parent-a`` naamruimtepaden zoals ``/parent-a/user1`` en ``/parent-a/user2`` maken. Deze mappaden zijn al toegankelijk in de naamruimte als subdirectory's van ``/parent-a`` .

Alle naamruimtepaden voor een NFS-opslagsysteem worden op één opslagdoel gemaakt. De meeste cacheconfiguraties kunnen maximaal tien naamruimtepaden per opslagdoel ondersteunen, maar grotere configuraties kunnen maximaal 20 ondersteunen.

In deze lijst wordt het maximum aantal naamruimtepaden per configuratie weergegeven.

* Tot 2 GB/s doorvoer:

  * 3 TB cache - 10 naamruimtepaden
  * 6 TB cache - 10 naamruimtepaden
  * 12 TB cache - 20 naamruimtepaden

* Tot 4 GB/s doorvoer:

  * 6 TB cache - 10 naamruimtepaden
  * 12 TB cache - 10 naamruimtepaden
  * 24 TB cache -20 naamruimtepaden

* Tot 8 GB/s doorvoer:

  * 12 TB cache - 10 naamruimtepaden
  * 24 TB cache - 10 naamruimtepaden
  * 48 TB cache - 20 naamruimtepaden

Geef voor elk NFS-naamruimtepad het client-gerichte pad, de export van het opslagsysteem en eventueel een exportsubdirectory op.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Laad vanuit Azure Portal pagina **Naamruimteinstellingen.** U kunt naamruimtepaden toevoegen, bewerken of verwijderen vanaf deze pagina.

* **Een nieuw pad toevoegen:** Klik bovenaan **op de** knop + Toevoegen en vul de gegevens in het bewerkingsvenster in.
* **Een bestaand pad wijzigen:** Klik op het naamruimtepad. Het deelvenster Bewerken wordt geopend en u kunt het pad wijzigen.
* **Een naamruimtepad verwijderen:** Schakel het selectievakje links van het pad in en klik op **de knop** Verwijderen.

Vul deze waarden in voor elk naamruimtepad:

* **Naamruimtepad:** het client-gerichte bestandspad.

* **Beleid voor clienttoegang:** selecteer welk toegangsbeleid voor dit pad moet worden gebruikt. Meer informatie over het aanpassen van clienttoegang in [Clienttoegangsbeleid gebruiken.](access-policies.md)

* **Opslagdoel:** als u een nieuw naamruimtepad maakt, selecteert u een opslagdoel in de vervolgkeuzelijst.

* **Exportpad:** voer het pad naar de NFS-export in. Zorg ervoor dat u de exportnaam correct typt: de portal valideert de syntaxis voor dit veld, maar controleert de export pas wanneer u de wijziging hebt indienen.

* **Subdirectory exporteren:** als u wilt dat met dit pad een specifieke subdirectory van de export wordt bevestigd, voert u deze hier in. Zo niet, laat dit veld leeg.

![schermopname van de naamruimtepagina van de portal met de pagina bewerken aan de rechterkant geopend. In het bewerkingsformulier worden instellingen voor een nfs-pad voor de naamruimte van de opslagdoelruimte](media/namespace-edit-nfs.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure CLI instellen voor Azure HPC Cache.](./az-cli-prerequisites.md)

Wanneer u de Azure CLI gebruikt, moet u ten minste één naamruimtepad toevoegen wanneer u het opslagdoel maakt. Lees [Een nieuw NFS-opslagdoel toevoegen](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) voor meer informatie.

Gebruik de opdracht [az hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update) om het pad naar de naamruimte van het doel bij te werken of om extra paden toe te voegen. Gebruik de ``--junction`` optie om alle naamruimtepaden op te geven die u wilt.

De opties die worden gebruikt voor de update opdracht zijn vergelijkbaar met de "maken"-opdracht, behalve dat u de gegevens van het opslagsysteem (IP-adres of hostnaam) niet door te geven en het gebruiksmodel is optioneel. Lees [Een nieuw NFS-opslagdoel toevoegen](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) voor meer informatie over de syntaxis van de ``--junction`` optie.

---

### <a name="adls-nfs-namespace-paths-preview"></a>ADLS-NFS-naamruimtepaden (PREVIEW)

Net als een normaal blobopslagdoel heeft een ADLS-NFS-opslagdoel slechts één export, zodat het slechts één naamruimtepad kan hebben.

Volg de onderstaande instructies om het pad in te stellen of te wijzigen met de Azure Portal.

Laad de **pagina Naamruimteinstellingen.**

* **Voeg een nieuw pad toe:** Klik bovenaan **op de** knop + Toevoegen en vul de gegevens in het bewerkingsvenster in.

  ![Schermopname van de bewerkingsvelden voor de naamruimte toevoegen met een ADLS-NFS-opslagdoel geselecteerd. De export- en subdirectorypaden zijn ingesteld op / en kunnen niet worden bewerkt.](media/namespace-add-adls.png)

  * Voer het pad in dat clients gebruiken om toegang te krijgen tot dit opslagdoel.

  * Selecteer welk toegangsbeleid u voor dit pad wilt gebruiken. Meer informatie over het aanpassen van clienttoegang in [Beleid voor clienttoegang gebruiken.](access-policies.md)

  * Selecteer het opslagdoel in de vervolgkeuzelijst. Als een ADLS-NFS-opslagdoel al een naamruimtepad heeft, kan dit niet worden geselecteerd.

  * Voor een ADLS-NFS-opslagdoel worden de export- en subdirectorypaden automatisch ingesteld op ``/`` .

* **Een bestaand pad wijzigen:** Klik op het naamruimtepad. Het deelvenster Bewerken wordt geopend. U kunt het pad en het toegangsbeleid wijzigen, maar u kunt niet wijzigen in een ander opslagdoel.

* **Een naamruimtepad verwijderen:** Schakel het selectievakje links van het pad in en klik op **de knop** Verwijderen.

## <a name="next-steps"></a>Volgende stappen

Nadat u de geaggregeerde naamruimte voor uw opslagdoelen hebt aanmaken, kunt u clients aan de cache monteren. Lees deze artikelen voor meer informatie.

* [De Azure HPC Cache koppelen](hpc-cache-mount.md)
* [Gegevens verplaatsen naar Azure Blob-opslag](hpc-cache-ingest.md)
