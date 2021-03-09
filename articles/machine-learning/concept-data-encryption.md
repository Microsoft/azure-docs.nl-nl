---
title: Gegevens versleuteling met Azure machine learning
titleSuffix: Azure Machine Learning
description: Meer informatie over het Azure Machine Learning van berekeningen en gegevens archieven voor gegevens versleuteling in rust en onderweg.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2020
ms.openlocfilehash: 13d5c02fbb4ae06c7a5279ab7c5d3af90c263f71
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521064"
---
# <a name="data-encryption-with-azure-machine-learning"></a>Gegevens versleuteling met Azure Machine Learning

Azure Machine Learning maakt gebruik van een aantal Azure-Services voor gegevens opslag en reken bronnen bij het trainen van modellen en het afleiden van interferentie. Elk van deze heeft hun eigen verhaal over hoe ze versleuteling bieden voor gegevens in rust en onderweg. In dit artikel vindt u meer informatie over elk van deze en wat het meest geschikt is voor uw scenario.

> [!IMPORTANT]
> Voor productie kwaliteit versleuteling tijdens de __training__ raadt micro soft aan om Azure machine learning Compute-cluster te gebruiken. Voor de versleuteling van de productie __kwaliteit tijdens de__ degradatie raadt micro soft de Azure Kubernetes-service te gebruiken.
>
> Azure Machine Learning Compute-instantie is een ontwikkel-en test omgeving. Wanneer u deze gebruikt, raden we u aan om uw bestanden, zoals notebooks en scripts, op te slaan in een bestands share. Uw gegevens moeten worden opgeslagen in een gegevens opslag.

## <a name="encryption-at-rest"></a>Versleuteling 'at rest'

> [!IMPORTANT]
> Als uw werk ruimte gevoelige gegevens bevat, kunt u het beste de [hbi_workspace vlag](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) instellen tijdens het maken van uw werk ruimte. De `hbi_workspace` vlag kan alleen worden ingesteld wanneer een werk ruimte wordt gemaakt. Het kan niet worden gewijzigd voor een bestaande werk ruimte.

