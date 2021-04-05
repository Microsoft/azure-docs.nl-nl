---
title: Een kennisarchief maken in de Azure-portal
titleSuffix: Azure Cognitive Search
description: Gebruik de wizard gegevens importeren om een kennisarchief te maken om verrijkte inhoud op te slaan. Maak verbinding met een kennisarchief voor analyses van andere apps of om verrijkte content te sturen naar downstreamprocessen.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/17/2020
ms.openlocfilehash: 3225013f09abd326c619b67caf77918889a64859
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94741804"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Quickstart: Een Azure Cognitive Search-kennisarchief maken in het Azure-portal

Kennisarchief is een functie van Azure Cognitive Search die uitvoer van een pijplijn voor inhoudsverwerking opslaat voor verdere analyse of downstreamverwerking. 

Een pijplijn aanvaardt ongestructureerde tekst- en afbeeldingsinhoud, past door Cognitive Services aangedreven AI (zoals OCR en natuurlijke taalverwerking) toe en voert nieuwe structuren en informatie uit die niet eerder bestonden. Een van de fysieke artefacten die een pijplijn maakt is een [kennisarchief](knowledge-store-concept-intro.md), dat u kunt openen via tools om inhoud te analyseren en doorzoeken.

In deze quickstart combineert u services en gegevens in de Azure-cloud om een kennisarchief te maken. Zodra alles gereed is, voert u de wizard **Gegevens importeren** uit in het portal om alles samen op te halen. Het eindresultaat is oorspronkelijke tekstinhoud plus door AI gegenereerde inhoud die u kunt bekijken in de portal ([Storage Explorer](knowledge-store-view-storage-explorer.md)).

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/)

