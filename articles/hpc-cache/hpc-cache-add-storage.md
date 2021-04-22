---
title: Opslag toevoegen aan een Azure HPC Cache
description: Opslagdoelen definiëren zodat uw Azure HPC Cache uw on-premises NFS-systeem of Azure Blob-containers kan gebruiken voor bestandsopslag op lange termijn
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 708ad8bbfec9e3fd0176c53c111b5b5b25a5318f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862233"
---
# <a name="add-storage-targets"></a>Opslagdoelen toevoegen

*Opslagdoelen* zijn back-endopslag voor bestanden die toegankelijk zijn via een Azure HPC Cache. U kunt NFS-opslag (zoals een on-premises hardwaresysteem) toevoegen of gegevens opslaan in Azure Blob.

U kunt maximaal 20 verschillende opslagdoelen voor één cache definiëren. De cache presenteert alle opslagdoelen in één geaggregeerde naamruimte.

De naamruimtepaden worden afzonderlijk geconfigureerd nadat u de opslagdoelen hebt toevoegen. Over het algemeen kan een NFS-opslagdoel maximaal tien naamruimtepaden hebben, of meer voor een aantal grote configuraties. Lees [NFS-naamruimtepaden](add-namespace-paths.md#nfs-namespace-paths) voor meer informatie.

Houd er rekening mee dat de opslagexports toegankelijk moeten zijn vanuit het virtuele netwerk van uw cache. Voor on-premises hardwareopslag moet u mogelijk een DNS-server instellen die hostnamen voor NFS-opslagtoegang kan oplossen. Meer informatie in [DNS-toegang.](hpc-cache-prerequisites.md#dns-access)

Voeg opslagdoelen toe na het maken van uw cache. Volg dit proces:

1. [De cache maken](hpc-cache-create.md)
1. Een opslagdoel definiëren (informatie in dit artikel)
1. [Maak de client-gerichte paden](add-namespace-paths.md) (voor de [geaggregeerde naamruimte](hpc-cache-namespace.md))

De procedure voor het toevoegen van een opslagdoel is iets anders, afhankelijk van het type opslag dat wordt gebruikt. Details voor elk van deze vindt u hieronder.

Klik op de onderstaande afbeelding om een [videodemonstratie](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) te bekijken van het maken van een cache en het toevoegen van een opslagdoel vanuit de Azure Portal.

[![videominiature: Azure HPC Cache: Instellen (klik om naar de videopagina te gaan)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>Een nieuw Azure Blob Storage-doel toevoegen

Een nieuw Blob Storage-doel heeft een lege Blob-container of een container nodig die is gevuld met gegevens in de Azure HPC Cache-bestandssysteemindeling van de cloud. Lees meer over het vooraf laden van een Blob-container in [Gegevens verplaatsen naar Azure Blob Storage.](hpc-cache-ingest.md)

De Azure Portal **Opslagdoel toevoegen** bevat de optie om een nieuwe Blob-container te maken net voordat u deze toevoegt.

> [!NOTE]
> Gebruik voor aan NFS-mounted blob-opslag het [doeltype ADLS-NFS-opslag.](#)

### <a name="portal"></a>[Portal](#tab/azure-portal)

Open vanuit Azure Portal cache-exemplaar en klik op **Opslagdoelen** in de linkerzijbalk.

![schermopname van de instellingen > opslagdoelpagina, met twee bestaande opslagdoelen in een tabel en een markering rond de knop + Opslagdoel toevoegen boven de tabel](media/add-storage-target-button.png)

Op **de pagina Opslagdoelen** worden alle bestaande doelen weergegeven en wordt een koppeling weergegeven om een nieuwe toe te voegen.

Klik op **de knop Opslagdoel** toevoegen.

![schermopname van de pagina Opslagdoel toevoegen, gevuld met informatie voor een nieuw Azure Blob-opslagdoel](media/hpc-cache-add-blob.png)

Voer deze informatie in om een Azure Blob-container te definiëren.

* **Naam van opslagdoel:** stel een naam in die dit opslagdoel in de Azure HPC Cache.
* **Doeltype:** kies **Blob**.
* **Opslagaccount:** selecteer het account dat u wilt gebruiken.

  U moet het cache-exemplaar toestemming geven voor toegang tot het opslagaccount, zoals beschreven in [De toegangsrollen toevoegen.](#add-the-access-control-roles-to-your-account)

  Lees Vereisten voor Blob-opslag voor meer informatie over het soort opslagaccount [dat u kunt gebruiken.](hpc-cache-prerequisites.md#blob-storage-requirements)

* **Opslagcontainer:** selecteer de Blob-container voor dit doel of klik **op Nieuwe maken.**

  ![schermopname van het dialoogvenster om de naam en het toegangsniveau (privé) voor de nieuwe container op te geven](media/add-blob-new-container.png)

Wanneer u klaar bent, klikt u **op OK** om het opslagdoel toe te voegen.

> [!NOTE]
> Als de firewall van uw opslagaccount zo is ingesteld dat de toegang tot alleen geselecteerde netwerken wordt beperkt, gebruikt u de tijdelijke tijdelijke oplossing die wordt beschreven in Firewallinstellingen voor het [Blob Storage-account.](hpc-cache-blob-firewall-fix.md)

### <a name="add-the-access-control-roles-to-your-account"></a>De toegangsbeheerrollen toevoegen aan uw account

Azure HPC Cache maakt gebruik van op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../role-based-access-control/index.yml) om de cacheservice toegang te verlenen tot uw opslagaccount voor Azure Blob Storage-doelen.

De eigenaar van het opslagaccount [](../role-based-access-control/built-in-roles.md#storage-account-contributor) moet expliciet [](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) de rollen Inzender voor opslagaccounts en Bijdrager voor opslagblobgegevens toevoegen voor de gebruiker 'HPC Cache resourceprovider'.

U kunt dit van tevoren doen of door op een koppeling te klikken op de pagina waar u een Blob Storage-doel toevoegt. Houd er rekening mee dat het maximaal vijf minuten kan duren voordat de rolinstellingen zijn doorgegeven via de Azure-omgeving. U moet dus enkele minuten wachten nadat u de rollen hebt toegevoegd voordat u een opslagdoel maakt.

Stappen voor het toevoegen van de Azure-rollen:

1. Open de **pagina Toegangsbeheer (IAM)** voor het opslagaccount. (Met de koppeling op **de pagina Opslagdoel toevoegen** wordt deze pagina automatisch geopend voor het geselecteerde account.)

1. Klik op **+** de bovenaan de pagina en kies Een **roltoewijzing toevoegen.**

1. Selecteer de rol 'Inzender voor opslagaccounts' in de lijst.

1. Laat in **het veld Toegang toewijzen** aan de standaardwaarde geselecteerd ('Azure AD-gebruiker, -groep of service-principal').  

1. Zoek in **het** veld Selecteren naar 'hpc'.  Deze tekenreeks moet overeenkomen met één service-principal, met de naam HPC Cache Resource Provider. Klik op die principal om deze te selecteren.

   > [!NOTE]
   > Als een zoekopdracht naar 'hpc' niet werkt, gebruikt u in plaats daarvan de tekenreeks 'storagecache'. Gebruikers die hebben deelgenomen aan previews (vóór ga) moeten mogelijk de oudere naam voor de service-principal gebruiken.

1. Klik onderaan **op** de knop Opslaan.

1. Herhaal dit proces om de rol 'Bijdrager voor opslagblobgegevens' toe te wijzen.  

![schermopname van GUI voor roltoewijzing toevoegen](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>Vereiste: toegang tot opslagaccount

[Azure CLI instellen voor Azure HPC Cache.](./az-cli-prerequisites.md)

Voordat u een blobopslagdoel toevoegt, controleert u of de cache de juiste rollen heeft voor toegang tot het opslagaccount en of de firewallinstellingen het maken van het opslagdoel toestaan.

Azure HPC Cache maakt gebruik van op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../role-based-access-control/index.yml) om de cacheservice toegang te verlenen tot uw opslagaccount voor Azure Blob-opslagdoelen.

De eigenaar van het opslagaccount [](../role-based-access-control/built-in-roles.md#storage-account-contributor) moet expliciet [](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) de rollen Inzender voor opslagaccounts en Bijdrager voor opslagblobgegevens toevoegen voor de gebruiker 'HPC Cache resourceprovider'.

Het maken van het opslagdoel mislukt als de cache deze rollen niet heeft.

Het kan vijf minuten duren voordat de rolinstellingen zijn doorgegeven in de Azure-omgeving. U moet dus enkele minuten wachten nadat u de rollen hebt toegevoegd voordat u een opslagdoel maakt.

Lees [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure CLI](../role-based-access-control/role-assignments-cli.md) voor gedetailleerde instructies.

Controleer ook de firewallinstellingen van uw opslagaccount. Als de firewall zo is ingesteld dat de toegang tot alleen geselecteerde netwerken wordt beperkt, kan het maken van het opslagdoel mislukken. Gebruik de tijdelijke oplossing die wordt beschreven in [Firewallinstellingen voor Blob Storage-account omzeilen.](hpc-cache-blob-firewall-fix.md)

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Een blobopslagdoel toevoegen met Azure CLI

Gebruik de [interface az hpc-cache blob-storage-target add](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_add) om een Azure Blob Storage-doel te definiëren.

> [!NOTE]
> Voor de Azure CLI-opdrachten moet u momenteel een naamruimtepad maken wanneer u een opslagdoel toevoegt. Dit wijf af van het proces dat wordt gebruikt met de Azure Portal interface.

Naast de standaardparameters voor resourcegroep en cachenaam moet u de volgende opties opgeven voor het opslagdoel:

* ``--name`` - Stel een naam in die dit opslagdoel in de Azure HPC Cache.

* ``--storage-account`` - De account-id, in deze vorm: /subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/providers/Microsoft.Storage/storageAccounts/*<account_name>*

  Lees Vereisten voor Blob-opslag voor meer informatie over het soort opslagaccount [dat u kunt gebruiken.](hpc-cache-prerequisites.md#blob-storage-requirements)

* ``--container-name`` - Geef de naam op van de container die moet worden gebruikt voor dit opslagdoel.

* ``--virtual-namespace-path`` - Stel het client-gerichte bestandspad voor dit opslagdoel in. Paden tussen aanhalingstekens plaatsen. Lees [De geaggregeerde naamruimte plannen](hpc-cache-namespace.md) voor meer informatie over de functie voor virtuele naamruimte.

Voorbeeldopdracht:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Een nieuw NFS-opslagdoel toevoegen

Een NFS-opslagdoel heeft andere instellingen dan een Blob Storage-doel. Met de instelling voor het gebruiksmodel kan de cache efficiënt gegevens uit dit opslagsysteem in de cache opslaan.

![Schermopname van de pagina Opslagdoel toevoegen met NFS-doel gedefinieerd](media/add-nfs-target.png)

> [!NOTE]
> Voordat u een NFS-opslagdoel maakt, moet u ervoor zorgen dat uw opslagsysteem toegankelijk is vanuit de Azure HPC Cache voldoet aan de machtigingsvereisten. Het maken van het opslagdoel mislukt als de cache geen toegang heeft tot het opslagsysteem. Lees [NFS-opslagvereisten en](hpc-cache-prerequisites.md#nfs-storage-requirements) [Los problemen met DE NAS-configuratie en NFS-opslagdoel op](troubleshoot-nas.md) voor meer informatie.

### <a name="choose-a-usage-model"></a>Een gebruiksmodel kiezen
<!-- referenced from GUI by aka.ms link -->

Wanneer u een opslagdoel maakt dat gebruikmaakt van NFS om het opslagsysteem te bereiken, moet u een gebruiksmodel voor dat doel kiezen. Dit model bepaalt hoe uw gegevens in de cache worden opgeslagen.

Lees [Gebruiksmodellen begrijpen](cache-usage-models.md) voor meer informatie over al deze instellingen.

Met de ingebouwde gebruiksmodellen kunt u kiezen hoe u een snelle reactie in balans wilt brengen met het risico op het verkrijgen van verouderde gegevens. Als u de snelheid voor het lezen van bestanden wilt optimaliseren, is het wellicht niet belangrijk of de bestanden in de cache worden gecontroleerd op de back-endbestanden. Als u daarentegen wilt controleren of uw bestanden altijd up-to-date zijn met de externe opslag, kiest u een model dat regelmatig wordt gecontroleerd.

Deze drie opties hebben betrekking op de meeste situaties:

* **Leeszware, niet-regelmatig schrijf schrijf-** - versnelt de leestoegang tot bestanden die statisch zijn of zelden worden gewijzigd.

  Met deze optie worden bestanden uit lees- en schrijfbestanden van de client in de cache opgeslagen, maar worden client-schrijfingen onmiddellijk door naar de back-endopslag. Bestanden die zijn opgeslagen in de cache worden niet automatisch vergeleken met de bestanden op het NFS-opslagvolume.

  Gebruik deze optie niet als er een risico bestaat dat een bestand rechtstreeks op het opslagsysteem wordt gewijzigd zonder het eerst naar de cache te schrijven. Als dat gebeurt, is de in cache opgeslagen versie van het bestand niet meer gesynchroniseerd met het back-endbestand.

* **Meer dan 15% schrijfprestaties:** deze optie versnelt zowel de lees- als schrijfprestaties.

  Client-lees- en client-schrijf schrijfingen worden beide in de cache opgeslagen. Er wordt van uitgegaan dat bestanden in de cache nieuwer zijn dan bestanden op het back-endopslagsysteem. Bestanden in de cache worden alleen elke acht uur automatisch gecontroleerd op basis van de bestanden in de back-endopslag. Gewijzigde bestanden in de cache worden zonder extra wijzigingen naar het back-endopslagsysteem geschreven nadat ze 20 minuten in de cache zijn geweest.

  Gebruik deze optie niet als er clients zijn die het back-endopslagvolume rechtstreeks aan het volume toevoegen, omdat er een risico bestaat dat deze verouderde bestanden bevatten.

* Clients schrijven naar het **NFS-doel en** omzeilen de cache: kies deze optie als clients in uw werkstroom gegevens rechtstreeks naar het opslagsysteem schrijven zonder eerst naar de cache te schrijven of als u de consistentie van gegevens wilt optimaliseren.

  Bestanden die clients aanvragen, worden in de cache opgeslagen, maar alle wijzigingen in deze bestanden van de client worden onmiddellijk doorgegeven aan het back-endopslagsysteem. Bestanden in de cache worden vaak gecontroleerd op updates in de back-endversies. Deze verificatie houdt gegevensconsistentie bij wanneer bestanden rechtstreeks op het opslagsysteem worden gewijzigd in plaats van via de cache.

Lees Meer informatie over gebruiksmodellen voor [meer informatie over de andere opties.](cache-usage-models.md)

In deze tabel worden de verschillen tussen alle gebruiksmodellen samengevat:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

> [!NOTE]
> De **waarde voor back-endverificatie** geeft aan wanneer de cache de bestanden automatisch vergelijkt met bronbestanden in externe opslag. U kunt echter een vergelijking activeren door een clientaanvraag te verzenden die een readdirplus-bewerking op het back-endopslagsysteem bevat. Readdirplus is een standaard NFS-API (ook wel uitgebreid lezen genoemd) die mapmetagegevens retourneert, waardoor de cache bestanden kan vergelijken en bijwerken.

### <a name="create-an-nfs-storage-target"></a>Een NFS-opslagdoel maken

### <a name="portal"></a>[Portal](#tab/azure-portal)

Open vanuit Azure Portal cache-exemplaar en klik op **Opslagdoelen** op de linkerzijbalk.

![schermopname van de instellingen > opslagdoelpagina, met twee bestaande opslagdoelen in een tabel en een markering rond de knop + Opslagdoel toevoegen boven de tabel](media/add-storage-target-button.png)

Op **de pagina Opslagdoelen** worden alle bestaande doelen weergegeven en wordt een koppeling weergegeven om een nieuwe toe te voegen.

Klik op **de knop Opslagdoel** toevoegen.

![Schermopname van de pagina Opslagdoel toevoegen met NFS-doel gedefinieerd](media/add-nfs-target.png)

Geef deze informatie op voor een opslagdoel met NFS-ondersteuning:

* **Naam van opslagdoel:** stel een naam in die dit opslagdoel in de Azure HPC Cache.

* **Doeltype:** kies **NFS.**

* **Hostnaam:** voer het IP-adres of de Fully Qualified Domain Name in voor uw NFS-opslagsysteem. (Gebruik alleen een domeinnaam als uw cache toegang heeft tot een DNS-server die de naam kan oplossen.)

* **Gebruiksmodel:** kies een van de gegevens in de caching-profielen op basis van uw werkstroom, zoals wordt beschreven in [Een gebruiksmodel kiezen hierboven.](#choose-a-usage-model)

Wanneer u klaar bent, klikt u **op OK** om het opslagdoel toe te voegen.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure CLI instellen voor Azure HPC Cache](./az-cli-prerequisites.md).

Gebruik de Azure CLI-opdracht [az hpc-cache nfs-storage-target add om](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_add) het opslagdoel te maken.

> [!NOTE]
> Voor de Azure CLI-opdrachten moet u momenteel een naamruimtepad maken wanneer u een opslagdoel toevoegt. Dit wijs af van het proces dat wordt gebruikt met de Azure Portal interface.

Gebruik deze waarden naast de cachenaam en cacheresourcegroep:

* ``--name`` - Stel een naam in die dit opslagdoel in de Azure HPC Cache.
* ``--nfs3-target`` - Het IP-adres van uw NFS-opslagsysteem. (U kunt hier een volledig gekwalificeerde domeinnaam gebruiken als uw cache toegang heeft tot een DNS-server die de naam kan oplossen.)
* ``--nfs3-usage-model`` - Een van de gegevens caching-profielen, beschreven in [Een gebruiksmodel kiezen](#choose-a-usage-model)hierboven.

  Controleer de namen van de gebruiksmodellen met de opdracht [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage_model_list).

* ``--junction`` - De koppelingsparameter koppelt het client gerichte virtuele bestandspad aan een exportpad op het opslagsysteem.

  Een NFS-opslagdoel kan meerdere virtuele paden hebben, zolang elk pad een andere export of subdirectory in hetzelfde opslagsysteem vertegenwoordigt. Maak alle paden voor één opslagsysteem op één opslagdoel.

  U kunt [op elk moment naamruimtepaden aan](add-namespace-paths.md) een opslagdoel toevoegen en bewerken.

  De ``--junction`` parameter gebruikt deze waarden:

  * ``namespace-path`` - Het client gerichte virtuele bestandspad
  * ``nfs-export`` - De export van het opslagsysteem om te koppelen aan het client-gerichte pad
  * ``target-path`` (optioneel) - Een subdirectory van de export, indien nodig

  Voorbeeld: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Lees [Geaggregeerde naamruimte configureren](hpc-cache-namespace.md) voor meer informatie over de functie voor virtuele naamruimte.

Voorbeeldopdracht:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

Uitvoer:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="add-a-new-adls-nfs-storage-target-preview"></a>Een nieuw ADLS-NFS-opslagdoel toevoegen (PREVIEW)

ADLS-NFS-opslagdoelen gebruiken Azure Blob-containers die ondersteuning bieden voor het NFS 3.0-protocol (Network File System).

> [!NOTE]
> Ondersteuning voor het NFS 3.0-protocol voor Azure Blob Storage is in openbare preview. Beschikbaarheid is beperkt en functies kunnen veranderen tussen nu en wanneer de functie algemeen beschikbaar komt. Gebruik geen preview-technologie in productiesystemen.
>
> Lees [ondersteuning voor het NFS 3.0-protocol](../storage/blobs/network-file-system-protocol-support.md) voor de meest recente informatie.

ADLS-NFS-opslagdoelen hebben een aantal overeenkomsten met Blob Storage-doelen en andere met NFS-opslagdoelen. Bijvoorbeeld:

* Net als bij een Blob Storage-doel moet u een Azure HPC Cache geven voor [toegang tot uw opslagaccount.](#add-the-access-control-roles-to-your-account)
* Net als bij een NFS-opslagdoel moet u een cachegebruiksmodel [instellen.](#choose-a-usage-model)
* Omdat blobcontainers met NFS een hiërarchische structuur hebben die compatibel is met NFS, hoeft u de cache niet te gebruiken om gegevens op te nemen en zijn de containers leesbaar voor andere NFS-systemen. U kunt gegevens vooraf laden in een ADLS-NFS-container, deze vervolgens toevoegen aan een HPC Cache als opslagdoel en de gegevens later van buiten een HPC Cache. Wanneer u een standaard-blobcontainer als HPC Cache-opslagdoel gebruikt, worden de gegevens in een eigen indeling geschreven en zijn ze alleen toegankelijk vanuit andere Azure HPC Cache-compatibele producten.

Voordat u een ADLS-NFS-opslagdoel kunt maken, moet u een NFS-opslagaccount maken. Volg de tips in [Prerequisites for Azure HPC Cache](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview) and the instructions in [Mount Blob storage by using NFS (Blob-opslag met behulp van NFS mount Blob storage met behulp van NFS).](../storage/blobs/network-file-system-protocol-support-how-to.md) Nadat uw opslagaccount is ingesteld, kunt u een nieuwe container maken wanneer u het opslagdoel maakt.

Lees [Use NFS-mounted blob storage with Azure HPC Cache (NFS-aan NFS](nfs-blob-considerations.md) Azure HPC Cache voor meer informatie over deze configuratie).

Als u een ADLS-NFS-opslagdoel wilt maken, opent u **de** pagina Opslagdoel toevoegen in de Azure Portal. (Er zijn aanvullende methoden in ontwikkeling.)

![Schermopname van de pagina Opslagdoel toevoegen met adls-NFS-doel gedefinieerd](media/add-adls-target.png)

Voer deze gegevens in.

* **Naam van opslagdoel:** stel een naam in die dit opslagdoel in de Azure HPC Cache.
* **Doeltype:** kies **ADLS-NFS.**
* **Opslagaccount:** selecteer het account dat u wilt gebruiken. Als uw opslagaccount met NFS niet wordt weergegeven in de lijst, controleert u of het voldoet aan de vereisten en of de cache er toegang toe heeft.

  U moet het cache-exemplaar toestemming geven voor toegang tot het opslagaccount, zoals beschreven in [De toegangsrollen toevoegen.](#add-the-access-control-roles-to-your-account)

* **Opslagcontainer:** selecteer de blobcontainer met NFS-functie voor dit doel of klik **op Nieuwe maken.**

* **Gebruiksmodel:** kies een van de gegevens caching-profielen op basis van uw werkstroom, zoals beschreven in [Een gebruiksmodel](#choose-a-usage-model) kiezen hierboven.

Wanneer u klaar bent, klikt u **op OK** om het opslagdoel toe te voegen.

## <a name="view-storage-targets"></a>Opslagdoelen weergeven

U kunt de Azure Portal of de Azure CLI gebruiken om de opslagdoelen weer te geven die al zijn gedefinieerd voor uw cache.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Open vanuit Azure Portal cache-exemplaar en klik op **Opslagdoelen.** Dit staat onder de kop Instellingen op de linkerzijbalk. De pagina opslagdoelen bevat alle bestaande doelen en besturingselementen voor het toevoegen of verwijderen ervan.

Klik op de naam van een opslagdoel om de detailpagina te openen.

Lees [Opslagdoelen bewerken](hpc-cache-edit-storage.md) voor meer informatie.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure CLI instellen voor Azure HPC Cache.](./az-cli-prerequisites.md)

Gebruik de [lijstoptie az hpc-cache storage-target om](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) de bestaande opslagdoelen voor een cache weer te geven. Geeft de naam van de cache en de resourcegroep op (tenzij u deze globaal hebt ingesteld).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Gebruik [az hpc-cache storage-target show om](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) details over een bepaald opslagdoel te bekijken. (Geef het opslagdoel op naam op.)

Voorbeeld:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>Volgende stappen

Nadat u opslagdoelen hebt gemaakt, gaat u verder met deze taken om uw cache klaar te maken voor gebruik:

* [De geaggregeerde naamruimte instellen](add-namespace-paths.md)
* [De Azure HPC Cache koppelen](hpc-cache-mount.md)
* [Gegevens verplaatsen naar Azure Blob Storage](hpc-cache-ingest.md)

Als u instellingen moet bijwerken, kunt u [een opslagdoel bewerken.](hpc-cache-edit-storage.md)
