---
title: Continue integratie en levering in Azure Data Factory
description: Leer hoe u doorlopende integratie en levering kunt gebruiken om Data Factory pijp lijnen van de ene omgeving (ontwikkeling, test, productie) naar een andere te verplaatsen.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: b31931af7b8d1442a66333622a23d017ab7fb5a9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658686"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Continue integratie en levering in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Overzicht

Continue integratie is de praktijk van het testen van elke wijziging die wordt aangebracht in uw code basis automatisch en zo snel mogelijk. Continue levering volgt de tests die optreden tijdens continue integratie en het pushen van wijzigingen in een staging-of productie systeem.

In Azure Data Factory wordt doorlopende integratie en levering (CI/CD) de mogelijkheid Data Factory pijp lijnen te verplaatsen van de ene omgeving (ontwikkeling, test, productie) naar de andere. Azure Data Factory gebruikt [Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md) voor het opslaan van de configuratie van uw verschillende ADF-entiteiten (pijp lijnen, gegevens sets, data stromen, enzovoort). Er zijn twee aanbevolen methoden om een data factory te promo veren naar een andere omgeving:

-    Geautomatiseerde implementatie met de integratie van Data Factory met [Azure-pijp lijnen](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops)
-    Een resource manager-sjabloon hand matig uploaden met behulp van Data Factory UX-integratie met Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD-levens cyclus

Hieronder volgt een voor beeld van een overzicht van de CI/CD-levens cyclus in een Azure-data factory die is geconfigureerd met Azure opslag plaatsen git. Zie [broncode beheer in azure Data Factory](source-control.md)voor meer informatie over het configureren van een Git-opslag plaats.

1.  Een ontwikkelings data factory wordt gemaakt en geconfigureerd met Azure opslag plaatsen git. Alle ontwikkel aars moeten toestemming hebben om Data Factory resources als pijp lijnen en gegevens sets te schrijven.

