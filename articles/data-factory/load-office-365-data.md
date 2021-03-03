---
title: Gegevens laden uit Office 365 met behulp van Azure Data Factory
description: Azure Data Factory gebruiken om gegevens te kopiëren van Office 365
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jingwang
ms.openlocfilehash: 54aa511414695d28e390529af61d484e465f1c19
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710262"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Gegevens laden uit Office 365 met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Data Factory _gegevens uit Office 365 laadt in Azure Blob-opslag_. U kunt vergelijk bare stappen volgen om gegevens te kopiëren naar Azure Data Lake gen1 of Gen2. Raadpleeg het [artikel office 365-connector](connector-office-365.md) over het kopiëren van gegevens uit Office 365 in het algemeen.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer in het linkermenu **Een resource maken** > **Analyse** > **Data Factory**: 
   
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geef op de pagina **nieuw Data Factory** waarden op voor de velden die worden weer gegeven in de volgende afbeelding:
      
   ![De pagina Nieuwe data factory](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Naam**: Voer een wereld wijd unieke naam in voor uw Azure-Data Factory. Als u de fout melding ' Data Factory-naam *LoadFromOffice365Demo* is niet beschikbaar ' ontvangt, voert u een andere naam in voor de Data Factory. U kunt bijvoorbeeld _**de naam**_**LoadFromOffice365Demo**. Probeer de data factory opnieuw te maken. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
    * **Abonnement**: Selecteer het Azure-abonnement waarin u de Data Factory wilt maken. 
    * **Resource groep**: Selecteer een bestaande resource groep in de vervolg keuzelijst of selecteer de optie **nieuwe maken** en voer de naam van een resource groep in. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
    * **Versie**: Selecteer **V2**.
    * **Locatie**: Selecteer de locatie voor de Data Factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevens archieven die door data factory worden gebruikt, kunnen zich op andere locaties en regio's bevinden. Deze gegevens archieven bevatten Azure Data Lake Store, Azure Storage, Azure SQL Database, enzovoort.

3. Selecteer **Maken**.
4. Nadat het maken is voltooid, gaat u naar uw data factory. U ziet de **Data Factory** start pagina zoals wordt weer gegeven in de volgende afbeelding:
   
   ![Startpagina van de gegevensfactory](./media/load-office-365-data/data-factory-home-page.png)

5. Klik op de tegel **Maken en bewaken** om de toepassing Gegevensintegratie in een afzonderlijk tabblad te openen.

## <a name="create-a-pipeline"></a>Een pijplijn maken

1. Selecteer op de pagina aan de slag de optie **pijp lijn maken**.
 
    ![Pijplijn maken](./media/load-office-365-data/create-pipeline-entry.png)

2. Voer op het **tabblad Algemeen** voor de pijp lijn ' CopyPipeline ' in voor de **naam** van de pijp lijn.

3. In het dialoog venster activiteiten > u & categorie trans formatie verplaatsen > sleept u de **Kopieer activiteit** uit de werkset naar het ontwerp oppervlak voor pijp lijnen. Geef ' CopyFromOffice365ToBlob ' op als de naam van de activiteit.

### <a name="configure-source"></a>Bron configureren

1. Ga naar het tabblad pijp lijn > **bron** en klik op **+ Nieuw** om een bron-gegevensset te maken. 

2. Selecteer in het venster nieuwe gegevensset de optie **Office 365** en selecteer vervolgens **door gaan**.
 
3. U bevindt zich nu op het tabblad Configuratie van de Kopieer activiteit. Klik op de knop **bewerken** naast de gegevensset Office 365 om door te gaan met de gegevens configuratie.

    ![Algemene configuratie van Office 365 gegevensset](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Er wordt een nieuw tabblad geopend voor de gegevensset Office 365. Voer op het **tabblad Algemeen** onder aan het venster Eigenschappen ' SourceOffice365Dataset ' in bij naam.
 
5. Ga naar het **tabblad verbinding** van de venster Eigenschappen. Klik naast het tekstvak gekoppelde service op **+ Nieuw**.

6. Voer in het venster nieuwe gekoppelde service ' Office365LinkedService ' in als naam, voer de Service-Principal-ID en de sleutel Service-Principal in, test de verbinding en selecteer **maken** om de gekoppelde service te implementeren.

    ![Nieuwe gekoppelde Office 365-service](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. Nadat de gekoppelde service is gemaakt, gaat u terug naar de gegevenssetinstellingen. Kies naast **tabel** de pijl-omlaag om de lijst met beschik bare Office 365-gegevens sets uit te vouwen en kies BasicDataSet_v0. Message_v0 ' in de vervolg keuzelijst:

    ![Tabel met configuratie van Office 365-gegevensset](./media/load-office-365-data/edit-dataset.png)

8. Ga nu terug naar het   >  **tabblad pijplijn bron** om verder te gaan met het configureren van aanvullende eigenschappen voor Office 365-gegevens extractie.  Het filter gebruikers bereik en gebruikers bereik zijn optionele predikaten die u kunt definiëren om de gegevens te beperken die u wilt uitpakken van Office 365. Zie de sectie [Eigenschappen van Office 365-gegevensset](./connector-office-365.md#dataset-properties) voor informatie over het configureren van deze instellingen.

9. U moet een van de datum filters kiezen en de waarden voor de begin-en eind tijd opgeven.

10. Klik op het tabblad **schema importeren** om het schema voor bericht gegevensset te importeren.

    ![Schema voor configuratie van Office 365-gegevensset](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Sink configureren

1. Ga naar het tabblad pijp lijn > **wastafel** en selecteer **+ Nieuw** om een Sink-gegevensset te maken.
 
2. In het venster nieuwe gegevensset ziet u dat alleen de ondersteunde bestemmingen worden geselecteerd bij het kopiëren van Office 365. Selecteer **Azure Blob Storage**, selecteer binaire indeling en selecteer vervolgens **door gaan**.  In deze zelf studie kopieert u Office 365-gegevens naar een Azure Blob Storage.

3. Klik op de knop **bewerken** naast de gegevensset van Azure Blob Storage om door te gaan met de gegevens configuratie.

4. Voer op het **tabblad Algemeen** van het venster Eigenschappen, in naam, ' OutputBlobDataset ' in.

5. Ga naar het **tabblad verbinding** van de venster Eigenschappen. Klik naast het tekstvak Gekoppelde service op **+Nieuw**.

6. Voer in het venster nieuwe gekoppelde service ' AzureStorageLinkedService ' in als naam, selecteer ' Service Principal ' in de vervolg keuzelijst met verificatie methoden, vul het service-eind punt, Tenant, Service-Principal-ID en Service-Principal-sleutel in en selecteer vervolgens opslaan om de gekoppelde service te implementeren.  [Hier](connector-azure-blob-storage.md#service-principal-authentication) vindt u informatie over het instellen van Service-Principal-verificatie voor Azure Blob Storage.

    ![Nieuwe gekoppelde BLOB-service](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>De pijplijn valideren

Selecteer in de werkbalk **Valideren** om de pijplijn te valideren.

U kunt ook de JSON-code die is gekoppeld aan de pijp lijn weer geven door in de rechter bovenhoek op code te klikken.

## <a name="publish-the-pipeline"></a>De pijp lijn publiceren

Selecteer in de bovenste werk balk de optie **Alles publiceren**. Met deze actie publiceert u entiteiten (gegevenssets en pijplijnen) die u hebt gemaakt met Data Factory.

![Wijzigingen publiceren](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>De pijplijn handmatig activeren

Selecteer op de werkbalk de optie **Trigger toevoegen** en selecteer vervolgens **Nu activeren**. Selecteer op de pagina pijplijn uitvoering de optie **volt ooien**. 

## <a name="monitor-the-pipeline"></a>De pijplijn bewaken

Ga naar het tabblad **Controleren** aan de linkerkant. U ziet een pijplijn die wordt geactiveerd door een handmatige trigger. U kunt via de links in de kolom **Acties** details van de activiteiten bekijken en de pijplijn opnieuw uitvoeren.

![De pijplijn bewaken](./media/load-office-365-data/pipeline-status.png) 

Selecteer de link **Uitvoeringen van activiteit weergeven** in de kolom Acties om de activiteituitvoeringen te zien die zijn gekoppeld aan de pijplijnuitvoering. Omdat er in dit voorbeeld slechts één activiteit in de pijplijn is, ziet u slechts één vermelding in de lijst. Selecteer de link **Details** (pictogram van een bril) in de kolom Acties om details over de kopieerbewerking te zien.

![Activiteit bewaken](./media/load-office-365-data/activity-status.png) 

Als dit de eerste keer is dat u gegevens aanvraagt voor deze context (een combi natie van welke gegevens tabel wordt geopend, met welk doel account de gegevens worden geladen en welke gebruikers-id de aanvraag voor gegevens toegang maakt), ziet u dat de status van de Kopieer activiteit wordt **uitgevoerd**, en alleen wanneer u op de koppeling Details onder acties ziet, wordt de status weer gegeven als **RequesetingConsent**.  Een lid van de groep voor het goed keuren van gegevens toegang moet de aanvraag goed keuren in de Privileged Access Management voordat de gegevens extractie kan worden voortgezet.

_Status als aanvraag om toestemming:_ 
 ![ Details van uitvoering van activiteit-toestemming van aanvraag](./media/load-office-365-data/activity-details-request-consent.png) 

_Status als geëxtraheerde gegevens:_

![Details van uitvoering van activiteit-gegevens ophalen](./media/load-office-365-data/activity-details-extract-data.png) 

Zodra de toestemming is gegeven, wordt de gegevens extractie voortgezet en wordt de pipeline-uitvoering na enige tijd weer gegeven als geslaagd.

![Pijp lijn bewaken-geslaagd](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Ga nu naar de doel-Azure-Blob Storage en controleer of Office 365-gegevens zijn geëxtraheerd in binaire indeling.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over ondersteuning voor Azure Synapse Analytics: 

> [!div class="nextstepaction"]
>[Office 365-connector](connector-office-365.md)