+ Een Azure Cognitive Search-service. [Maak een service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in uw huidige abonnement. U kunt een gratis service voor deze quickstart gebruiken. 

+ Een Azure Storage-account met [Blob-opslag](../storage/blobs/index.yml).

> [!NOTE]
> Deze quickstart maakt ook gebruik van [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) voor de AI. Omdat de workload zo klein is, wordt de gratis verwerking (maximaal 20 transacties) van Cognitive Services achter de schermen gebruikt. Dit betekent dat u deze oefening kunt doen zonder dat u een nieuwe Cognitive Services-resource moet aanmaken.

## <a name="set-up-your-data"></a>Uw gegevens voorbereiden

Volg deze stappen om een blobcontainer aan te maken in Azure Storage om heterogene inhoudsbestanden op te slaan.

1. [Download HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Dit zijn gegevens van hotelbeoordelingen die zijn opgeslagen in een CSV-bestand (oorsprong Kaggle.com). Ze bevatten 19 items met klantenfeedback over een hotel. 

1. [Maak een Azure Storage-account](../storage/common/storage-account-create.md?tabs=azure-portal) of [zoek een bestaand account](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) onder uw huidige abonnement. U gebruikt Azure Storage zowel voor de onbewerkte inhoud die u wilt importeren als het kennisarchief dat het eindresultaat vormt.

   + Kies het accounttype **StorageV2 (algemeen gebruik V2)** .

1. Open de Blob-servicepagina's en maak een container met de naam *hotel-beoordelingen*.

1. Klik op **Uploaden**.

    ![De gegevens uploaden](media/knowledge-store-create-portal/upload-command-bar.png "De hotelrecensies uploaden")

1. Selecteer het bestand **HotelReviews-Free.csv** dat u in de eerste stap hebt gedownload.

    ![De Azure-blobcontainer maken](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "De Azure-blobcontainer aanmaken")

1. Voordat u de Blob-opslagpagina's afsluit, gebruikt u een koppeling in het navigatievenster aan de linkerkant om de pagina **Toegangssleutels** te openen. Gebruik een verbindingsreeks om gegevens op te halen uit de Blob-opslag. Een verbindingsreeks ziet er ongeveer als volgt uit: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

U kunt nu verdergaan met de wizard **Gegevens importeren**.

## <a name="run-the-import-data-wizard"></a>De wizard Gegevens importeren uitvoeren

1. Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com/).

1. [Vind uw zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) en klik op de overzichtspagina op **Gegevens importeren** in de opdrachtbalk om in vier stappen een kennisarchief te maken.

   ![Opdracht Gegevens importeren](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Stap 1: Een gegevensbron maken

1. In **Verbinden met uw gegevens** kiest u **Azure Blob Storage** en selecteert u het account dat en de container die u hebt gemaakt. 

1. Voer `hotel-reviews-ds` in onder **Naam**.

1. Selecteer voor **Parseermodus** **Tekstbestand met scheidingstekens** en schakel vervolgens het selectievakje **Eerste regel bevat de koptekst** in. Zorg ervoor dat het **Scheidingsteken** een komma (,) is.

1. Plak bij **Verbindingsreeks** de verbindingsreeks die u heeft gekopieerd heeft van de pagina **Toegangssleutels** in Azure Storage.

1. Voer bij **Containers** de naam in van de blobcontainer die de gegevens bevat.

    Uw pagina moet er ongeveer uitzien als in de volgende schermafbeelding.

    ![Een gegevensbronobject maken](media/knowledge-store-create-portal/hotel-reviews-ds.png "Een gegevensbronobject maken")

1. Ga door naar de volgende pagina.

### <a name="step-2-add-cognitive-skills"></a>Stap 2: Cognitieve vaardigheden toevoegen

In deze stap van de wizard maakt u een vaardighedenset met verrijkingen van cognitieve vaardigheden. De brongegevens bestaan uit beoordelingen van klanten in verschillende talen. De vaardigheden die relevant zijn voor deze gegevensset zijn sleuteltermextractie, gevoelsdetectie en vertaling. In een latere stap worden deze verrijkingen als Azure-tabellen 'geprojecteerd' naar een kennisarchied.

1. Vouw **Cognitive Services toevoegen** uit. Standaard is **Gratis (beperkte verrijkingen)** geselecteerd. U kunt deze resource gebruiken omdat HotelReviews-Free.csv 19 records bevat en deze gratis resource tot 20 transacties toestaat.

1. Vouw **Verrijkingen toevoegen** uit.

1. Voer `hotel-reviews-ss` in bij **Naam van de vaardighedenset**.

1. Selecteer **reviews_text** bij **veld voor brongegevens**.

1. Selecteer **Pagina's (5000 tekensegmenten)** bij **Granulatieniveau van de verrijking**

1. Selecteer deze cognitieve vaardigheden:
    + **Sleuteltermen extraheren**
    + **Tekst vertalen**
    + **Gevoel detecteren**

      ![Een vaardighedenset maken](media/knowledge-store-create-portal/hotel-reviews-ss.png "Een set vaardigheden maken")

1. Vouw **Verrijkingen opslaan in een kennisarchief** uit.

1. Selecteer deze **Azure-tabelprojecties**:
    + **Documenten**
    + **Pagina's**
    + **Sleuteltermen**

1. Voer de **Verbindingsreeks voor het opslagaccount** in die u in een vorige stap hebt opgeslagen.

    ![Een kennisarchief configureren](media/knowledge-store-create-portal/hotel-reviews-ks.png "Een kennisarchief configureren")

1. Download een Power BI-sjabloon (optioneel) Wanneer u het sjabloon opent vanuit de wizard, wordt het lokale .pbit-bestand aangepast aan de vorm van uw gegevens.

1. Ga door naar de volgende pagina.

### <a name="step-3-configure-the-index"></a>Stap 3: De index configureren

In deze stap van de wizard configureert u een index voor optionele zoekopdrachten in volledige tekst. Deze wizard gebruikt uw gegevensbron om velden en gegevenstypen af te leiden. U hoeft alleen de kenmerken voor het gewenste gedrag te selecteren. Met het kenmerk **Ophalen mogelijk** kan de zoekservice bijvoorbeeld een veldwaarde retourneren, met **Doorzoekbaar** wordt zoeken in volledige tekst mogelijk.

1. Voer `hotel-reviews-idx` in bij **Indexnaam**.

1. Accepteer de standaardselecties voor de kenmerken: **Ophalen mogelijk** en **Doorzoekbaar** voor de nieuwe velden die de pijplijn maakt.

    Uw index moet er ongeveer uitzien als op deze afbeelding. Omdat de lijst lang is, zijn niet alle velden zichtbaar op de afbeelding.

    ![Een index configureren](media/knowledge-store-create-portal/hotel-reviews-idx.png "Een index configureren")

1. Ga door naar de volgende pagina.

### <a name="step-4-configure-the-indexer"></a>Stap 4: De indexeerfunctie configureren

In deze stap van de wizard configureert u een indexeerfunctie die de gegevensbron, vaardighedenset en de index die u tijdens de vorige stappen van de wizard heeft gemaakt samenbrengt.

1. Voer `hotel-reviews-idxr` in bij **Naam**.

1. Behoud de standaardinstelling **Eén keer** voor **Planning**.

1. Klik op **Verzenden** om de indexeerfunctie uit te voeren. Gegevensextractie, indexering en toepassing van cognitieve vaardigheden vinden allemaal plaats in deze stap.

## <a name="monitor-status"></a>Status controleren

Indexering van de cognitieve vaardigheid vergt meer tijd dan gebruikelijke indexering op basis van tekst. De lijst van de indexeerfunctie moet door de wizard worden geopend op de overzichtspagina, zodat u de voortgang kunt volgen. Voor zelfnavigatie gaat u naar de overzichtspagina en klikt u op **Indexeerfuncties**.

In het Azure-portal kunt u ook het activiteitenlogboek met meldingen controleren op een klikbare statuskoppeling **Azure Cognitive Search-melding**. De uitvoering kan enkele minuten duren.

## <a name="next-steps"></a>Volgende stappen

Nu dat u uw gegevens hebt verrijkt met Cognitive Services en de resultaten naar een kennisarchief hebt geprojecteerd, kunt u Storage Explorer of Power BI gebruiken om uw verrijkte gegevensset te verkennen.

U kunt inhoud weergeven in Storage Explorer of een stap verder gaan met Power BI om inzichten te krijgen via visualisatie.

> [!div class="nextstepaction"]
> [Weergeven met Storage Explorer](knowledge-store-view-storage-explorer.md)
> [Verbinding maken met Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Als u deze oefening wilt herhalen of een ander AI-verrijkingsscenario wilt uitproberen, verwijder dan de indexeerfunctie *hotel-reviews-idxr*. Door de indexeerfunctie te verwijderen komt de teller van het aantal gratis dagelijkse transacties weer op nul te staan voor Cognitive Services-verwerking.
