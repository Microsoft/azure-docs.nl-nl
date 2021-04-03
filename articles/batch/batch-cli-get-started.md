---
title: Aan de slag met Azure CLI voor batch
description: Een korte inleiding in de Batch-opdrachten in Azure CLI voor het beheren van Azure Batch-serviceresources
ms.topic: how-to
ms.date: 07/24/2018
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: bee25d9b8985f1627a5cfc05bfb336b83be60f74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144755"
---
# <a name="manage-batch-resources-with-azure-cli"></a>Batch-resources beheren met Azure CLI

Azure CLI is de nieuwe opdrachtregel van Azure voor het beheren van Azure-resources. Deze kan worden gebruikt in Mac OS, Linux en Windows. De Azure CLI is geoptimaliseerd voor het beheren van Azure-resources vanaf de opdrachtregel. U kunt de Azure CLI gebruiken om uw Azure Batch-accounts te beheren en om resources zoals pools, functies en taken te beheren. Met de Azure CLI kunt u scripts maken om veel van dezelfde taken uit te voeren die u ook uitvoert met de Batch-API's, Azure Portal en Batch PowerShell-cmdlets.

Dit artikel biedt een overzicht van het gebruik van [Azure CLI versie 2.0](/cli/azure) met Batch. Zie [Aan de slag met Azure CLI](/cli/azure/get-started-with-azure-cli) voor een overzicht van het gebruik van CLI met Azure.

## <a name="set-up-the-azure-cli"></a>De Azure CLI instellen

U kunt de nieuwste Azure CLI uitvoeren in [Azure Cloud Shell](../cloud-shell/overview.md). Volg de stappen in [Azure CLI installeren](/cli/azure/install-azure-cli) als u Azure CLI lokaal wilt installeren.

> [!TIP]
> Het wordt aangeraden de Azure CLI-installatie regelmatig bij te werken om te profiteren van service-updates en verbeteringen.
> 
> 

## <a name="command-help"></a>Opdracht Help

U kunt voor elke opdracht in de Azure CLI Help-tekst weergeven door `-h` toe te voegen aan de opdracht. Laat alle andere opties weg. Bijvoorbeeld:

* Als u hulp nodig hebt bij de opdracht `az`, voert u in: `az -h`
* Voor een lijst met alle Batch-opdrachten in de opdrachtregelinterface, gebruikt u: `az batch -h`
* Als u hulp nodig hebt bij het maken van een Batch-account, voert u in: `az batch account create -h`

Gebruik bij twijfel de opdrachtregeloptie `-h` voor hulp bij een willekeurige Azure CLI-opdracht.



Raadpleeg de naslagdocumentatie van Azure CLI voor informatie over [Azure CLI-opdrachten voor Batch](/cli/azure/batch). 

## <a name="log-in-and-authenticate"></a>Aanmelden en verifiëren

Als u de Azure CLI wilt gebruiken met Batch, moet u zich aanmelden en verifiëren. Dit kan via twee eenvoudige stappen:

1. **Aanmelden bij Azure.** Als u bent aangemeld bij Azure, hebt u toegang tot opdrachten van Azure Resource Manager, inclusief opdrachten van de [Batch Management-service](batch-management-dotnet.md).  
2. **Aanmelden bij uw Batch-account.** Als u bent aangemeld bij uw Batch-account, hebt u toegang tot opdrachten van de Batch-service.   

### <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Er zijn een aantal manieren om u aan te melden bij Azure, zoals u kunt lezen in [Aanmelden met de Azure CLI](/cli/azure/authenticate-azure-cli):

1. [Interactief aanmelden](/cli/azure/authenticate-azure-cli). Meld u interactief aan wanneer u zelf Azure CLI-opdrachten uitvoert vanaf de opdrachtregel.
2. [Meld u aan met een Service-Principal](/cli/azure/authenticate-azure-cli). Meld u aan met een service-principal wanneer u Azure CLI-opdrachten uitvoert vanuit een script of een toepassing.

