---
title: 'Zelfstudie: Een statische website hosten op Blob-opslag - Azure Storage'
description: Bekijk informatie over het configureren van een opslagaccount voor het hosten van een statische website en het implementeren van een statische website op Azure Storage.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 36cdaa813e0eccb23563301052aee268ab61533a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94888501"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Zelfstudie: Een statische website hosten op Blob-opslag

In deze zelfstudie leert u hoe u een statische website kunt bouwen en implementeren op Azure Storage. Wanneer u klaar bent, hebt u een statische website die openbaar toegankelijk is voor gebruikers. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het hosten van een statische website configureren
> * Een 'Hallo wereld'-website implementeren

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> Zorg ervoor dat u een standaardopslagaccount van het type v2 voor algemeen gebruik maakt. Statische websites zijn niet beschikbaar in een ander type opslagaccount.

In deze zelfstudie wordt gebruikgemaakt van [Visual Studio Code](https://code.visualstudio.com/download), een gratis hulpprogramma voor programmeurs, waarmee de statische website kan worden gebouwd en in een Azure Storage-account kan worden geïmplementeerd.

Nadat u Visual Studio Code hebt geïnstalleerd, installeert u de previewextensie van Azure Storage. Deze extensie integreert de beheerfunctionaliteit van Azure Storage met Visual Studio Code. U gaat de extensie gebruiken om uw statische website te implementeren in Azure Storage. De extensie installeren:

1. Start Visual Studio Code.
2. Klik op **Extensies** op de werkbalk. Zoek naar *Azure Storage* en selecteer de **Azure Storage**-extensie uit de lijst. Klik vervolgens op de knop **Installeren** om de extensie te installeren.

    ![De Azure Storage-extensie installeren in VS Code](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/) om aan de slag te gaan.

## <a name="configure-static-website-hosting"></a>Het hosten van een statische website configureren

De eerste stap is het configureren van uw opslagaccount voor het hosten van een statische website in de Azure-portal. Wanneer u uw account configureert voor het hosten van een statische website, maakt Azure Storage automatisch een container met de naam *$web*. De *$web*-container bevat de bestanden voor uw statische website. 

1. Open de [Azure-portal](https://portal.azure.com/) in uw webbrowser. 
1. Zoek uw opslagaccount en geef het accountoverzicht weer.
1. Selecteer **Statische website** om de configuratiepagina voor statische websites weer te geven.
1. Selecteer **Ingeschakeld** om het hosten van statische websites in te schakelen voor het opslagaccount.
1. Geef in het veld **Naam van het indexdocument** een standaardindexpagina van *index.html* op. De standaardindexpagina wordt weergegeven wanneer een gebruiker naar de hoofdmap van uw statische website navigeert.  
1. Geef in het veld **Fout bij het documentpad** een standaardfoutpagina van *404. html* op. De standaardfoutpagina wordt weergegeven wanneer een gebruiker probeert te navigeren naar een pagina die niet in uw statische website bestaat.
1. Klik op **Opslaan**. Het eindpunt voor uw statische website wordt nu weergegeven in de Azure-portal. 

    ![Het hosten van een statische website inschakelen voor een opslagaccount](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Een 'Hallo wereld'-website implementeren

Maak vervolgens een 'Hallo wereld'-webpagina met Visual Studio Code en implementeer deze in de statische website die wordt gehost in uw Azure Storage-account.

1. Maak een lege map met de naam *MijnWebsite* op uw lokale bestandssysteem. 
1. Start Visual Studio Code en open de map die u zojuist hebt gemaakt in het deelvenster **Explorer**.

    ![De map openen in Visual Studio Code](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Maak het standaardindexbestand in de map *MijnWebsite* en noem het bestand *index.html*.

    ![Het standaardindexbestand maken in Visual Studio Code](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Open *index.html* in de editor, plak de volgende tekst in het bestand en sla het bestand op:

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>Hello World!</h1>
      </body>
    </html>
    ```

1. Maak het standaardfoutbestand en noem dat *404. html*.
1. Open *404.html* in de editor, plak de volgende tekst in het bestand en sla het bestand op:

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>404</h1>
      </body>
    </html>
    ```

1. Klik met de rechtermuisknop onder de map *MijnWebsite* in het deelvenster **Explorer** en selecteer **Implementeren in statische website...** om uw website te implementeren. U wordt gevraagd u aan te melden bij Azure om een lijst met abonnementen op te halen.

1. Selecteer het abonnement met het opslagaccount waarvoor u het hosten van een statische website hebt ingeschakeld. Selecteer vervolgens het opslagaccount wanneer u hierom wordt gevraagd.

Visual Studio Code uploadt nu uw bestanden naar uw webeindpunt en geeft de statusbalk voor succes weer. Start de website om deze te bekijken in Azure.

U hebt de zelfstudie voltooid en een statische website in Azure geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u uw Azure Storage-account voor het hosten van een statische website configureert en hoe u een statische website op een Azure-eindpunt maakt en implementeert.

Vervolgens leert u hoe u een aangepast domein configureert voor uw statische website.

> [!div class="nextstepaction"]
> [Een aangepast domein toewijzen aan een Azure Blob Storage-eindpunt](storage-custom-domain-name.md)
