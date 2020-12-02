---
title: 'Zelfstudie: Metagegevens genereren voor Azure-afbeeldingen'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie leert u hoe u de Azure Computer Vision-service integreert in een web-app om metagegevens voor afbeeldingen te genereren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: b51234ff121071de27ec8c91425f4a769c8d833e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008783"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Zelfstudie: Computer Vision gebruiken voor het genereren van afbeeldingsmetagegevens te genereren in Azure Storage

In deze zelfstudie leert u hoe u de Azure Computer Vision-service in een web-app integreert voor het genereren van metagegevens voor geüploade afbeeldingen. Dit is nuttig voor [DAM-scenario's (Digital Asset Management)](../overview.md#computer-vision-for-digital-asset-management), bijvoorbeeld wanneer een bedrijf snel beschrijvende bijschriften of doorzoekbare trefwoorden wil genereren voor al zijn afbeeldingen.

De volledige app-handleiding vindt u in het [Azure Storage- en Cognitive Services-lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) op GitHub. In deze zelfstudie wordt alleen oefening 5 uit het lab behandeld. Misschien wilt u de volledige app maken door elke stap te volgen, maar als u alleen wilt leren hoe u Computer Vision kunt integreren in een bestaande web-app, leest u hier verder.

In deze zelfstudie ontdekt u hoe u:

> [!div class="checklist"]
> * Een Computer Vision-resource maken in Azure
> * Afbeeldingsanalyses uitvoeren op Azure Storage-afbeeldingen
> * Metagegevens koppelen aan Azure Storage-afbeeldingen
> * Afbeeldingsmetagegevens controleren met Azure Storage Explorer

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten

- [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) of hoger, met de workloads 'ASP.NET and web development' en 'Azure development' geïnstalleerd.
- Een Azure Storage-account met een blobcontainer die is ingesteld voor opslag van afbeeldingen (volg [Oefening 1 van het Azure Storage-lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) als u hulp nodig hebt met deze stap).
- Het Azure Storage Explorer-hulpprogramma (volg [oefening 2 van het Azure Storage-lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) als u hulp nodig hebt met deze stap).
- Een ASP.NET-webtoepassing met toegang tot Azure Storage (volg [oefening 3 van het Azure Storage-lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) om snel een dergelijke app te maken).

## <a name="create-a-computer-vision-resource"></a>Een Computer Vision-resource maken

U moet een Computer Vision-resource maken voor uw Azure-account. Deze resource beheert uw toegang tot de Azure Computer Vision-service. 

1. Volg de instructies in [Een Azure Cognitive Services-resource maken](../../cognitive-services-apis-create-account.md) om een Computer Vision-resource te maken.

1. Ga vervolgens naar het menu van uw resourcegroep en klik op het Computer Vision-API-abonnement dat u zojuist hebt gemaakt. Kopieer de URL onder **Eindpunt** naar een plaats waar u de URL eenvoudig en snel weer kunt ophalen. Klik vervolgens op **Toegangssleutels weergeven**.

    ![Azure Portal-pagina met de koppeling naar de eindpunt-URL en toegangssleutels gemarkeerd](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. In het volgende venster kopieert u de waarde van **sleutel 1** naar het klembord.

    ![Het dialoogvenster Sleutels beheren met de knop Kopiëren gemarkeerd](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Computer Vision-referenties toevoegen

Vervolgens voegt u de vereiste referenties toe aan uw app zodat deze toegang krijgt tot de Computer Vision-resources.

Open de ASP.NET-webtoepassing in Visual Studio en ga naar het bestand **Web.config** in de hoofdmap van het project. Voeg de volgende instructies toe aan het gedeelte `<appSettings>` van het bestand. Vervang `VISION_KEY` door de sleutel die u in de vorige stap hebt gekopieerd en `VISION_ENDPOINT` door de URL die u in de stap daarvoor hebt opgeslagen.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

Klik vervolgens in Solution Explorer met de rechtermuisknop op het project en gebruik de opdracht **NuGet-pakketten beheren** om het pakket **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** te installeren. Dit pakket bevat de typen die nodig zijn voor het aanroepen van de Computer Vision-API.

## <a name="add-metadata-generation-code"></a>Code voor het genereren van metagegevens toevoegen

Voeg vervolgens de code toe waarmee gebruik wordt gemaakt van de Computer Vision-service om metagegevens te maken voor afbeeldingen. Deze stappen zijn van toepassing op de ASP.NET-app in het lab, maar u kunt ze ook aanpassen aan uw eigen app. Het is op dit punt belangrijk dat u over een ASP.NET-webtoepassing beschikt waarmee u afbeeldingen kunt uploaden naar een Azure Storage-container, waarmee u er afbeeldingen uit kunt lezen en waarmee u deze kunt weergeven. Als u niet zeker weet of dit de juiste stap is, volgt u [oefening 3 van het Azure Storage-lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3). 

1. Open het bestand *HomeController.cs* in de map **Controllers** van het project en voeg de volgende `using` instructies toe aan de bovenzijde van het bestand:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Ga vervolgens naar de methode **Uploaden**. Met deze methode worden afbeeldingen geconverteerd en naar de blob-opslag geüpload. Voeg de volgende code in achter het blok dat begint met `// Generate a thumbnail` (of na afloop van het aanmaakproces voor de afbeeldingsblob). Met deze code wordt de blob met de afbeelding (`photo`) gezocht en wordt Computer Vision gebruikt voor het genereren van een beschrijving voor de betreffende afbeelding. De Computer Vision-API maakt ook een lijst trefwoorden die van toepassing zijn op de afbeelding. De gegenereerde beschrijving en de trefwoorden worden opgeslagen in de metagegevens van de blob zodat ze later kunnen worden opgehaald.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. Ga vervolgens naar de methode **Index** in hetzelfde bestand. Met deze methode worden de opgeslagen afbeeldingsblobs in de blob-doelcontainer opgesomd (als **IListBlobItem**-instanties) en worden deze doorgegeven aan de toepassingsweergave. Vervang het blok `foreach` in de methode door de volgende code. Deze code roept **CloudBlockBlob.FetchAttributes** aan om de metagegevens die aan elke blob zijn gekoppeld op te halen. De door de computer gegenereerde beschrijving (`caption`) wordt opgehaald uit de metagegevens en toegevoegd aan het object **BlobInfo**; dit wordt vervolgens doorgegeven aan de weergave.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>De app testen

Sla de wijzigingen in Visual Studio op en druk op **Ctrl+F5** om de toepassing in uw browser te starten. Gebruik de app om enkele afbeeldingen te uploaden, ofwel vanuit de map 'photos' in de labresources ofwel vanuit uw eigen map. Wanneer u de muisaanwijzer op een van de afbeeldingen in de weergave houdt, wordt er een knopinfovenster weergegeven. Hierin ziet u het door de computer gegenereerde bijschrift van de afbeelding.

![Het door de computer gegenereerde bijschrift](../Images/thumbnail-with-tooltip.png)

Als u alle gekoppelde metagegevens wilt bekijken, gebruikt u Azure Storage Explorer om de opslagcontainer te gebruiken die u voor afbeeldingen gebruikt. Klik met de rechtermuisknop op een van de blobs in de container en selecteer **Eigenschappen**. In het dialoogvenster ziet u een lijst met sleutel-waardeparen. De door de computer gegenereerde afbeeldingsbeschrijving wordt opgeslagen in het item Bijschrift en de zoektrefwoorden worden opgeslagen in Tag0, Tag1, enzovoort. Wanneer u klaar bent, klikt u op **Annuleren** om het dialoogvenster te sluiten.

![Het dialoogvenster Eigenschappen van afbeelding, met een lijst van tags met metagegevens](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u aan de web-app wilt blijven werken, ziet u het gedeelte [Volgende stappen](#next-steps). Als u deze toepassing niet wilt blijven gebruiken, verwijdert u alle app-specifieke resources. Als u resources wilt verwijderen, kunt u de resourcegroep met uw Azure Storage-abonnement en Computer Vision-resource verwijderen. Hiermee worden het opslagaccount, de ernaar geüploade blobs en de App Service-resource die nodig is om verbinding te maken met de ASP.NET-web-app verwijderd. 

Als u de resourcegroep wilt verwijderen, opent u het tabblad **Resourcegroepen** in de portal, gaat u naar de resourcegroep die u hebt gebruikt voor dit project en klikt u op **Resourcegroep verwijderen** bovenaan de weergave. U wordt gevraagd om de naam van de resourcegroep in te voeren om te bevestigen dat u deze wilt verwijderen. Na het verwijderen kunnen resourcegroepen namelijk niet meer worden hersteld.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de Azure Computer Vision-service ingesteld in een bestaande web-app om automatisch bijschriften en trefwoorden te genereren voor blobafbeeldingen nadat ze zijn geüpload. Zie nu oefening 6 uit het Azure Storage-lab om te ontdekken hoe u zoekfunctionaliteit toevoegt aan uw web-app. Hierbij wordt gebruikgemaakt van de zoektrefwoorden die de Computer Vision-service genereert.

> [!div class="nextstepaction"]
> [Zoekfunctionaliteit toevoegen aan uw app](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
