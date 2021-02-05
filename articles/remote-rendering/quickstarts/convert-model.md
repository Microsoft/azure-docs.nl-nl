---
title: Een model converteren
description: Quickstart die de stappen laat zien om een aangepast model te converteren.
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: c9b5d525954e7f0742cd13fe4d64a73df64ea854
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594464"
---
# <a name="quickstart-convert-a-model-for-rendering"></a>Quickstart: Een model converteren voor rendering

In [Quickstart: een model weergeven met Unity](render-model.md) hebt u geleerd hoe u het voorbeeldproject van Unity kunt gebruiken om een ingebouwd model te weer te geven. Deze gids leert u om uw eigen modellen te converteren.

U leert het volgende:

> [!div class="checklist"]
>
> * Een Azure Blob Storage-account instellen voor invoer en uitvoer
> * Een 3D-model uploaden en converteren om het te gebruiken met Azure Remote Rendering
> * Het omgezette 3D-model toevoegen aan een toepassing voor rendering

## <a name="prerequisites"></a>Vereisten

* [Quickstart: Een model weergeven met Unity](render-model.md)
* Voor de conversie met behulp van het PowerShell-script: Azure PowerShell installeren [(documentatie)](/powershell/azure/)
  * Open een PowerShell met beheerdersrechten
  * Voer dit uit: `Install-Module -Name Az -AllowClobber`

## <a name="overview"></a>Overzicht

De renderer op de server kan niet rechtstreeks werken met indelingen voor bronmodellen zoals FBX of GLTF. In plaats daarvan moet het model een eigen binaire indeling hebben.
De conversieservice neemt modellen van de Azure Blob-opslag en schrijft geconverteerde modellen terug naar een opgegeven Azure Blob-opslagcontainer.

U hebt de volgende zaken nodig:

