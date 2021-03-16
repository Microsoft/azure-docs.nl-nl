---
title: Continue integratie en levering voor Synapse-werk ruimte
description: Meer informatie over hoe u doorlopende integratie en levering kunt gebruiken om wijzigingen in de werk ruimte te implementeren in een andere omgeving (ontwikkeling, test, productie).
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: de3738573bb9bb6f045a45d290c74ba9e6902a5e
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561954"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Continue integratie en levering voor Azure Synapse-werk ruimte

## <a name="overview"></a>Overzicht

Continue integratie (CI) is het proces van het automatiseren van het bouwen en testen van code telkens wanneer een teamlid wijzigingen in versie beheer doorvoert. Continue implementatie (CD) is het proces voor het bouwen, testen, configureren en implementeren van meerdere test-of faserings omgevingen naar een productie omgeving.

Voor de Azure Synapse-werk ruimte, doorlopende integratie en levering (CI/CD) worden alle entiteiten van de ene omgeving (ontwikkeling, test, productie) naar de andere verplaatst. Als u uw werk ruimte wilt promo veren naar een andere werk ruimte, zijn er twee onderdelen: gebruik [Azure Resource Manager sjablonen](../../azure-resource-manager/templates/overview.md) om werkruimte resources (Pools en werk ruimte) te maken of bij te werken. Migreer artefacten (SQL-scripts, notebook, Spark-taak definitie, pijp lijnen, gegevens sets, data stromen, enzovoort) met Synapse CI/CD-hulpprogram ma's in azure DevOps. 

In dit artikel wordt uitgelegd hoe u Azure release-pijp lijn gebruikt om de implementatie van een Synapse-werk ruimte naar meerdere omgevingen te automatiseren.

## <a name="prerequisites"></a>Vereisten

-   De werk ruimte die wordt gebruikt voor ontwikkeling is geconfigureerd met een Git-opslag plaats in Studio. Zie [broncode beheer in Synapse Studio](source-control.md).
-   Er is een Azure DevOps-project voor bereid voor het uitvoeren van een release pijplijn.

## <a name="set-up-a-release-pipelines"></a>Een release pijplijn instellen

