---
title: Een Azure Time Series Insights Gen2-omgeving maken met behulp van Azure CLI-Azure Time Series Insights Gen2 | Microsoft Docs
description: Meer informatie over het instellen van een omgeving in Azure Time Series Insights Gen2 met behulp van de Azure CLI.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: ed185413cff155610b2b088b1791169e33f6ce7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103464432"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Een Azure Time Series Insights Gen2-omgeving maken met behulp van de Azure CLI

In dit document vindt u instructies voor het maken van een nieuwe Time Series Insights Gen2-omgeving.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

* Een Azure Storage-account te maken als de [koude opslag](./concepts-storage.md#cold-store) van uw omgeving. Dit account is ontworpen voor langetermijnretentie en voor het analyseren van historische gegevens.

> [!NOTE]
> Vervang in uw code `mytsicoldstore` door een unieke naam voor het koude-opslagaccount.

Maak eerst het opslag account:

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>De omgeving maken

Nu het opslag account is gemaakt en de naam en beheer sleutel zijn toegewezen aan de variabelen, voert u de onderstaande opdracht uit om de Azure Time Series Insights omgeving te maken:

> [!NOTE]
> Vervang in uw code het volgende door unieke namen voor uw scenario:
>
> * `my-tsi-env` met de naam van uw omgeving.
> * `my-ts-id-prop` met de naam van de time series id-eigenschap.

> [!IMPORTANT]
> De tijd reeks-ID van uw omgeving is als een database partitie sleutel. De time series-ID fungeert ook als primaire sleutel voor uw time series-model.
>
> Zie [Aanbevolen procedures voor het kiezen van een time series-id](./how-to-select-tsid.md) voor meer informatie.

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Een Azure Time Series Insights omgeving verwijderen

U kunt de Azure CLI gebruiken om een afzonderlijke resource te verwijderen, zoals een Time Series Insights omgeving, of om een resource groep en alle bijbehorende resources te verwijderen, inclusief eventuele Time Series Insights omgevingen.

Als u [een time series Insights omgevingen wilt verwijderen](/cli/azure/ext/timeseriesinsights/tsi/environment?view=azure-cli-latest#ext_timeseriesinsights_az_tsi_environment_delete), voert u de volgende opdracht uit:

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

Als u [het opslag account wilt verwijderen](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_delete), voert u de volgende opdracht uit:

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

Als u [een resource groep](/cli/azure/group#az-group-delete) en alle bijbehorende resources wilt verwijderen, voert u de volgende opdracht uit:

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [streaming-opname gebeurtenis bronnen](./concepts-streaming-ingestion-event-sources.md) voor uw Azure time series Insights Gen2-omgeving.
* Meer informatie over verbinding maken met een [IOT hub](./how-to-ingest-data-iot-hub.md)
