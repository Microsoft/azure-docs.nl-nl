---
title: Verbinding maken met gegevens in Storage services op Azure
titleSuffix: Azure Machine Learning
description: Maak data stores en gegevens sets om veilig verbinding te maken met gegevens in Storage services in azure met de Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.custom: how-to, data4ml
ms.openlocfilehash: 554c815e6384115d56611e497f49a2c97ed15e38
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461442"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>Verbinding maken met gegevens met behulp van de Azure Machine Learning Studio

In dit artikel leert u hoe u toegang krijgt tot uw gegevens met behulp van de [Azure machine learning Studio](overview-what-is-machine-learning-studio.md). Maak verbinding met uw gegevens in Storage services in azure met [Azure machine learning data stores](how-to-access-data.md)en verpak die gegevens vervolgens in uw ml-werk stromen met [Azure machine learning gegevens sets](how-to-create-register-datasets.md).

In de volgende tabel worden de voor delen van data stores en gegevens sets gedefinieerd en samenvatten. 

|Object|Beschrijving| Voordelen|   
|---|---|---|
|Gegevensarchieven| U kunt veilig verbinding maken met uw opslag service op Azure door uw verbindings gegevens op te slaan, zoals uw abonnements-ID en Token autorisatie in uw [Key Vault](https://azure.microsoft.com/services/key-vault/) die aan de werk ruimte zijn gekoppeld. | Omdat uw gegevens veilig worden opgeslagen, kunt u <br><br> <li> &nbsp;Plaats geen &nbsp; &nbsp; risico voor verificatie referenties &nbsp; of &nbsp; oorspronkelijke &nbsp; gegevens bronnen. <li> U hoeft deze niet meer vast te coderen in uw scripts.
|Gegevenssets| Als u een gegevensset maakt, maakt u ook een verwijzing naar de locatie van de gegevensbron, samen met een kopie van de bijbehorende metagegevens. Met gegevens sets kunt u <br><br><li> Toegang tot gegevens tijdens model training.<li> Gegevens delen en samen werken met andere gebruikers.<li> Maak gebruik van open-source bibliotheken, zoals Pandas, voor het verkennen van gegevens. | Omdat gegevens sets worden geëvalueerd als vertraagd, en de huidige locatie blijft bestaan, kunt u <br><br><li>Bewaar één kopie van de gegevens in uw opslag.<li> Geen extra opslag kosten in rekening brengen <li> Geen risico dat u onbedoeld uw oorspronkelijke gegevens bronnen wijzigt.<li>Verbeter de prestaties van de werk stroom snelheid. 

Zie het artikel over [beveiligde toegang](concept-data.md#data-workflow) als u wilt weten waar gegevens opslag en gegevens sets passen in de algehele werk stroom van Azure machine learning Data Access.

Raadpleeg de volgende artikelen voor een code-eerste ervaring om de [Azure machine learning python-SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py) te gebruiken voor het volgende:
* [Verbinding maken met Azure Storage-services met gegevens opslag](how-to-access-data.md). 
* [Azure machine learning gegevens sets maken](how-to-create-register-datasets.md). 

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

- Toegang tot [Azure machine learning Studio](https://ml.azure.com/).

- Een Azure Machine Learning-werkruimte. [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md)

    -  Wanneer u een werk ruimte maakt, worden een Azure Blob-container en een Azure-bestands share automatisch geregistreerd als gegevens opslag in de werk ruimte. Ze hebben respectievelijk de naam `workspaceblobstore` en `workspacefilestore` . Als Blob Storage voldoende is voor uw behoeften, `workspaceblobstore` wordt de ingesteld als de standaard gegevens opslag en is deze al geconfigureerd voor gebruik. Anders hebt u een opslag account in azure nodig met een [ondersteund opslag type](how-to-access-data.md#matrix).
    

## <a name="create-datastores"></a>Gegevens opslag maken

U kunt gegevens opslag maken op basis van [deze Azure Storage-oplossingen](how-to-access-data.md#matrix). **Voor niet-ondersteunde opslag oplossingen** en voor het opslaan van de kosten voor de uitvoer van gegevens tijdens ml experimenten, moet u [uw gegevens verplaatsen](how-to-access-data.md#move) naar een ondersteunde Azure Storage-oplossing. [Meer informatie over gegevens opslag](how-to-access-data.md). 

Maak in een paar stappen een nieuwe gegevens opslag met behulp van de Azure Machine Learning Studio.

> [!IMPORTANT]
> Als uw gegevens opslag account zich in een virtueel netwerk bevindt, zijn er extra configuratie stappen vereist om ervoor te zorgen dat Studio toegang heeft tot uw gegevens. Zie [netwerk isolatie & privacy](how-to-enable-studio-virtual-network.md) om te controleren of de juiste configuratie stappen worden toegepast.

1. Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selecteer **gegevens opslag** in het linkerdeel venster onder **beheren**.
1. Selecteer **+ Nieuw gegevensarchief**.
1. Vul het formulier in om een nieuw gegevens archief te maken en te registreren. Het formulier wordt intelligent bijgewerkt op basis van uw selecties voor het Azure-opslag type en verificatie type. Zie de [sectie opslag toegang en-machtigingen](#access-validation) voor meer informatie over waar u de verificatie referenties kunt vinden die u nodig hebt om dit formulier in te vullen.

In het volgende voor beeld ziet u hoe het formulier eruitziet wanneer u een **Azure Blob-gegevens opslag** maakt:

![Formulier voor een nieuwe gegevens opslag](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>Gegevenssets maken

Nadat u een gegevens opslag hebt gemaakt, maakt u een gegevensset om te communiceren met uw gegevens. Met gegevens sets kunt u in een vertraagd geëvalueerd voor machine learning-taken, zoals training. [Meer informatie over gegevenssets](how-to-create-register-datasets.md).

Er zijn twee typen gegevens sets: FileDataset en TabularDataset. 
[FileDatasets](how-to-create-register-datasets.md#filedataset) maakt verwijzingen naar één of meer bestanden of open bare url's. Dat [TabularDatasets](how-to-create-register-datasets.md#tabulardataset) uw gegevens in tabel vorm weer geven. 

In de volgende stappen en animatie ziet u hoe u een gegevensset maakt in [Azure machine learning Studio](https://ml.azure.com).

> [!Note]
> Gegevens sets die zijn gemaakt via Azure Machine Learning Studio, worden automatisch geregistreerd bij de werk ruimte.

![Een gegevensset maken met de gebruikers interface](./media/how-to-connect-data-ui/create-dataset-ui.gif)

Een gegevensset maken in Studio:
1. Meld u aan bij de [Azure machine learning Studio](https://ml.azure.com/).
1. Selecteer **gegevens sets** in het gedeelte **assets** van het linkerdeel venster.
1. Selecteer **gegevensset maken** om de bron van uw gegevensset te kiezen. Deze bron kan lokale bestanden, een gegevens opslag, een open bare URL of een [Azure open-gegevens sets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)zijn.
1. Selecteer een **tabel** of **bestand** voor het type gegevensset.
1. Selecteer **volgende** om het formulier **gegevens opslag en bestand selecteren** te openen. Op dit formulier selecteert u waar u uw gegevensset na het maken moet blijven en selecteert u welke gegevens bestanden u voor uw gegevensset wilt gebruiken.
    1. Schakel overs laan van validatie in als uw gegevens zich in een virtueel netwerk bevindt. Meer informatie over het [isoleren van virtuele netwerken en privacy](how-to-enable-studio-virtual-network.md).
    1. Voor Tabellaire gegevens sets kunt u een ' tijds Erie-Trait opgeven om tijd gerelateerde bewerkingen op uw gegevensset in te scha kelen. Meer informatie over [het toevoegen van de tijds Erie-eigenschappen aan uw gegevensset](how-to-monitor-datasets.md#studio-dataset).
1. Selecteer **volgende** om de **instellingen en de voor beeld** -en **schema** formulieren in te vullen. ze worden op intelligente wijze ingevuld op basis van het bestands type en u kunt uw gegevensset verder configureren voordat deze formulieren worden gemaakt. 
1. Selecteer **volgende** om het formulier **Details bevestigen** weer te geven. Controleer uw selecties en maak een optioneel gegevens profiel voor uw gegevensset. Meer informatie over [gegevensprofilering](#profile).
1. Selecteer **maken** om het maken van de gegevensset te volt ooien.

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>Gegevens profiel en preview

Nadat u de gegevensset hebt gemaakt, controleert u of u het profiel en de preview-versie in de Studio kunt bekijken met de volgende stappen. 

1. Meld u aan bij de [Azure machine learning Studio](https://ml.azure.com/)
1. Selecteer **gegevens sets** in het gedeelte **assets** van het linkerdeel venster.
1. Selecteer de naam van de gegevensset die u wilt weer geven. 
1. Selecteer het tabblad **verkennen** . 
1. Selecteer het tabblad **voor beeld** of **profiel** . 

![Het profiel van de gegevensset en de preview weer geven](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

U kunt een groot aantal samenvattings statistieken in uw gegevensset ontvangen om te controleren of uw gegevensset van ML is. Voor niet-numerieke kolommen bevatten ze alleen basis statistieken zoals min, Max en aantal fouten. Voor numerieke kolommen kunt u ook hun statistische momenten en geschatte quantiles bekijken. 

Het gegevens Profiel van Azure Machine Learning gegevensset bevat met name het volgende:

>[!NOTE]
> Lege vermeldingen worden weer gegeven voor functies met irrelevante typen.

|Statistic|Beschrijving
|------|------
|Functie| De naam van de kolom die wordt samenvatten.
|Profiel| In-line visualisatie op basis van het type dat is afgeleid. Teken reeksen, Booleaanse waarden en datums hebben bijvoorbeeld een aantal aantallen, terwijl decimalen (cijfers) geschatte histogrammen hebben. Zo kunt u een goed inzicht krijgen in de distributie van de gegevens.
|Type distributie| In-line waarde van het aantal typen in een kolom. Nullen zijn hun eigen type, waardoor deze visualisatie nuttig is voor het detecteren van oneven of ontbrekende waarden.
|Type|Het type van de kolom is afgeleid. Mogelijke waarden zijn: teken reeksen, booleans, datums en decimalen.
|Min.| Minimum waarde van de kolom. Lege vermeldingen worden weer gegeven voor functies waarvan het type geen inherente ordening (zoals Boole-waarden) heeft.
|Max.| De maximum waarde van de kolom. 
|Aantal| Totaal aantal ontbrekende en niet-ontbrekende vermeldingen in de kolom.
|Niet-ontbrekend aantal| Het aantal vermeldingen in de kolom die niet ontbreken. Lege teken reeksen en fouten worden beschouwd als waarden, zodat ze niet bijdragen aan het aantal niet-ontbrekende items.
|Kwantielen| Geschatte waarden bij elke quantile om een idee te geven van de distributie van de gegevens.
|Gemiddeld| Reken kundige gemiddelde of gemiddelde van de kolom.
|Standaarddeviatie| Meting van de hoeveelheid sprei ding of variatie van de gegevens van deze kolom.
|Variantie| De mate waarin de gegevens van deze kolom worden verdeeld uit de gemiddelde waarde. 
|Asymmetrie| Meting van de manier waarop de gegevens van de andere kolom van een normale distributie zijn.
|Kurtosis| Meting van hoe sterk de gegevens van deze kolom worden vergeleken met een normale distributie.

## <a name="storage-access-and-permissions"></a>Toegang tot en machtigingen voor opslag

Om ervoor te zorgen dat u veilig verbinding maakt met uw Azure Storage-service, moet u voor Azure Machine Learning toegang hebben tot de bijbehorende gegevens opslag. Deze toegang is afhankelijk van de verificatie referenties die worden gebruikt voor het registreren van de gegevens opslag.

### <a name="virtual-network"></a>Virtueel netwerk

Als uw gegevens opslag account zich in een **virtueel netwerk** bevindt, moet u extra configuratie stappen uitvoeren om ervoor te zorgen dat Azure machine learning toegang heeft tot uw gegevens. Zie [netwerk isolatie & privacy](how-to-enable-studio-virtual-network.md) om te controleren of de juiste configuratie stappen worden toegepast wanneer u uw gegevens opslag maakt en registreert.  

### <a name="access-validation"></a>Toegangs validatie

**Als onderdeel van het proces voor het maken en registreren van de eerste Data Store**, Azure machine learning automatisch gevalideerd of de onderliggende opslag service bestaat en de door de gebruiker opgegeven principal (gebruikers naam, Service-Principal of SAS-token) heeft toegang tot de opgegeven opslag.

**Nadat Data Store is gemaakt**, wordt deze validatie alleen uitgevoerd voor methoden waarvoor toegang tot de onderliggende opslag container is vereist. **niet** elke keer dat de objecten gegevens opslag worden opgehaald. Validatie vindt bijvoorbeeld plaats als u bestanden wilt downloaden uit uw gegevens archief. maar als u de standaard gegevens opslag gewoon wilt wijzigen, vindt validatie niet plaats.

Als u uw toegang tot de onderliggende opslag service wilt verifiëren, kunt u uw account sleutel, SAS-tokens (Shared Access signatures) of Service-Principal opgeven op basis van het gegevens opslag type dat u wilt maken. De [opslag type matrix](how-to-access-data.md#matrix) bevat de ondersteunde verificatie typen die overeenkomen met elk type gegevens opslag.

U vindt de account sleutel, het SAS-token en de gegevens van de Service-Principal op uw [Azure Portal](https://portal.azure.com).

* Als u van plan bent om een account sleutel of SAS-token voor verificatie te gebruiken, selecteert u **opslag accounts** in het linkerdeel venster en kiest u het opslag account dat u wilt registreren.
  * De **overzichts** pagina bevat informatie zoals de account naam, container en naam van de bestands share.
      1. Ga voor account sleutels naar **toegangs sleutels** in het deel venster **instellingen** .
      1. Voor SAS-tokens gaat u naar de **hand tekeningen voor gedeelde toegang** in het deel venster **instellingen** .

* Als u van plan bent om een [Service-Principal](../active-directory/develop/howto-create-service-principal-portal.md) te gebruiken voor verificatie, gaat u naar uw **app-registraties** en selecteert u welke app u wilt gebruiken.
    * De bijbehorende **overzichts** pagina bevat de vereiste informatie, zoals Tenant-id en client-id.

> [!IMPORTANT]
> * Als u de toegangs sleutels voor een Azure Storage account (account sleutel of SAS-token) wilt wijzigen, moet u ervoor zorgen dat u de nieuwe referenties synchroniseert met uw werk ruimte en de gegevens opslag die ermee zijn verbonden. Meer informatie over [het synchroniseren van uw bijgewerkte referenties](how-to-change-storage-access-key.md). <br> <br>
> * Als u de registratie ongedaan maakt en een gegevens archief met dezelfde naam opnieuw registreert, en dit mislukt, is het mogelijk dat de Azure Key Vault voor uw werk ruimte niet voor het voorlopig verwijderen is ingeschakeld. Voorlopig verwijderen is standaard ingeschakeld voor het sleutel kluis-exemplaar dat is gemaakt door uw werk ruimte, maar kan niet worden ingeschakeld als u een bestaande sleutel kluis hebt gebruikt of een werk ruimte hebt gemaakt vóór oktober 2020. Zie voor meer informatie over het inschakelen van soft voorlopig [verwijderen inschakelen voor een bestaande sleutel kluis]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault).

### <a name="permissions"></a>Machtigingen

Voor Azure Blob-container en Azure Data Lake gen 2-opslag, moet u ervoor zorgen dat uw verificatie referenties toegang hebben tot **BLOB-gegevens lezer voor opslag** . Meer informatie over [Storage BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Een SAS-token voor het account is standaard ingesteld op geen machtigingen. 
* Voor **Lees toegang** voor gegevens moet uw verificatie referenties mini maal lijst-en lees machtigingen voor containers en objecten hebben. 

* Voor het **schrijven** van gegevens is het schrijven en toevoegen van machtigingen ook vereist.

## <a name="train-with-datasets"></a>Trainen met gegevenssets

Gebruik uw gegevens sets in uw machine learning experimenten voor trainings ML-modellen. [Meer informatie over het trainen van gegevens sets](how-to-train-with-datasets.md)

## <a name="next-steps"></a>Volgende stappen

* [Een stapsgewijze voor beeld van training met TabularDatasets en geautomatiseerde machine learning](tutorial-first-experiment-automated-ml.md).

* [Train een model](how-to-set-up-training-targets.md).

* Zie de voor beelden van [notitie blokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)voor meer informatie over het trainen van gegevensset.