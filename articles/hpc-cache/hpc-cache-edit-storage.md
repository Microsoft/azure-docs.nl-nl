---
title: Opslagdoelen Azure HPC Cache bijwerken
description: Opslagdoelen Azure HPC Cache bewerken
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: ebf68c1eb06984e2de8114c53e1bb55d52aed70a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862629"
---
# <a name="edit-storage-targets"></a>Opslagdoelen bewerken

U kunt opslagdoelen verwijderen of wijzigen met de Azure Portal of met behulp van de Azure CLI.

Afhankelijk van het type opslag kunt u deze opslagdoelwaarden wijzigen:

* Voor Blob Storage-doelen kunt u het pad naar de naamruimte en het toegangsbeleid wijzigen.

* Voor NFS-opslagdoelen kunt u deze waarden wijzigen:

  * Naamruimtepaden
  * Toegangsbeleid
  * De subdirectory voor het exporteren of exporteren van opslag die is gekoppeld aan een naamruimtepad
  * Gebruiksmodel

* Voor ADLS-NFS-opslagdoelen kunt u het pad naar de naamruimte, het toegangsbeleid en het gebruiksmodel wijzigen.

U kunt de naam, het type of het back-endopslagsysteem van een opslagdoel niet bewerken (blobcontainer of NFS-hostnaam/IP-adres). Als u deze eigenschappen wilt wijzigen, verwijdert u het opslagdoel en maakt u een vervanging met de nieuwe waarde.

> [!TIP]
> In [de video Azure HPC Cache ziet](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) u hoe u een opslagdoel in de Azure Portal.

## <a name="remove-a-storage-target"></a>Een opslagdoel verwijderen

### <a name="portal"></a>[Portal](#tab/azure-portal)

Als u een opslagdoel wilt verwijderen, opent u **de pagina Opslagdoelen.** Selecteer het opslagdoel in de lijst en klik op **de knop** Verwijderen.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure CLI instellen voor Azure HPC Cache.](./az-cli-prerequisites.md)

Gebruik [az hpc-cache storage-target remove om](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage_target_remove) een opslagdoel uit de cache te verwijderen.

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

Als u een opslagdoel verwijdert, wordt de associatie van het opslagsysteem met dit Azure HPC Cache-systeem verwijderd, maar wordt het back-endopslagsysteem niet gewijzigd. Als u bijvoorbeeld een Azure Blob Storage-container hebt gebruikt, bestaan de container en de inhoud ervan nog steeds nadat u deze uit de cache hebt verwijderd. U kunt de container toevoegen aan een Azure HPC Cache, deze opnieuw toevoegen aan deze cache of deze verwijderen met de Azure Portal.

Bestandswijzigingen die zijn opgeslagen in de cache, worden naar het back-endopslagsysteem geschreven voordat het opslagdoel wordt verwijderd. Dit proces kan een uur of meer duren als er veel gewijzigde gegevens in de cache staan.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Het pad naar de naamruimte van een blobopslagdoel wijzigen

Naamruimtepaden zijn de paden die clients gebruiken om dit opslagdoel te mounten. (Lees Plan the [aggregated namespace (De geaggregeerde naamruimte plannen)](hpc-cache-namespace.md) en [Set up the aggregated namespace (De geaggregeerde naamruimte instellen) voor meer informatie.](add-namespace-paths.md)

Het naamruimtepad is de enige update die u kunt maken op een Azure Blob Storage-doel. Gebruik de Azure Portal of de Azure CLI om deze te wijzigen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Gebruik de **pagina Naamruimte** voor uw Azure HPC Cache. De naamruimtepagina wordt uitgebreid beschreven in het artikel De geaggregeerde [naamruimte instellen.](add-namespace-paths.md)

Klik op de naam van het pad dat u wilt wijzigen en maak het nieuwe pad in het bewerkingsvenster dat wordt weergegeven.

![Schermopname van de naamruimtepagina nadat u op een pad naar een Blob-naamruimte hebt geklikt: de bewerkingsvelden worden weergegeven in een deelvenster aan de rechterkant](media/update-namespace-blob.png)

Nadat u wijzigingen hebt aangebracht, klikt u **op OK** om het opslagdoel bij te werken of klikt u op **Annuleren om** wijzigingen te verwijderen.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure CLI instellen voor Azure HPC Cache.](./az-cli-prerequisites.md)

