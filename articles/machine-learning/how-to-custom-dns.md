---
title: Aangepaste DNS-server gebruiken
titleSuffix: Azure Machine Learning
description: Een aangepaste DNS-server configureren voor gebruik met een Azure Machine Learning-werk ruimte en een persoonlijk eind punt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 66a709f15191a8142f10f15d825276ea2ba4b83f
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487981"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Werkruimte gebruiken met een aangepaste DNS-server

Wanneer u een Azure Machine Learning-werk ruimte met een persoonlijk eind punt gebruikt, zijn er [verschillende manieren om DNS-naam omzetting te verwerken](../private-link/private-endpoint-dns.md). Standaard verwerkt Azure automatisch naam omzetting voor uw werk ruimte en persoonlijk eind punt. Als u in plaats daarvan _uw eigen aangepaste DNS-server gebruikt_, moet u hand matig DNS-vermeldingen maken of voorwaardelijke doorstuur servers gebruiken voor de werk ruimte.

> [!IMPORTANT]
> In dit artikel wordt alleen beschreven hoe u de Fully Qualified Domain Name (FQDN) en IP-adressen voor deze vermeldingen kunt vinden. Deze bevat geen informatie over het configureren van de DNS-records voor deze items. Raadpleeg de documentatie bij uw DNS-software voor informatie over het toevoegen van records.

## <a name="prerequisites"></a>Vereisten

- Een Azure-Virtual Network die gebruikmaakt van [uw eigen DNS-server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

- Een Azure Machine Learning-werk ruimte met een persoonlijk eind punt. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.

- Vertrouwd met het gebruik van [netwerk isolatie tijdens de training & deinterferentie](./how-to-network-security-overview.md).

- Vertrouwd met de [configuratie van de DNS-zone voor het persoonlijke eind punt van Azure](../private-link/private-endpoint-dns.md)

- Optioneel, [Azure cli](/cli/azure/install-azure-cli) of [Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="fqdns-in-use"></a>FQDN-in gebruik
### <a name="these-fqdns-are-in-use-in-the-following-regions-eastus-southcentralus-and-westus2"></a>Deze FQDN-vormen worden gebruikt in de volgende regio's: Oost-, southcentralus-en westus2.
De volgende lijst bevat de volledig gekwalificeerde domein namen (FQDN) die worden gebruikt door uw werk ruimte:

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* Als u een reken instantie maakt, moet u ook een vermelding voor toevoegen `<instance-name>.<region>.instances.azureml.ms` met het privé-IP-eind punt van de werk ruimte.

    > [!NOTE]
    > Reken instanties kunnen alleen worden geopend vanuit het virtuele netwerk.
    
### <a name="these-fqdns-are-in-use-in-all-other-public-regions"></a>Deze FQDN-zijn in alle andere open bare regio's in gebruik
De volgende lijst bevat de volledig gekwalificeerde domein namen (FQDN) die worden gebruikt door uw werk ruimte:

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* `<instance-name>.<region>.instances.azureml.ms`

    > [!NOTE]
    > Reken instanties kunnen alleen worden geopend vanuit het virtuele netwerk.

### <a name="azure-china-21vianet-regions"></a>Azure China 21Vianet-regio's

De volgende FQDN-gebieden zijn voor Azure China 21Vianet-regio's:

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.cn`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.cn`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.chinacloudapi.cn`

    > [!NOTE]
    > De naam van de werk ruimte voor deze FQDN kan worden afgekapt. De afkap ping wordt uitgevoerd om de FQDN-waarde kleiner dan of gelijk aan 63 tekens te hebben.
* `<instance-name>.<region>.instances.ml.azure.cn`
## <a name="find-the-ip-addresses"></a>De IP-adressen zoeken

Gebruik een van de volgende methoden om de interne IP-adressen voor de FQDN-namen in het VNet te vinden:

> [!NOTE]
> De volledig gekwalificeerde domein namen en IP-adressen verschillen op basis van uw configuratie. De GUID-waarde in de domein naam is bijvoorbeeld specifiek voor uw werk ruimte.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure-portal](#tab/azure-portal)

1. Selecteer uw Azure Machine Learning- __werk ruimte__ In de [Azure Portal](https://portal.azure.com).
1. Selecteer in de sectie __instellingen__ de optie verbindingen met een __privé-eind punt__.
1. Selecteer de koppeling in de kolom met het __persoonlijke eind punt__ die wordt weer gegeven.
1. Onder aan de pagina vindt u een lijst met de FQDN-namen (Fully Qualified Domain names) en IP-adressen voor het persoonlijke eind punt van de werk ruimte.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Lijst met FQDN-namen in de portal":::

---

De informatie die door alle methoden wordt geretourneerd, is hetzelfde. een lijst met de FQDN en het persoonlijke IP-adres voor de resources. Het volgende voor beeld is afkomstig uit een wereld wijde Azure-regio:

| FQDN | IP-adres |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Sommige FQDN-vormen worden niet weer gegeven in de lijst die wordt weer gegeven door het persoonlijke eind punt, maar zijn vereist voor de werk ruimte in Oost-southcentralus en westus2. Deze FQDN-namen worden weer gegeven in de volgende tabel en moeten ook worden toegevoegd aan uw DNS-server en/of een Azure Privé-DNS-zone:
>
> * `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Als u een reken instantie hebt, gebruikt u `<instance-name>.<region>.instances.azureml.ms` , waarbij `<instance-name>` de naam van uw reken instantie is. Gebruik een privé-IP-adres van het persoonlijke eind punt van de werk ruimte. Houd er rekening mee dat Compute instance alleen kan worden geopend vanuit het virtuele netwerk.
>
> Voor al deze IP-adressen gebruikt u hetzelfde adres als de `*.api.azureml.ms` vermeldingen die in de vorige stappen zijn geretourneerd.

In de volgende tabel ziet u voor beelden van IP-adressen uit Azure China 21Vianet-regio's:

| FQDN | IP-adres |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.cn` | `10.1.0.5` |
| `ml-mype-pltest-chinaeast2-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.chinacloudapi.cn` | `10.1.0.6` |
## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van Virtual Network](how-to-network-security-overview.md)voor meer informatie over het gebruik van Azure machine learning met een virtueel netwerk.

Zie voor meer informatie over het integreren van persoonlijke eind punten in uw DNS-configuratie [Azure private endpoint DNS-configuratie](../private-link/private-endpoint-dns.md).