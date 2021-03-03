---
title: Azure Storage toevoegen (container)
description: Meer informatie over het koppelen van een aangepaste netwerk share in een container-app in Azure App Service. Bestanden delen tussen apps, statische inhoud extern beheren en lokaal toegang krijgen, enzovoort.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: c3e5ca30b7f0f00b6d647f5fdaeb772900996adc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720236"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>Toegang Azure Storage (preview) als een netwerk share van een container in App Service

::: zone pivot="container-windows"

In deze hand leiding wordt beschreven hoe u Azure Storage-bestanden als een netwerk share koppelt aan een Windows-container in App Service. Alleen [Azure files shares](../storage/files/storage-how-to-use-files-cli.md) en [Premium-bestands shares](../storage/files/storage-how-to-create-file-share.md) worden ondersteund. Voor delen zijn onder andere beveiligde inhoud, portabiliteit van inhoud, toegang tot meerdere apps en meerdere overdrachts methoden.

> [!NOTE]
>Azure Storage in App Service is **een preview-versie** en wordt **niet ondersteund** voor **productie scenario's**.

::: zone-end

::: zone pivot="container-linux"

In deze hand leiding wordt uitgelegd hoe u Azure Storage kunt koppelen aan een Linux-container App Service. Voor delen zijn onder andere beveiligde inhoud, draag baarheid van inhoud, permanente opslag, toegang tot meerdere apps en meerdere overdrachts methoden.

> [!NOTE]
>Azure Storage in App Service is **in Preview** voor app service in Linux en Web App for containers. Het wordt **niet ondersteund** voor **productie scenario's**.

::: zone-end

## <a name="prerequisites"></a>Vereisten

::: zone pivot="container-windows"

- [Een bestaande Windows-container-app in Azure App Service](quickstart-custom-container.md)
- [Een Azure-bestands share maken](../storage/files/storage-how-to-use-files-cli.md)
- [Bestanden uploaden naar Azure-bestands share](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- Een bestaande [app service in de Linux-app](index.yml).
- Een [Azure Storage-account](../storage/common/storage-account-create.md?tabs=azure-cli)
- Een [Azure-bestands share en-map](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> Azure Files is niet-standaard opslag en wordt afzonderlijk gefactureerd, niet opgenomen in de web-app. Het gebruik van de firewall configuratie wordt niet ondersteund vanwege beperkingen van de infra structuur.
>

## <a name="limitations"></a>Beperkingen

::: zone pivot="container-windows"

- Azure Storage in App Service wordt momenteel **niet ondersteund** voor het nemen van uw eigen code scenario's (niet-container Windows-apps).
- Azure Storage in App Service biedt **geen ondersteuning** voor het gebruik van de **opslag firewall** -configuratie vanwege beperkingen van de infra structuur.
- Met Azure Storage met App Service kunt u **Maxi maal vijf** koppel punten per app opgeven.
- Azure Storage die aan een app zijn gekoppeld, is niet toegankelijk via App Service FTP-en FTPs-eind punten. Gebruik [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

::: zone pivot="container-linux"

- Azure Storage in App Service ondersteunt het koppelen van **Azure files containers** (lezen/schrijven) en **Azure Blob-containers** (alleen-lezen)
- Met Azure Storage in App Service kunt u **Maxi maal vijf** koppel punten per app opgeven.
- Azure Storage die aan een app zijn gekoppeld, is niet toegankelijk via App Service FTP-en FTPs-eind punten. Gebruik [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

## <a name="link-storage-to-your-app"></a>Opslag koppelen aan uw app

::: zone pivot="container-windows"

Nadat u uw [Azure Storage-account, de bestands share en de map](#prerequisites)hebt gemaakt, kunt u uw app nu configureren met Azure Storage.

Als u een Azure Files share wilt koppelen aan een map in uw App Service-app, gebruikt u de [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) opdracht. Het opslag type moet Azure files zijn.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

U moet dit doen voor andere directory's die u wilt koppelen aan een Azure Files share.

::: zone-end

::: zone pivot="container-linux"

Nadat u uw [Azure Storage-account, de bestands share en de map](#prerequisites)hebt gemaakt, kunt u uw app nu configureren met Azure Storage.

Als u een opslag account wilt koppelen aan een map in uw App Service-app, gebruikt u de [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) opdracht. Het opslag type kan AzureBlob of Azure files zijn. Azure files wordt in dit voor beeld gebruikt. De instelling van het koppelingspad komt overeen met de map in de container die u wilt koppelen aan Azure Storage. Als u deze instelt op/, wordt de volledige container gekoppeld aan Azure Storage.


> [!CAUTION]
> De map die is opgegeven als koppel pad in uw web-app moet leeg zijn. Alle inhoud die in deze map is opgeslagen, wordt verwijderd wanneer een externe koppeling wordt toegevoegd. Als u bestanden migreert voor een bestaande app, maakt u een back-up van uw app en de bijbehorende inhoud voordat u begint.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

U moet dit doen voor alle andere directory's die u aan een opslag account wilt koppelen.

::: zone-end

## <a name="verify-linked-storage"></a>Gekoppelde opslag controleren

Zodra de share is gekoppeld aan de app, kunt u dit controleren door de volgende opdracht uit te voeren:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>Volgende stappen

::: zone pivot="container-windows"

- [Aangepaste software migreren naar Azure app service met behulp van een aangepaste container](tutorial-custom-container.md?pivots=container-windows).

::: zone-end

::: zone pivot="container-linux"

- [Configureer een aangepaste container](configure-custom-container.md?pivots=platform-linux).

::: zone-end