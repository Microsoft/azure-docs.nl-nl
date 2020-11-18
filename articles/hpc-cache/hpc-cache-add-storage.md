---
title: Opslag toevoegen aan een Azure HPC-cache
description: Hoe u opslag doelen definieert zodat uw Azure HPC-cache uw on-premises NFS-systeem of Azure Blob-containers voor lange termijn bestands opslag kan gebruiken
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: b2497a49703ab675bde50c7845995c92de32f376
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657173"
---
# <a name="add-storage-targets"></a>Opslagdoelen toevoegen

*Opslag doelen* zijn back-end opslag voor bestanden die worden geopend via een Azure HPC-cache. U kunt NFS-opslag (zoals een on-premises hardware-systeem) toevoegen of gegevens opslaan in Azure Blob.

U kunt Maxi maal tien verschillende opslag doelen definiëren voor één cache. De cache geeft alle opslag doelen weer in één geaggregeerde naam ruimte.

De naam ruimte paden worden afzonderlijk geconfigureerd nadat u de opslag doelen hebt toegevoegd. In het algemeen kan een NFS-opslag doel Maxi maal tien naam ruimte paden hebben, of meer voor sommige grote configuraties. Lees [NFS-naam ruimte paden](add-namespace-paths.md#nfs-namespace-paths) voor meer informatie.

Houd er rekening mee dat de export van de opslag toegankelijk moet zijn vanuit het virtuele netwerk van uw cache. Voor on-premises hardwarematige opslag moet u mogelijk een DNS-server instellen die hostnamen voor NFS-opslag toegang kan omzetten. Meer informatie vindt u in [DNS-toegang](hpc-cache-prerequisites.md#dns-access).

Voeg opslag doelen toe nadat u de cache hebt gemaakt. Volg deze procedure:

1. [De cache maken](hpc-cache-create.md)
1. Een opslag doel definiëren (informatie in dit artikel)
1. [De client gerichte paden maken](add-namespace-paths.md) (voor de [geaggregeerde naam ruimte](hpc-cache-namespace.md))

De procedure voor het toevoegen van een opslag doel verschilt enigszins, afhankelijk van of u Azure Blob-opslag of een NFS-export toevoegt. Hieronder vindt u meer informatie.

Klik op de onderstaande afbeelding om een [video demonstratie](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) te bekijken van het maken van een cache en het toevoegen van een opslag doel van de Azure Portal.

[![Video miniatuur: Azure HPC cache: Setup (Klik om de video pagina te bezoeken)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>Een nieuw Azure Blob-opslag doel toevoegen

Een nieuw Blob-opslag doel vereist een lege BLOB-container of een container die is gevuld met gegevens in de bestandssysteem indeling van de Azure HPC-cache. Lees meer over het vooraf laden van een BLOB-container in [gegevens verplaatsen naar Azure Blob-opslag](hpc-cache-ingest.md).

De pagina **opslag doel Azure portal toevoegen** bevat de optie om een nieuwe BLOB-container te maken voordat u deze toevoegt.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Open in de Azure Portal uw cache-exemplaar en klik op **opslag doelen** op de zijbalk aan de linkerkant.

![scherm afbeelding van de pagina instellingen > opslag doel, met twee bestaande opslag doelen in een tabel en een hooglicht rond de knop + opslag doel toevoegen boven aan de tabel](media/add-storage-target-button.png)

De pagina **opslag doelen** bevat een lijst met alle bestaande doelen en bevat een koppeling om een nieuwe toe te voegen.

Klik op de knop **opslag doel toevoegen** .

![scherm afbeelding van de pagina opslag doel toevoegen, gevuld met informatie voor een nieuw Azure Blob-opslag doel](media/hpc-cache-add-blob.png)

Als u een Azure Blob-container wilt definiëren, voert u deze informatie in.

* **Naam van opslag doel** : Stel een naam in die dit opslag doel identificeert in de Azure HPC-cache.
* **Doel type** : Kies **BLOB**.
* **Opslag account** : Selecteer het account dat u wilt gebruiken.

  U moet het cache-exemplaar toestemming geven om toegang te krijgen tot het opslag account zoals beschreven in [de toegangs functies toevoegen](#add-the-access-control-roles-to-your-account).

  Lees [vereisten voor Blob Storage](hpc-cache-prerequisites.md#blob-storage-requirements)voor meer informatie over het type opslag account dat u kunt gebruiken.

* **Opslag container** : Selecteer de BLOB-container voor dit doel of klik op **nieuwe maken**.

  ![scherm afbeelding van het dialoog venster voor het opgeven van naam en toegangs niveau (privé) voor een nieuwe container](media/add-blob-new-container.png)

Wanneer u klaar bent, klikt u op **OK** om het opslag doel toe te voegen.

> [!NOTE]
> Als de firewall van uw opslag account is ingesteld op het beperken van de toegang tot alleen geselecteerde netwerken, gebruikt u de tijdelijke oplossing die is beschreven in de firewall-instellingen voor het [werk account voor Blob Storage](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>De toegangs beheer rollen toevoegen aan uw account

Azure HPC cache maakt gebruik van [Azure RBAC (op rollen gebaseerd toegangs beheer)](../role-based-access-control/index.yml) voor het machtigen van de cache service voor toegang tot uw opslag account voor Azure Blob Storage-doelen.

De eigenaar van het opslag account moet expliciet de Inzender rollen voor het [opslag account](../role-based-access-control/built-in-roles.md#storage-account-contributor) en de [blobgegevens](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) voor de gebruiker ' HPC-cache resource provider ' toevoegen.

U kunt dit vooraf doen, of door te klikken op een koppeling op de pagina waar u een Blob-opslag doel toevoegt. Houd er rekening mee dat het Maxi maal vijf minuten kan duren voordat de rolinstellingen zijn door gegeven via de Azure-omgeving. u moet dus een paar minuten wachten nadat u de rollen hebt toegevoegd voordat u een opslag doel maakt.

Stappen voor het toevoegen van de Azure-rollen:

1. Open de pagina **toegangs beheer (IAM)** voor het opslag account. (De koppeling op de pagina **opslag doel toevoegen** opent deze pagina automatisch voor het geselecteerde account.)

1. Klik **+** boven aan de pagina en kies **een roltoewijzing toevoegen**.

1. Selecteer de rol ' Inzender voor opslag accounts ' in de lijst.

1. In het veld **toegang toewijzen aan kunt u** de geselecteerde standaard waarde (' Azure AD-gebruiker,-groep of Service-Principal ') laten staan.  

1. In het veld **selecteren zoekt u** naar ' HPC '.  Deze teken reeks moet overeenkomen met een service-principal met de naam ' HPC-cache resource provider '. Klik op die principal om deze te selecteren.

   > [!NOTE]
   > Als een zoek opdracht voor "HPC" niet werkt, kunt u de teken reeks "storagecache" gebruiken. Gebruikers die hebben deel genomen aan de previews (vóór GA), moeten mogelijk de oudere naam voor de Service-Principal gebruiken.

1. Klik onderaan op de knop **Opslaan** .

1. Herhaal dit proces om de rol ' Storage BLOB data contributor ' toe te wijzen.  

![scherm opname van de gebruikers interface voor roltoewijzing toevoegen](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>Vereiste: toegang tot het opslag account

[Azure cli instellen voor Azure HPC-cache](./az-cli-prerequisites.md).

Voordat u een Blob-opslag doel toevoegt, controleert u of de cache de juiste rollen heeft voor toegang tot het opslag account en of de firewall instellingen het maken van het opslag doel toestaan.

Azure HPC cache maakt gebruik van [Azure RBAC (op rollen gebaseerd toegangs beheer)](../role-based-access-control/index.yml) voor het machtigen van de cache service voor toegang tot uw opslag account voor Azure Blob Storage-doelen.

De eigenaar van het opslag account moet expliciet de Inzender rollen voor het [opslag account](../role-based-access-control/built-in-roles.md#storage-account-contributor) en de [blobgegevens](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) voor de gebruiker ' HPC-cache resource provider ' toevoegen.

Het maken van een opslag doel mislukt als de cache deze rollen niet bevat.

Het kan tot vijf minuten duren voordat de rolinstellingen zijn door gegeven via de Azure-omgeving. u moet dus een paar minuten wachten nadat u de rollen hebt toegevoegd voordat u een opslag doel maakt.

Meer informatie over het [toevoegen of verwijderen van Azure-roltoewijzingen met Azure cli](../role-based-access-control/role-assignments-cli.md) voor gedetailleerde instructies.

Controleer ook de firewall instellingen van uw opslag account. Als de firewall is ingesteld om de toegang te beperken tot alleen geselecteerde netwerken, kan het maken van de opslag doel mislukken. Gebruik de tijdelijke oplossing die wordt beschreven in de firewall-instellingen van het [werk account voor Blob Storage](hpc-cache-blob-firewall-fix.md).

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Een Blob Storage-doel toevoegen met Azure CLI

Gebruik de interface [AZ HPC-cache Blob-Storage-target](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-add) om een Azure Blob Storage-doel te definiëren.

> [!NOTE]
> Voor de Azure CLI-opdrachten moet u op dit moment een pad naar een naam ruimte maken wanneer u een opslag doel toevoegt. Dit wijkt af van het proces dat wordt gebruikt in combi natie met de Azure Portal-interface.

Naast de para meters voor de standaard resource groep en de cache naam moet u deze opties voor het opslag doel opgeven:

* ``--name`` -Stel een naam in die dit opslag doel identificeert in de Azure HPC-cache.

* ``--storage-account`` -De account-id, in dit formulier:/Subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/providers/Microsoft.Storage/storageAccounts/*<account_name>*

  Lees [vereisten voor Blob Storage](hpc-cache-prerequisites.md#blob-storage-requirements)voor meer informatie over het type opslag account dat u kunt gebruiken.

* ``--container-name`` -Geef de naam op van de container die moet worden gebruikt voor dit opslag doel.

* ``--virtual-namespace-path`` -Het pad naar de client voor dit opslag doel instellen. Plaats paden tussen aanhalings tekens. Raadpleeg [de geaggregeerde naam ruimte plannen](hpc-cache-namespace.md) voor meer informatie over de functie virtuele naam ruimte.

Voor beeld opdracht:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Een nieuw NFS-opslag doel toevoegen

Een NFS-opslag doel heeft verschillende instellingen van een Blob Storage-doel. Met de instelling gebruiks model kunt u de cache gebruiken om efficiënt gegevens uit dit opslag systeem op te slaan.

![Scherm afbeelding van de pagina opslag doel toevoegen met NFS-doel gedefinieerd](media/add-nfs-target.png)

> [!NOTE]
> Voordat u een NFS-opslag doel maakt, moet u controleren of uw opslag systeem toegankelijk is vanuit de Azure HPC-cache en voldoet aan de machtigings vereisten. Het maken van een opslag doel mislukt als de cache geen toegang kan krijgen tot het opslag systeem. Lees de [NFS-opslag vereisten](hpc-cache-prerequisites.md#nfs-storage-requirements) en [los problemen met de NAS-configuratie en NFS-opslag doel](troubleshoot-nas.md) op voor meer informatie.

### <a name="choose-a-usage-model"></a>Kies een gebruiks model
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Wanneer u een opslag doel maakt dat verwijst naar een NFS-opslag systeem, moet u het gebruiks model voor dat doel kiezen. Dit model bepaalt hoe de gegevens in de cache worden opgeslagen.

Er zijn drie opties:

* **Lees zware, incidentele schrijf bewerkingen** : gebruik deze optie als u lees toegang tot bestanden wilt versnellen die statisch zijn of zelden worden gewijzigd.

  Met deze optie worden bestanden die door clients worden gelezen, in de cache opgeslagen, maar worden direct naar de back-end-opslag door gegeven. Bestanden die in de cache zijn opgeslagen, worden nooit vergeleken met de bestanden op het NFS-opslag volume.

  Gebruik deze optie niet als er een risico bestaat dat een bestand rechtstreeks op het opslag systeem kan worden gewijzigd zonder het eerst naar de cache te schrijven. Als dat gebeurt, wordt de versie van het bestand in de cache nooit bijgewerkt met de wijzigingen van de back-end en kan de gegevensset inconsistent worden.

* **Meer dan 15% schrijf bewerkingen** : deze optie versnelt de lees-en schrijf prestaties. Wanneer u deze optie gebruikt, moeten alle clients toegang hebben tot bestanden via de Azure HPC-cache in plaats van de back-end-opslag rechtstreeks te koppelen. De bestanden in de cache hebben recente wijzigingen die niet worden opgeslagen op de back-end.

  In dit gebruiks model worden bestanden in de cache niet gecontroleerd op basis van de bestanden in de back-end-opslag. Er wordt ervan uitgegaan dat de cache versie van het bestand meer actueel is. Een gewijzigd bestand in de cache wordt naar het back-end-opslag systeem geschreven nadat het een uur in de cache heeft bevinden zonder extra wijzigingen.

* **Clients schrijven naar het NFS-doel, waarbij de cache wordt omzeild** : Kies deze optie als clients in uw werk stroom gegevens rechtstreeks naar het opslag systeem schrijven zonder eerst naar de cache te schrijven. Bestanden die door clients worden aangevraagd, worden in de cache opgeslagen, maar eventuele wijzigingen aan deze bestanden van de client worden onmiddellijk door gegeven aan het back-end-opslag systeem.

  Met dit gebruiks model worden de bestanden in de cache vaak gecontroleerd op basis van de back-end-versies voor updates. Met deze verificatie kunnen bestanden buiten de cache worden gewijzigd terwijl de consistentie van gegevens wordt behouden.

Deze tabel bevat een overzicht van de verschillen in het gebruiks model:

| Gebruiks model                   | Cache modus | Back-end-verificatie | Maximale vertraging voor terugschrijven |
|-------------------------------|--------------|-----------------------|--------------------------|
| Zware, incidentele schrijf bewerkingen lezen | Lezen         | Nooit                 | Geen                     |
| Meer dan 15% schrijf bewerkingen       | Lezen/schrijven   | Nooit                 | 1 uur                   |
| Clients slaan de cache over      | Lezen         | 30 seconden            | Geen                     |

### <a name="create-an-nfs-storage-target"></a>Een NFS-opslag doel maken

### <a name="portal"></a>[Portal](#tab/azure-portal)

Open in de Azure Portal uw cache-exemplaar en klik op **opslag doelen** op de zijbalk aan de linkerkant.

![scherm afbeelding van de pagina instellingen > opslag doel, met twee bestaande opslag doelen in een tabel en een hooglicht rond de knop + opslag doel toevoegen boven aan de tabel](media/add-storage-target-button.png)

De pagina **opslag doelen** bevat een lijst met alle bestaande doelen en bevat een koppeling om een nieuwe toe te voegen.

Klik op de knop **opslag doel toevoegen** .

![Scherm afbeelding van de pagina opslag doel toevoegen met NFS-doel gedefinieerd](media/add-nfs-target.png)

Geef deze informatie op voor een opslag doel met NFS-back-ups:

* **Naam van opslag doel** : Stel een naam in die dit opslag doel identificeert in de Azure HPC-cache.

* **Doel type** : Kies voor **NFS**.

* **Hostnaam** : Voer het IP-adres of de Fully Qualified Domain name voor uw NFS-opslag systeem in. (Gebruik alleen een domein naam als uw cache toegang heeft tot een DNS-server die de naam kan omzetten.)

* **Gebruiks model** : Kies een van de profielen voor het opslaan van gegevens op basis van uw werk stroom, zoals wordt beschreven in [een gebruiks model selecteren](#choose-a-usage-model) hierboven.

Wanneer u klaar bent, klikt u op **OK** om het opslag doel toe te voegen.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure cli instellen voor Azure HPC-cache](./az-cli-prerequisites.md).

Gebruik de Azure CLI [-opdracht AZ HPC-cache NFS-Storage-doel add](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-add) om het opslag doel te maken.

> [!NOTE]
> Voor de Azure CLI-opdrachten moet u op dit moment een pad naar een naam ruimte maken wanneer u een opslag doel toevoegt. Dit wijkt af van het proces dat wordt gebruikt in combi natie met de Azure Portal-interface.

Geef naast de cache naam en cache resource groep deze waarden op:

* ``--name`` -Stel een naam in die dit opslag doel identificeert in de Azure HPC-cache.
* ``--nfs3-target`` -Het IP-adres van uw NFS-opslag systeem. (U kunt hier een Fully Qualified Domain Name gebruiken als uw cache toegang heeft tot een DNS-server die de naam kan omzetten.)
* ``--nfs3-usage-model`` -Een van de profielen voor het opslaan van gegevens, beschreven in [een gebruiks model kiezen](#choose-a-usage-model)hierboven.

  Controleer de namen van de gebruiks modellen met de opdracht [AZ HPC-cache Usage-model List](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

* ``--junction`` -De para meter Junction koppelt het pad naar het client gerichte virtuele bestand met een exportpad op het opslag systeem.

  Een NFS-opslag doel kan meerdere virtuele paden hebben, zolang elk pad een andere export of submap op hetzelfde opslag systeem vertegenwoordigt. Maak alle paden voor één opslag systeem op één opslag doel.

  U kunt op elk gewenst moment naam ruimte paden op een opslag doel [toevoegen en bewerken](add-namespace-paths.md) .

  De ``--junction`` para meter gebruikt deze waarden:

  * ``namespace-path`` -Het pad naar het client gerichte virtuele bestand
  * ``nfs-export`` -Het opslag systeem exporteren om te koppelen aan het client gerichte pad
  * ``target-path`` (optioneel): een submap van de export, indien nodig

  Voorbeeld: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Lees de [geaggregeerde naam ruimte configureren](hpc-cache-namespace.md) voor meer informatie over de functie virtuele naam ruimte.

Voor beeld opdracht:

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

## <a name="view-storage-targets"></a>Opslag doelen weer geven

U kunt de Azure Portal of de Azure CLI gebruiken om de opslag doelen weer te geven die al zijn gedefinieerd voor uw cache.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Open in de Azure Portal uw cache-exemplaar en klik op **opslag doelen**. Dit bevindt zich onder de kop instellingen op de zijbalk aan de linkerkant. Op de pagina opslag doelen vindt u een lijst met alle bestaande doelen en besturings elementen voor het toevoegen of verwijderen ervan.

Klik op de naam van een opslag doel om de detail pagina ervan te openen.

Lees [opslag doelen bewerken](hpc-cache-edit-storage.md) voor meer informatie.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[Azure cli instellen voor Azure HPC-cache](./az-cli-prerequisites.md).

Gebruik de optie [AZ HPC-cache Storage doel List](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) om de bestaande opslag doelen voor een cache weer te geven. Geef de naam van de cache en de resource groep op (tenzij u deze wereld wijd hebt ingesteld).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Gebruik [AZ HPC-cache Storage-doel show](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) om de details van een bepaald opslag doel te bekijken. (Geef het opslag doel op naam op.)

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

Nadat u opslag doelen hebt gemaakt, gaat u door met deze taken om uw cache gereed te maken voor gebruik:

* [De geaggregeerde naam ruimte instellen](add-namespace-paths.md)
* [De Azure HPC Cache koppelen](hpc-cache-mount.md)
* [Gegevens verplaatsen naar Azure Blob-opslag](hpc-cache-ingest.md)

Als u instellingen moet bijwerken, kunt u [een opslag doel bewerken](hpc-cache-edit-storage.md).