1.  Een ontwikkelaar [maakt een functie vertakking](source-control.md#creating-feature-branches) om een wijziging aan te brengen. Ze kunnen fouten opsporen in hun pijplijn uitvoeringen met hun meest recente wijzigingen. Zie [iteratieve ontwikkeling en fout opsporing met Azure Data Factory](iterative-development-debugging.md)voor meer informatie over het opsporen van fouten in een pijplijn uitvoering.

1.  Nadat de wijzigingen zijn aangebracht aan een ontwikkelaar, maken ze een pull-aanvraag van hun functie vertakking in de vertakking Master of collaboration om hun wijzigingen te verkrijgen die door peers worden gecontroleerd.

1.  Nadat een pull-aanvraag is goedgekeurd en wijzigingen zijn samengevoegd in de hoofd vertakking, worden de wijzigingen gepubliceerd in de ontwikkelings-Factory.

1.  Wanneer het team gereed is voor het implementeren van de wijzigingen van een test-of bedoeld (acceptatie test door gebruiker), gaat het team naar de release van Azure pipeline en implementeert de gewenste versie van de Development Factory naar bedoeld. Deze implementatie vindt plaats als onderdeel van een Azure-pijplijn taak en maakt gebruik van Resource Manager-sjabloon parameters om de juiste configuratie toe te passen.

1.  Nadat de wijzigingen zijn geverifieerd in de test-Factory, implementeert u deze in de productie-Factory met behulp van de volgende taak van de pijp lijnen release.

> [!NOTE]
> Alleen de Development Factory is gekoppeld aan een Git-opslag plaats. Aan de test-en productie fabrieken mag geen git-opslag plaats zijn gekoppeld en mag deze alleen worden bijgewerkt via een Azure DevOps-pijp lijn of via een resource beheer sjabloon.

De onderstaande afbeelding markeert de verschillende stappen van deze levens cyclus.

![Diagram van doorlopende integratie met Azure-pijp lijnen](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Continue integratie automatiseren met Azure pipelines-releases

Hieronder vindt u een hand leiding voor het instellen van een Azure pijp lijnen versie waarmee de implementatie van een data factory naar meerdere omgevingen wordt geautomatiseerd.

### <a name="requirements"></a>Vereisten

-   Een Azure-abonnement dat is gekoppeld aan Visual Studio Team Foundation Server of Azure opslag plaatsen dat gebruikmaakt van het [Azure Resource Manager service-eind punt](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Een data factory dat is geconfigureerd met Azure opslag plaatsen Git-integratie.

-   Een [Azure-sleutel kluis](https://azure.microsoft.com/services/key-vault/) die de geheimen voor elke omgeving bevat.

### <a name="set-up-an-azure-pipelines-release"></a>Een Azure pipelines-release instellen

1.  Open in [Azure DevOps](https://dev.azure.com/)het project dat is geconfigureerd met uw Data Factory.

1.  Selecteer aan de linkerkant van de pagina **pijp lijnen** en selecteer vervolgens **releases**.

    ![Selecteer pijp lijnen, releases](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selecteer **nieuwe pijp lijn** of, als u bestaande pijp lijnen hebt, selecteert u **Nieuw** en vervolgens **nieuwe release pijplijn**.

1.  Selecteer de **lege taak** sjabloon.

    ![Lege taak selecteren](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Voer in het vak **naam fase** de naam van uw omgeving in.

1.  Selecteer **artefact toevoegen** en selecteer vervolgens de Git-opslag plaats die is geconfigureerd met uw ontwikkelings Data Factory. Selecteer de [vertakking publiceren](source-control.md#configure-publishing-settings) van de opslag plaats voor de **standaard vertakking**. Deze publicatie vertakking is standaard `adf_publish` . Selecteer **laatste uit standaard vertakking** voor de **standaard versie**.

    ![Een artefact toevoegen](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Een Azure Resource Manager implementatie taak toevoegen:

    a.  Selecteer in de weer gave fase de optie **fase taken weer geven**.

    ![Fase weergave](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Een nieuwe taak maken. Zoek naar **arm-sjabloon implementatie** en selecteer **toevoegen**.

    c.  Selecteer in de implementatie taak het abonnement, de resource groep en de locatie voor het doel data factory. Geef indien nodig referenties op.

    d.  Selecteer in de lijst **actie** de optie **resource groep maken of bijwerken**.

    e.  Selecteer de knop met het weglatings teken (**...**) naast het vak **sjabloon** . Blader naar de Azure Resource Manager sjabloon die is gegenereerd in de vertakking Publish van de geconfigureerde Git-opslag plaats. Zoek naar het bestand `ARMTemplateForFactory.json` in de <FactoryName> map van de vertakking adf_publish.

    f.  Selecteren **...** Naast het vak **sjabloon parameters** om het parameter bestand te kiezen. Zoek naar het bestand `ARMTemplateParametersForFactory.json` in de <FactoryName> map van de vertakking adf_publish.

    g.  Selecteren **...** Naast het vak **sjabloon parameters negeren** en voer de gewenste parameter waarden in voor de doel Data Factory. Voer de naam van het geheim tussen dubbele aanhalings tekens in voor de referenties die afkomstig zijn van Azure Key Vault. Als de naam van het geheim bijvoorbeeld cred1 is, voert u **$ (cred1)** in voor deze waarde.

    h. Selecteer **Incrementeel** voor de **implementatie modus**.

    > [!WARNING]
    > In de volledige implementatie modus worden resources die in de resource groep aanwezig zijn, maar niet opgegeven in de nieuwe resource manager-sjabloon, **verwijderd**. Raadpleeg [Azure Resource Manager-implementatie modi](../azure-resource-manager/templates/deployment-modes.md) voor meer informatie

    ![Data Factory Prod-implementatie](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Sla de release pijplijn op.

1. Selecteer **vrijgave maken** om een release te activeren. Zie [Azure DevOps release triggers](/azure/devops/pipelines/release/triggers?view=azure-devops) voor het automatiseren van het maken van releases

   ![Selecteer release maken](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> In de CI/CD-scenario's moet het type Integration runtime (IR) in verschillende omgevingen hetzelfde zijn. Als u bijvoorbeeld een zelf-hostende IR in de ontwikkel omgeving hebt, moet dezelfde IR ook van het type zelf-hostend zijn in andere omgevingen, zoals testen en productie. En als u integratie-runtime in meerdere fasen deelt, moet u de Integration Runtimes als gekoppelde zelf-hostende in alle omgevingen configureren, zoals ontwikkeling, testen en productie.

### <a name="get-secrets-from-azure-key-vault"></a>Geheimen van Azure Key Vault ophalen

Als u geheimen in een Azure Resource Manager sjabloon wilt door geven, raden we u aan Azure Key Vault te gebruiken met de versie van Azure pipeline.

Er zijn twee manieren om geheimen af te handelen:

1.  Voeg de geheimen toe aan het parameter bestand. Zie [Azure Key Vault gebruiken om de waarde van een beveiligde para meter door te geven tijdens de implementatie](../azure-resource-manager/templates/key-vault-parameter.md)voor meer informatie.

    Maak een kopie van het parameter bestand dat is geüpload naar de Publish-vertakking. Stel de waarden in van de para meters die u wilt ophalen van Key Vault met behulp van de volgende indeling:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Wanneer u deze methode gebruikt, wordt het geheim automatisch opgehaald uit de sleutel kluis.

    Het parameter bestand moet ook in de publicatie vertakking staan.

1. Voeg een [Azure Key Vault-taak](/azure/devops/pipelines/tasks/deploy/azure-key-vault) toe vóór de Azure Resource Manager implementatie taak die in de vorige sectie wordt beschreven:

    1.  Maak een nieuwe taak op het tabblad **taken** . Zoek naar **Azure Key Vault** en voeg deze toe.

    1.  Selecteer in de taak Key Vault het abonnement waarin u de sleutel kluis hebt gemaakt. Geef indien nodig referenties op en selecteer vervolgens de sleutel kluis.

    ![Een Key Vault taak toevoegen](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Machtigingen verlenen aan de Azure pipelines-agent

De Azure Key Vault taak kan mislukken met de fout toegang geweigerd als de juiste machtigingen niet zijn ingesteld. Down load de logboeken voor de release en zoek het. ps1-bestand dat de opdracht bevat om machtigingen te verlenen aan de Azure pipelines-agent. U kunt de opdracht rechtstreeks uitvoeren. U kunt ook de principal-ID uit het bestand kopiëren en het toegangs beleid hand matig toevoegen in de Azure Portal. `Get` en `List` zijn de mini maal vereiste machtigingen.

### <a name="updating-active-triggers"></a>Actieve triggers bijwerken

De implementatie kan mislukken als u probeert actieve triggers bij te werken. Als u actieve triggers wilt bijwerken, moet u deze hand matig stoppen en opnieuw opstarten na de implementatie. U kunt dit doen met behulp van een Azure PowerShell taak:

1.  Voeg op het tabblad **taken** van de release een **Azure PowerShell** taak toe. Kies taak versie 4. *. 

1.  Selecteer het abonnement waarin uw fabriek zich bevindt.

1.  Selecteer het **pad naar het script bestand** als het script type. Hiervoor moet u het Power shell-script opslaan in uw opslag plaats. Het volgende Power shell-script kan worden gebruikt om triggers te stoppen:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

U kunt vergelijk bare stappen uitvoeren (met de `Start-AzDataFactoryV2Trigger` functie) om de triggers na de implementatie opnieuw te starten.

Het data factory-team heeft een voor beeld van een [script voor voorafgaande en na de implementatie](#script) opgesteld, onder aan dit artikel. 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>Een resource manager-sjabloon hand matig promo veren voor elke omgeving

1. Selecteer in de lijst **arm-sjabloon** de optie **arm-sjabloon exporteren** om de Resource Manager-sjabloon voor uw Data Factory te exporteren in de ontwikkel omgeving.

   ![Een resource manager-sjabloon exporteren](media/continuous-integration-deployment/continuous-integration-image1.png)

1. Selecteer in uw test-en productie gegevens fabrieken de optie **arm-sjabloon importeren**. Met deze actie gaat u naar de Azure Portal, waar u de geëxporteerde sjabloon kunt importeren. Selecteer **uw eigen sjabloon bouwen in de editor** om de Resource Manager-sjabloon editor te openen.

   ![Uw eigen sjabloon bouwen](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Selecteer **bestand laden** en selecteer vervolgens de gegenereerde Resource Manager-sjabloon. Dit is de **arm_template.jsvoor** het bestand dat is opgeslagen in het zip-bestand dat u in stap 1 hebt geëxporteerd.

   ![Sjabloon bewerken](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. Voer in de sectie instellingen de configuratie waarden in, zoals gekoppelde service referenties. Wanneer u klaar bent, selecteert u **kopen** om de Resource Manager-sjabloon te implementeren.

   ![De sectie instellingen](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Aangepaste parameters gebruiken met Resource Manager-sjabloon

Als uw Development Factory een bijbehorende Git-opslag plaats heeft, kunt u de standaard para meters voor Resource Manager-sjablonen van de Resource Manager-sjabloon die is gegenereerd door het publiceren of exporteren van de sjabloon negeren. In deze scenario's wilt u mogelijk de standaard sjabloon parameterisering overschrijven:

* U gebruikt automatische CI/CD en u wilt enkele eigenschappen wijzigen tijdens de implementatie van Resource Manager, maar de eigenschappen zijn niet standaard ingesteld op para meters.
* Uw fabriek is zo groot dat de standaard Resource Manager-sjabloon ongeldig is omdat deze meer dan de Maxi maal toegestane para meters (256) heeft.

Als u de standaard sjabloon voor parameterisering wilt overschrijven, gaat u naar de beheer hub en selecteert u **parameterisering-sjabloon** in het gedeelte bron beheer. Selecteer **sjabloon bewerken** om de parameterisering-sjabloon code-editor te openen. 

![Aangepaste para meters beheren](media/author-management-hub/management-hub-custom-parameters.png)

Als u een aangepaste parameterisering-sjabloon maakt, maakt u een bestand met de naam **arm-template-parameters-definition.js** in de hoofdmap van uw Git-vertakking. U moet die exacte bestands naam gebruiken.

![Bestand met aangepaste para meters](media/continuous-integration-deployment/custom-parameters.png)

Wanneer u publiceert vanuit de collaboration Branch, wordt dit bestand door Data Factory gelezen en wordt de configuratie gebruikt om te genereren welke eigenschappen para meters ophalen. Als er geen bestand wordt gevonden, wordt de standaard sjabloon gebruikt.

Wanneer u een resource manager-sjabloon exporteert, wordt dit bestand door Data Factory gelezen van de vertakking waaraan u momenteel werkt, niet met de vertakking voor samen werking. U kunt het bestand maken of bewerken vanuit een persoonlijke vertakking, waar u uw wijzigingen kunt testen door **arm-sjabloon exporteren** te selecteren in de gebruikers interface. U kunt het bestand vervolgens samen voegen in de vertakking voor samen werking.

> [!NOTE]
> Een aangepaste parameterisering-sjabloon wijzigt niet de limiet van de ARM-sjabloon parameters van 256. Hiermee kunt u het aantal geparametriseerde eigenschappen kiezen en verlagen.

### <a name="custom-parameter-syntax"></a>Syntaxis van aangepaste para meter

Hieronder vindt u enkele richt lijnen die u moet volgen wanneer u het bestand met aangepaste para meters maakt **arm-template-parameters-definition.jsop**. Het bestand bestaat uit een sectie voor elk entiteits type: trigger, pijp lijn, gekoppelde service, gegevensset, Integration runtime en gegevens stroom.

* Voer het pad naar de eigenschap in onder het relevante entiteits type.
* Als u een eigenschaps naam instelt `*` , geeft u aan dat u alle eigenschappen daaronder wilt para meters (alleen naar het eerste niveau, niet recursief). U kunt ook uitzonde ringen voor deze configuratie opgeven.
* Als u de waarde van een eigenschap instelt als een teken reeks, geeft u aan dat u de eigenschap wilt para meters. Gebruik de indeling `<action>:<name>:<stype>`.
   *  `<action>` Dit kan een van de volgende tekens zijn:
      * `=` houdt in dat de huidige waarde wordt ingesteld als de standaard waarde voor de para meter.
      * `-` houdt in dat de standaard waarde voor de para meter niet wordt bewaard.
      * `|` is een speciaal geval voor geheimen van Azure Key Vault voor verbindings reeksen of sleutels.
   * `<name>` is de naam van de para meter. Als deze leeg is, wordt de naam van de eigenschap gebruikt. Als de waarde begint met een `-` teken, wordt de naam Inge kort. Zo wordt bijvoorbeeld `AzureStorage1_properties_typeProperties_connectionString` Inge kort tot `AzureStorage1_connectionString` .
   * `<stype>` is het type para meter. Als `<stype>` deze leeg is, is het standaard type `string` . Ondersteunde waarden: `string` , `bool` , `number` , `object` en `securestring` .
* Het opgeven van een matrix in het definitie bestand geeft aan dat de overeenkomende eigenschap in de sjabloon een matrix is. Data Factory doorloopt alle objecten in de matrix door gebruik te maken van de definitie die is opgegeven in het object Integration runtime van de matrix. Het tweede object, een teken reeks, wordt de naam van de eigenschap, die wordt gebruikt als de naam voor de para meter voor elke iteratie.
* Een definitie kan niet specifiek zijn voor een resource-exemplaar. Elke wille keurige definitie is van toepassing op alle resources van dat type.
* Standaard zijn alle beveiligde teken reeksen, zoals Key Vault geheimen en beveiligde teken reeksen, zoals verbindings reeksen, sleutels en tokens, para meters.
 
### <a name="sample-parameterization-template"></a>Voor beeld van parameterisering-sjabloon

Hier volgt een voor beeld van hoe een parameterisering-sjabloon eruit kan zien:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
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
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Hier volgt een uitleg van de manier waarop de vorige sjabloon is samengesteld, onderverdeeld op resource type.

#### <a name="pipelines"></a>Pipelines
    
* Een eigenschap in het pad `activities/typeProperties/waitTimeInSeconds` is para meters. Alle activiteiten in een pijp lijn met de naam eigenschap `waitTimeInSeconds` (bijvoorbeeld de `Wait` activiteit) worden als een getal met een standaard naam vastgelegd. Maar heeft geen standaard waarde in de Resource Manager-sjabloon. Het is een verplichte invoer tijdens de implementatie van Resource Manager.
* Op dezelfde manier is een eigenschap `headers` (bijvoorbeeld in een `Web` activiteit) para meters van het type `object` (JObject). Het heeft een standaard waarde. Dit is dezelfde waarde als die van de bron-Factory.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Alle eigenschappen onder het pad `typeProperties` zijn para meters met hun respectievelijke standaard waarden. Er zijn bijvoorbeeld twee eigenschappen onder type- `IntegrationRuntimes` Eigenschappen: `computeProperties` en `ssisProperties` . Beide eigenschaps typen worden gemaakt met hun respectievelijke standaard waarden en typen (object).

#### <a name="triggers"></a>Triggers

* Onder `typeProperties` zijn twee eigenschappen para meters. De eerste is `maxConcurrency` , die is opgegeven om een standaard waarde te hebben en van het type is `string` . Deze heeft de standaard parameter naam `<entityName>_properties_typeProperties_maxConcurrency` .
* De `recurrence` eigenschap is ook para meters. Hieronder worden alle eigenschappen op dat niveau opgegeven om para meters te worden ingesteld als teken reeksen, met standaard waarden en parameter namen. Een uitzonde ring is de `interval` eigenschap, die als type wordt para meter `number` . De parameter naam is met een achtervoegsel `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . Op dezelfde manier `freq` is de eigenschap een teken reeks en wordt de para meter als teken reeks. De `freq` eigenschap is echter para meters zonder standaard waarde. De naam is inge kort en achtervoegsel. Bijvoorbeeld `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Gekoppelde services zijn uniek. Omdat gekoppelde services en gegevens sets een breed scala van typen hebben, kunt u type-specifieke aanpassing opgeven. In dit voor beeld wordt voor alle gekoppelde services van `AzureDataLakeStore` het type een specifieke sjabloon toegepast. Voor alle andere (via `*` ) wordt een andere sjabloon toegepast.
* De `connectionString` eigenschap wordt als waarde para meters `securestring` . Er is geen standaard waarde. Er wordt een kortere parameter naam gebruikt die is gekoppeld aan `connectionString` .
* De eigenschap `secretAccessKey` gebeurt als een `AzureKeyVaultSecret` (bijvoorbeeld in een gekoppelde service van Amazon S3). De para meter wordt automatisch ingesteld als een Azure Key Vault geheim en opgehaald uit de geconfigureerde sleutel kluis. U kunt ook de sleutel kluis zelf para meters.

#### <a name="datasets"></a>Gegevenssets

* Hoewel het type-specifieke aanpassing beschikbaar is voor gegevens sets, kunt u een configuratie opgeven zonder dat u expliciet een configuratie op een niveau hoeft te maken \* . In het vorige voor beeld zijn alle eigenschappen van de gegevensset onder `typeProperties` para meters.

### <a name="default-parameterization-template"></a>Standaard parameterisering-sjabloon

Hieronder ziet u de huidige standaard sjabloon parameterisering. Als u slechts een paar para meters wilt toevoegen, is het wellicht een goed idee om deze sjabloon rechtstreeks te bewerken, omdat u de bestaande parameterisering-structuur niet kwijtraakt.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>Voor beeld: een bestaande Azure Databricks interactieve cluster-ID parameterizing

In het volgende voor beeld ziet u hoe u een enkele waarde kunt toevoegen aan de standaard sjabloon parameterisering. We willen alleen een bestaande Azure Databricks interactieve cluster-ID voor een gekoppelde Databricks-service toevoegen aan het parameter bestand. Houd er rekening mee dat dit bestand hetzelfde is als het vorige bestand, met uitzonde ring van het toevoegen van `existingClusterId` onder het veld eigenschappen van `Microsoft.DataFactory/factories/linkedServices` .

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Gekoppelde Resource Manager-sjablonen

Als u CI/CD voor uw gegevens fabrieken hebt ingesteld, kunt u de limieten van de Azure Resource Manager-sjabloon overschrijden, omdat uw fabriek groter groeit. Een limiet is bijvoorbeeld het maximum aantal resources in een resource manager-sjabloon. Data Factory nu gekoppelde Resource Manager-sjablonen genereert om grote fabrieken te kunnen voorzien van de volledige Resource Manager-sjabloon voor een fabriek. Met deze functie wordt de gehele nettolading van de fabriek onderverdeeld in verschillende bestanden, zodat u niet beperkt bent door de limieten.

Als u Git hebt geconfigureerd, worden de gekoppelde sjablonen gegenereerd en opgeslagen in de volledige Resource Manager-sjablonen in de adf_publish vertakking in een nieuwe map met de naam linkedTemplates:

![Map gekoppelde Resource Manager-sjablonen](media/continuous-integration-deployment/linked-resource-manager-templates.png)

De gekoppelde Resource Manager-sjablonen bestaan gewoonlijk uit een hoofd sjabloon en een reeks onderliggende sjablonen die zijn gekoppeld aan de Master. De bovenliggende sjabloon heet ArmTemplate_master.jsop, en de onderliggende sjablonen worden benoemd met het patroon ArmTemplate_0.jsop, ArmTemplate_1.jsop, enzovoort. 

Als u gekoppelde sjablonen wilt gebruiken in plaats van de volledige Resource Manager-sjabloon, moet u uw CI/CD-taak bijwerken zodat deze verwijst naar ArmTemplate_master.jsin plaats van ArmTemplateForFactory.jsop (de volledige Resource Manager-sjabloon). Resource Manager vereist ook dat u de gekoppelde sjablonen uploadt naar een opslag account, zodat Azure deze kan openen tijdens de implementatie. Zie [gekoppelde Resource Manager-sjablonen implementeren met VSTS](/archive/blogs/najib/deploying-linked-arm-templates-with-vsts)voor meer informatie.

Vergeet niet om de Data Factory scripts in uw CI/CD-pijp lijn vóór en na de implementatie taak toe te voegen.

Als u geen git hebt geconfigureerd, kunt u de gekoppelde sjablonen openen via **arm-sjabloon exporteren** in de lijst met **arm** -sjablonen.

## <a name="hotfix-production-environment"></a>Hotfix-productie omgeving

Als u een Factory implementeert voor productie en beseft dat er een fout is die direct moet worden opgelost, maar u niet de huidige collaboration Branch kunt implementeren, moet u mogelijk een hotfix implementeren. Deze aanpak is net zo bekend als snelle herstel techniek of QFE.

1.    In azure DevOps gaat u naar de release die is geïmplementeerd voor productie. Zoek de laatste door voering die is geïmplementeerd.

2.    Haal de ID van de samenwerkings vertakking op uit het bericht door voeren.

3.    Maak een nieuwe hotfix vertakking van die doorvoer.

4.    Ga naar de Azure Data Factory UX en schakel over naar de hotfix-vertakking.

5.    Los de fout op met behulp van de Azure Data Factory UX. Test uw wijzigingen.

6.    Nadat de oplossing is gecontroleerd, selecteert u **arm-sjabloon exporteren** om de hotfix Resource Manager-sjabloon op te halen.

7.    Controleer deze build hand matig in de vertakking adf_publish.

8.    Als u uw release pijplijn hebt geconfigureerd om automatisch te activeren op basis van adf_publish check-ins, wordt een nieuwe release automatisch gestart. U kunt ook hand matig een release in de wachtrij plaatsen.

9.    Implementeer de hotfix-release voor de test-en productie fabrieken. Deze release bevat de vorige nettolading van de productie plus de oplossing die u in stap 5 hebt gemaakt.

10.   Voeg de wijzigingen van de hotfix toe aan de ontwikkelings vertakking, zodat latere versies niet dezelfde fout bevatten.

Zie de video onder een gedetailleerde video zelf studie over het dynamisch oplossen van uw omgevingen. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4I7fi]

## <a name="best-practices-for-cicd"></a>Aanbevolen procedures voor CI/CD

Als u gebruik wilt maken van Git-integratie met uw data factory en een CI/CD-pijp lijn hebt die uw wijzigingen van de ontwikkeling naar de test verplaatst en vervolgens naar productie, raden we u aan deze aanbevolen procedures te volgen:

-   **Git-integratie**. Configureer alleen uw ontwikkel data factory met git-integratie. Wijzigingen in testen en productie worden geïmplementeerd via CI/CD en beschikken niet over git-integratie.

-   **Script vóór en na de implementatie**. Vóór de implementatie stap van resource manager in CI/CD moet u bepaalde taken uitvoeren, zoals Triggers stoppen en opnieuw starten en het opschonen uitvoeren. Het is raadzaam om Power shell-scripts voor en na de implementatie taak te gebruiken. Zie [actieve triggers bijwerken](#updating-active-triggers)voor meer informatie. Het data factory-team heeft [een script ontvangen](#script) dat zich onder aan deze pagina bevindt.

-   **Integration Runtimes en delen**. Integration Runtimes veranderen niet vaak en zijn vergelijkbaar in alle fasen van uw CI/CD. Data Factory verwacht dat u dezelfde naam en hetzelfde type Integration runtime in alle fasen van CI/CD hebt. Als u integratie-Runtimes in alle fasen wilt delen, kunt u overwegen een ternaire fabriek alleen te gebruiken om de gedeelde integratie-runtime te bevatten. U kunt deze gedeelde Factory in al uw omgevingen gebruiken als het type gekoppelde integratie runtime.

-   **Beheerde implementatie van een privé-eind punt**. Als er al een persoonlijk eind punt in een Factory bestaat en u probeert een ARM-sjabloon te implementeren dat een persoonlijk eind punt met dezelfde naam, maar met gewijzigde eigenschappen bevat, mislukt de implementatie. Met andere woorden, u kunt een persoonlijk eind punt implementeren op dezelfde wijze als de eigenschappen die al in de fabriek aanwezig zijn. Als een van de eigenschappen verschilt tussen omgevingen, kunt u deze overschrijven door parameterizing die eigenschap en de desbetreffende waarde op te geven tijdens de implementatie.

-   **Key Vault**. Wanneer u gekoppelde services gebruikt waarvan de verbindings gegevens zijn opgeslagen in Azure Key Vault, wordt aanbevolen om afzonderlijke sleutel kluizen voor verschillende omgevingen te houden. U kunt ook afzonderlijke machtigings niveaus configureren voor elke sleutel kluis. Het is bijvoorbeeld mogelijk dat uw team leden geen machtigingen voor productie geheimen mogen hebben. Als u deze aanpak volgt, wordt u aangeraden dezelfde geheime namen in alle fasen te houden. Als u dezelfde geheime namen behoudt, hoeft u niet elk connection string te para metersen in de CI/CD-omgevingen, omdat de naam van de sleutel kluis een afzonderlijke para meter is.

-  **Resource naamgeving** Vanwege de ARM-sjabloon beperkingen kunnen problemen in de implementatie optreden als uw resources spaties in de naam bevatten. Het Azure Data Factory Team raadt aan om ' _ ' of '-' tekens te gebruiken in plaats van ruimten voor resources. Zo zou ' Pipeline_1 ' een voor keurige naam zijn via ' pijp lijn 1 '.

## <a name="unsupported-features"></a>Niet-ondersteunde functies

- Data Factory is standaard niet toegestaan Cherry-orderverzamelen van door voeringen of selectief publiceren van resources. De publicatie bevat alle wijzigingen die zijn aangebracht in de data factory.

    - Data Factory-entiteiten zijn afhankelijk van elkaar. Triggers zijn bijvoorbeeld afhankelijk van pijp lijnen, en pijp lijnen zijn afhankelijk van gegevens sets en andere pijp lijnen. Selectief publiceren van een subset van bronnen kan leiden tot onverwacht gedrag en fouten.
    - In zeldzame gevallen wanneer u selectief publiceren nodig hebt, kunt u overwegen om een hotfix te gebruiken. Zie [hotfix production environment](#hotfix-production-environment)(Engelstalig) voor meer informatie.

- Het Azure Data Factory team adviseert geen Azure RBAC-besturings elementen toe te wijzen aan afzonderlijke entiteiten (pijp lijnen, gegevens sets, enzovoort) in een data factory. Als een ontwikkelaar bijvoorbeeld toegang heeft tot een pijp lijn of een gegevensset, moeten ze toegang hebben tot alle pijp lijnen of gegevens sets in de data factory. Als u van mening bent dat u veel Azure-rollen moet implementeren in een data factory, raadpleegt u de implementatie van een tweede data factory.

-   U kunt niet publiceren vanuit privé-filialen.

-   U kunt momenteel geen projecten hosten op bitbucket.

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a> Voor beeld van een script vóór en na implementatie

Het volgende voorbeeld script kan worden gebruikt om triggers voor implementatie te stoppen en opnieuw op te starten. Het script bevat ook code voor het verwijderen van resources die zijn verwijderd. Sla het script op in een Azure DevOps Git-opslag plaats en verwijs naar een Azure PowerShell-taak met behulp van versie 4. *.

Wanneer u een script voorafgaand aan de implementatie uitvoert, moet u een variant van de volgende para meters opgeven in het veld **script argumenten** .

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Bij het uitvoeren van een script na de implementatie moet u in het veld **script argumenten** een variant van de volgende para meters opgeven.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell-taak](media/continuous-integration-deployment/continuous-integration-image11.png)

Dit is het script dat kan worden gebruikt voor de voorafgaande en na de implementatie. IT-accounts voor verwijderde resources en resource verwijzingen.

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    if ($trigger.Properties.DependsOn) {
        $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
            triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
        }
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersInTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNamesInTemplate = $triggersInTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}

$triggersDeployed = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

$triggersToStop = $triggersDeployed | Where-Object { $triggerNamesInTemplate -contains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToDelete = $triggersDeployed | Where-Object { $triggerNamesInTemplate -notcontains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToStart = $triggersInTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.name.Substring(37, $_.name.Length-40)
        TriggerType = $_.Properties.type
    }
}

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers`n"
    $triggersToStop | ForEach-Object {
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Unsubscribing" $_.Name "from events"
            $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Disabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Stopping trigger" $_.Name
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $triggersToDelete | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            if ($_.TriggerType -eq "BlobEventsTrigger") {
                Write-Host "Unsubscribing trigger" $_.Name "from events"
                $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                while ($status.Status -ne "Disabled"){
                    Start-Sleep -s 15
                    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                }
            }
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $triggersToStart | ForEach-Object { 
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Subscribing" $_.Name "to events"
            $status = Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Enabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Starting trigger" $_.Name
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
```