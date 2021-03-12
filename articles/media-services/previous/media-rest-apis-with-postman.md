---
title: Postman configureren voor Azure Media Services REST API-aanroepen
description: In dit artikel wordt beschreven hoe u na het configureren van Postman voor Media Services REST API-aanroepen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: ef92e772085b1b89388c3f85fb3fdb91df0f6a75
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012204"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Postman configureren voor Media Services v2-REST API-aanroepen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

In deze zelf studie wordt uitgelegd hoe u **postman** zo configureert dat deze kan worden gebruikt om Azure Media Services-rest API'S (AMS) aan te roepen. In de zelf studie ziet u hoe u omgevings-en verzamelings bestanden importeert in een **bericht**. De verzameling bevat gegroepeerde definities van HTTP-aanvragen die de REST-Api's van Azure Media Services (AMS) aanroepen. Het omgevingsbestand bevat variabelen die worden gebruikt door de verzameling.

Deze omgeving en verzameling wordt in artikelen gebruikt die laten zien hoe u verschillende taken met Azure Media Services REST-Api's kunt bezorgen.

## <a name="prerequisites"></a>Vereisten

- Installeer de [Postman](https://www.getpostman.com/) REST-client als u de REST-API's wilt uitvoeren die in een aantal AMS REST-zelfstudies worden weergegeven. 

    We gebruiken **Postman** maar elk ander REST-hulpprogramma is hiervoor geschikt. Enkele andere alternatieven: **Visual Studio Code** met de REST-invoegtoepassing of **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>De omgeving configureren 

1. Maak een JSON-bestand dat de omgevings variabelen bevat die in AMS-zelf studies worden gebruikt. Noem het bestand (bijvoorbeeld **AzureMediaServices.postman_environment.jsop**). Open het bestand en plak de code die de Postman-omgeving in de [code vermelding](postman-environment.md)definieert. 
2. Open de **Postman**.
3. Selecteer rechts van het scherm de optie **Manage environment**.

    ![In de scherm afbeelding ziet u de optie omgeving beheren geselecteerd.](./media/media-services-rest-upload-files/postman-create-env.png)
4. Klik in het dialoogvenster **Manage environment** op **Import**.
5. Blader en selecteer de **AzureMediaServices.postman_environment.jsin** het bestand.
6. De **Media** -omgeving wordt toegevoegd.
7. Sluit het dialoogvenster.
8. Selecteer de **Media** -omgeving.

    ![Scherm afbeelding toont de geselecteerde media-omgeving.](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>De collectie configureren

1. Maak een JSON-bestand dat de **postman** -verzameling bevat met alle bewerkingen die nodig zijn om een bestand te uploaden naar Media Services. Noem het bestand (bijvoorbeeld **AzureMediaServicesOperations.postman_collection.jsop**). Open het bestand en plak de code die de **postman** -verzameling definieert vanuit [deze code lijst](postman-collection.md).
2. Klik op **Import** om het verzamelingbestand te importeren.
3. Kies de **AzureMediaServicesOperations.postman_collection.jsin** het bestand.

    ![Scherm afbeelding toont het dialoog venster importeren met geselecteerde bestanden selecteren.](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Volgende stappen

Bekijk het artikel [Upload assets](media-services-rest-upload-files.md) .  
