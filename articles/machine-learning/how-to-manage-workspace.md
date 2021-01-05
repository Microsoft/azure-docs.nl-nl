---
title: Werk ruimten maken in de portal
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken, weer geven en verwijderen van Azure Machine Learning-werk ruimten in de Azure Portal of met de SDK voor python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: 3fca8e74112b90b3cac70adaa955bbf242999705
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739583"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Azure Machine Learning-werk ruimten maken en beheren 

In dit artikel maakt, bekijkt en verwijdert u [**Azure machine learning-werk ruimten**](concept-workspace.md) voor [Azure machine learning](overview-what-is-azure-ml.md), met behulp van de Azure portal of de [SDK voor python](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py)

Als uw behoeften veranderen of vereisten voor automatisering verhogen, kunt u ook werk ruimten maken en verwijderen [met behulp van de CLI](reference-azure-machine-learning-cli.md)of [via de VS code-extensie](tutorial-setup-vscode-extension.md).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).
* Als u de python-SDK gebruikt, [installeert u de SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

## <a name="limitations"></a>Beperkingen

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

Als u een werk ruimte maakt, wordt er standaard ook een Azure Container Registry (ACR) gemaakt.  Omdat ACR momenteel geen Unicode-tekens in namen van resource groepen ondersteunt, gebruikt u een resource groep die deze tekens niet bevat.

## <a name="create-a-workspace"></a>Een werkruimte maken

# <a name="python"></a>[Python](#tab/python)

* **Standaard specificatie.** Standaard worden afhankelijke resources en de resource groep automatisch gemaakt. Met deze code wordt een werk ruimte gemaakt met de naam `myworkspace` en een resource groep met de naam `myresourcegroup` in `eastus2` .
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    Stel `create_resource_group` deze waarde in op ONWAAR als u een bestaande Azure-resource groep hebt die u wilt gebruiken voor de werk ruimte.

* <a name="create-multi-tenant"></a>**Meerdere tenants.**  Als u meerdere accounts hebt, voegt u de Tenant-ID toe van de Azure Active Directory die u wilt gebruiken.  Zoek uw Tenant-ID uit de [Azure Portal](https://portal.azure.com) onder **Azure Active Directory, externe identiteiten**.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **[Soevereine Cloud](reference-machine-learning-cloud-parity.md)**. U hebt extra code nodig om u te verifiëren bij Azure als u in een soevereine Cloud werkt.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **Gebruik bestaande Azure-resources**.  U kunt ook een werk ruimte maken die gebruikmaakt van bestaande Azure-resources met de Azure-Resource-ID-indeling. Zoek de specifieke Azure-resource-Id's in de Azure Portal of met de SDK. In dit voor beeld wordt ervan uitgegaan dat de resource groep, het opslag account, de sleutel kluis, app Insights en container Registry al bestaan.

   ```python
   import os
   from azureml.core import Workspace
   from azureml.core.authentication import ServicePrincipalAuthentication

   service_principal_password = os.environ.get("AZUREML_PASSWORD")

   service_principal_auth = ServicePrincipalAuthentication(
      tenant_id="<tenant-id>",
      username="<application-id>",
      password=service_principal_password)

                        auth=service_principal_auth,
                             subscription_id='<azure-subscription-id>',
                             resource_group='myresourcegroup',
                             create_resource_group=False,
                             location='eastus2',
                             friendly_name='My workspace',
                             storage_account='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.storage/storageaccounts/mystorageaccount',
                             key_vault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/mykeyvault',
                             app_insights='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.insights/components/myappinsights',
                             container_registry='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.containerregistry/registries/mycontainerregistry',
                             exist_ok=False)
   ```

Zie Naslag informatie over de [werk ruimte-SDK](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py).

Als u problemen ondervindt bij het openen van uw abonnement, raadpleegt u [verificatie instellen voor Azure machine learning resources en werk stromen](how-to-setup-authentication.md), evenals de [verificatie in azure machine learning](https://aka.ms/aml-notebook-auth) notebook.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met behulp van de referenties van uw Azure-abonnement. 

1. Selecteer in de linkerbovenhoek van Azure Portal **+ een resource maken**.

      ![Een nieuwe resource maken](./media/how-to-manage-workspace/create-workspace.gif)

1. Gebruik de zoekbalk om **Machine Learning** te vinden.

1. Selecteer **Machine Learning**.

1. Selecteer **Maken** in het deelvenster **Machine Learning** om te beginnen.

1. Geef de volgende gegevens op om uw nieuwe werkruimte te configureren:

   Veld|Beschrijving 
   ---|---
   Werkruimtenaam |Voer een unieke naam in die uw werkruimte aanduidt. In dit voorbeeld gebruiken we **docs-ws**. Namen moeten uniek zijn binnen de resourcegroep. Gebruik een naam die gemakkelijk te onthouden is en te onderscheiden is van door anderen gemaakte werkruimten. De naam van de werk ruimte is niet hoofdletter gevoelig.
   Abonnement |Selecteer het Azure-abonnement dat u wilt gebruiken.
   Resourcegroep | Gebruik een bestaande resourcegroep in uw abonnement of voer een naam in om een nieuwe resourcegroep te maken. Een resourcegroep bevat gerelateerde resources voor een Azure-oplossing. In dit voorbeeld gebruiken we **docs-aml**. U hebt de rol *Inzender* of *eigenaar* nodig voor het gebruik van een bestaande resource groep.  Zie [toegang tot een Azure machine learning-werk ruimte beheren](how-to-assign-roles.md)voor meer informatie over toegang.
   Regio | Selecteer de Azure-regio die het dichtst bij uw gebruikers ligt en de gegevens bronnen om uw werk ruimte te maken.
   | Storage-account | Het standaard opslag account voor de werk ruimte. Standaard wordt er een nieuw item gemaakt. |
   | Key Vault | De Azure Key Vault die wordt gebruikt door de werk ruimte. Standaard wordt er een nieuw item gemaakt. |
   | Application Insights | Het Application Insights-exemplaar voor de werk ruimte. Standaard wordt er een nieuw item gemaakt. |
   | Container Registry | De Azure Container Registry voor de werk ruimte. Standaard wordt een nieuw item _niet_ gemaakt voor de werk ruimte. In plaats daarvan wordt deze gemaakt wanneer u deze nodig hebt tijdens het maken van een docker-installatie kopie tijdens de training of implementatie. |

   :::image type="content" source="media/how-to-manage-workspace/create-workspace-form.png" alt-text="Configureer uw werk ruimte.":::

1. Wanneer u klaar bent met het configureren van de werk ruimte, selecteert u **controleren + maken**. Gebruik eventueel de secties [netwerken](#networking) en [Geavanceerd](#advanced) om meer instellingen te configureren voor de werk ruimte.

1. Controleer de instellingen en breng eventuele aanvullende wijzigingen of correcties aan. Wanneer u tevreden bent met de instellingen, selecteert u **maken**.

   > [!Warning] 
   > Het kan enkele minuten duren om uw werkruimte in de cloud te maken.

   Wanneer het proces is voltooid, wordt er een bericht weergegeven dat de implementatie is geslaagd. 
 
 1. Selecteer **Ga naar resource** om de nieuwe werkruimte te bekijken.
 
---



### <a name="networking"></a>Netwerken  

> [!IMPORTANT]  
> Zie [netwerk isolatie en privacy](how-to-network-security-overview.md)voor meer informatie over het gebruik van een persoonlijk eind punt en een virtueel netwerk met uw werk ruimte.


# <a name="python"></a>[Python](#tab/python)

De Azure Machine Learning python SDK biedt de klasse [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig?preserve-view=true&view=azure-ml-py) , die kan worden gebruikt met de [werk ruimte. Create ()](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-&preserve-view=true) om een werk ruimte met een persoonlijk eind punt te maken. Voor deze klasse is een bestaand virtueel netwerk vereist.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. De standaard netwerk configuratie is het gebruik van een __openbaar eind punt__ dat toegankelijk is op het open bare Internet. Als u de toegang tot uw werk ruimte wilt beperken tot een Azure-Virtual Network die u hebt gemaakt, kunt u in plaats daarvan __persoonlijk eind punt__ (preview) selecteren als de __verbindings methode__ en vervolgens __+ toevoegen__ gebruiken om het eind punt te configureren.   

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Persoonlijke eindpunt selectie":::  

1. Stel op het formulier __persoonlijk eind punt maken__ de locatie, naam en het virtuele netwerk in op gebruik. Als u het eind punt met een Privé-DNS zone wilt gebruiken, selecteert u __integreren met privé-DNS-zone__ en selecteert u de zone in het veld __privé-DNS zone__ . Selecteer __OK__ om het eind punt te maken.   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Persoonlijk eind punt maken":::   

1. Wanneer u klaar bent met het configureren van het netwerk, kunt u __controleren + maken__ selecteren of door gaan naar de optionele __Geavanceerde__ configuratie.

---

> [!IMPORTANT]  
> Het gebruik van een persoonlijk eind punt met Azure Machine Learning werk ruimte bevindt zich momenteel in de open bare preview. Deze preview wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.     
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

### <a name="multiple-workspaces-with-private-endpoint"></a>Meerdere werk ruimten met een persoonlijk eind punt

Wanneer u een persoonlijk eind punt maakt, wordt er een nieuwe Privé-DNS zone gemaakt met de naam __privatelink.API.azureml.MS__ . Dit bevat een koppeling naar het virtuele netwerk. Als u meerdere werk ruimten met persoonlijke eind punten in dezelfde resource groep maakt, kan alleen het virtuele netwerk voor het eerste persoonlijke eind punt worden toegevoegd aan de DNS-zone. Gebruik de volgende stappen om vermeldingen toe te voegen voor de virtuele netwerken die worden gebruikt door de extra werk ruimten/persoonlijke eind punten:

1. Selecteer in de [Azure Portal](https://portal.azure.com)de resource groep die de werk ruimte bevat. Selecteer vervolgens de Privé-DNS zone resource met de naam __privatelink.API.azureml.MS__
2. Selecteer in de __instellingen__ __virtuele netwerk koppelingen__.
3. Selecteer __Toevoegen__. Geef op de pagina __virtuele netwerk koppeling toevoegen__ een unieke naam op voor de __koppeling__ en selecteer vervolgens het __virtuele netwerk__ dat u wilt toevoegen. Selecteer __OK__ om de netwerk koppeling toe te voegen.

Zie voor meer informatie [Azure private endpoint DNS-configuratie](../private-link/private-endpoint-dns.md).

### <a name="vulnerability-scanning"></a>Scannen op beveiligingsproblemen

Azure Security Center biedt geïntegreerd beveiligingsbeheer en geavanceerde bedreigingsbeveiliging voor verschillende hybride cloudworkloads. U moet Azure Security Center toestaan om uw resources te scannen en de aanbevelingen te volgen. Zie  [Azure container Registry Image scanning door Security Center](../security-center/defender-for-container-registries-introduction.md) en [Azure Kubernetes Services-integratie met Security Center](../security-center/defender-for-kubernetes-introduction.md)voor meer.

### <a name="advanced"></a>Geavanceerd

Meta gegevens voor de werk ruimte worden standaard opgeslagen in een Azure Cosmos DB exemplaar dat door micro soft wordt beheerd. Deze gegevens zijn versleuteld met door micro soft beheerde sleutels.

Als u de gegevens wilt beperken die door micro soft worden verzameld op uw werk ruimte, selecteert u een __werk ruimte met een grote bedrijfs impact__ in de portal of stelt u `hbi_workspace=true ` in python in. Zie [versleuteling op rest](concept-data-encryption.md#encryption-at-rest)voor meer informatie over deze instelling.

> [!IMPORTANT]  
> Het selecteren van belang rijke bedrijfs impact kan alleen worden uitgevoerd bij het maken van een werk ruimte. U kunt deze instelling niet wijzigen nadat de werk ruimte is gemaakt.   

#### <a name="use-your-own-key"></a>Uw eigen sleutel gebruiken

U kunt uw eigen sleutel voor gegevens versleuteling opgeven. Dit maakt het Azure Cosmos DB-exemplaar dat meta gegevens opslaat in uw Azure-abonnement.

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Gebruik de volgende stappen om uw eigen sleutel op te geven:

> [!IMPORTANT]  
> Voordat u deze stappen volgt, moet u eerst de volgende acties uitvoeren:   
>
> 1. Machtig de __machine learning-app__ (in identiteits-en toegangs beheer) met Inzender machtigingen voor uw abonnement.  
> 1. Volg de stappen in door de [klant beheerde sleutels configureren](../cosmos-db/how-to-setup-cmk.md) voor:
>     * De Azure Cosmos DB provider registreren
>     * Een Azure Key Vault maken en configureren
>     * Een sleutel genereren
>   
>     U hoeft het Azure Cosmos DB exemplaar niet hand matig te maken, er wordt een voor u gemaakt tijdens het maken van de werk ruimte. Dit Azure Cosmos DB exemplaar wordt in een afzonderlijke resource groep gemaakt met behulp van een naam op basis van dit patroon: `<your-workspace-resource-name>_<GUID>` .   
>   
> U kunt deze instelling niet wijzigen nadat de werk ruimte is gemaakt. Als u de Azure Cosmos DB die door uw werk ruimte worden gebruikt, verwijdert, moet u ook de werk ruimte verwijderen die er gebruik van maakt.

# <a name="python"></a>[Python](#tab/python)

Gebruik `cmk_keyvault` en `resource_cmk_uri` om de door de klant beheerde sleutel op te geven.

```python
from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
               subscription_id='<azure-subscription-id>',
               resource_group='myresourcegroup',
               create_resource_group=True,
               location='eastus2'
               cmk_keyvault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/<keyvault-name>', 
               resource_cmk_uri='<key-identifier>'
               )

```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer door de __klant beheerde sleutels__ en selecteer vervolgens __klikken om een sleutel te selecteren__.

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Door klant beheerde sleutels":::

1. Selecteer in het formulier __sleutel selecteren van Azure Key Vault__ een bestaande Azure Key Vault, een sleutel die deze bevat en de versie van de sleutel. Deze sleutel wordt gebruikt voor het versleutelen van de gegevens die zijn opgeslagen in Azure Cosmos DB. Gebruik tot slot de knop __selecteren__ om deze sleutel te gebruiken.

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Selecteer de sleutel":::

---

### <a name="download-a-configuration-file"></a>Een configuratiebestand downloaden

Als u een [reken instantie](tutorial-1st-experiment-sdk-setup.md#azure)wilt maken, slaat u deze stap over.  Er is al een kopie van dit bestand voor u gemaakt met het reken exemplaar.

# <a name="python"></a>[Python](#tab/python)

Als u van plan bent code te gebruiken in uw lokale omgeving die verwijst naar deze werk ruimte ( `ws` ), schrijft u het configuratie bestand:

```python
ws.write_config()
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Als u van plan bent om code te gebruiken in uw lokale omgeving die verwijst naar deze werk ruimte, selecteert u  **config.jsdownloaden in** het gedeelte **overzicht** van de werk ruimte.  

   ![Config.json downloaden](./media/how-to-manage-workspace/configure.png)

---

Plaats het bestand in de mapstructuur met uw python-scripts of Jupyter-notebooks. Deze kan zich in dezelfde map bevindt, in een submap met de naam *. azureml* of in een bovenliggende map. Wanneer u een reken instantie maakt, wordt dit bestand voor u toegevoegd aan de juiste map op de virtuele machine.

## <a name="connect-to-a-workspace"></a>Verbinding maken met een werkruimte

In uw Python-code maakt u een werkruimte object om verbinding te maken met uw werk ruimte.  Met deze code wordt de inhoud van het configuratie bestand gelezen om uw werk ruimte te vinden.  U wordt gevraagd om u aan te melden als u nog niet bent geverifieerd.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>**Meerdere tenants.**  Als u meerdere accounts hebt, voegt u de Tenant-ID toe van de Azure Active Directory die u wilt gebruiken.  Zoek uw Tenant-ID uit de [Azure Portal](https://portal.azure.com) onder **Azure Active Directory, externe identiteiten**.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* **[Soevereine Cloud](reference-machine-learning-cloud-parity.md)**. U hebt extra code nodig om u te verifiëren bij Azure als u in een soevereine Cloud werkt.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
Als u problemen ondervindt bij het openen van uw abonnement, raadpleegt u [verificatie instellen voor Azure machine learning resources en werk stromen](how-to-setup-authentication.md), evenals de [verificatie in azure machine learning](https://aka.ms/aml-notebook-auth) notebook.

## <a name="find-a-workspace"></a><a name="view"></a>Een werk ruimte zoeken

Bekijk een lijst met alle werk ruimten die u kunt gebruiken.

# <a name="python"></a>[Python](#tab/python)

Zoek uw abonnementen op de [pagina Abonnementen in het Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).  Kopieer de ID en gebruik deze in de onderstaande code om alle beschik bare werk ruimten voor dat abonnement weer te geven.

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Typ **machine learning** in het bovenste zoek veld.  

1. Selecteer **Machine Learning**.

   ![Zoeken naar Azure Machine Learning-werk ruimte](./media/how-to-manage-workspace/find-workspaces.png)

1. Bekijk de lijst met werk ruimten die zijn gevonden. U kunt filteren op basis van abonnement, resource groepen en locaties.  

1. Selecteer een werk ruimte om de eigenschappen ervan weer te geven.

---


## <a name="delete-a-workspace"></a>Een werkruimte verwijderen

Als u een werk ruimte niet meer nodig hebt, verwijdert u deze.  

# <a name="python"></a>[Python](#tab/python)

De werk ruimte verwijderen `ws` :

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

De standaard actie is niet om resources te verwijderen die zijn gekoppeld aan de werk ruimte, dat wil zeggen container register, opslag account, sleutel kluis en Application Insights.  Stel `delete_dependent_resources` deze waarde in op True als u deze resources ook wilt verwijderen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer in de [Azure Portal](https://portal.azure.com/) **verwijderen**  boven aan de werk ruimte die u wilt verwijderen.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Werk ruimte verwijderen":::

---

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Problemen oplossen

* **Ondersteunde browsers in azure machine learning Studio**: we raden u aan om de meest recente browser te gebruiken die compatibel is met uw besturings systeem. De volgende browsers worden ondersteund:
  * Micro soft Edge (de nieuwe micro soft Edge, nieuwste versie. Geen micro soft Edge verouderd)
  * Safari (meest recente versie, alleen Mac)
  * Chrome (meest recente versie)
  * Firefox (meest recente versie)

* **Azure Portal**: 
  * Als u rechtstreeks naar uw werk ruimte gaat vanuit een koppeling voor delen vanuit de SDK of de Azure Portal, kunt u de standaard **overzichts** pagina met abonnements gegevens in de uitbrei ding niet weer geven. In dit scenario kunt u ook niet overschakelen naar een andere werk ruimte. Als u een andere werk ruimte wilt weer geven, gaat u rechtstreeks naar [Azure machine learning Studio](https://ml.azure.com) en zoekt u naar de naam van de werk ruimte.
  * Alle assets (gegevens sets, experimenten, reken processen, enzovoort) zijn alleen beschikbaar in [Azure machine learning Studio](https://ml.azure.com). Deze zijn *niet* beschikbaar via de Azure Portal.

### <a name="resource-provider-errors"></a>Fouten van de resource provider

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>De werk ruimte verplaatsen

> [!WARNING]
> Het verplaatsen van uw Azure Machine Learning-werk ruimte naar een ander abonnement of het verplaatsen van het abonnement dat eigenaar is naar een nieuwe Tenant, wordt niet ondersteund. Dit kan fouten veroorzaken.

### <a name="deleting-the-azure-container-registry"></a>De Azure Container Registry verwijderen

In de Azure Machine Learning-werk ruimte wordt Azure Container Registry (ACR) gebruikt voor bepaalde bewerkingen. Er wordt automatisch een ACR-exemplaar gemaakt wanneer dit voor het eerst nodig is.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="examples"></a>Voorbeelden

Voor beelden van het maken van een werk ruimte:
* Azure Portal gebruiken om [een werk ruimte en een reken instantie te maken](tutorial-1st-experiment-sdk-setup.md)
* Python-SDK gebruiken om [een werk ruimte in uw eigen omgeving te maken](tutorial-1st-experiment-sdk-setup-local.md)

## <a name="next-steps"></a>Volgende stappen

Zodra u een werk ruimte hebt, leert u hoe u [een model traint en implementeert](tutorial-train-models-with-aml.md).