Ten behoeve van dit artikel laten we zien hoe u zich interactief aanmeldt bij Azure. Typ [az login](/cli/azure/reference-index#az-login) op de opdrachtregel:

```azurecli
# Log in to Azure and authenticate interactively.
az login
```

De opdracht `az login` retourneert een token dat u kunt gebruiken om u te verifiëren, zoals hier wordt weergegeven. Volg de weergegeven instructies om een webpagina te openen en het token naar Azure te verzenden:

![Meld u aan bij Azure.](./media/batch-cli-get-started/az-login.png)

De voorbeelden in de sectie Voorbeelden van shell-scripts laten ook zien hoe u een Azure CLI-sessie start door u interactief aan te melden bij Azure. Wanneer u bent aangemeld, kunt u opdrachten aanroepen voor gebruik met resources van Batch Management, met inbegrip van Batch-accounts, sleutels, toepassingspakketten en quota's.  

### <a name="log-in-to-your-batch-account"></a>Aanmelden bij uw Batch-account

Als u de Azure CLI wilt gebruiken voor het beheren van Batch-resources, zoals pools, functies en taken, moet u zich aanmelden bij uw Batch-account en u vervolgens verifiëren. U kunt zich aanmelden bij de Batch-service met de opdracht [az batch account login](/cli/azure/batch/account#az-batch-account-login). 

Er zijn twee mogelijkheden voor verificatie van uw Batch-account:

- **Met behulp van Azure Active Directory-verificatie (Azure AD)** 

    Verificatie met Azure AD is de standaardinstelling als u de Azure CLI gebruikt met Batch en wordt aanbevolen voor de meeste scenario's. 
    
    Wanneer u zich interactief aanmeldt bij Azure, zoals beschreven in de vorige sectie, worden uw referenties in de cache opgeslagen, zodat de Azure CLI u met behulp van deze zelfde referenties kan aanmelden bij uw Batch-account. Als u zich aanmeldt bij Azure met behulp van een service-principal, worden deze referenties ook gebruikt voor aanmelding bij uw Batch-account.

    Een voor deel van Azure AD is dat het Azure op rollen gebaseerd toegangs beheer (Azure RBAC) biedt. Met Azure RBAC is de toegang van een gebruiker afhankelijk van hun toegewezen rol, in plaats van of ze beschikken over de account sleutels. In plaats van account sleutels te beheren, kunt u Azure-rollen beheren en toegang en verificatie van Azure AD toestaan.  

     Als u zich via Azure AD wilt aanmelden bij uw Batch-account, gebruikt u de opdracht [az batch account login](/cli/azure/batch/account#az-batch-account-login): 

    ```azurecli
    az batch account login -g myresource group -n mybatchaccount
    ```

- **Met behulp van gedeelde sleutel verificatie**

    Bij [gedeelde sleutelverificatie](/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service#authentication-via-shared-key) worden de toegangssleutels van uw account gebruikt om Azure CLI-opdrachten te verifiëren voor de Batch-service.

    Als u Azure CLI-scripts maakt om het aanroepen van Batch-opdrachten te automatiseren, kunt u gebruikmaken van gedeelde sleutelverificatie of van een service-principal van Azure AD. In sommige scenario's kan gedeelde sleutelverificatie een eenvoudigere oplossing zijn dan het maken van een service-principal.  

    Als u zich wilt aanmelden met behulp van gedeelde sleutelverificatie, gebruikt u de optie `--shared-key-auth` op de opdrachtregel:

    ```azurecli
    az batch account login -g myresourcegroup -n mybatchaccount --shared-key-auth
    ```

De voorbeelden in de sectie Voorbeelden van shell-scripts laten zien hoe u zich met de Azure CLI aanmeldt bij uw Batch-account met zowel Azure AD als een gedeelde sleutel.

## <a name="use-azure-batch-cli-extension-commands"></a>Opdrachten van de CLI-extensie van Azure Batch gebruiken

Als u de CLI-extensie van Azure Batch installeert, kunt u Azure CLI gebruiken om Batch-taken end-to-end uit te voeren zonder code te schrijven. Met Batchopdrachten die door de extensie worden ondersteund, kunt u JSON-sjablonen gebruiken om pools en taken te maken met Azure CLI. U kunt de opdrachten van de CLI-extensie ook gebruiken om taakinvoerbestanden te uploaden naar het Azure Storage-account dat is gekoppeld aan het Batch-account, en er de taakuitvoerbestanden downloaden. Zie [Azure Batch CLI-sjablonen en -bestandsoverdracht gebruiken](batch-cli-templates.md) voor meer informatie.

## <a name="script-examples"></a>Voorbeeldscripts

Bekijk de [Voorbeelden van CLI-scripts](./scripts/batch-cli-sample-create-account.md) voor Batch voor veelvoorkomende taken. Deze voorbeelden omvatten veel van de opdrachten die beschikbaar zijn in de Azure CLI voor Batch voor het maken en beheren van accounts, pools en taken.

## <a name="json-files-for-resource-creation"></a>JSON-bestanden voor het maken van resources

Als u Batch-resources maakt, zoals groepen en taken, kunt u een JSON-bestand opgeven dat de configuratie van de nieuwe resource bevat, in plaats van de bijbehorende parameters op te geven als opdrachtregelopties. Bijvoorbeeld:

```azurecli
az batch pool create my_batch_pool.json
```

Hoewel u de meeste resources kunt maken met alleen opdrachtregelopties, is voor sommige functies vereist dat u een bestand met de JSON-indeling opgeeft met daarin details van de resource. U moet bijvoorbeeld een JSON-bestand gebruiken als u resourcebestanden wilt opgeven voor een starttaak.

Informatie over de juiste JSON-syntaxis voor het maken van een resource vindt u in de Engelstalige [Azure Batch REST API Reference][rest_api]. De onderwerpen voor het toevoegen van een *resourcetype* in de Azure Batch REST API Reference bevat JSON-voorbeeldscripts voor het maken van die resource. U kunt deze JSON-voorbeeldscripts als sjablonen gebruiken voor JSON-bestanden die u met de Azure CLI wilt gebruiken. Als u bijvoorbeeld de JSON-syntaxis wilt zien voor het maken van een pool, raadpleegt u [Add a pool to an account][rest_add_pool] (Een pool toevoegen aan een account).

Zie [Running jobs on Azure Batch with Azure CLI](./scripts/batch-cli-sample-run-job.md) (Taken uitvoeren in Azure Batch met Azure CLI) voor een voorbeeld van een script waarin een JSON-bestand is opgegeven.

> [!NOTE]
> Als u een JSON-bestand opgeeft bij het maken van een resource, worden alle andere parameters die u opgeeft op de opdrachtregel, genegeerd.
> 
> 

## <a name="efficient-queries-for-batch-resources"></a>Efficiënte query's voor Batch-resources

Elk Batch-resourcetype ondersteunt een `list`-opdracht die een query uitvoert voor het Batch-account, en vermeldt een lijst met resources van dit type. U kunt bijvoorbeeld de groepen in uw account vermelden en de taken in een taak:

```azurecli
az batch pool list
az batch task list --job-id job001
```

Wanneer u op de Batch-service een query uitvoert met daarin een `list`-bewerking, kunt u een OData-component opgeven om de hoeveelheid geretourneerde gegevens te beperken. Omdat de filteracties op de server plaatsvinden, worden alleen de gegevens geretourneerd waarin u bent geïnteresseerd. Gebruik deze componenten om bandbreedte (en dus tijd) te besparen tijdens het uitvoeren van lijstbewerkingen.

De volgende tabel beschrijft de OData-componenten die worden ondersteund door de Batch-service:

| Component | Description |
|---|---|
| `--select-clause [select-clause]` | Retourneert een subset met eigenschappen voor elke entiteit. |
| `--filter-clause [filter-clause]` | Retourneert alleen entiteiten die overeenkomen met de opgegeven OData-expressie. |
| `--expand-clause [expand-clause]` | Vraagt de entiteitgegevens op in één onderliggende REST-aanroep. De component expand ondersteunt momenteel alleen de eigenschap `stats`. |

Zie [Een functie en taken uitvoeren met Batch](./scripts/batch-cli-sample-run-job.md) voor een voorbeeld van een script dat laat zien hoe u een OData-component gebruikt.

Zie [Create queries to list Batch resources efficiently](batch-efficient-list-queries.md) (Query's maken om efficiënt Batch-resources op te vragen) voor meer informatie over het uitvoeren van efficiënte lijstquery's met OData-componenten.

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

De volgende tips kunnen helpen bij het oplossen van problemen met Azure CLI:

* Gebruik `-h` om **Help-tekst** weer te geven voor elke willekeurige CLI-opdracht
* Gebruik `-v` en `-vv` om **uitgebreide** opdrachtuitvoer weer te geven. Als u de vlag `-vv` toevoegt, toont de Azure CLI de werkelijke REST-aanvragen en -antwoorden. Deze schakelopties zijn handig voor het weergeven van de volledige foutuitvoer.
* U kunt **opdrachtuitvoer weergeven als JSON** met de `--json`-optie. `az batch pool show pool001 --json` wordt bijvoorbeeld weergegeven als eigenschappen van pool001 in de JSON-indeling. Vervolgens kunt u deze uitvoer kopiëren en aanpassen om te worden gebruikt in een `--json-file` (zie JSON-bestanden eerder in dit artikel).
<!---Loc Comment: Please, check link [JSON files] since it's not redirecting to any location.--->

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [documentatie van Azure cli](/cli/azure).
* Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
* Meer informatie over het gebruik van batch-sjablonen om Pools, taken en taken te maken zonder code te hoeven schrijven [Azure batch cli-sjablonen en-bestands overdracht](batch-cli-templates.md).

[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: /rest/api/batchservice/
[rest_add_pool]: /rest/api/batchservice/pool/add