De `hbi_workspace` markering bepaalt de hoeveelheid [gegevens die micro soft verzamelt voor diagnostische doel einden](#microsoft-collected-data) en maakt [extra versleuteling mogelijk in door micro soft beheerde omgevingen](../security/fundamentals/encryption-atrest.md). Daarnaast kunnen de volgende acties worden uitgevoerd:

* Hiermee wordt het versleutelen van de lokale werk schijf in uw Azure Machine Learning Compute-Cluster gestart, mits u geen eerdere clusters hebt gemaakt in dat abonnement. Anders moet u een ondersteunings ticket genereren om versleuteling van de Scratch schijf van uw reken clusters mogelijk te maken 
* De lokale scratchschijf opschonen tussen uitvoeringen
* Referenties voor uw opslag account, container register en SSH-account veilig door gegeven van de uitvoerings slaag naar uw reken clusters met behulp van uw sleutel kluis
* IP-filtering inschakelen om ervoor te zorgen dat de onderliggende batch-groepen niet kunnen worden aangeroepen door andere externe services dan AzureMachineLearningService
* Houd er rekening mee dat reken instanties niet worden ondersteund in de HBI-werk ruimte

### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning slaat moment opnamen, uitvoer en logboeken op in het Azure Blob Storage-account dat is gekoppeld aan de Azure Machine Learning-werk ruimte en uw abonnement. Alle gegevens die zijn opgeslagen in Azure Blob Storage, worden op rest versleuteld met door micro soft beheerde sleutels.

Zie [Azure Storage versleuteling met door de klant beheerde sleutels in azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md)voor meer informatie over het gebruik van uw eigen sleutels voor gegevens die zijn opgeslagen in Azure Blob-opslag.

Trainings gegevens worden doorgaans ook opgeslagen in Azure Blob-opslag, zodat deze toegankelijk is voor Compute-doel stellingen. Deze opslag wordt niet beheerd door Azure Machine Learning, maar gekoppeld aan Compute-doelen als een extern bestands systeem.

Als u de sleutel wilt __draaien of intrekken__ , kunt u dit op elk gewenst moment doen. Bij het draaien van een sleutel begint het opslag account met de nieuwe sleutel (meest recente versie) voor het versleutelen van gegevens in rust. Bij het intrekken (uitschakelen) van een sleutel zorgt het opslag account ervoor dat er mislukte aanvragen worden uitgevoerd. Het duurt doorgaans een uur voordat de draaiing of intrekken effectief is.

Zie [toegangs sleutels voor opslag opnieuw genereren](how-to-change-storage-access-key.md)voor meer informatie over het opnieuw genereren van de toegangs sleutels.

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning meta gegevens worden opgeslagen in een Azure Cosmos DB-exemplaar. Dit exemplaar is gekoppeld aan een micro soft-abonnement dat wordt beheerd door Azure Machine Learning. Alle gegevens die zijn opgeslagen in Azure Cosmos DB, worden op rest versleuteld met door micro soft beheerde sleutels.

Als u uw eigen (door de klant beheerde) sleutels wilt gebruiken om de Azure Cosmos DB-exemplaar te versleutelen, kunt u een speciaal Cosmos DB exemplaar maken voor gebruik met uw werk ruimte. We raden u aan deze aanpak te gebruiken als u uw gegevens wilt opslaan, zoals informatie over de uitvoerings geschiedenis, buiten het Cosmos DB exemplaar met meerdere tenants dat wordt gehost op het micro soft-abonnement. 

Voer de volgende acties uit om het inrichten van een Cosmos DB-exemplaar in uw abonnement met door de klant beheerde sleutels in te scha kelen:

* Registreer de resource providers micro soft. MachineLearning en Microsoft.DocumentDB in uw abonnement, als u dit nog niet hebt gedaan.

* Gebruik de volgende para meters bij het maken van de Azure Machine Learning-werk ruimte. Beide para meters zijn verplicht en worden ondersteund in SDK, CLI, REST Api's en Resource Manager-sjablonen.

    * `resource_cmk_uri`: Deze para meter is de volledige resource-URI van de door de klant beheerde sleutel in uw sleutel kluis, met inbegrip [van de versie gegevens voor de sleutel](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Deze para meter is de resource-ID van de sleutel kluis in uw abonnement. Deze sleutel kluis moet zich in dezelfde regio en hetzelfde abonnement bevinden als de-werk ruimte die u wilt gebruiken voor de Azure Machine Learning. 
    
        > [!NOTE]
        > Dit sleutel kluis exemplaar kan afwijken van de sleutel kluis die door Azure Machine Learning wordt gemaakt wanneer u de werk ruimte inricht. Als u hetzelfde sleutel kluis exemplaar voor de werk ruimte wilt gebruiken, moet u dezelfde sleutel kluis door geven tijdens het inrichten van de werk ruimte met behulp van de [para meter key_vault](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Als u de sleutel wilt __draaien of intrekken__ , kunt u dit op elk gewenst moment doen. Bij het draaien van een sleutel begint Cosmos DB met het gebruik van de nieuwe sleutel (meest recente versie) om gegevens in rust te versleutelen. Wanneer u een sleutel intrekt (uitschakelt), wordt Cosmos DB het uitvoeren van mislukte aanvragen uitgevoerd. Het duurt doorgaans een uur voordat de draaiing of intrekken effectief is.

Zie door [de klant beheerde sleutels voor uw Azure Cosmos DB account configureren](../cosmos-db/how-to-setup-cmk.md)voor meer informatie over door de klant beheerde sleutels met Cosmos db.

### <a name="azure-container-registry"></a>Azure Container Registry

Alle container installatie kopieën in het REGI ster (Azure Container Registry) worden op rest versleuteld. Azure versleutelt automatisch een afbeelding voordat deze wordt opgeslagen en ontsleuteld wanneer Azure Machine Learning de installatie kopie ophaalt.

Als u uw eigen (door de klant beheerde) sleutels wilt gebruiken om uw Azure Container Registry te versleutelen, moet u uw eigen ACR maken en koppelen tijdens het inrichten van de werk ruimte, of het standaard exemplaar dat wordt gemaakt, versleutelen op het moment van de inrichting van de werk ruimte.

> [!IMPORTANT]
> Voor Azure Machine Learning moet het beheerders account zijn ingeschakeld op uw Azure Container Registry. Deze instelling is standaard uitgeschakeld wanneer u een container register maakt. Zie [beheerders account](../container-registry/container-registry-authentication.md#admin-account)voor meer informatie over het inschakelen van het beheerders account.
>
> Als er een Azure Container Registry voor een werkruimte is gemaakt, moet u deze niet verwijderen. Als u dat doet, werkt de Azure Machine Learning-werkruimte niet meer.

Raadpleeg de volgende artikelen voor een voor beeld van het maken van een werk ruimte met behulp van een bestaande Azure Container Registry:

* [Een werk ruimte maken voor Azure machine learning met Azure cli](how-to-manage-workspace-cli.md).
* [Een werk ruimte maken met PYTHON SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace).
* [Een Azure Resource Manager sjabloon gebruiken om een werk ruimte te maken voor Azure Machine Learning](how-to-create-workspace-template.md)

### <a name="azure-container-instance"></a>Azure Container Instance

U kunt een geïmplementeerde Azure container instance-resource (ACI) versleutelen met door de klant beheerde sleutels. De door de klant beheerde sleutel die wordt gebruikt voor ACI kan worden opgeslagen in de Azure Key Vault voor uw werk ruimte. Zie [gegevens versleutelen met een door de klant beheerde sleutel](../container-instances/container-instances-encrypt-data.md#generate-a-new-key)voor meer informatie over het genereren van een sleutel.

Als u de sleutel wilt gebruiken bij het implementeren van een model naar Azure container instance, maakt u een nieuwe implementatie configuratie met `AciWebservice.deploy_configuration()` . Geef de belangrijkste informatie op met behulp van de volgende para meters:

* `cmk_vault_base_url`: De URL van de sleutel kluis die de sleutel bevat.
* `cmk_key_name`: De naam van de sleutel.
* `cmk_key_version`: De versie van de sleutel.

Zie de volgende artikelen voor meer informatie over het maken en gebruiken van een implementatie configuratie:

* Referentie voor [AciWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Waar en hoe u implementeert](how-to-deploy-and-where.md)
* [Een model implementeren op Azure Container Instances](how-to-deploy-azure-container-instance.md)

Zie [gegevens versleutelen met een door de klant beheerde sleutel](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key)voor meer informatie over het gebruik van een door de klant beheerde sleutel met ACI.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

U kunt een geïmplementeerde Azure Kubernetes service-resource op elk gewenst moment versleutelen met door de klant beheerde sleutels. Zie [uw eigen sleutels maken met de Azure Kubernetes-service](../aks/azure-disk-customer-managed-keys.md)voor meer informatie. 

Met dit proces kunt u zowel de gegevens als de besturingssysteem schijf van de geïmplementeerde virtuele machines in het Kubernetes-cluster versleutelen.

> [!IMPORTANT]
> Dit proces werkt alleen met AKS K8s versie 1,17 of hoger. Azure Machine Learning is ondersteuning toegevoegd voor AKS 1,17 op 13 januari 2020.

### <a name="machine-learning-compute"></a>Machine Learning Compute

De besturingssysteem schijf voor elk reken knooppunt dat in Azure Storage is opgeslagen, is versleuteld met door micro soft beheerde sleutels in Azure Machine Learning-opslag accounts. Dit Compute-doel is kortstondig en clusters worden meestal omlaag geschaald wanneer er geen uitvoeringen in de wachtrij worden geplaatst. De inrichting van de onderliggende virtuele machine is ongedaan gemaakt en de besturingssysteem schijf wordt verwijderd. Azure Disk Encryption wordt niet ondersteund voor de besturingssysteem schijf.

Elke virtuele machine heeft ook een lokale tijdelijke schijf voor besturingssysteem bewerkingen. Als u wilt, kunt u de trainings gegevens voor de schijf gebruiken. De schijf is standaard versleuteld voor werk ruimten waarvoor de `hbi_workspace` para meter is ingesteld op `TRUE` . Deze omgeving is slechts korte tijd voor de duur van de uitvoering en de ondersteuning voor versleuteling is beperkt tot alleen door het systeem beheerde sleutels.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks kunnen worden gebruikt in Azure Machine Learning pijp lijnen. Het Databricks File System (DBFS) dat door Azure Databricks wordt gebruikt, is standaard versleuteld met een door micro soft beheerde sleutel. Zie door de klant beheerde sleutels [configureren op standaard (root) DBFS](/azure/databricks/security/customer-managed-keys-dbfs)om Azure Databricks te configureren voor het gebruik van door de klant beheerde sleutels.

### <a name="microsoft-generated-data"></a>Door micro soft gegenereerde gegevens

Wanneer u services zoals automatische Machine Learning gebruikt, kan micro soft een tijdelijke, vooraf verwerkte gegevens genereren voor het trainen van meerdere modellen. Deze gegevens worden opgeslagen in een gegevens opslag in uw werk ruimte, zodat u op de juiste wijze toegangs controle en versleuteling kunt afdwingen.

U kunt ook [Diagnostische gegevens die vanuit uw geïmplementeerde eind punt zijn geregistreerd](how-to-enable-app-insights.md) , versleutelen in uw Azure-toepassing Insights-exemplaar.

## <a name="encryption-in-transit"></a>Versleuteling 'in transit'

Azure Machine Learning gebruikt TLS om interne communicatie tussen verschillende Azure Machine Learning micro services te beveiligen. Alle Azure Storage toegang vindt ook plaats via een beveiligd kanaal.

Azure Machine Learning maakt gebruik van TLS om externe aanroepen naar het Score-eind punt te beveiligen. Zie [TLS gebruiken om een webservice te beveiligen via Azure machine learning](./how-to-secure-web-service.md)voor meer informatie.

## <a name="data-collection-and-handling"></a>Gegevens verzameling en-verwerking

### <a name="microsoft-collected-data"></a>Verzamelde gegevens van micro soft

Micro soft kan gegevens over niet-gebruikers identificeren, zoals resource namen (bijvoorbeeld de naam van de gegevensset of de naam van het machine learning experiment) of taak omgevings variabelen voor diagnostische doel einden. Al deze gegevens worden opgeslagen met behulp van door micro soft beheerde sleutels in de opslag die wordt gehost in micro soft-abonnementen en [de standaard standaarden voor privacybeleid en gegevens verwerking van micro soft](https://privacy.microsoft.com/privacystatement)worden gevolgd.

Micro soft adviseert ook niet om gevoelige informatie (zoals account sleutel geheimen) op te slaan in omgevings variabelen. Omgevings variabelen worden geregistreerd, versleuteld en opgeslagen door ons. Zorg er ook voor dat u een naam [run_id](/python/api/azureml-core/azureml.core.run%28class%29)geeft, Vermijd het gebruik van gevoelige gegevens zoals gebruikers namen of geheime project namen. Deze informatie kan worden weer gegeven in telemetrie-logboeken die toegankelijk zijn voor Microsoft Ondersteuning engineers.

U kunt zich afmelden van diagnostische gegevens die worden verzameld door de `hbi_workspace` para meter in te stellen op `TRUE` tijdens het inrichten van de werk ruimte. Deze functionaliteit wordt ondersteund bij het gebruik van de AzureML python SDK, CLI, REST-Api's of Azure Resource Manager sjablonen.

## <a name="using-azure-key-vault"></a>Azure Key Vault gebruiken

Azure Machine Learning gebruikt het Azure Key Vault exemplaar dat is gekoppeld aan de werk ruimte om referenties van verschillende soorten op te slaan:

* Het gekoppelde opslag account connection string
* Wacht woorden voor Azure container repository-instanties
* Verbindings reeksen naar gegevens archieven

SSH-wacht woorden en sleutels voor het berekenen van doelen zoals Azure HDInsight en Vm's worden opgeslagen in een afzonderlijke sleutel kluis die is gekoppeld aan het micro soft-abonnement. Azure Machine Learning slaat geen wacht woorden of sleutels op die door gebruikers worden verschaft. In plaats daarvan worden er eigen SSH-sleutels gegenereerd, geautoriseerd en opgeslagen om verbinding te maken met Vm's en HDInsight om de experimenten uit te voeren.

Aan elke werk ruimte is een door het systeem toegewezen beheerde identiteit gekoppeld die dezelfde naam heeft als de werk ruimte. Deze beheerde identiteit heeft toegang tot alle sleutels, geheimen en certificaten in de sleutel kluis.

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met Azure Storage](how-to-access-data.md)
* [Gegevens uit gegevensarchief ophalen](how-to-create-register-datasets.md)
* [Verbinding maken met gegevens](how-to-connect-data-ui.md)
* [Trainen met gegevenssets](how-to-train-with-datasets.md)