---
title: Rollen in uw werk ruimte beheren
titleSuffix: Azure Machine Learning
description: Meer informatie over toegang tot een Azure Machine Learning-werk ruimte met behulp van Azure RBAC (op rollen gebaseerd toegangs beheer).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 11/09/2020
ms.custom: how-to, seodec18, devx-track-azurecli, contperfq2
ms.openlocfilehash: 8af3da5d04f9aaafc18299f4837660694ee34b51
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345266"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>De toegang tot een Azure Machine Learning-werkruimte beheren

In dit artikel leert u hoe u de toegang (autorisatie) beheert voor een Azure Machine Learning-werk ruimte. [Azure RBAC (op rollen gebaseerd toegangs beheer)](../role-based-access-control/overview.md) wordt gebruikt voor het beheren van de toegang tot Azure-resources, zoals de mogelijkheid om nieuwe resources te maken of bestaande te gebruiken. Gebruikers in uw Azure Active Directory (Azure AD) krijgen specifieke rollen toegewezen, die toegang tot resources verlenen. Azure biedt zowel ingebouwde rollen als de mogelijkheid om aangepaste rollen te maken.

> [!TIP]
> Hoewel dit artikel gericht is op Azure Machine Learning, bieden afzonderlijke services die Azure ML op hun eigen RBAC-instellingen zijn gebaseerd. Met de informatie in dit artikel kunt u bijvoorbeeld configureren wie Score aanvragen kan indienen bij een model dat is geïmplementeerd als webservice op de Azure Kubernetes-service. Maar de Azure Kubernetes-service biedt een eigen set Azure-rollen. Raadpleeg de volgende koppelingen voor informatie over servicespecifieke gegevens die nuttig kunnen zijn met Azure Machine Learning:
>
> * [Toegang tot Azure Kubernetes-cluster resources beheren](../aks/azure-ad-rbac.md)
> * [Azure RBAC gebruiken voor Kubernetes-autorisatie](../aks/manage-azure-rbac.md)
> * [Azure RBAC gebruiken voor toegang tot BLOB-gegevens](/azure/storage/common/storage-auth-aad-rbac-portal)

> [!WARNING]
> Het Toep assen van sommige functies kan de functionaliteit van de gebruikers interface in Azure Machine Learning Studio beperken voor andere gebruikers. Als de rol van een gebruiker bijvoorbeeld niet de mogelijkheid heeft om een reken instantie te maken, is de optie voor het maken van een reken instantie niet beschikbaar in Studio. Dit gedrag wordt verwacht en voor komt dat de gebruiker probeert bewerkingen uit te voeren die een fout bericht krijgen dat de toegang is geweigerd.

## <a name="default-roles"></a>Standaard rollen

Een Azure Machine Learning-werkruimte is een Azure-resource. Net als andere Azure-resources, heeft een nieuwe Azure Machine Learning-werkruimte drie standaardrollen wanneer deze wordt gemaakt. U kunt gebruikers toevoegen aan de werk ruimte en ze toewijzen aan een van deze ingebouwde rollen.

| Rol | Toegangsniveau |
| --- | --- |
| **Lezer** | Alleen-lezen acties in de werk ruimte. Lezers kunnen assets weer geven en bekijken, inclusief [Data Store](how-to-access-data.md) -referenties, in een werk ruimte. Lezers kunnen deze assets niet maken of bijwerken. |
| **Inzender** | Activa in een werk ruimte weer geven, maken, bewerken of verwijderen (indien van toepassing). Zo kunnen inzenders een experiment maken, een rekencluster maken of koppelen, een uitvoering starten en een webservice implementeren. |
| **Eigenaar** | Volledige toegang tot de werk ruimte, met inbegrip van de mogelijkheid om activa in een werk ruimte weer te geven, te maken, te bewerken of te verwijderen (indien van toepassing). Daarnaast kunt u roltoewijzingen wijzigen. |
| **Aangepaste rol** | Hiermee kunt u de toegang tot specifieke besturings elementen of gegevenslaag bewerkingen in een werk ruimte aanpassen. Bijvoorbeeld het verzenden van een run, het maken van een compute, het implementeren van een model of het registreren van een gegevensset. |

