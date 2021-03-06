---
title: Continue integratie en levering voor Synapse-werkruimte
description: Leer hoe u continue integratie en levering kunt gebruiken om wijzigingen in de werkruimte van de ene omgeving (ontwikkeling, test, productie) in de andere te implementeren.
author: liudan66
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 833478d956560c981bd6cc3ba03b48bb602f563c
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739670"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Continue integratie en levering voor Azure Synapse werkruimte

## <a name="overview"></a>Overzicht

Continue integratie (CI) is het proces van het automatiseren van het bouwen en testen van code telkens als een teamlid wijzigingen aan het versiebeheer doorvoering. Continue implementatie (CD) is het proces voor het bouwen, testen, configureren en implementeren van meerdere test- of faseringsomgevingen naar een productieomgeving.

In een Azure Synapse Analytics verplaatst continue integratie en levering (CI/CD) alle entiteiten van de ene omgeving (ontwikkeling, test, productie) naar een andere. Als u uw werkruimte wilt promoveren naar een andere werkruimte, zijn er twee onderdelen. Gebruik eerst een [Azure Resource Manager sjabloon (ARM-sjabloon) om](../../azure-resource-manager/templates/overview.md) werkruimte-resources (pools en werkruimte) te maken of bij te werken. Migreert vervolgens artefacten (SQL-scripts, notebook, Spark-taakdefinitie, pijplijnen, gegevenssets, gegevensstromen, Azure Synapse Analytics CI/CD-hulpprogramma's in Azure DevOps). 

In dit artikel wordt beschreven hoe u een Azure DevOps-release-pijplijn gebruikt om de implementatie van een Azure Synapse in meerdere omgevingen te automatiseren.

## <a name="prerequisites"></a>Vereisten

Deze vereisten en configuraties moeten zijn geïmplementeerd om de implementatie van een Azure Synapse in meerdere omgevingen te automatiseren.

### <a name="azure-devops"></a>Azure DevOps

- Er is een Azure DevOps-project voorbereid voor het uitvoeren van de release-pijplijn.
- [Verleen alle gebruikers die de code 'Basic' incheckt toegang](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page)op organisatieniveau, zodat ze de repo kunnen zien.
- Verleen eigendomsrechten aan de Azure Synapse-repo.
- Zorg ervoor dat u een zelf-hostende Azure DevOps VM-agent hebt gemaakt of gebruik een gehoste Azure DevOps-agent.
- Machtigingen voor [het maken van een Azure Resource Manager-serviceverbinding voor de resourcegroep](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml).
- Een Azure Active Directory (Azure AD) moet de [extensie Azure DevOps Synapse Workspace Deployment Agent installeren in de Azure DevOps-organisatie](/azure/devops/marketplace/install-extension).
- Maak of benoem een bestaand serviceaccount om de pijplijn als uit te voeren. U kunt een persoonlijk toegang token gebruiken in plaats van een serviceaccount, maar uw pijplijnen werken niet nadat het gebruikersaccount is verwijderd.

### <a name="azure-active-directory"></a>Azure Active Directory

- Maak in Azure AD een service-principal voor implementatie. De synapse-werkruimte-implementatietaak biedt geen ondersteuning voor het gebruik van een beheerde identiteit in verion 1* en eerder.
- Azure AD-beheerdersrechten zijn vereist voor deze actie.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> U kunt deze vereisten automatiseren en implementeren met behulp van dezelfde pijplijn, een ARM-sjabloon of de Azure CLI, maar het proces wordt niet in dit artikel beschreven.

- De 'bronwerkruimte' die wordt gebruikt voor ontwikkeling, moet worden geconfigureerd met een Git-opslagplaats in Synapse Studio. Zie Broncodebeheer [in](source-control.md#configuration-method-2-manage-hub)Synapse Studio.

- Een lege werkruimte om in te implementeren. De lege werkruimte instellen:

  1. Maak een nieuwe Azure Synapse Analytics werkruimte.
  1. Verleen de VM-agent en de inzendersrechten van de service-principal aan de resourcegroep waarin de nieuwe werkruimte wordt gehost.
  1. Configureer in de nieuwe werkruimte de verbinding met de Git-repo niet.
  1. Zoek in Azure Portal de nieuwe werkruimte Azure Synapse Analytics werkruimte en verleen uzelf en iedereen die de Azure DevOps-pijplijn gaat uitvoeren Azure Synapse Analytics eigendomsrechten voor de werkruimte. 
  1. Voeg de Azure DevOps VM-agent en de service-principal toe aan de rol Inzender voor de werkruimte (deze moet zijn overgenomen, maar controleer of dit het is).
  1. Ga in Azure Synapse Analytics werkruimte naar **Studio**  >    >  **IAM beheren.** Voeg de Azure DevOps VM-agent en de service-principal toe aan de groep werkruimtebeheerders.
  1. Open het opslagaccount dat wordt gebruikt voor de werkruimte. Voeg in IAM de VM-agent en de service-principal toe aan de rol Inzender voor opslagblobgegevens selecteren.
  1. Maak een sleutelkluis in het ondersteuningsabonnement en zorg ervoor dat zowel de bestaande werkruimte als de nieuwe werkruimte ten minste de machtigingEN GET en LIST voor de kluis hebben.
  1. De geautomatiseerde implementatie werkt alleen als de verbindingsreeksen die zijn opgegeven in uw gekoppelde services zich in de sleutelkluis hebben.

### <a name="additional-prerequisites"></a>Aanvullende vereisten
 
 - Spark-pools en zelf-hostende integratieruntimes worden niet in een pijplijn gemaakt. Als u een gekoppelde service hebt die gebruikmaakt van een zelf-hostende Integration Runtime, maakt u deze handmatig in de nieuwe werkruimte.
 - Als u notebooks ontwikkelt en deze hebt verbonden met een Spark-pool, maakt u de Spark-pool opnieuw in de werkruimte.
 - Notebooks die zijn gekoppeld aan een Spark-pool die niet bestaat in een omgeving, worden niet geïmplementeerd.
 - De namen van de Spark-pool moeten in beide werkruimten hetzelfde zijn.
 - Noem alle databases, SQL-pools en andere resources hetzelfde in beide werkruimten.
 - Als uw inrichtende SQL-pools worden onderbroken wanneer u probeert te implementeren, kan de implementatie mislukken.

Zie CI CD in Azure Synapse Analytics [Part 4 - The Release Pipeline voor meer informatie.](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434) 


## <a name="set-up-a-release-pipeline"></a>Een release-pijplijn instellen

1.  Open [in Azure DevOps](https://dev.azure.com/)het project dat voor de release is gemaakt.

1.  Selecteer aan de linkerkant van de pagina **Pijplijnen** en selecteer vervolgens **Releases.**

    ![Selecteer Pijplijnen, Releases](media/create-release-1.png)

1.  Selecteer **Nieuwe pijplijn** of als u bestaande pijplijnen hebt, selecteert u **Nieuw** en vervolgens **Nieuwe release-pijplijn.**

1.  Selecteer de **sjabloon Lege** taak.

    ![Lege taak selecteren](media/create-release-select-empty.png)

1.  Voer in **het vak** Fasenaam de naam van uw omgeving in.

1.  Selecteer **Artefact toevoegen** en selecteer vervolgens de git-opslagplaats die is geconfigureerd met uw Synapse Studio. Selecteer de Git-opslagplaats die u hebt gebruikt voor het beheren van de ARM-sjabloon van pools en werkruimte. Als u GitHub als bron gebruikt, moet u een serviceverbinding maken voor uw GitHub-account en pull-opslagplaatsen. Voor meer informatie over [serviceverbinding](/azure/devops/pipelines/library/service-endpoints) 

    ![Publicatie-vertakking toevoegen](media/release-creation-github.png)

1.  Selecteer de vertakking van de ARM-sjabloon voor het bijwerken van resources. Voor de **standaardversie selecteert** u **Meest recent in de standaard branch**.

    ![ARM-sjabloon toevoegen](media/release-creation-arm-branch.png)

1.  Selecteer de [publicatie-vertakking](source-control.md#configure-publishing-settings) van de opslagplaats voor de **standaard branch**. Deze publicatie-vertakking is standaard `workspace_publish` . Voor de **standaardversie selecteert** u **Meest recent in de standaard branch**.

    ![Een artefact toevoegen](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-resource"></a>Een fasetaak instellen voor een ARM-sjabloon om een resource te maken en bij te werken 

Als u een ARM-sjabloon hebt om een resource te implementeren, zoals een Azure Synapse-werkruimte, Spark- en SQL-pools of een sleutelkluis, voegt u een Azure Resource Manager Deployment-taak toe om deze resources te maken of bij te werken:

1. Selecteer fasetaken weergeven in de **faseweergave.**

    ![Faseweergave](media/release-creation-stage-view.png)

1. Maak een nieuwe taak. Zoek naar **ARM-sjabloonimplementatie** en selecteer **vervolgens Toevoegen.**

1. Selecteer in de taak Implementatie het abonnement, de resourcegroep en de locatie voor de doelwerkruimte. Geef indien nodig referenties op.

1. Selecteer **resourcegroep** maken of bijwerken in **de lijst Actie.**

1. Selecteer de knop met het beletselteken (**...**) naast het **vak** Sjabloon. Blader naar de sjabloon Azure Resource Manager doelwerkruimte

1. Selecteer **...** naast het vak **Sjabloonparameters** om het parametersbestand te kiezen.

1. Selecteer **...** naast het vak **Sjabloonparameters overschrijven** en voer de gewenste parameterwaarden in voor de doelwerkruimte. 

1. Selecteer **Incrementeel** voor de **implementatiemodus.**
    
    ![werkruimte en pools implementeren](media/pools-resource-deploy.png)

1. (Optioneel) Voeg **Azure PowerShell** toe voor het verlenen en bijwerken van de roltoewijzing van de werkruimte. Als u een release-pijplijn gebruikt om een Synapse-werkruimte te maken, wordt de service-principal van de pijplijn toegevoegd als standaardwerkruimtebeheerder. U kunt PowerShell uitvoeren om andere accounts toegang te verlenen tot de werkruimte. 
    
    ![Toestemming](media/release-creation-grant-permission.png)

 > [!WARNING]
> In de implementatiemodus Voltooien worden resources verwijderd die in de resourcegroep bestaan, maar niet zijn opgegeven in de nieuwe Resource Manager **sjabloon.** Raadpleeg implementatiemodi voor meer [informatie Azure Resource Manager implementatiemodi](../../azure-resource-manager/templates/deployment-modes.md)

## <a name="set-up-a-stage-task-for-synapse-artifacts-deployment"></a>Een fasetaak instellen voor de implementatie van Synapse-artefacten 

Gebruik de [implementatie-extensie van de Synapse-werkruimte](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) om andere items in de Synapse-werkruimte te implementeren, zoals gegevensset, SQL-script, notebook, Spark-taakdefinitie, gegevensstroom, pijplijn, gekoppelde service, referenties en IR (Integration Runtime).  

1. Zoek en haal de extensie op **via Azure DevOps Marketplace**(https://marketplace.visualstudio.com/azuredevops) 

     ![Extensie op halen](media/get-extension-from-market.png)

1. Selecteer een organisatie om de extensie te installeren. 

     ![De extensie installeren](media/install-extension.png)

1. Zorg ervoor dat de service-principal van de Azure DevOps-pijplijn de machtiging van het abonnement heeft gekregen en ook is toegewezen als werkruimtebeheerder voor de doelwerkruimte. 

1. Maak een nieuwe taak. Zoek naar **synapse-werkruimte-implementatie** en selecteer **vervolgens Toevoegen.**

     ![Extensie toevoegen](media/add-extension-task.png)

1.  Selecteer in de taak **...** naast het vak **Sjabloon** om het sjabloonbestand te kiezen.

1. Selecteer **...** naast het vak **Sjabloonparameters** om het parametersbestand te kiezen.

1. Selecteer de verbinding, de resourcegroep en de naam van de doelwerkruimte. 

1. Selecteer **...** naast het vak **Sjabloonparameters** overschrijven en voer de gewenste parameterwaarden in voor de doelwerkruimte, inclusief verbindingsreeksen en accountsleutels die worden gebruikt in uw gekoppelde services. [Klik hier voor meer informatie] (https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)

    ![Synapse-werkruimte implementeren](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> In CI/CD-scenario's moet het type Integratieruntime (IR) in verschillende omgevingen hetzelfde zijn. Als u bijvoorbeeld een zelf-hostende IR in de ontwikkelomgeving hebt, moet dezelfde IR ook van het type zelf-hostend zijn in andere omgevingen, zoals test en productie. En als u integratieruntimes in meerdere fasen deelt, moet u de integratieruntimes configureren als gekoppelde zelf-hostend in alle omgevingen, zoals ontwikkeling, testen en productie.

## <a name="create-release-for-deployment"></a>Release voor implementatie maken 

Nadat u alle wijzigingen hebt op slaan, kunt u **Release maken selecteren** om handmatig een release te maken. Zie [Azure DevOps-releasetriggers](/azure/devops/pipelines/release/triggers) als u het maken van releases wilt automatiseren

   ![Selecteer Release maken](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>Aangepaste parameters van de werkruimtesjabloon gebruiken 

U gebruikt geautomatiseerde CI/CD en u wilt enkele eigenschappen wijzigen tijdens de implementatie, maar de eigenschappen worden niet standaard geparameteriseerd. In dit geval kunt u de standaardparametersjabloon overschrijven.

Als u de standaardparametersjabloon wilt overschrijven, moet u een aangepaste parametersjabloon maken, een bestand met de naam **template-parameters-definition.js** in de hoofdmap van uw git-samenwerkingstak. U moet die exacte bestandsnaam gebruiken. Wanneer u publiceert vanuit de samenwerkingsbranche, leest de Synapse-werkruimte dit bestand en gebruikt de configuratie ervan om de parameters te genereren. Als er geen bestand wordt gevonden, wordt de standaardparametersjabloon gebruikt.

### <a name="custom-parameter-syntax"></a>Aangepaste parametersyntaxis

Hier volgen enkele richtlijnen voor het maken van het aangepaste parametersbestand:

* Voer het eigenschapspad in onder het relevante entiteitstype.
* Als u de naam van een eigenschap instelt op , geeft u aan dat u alle eigenschappen daar onder wilt parameteriseren (alleen omlaag naar het eerste niveau, niet `*` recursief). U kunt ook uitzonderingen op deze configuratie geven.
* Door de waarde van een eigenschap in te stellen als een tekenreeks, geeft u aan dat u de eigenschap wilt parameteriseren. Gebruik de indeling `<action>:<name>:<stype>`.
   *  `<action>` kan een van deze tekens zijn:
      * `=` betekent dat de huidige waarde als de standaardwaarde voor de parameter moet worden gebruikt.
      * `-` betekent dat de standaardwaarde voor de parameter niet behouden.
      * `|` is een speciaal geval voor geheimen uit Azure Key Vault voor verbindingsreeksen of sleutels.
   * `<name>` is de naam van de parameter . Als deze leeg is, krijgt deze de naam van de eigenschap. Als de waarde begint met een `-` teken, wordt de naam ingekort. Wordt bijvoorbeeld `AzureStorage1_properties_typeProperties_connectionString` ingekort tot `AzureStorage1_connectionString` .
   * `<stype>` is het type parameter. Als `<stype>` leeg is, is het standaardtype `string` . Ondersteunde `string` waarden: `securestring` , , , , en `int` `bool` `object` `secureobject` `array` .
* Het opgeven van een matrix in het bestand geeft aan dat de overeenkomende eigenschap in de sjabloon een matrix is. Synapse doorkeert alle objecten in de matrix met behulp van de opgegeven definitie. Het tweede object, een tekenreeks, wordt de naam van de eigenschap, die wordt gebruikt als de naam voor de parameter voor elke iteratie.
* Een definitie kan niet specifiek zijn voor een resource-exemplaar. Elke definitie is van toepassing op alle resources van dat type.
* Standaard worden alle beveiligde tekenreeksen, Key Vault geheimen en beveiligde tekenreeksen, zoals verbindingsreeksen, sleutels en tokens, geparameteriseerd.

### <a name="parameter-template-definition-samples"></a>Voorbeelden van parametersjabloondefinitie 

Hier ziet u een voorbeeld van hoe een definitie van een parametersjabloon eruitziet:

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

Hier volgt een uitleg van hoe de voorgaande sjabloon wordt samengesteld, onderverdeeld naar resourcetype.

#### <a name="notebooks"></a>Notebooks 

* Elke eigenschap in het pad `properties/bigDataPool/referenceName` wordt geparameteriseerd met de standaardwaarde. U kunt de gekoppelde Spark-pool parameteriseren voor elk notebookbestand. 

#### <a name="sql-scripts"></a>SQL-scripts 

* Eigenschappen (poolName en databaseName) in het pad worden geparameteriseerd als tekenreeksen `properties/content/currentConnection` zonder de standaardwaarden in de sjabloon. 

#### <a name="pipelines"></a>Pipelines

* Elke eigenschap in het pad `activities/typeProperties/waitTimeInSeconds` wordt geparameteriseerd. Elke activiteit in een pijplijn met een eigenschap op codeniveau met de naam (bijvoorbeeld de activiteit) wordt geparameteriseerd als een `waitTimeInSeconds` `Wait` getal, met een standaardnaam. De sjabloon heeft echter geen standaardwaarde in de Resource Manager sjabloon. Dit is een verplichte invoer tijdens de implementatie Resource Manager implementatie.
* Op dezelfde manier wordt een eigenschap met de naam `headers` (bijvoorbeeld in een `Web` activiteit) geparameteriseerd met het type `object` (Object). Deze heeft een standaardwaarde, die dezelfde waarde is als die van de bron factory.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Alle eigenschappen onder het pad `typeProperties` worden geparameteriseerd met hun respectieve standaardwaarden. Er zijn bijvoorbeeld twee eigenschappen onder `IntegrationRuntimes` typeeigenschappen: `computeProperties` en `ssisProperties` . Beide eigenschapstypen worden gemaakt met hun respectieve standaardwaarden en -typen (Object).

#### <a name="triggers"></a>Triggers

* Onder `typeProperties` worden twee eigenschappen geparameteriseerd. De eerste is , die is opgegeven om een standaardwaarde te `maxConcurrency` hebben en van het type `string` is. Deze heeft de standaardparameternaam `<entityName>_properties_typeProperties_maxConcurrency` .
* De `recurrence` eigenschap is ook geparameteriseerd. Daar onder worden alle eigenschappen op dat niveau opgegeven om te worden geparameteriseerd als tekenreeksen, met standaardwaarden en parameternamen. Een uitzondering is de `interval` eigenschap , die is geparameteriseerd als type `int` . De parameternaam heeft het achtervoegsel `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . Op dezelfde manier `freq` is de eigenschap een tekenreeks en wordt deze geparameteriseerd als een tekenreeks. De eigenschap wordt `freq` echter geparameteriseerd zonder een standaardwaarde. De naam wordt ingekort en als achtervoegsel gebruikt. Bijvoorbeeld `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Gekoppelde services zijn uniek. Omdat gekoppelde services en gegevenssets een breed scala aan typen hebben, kunt u typespecifieke aanpassingen bieden. In dit voorbeeld wordt voor alle gekoppelde services van het type `AzureDataLakeStore` een specifieke sjabloon toegepast. Voor alle andere (via `*` ) wordt een andere sjabloon toegepast.
* De `connectionString` eigenschap wordt geparameteriseerd als een `securestring` waarde. Deze heeft geen standaardwaarde. Deze heeft een verkorte parameternaam met het achtervoegsel `connectionString` .
* De eigenschap is een (bijvoorbeeld in een `secretAccessKey` `AzureKeyVaultSecret` gekoppelde Amazon S3-service). Deze wordt automatisch geparameteriseerd als een Azure Key Vault en opgehaald uit de geconfigureerde sleutelkluis. U kunt ook de sleutelkluis zelf parameteriseren.

#### <a name="datasets"></a>Gegevenssets

* Hoewel typespecifieke aanpassingen beschikbaar zijn voor gegevenssets, kunt u configuratie bieden zonder expliciet een \* configuratie op -niveau te hebben. In het voorgaande voorbeeld worden alle eigenschappen van de gegevensset `typeProperties` onder geparameteriseerd.


## <a name="best-practices-for-cicd"></a>Best practices voor CI/CD

Als u Git-integratie met uw Azure Synapse-werkruimte gebruikt en een CI/CD-pijplijn hebt die uw wijzigingen verplaatst van ontwikkeling naar test en vervolgens naar productie, raden we u de volgende best practices aan:

-   **Git-integratie.** Configureer alleen uw ontwikkelwerkruimte Azure Synapse git-integratie. Wijzigingen in test- en productiewerkruimten worden geïmplementeerd via CI/CD en hebben geen Git-integratie nodig.
-   **Pools voorbereiden vóór de migratie van artefacten.** Als u een SQL-script of notebook hebt gekoppeld aan pools in de ontwikkelwerkruimte, wordt dezelfde naam van pools in verschillende omgevingen verwacht. 
-   **Infrastructure as Code (IaC).** Voor het beheer van infrastructuur (netwerken, virtuele machines, load balancers en verbindingstopologie) in een beschrijvend model wordt dezelfde versiebeheer gebruikt als het DevOps-team gebruikt voor broncode. 
-   **Andere**. Zie [best practices voor ADF-artefacten](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>Problemen met de implementatie van artefacten oplossen 

### <a name="use-the-azure-synapse-analytics-workspace-deployment-task"></a>De implementatietaak Azure Synapse Analytics werkruimte gebruiken

In Azure Synapse Analytics zijn er een aantal artefacten die geen ARM-resources zijn. Dit wijkt af van Azure Data Factory. De implementatietaak van de ARM-sjabloon werkt niet goed om de Azure Synapse Analytics te implementeren.
 
### <a name="unexpected-token-error-in-release"></a>Onverwachte tokenfout in release

Wanneer uw parameterbestand parameterwaarden bevat die geen escape-teken hebben, kan de release-pijplijn het bestand niet parseren en wordt de fout 'onverwacht token' gegenereerd. We raden u aan parameters te overschrijven of Azure Key Vault parameterwaarden op te halen. U kunt ook dubbele escapetekens gebruiken als tijdelijke oplossing.
