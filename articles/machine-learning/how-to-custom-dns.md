---
title: Aangepaste DNS-server gebruiken
titleSuffix: Azure Machine Learning
description: Een aangepaste DNS-server configureren voor gebruik met een Azure Machine Learning werkruimte en privé-eindpunt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 04/01/2021
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.openlocfilehash: 2a55fedd0fe059e7bff8203924389956dce36f3b
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107889804"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Werkruimte gebruiken met een aangepaste DNS-server

Wanneer u een Azure Machine Learning werkruimte met een privé-eindpunt gebruikt, zijn er verschillende manieren om [DNS-naamresolutie te verwerken.](../private-link/private-endpoint-dns.md) Standaard verwerkt Azure automatisch naamresolutie voor uw werkruimte en privé-eindpunt. Als u in plaats __daarvan uw eigen aangepaste DNS-server gebruikt,__ moet u handmatig DNS-vermeldingen maken of voorwaardelijke doorsturen gebruiken voor de werkruimte.

> [!IMPORTANT]
> In dit artikel wordt alleen beschreven hoe u de FQDN (Fully Qualified Domain Name) en IP-adressen voor deze vermeldingen kunt vinden. Er wordt geen informatie gegeven over het configureren van de DNS-records voor deze items. Raadpleeg de documentatie voor uw DNS-software voor informatie over het toevoegen van records.

## <a name="prerequisites"></a>Vereisten

- Een Azure Virtual Network die gebruikmaakt [van uw eigen DNS-server.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

- Een Azure Machine Learning werkruimte met een privé-eindpunt. Zie Create [an Azure Machine Learning workspace (Een werkruimte Azure Machine Learning maken) voor meer informatie.](how-to-manage-workspace.md)

- Bekendheid met het gebruik [van netwerkisolatie tijdens het trainen & de deferentie](./how-to-network-security-overview.md).

- Bekendheid met de [configuratie van de DNS-zone van het privé-eindpunt van Azure](../private-link/private-endpoint-dns.md)

- U kunt ook [Azure CLI of](/cli/azure/install-azure-cli) [Azure PowerShell.](/powershell/azure/install-az-ps)

## <a name="public-regions"></a>Openbare regio's

De volgende lijst bevat de volledig gekwalificeerde domeinnamen (FQDN) die worden gebruikt door uw werkruimte als deze zich in een openbare regio::

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.azure.net`

    > [!NOTE]
    > De naam van de werkruimte voor deze FQDN kan worden afgekapt. Afroepen wordt uitgevoerd om `ml-<workspace-name, truncated>-<region>-<workspace-guid>` 63 tekens te behouden.
* `<instance-name>.<region>.instances.azureml.ms`

    > [!NOTE]
    > * Reken-exemplaren zijn alleen toegankelijk vanuit het virtuele netwerk.
    > * Het IP-adres voor deze FQDN is **niet het** IP-adres van de reken-instantie. Gebruik in plaats daarvan het privé-IP-adres van het privé-eindpunt van de werkruimte (het IP-adres van `*.api.azureml.ms` de vermeldingen).)

## <a name="azure-china-21vianet-regions"></a>Azure China 21Vianet regio's

De volgende FQDN's zijn voor Azure China 21Vianet regio's:

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.cn`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.cn`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.chinacloudapi.cn`

    > [!NOTE]
    > De naam van de werkruimte voor deze FQDN kan worden afgekapt. Afzetting wordt uitgevoerd om `ml-<workspace-name, truncated>-<region>-<workspace-guid>` 63 tekens te behouden.
* `<instance-name>.<region>.instances.ml.azure.cn`
## <a name="find-the-ip-addresses"></a>De IP-adressen zoeken

Gebruik een van de volgende methoden om de interne IP-adressen voor de FQDN's in het VNet te vinden:

> [!NOTE]
> De volledig gekwalificeerde domeinnamen en IP-adressen verschillen op basis van uw configuratie. De GUID-waarde in de domeinnaam is bijvoorbeeld specifiek voor uw werkruimte.

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

1. Selecteer in [Azure Portal](https://portal.azure.com)de Azure Machine Learning __werkruimte__.
1. Selecteer in __de sectie__ Instellingen de optie __Privé-eindpuntverbindingen.__
1. Selecteer de koppeling in de __kolom Privé-eindpunt__ die wordt weergegeven.
1. Onderaan de pagina staat een lijst met volledig gekwalificeerde domeinnamen (FQDN) en IP-adressen voor het privé-eindpunt van de werkruimte.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Lijst met FQDN's in de portal":::

---

De informatie die door alle methoden wordt geretourneerd, is hetzelfde; een lijst met de FQDN en het privé-IP-adres voor de resources. Het volgende voorbeeld is afkomstig uit een wereldwijde Azure-regio:

| FQDN | IP-adres |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Sommige FQDN's worden niet weergegeven in vermeld door het privé-eindpunt, maar zijn vereist voor de werkruimte in eastus, southcentralus en westus2. Deze FQDN's worden vermeld in de volgende tabel en moeten ook worden toegevoegd aan uw DNS-server en/of een Azure Privé-DNS Zone:
>
> * `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Als u een reken-exemplaar hebt, gebruikt `<instance-name>.<region>.instances.azureml.ms` u , waarbij de naam is van uw `<instance-name>` reken-exemplaar. Gebruik het privé-IP-adres van het privé-eindpunt van de werkruimte. De reken-instantie is alleen toegankelijk vanuit het virtuele netwerk.
>
> Gebruik voor al deze IP-adressen hetzelfde adres als de vermeldingen `*.api.azureml.ms` die in de vorige stappen zijn geretourneerd.

In de volgende tabel ziet u voorbeeld-IP's uit Azure China 21Vianet regio's:

| FQDN | IP-adres |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.cn` | `10.1.0.5` |
| `ml-mype-pltest-chinaeast2-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.chinacloudapi.cn` | `10.1.0.6` |
## <a name="next-steps"></a>Volgende stappen

Zie het overzicht van virtuele Azure Machine Learning voor meer informatie over het gebruik van virtuele [netwerken met een virtueel netwerk.](how-to-network-security-overview.md)

Zie Azure Private Endpoint DNS-configuratie voor meer informatie over het integreren van privé-eindpunten in uw [DNS-configuratie.](../private-link/private-endpoint-dns.md)
