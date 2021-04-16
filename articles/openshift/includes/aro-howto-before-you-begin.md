---
author: sabbour
ms.author: asabbour
ms.date: 4/5/2020
ms.openlocfilehash: 1fded0ad08af4b1e5d915e32e09087c1a78bd318
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520706"
---
### <a name="create-the-cluster"></a>Het cluster maken

Volg de zelfstudie om [een cluster Azure Red Hat OpenShift maken.](../tutorial-create-cluster.md) Als u ervoor kiest om CLI lokaal te installeren en gebruiken, moet u Azure CLI versie 2.6.0 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Als u een Azure Red Hat OpenShift cluster wilt beheren, gebruikt u [oc](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html), de OpenShift-opdrachtregelclient.

> [!NOTE]
> U wordt [aangeraden de OpenShift-opdrachtregel](../tutorial-connect-cluster.md) te installeren [op Azure Cloud Shell](https://shell.azure.com/) en deze te gebruiken voor alle onderstaande opdrachtregelbewerkingen. Start de shell vanuit shell.azure.com of door op de koppeling te klikken:
>
> [![Starten insluiten](https://docs.microsoft.com/azure/includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "Azure Cloud Shell starten")](https://shell.azure.com/bash)

Volg de zelfstudie om de CLI te installeren, de clusterreferenties op te halen en verbinding te maken met het [cluster](../tutorial-connect-cluster.md) met behulp van de webconsole en de OpenShift CLI.

Wanneer u bent aangemeld, ziet u een bericht met de melding dat u het `default` project gebruikt.

```output
Login successful.

You have access to 61 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```
