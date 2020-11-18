---
title: Doel doelen voor Azure HPC-cache bijwerken
description: Doel doelen van de Azure HPC-cache bewerken
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 7b7bc87f83925baed15bc6a411f5b4e4f78df6cc
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654504"
---
# <a name="edit-storage-targets"></a>Opslagdoelen bewerken

U kunt opslag doelen verwijderen of wijzigen met de Azure Portal of door gebruik te maken van de Azure CLI.

Afhankelijk van het type opslag kunt u deze opslag doel waarden wijzigen:

* Voor Blob Storage-doelen kunt u het pad naar de naam ruimte wijzigen.

* Voor NFS-opslag doelen kunt u deze waarden wijzigen:

  * Naam ruimte paden
  * De export-of export submap van de opslag die is gekoppeld aan een naam ruimte-pad
  * Gebruiks model

U kunt de naam, het type of het back-end-opslag systeem (BLOB-container of NBS-hostnaam/IP-adres) van een opslag doel niet bewerken. Als u deze eigenschappen wilt wijzigen, verwijdert u het opslag doel en maakt u een vervanging met de nieuwe waarde.

> [!TIP]
> De [video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) voor het beheren van de HPC-cache van Azure laat zien hoe u een opslag doel bewerkt in de Azure Portal.

## <a name="remove-a-storage-target"></a>Een opslag doel verwijderen

### <a name="portal"></a>[Portal](#tab/azure-portal)

Als u een opslag doel wilt verwijderen, opent u de pagina **opslag doelen** . Selecteer het opslag doel in de lijst en klik op de knop **verwijderen** .

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure cli instellen voor Azure HPC-cache](./az-cli-prerequisites.md).

Gebruik [AZ HPC-cache Storage-doel Remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) om een opslag doel uit de cache te verwijderen.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

Als u een opslag doel verwijdert, wordt de koppeling van het opslag systeem met dit Azure HPC-cache systeem verwijderd, maar wordt het back-end-opslag systeem niet gewijzigd. Als u bijvoorbeeld een Azure Blob Storage-container hebt gebruikt, bestaan de container en de inhoud ervan nog steeds nadat u deze uit de cache hebt verwijderd. U kunt de container toevoegen aan een andere Azure HPC-cache door deze opnieuw toe te voegen aan deze cache of door de Azure Portal te verwijderen.

Alle bestands wijzigingen die in de cache zijn opgeslagen, worden naar het back-end-opslag systeem geschreven voordat het opslag doel wordt verwijderd. Dit proces kan een uur of langer duren als er veel gewijzigde gegevens in de cache staan.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Het pad van de naam ruimte van een Blob-opslag doel wijzigen

Naam ruimte paden zijn de paden die clients gebruiken om dit opslag doel te koppelen. (Voor meer informatie raadpleegt u [de geaggregeerde naam ruimte plannen](hpc-cache-namespace.md) en [de geaggregeerde naam ruimte instellen](add-namespace-paths.md)).

Het pad naar de naam ruimte is de enige update die u kunt maken in een Azure Blob-opslag doel. Gebruik de Azure Portal of de Azure CLI om deze te wijzigen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Gebruik de **naam ruimte** pagina voor uw Azure HPC-cache. De naam ruimte pagina wordt uitgebreid beschreven in het artikel [de geaggregeerde naam ruimte instellen](add-namespace-paths.md).

Klik op de naam van het pad dat u wilt wijzigen en maak het nieuwe pad in het bewerkings venster dat wordt weer gegeven.

![Scherm afbeelding van de pagina met de naam ruimte na klikken op een pad naar een BLOB-naam ruimte: de velden bewerken worden in een deel venster aan de rechter kant weer gegeven](media/edit-namespace-blob.png)

Nadat u wijzigingen hebt aangebracht, klikt u op **OK** om het opslag doel bij te werken of klikt u op **Annuleren** om de wijzigingen te negeren.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure cli instellen voor Azure HPC-cache](./az-cli-prerequisites.md).

Als u de naam ruimte van een Blob Storage-doel wilt wijzigen in de Azure CLI, gebruikt u de opdracht [AZ HPC-cache Blob-Storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update). Alleen de `--virtual-namespace-path` waarde kan worden gewijzigd.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Een NFS-opslag doel bijwerken

Voor NFS-opslag doelen kunt u virtuele naam ruimte paden wijzigen of toevoegen, de NFS-export-of-submap waarden wijzigen waarnaar een naam ruimte verwijst en het gebruiks model wijzigen.

Meer informatie vindt u hieronder:

