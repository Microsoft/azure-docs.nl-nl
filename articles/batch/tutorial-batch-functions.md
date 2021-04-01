---
title: 'Zelfstudie: Een Batch-taak activeren met behulp van Azure Functions'
description: 'Zelfstudie: OCR toepassen op gescande documenten wanneer ze worden toegevoegd aan een opslagblob'
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: peshultz
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: b441b4c4fcbeb089cef24c3a84fa33021e7840de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97106379"
---
# <a name="tutorial-trigger-a-batch-job-using-azure-functions"></a>Zelfstudie: Een Batch-taak activeren met behulp van Azure Functions

In deze zelfstudie leert u hoe u een Batch-taak kunt activeren met behulp van [Azure Functions](../azure-functions/functions-overview.md). We gaan een voorbeeld bekijken waarin OCR (optische tekenherkenning) via Azure Batch wordt toegepast op documenten die worden toegevoegd aan een Azure Storage-blobcontainer. Om de OCR-verwerking te stroomlijnen, gaan we een Azure-functie configureren die een Batch-taak voor OCR uitvoert elke keer dat een bestand wordt toegevoegd aan de blobcontainer. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Batch Explorer gebruiken om pools en taken te maken
> * Storage Explorer gebruiken om blobcontainers en een handtekening voor gedeelde toegang (shared access signature, SAS) te maken
> * Een Azure-functie maken die wordt geactiveerd via een blob
> * Invoerbestanden uploaden naar Storage
> * Taakuitvoering controleren
> * Uitvoerbestanden ophalen

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een Azure Batch-account en een gekoppeld Azure Storage-account. Zie [Een Batch-account maken](quick-create-portal.md#create-a-batch-account) voor meer informatie over het maken en koppelen van accounts.
* [Batch Explorer](https://azure.github.io/BatchExplorer/)
* [Azure-opslagverkenner](https://azure.microsoft.com/features/storage-explorer/)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-batch-pool-and-batch-job-using-batch-explorer"></a>Een Batch-pool en Batch-taak maken met behulp van Batch Explorer

In deze sectie gebruikt u Batch Explorer om de Batch-pool en Batch-taak te maken voor OCR-taken. 

### <a name="create-a-pool"></a>Een groep maken

1. Meld u aan bij Batch Explorer met uw Azure-referenties.
1. Maak een pool door **Pools** aan de linkerkant te selecteren en klik vervolgens op de knop **Toevoegen** boven het zoekformulier. 
    1. Kies een id en weergavenaam. In dit voorbeeld gebruiken we `ocr-pool`.
    1. Stel het schaaltype in op **Vaste grootte** en stel het toegewezen aantal knooppunten in op 3.
    1. Selecteer **Ubuntu 18.04-LTS** als besturingssysteem.
    1. Kies `Standard_f2s_v2` als de grootte van de virtuele machine.
    1. Schakel de begintaak in en voeg de opdracht `/bin/bash -c "sudo update-locale LC_ALL=C.UTF-8 LANG=C.UTF-8; sudo apt-get update; sudo apt-get -y install ocrmypdf"`toe. Stel **Standaardgebruiker taak (beheerder)** in als gebruikersidentiteit zodat begintaken opdrachten met `sudo` kunnen bevatten.
    1. Selecteer **OK**.
### <a name="create-a-job"></a>Een taak maken

1. Maak een taak in de pool door **Taken** aan de linkerkant te selecteren en klik vervolgens op de knop **Toevoegen** boven het zoekformulier. 
    1. Kies een id en weergavenaam. In dit voorbeeld gebruiken we `ocr-job`.
    1. Stel de pool in op `ocr-pool` of de naam die u voor uw pool hebt gekozen.
    1. Selecteer **OK**.


## <a name="create-blob-containers"></a>Blob-containers maken

Hier maakt u blob-containers waarmee de invoer- en uitvoer bestanden voor de OCR Batch-taak worden opgeslagen.

1. Meld u aan bij Storage Explorer met uw Azure-referenties.
1. Met het opslagaccount dat is gekoppeld aan uw Batch-account, maakt u twee blob-containers (één voor invoerbestanden, één voor uitvoerbestanden) door de stappen te volgen op [Een blob-container maken](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).

In dit voorbeeld heeft de invoercontainer de naam `input`. Dit is waar alle documenten zonder OCR aanvankelijk worden geüpload om verwerkt te worden. De uitvoercontainer heet `output` en is waar de Batch-taak verwerkte documenten schrijft met OCR.  
    * In dit voorbeeld roepen we onze invoercontainer `input`en onze uitvoercontainer`output` aan.  
    * De invoercontainer is waar alle documenten zonder OCR aanvankelijk worden geüpload.  
    * De uitvoercontainer is waar de Batch-taak documenten schrijft met OCR.  

Maak een handtekening voor gedeelte toegang voor uw uitvoercontainer in Storage Explorer. Klik hiervoor met de rechtermuisknop op de uitvoercontainer en selecteer **Handtekening voor gedeelde toegang ophalen...** Kijk bij **Schrijven** onder **Machtigingen**. Er zijn geen andere machtigingen nodig.  

## <a name="create-an-azure-function"></a>Een Azure-functie maken

In deze sectie maakt u de Azure-functie waarmee de Batch-taak voor OCR wordt geactiveerd wanneer een bestand wordt geüpload naar de invoercontainer.

1. Volg de stappen in [Een functie maken die wordt geactiveerd door Azure Blob Storage](../azure-functions/functions-create-storage-blob-triggered-function.md) om een functie te maken.
    1. Gebruik hetzelfde opslagaccount dat u heeft gekoppeld aan uw Batch-account wanneer u daarom gevraagd wordt.
    1. Kies .NET als **Runtimestack**. We schrijven onze functie in C# om gebruik te maken van de .NET SDK voor de Batch.
1. Zodra de door de blob geactiveerde functie is aangemaakt, gebruikt u de [`run.csx`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/run.csx) en [`function.proj`](https://github.com/Azure-Samples/batch-functions-tutorial/blob/master/function.proj) van GitHub in de Functie.
    * `run.csx` wordt uitgevoerd wanneer een nieuwe blob wordt toegevoegd aan uw blobcontainer voor invoer.
    * `function.proj` geeft de externe bibliotheken in uw Functiecode weer, bijvoorbeeld de Batch .NET SDK.
1. Wijzig de voorbeeldwaarden van de variabelen in de `Run()`-functie van het `run.csx`-bestand naar uw Batch- en opslagreferenties. U vindt uw Batch- en Storage-accountreferenties in het Azure-portal, in de sectie **Sleutels** van uw Batch-account.
    * Haal uw Batch- en Storage-accountreferenties op in het Azure-portal, in de sectie **Sleutels** van uw Batch-account. 

## <a name="trigger-the-function-and-retrieve-results"></a>De functie activeren en resultaten ophalen

Upload de gewenste of alle gescande bestanden uit de map [`input_files`](https://github.com/Azure-Samples/batch-functions-tutorial/tree/master/input_files) op GitHub naar uw invoercontainer. Controleer Batch Explorer om te bevestigen dat voor elk bestand een taak wordt toegevoegd aan `ocr-pool`. Na enkele seconden is het bestand met OCR toegevoegd aan de uitvoercontainer. Het bestand is dan zichtbaar en kan opgehaald worden in Storage Explorer.

Daarnaast kunt u het logboekbestand bekijken onderaan de webeditor van Azure Functions, waar u berichten zoals deze ziet wanneer u een bestand uploadt naar uw invoercontainer:

```
2019-05-29T19:45:25.846 [Information] Creating job...
2019-05-29T19:45:25.847 [Information] Accessing input container <inputContainer>...
2019-05-29T19:45:25.847 [Information] Adding <fileName> as a resource file...
2019-05-29T19:45:25.848 [Information] Name of output text file: <outputTxtFile>
2019-05-29T19:45:25.848 [Information] Name of output PDF file: <outputPdfFile>
2019-05-29T19:45:26.200 [Information] Adding OCR task <taskID> for <fileName> <size of fileName>...
```

Als u de uitvoerbestanden van Storage Explorer wilt downloaden naar uw lokale computer, selecteert u eerst de gewenste bestanden en vervolgens **Download** in het bovenste lint. 

> [!TIP]
> De gedownloade bestanden zijn doorzoekbaar wanneer u ze opent in een PDF-lezer.

## <a name="clean-up-resources"></a>Resources opschonen

Er worden kosten berekend voor de pool zolang de knooppunten actief zijn, zelfs als er geen taken zijn gepland. Verwijder de pool wanneer u deze niet meer nodig hebt. Selecteer in de accountweergave **Pools** en de naam van de pool. Selecteer vervolgens **Verwijderen**. Wanneer u de pool verwijdert, wordt ook alle taakuitvoer op de knooppunten verwijderd. De uitvoerbestanden blijven echter aanwezig in het opslagaccount. Wanneer u het Batch-account en het opslagaccount niet meer nodig hebt, kunt u deze ook verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Batch Explorer gebruiken om pools en taken te maken
> * Storage Explorer gebruiken om blobcontainers en een handtekening voor gedeelde toegang (shared access signature, SAS) te maken
> * Een Azure-functie maken die wordt geactiveerd via een blob
> * Invoerbestanden uploaden naar Storage
> * Taakuitvoering controleren
> * Uitvoerbestanden ophalen


Ga door met verkennen van de rendering-toepassingen die beschikbaar zijn via Batch Explorer in de sectie **Galerie**. Er zijn voor elke toepassing verschillende sjablonen beschikbaar, die na verloop van tijd worden uitgebreid. Voor Blender bestaan bijvoorbeeld sjablonen die één installatiekopie in tegels opsplitsen, dus delen van een installatiekopie kunnen parallel worden gerenderd.

Voor meer voorbeelden van het gebruik van de .NET API om Batch-workloads te plannen en te verwerken raadpleegt u de voorbeelden op GitHub.

> [!div class="nextstepaction"]
> [C#-voorbeelden voor Batch](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp)
