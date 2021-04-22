---
title: Verbinding maken met gegevens in opslagservices in Azure
titleSuffix: Azure Machine Learning
description: Maak gegevensopslag en gegevenssets om veilig verbinding te maken met gegevens in opslagservices in Azure met de Azure Machine Learning-studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.custom: data4ml
ms.openlocfilehash: 4263c3334e19ddeb6a61325e8802f8544a282c6f
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107890056"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>Verbinding maken met gegevens met de Azure Machine Learning-studio

In dit artikel leert u hoe u toegang krijgt tot uw gegevens met de [Azure Machine Learning-studio](overview-what-is-machine-learning-studio.md). Maak verbinding met uw gegevens in opslagservices in Azure [met Azure Machine Learning-gegevensopslag](how-to-access-data.md)en verwerk die gegevens vervolgens voor taken in uw ML-werkstromen met Azure Machine Learning [gegevenssets](how-to-create-register-datasets.md).

De volgende tabel bevat een overzicht van de voordelen van gegevensstores en gegevenssets. 

|Object|Description| Voordelen|   
|---|---|---|
|Gegevensarchieven| Maak veilig verbinding met uw opslagservice in Azure door uw verbindingsgegevens, zoals uw abonnements-id en tokenautorisatie, op te slaan in uw [Key Vault](https://azure.microsoft.com/services/key-vault/) gekoppeld aan de werkruimte | Omdat uw gegevens veilig worden opgeslagen, kunt u <br><br> <li> Maak geen risico &nbsp; &nbsp; voor verificatiereferenties of oorspronkelijke &nbsp; &nbsp; &nbsp; &nbsp; gegevensbronnen. <li> U hoeft ze niet langer in uw scripts in code vast te houden.
|Gegevenssets| Als u een gegevensset maakt, maakt u ook een verwijzing naar de locatie van de gegevensbron, samen met een kopie van de bijbehorende metagegevens. Met gegevenssets kunt u <br><br><li> Toegang tot gegevens tijdens het trainen van het model.<li> Gegevens delen en samenwerken met andere gebruikers.<li> Maak gebruik van opensource-bibliotheken, zoals pandas, voor gegevensverkenning. | Omdat gegevenssets lazily worden geëvalueerd en de gegevens op de bestaande locatie blijven, <br><br><li>Bewaar één kopie van de gegevens in uw opslag.<li> Geen extra opslagkosten <li> Risico's voor het onbedoeld wijzigen van uw oorspronkelijke gegevensbronnen niet.<li>Prestatiesnelheden van ML-werkstromen verbeteren. 

Zie het artikel Gegevens veilig openen om te begrijpen waar gegevensstores en gegevenssets in Azure Machine Learning algemene werkstroom voor [gegevenstoegang](concept-data.md#data-workflow) passen.

Zie de volgende artikelen voor een eerste code-ervaring om de [python-SDK](/python/api/overview/azure/ml/) Azure Machine Learning te gebruiken voor:
* [Maak verbinding met Azure Storage-services met behulp van gegevensopslag.](how-to-access-data.md) 
* [Maak Azure Machine Learning gegevenssets](how-to-create-register-datasets.md). 

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

- Toegang tot [Azure Machine Learning-studio](https://ml.azure.com/).

- Een Azure Machine Learning-werkruimte. [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md)

    -  Wanneer u een werkruimte maakt, worden een Azure Blob-container en een Azure-bestands share automatisch geregistreerd als gegevensopslag voor de werkruimte. Ze hebben respectievelijk `workspaceblobstore` de `workspacefilestore` namen en . Als blob-opslag voldoende is voor uw behoeften, wordt de ingesteld als het `workspaceblobstore` standaardgegevensopslag en is deze al geconfigureerd voor gebruik. Anders hebt u een opslagaccount in Azure nodig met een [ondersteund opslagtype](how-to-access-data.md#matrix).
    

## <a name="create-datastores"></a>Gegevensstores maken

U kunt gegevensopslag maken van deze [Azure-opslagoplossingen.](how-to-access-data.md#matrix) **Voor niet-ondersteunde opslagoplossingen** en om kosten voor gegevensuitdeding te besparen tijdens ML-experimenten, moet u uw gegevens verplaatsen [naar](how-to-access-data.md#move) een ondersteunde Azure-opslagoplossing. [Meer informatie over gegevensstores.](how-to-access-data.md) 

Maak in een paar stappen een nieuwe gegevensstore met de Azure Machine Learning-studio.

> [!IMPORTANT]
> Als uw gegevensopslagaccount zich in een virtueel netwerk, zijn aanvullende configuratiestappen vereist om ervoor te zorgen dat de studio toegang heeft tot uw gegevens. Zie [Netwerkisolatie & privacy om](how-to-enable-studio-virtual-network.md) ervoor te zorgen dat de juiste configuratiestappen worden toegepast.

1. Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selecteer **Gegevensstores in** het linkerdeelvenster onder **Beheren.**
1. Selecteer **+ Nieuw gegevensarchief**.
1. Vul het formulier in om een nieuwe gegevensstore te maken en te registreren. Het formulier wordt op intelligente wijze bijgewerkt op basis van uw selecties voor azure-opslagtype en verificatietype. Zie de [sectie Toegang tot opslag en machtigingen](#access-validation) voor meer informatie over waar u de verificatiereferenties kunt vinden die u nodig hebt om dit formulier in te vullen.

In het volgende voorbeeld ziet u hoe het formulier eruitziet wanneer u een **Azure Blob-gegevensopslag maakt:**

![Formulier voor een nieuwe gegevensstore](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>Gegevenssets maken

Nadat u een gegevensstore hebt maken, maakt u een gegevensset om met uw gegevens te communiceren. Gegevenssets verpakken uw gegevens in een tijdrovend geëvalueerd verbruiksobject voor machine learning taken, zoals training. [Meer informatie over gegevenssets](how-to-create-register-datasets.md).

Er zijn twee typen gegevenssets: FileDataset en TabularDataset. 
[FileDatasets maakt](how-to-create-register-datasets.md#filedataset) verwijzingen naar één of meerdere bestanden of openbare URL's. [TabularDatasets vertegenwoordigen](how-to-create-register-datasets.md#tabulardataset) uw gegevens in tabelvorm. U kunt TabularDatasets maken op basis van .csv-, .tsv-, .parquet-, .jsonl-bestanden en van SQL-queryresultaten.

De volgende stappen en animatie laten zien hoe u een gegevensset maakt in [Azure Machine Learning-studio](https://ml.azure.com).

> [!Note]
> Gegevenssets die zijn gemaakt Azure Machine Learning-studio worden automatisch geregistreerd bij de werkruimte.

![Een gegevensset maken met de gebruikersinterface](./media/how-to-connect-data-ui/create-dataset-ui.gif)

Een gegevensset maken in de studio:
1. Meld u aan bij [Azure Machine Learning-studio](https://ml.azure.com/).
1. Selecteer **Gegevenssets** in **de sectie** Assets van het linkerdeelvenster.
1. Selecteer **Gegevensset maken** om de bron van uw gegevensset te kiezen. Deze bron kan lokale bestanden, een gegevensstore, openbare URL's [of Azure Open Datasets.](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)
1. Selecteer **Tabellair** of **Bestand als** Gegevenssettype.
1. Selecteer **Volgende om** het formulier Gegevens opslaan en **bestandsselectie te** openen. Op dit formulier selecteert u waar u uw gegevensset wilt bewaren nadat deze is gemaakt, en selecteert u welke gegevensbestanden u wilt gebruiken voor uw gegevensset.
    1. Schakel validatie overslaan in als uw gegevens zich in een virtueel netwerk. Meer informatie over isolatie [van virtuele netwerken en privacy.](how-to-enable-studio-virtual-network.md)
    1. Voor gegevenssets in tabelvorm kunt u een eigenschap 'timeseries' opgeven om tijdgerelateerde bewerkingen op uw gegevensset mogelijk te maken. Meer informatie over het [toevoegen van de eigenschap timeseries aan uw gegevensset](how-to-monitor-datasets.md#studio-dataset).
1. Selecteer **Volgende om** de formulieren Instellingen en voorbeeld **en** Schema **in te** vullen; Ze worden intelligent ingevuld op basis van het bestandstype en u kunt uw gegevensset verder configureren voordat u deze formulieren maakt. 
1. Selecteer **Volgende om** het formulier Details bevestigen **te** controleren. Controleer uw selecties en maak een optioneel gegevensprofiel voor uw gegevensset. Meer informatie over [gegevensprofilering](#profile).
1. Selecteer **Maken om** het maken van uw gegevensset te voltooien.

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>Gegevensprofiel en preview

Nadat u de gegevensset hebt gemaakt, controleert u of u het profiel en de preview-versie in de studio kunt bekijken met de volgende stappen. 

1. Meld u aan bij [de Azure Machine Learning-studio](https://ml.azure.com/)
1. Selecteer **Gegevenssets** in **de sectie** Assets van het linkerdeelvenster.
1. Selecteer de naam van de gegevensset die u wilt weergeven. 
1. Selecteer het **tabblad** Verkennen. 
1. Selecteer het **tabblad Voorbeeld** **of** Profiel. 

![Profiel en preview van gegevensset weergeven](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

U kunt een groot aantal samenvattingsstatistieken voor uw gegevensset krijgen om te controleren of uw gegevensset gereed is voor ML. Voor niet-numerieke kolommen bevatten ze alleen basisstatistieken zoals min, max en aantal fouten. Voor numerieke kolommen kunt u ook hun statistische momenten en geschatte kwantielen bekijken. 

Het gegevensprofiel Azure Machine Learning de gegevensset omvat met name:

>[!NOTE]
> Lege vermeldingen worden weergegeven voor functies met irrelevante typen.

|Statistic|Description
|------|------
|Functie| Naam van de kolom die wordt samengevat.
|Profiel| In-line visualisatie op basis van het type afgeleid. Tekenreeksen, booleaanse waarden en datums hebben bijvoorbeeld waardetellingen, terwijl decimalen (numerieke waarden) bij benadering histogrammen hebben. Hierdoor krijgt u snel inzicht in de distributie van de gegevens.
|Typedistributie| Aantal in line-waarden van typen binnen een kolom. Null-waarden zijn hun eigen type, dus deze visualisatie is handig voor het detecteren van afwijkende of ontbrekende waarden.
|Type|Afgeleid type van de kolom. Mogelijke waarden zijn: tekenreeksen, booleaanse waarden, datums en decimalen.
|Min| Minimumwaarde van de kolom. Lege vermeldingen worden weergegeven voor functies waarvan het type geen inherente volgorde heeft (zoals Booleaansen).
|Max| Maximumwaarde van de kolom. 
|Count| Totaal aantal ontbrekende en niet-ontbrekende vermeldingen in de kolom.
|Niet-ontbrekend aantal| Het aantal vermeldingen in de kolom dat niet ontbreekt. Lege tekenreeksen en fouten worden behandeld als waarden, zodat ze niet bijdragen aan het 'niet ontbrekende aantal'.
|Kwantielen| Geschatte waarden op elk kwantiel om een idee te geven van de verdeling van de gegevens.
|Gemiddeld| Rekenkundig gemiddelde of gemiddelde van de kolom.
|Standaarddeviatie| Meting van de hoeveelheid spreiding of variatie van de gegevens van deze kolom.
|Variantie| Meting van hoe ver de gegevens van deze kolom zijn verspreid, is van de gemiddelde waarde. 
|Asymmetrie| Meting van hoe verschillend de gegevens van deze kolom zijn van een normale verdeling.
|Kurtosis| Meting van hoe sterk tailed de gegevens van deze kolom worden vergeleken met een normale verdeling.

## <a name="storage-access-and-permissions"></a>Toegang en machtigingen voor opslag

Om ervoor te zorgen dat u veilig verbinding maakt met uw Azure-opslagservice, Azure Machine Learning dat u toegang hebt tot de bijbehorende gegevensopslag. Deze toegang is afhankelijk van de verificatiereferenties die worden gebruikt om het gegevensstore te registreren.

### <a name="virtual-network"></a>Virtueel netwerk

Als uw gegevensopslagaccount zich in een virtueel **netwerk**, zijn aanvullende configuratiestappen vereist om ervoor te zorgen Azure Machine Learning toegang tot uw gegevens heeft. Zie [Netwerkisolatie & privacy om](how-to-enable-studio-virtual-network.md) ervoor te zorgen dat de juiste configuratiestappen worden toegepast wanneer u uw gegevensstore maakt en registreert.  

### <a name="access-validation"></a>Toegangsvalidatie

**Als** onderdeel van het eerste proces voor het maken en registreren van gegevensopslag valideert Azure Machine Learning automatisch of de onderliggende opslagservice bestaat en de door de gebruiker opgegeven principal (gebruikersnaam, service-principal of SAS-token) toegang heeft tot de opgegeven opslag.

**Nadat het gegevensopslag is gemaakt,** wordt deze validatie alleen uitgevoerd voor methoden waarvoor toegang tot de onderliggende opslagcontainer is **vereist,** niet telkens wanneer gegevensopslagobjecten worden opgehaald. Validatie vindt bijvoorbeeld plaats als u bestanden wilt downloaden uit uw gegevensstore; Maar als u alleen uw standaardgegevensstore wilt wijzigen, wordt de validatie niet uitgevoerd.

Als u uw toegang tot de onderliggende opslagservice wilt verifiëren, kunt u uw accountsleutel, SAS-tokens (Shared Access Signatures) of service-principal verstrekken op basis van het gegevensopslagtype dat u wilt maken. De [opslagtypematrix](how-to-access-data.md#matrix) bevat de ondersteunde verificatietypen die overeenkomen met elk gegevensopslagtype.

U vindt informatie over de accountsleutel, het SAS-token en de service-principal op [Azure Portal](https://portal.azure.com).

* Als u van plan bent om een accountsleutel of SAS-token te gebruiken voor verificatie, selecteert u Opslagaccounts **in** het linkerdeelvenster en kiest u het opslagaccount dat u wilt registreren.
  * De **pagina** Overzicht bevat informatie zoals de accountnaam, container en bestands sharenaam.
      1. Ga voor accountsleutels naar **Toegangssleutels** in het **deelvenster** Instellingen.
      1. Ga voor SAS-tokens naar **Shared Access Signatures** in het **deelvenster** Instellingen.

* Als u van plan bent om een [service-principal](../active-directory/develop/howto-create-service-principal-portal.md) te gebruiken voor verificatie, gaat u naar uw **App-registraties** selecteert u welke app u wilt gebruiken.
    * De **bijbehorende overzichtspagina** bevat vereiste informatie, zoals tenant-id en client-id.

> [!IMPORTANT]
> * Als u uw toegangssleutels voor een Azure Storage-account (accountsleutel of SAS-token) moet wijzigen, moet u de nieuwe referenties synchroniseren met uw werkruimte en de gegevensstores die erop zijn aangesloten. Meer informatie over het [synchroniseren van uw bijgewerkte referenties.](how-to-change-storage-access-key.md) <br> <br>
> * Als u de registratie van een gegevensstore met dezelfde naam ongedaan maakt en opnieuw registreert en dit mislukt, is de Azure Key Vault voor uw werkruimte mogelijk niet ingeschakeld voor het verwijderen van gegevens. De functie voor het verwijderen van de sleutelkluis is standaard ingeschakeld voor het sleutelkluis-exemplaar dat door uw werkruimte is gemaakt, maar is mogelijk niet ingeschakeld als u een bestaande sleutelkluis hebt gebruikt of als u een werkruimte hebt gemaakt vóór oktober 2020. Zie Soft Delete inschakelen voor een bestaande sleutelkluis voor meer informatie over het inschakelen van [soft-delete.]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault)

### <a name="permissions"></a>Machtigingen

Zorg ervoor dat uw verificatiereferenties toegang hebben tot Storage **Blob Data Reader** voor Azure Blob-container en Azure Data Lake Gen 2-opslag. Meer informatie over [Lezer voor Opslagblobgegevens.](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) Een SAS-token voor een account is standaard ingesteld op geen machtigingen. 
* Voor **leestoegang tot gegevens** moeten uw verificatiereferenties minimaal lijst- en leesmachtigingen hebben voor containers en objecten. 

* Voor **schrijftoegang zijn ook** schrijf- en toevoegmachtigingen vereist.

## <a name="train-with-datasets"></a>Trainen met gegevenssets

Gebruik uw gegevenssets in uw machine learning voor het trainen van ML-modellen. [Meer informatie over trainen met gegevenssets](how-to-train-with-datasets.md)

## <a name="next-steps"></a>Volgende stappen

* [Een stapsgewijs voorbeeld van training met TabularDatasets en geautomatiseerde machine learning](tutorial-first-experiment-automated-ml.md).

* [Een model trainen.](how-to-set-up-training-targets.md)

* Zie de voorbeeldnotenotes voor meer voorbeelden van [gegevenssettraining.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)