* Een Azure-abonnement
* Een 'StorageV2'-account in uw abonnement
* Een Blob Storage-container maken voor uw invoermodel
* Een Blob Storage-container voor uw uitvoergegevens
* Een model dat u kunt omzetten, zie [voorbeeldmodellen](../samples/sample-model.md)
  * Overzicht van [ondersteunde bronindelingen](../how-tos/conversion/model-conversion.md#supported-source-formats) weergeven
  * Als u het voorbeeldscript voor omzetten wilt gebruiken, dient u een invoermap voor te bereiden die het model en alle externe afhankelijkheden (zoals externe patronen of geometrie) bevat

## <a name="azure-setup"></a>Azure-configuratie

Als u nog geen account hebt, ga dan naar [https://azure.microsoft.com/get-started/](https://azure.microsoft.com/get-started/), klik op de optie voor een gratis account en volg de instructies.

Zodra u een Azure-account hebt, gaat u naar [https://ms.portal.azure.com/#home](https://ms.portal.azure.com/#home).

### <a name="storage-account-creation"></a>Maken van een opslagaccount

Als u blob-opslag wilt maken, hebt u eerst een opslagaccount nodig.
Klik daarvoor op de knop 'Een resource maken':

![Azure: resource toevoegen](media/azure-add-a-resource.png)

Kies in het volgende scherm **Opslag** aan de linkerkant en vervolgens **Opslagaccount - blob, bestand, tabel, wachtrij** in de volgende kolom:

![Azure - opslag toevoegen](media/azure-add-storage.png)

Als u op deze knop klikt, wordt het volgende scherm weergegeven waar u de opslageigenschappen kunt invullen:

![Azure-configuratie](media/azure-setup1.png)

Vul het formulier als volgt in:

* Maak een nieuwe resourcegroep via de koppeling onder de vervolgkeuzelijst en geef deze de naam **ARR_Tutorial**
* Geef bij **Naam opslagaccount** een unieke naam in. **Deze naam moet wereldwijd uniek zijn**, anders wordt er een bericht weergegeven dat de naam al in gebruik is. In deze quickstart kiezen we de naam **arrtutorialstorage**. U moet deze overal waar hij gebruikt wordt in deze quickstart vervangen door uw eigen naam.
* Selecteer een **locatie** dicht bij u in de buurt. Het is best om dezelfde locatie te kiezen die u heeft gebruikt om de rendering in de andere quickstart in te stellen.
* **Prestatie** ingesteld op 'Standaard'
* **Soort account** ingesteld op 'StorageV2 (algemeen gebruik v2)'
* **Replicatie** ingesteld op 'Geografisch redundante opslag met leestoegang (RA-GRS)'
* **Toegangslaag** ingesteld op 'Dynamisch'

De eigenschappen in andere tabbladen moeten niet gewijzigd worden. U kunt dus doorgaan met **'Beoordelen en maken'** en de stappen volgen om de installatie te voltooien.

De website geeft u nu informatie over de voortgang van uw implementatie, om uiteindelijk 'Uw implementatie is voltooid' weer te geven. Klik op de knop **'Naar resource gaan'** om de volgende stappen uit te voeren:

![Azure Storage is aangemaakt](./media/storage-creation-complete.png)

### <a name="blob-storage-creation"></a>Aanmaken Blob Storage

Vervolgens hebben we twee blobcontainers nodig: één voor invoer en één voor uitvoer.

Gebruik de bovenstaande knop **'Naar resource gaan'** om naar de pagina te gaan met aan de linkerzijde een deelvenster waarin zich een lijstmenu bevindt. Klik in de lijst onder de **'Blob-service'** op de knop **'Containers'** :

![Azure: Containers toevoegen](./media/azure-add-containers.png)

Druk op de knop **'+ Container'** om de **invoer** blob-opslagcontainer te maken.
Gebruik de volgende instellingen bij het maken:
  
* Naam = arrinput
* Openbaar toegangsniveau = Privé

Nadat de container is gemaakt, klikt u nogmaals op **+ Container** en herhaalt u deze instellingen voor de **uitvoer** container:

* Naam = arroutput
* Openbaar toegangsniveau = Privé

U hebt nu twee blob-opslagcontainers:

![Instelling Blob Storage](./media/blob-setup.png)

## <a name="run-the-conversion"></a>De conversie uitvoeren

Er zijn drie verschillende manieren om een modelconversie te activeren:

### <a name="1-conversion-via-the-arrt-tool"></a>1. Conversie via het ARRT-hulpprogramma

Er is een [op de gebruikersinterface gebaseerd hulpprogramma met de naam ARRT](./../samples/azure-remote-rendering-asset-tool.md) om conversies te starten en te communiceren met het weergegeven resultaat.
![ARRT](./../samples/media/azure-remote-rendering-asset-tool.png "Schermopname van ARRT")

### <a name="2-conversion-via-a-powershell-script"></a>2. Conversie via een PowerShell-script

We bieden een hulpscript aan om de conversieservice voor assets gemakkelijker aan te roepen. Deze bevindt zich in de map *Scripts* en heet **Conversion.ps1**.

Dit script

1. uploadt alle bestanden in een bepaalde map van een lokale schijf naar de opslagcontainer voor invoer
1. roept de [REST API voor conversie van assets](../how-tos/conversion/conversion-rest-api.md) aan, die vervolgens de gegevens ophaalt vanuit de opslagcontainer voor invoer en een conversie begint, die vervolgens een conversie-id retourneert
1. bevraagt de conversiestatus van de API met de verkregen conversie-id tot het conversieproces voltooid of mislukt is
1. haalt een koppeling naar de geconverteerde asset in de uitvoeropslag op

Het script leest de configuratie uit het bestand *Scripts\arrconfig.json*. Open dit JSON-bestand in een teksteditor.

```json
{
    "accountSettings": {
        "arrAccountId": "8*******-****-****-****-*********d7e",
        "arrAccountKey": "R***************************************l04=",
        "region": "<your-region>"
    },
    "renderingSessionSettings": {
        "vmSize": "standard",
        "maxLeaseTime": "1:00:00"
    },
    "assetConversionSettings": {
        "localAssetDirectoryPath": "D:\\tmp\\robot",
        "resourceGroup": "ARR_Tutorial",
        "storageAccountName": "arrexamplestorage",
        "blobInputContainerName": "arrinput",
        "inputFolderPath": "robotConversion",
        "inputAssetPath": "robot.fbx",
        "blobOutputContainerName": "arroutput",
        "outputFolderPath":"converted/robot",
        "outputAssetFileName": "robot.arrAsset"
    }
}
```

De configuratie binnen de groep **accountSettings** (account-id en -sleutel) moet overeenkomen met de referenties in de [quickstart 'Een model weergeven met Unity'](render-model.md).

Zorg ervoor dat u in de groep **assetConversionSettings** **resourceGroup**, **blobInputContainerName** en **blobOutputContainerName** aanpast zoals hierboven te zien is.
De waarde **arrtutorialstorage** moet worden vervangen door de unieke naam die u hebt gekozen toen u het opslagaccount maakte.

Wijzig **localAssetDirectoryPath** zodat deze verwijst naar de map op uw schijf die het model bevat dat u wilt converteren. Zorg ervoor dat u backslashes ('\\') tussen escape-tekens plaatst in het pad met dubbele backslashes ('\\\\').

Alle gegevens van het pad dat is opgegeven in **localAssetDirectoryPath** worden geüpload naar de blobcontainer **blobInputContainerName** onder een subpad opgegeven door **inputFolderPath**. In de bovenstaande voorbeeldconfiguratie wordt de inhoud van de map 'D:\\tmp\\robot' geüpload naar de blobcontainer 'arrinput' van het opslagaccount 'arrtutorialstorage' onder het pad 'robotConversion'. Bestaande bestanden worden overschreven.

Verander **inputAssetPath** naar het pad van het model dat omgezet moet worden - het pad is afhankelijk van localAssetDirectoryPath. Gebruik '/' in plaats van '\\' als scheidingsteken. Voor een bestand robot.fbx, dat zich in D:\\tmp\\robot bevindt, gebruikt u robot.fbx.

Zodra het model is omgezet, wordt het teruggeschreven naar de opslagcontainer die is opgegeven door **blobOutputContainerName**. U kunt een subpad opgeven met het optionele **outputFolderPath**. In het bovenstaande voorbeeld wordt het resulterende robot.arrAsset gekopieerd naar de blobcontainer voor uitvoer onder converted/robot.

De configuratie-instelling **outputAssetFileName** bepaalt de naam van de omgezette asset. De parameter is optioneel en indien hij niet gebruikt wordt, zal de bestandsnaam voor de uitvoer worden afgeleid van de bestandsnaam voor invoer.

Open een PowerShell en controleer of u de *Azure PowerShell* hebt geïnstalleerd zoals vermeld in de [vereisten](#prerequisites). Meld u vervolgens aan bij uw abonnement met de volgende opdracht en volg de instructies op het scherm:

```PowerShell
Connect-AzAccount
```

> [!NOTE]
> Als uw organisatie meerdere abonnementen heeft, moet u mogelijk de argumenten SubscriptionId en Tenant opgeven. Meer informatie vindt u in de [documentatie bij Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Schakel naar de `azure-remote-rendering\Scripts`-directory en voer het conversiescript uit:

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Deze lijst ziet er ongeveer zo uit: ![Conversion.ps1](./media/successful-conversion.png)

### <a name="3-conversion-via-api-calls"></a>3. Conversie via API-aanroepen

Zowel de C# als de C++ API bieden een invoerpunt om te communiceren met de service:
* [C# RemoteRenderingClient. StartAssetConversionAsync ()](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient.startassetconversionasync)
* [C++ RemoteRenderingClient:: StartAssetConversionAsync ()](/cpp/api/remote-rendering/remoterenderingclient#startassetconversionasync)


## <a name="insert-new-model-into-quickstart-sample-app"></a>Nieuw model in Quickstart-voorbeeldapp invoegen

Het conversiescript genereert een *SAS*-URI (Shared Access Signature) voor het omgezette model. U kunt deze URI nu als **Modelnaam** kopiëren naar de quickstart-voorbeeldapp (zie [Quickstart: Een model weergeven met Unity](render-model.md)).

![Model vervangen in Unity](./media/replace-model-in-unity.png)

 Het voorbeeld zou nu moeten laden en uw aangepast model moeten weergeven!

## <a name="optional-re-creating-a-sas-uri"></a>Optioneel: Een SAS-URI opnieuw maken

De SAS-URI die door het conversiescript is gemaakt, is slechts voor 24 uur geldig. Wanneer het model is verlopen, moet u het echter niet opnieuw converteren. In plaats daarvan kunt u in het portal een nieuwe SAS maken, zoals wordt beschreven in de volgende stappen:

1. Ga naar de [Azure-portal](https://www.portal.azure.com)
1. Klik op de resource van uw **Opslagaccount**: ![Schermopname waarin de geselecteerde Storage-accountresource is gemarkeerd.](./media/portal-storage-accounts.png)
1. Klik in het volgende scherm op **Storage Explorer** in het deelvenster links en zoek uw uitvoermodel ( *.arrAsset*-bestand) in de Blob Storage-container *arroutput*. Klik met de rechtermuisknop op het bestand en selecteer **Shared Access Signature ophalen** in het contextmenu: ![Signature Access](./media/portal-storage-explorer.png)
1. Er wordt een nieuw scherm geopend waarin u een verloopdatum kunt selecteren. Druk op **Maken** en kopieer de URI die in het volgende dialoogvenster wordt weergegeven. Deze nieuwe URI vervangt de tijdelijke URI die het script heeft gemaakt.

## <a name="next-steps"></a>Volgende stappen

Nu u de basisprincipes kent, kunt u onze zelfstudies ontdekken om meer uitgebreide kennis op te doen.

Als u meer wilt weten over modelconversie, bekijk dan [de REST API voor modelconversie](../how-tos/conversion/conversion-rest-api.md).

> [!div class="nextstepaction"]
> [Zelfstudie: Extern gegenereerde modellen bekijken](../tutorials/unity/view-remote-models/view-remote-models.md)