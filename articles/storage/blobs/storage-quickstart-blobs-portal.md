---
title: 'Quickstart: een blob maken met Azure Portal'
titleSuffix: Azure Storage
description: In deze snelstart gebruikt u Azure Portal in object-opslag (Blob). Vervolgens gebruikt u Azure Portal om een blob in Azure Storage te uploaden, een blob te downloaden en de blobs in een container te vermelden.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: tamram
ms.openlocfilehash: edadcc4025913052e048ea94d47cac253e4bcd1a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95523328"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Snelstart: Blobs uploaden, downloaden en vermelden met Azure Portal

In deze snelstartgids leert u hoe u [Azure Portal](https://portal.azure.com/) gebruikt om een container te maken in Azure Storage, en om blok-blobs in deze container te uploaden en te downloaden.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>Een container maken

Als u een container wilt maken in Azure Portal, volgt u deze stappen:

1. Navigeer naar het nieuwe opslagaccount in Azure Portal.
2. Schuif in het linkermenu voor het opslagaccount naar de sectie **Blob service**. Selecteer vervolgens **Containers**.
3. Selecteer de knop **+ Container**.
4. Typ een naam voor de nieuwe container. De containernaam mag alleen kleine letters bevatten, moet beginnen met een letter of cijfer en mag alleen letters, cijfers en het streepje (-) bevatten. Zie [Naamgeving van en verwijzen naar containers, blobs en metagegevens](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de namen van containers en blobs.
5. Stel het niveau van openbare toegang tot de container in. Het standaardniveau is **Persoonlijk (geen anonieme toegang)**.
6. Selecteer **OK** om de container te maken.

    :::image type="content" source="media/storage-quickstart-blobs-portal/create-container.png" alt-text="Schermopname van het maken van een container in Azure Portal":::

## <a name="upload-a-block-blob"></a>Een blok-blob uploaden

Blok-blobs bestaan uit blokken met gegevens die worden samengevoegd tot een blob. In de meeste gebruiksscenario's voor Blob-opslag worden blok-blobs gebruikt. Blok-blobs zijn zeer geschikt om tekst en binaire gegevens op te slaan in de cloud, zoals bestanden, afbeeldingen en video's. Deze snelstartgids laat zien hoe u kunt werken met blok-blobs.

Als u een blok-blob wilt uploaden naar de nieuwe container in Azure Portal, volgt u deze stappen:

1. Navigeer in Azure Portal naar de container die u in de vorige sectie hebt gemaakt.
1. Selecteer de container om een lijst weer te geven met de blobs die deze bevat. Deze container is nieuw en bevat dus nog geen blobs.
1. Selecteer de knop **Uploaden** om de blade voor uploaden te openen en blader door het lokale bestandssysteem om een bestand te vinden dat u wilt uploaden als een blok-blob. U kunt de sectie **Geavanceerd** eventueel uitvouwen om andere instellingen voor de uploadbewerking te configureren.

    :::image type="content" source="media/storage-quickstart-blobs-portal/upload-blob.png" alt-text="Schermopname van het uploaden van een blob vanaf de lokale schijf via de Azure Portal":::

1. Selecteer de knop **Uploaden** om de blob te uploaden.
1. U kunt op deze manier zoveel blobs uploaden als u wilt. De nieuwe blobs worden nu weergegeven in de container.

## <a name="download-a-block-blob"></a>Een blok-blob downloaden

U kunt een blok-blob downloaden om deze weer te geven in de browser of op te slaan op het lokale bestandssysteem. Als u een blok-blob wilt downloaden, volgt u deze stappen:

1. Navigeer naar de lijst met blobs die u in de vorige sectie hebt geüpload.
1. Klik met de rechtermuisknop op de blob die u wilt downloaden en selecteer **Downloaden**.

    :::image type="content" source="media/storage-quickstart-blobs-portal/download-blob.png" alt-text="Schermopname van het downloaden van een blob in Azure Portal":::

## <a name="delete-a-block-blob"></a>Een blok-blob verwijderen

Als u een of meer blobs wilt verwijderen in de Azure Portal volgt u deze stappen:

1. Navigeer in de Azure Portal naar de container.
1. Geef de lijst met blobs in de container weer.
1. Gebruik het selectievakje om een of meer blobs uit de lijst te selecteren.
1. Selecteer de knop **Verwijderen** om de geselecteerde blobs te verwijderen.
1. Bevestig de verwijdering in het dialoogvenster en geef aan of u ook momentopnamen van blobs wilt verwijderen.

:::image type="content" source="media/storage-quickstart-blobs-portal/delete-blobs.png" alt-text="Schermopname die laat zien hoe u blobs verwijdert uit de Azure Portal":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle resources wilt verwijderen die u in deze snelstartgids hebt gemaakt, verwijdert u gewoon de container. Alle blobs in de container worden dan ook verwijderd.

Ga als volgt te werk om de container te verwijderen:

1. Navigeer in Azure Portal naar de lijst met containers in uw opslagaccount.
1. Selecteer de container die u wilt verwijderen.
1. Klik op de knop **Meer** (**...**) en selecteer **Verwijderen**.
1. Bevestig dat u de container wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een container maakt en een blob upload met Azure Portal. Als u meer wilt leren over Blob-opslag vanuit een web-app, gaat u verder naar een zelfstudie waarin u leert hoe u installatiekopieën uploadt naar een opslagaccount.

> [!div class="nextstepaction"]
> [Zelfstudie: Afbeeldingsgegevens uploaden in de cloud met Azure Storage](storage-upload-process-images.md)