1.  Open in [Azure DevOps](https://dev.azure.com/)het project dat is gemaakt voor de release.

1.  Selecteer aan de linkerkant van de pagina **pijp lijnen** en selecteer vervolgens **releases**.

    ![Selecteer pijp lijnen, releases](media/create-release-1.png)

1.  Selecteer **nieuwe pijp lijn** of, als u bestaande pijp lijnen hebt, selecteert u **Nieuw** en vervolgens **nieuwe release pijplijn**.

1.  Selecteer de **lege taak** sjabloon.

    ![Lege taak selecteren](media/create-release-select-empty.png)

1.  Voer in het vak **naam fase** de naam van uw omgeving in.

1.  Selecteer **artefact toevoegen** en selecteer vervolgens de Git-opslag plaats die is geconfigureerd met uw Development Synapse Studio. Selecteer de Git-opslag plaats die u hebt gebruikt voor het beheren van ARM-sjabloon van Pools en werk ruimte. Als u GitHub als bron gebruikt, moet u een service verbinding maken voor uw GitHub-account en opslag plaatsen. Voor meer informatie over [service verbinding](/azure/devops/pipelines/library/service-endpoints) 

    ![Publish branch toevoegen](media/release-creation-github.png)

1.  Selecteer de tak van ARM-sjabloon voor het bijwerken van resources. Selecteer **laatste uit standaard vertakking** voor de **standaard versie**.

    ![ARM-sjabloon toevoegen](media/release-creation-arm-branch.png)

1.  Selecteer de [vertakking publiceren](source-control.md#configure-publishing-settings) van de opslag plaats voor de **standaard vertakking**. Deze publicatie vertakking is standaard `workspace_publish` . Selecteer **laatste uit standaard vertakking** voor de **standaard versie**.

    ![Een artefact toevoegen](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>Een taak fase instellen voor een ARM-resource maken en bijwerken 

Een Azure Resource Manager implementatie taak toevoegen om resources, waaronder werk ruimte en Pools, te maken of bij te werken:

1. Selecteer in de weer gave fase de optie **fase taken weer geven**.

    ![Fase weergave](media/release-creation-stage-view.png)

1. Een nieuwe taak maken. Zoek naar **arm-sjabloon implementatie** en selecteer **toevoegen**.

1. Selecteer in de implementatie taak het abonnement, de resource groep en de locatie voor de doel werkruimte. Geef indien nodig referenties op.

1. Selecteer in de lijst **actie** de optie **resource groep maken of bijwerken**.

1. Selecteer de knop met het weglatings teken (**...**) naast het vak **sjabloon** . Bladeren naar de Azure Resource Manager-sjabloon van uw doel werkruimte

1. Selecteren **...** Naast het vak **sjabloon parameters** om het parameter bestand te kiezen.

1. Selecteren **...** Naast het vak **sjabloon parameters negeren** en voer de gewenste parameter waarden in voor de doel werkruimte. 

1. Selecteer **Incrementeel** voor de **implementatie modus**.
    
    ![werk ruimte en groepen implementeren](media/pools-resource-deploy.png)

1. Beschrijving Voeg **Azure PowerShell** toe voor de toewijzing van werk ruimte-rollen toekennen en bijwerken. Als u release pijplijn gebruikt voor het maken van een Synapse-werk ruimte, wordt de service-principal van de pijp lijn toegevoegd als standaard-werkruimte beheerder. U kunt Power shell uitvoeren om andere accounts toegang tot de werk ruimte te verlenen. 
    
    ![machtiging verlenen](media/release-creation-grant-permission.png)

 > [!WARNING]
> In de volledige implementatie modus worden resources die in de resource groep aanwezig zijn, maar niet opgegeven in de nieuwe resource manager-sjabloon, **verwijderd**. Raadpleeg [Azure Resource Manager-implementatie modi](../../azure-resource-manager/templates/deployment-modes.md) voor meer informatie

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>Een fase taak voor de implementatie van artefacten instellen 

Gebruik de [implementatie uitbreiding Synapse werk ruimte](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) om andere items in Synapse-werk ruimte te implementeren, zoals DATASET, SQL script, notebook, Spark-taak definitie, gegevens stroom, pijp lijn, gekoppelde service, referenties en IR (Integration runtime).  

1. Zoek en down load de uitbrei ding van **Azure DevOps Marketplace**(https://marketplace.visualstudio.com/azuredevops) 

     ![Extensie ophalen](media/get-extension-from-market.png)

1. Selecteer een organisatie om de extensie te installeren. 

     ![De extensie installeren](media/install-extension.png)

1. Zorg ervoor dat de service-principal van de Azure DevOps-pijp lijn de machtiging van het abonnement heeft gekregen en ook is toegewezen als werkruimte beheerder voor de doel werkruimte. 

1. Een nieuwe taak maken. Zoek naar de implementatie van de **Synapse-werk ruimte** en selecteer vervolgens **toevoegen**.

     ![Extensie toevoegen](media/add-extension-task.png)

1.  Selecteer in de taak **...** Naast het vak **sjabloon** om het sjabloon bestand te kiezen.

1. Selecteren **...** Naast het vak **sjabloon parameters** om het parameter bestand te kiezen.

1. Selecteer de verbinding, de resource groep en de naam van de doel werkruimte. 

1. Selecteren **...** Naast het vak **sjabloon parameters negeren** en voer de gewenste parameter waarden in voor de doel werkruimte. 

    ![Implementatie van Synapse-werk ruimte](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> In de CI/CD-scenario's moet het type Integration runtime (IR) in verschillende omgevingen hetzelfde zijn. Als u bijvoorbeeld een zelf-hostende IR in de ontwikkel omgeving hebt, moet dezelfde IR ook van het type zelf-hostend zijn in andere omgevingen, zoals testen en productie. En als u integratie-runtime in meerdere fasen deelt, moet u de Integration Runtimes als gekoppelde zelf-hostende in alle omgevingen configureren, zoals ontwikkeling, testen en productie.

## <a name="create-release-for-deployment"></a>Release maken voor implementatie 

Nadat u alle wijzigingen hebt opgeslagen, kunt u **release maken** selecteren om hand matig een release te maken. Zie [Azure DevOps release triggers](/azure/devops/pipelines/release/triggers) voor het automatiseren van het maken van releases

   ![Selecteer release maken](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>Aangepaste para meters van de werkruimte sjabloon gebruiken 

U gebruikt automatische CI/CD en u wilt enkele eigenschappen wijzigen tijdens de implementatie, maar de eigenschappen zijn niet standaard ingesteld op para meters. In dit geval kunt u de standaard parameter sjabloon onderdrukken.

Als u de standaard parameter sjabloon wilt overschrijven, moet u een aangepaste parameter sjabloon maken, een bestand met de naam **template-parameters-definition.js** in de hoofdmap van uw Git-samenwerkings vertakking. U moet die exacte bestands naam gebruiken. Wanneer u publiceert vanuit de samenwerkings vertakking, wordt dit bestand door Synapse-werk ruimte gelezen en wordt de configuratie voor het genereren van de para meters gebruikt. Als er geen bestand wordt gevonden, wordt de standaard parameter sjabloon gebruikt.

### <a name="custom-parameter-syntax"></a>Syntaxis van aangepaste para meter

Hier volgen enkele richt lijnen voor het maken van het bestand met aangepaste para meters:

* Voer het pad naar de eigenschap in onder het relevante entiteits type.
* Als u een eigenschaps naam instelt `*` , geeft u aan dat u alle eigenschappen daaronder wilt para meters (alleen naar het eerste niveau, niet recursief). U kunt ook uitzonde ringen voor deze configuratie opgeven.
* Als u de waarde van een eigenschap instelt als een teken reeks, geeft u aan dat u de eigenschap wilt para meters. Gebruik de indeling `<action>:<name>:<stype>`.
   *  `<action>` Dit kan een van de volgende tekens zijn:
      * `=` houdt in dat de huidige waarde wordt ingesteld als de standaard waarde voor de para meter.
      * `-` houdt in dat de standaard waarde voor de para meter niet wordt bewaard.
      * `|` is een speciaal geval voor geheimen van Azure Key Vault voor verbindings reeksen of sleutels.
   * `<name>` is de naam van de para meter. Als deze leeg is, wordt de naam van de eigenschap gebruikt. Als de waarde begint met een `-` teken, wordt de naam Inge kort. Zo wordt bijvoorbeeld `AzureStorage1_properties_typeProperties_connectionString` Inge kort tot `AzureStorage1_connectionString` .
   * `<stype>` is het type para meter. Als `<stype>` deze leeg is, is het standaard type `string` . Ondersteunde waarden: `string` , `securestring` , `int` , `bool` , `object` `secureobject` en `array` .
* Het opgeven van een matrix in het bestand geeft aan dat de overeenkomende eigenschap in de sjabloon een matrix is. Synapse doorloopt alle objecten in de matrix met behulp van de definitie die is opgegeven. Het tweede object, een teken reeks, wordt de naam van de eigenschap, die wordt gebruikt als de naam voor de para meter voor elke iteratie.
* Een definitie kan niet specifiek zijn voor een resource-exemplaar. Elke wille keurige definitie is van toepassing op alle resources van dat type.
* Standaard zijn alle beveiligde teken reeksen, zoals Key Vault geheimen en beveiligde teken reeksen, zoals verbindings reeksen, sleutels en tokens, para meters.

### <a name="parameter-template-definition-samples"></a>Voor beelden van parameter sjabloon definitie 

Hier volgt een voor beeld van hoe een definitie van een parameter sjabloon eruitziet:

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Hier volgt een uitleg van de manier waarop de vorige sjabloon is samengesteld, onderverdeeld op resource type.

#### <a name="notebooks"></a>Notebooks 

* Een eigenschap in het pad `properties/bigDataPool/referenceName` is para meters met de standaard waarde. U kunt gekoppelde Spark-groep para meters voor elk notebook-bestand. 

#### <a name="sql-scripts"></a>SQL-scripts 

* Eigenschappen (poolnaam en databasenaam) in het pad `properties/content/currentConnection` zijn als teken reeksen in de vorm van para meters zonder de standaard waarden in de sjabloon. 

#### <a name="pipelines"></a>Pipelines

* Een eigenschap in het pad `activities/typeProperties/waitTimeInSeconds` is para meters. Alle activiteiten in een pijp lijn met de naam eigenschap `waitTimeInSeconds` (bijvoorbeeld de `Wait` activiteit) worden als een getal met een standaard naam vastgelegd. Maar heeft geen standaard waarde in de Resource Manager-sjabloon. Het is een verplichte invoer tijdens de implementatie van Resource Manager.
* Een eigenschap met de naam `headers` (bijvoorbeeld in een `Web` activiteit) is een para meter van het type `object` (object). Het heeft een standaard waarde. Dit is dezelfde waarde als die van de bron-Factory.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Alle eigenschappen onder het pad `typeProperties` zijn para meters met hun respectievelijke standaard waarden. Er zijn bijvoorbeeld twee eigenschappen onder type- `IntegrationRuntimes` Eigenschappen: `computeProperties` en `ssisProperties` . Beide eigenschaps typen worden gemaakt met hun respectievelijke standaard waarden en typen (object).

#### <a name="triggers"></a>Triggers

* Onder `typeProperties` zijn twee eigenschappen para meters. De eerste is `maxConcurrency` , die is opgegeven om een standaard waarde te hebben en van het type is `string` . Deze heeft de standaard parameter naam `<entityName>_properties_typeProperties_maxConcurrency` .
* De `recurrence` eigenschap is ook para meters. Hieronder worden alle eigenschappen op dat niveau opgegeven om para meters te worden ingesteld als teken reeksen, met standaard waarden en parameter namen. Een uitzonde ring is de `interval` eigenschap, die als type wordt para meter `int` . De parameter naam is met een achtervoegsel `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . Op dezelfde manier `freq` is de eigenschap een teken reeks en wordt de para meter als teken reeks. De `freq` eigenschap is echter para meters zonder standaard waarde. De naam is inge kort en achtervoegsel. Bijvoorbeeld `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Gekoppelde services zijn uniek. Omdat gekoppelde services en gegevens sets een breed scala van typen hebben, kunt u type-specifieke aanpassing opgeven. In dit voor beeld wordt voor alle gekoppelde services van `AzureDataLakeStore` het type een specifieke sjabloon toegepast. Voor alle andere (via `*` ) wordt een andere sjabloon toegepast.
* De `connectionString` eigenschap wordt als waarde para meters `securestring` . Er is geen standaard waarde. Er wordt een kortere parameter naam gebruikt die is gekoppeld aan `connectionString` .
* De eigenschap `secretAccessKey` gebeurt als een `AzureKeyVaultSecret` (bijvoorbeeld in een gekoppelde service van Amazon S3). De para meter wordt automatisch ingesteld als een Azure Key Vault geheim en opgehaald uit de geconfigureerde sleutel kluis. U kunt ook de sleutel kluis zelf para meters.

#### <a name="datasets"></a>Gegevenssets

* Hoewel het type-specifieke aanpassing beschikbaar is voor gegevens sets, kunt u een configuratie opgeven zonder dat u expliciet een configuratie op een niveau hoeft te maken \* . In het vorige voor beeld zijn alle eigenschappen van de gegevensset onder `typeProperties` para meters.


## <a name="best-practices-for-cicd"></a>Aanbevolen procedures voor CI/CD

Als u gebruik wilt maken van Git-integratie met uw Synapse-werk ruimte en een CI/CD-pijp lijn hebt die uw wijzigingen van de ontwikkeling naar de test verplaatst en vervolgens naar productie, raden we u aan deze aanbevolen procedures te volgen:

-   **Git-integratie**. Configureer alleen uw ontwikkel Synapse-werk ruimte met git-integratie. Wijzigingen in werk ruimten voor testen en productie worden geïmplementeerd via CI/CD en beschikken niet over git-integratie.
-   **Groep voorbereiden voordat artefacten worden gemigreerd**. Als er een SQL-script of notitie blok is gekoppeld aan Pools in de werk ruimte ontwikkeling, worden dezelfde naam van Pools in verschillende omgevingen verwacht. 
-   **Infra structuur als code (IaC)**. Beheer van de infra structuur (netwerken, virtuele machines, load balancers en verbindings topologie) in een beschrijvende model, gebruik dezelfde versie als DevOps-team voor de bron code. 
-   **Andere**. Zie [Aanbevolen procedures voor ADF-artefacten](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>Problemen met de implementatie van artefacten oplossen 

### <a name="use-the-synapse-workspace-deployment-task"></a>De implementatie taak voor de Synapse-werk ruimte gebruiken

In Synapse zijn er een aantal artefacten die geen ARM-bronnen zijn. Dit wijkt af van Azure Data Factory. De implementatie taak voor de ARM-sjabloon werkt niet goed voor het implementeren van Synapse-artefacten
 
### <a name="unexpected-token-error-in-release"></a>Onverwachte token fout in de release

Als uw parameter bestand parameter waarden bevat die niet worden voorafgegaan, kan de release pijp lijn het bestand niet parseren en wordt de fout ' onverwacht token ' gegenereerd. We raden u aan para meters te overschrijven of Azure-sleutel kluis te gebruiken om parameter waarden op te halen. U kunt ook dubbele escape tekens gebruiken als tijdelijke oplossing.