Als u de naamruimte van een blob-opslagdoel wilt wijzigen met de Azure CLI, gebruikt u de opdracht [az hpc-cache blob-storage-target update.](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update) Alleen de `--virtual-namespace-path` waarde kan worden gewijzigd.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Een NFS-opslagdoel bijwerken

Voor NFS-opslagdoelen kunt u virtuele naamruimtepaden wijzigen of toevoegen, de NFS-export- of subdirectorywaarden wijzigen waar een naamruimtepad naar wijst en het gebruiksmodel wijzigen.

Opslagdoelen in caches met bepaalde typen aangepaste DNS-instellingen hebben ook een besturingselement voor het vernieuwen van hun IP-adressen. (Dit soort configuratie is zeldzaam.)

Hieronder vindt u meer informatie:

* [Geaggregeerde naamruimtewaarden wijzigen](#change-aggregated-namespace-values) (pad naar virtuele naamruimte, toegangsbeleid, export- en export-subdirectory)
* [Het gebruiksmodel wijzigen](#change-the-usage-model)
* [DNS vernieuwen](#update-ip-address-custom-dns-configurations-only)

### <a name="change-aggregated-namespace-values"></a>Geaggregeerde naamruimtewaarden wijzigen

U kunt de Azure Portal of de Azure CLI gebruiken om het pad naar de client gerichte naamruimte, de opslagexport en de exportsubdirectory te wijzigen (indien gebruikt).

Lees de richtlijnen in [Paden voor NFS-naamruimte toevoegen](add-namespace-paths.md#nfs-namespace-paths) als u een herinnering nodig hebt over het maken van meerdere geldige paden op één opslagdoel.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Gebruik de **pagina Naamruimte** voor uw Azure HPC Cache naamruimtewaarden bij te werken. Deze pagina wordt gedetailleerder beschreven in het artikel [De geaggregeerde naamruimte instellen.](add-namespace-paths.md)

![schermopname van de portalnaamruimtepagina met de NFS-updatepagina aan de rechterkant geopend](media/update-namespace-nfs.png)

1. Klik op de naam van het pad dat u wilt wijzigen.
1. Gebruik het bewerkingsvenster om waarden voor nieuw virtueel pad, exporteren of subdirectory in te voeren of om een ander toegangsbeleid te selecteren.
1. Nadat u wijzigingen hebt aangebracht, klikt u **op OK om** het opslagdoel bij te werken of op Annuleren **om** wijzigingen te verwijderen.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure CLI instellen voor Azure HPC Cache.](./az-cli-prerequisites.md)

Gebruik de optie in de opdracht ``--junction`` [az hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target) om het naamruimtepad, de NFS-export of de export-subdirectory te wijzigen.

De ``--junction`` parameter gebruikt deze waarden:

* ``namespace-path`` - Het client gerichte virtuele bestandspad
* ``nfs-export`` - De export van het opslagsysteem om te koppelen aan het client-gerichte pad
* ``target-path`` (optioneel) - Een subdirectory van de export, indien nodig

Voorbeeld: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

U moet alle drie de waarden voor elk pad in de ``--junction`` instructie. Gebruik de bestaande waarden voor waarden die u niet wilt wijzigen.

De cachenaam, de doelnaam van de opslag en de resourcegroep zijn ook vereist in alle updateopdrachten.

Voorbeeldopdracht:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Het gebruiksmodel wijzigen

Het gebruiksmodel is van invloed op de manier waarop de cache gegevens bewaart. Lees [Meer informatie over cachegebruiksmodellen](cache-usage-models.md) voor meer informatie.

> [!NOTE]
> Als u gebruiksmodellen wijzigt, moet u mogelijk clients opnieuw ontkoppelen om NLM-fouten te voorkomen. Lees [Weten wanneer clients opnieuw moeten worden ontkoppeld](cache-usage-models.md#know-when-to-remount-clients-for-nlm) voor meer informatie.

Gebruik een van deze methoden om het gebruiksmodel voor een NFS-opslagdoel te wijzigen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Wijzig het gebruiksmodel op de **pagina Opslagdoelen** in de Azure Portal. Klik op de naam van het opslagdoel dat u wilt wijzigen.

![schermopname van de pagina bewerken voor een NFS-opslagdoel](media/edit-storage-nfs.png)

Gebruik de vervolgkeuze selecteren om een nieuw gebruiksmodel te kiezen. Klik **op OK** om het opslagdoel bij te werken of klik op Annuleren **om** wijzigingen te verwijderen.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure CLI instellen voor Azure HPC Cache.](./az-cli-prerequisites.md)

Gebruik de [opdracht az hpc-cache nfs-storage-target update.](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update)

De update opdracht is bijna identiek aan de opdracht die u gebruikt om een NFS-opslagdoel toe te voegen. Raadpleeg [Een NFS-opslagdoel maken voor](hpc-cache-add-storage.md#create-an-nfs-storage-target) meer informatie en voorbeelden.

Als u het gebruiksmodel wilt wijzigen, moet u de optie ``--nfs3-usage-model`` bijwerken. Voorbeeld: ``--nfs3-usage-model WRITE_WORKLOAD_15``

De cachenaam, de doelnaam van de opslag en de resourcegroepwaarden zijn ook vereist.

Als u de namen van de gebruiksmodellen wilt controleren, gebruikt u de opdracht [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage-model-list).

Als de cache is gestopt of niet in orde is, wordt de update toegepast nadat de cache in orde is.

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>IP-adres bijwerken (alleen aangepaste DNS-configuraties)

Als uw cache een niet-standaard-DNS-configuratie gebruikt, is het mogelijk dat het IP-adres van uw NFS-opslagdoel wordt gewijzigd vanwege back-end-DNS-wijzigingen. Als uw DNS-server het IP-adres van het back-endopslagsysteem wijzigt, kan Azure HPC Cache toegang tot het opslagsysteem verliezen.

In het ideale geval moet u samenwerken met de manager van het aangepaste DNS-systeem van uw cache om te plannen voor eventuele updates, omdat deze wijzigingen ervoor zorgen dat opslag niet beschikbaar is.

Als u het door DNS opgegeven IP-adres van een opslagdoel moet bijwerken, is er een knop in de lijst Opslagdoelen. Klik **op DNS vernieuwen** om een query uit te voeren op de aangepaste DNS-server voor een nieuw IP-adres.

![Schermopname van de lijst met opslagdoel. Voor één opslagdoel, de '...' menu in de kolom rechts is geopend en er worden twee opties weergegeven: Verwijderen en DNS vernieuwen.](media/refresh-dns.png)

Als dit lukt, duurt de update minder dan twee minuten. U kunt slechts één opslagdoel per keer vernieuwen; wacht tot de vorige bewerking is voltooid voordat u een andere bewerking probeert.

## <a name="update-an-adls-nfs-storage-target-preview"></a>Een ADLS-NFS-opslagdoel bijwerken (PREVIEW)

Net als bij NFS-doelen kunt u het naamruimtepad en het gebruiksmodel voor ADLS-NFS-opslagdoelen wijzigen.

### <a name="change-an-adls-nfs-namespace-path"></a>Een pad naar een ADLS-NFS-naamruimte wijzigen

Gebruik de **pagina Naamruimte** voor uw Azure HPC Cache naamruimtewaarden bij te werken. Deze pagina wordt gedetailleerder beschreven in het artikel [De geaggregeerde naamruimte instellen.](add-namespace-paths.md)

![schermopname van de portalnaamruimtepagina met een ads-NFS-updatepagina aan de rechterkant geopend](media/update-namespace-adls.png)

1. Klik op de naam van het pad dat u wilt wijzigen.
1. Gebruik het bewerkingsvenster om een nieuw virtueel pad in te voeren of het toegangsbeleid bij te werken.
1. Nadat u wijzigingen hebt aangebracht, klikt u **op OK** om het opslagdoel bij te werken of op **Annuleren om** wijzigingen te verwijderen.

### <a name="change-adls-nfs-usage-models"></a>ADLS-NFS-gebruiksmodellen wijzigen

De configuratie voor ADLS-NFS-gebruiksmodellen is identiek aan de selectie van het NFS-gebruiksmodel. Lees de portalinstructies in [Het gebruiksmodel wijzigen](#change-the-usage-model) in de sectie NFS hierboven. Aanvullende hulpprogramma's voor het bijwerken van ADLS-NFS-opslagdoelen zijn in ontwikkeling.


## <a name="next-steps"></a>Volgende stappen

* Lees [Opslagdoelen toevoegen](hpc-cache-add-storage.md) voor meer informatie over deze opties.
* Lees [De geaggregeerde naamruimte plannen voor](hpc-cache-namespace.md) meer tips over het gebruik van virtuele paden.