> [!IMPORTANT]
> De toegang tot rollen kan worden beperkt tot meerdere niveaus in Azure. Bijvoorbeeld: iemand met een eigenaar van de gebruikers toegang tot een werk ruimte heeft geen eigenaars toegang tot de resource groep die de werk ruimte bevat. Zie [hoe Azure RBAC werkt](../role-based-access-control/overview.md#how-azure-rbac-works)voor meer informatie.

Er zijn momenteel geen aanvullende ingebouwde rollen die specifiek zijn voor Azure Machine Learning. Zie [ingebouwde rollen van Azure](../role-based-access-control/built-in-roles.md)voor meer informatie over ingebouwde rollen.

## <a name="manage-workspace-access"></a>Toegang tot de werk ruimte beheren

Als u eigenaar bent van een werk ruimte, kunt u rollen toevoegen en verwijderen voor de werk ruimte. U kunt ook rollen toewijzen aan gebruikers. Gebruik de volgende koppelingen om te ontdekken hoe u de toegang beheert:
- [Azure Portal gebruikers interface](../role-based-access-control/role-assignments-portal.md)
- [PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Azure-CLI](../role-based-access-control/role-assignments-cli.md)
- [REST API](../role-based-access-control/role-assignments-rest.md)
- [Azure Resource Manager sjablonen](../role-based-access-control/role-assignments-template.md)

Als u de [Azure machine learning cli](reference-azure-machine-learning-cli.md)hebt geïnstalleerd, kunt u de CLI-opdrachten gebruiken om rollen toe te wijzen aan gebruikers:

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Het `user` veld is het e-mail adres van een bestaande gebruiker in het exemplaar van Azure Active Directory waarin het bovenliggende abonnement van de werk ruimte woont. Hier volgt een voor beeld van het gebruik van deze opdracht:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> de opdracht AZ ml Workspace share werkt niet voor een federatief account door Azure Active Directory B2B. Gebruik de Azure UI-Portal in plaats van de opdracht.

## <a name="create-custom-role"></a>Aangepast rol maken

Als de ingebouwde rollen niet voldoen, kunt u aangepaste rollen maken. Aangepaste rollen kunnen Lees-, schrijf-, verwijder-en reken resource machtigingen hebben in die werk ruimte. U kunt de rol beschikbaar maken op een specifiek werk ruimte niveau, op een specifiek niveau van een resource groep of op een specifiek abonnements niveau.

> [!NOTE]
> U moet een eigenaar van de resource op dat niveau zijn om aangepaste rollen te maken binnen die resource.

Als u een aangepaste rol wilt maken, maakt u eerst een JSON-bestand met een roldefinitie waarmee u de machtiging en het bereik voor de rol opgeeft. In het volgende voor beeld wordt een aangepaste functie gedefinieerd met de naam ' data wetenschapper Custom ' op een specifiek niveau van de werk ruimte:

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> U kunt het `AssignableScopes` veld wijzigen om het bereik van deze aangepaste rol in te stellen op abonnements niveau, op het niveau van de resource groep of op een bepaald niveau van de werk ruimte.
> De bovenstaande aangepaste rol is slechts een voor beeld. Zie een aantal voorgestelde [aangepaste rollen voor de Azure machine learning-service](#customroles).

Deze aangepaste rol kan alles in de werk ruimte doen, met uitzonde ring van de volgende acties:

- Het is niet mogelijk om een compute-resource te maken of bij te werken.
- Er kan geen reken resource worden verwijderd.
- Er kunnen geen roltoewijzingen worden toegevoegd, verwijderd of gewijzigd.
- De werk ruimte kan niet worden verwijderd.

Gebruik de volgende Azure CLI-opdracht om deze aangepaste functie te implementeren:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Na de implementatie wordt deze rol beschikbaar in de opgegeven werk ruimte. U kunt deze rol nu toevoegen en toewijzen in de Azure Portal. U kunt deze rol ook toewijzen aan een gebruiker met behulp van de `az ml workspace share` cli-opdracht:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Zie aangepaste rollen voor [Azure](../role-based-access-control/custom-roles.md)voor meer informatie over aangepaste rollen. 

### <a name="azure-machine-learning-operations"></a>Azure Machine Learning bewerkingen

Zie bewerkingen voor de [resource provider](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)voor meer informatie over de bewerkingen (acties en niet acties) die kunnen worden gebruikt met aangepaste rollen. U kunt ook de volgende Azure CLI-opdracht gebruiken om bewerkingen weer te geven:

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

## <a name="list-custom-roles"></a>Aangepaste rollen opvragen

Voer de volgende opdracht uit in de Azure CLI:

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

Gebruik de volgende Azure CLI-opdracht om de roldefinitie voor een specifieke aangepaste rol weer te geven. De `<role-name>` notatie moet hetzelfde zijn als de indeling die wordt geretourneerd door de bovenstaande opdracht:

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

## <a name="update-a-custom-role"></a>Een aangepaste rol bijwerken

Voer de volgende opdracht uit in de Azure CLI:

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

U moet machtigingen hebben voor het hele bereik van de nieuwe roldefinitie. Als deze nieuwe rol bijvoorbeeld een bereik heeft over drie abonnementen, moet u machtigingen hebben voor alle drie de abonnementen. 

> [!NOTE]
> Het kan 15 minuten tot een uur duren voordat de functie-updates zijn toegepast op alle roltoewijzingen in dat bereik.

## <a name="common-scenarios"></a>Algemene scenario's

De volgende tabel bevat een overzicht van Azure Machine Learning activiteiten en de machtigingen die nodig zijn om ze uit te voeren op het minste bereik. Als een activiteit bijvoorbeeld kan worden uitgevoerd met een werkruimte bereik (kolom 4), zal alle grotere bereiken met die machtiging ook automatisch worden gebruikt:

> [!IMPORTANT]
> Alle paden in deze tabel die beginnen met `/` **relatieve paden** zijn `Microsoft.MachineLearningServices/` :

| Activiteit | Bereik op abonnements niveau | Bereik van resource groeps niveau | Bereik op werkruimte niveau |
| ----- | ----- | ----- | ----- |
| Nieuwe werk ruimte maken | Niet vereist | Eigenaar of bijdrager | N.v.t. (wordt eigenaar of neemt de hogere bereik functie over nadat deze is gemaakt) |
| Amlcompute-quotum niveau aanvragen of quotum voor werkruimte niveau instellen | Eigenaar of Inzender, of aangepaste rol </br>kunnen `/locations/updateQuotas/action`</br> bij abonnements bereik | Niet geautoriseerd | Niet geautoriseerd |
| Nieuw reken cluster maken | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor: `/workspaces/computes/write` |
| Nieuw reken exemplaar maken | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor: `/workspaces/computes/write` |
| Elk type uitvoering verzenden | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor: `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| Pijp lijnen en eind punten publiceren | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor: `"/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Een geregistreerd model implementeren op een AKS/ACI-bron | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor: `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Score voor een geïmplementeerd AKS-eind punt | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol waarmee: `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (wanneer u geen Azure Active Directory auth) of `"/workspaces/read"` (wanneer u token verificatie gebruikt) |
| Toegang tot opslag met interactieve notebooks | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor: `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` |
| Nieuwe aangepaste rol maken | Eigenaar, bijdrager of aangepaste rol die `Microsoft.Authorization/roleDefinitions/write` | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor: `/workspaces/computes/write` |

> [!TIP]
> Als er een fout optreedt wanneer u een werk ruimte voor de eerste keer probeert te maken, moet u ervoor zorgen dat uw rol toestaat `Microsoft.MachineLearningServices/register/action` . Met deze actie kunt u de Azure Machine Learning Resource provider registreren bij uw Azure-abonnement.

### <a name="user-assigned-managed-identity-with-azure-ml-compute-cluster"></a>Door de gebruiker toegewezen beheerde identiteit met Azure ML Compute-Cluster

Als u een door de gebruiker toegewezen identiteit aan een Azure Machine Learning Compute-cluster wilt toewijzen, moet u schrijf machtigingen hebben voor het maken van de reken [kundige rol van de beheerde identiteit](../role-based-access-control/built-in-roles.md#managed-identity-operator). Lees voor meer informatie over Azure RBAC met beheerde identiteiten [hoe u een door de gebruiker toegewezen identiteit beheert](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

### <a name="mlflow-operations"></a>MLflow bewerkingen

Als u MLflow-bewerkingen wilt uitvoeren met uw Azure Machine Learning-werk ruimte, gebruikt u de volgende bereiken voor uw aangepaste rol:

| Bewerking MLflow | Bereik |
| --- | --- |
| Alle experimenten in de werk ruimte bijhouden, een experimenteren met de id, een experiment verkrijgen op naam | `Microsoft.MachineLearningServices/workspaces/experiments/read` |
| Een experiment met een naam maken, een tag op een experiment instellen, een experiment herstellen dat is gemarkeerd voor verwijdering| `Microsoft.MachineLearningServices/workspaces/experiments/write` | 
| Een experiment verwijderen | `Microsoft.MachineLearningServices/workspaces/experiments/delete` |
| Een uitvoering en gerelateerde gegevens en meta gegevens ophalen, een lijst ophalen van alle waarden voor de opgegeven metriek voor een bepaalde uitvoering, lijst artefacten voor een uitvoering | `Microsoft.MachineLearningServices/workspaces/experiments/runs/read` |
| Een nieuwe uitvoeringsrun binnen een experiment maken, uitvoeringen verwijderen, verwijderde uitvoeringen herstellen, metrische gegevens registreren onder de huidige uitvoering, tags instellen voor een uitvoering, Tags verwijderen bij een uitvoering, logboek-para meters (sleutel waarde-paar) voor een uitvoering registreren, een batch met metrische gegevens, para meters en Tags vastleggen voor een uitvoering, de status van de update-uitvoering | `Microsoft.MachineLearningServices/workspaces/experiments/runs/write` |
| Geregistreerd model op naam ophalen, een lijst met alle geregistreerde modellen in het REGI ster ophalen, zoeken naar geregistreerde modellen, nieuwste versie modellen voor elke aanvragen fase, een geregistreerde model versie ophalen, versies van het zoek model ontvangen, URI ophalen waarbij de artefacten van een model versie worden opgeslagen, zoeken naar uitvoeringen op experiment-id's | `Microsoft.MachineLearningServices/workspaces/models/read` |
| Maak een nieuw geregistreerd model, werk de naam/beschrijving van een geregistreerd model bij, wijzig de naam van het bestaande geregistreerde model, maak een nieuwe versie van het model, werk de beschrijving van een model versie bij en zet een geregistreerd model om in een van de fasen | `Microsoft.MachineLearningServices/workspaces/models/write` |
| Een geregistreerd model samen met alle versies verwijderen, specifieke versies van een geregistreerd model verwijderen | `Microsoft.MachineLearningServices/workspaces/models/delete` |

<a id="customroles"></a>

## <a name="example-custom-roles"></a>Voor beeld van aangepaste rollen

### <a name="data-scientist"></a>Gegevenswetenschapper

Hiermee kan een gegevens wetenschapper alle bewerkingen binnen een werk ruimte uitvoeren **, met uitzonde ring** van:

* Maken van compute
* Modellen implementeren in een productie AKS-cluster
* Een pijplijn eindpunt implementeren in productie

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/*/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="data-scientist-restricted"></a>Beperkte gegevens wetenschapper

Een meer beperkte roldefinitie zonder joker tekens in de toegestane acties. Hiermee kunnen alle bewerkingen binnen een werk ruimte worden uitgevoerd **, met uitzonde ring** van:

* Maken van compute
* Modellen implementeren in een productie AKS-cluster
* Een pijplijn eindpunt implementeren in productie

`data_scientist_restricted_custom_role.json` :
```json
{
    "Name": "Data Scientist Restricted Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/computes/start/action",
        "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
        "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
        "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
        "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
        "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/modules/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/write",
        "Microsoft.MachineLearningServices/workspaces/datastores/delete"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```
     
### <a name="mlflow-data-scientist"></a>MLflow data wetenschapper

Hiermee kan een Data wetenschapper alle door MLflow ondersteunde bewerkingen uitvoeren **, met uitzonde ring** van:

* Maken van compute
* Modellen implementeren in een productie AKS-cluster
* Een pijplijn eindpunt implementeren in productie

`mlflow_data_scientist_custom_role.json` :
```json
{
    "Name": "MLFlow Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can perform azureml mlflow integrated functionalities that includes mlflow tracking, projects, model registry",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/experiments/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/delete",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/models/read",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/models/delete"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```   

### <a name="mlops"></a>MLOps

Hiermee kunt u een rol toewijzen aan een Service-Principal en gebruiken om uw MLOps-pijp lijnen te automatiseren. Bijvoorbeeld voor het verzenden van uitvoeringen voor een al gepubliceerde pijp lijn:

`mlops_custom_role.json` :
```json
{
    "Name": "MLOps Custom",
    "IsCustom": true,
    "Description": "Can run pipelines against a published pipeline endpoint",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
        "Microsoft.MachineLearningServices/workspaces/environments/read",    
        "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
        "Microsoft.MachineLearningServices/workspaces/modules/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/read",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="workspace-admin"></a>Werkruimte beheerder

Hiermee kunt u alle bewerkingen binnen het bereik van een werk ruimte uitvoeren, **met uitzonde ring** van:

* Een nieuwe werk ruimte maken
* Quota voor abonnements-of werkruimte niveau toewijzen

De werkruimte beheerder kan ook geen nieuwe rol maken. U kunt alleen bestaande ingebouwde of aangepaste rollen toewijzen binnen het bereik van hun werk ruimte:

`workspace_admin_custom_role.json` :
```json
{
    "Name": "Workspace Admin Custom",
    "IsCustom": true,
    "Description": "Can perform all operations except quota management and upgrades",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/write",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.Authorization/roleAssignments/*"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

<a name="labeler"></a>
### <a name="data-labeler"></a>Gegevens Labeler

Hiermee kunt u een Role alleen definiëren voor het labelen van gegevens:

`labeler_custom_role.json` :
```json
{
    "Name": "Labeler Custom",
    "IsCustom": true,
    "Description": "Can label data for Labeling",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

## <a name="troubleshooting"></a>Problemen oplossen

Hier volgen enkele dingen waarvan u op de hoogte moet zijn terwijl u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruikt:

- Wanneer u een resource maakt in azure, zoals een werk ruimte, bent u niet rechtstreeks de eigenaar van de resource. Uw rol wordt overgenomen van de hoogste rol van het bereik waarvoor u gemachtigd bent in dat abonnement. Als u bijvoorbeeld een netwerk beheerder bent en u de machtigingen hebt om een Machine Learning-werk ruimte te maken, wijst u de rol netwerk beheerder toe aan die werk ruimte en niet de rol eigenaar.

- Als u quota bewerkingen in een werk ruimte wilt uitvoeren, moet u machtigingen op abonnements niveau hebben. Dit betekent dat het instellen van quotum op abonnements niveau of quotum voor het werkruimte niveau voor uw beheerde reken resources alleen kan optreden als u schrijf machtigingen hebt voor het abonnements bereik.

- Wanneer er twee roltoewijzingen aan dezelfde Azure Active Directory gebruiker met conflicterende secties met acties/verhoudingen, worden uw bewerkingen die worden vermeld in de ene rol, mogelijk niet van kracht als ze ook worden weer gegeven als acties in een andere rol. Lees [hoe Azure RBAC bepaalt of een gebruiker toegang heeft tot een resource](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource) voor meer informatie over hoe Azure-roltoewijzingen parseert.

- Als u uw reken resources binnen een VNet wilt implementeren, moet u expliciet machtigingen hebben voor de volgende acties:
    - `Microsoft.Network/virtualNetworks/join/action` op de VNet-resource.
    - `Microsoft.Network/virtualNetworks/subnet/join/action` op de bron van het subnet.
    
    Zie voor meer informatie over Azure RBAC met netwerken de [ingebouwde rollen voor netwerken](../role-based-access-control/built-in-roles.md#networking).

- Het kan soms Maxi maal 1 uur duren voordat de nieuwe roltoewijzingen van kracht worden over machtigingen in de cache in de stack.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Enterprise-beveiliging](concept-enterprise-security.md)
- [Overzicht van virtuele netwerk isolatie en privacy](how-to-network-security-overview.md)
- [Zelf studie: modellen trainen](tutorial-train-models-with-aml.md)
- [Resourceproviderbewerkingen](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)
