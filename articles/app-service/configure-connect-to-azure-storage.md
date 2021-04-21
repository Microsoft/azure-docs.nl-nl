---
title: Een Azure Storage (container) toevoegen
description: Meer informatie over het koppelen van een aangepaste netwerk share in een container-app in Azure App Service. Bestanden delen tussen apps, statische inhoud op afstand beheren en lokaal openen, enzovoort.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: f844b5b413cda2cb16f9701970857be65fe6d91d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777613"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>Toegang tot Azure Storage (preview-versie) als een netwerkshare vanuit een container in App Service

::: zone pivot="container-windows"

Deze handleiding laat zien hoe u Azure Storage-bestanden als een netwerk share kunt koppelen aan een Windows-container in App Service. Alleen [Azure Files shares en](../storage/files/storage-how-to-use-files-cli.md) [Premium-bestands shares](../storage/files/storage-how-to-create-file-share.md) worden ondersteund. Voordelen zijn onder andere beveiligde inhoud, draagbaarheid van inhoud, toegang tot meerdere apps en meerdere overdrachtsmethoden.

> [!NOTE]
>Azure Storage in App Service is in **preview** en **wordt niet ondersteund voor** **productiescenario's.**

::: zone-end

::: zone pivot="container-linux"

Deze handleiding laat zien hoe u een Azure Storage aan een Linux-container App Service. Voordelen zijn onder andere beveiligde inhoud, draagbaarheid van inhoud, permanente opslag, toegang tot meerdere apps en meerdere overdrachtsmethoden.

> [!NOTE]
>Azure Storage in App Service is **in preview** voor App Service op Linux en Web App for Containers. Dit wordt niet ondersteund voor **productiescenario's.** 

::: zone-end

## <a name="prerequisites"></a>Vereisten

::: zone pivot="container-windows"

- [Een bestaande Windows Container-app in Azure App Service](quickstart-custom-container.md)
- [Een Azure-bestands share maken](../storage/files/storage-how-to-use-files-cli.md)
- [Bestanden uploaden naar Een Azure-bestands share](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- Een bestaande [App Service op Linux app](index.yml).
- Een [Azure Storage account](../storage/common/storage-account-create.md?tabs=azure-cli)
- Een [Azure-bestands share en map](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> Azure Files niet-standaardopslag is en afzonderlijk wordt gefactureerd, niet inbegrepen bij de web-app. Het biedt geen ondersteuning voor het gebruik van firewallconfiguratie vanwege infrastructuurbeperkingen.
>

## <a name="limitations"></a>Beperkingen

::: zone pivot="container-windows"

- Azure Storage in App Service wordt momenteel **niet ondersteund** voor Bring Your Own Code-scenario's (windows-apps zonder container).
- Azure Storage in App Service biedt **geen ondersteuning voor** het gebruik van de **Storage Firewall-configuratie** vanwege infrastructuurbeperkingen.
- Azure Storage met App Service kunt u maximaal vijf **bevestigingspunten** per app opgeven.
- Azure Storage aan een app zijn bevestigd, is niet toegankelijk via App Service FTP-/FTP-eindpunten. Gebruik [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

::: zone pivot="container-linux"

- Azure Storage in App Service ondersteuning voor het Azure Files **van containers** (lezen/schrijven) en **Azure Blob-containers** (alleen-lezen)
- Azure Storage in App Service kunt u maximaal **vijf bevestigingspunten** per app opgeven.
- Azure Storage aan een app zijn bevestigd, is niet toegankelijk via App Service FTP-/FTP-eindpunten. Gebruik [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

## <a name="link-storage-to-your-app"></a>Opslag koppelen aan uw app

::: zone pivot="container-windows"

Zodra u uw [Azure Storage account,](#prerequisites)bestands share en map hebt gemaakt, kunt u uw app nu configureren met Azure Storage.

Als u een Azure Files Share wilt toevoegen aan een map in App Service app, gebruikt u de [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) opdracht . Het opslagtype moet AzureFiles zijn.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

U moet dit doen voor alle andere directories die u wilt koppelen aan een Azure Files share.

::: zone-end

::: zone pivot="container-linux"

Zodra u uw [Azure Storage account,](#prerequisites)bestands share en map hebt gemaakt, kunt u uw app nu configureren met Azure Storage.

Als u een opslagaccount wilt toevoegen aan een map in App Service app, gebruikt u de [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) opdracht . Het opslagtype kan AzureBlob of AzureFiles zijn. AzureFiles wordt in dit voorbeeld gebruikt. De instelling voor het pad voor het bevestigingspad komt overeen met de map in de container die u wilt Azure Storage. Als u deze instelt op /, wordt de hele container aan de Azure Storage.


> [!CAUTION]
> De map die is opgegeven als het pad voor het maken van een web-app, moet leeg zijn. Alle inhoud die in deze map is opgeslagen, wordt verwijderd wanneer een externe mount wordt toegevoegd. Als u bestanden migreert voor een bestaande app, maakt u een back-up van uw app en de inhoud ervan voordat u begint.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

U moet dit doen voor alle andere directories die u wilt koppelen aan een opslagaccount.

::: zone-end

## <a name="verify-linked-storage"></a>Gekoppelde opslag verifiëren

Zodra de share aan de app is gekoppeld, kunt u dit controleren door de volgende opdracht uit te voeren:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>Volgende stappen

::: zone pivot="container-windows"

- [Migreert aangepaste software naar Azure App Service met behulp van een aangepaste container](tutorial-custom-container.md?pivots=container-windows).

::: zone-end

::: zone pivot="container-linux"

- [Configureer een aangepaste container](configure-custom-container.md?pivots=platform-linux).

::: zone-end