* De waarden van de [geaggregeerde naam](#change-aggregated-namespace-values) ruimte (virtuele naam ruimte, export en export) wijzigen
* [Het gebruiks model wijzigen](#change-the-usage-model)

### <a name="change-aggregated-namespace-values"></a>Samengevoegde waarden van naam ruimte wijzigen

U kunt de Azure Portal of de Azure CLI gebruiken om het pad naar de client gerichte naam ruimte, het exporteren van de opslag en de export submap (indien gebruikt) te wijzigen.

Lees de richt lijnen in paden voor het [toevoegen van NFS-naam ruimten](add-namespace-paths.md#nfs-namespace-paths) als u een herinnering wilt over het maken van meerdere geldige paden voor één opslag doel.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Gebruik de **naam ruimte** pagina voor uw Azure HPC-cache om naam ruimte waarden bij te werken. Deze pagina wordt uitgebreid beschreven in het artikel [de geaggregeerde naam ruimte instellen](add-namespace-paths.md).

![scherm afbeelding van de pagina Portal-naam ruimte met de pagina NFS-update openen aan de rechter kant](media/update-namespace-nfs.png)

1. Klik op de naam van het pad dat u wilt wijzigen.
1. Gebruik het bewerkings venster om nieuwe waarden voor het virtuele pad, de export of de subdirectory te typen.
1. Nadat u wijzigingen hebt aangebracht, klikt u op **OK** om het opslag doel bij te werken of op **Annuleren** om de wijzigingen te negeren.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure cli instellen voor Azure HPC-cache](./az-cli-prerequisites.md).

Gebruik de ``--junction`` optie in de opdracht [AZ HPC-cache NFS-Storage-doel update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) om het pad naar de naam ruimte, de NFS-export of de export submap te wijzigen.

De ``--junction`` para meter gebruikt deze waarden:

* ``namespace-path`` -Het pad naar het client gerichte virtuele bestand
* ``nfs-export`` -Het opslag systeem exporteren om te koppelen aan het client gerichte pad
* ``target-path`` (optioneel): een submap van de export, indien nodig

Voorbeeld: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

U moet alle drie de waarden voor elk pad opgeven in de ``--junction`` instructie. Gebruik de bestaande waarden voor waarden die u niet wilt wijzigen.

De cache naam, de naam van het opslag doel en de resource groep zijn ook vereist in alle update-opdrachten.

Voor beeld opdracht:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Het gebruiks model wijzigen

Het gebruiks model is van invloed op de manier waarop de cache gegevens bewaart. Lees [een gebruiks model kiezen](hpc-cache-add-storage.md#choose-a-usage-model) voor meer informatie.

Gebruik een van de volgende methoden om het gebruiks model voor een NFS-opslag doel te wijzigen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Wijzig het gebruiks model op de pagina **opslag doelen** in het Azure Portal. Klik op de naam van het opslag doel dat u wilt wijzigen.

![scherm afbeelding van de bewerkings pagina voor een NFS-opslag doel](media/edit-storage-nfs.png)

Gebruik de vervolg keuzelijst om een nieuw gebruiks model te kiezen. Klik op **OK** om het opslag doel bij te werken of klik op **Annuleren** om de wijzigingen te negeren.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure cli instellen voor Azure HPC-cache](./az-cli-prerequisites.md).

Gebruik de opdracht [AZ HPC-cache NFS-Storage-doel update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target?view=azure-cli-latest#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) .

De opdracht update is bijna identiek aan de opdracht die u gebruikt om een NFS-opslag doel toe te voegen. Raadpleeg [een NFS-opslag doel maken](hpc-cache-add-storage.md#create-an-nfs-storage-target) voor meer informatie en voor beelden.

Als u het gebruiks model wilt wijzigen, moet u de ``--nfs3-usage-model`` optie bijwerken. Voorbeeld: ``--nfs3-usage-model WRITE_WORKLOAD_15``

De cache naam, de naam van het opslag doel en de waarden van de resource groep zijn ook vereist.

Als u de namen van de gebruiks modellen wilt controleren, gebruikt u de opdracht [AZ HPC-cache Usage-model List](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

Als de cache is gestopt of niet in orde is, wordt de update toegepast nadat de cache in orde is.

---

## <a name="next-steps"></a>Volgende stappen

* Lees [opslag doelen toevoegen](hpc-cache-add-storage.md) voor meer informatie over deze opties.
* [Plan de geaggregeerde naam ruimte](hpc-cache-namespace.md) voor meer tips over het gebruik van virtuele paden.
