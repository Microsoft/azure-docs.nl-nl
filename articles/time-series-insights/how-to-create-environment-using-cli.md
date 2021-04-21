---
title: Een Azure Time Series Insights Gen2-omgeving maken met behulp van de Azure CLI - Azure Time Series Insights Gen2-| Microsoft Docs
description: Meer informatie over het instellen van een omgeving in Azure Time Series Insights Gen2 met behulp van de Azure CLI.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e2846b7ba07ec0a7678a8287fe6a84bc169497a3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785123"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Een Azure Time Series Insights Gen2-omgeving maken met behulp van de Azure CLI

Dit document begeleidt u bij het maken van een nieuwe Time Series Insights Gen2-omgeving.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Vereisten

* Een Azure Storage-account te maken als de [koude opslag](./concepts-storage.md#cold-store) van uw omgeving. Dit account is ontworpen voor langetermijnretentie en voor het analyseren van historische gegevens.

> [!NOTE]
> Vervang in uw code `mytsicoldstore` door een unieke naam voor het koude-opslagaccount.

Maak eerst het opslagaccount:

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>De omgeving maken

Nu het opslagaccount is gemaakt en de naam en beheersleutel zijn toegewezen aan de variabelen, kunt u de onderstaande opdracht uitvoeren om de Azure Time Series Insights maken:

> [!NOTE]
> Vervang in uw code het volgende door unieke namen voor uw scenario:
>
> * `my-tsi-env` door de naam van uw omgeving.
> * `my-ts-id-prop` door de naam van uw tijdreeks-id-eigenschap.

> [!IMPORTANT]
> De time series-id van uw omgeving is net als een databasepartitiesleutel. De Time Series-id fungeert ook als de primaire sleutel voor uw Time Series-model.
>
> Zie Best practices for [choosing a Time Series ID (Best practices voor het kiezen van een tijdreeks-id) voor meer informatie.](./how-to-select-tsid.md)

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Een Azure Time Series Insights verwijderen

U kunt de Azure CLI gebruiken om een afzonderlijke resource, zoals een Time Series Insights Environment, te verwijderen of een resourcegroep en alle resources ervan te verwijderen, inclusief alle Time Series Insights Environments.

Als [u een omgeving Time Series Insights verwijderen,](/cli/azure/ext/timeseriesinsights/tsi/environment#ext_timeseriesinsights_az_tsi_environment_delete)moet u de volgende opdracht uitvoeren:

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

Voer [de volgende opdracht uit om het](/cli/azure/storage/account#az_storage_account_delete)opslagaccount te verwijderen:

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

Voer de volgende opdracht uit om een [resourcegroep](/cli/azure/group#az_group_delete) en alle resources ervan te verwijderen:

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over gebeurtenisbronnen voor streaming-opname](./concepts-streaming-ingestion-event-sources.md) voor uw Azure Time Series Insights Gen2-omgeving.
* Meer informatie over verbinding maken met [een IoT Hub](./how-to-ingest-data-iot-hub.md)
