---
title: Gezichten in Azure Media Services v3 API redigeren | Microsoft Docs
description: Azure Media Services V3 biedt een voor beeld van een gezichts detectie en redactie, waarmee u een video bestand kunt verzenden, gezichten kunt detecteren en vervaging in één enkele gecombineerde Pass kunt Toep assen, of via een bewerking met twee fasen die kan worden bewerkt. In dit artikel wordt beschreven hoe u gezichten kunt redigeren met de voor instelling voor face detector in de V3 API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 6db93aa369366936c90446c41406eafe9ee6e414
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630398"
---
# <a name="redact-faces-with-the-face-detector-preset"></a>Gezichten met de voor instelling gezichts detector redigeren

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services v3 API bevat een voor beeld van een face-detector die schaal bare detectie en redactie (vervaging) in de Cloud biedt. Met gezichts redactie kunt u uw video aanpassen zodat u de gezichten van geselecteerde personen kunt vervagen. U kunt de gezichts redactie service gebruiken in de scenario's voor open bare veiligheid en nieuws media. Een paar minuten van beeld materiaal dat meerdere gezichten bevat, kan uren in beslag nemen om hand matig te worden geredigeerd, maar met deze vooraf ingestelde voor instelling hoeft het gezichts proces slechts enkele eenvoudige stappen te worden uitgevoerd.

In dit artikel vindt u informatie over de **voor instelling face detector** en ziet u hoe u deze kunt gebruiken met Azure Media Services SDK voor .net.

## <a name="compliance-privacy-and-security"></a>Naleving, privacy en beveiliging
 
Als belang rijke herinnering moet u zich houden aan alle toepasselijke wetten in het gebruik van Analytics in Azure Media Services. U moet Azure Media Services of een andere Azure-service niet gebruiken op een manier die de rechten van anderen schendt. Voordat u Video's, met inbegrip van biometrische gegevens, naar de Azure Media Services-service voor verwerking en opslag uploadt, moet u alle juiste rechten hebben, inclusief alle toepasselijke mede werkers, van de personen in de video. Voor meer informatie over naleving, privacy en beveiliging in Azure Media Services, de Azure [Cognitive Services-voor waarden](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/). Raadpleeg de [privacyverklaring](https://privacy.microsoft.com/PrivacyStatement)van micro soft, het OST-bericht ( [Online Services-voor waarden](https://www.microsoft.com/licensing/product-licensing/products) ) en de [addendum op gegevens verwerking](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA") voor meer informatie over de privacy van micro soft en het afhandelen van uw gegevens. Meer informatie over privacy, waaronder het bewaren van gegevens, verwijderen/vernietigen, is beschikbaar in de OST en [hier](../video-indexer/faq.md). Door Azure Media Services te gebruiken, gaat u akkoord met de Cognitive Services voor waarden, de OST, DPA en de privacyverklaring


## <a name="face-redaction-modes"></a>Modus voor redactie van gezicht

Gezichts redactie werkt door het detecteren van gezichten in elk frame van de video en bij het volgen van het gezichts object in de loop van de tijd, zodat dezelfde persoon van andere hoeken ook kan worden vervaagd. Het geautomatiseerde redactie proces is complex en vervagt niet altijd elk gezicht 100% gegarandeerd. Daarom kan de voor instelling worden gebruikt als twee fasen om de kwaliteit en nauw keurigheid van het vervagen in een bewerkings stadium te verbeteren voordat het bestand wordt verzonden voor de laatste vervagings fase. 

Naast een volledig automatische **gecombineerde** modus kunt u met de werk stroom voor twee slagen de gezichten kiezen die u wilt vervagen (of niet vervagen) via een lijst met gezichts-id's. Als u een wille keurige per kader aanpassing wilt maken, gebruikt de vooraf ingestelde meta gegevens een bestand in JSON-indeling als invoer voor de tweede fase. Deze werk stroom is opgesplitst in de modi **analyseren** en **redactie** .

U kunt de twee modi ook eenvoudig combi neren in één fase waarmee beide taken in één taak worden uitgevoerd. deze modus heet **gecombineerd**.  In dit artikel wordt in de voorbeeld code getoond hoe u de vereenvoudigde **gecombineerde** modus voor enkelvoudige Pass kunt gebruiken op een bron bestand voor een voor beeld.

### <a name="combined-mode"></a>Gecombineerde modus

Dit produceert een geredigeerd MP4-video bestand in één keer zonder hand matige bewerking van het JSON-bestand dat vereist is. De uitvoer in de map Assets voor de taak is een enkel MP4-bestand dat vage gezichten bevat met het geselecteerde vervagings effect. Gebruik de eigenschap Resolution die is ingesteld op **SourceResolution** om de beste resultaten voor redactie te krijgen.

| Fase | Bestandsnaam | Notities |
| --- | --- | --- |
| Invoer Asset |"ignite-sample.mp4" |Video in WMV-, MOV-of MP4-indeling |
| Vooraf ingestelde configuratie |Face detector configuratie | **modus**: FaceRedactorMode. gecombineerd,  **blurType**: blurType. med, **oplossing**: AnalysisResolution. SourceResolution |
| Uitvoer activum |"ignite-redacted.mp4 |Video met vervagend effect toegepast op gezichten |

### <a name="analyze-mode"></a>Analyse modus

De **analyse** fase van de werk stroom met twee slagen neemt een video-invoer en produceert een JSON-bestand met een lijst met de locatie van het gezicht, het gezichts-id en de JPG-afbeeldingen van elk gedetecteerd gezicht. 

| Fase | Bestandsnaam | Notities |
| --- | --- | --- |
| Invoer Asset |"ignite-sample.mp4" |Video in WMV-, MPV-of MP4-indeling |
| Vooraf ingestelde configuratie |Face detector configuratie |**modus**: FaceRedactorMode. analyseren, **oplossing**: AnalysisResolution. SourceResolution|
| Uitvoer activum |ignite-sample_annotations.jsop |Aantekening gegevens van gezichts locaties in JSON-indeling. Dit kan worden bewerkt door de gebruiker om de vervagings kaders te wijzigen. Zie het voor beeld hieronder. |
| Uitvoer activum |foo_thumb% 06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Een bijgesneden jpg van elk gedetecteerd gezicht, waarbij het nummer de labelId van het gezicht aangeeft |

#### <a name="output-example"></a>Uitvoer voorbeeld

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>Modus redactie (vervagen)

De tweede fase van de werk stroom vergt een groter aantal invoer waarden die in één activum moeten worden gecombineerd.

Dit omvat een lijst met Id's voor vervagen, de oorspronkelijke video en de JSON van de aantekeningen. In deze modus wordt de annotaties gebruikt om vervaging toe te passen op de invoer video.

De uitvoer van de analyse fase bevat niet de oorspronkelijke video. De video moet worden geüpload naar de invoer Asset voor de redactie modus taak en als primair bestand zijn geselecteerd.

| Fase | Bestandsnaam | Notities |
| --- | --- | --- |
| Invoer Asset |"ignite-sample.mp4" |Video in WMV-, MPV-of MP4-indeling. Dezelfde video als in stap 1. |
| Invoer Asset |"ignite-sample_annotations.jsop" |aantekeningen bestand met meta gegevens van fase één, met optionele wijzigingen als u de gezichten wilt wijzigen. Dit moet worden bewerkt in een externe toepassing, code of tekst editor. |
| Invoer Asset | "ignite-sample_IDList.txt" (optioneel) | Optionele, door de nieuwe gescheiden lijst met gezichts-Id's voor redactie. Als dit veld leeg blijft, wordt de vervaging toegepast op alle gezichten in de bron. U kunt de lijst gebruiken om ervoor te kiezen om specifieke gezichten niet te vervagen. |
| Face detector vooraf instellen  |Vooraf ingestelde configuratie  | **modus**: FaceRedactorMode. redactie, **blurType**: blurType. med |
| Uitvoer activum |"ignite-sample-redacted.mp4" |Video met vervaging toegepast op basis van aantekeningen |

#### <a name="example-output"></a>Voorbeelduitvoer

Dit is de uitvoer van een IDList waarvoor een ID is geselecteerd.

Voor beeld foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>Vervagings typen

In de **gecombineerde** of **de redactie** modus zijn er vijf verschillende vervagings modi waaruit u kunt kiezen via de JSON-invoer configuratie: **laag**, **med**, **hoog**, **vak** en **zwart**. Standaard wordt **med** gebruikt.

Hieronder vindt u voor beelden van de vervagings typen die hieronder worden beschreven.

### <a name="example-settings-for-face-detector-preset"></a>Voorbeeld instellingen voor de voor instelling face detector
[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]


#### <a name="low"></a>Beperkt

![Voor beeld van een vervagings instelling met lage resolutie.](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>STB

![Voor beeld van een vervagings instelling voor gemiddelde resolutie.](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>Hoog

![Voor beeld van een wazige instelling met hoge resolutie.](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>Box

![Box-modus voor gebruik bij het opsporen van fouten in uw uitvoer.](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>Zwart

![De modus zwart vak bedekt alle gezichten met zwarte vakken.](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>Elementen van het JSON-uitvoer bestand

De redactie-MP biedt een hoge precisie voor detectie en tracering van locaties, waarmee u in een video frame Maxi maal 64 menselijke gezichten kunt detecteren. Front-gezichten bieden de beste resultaten, terwijl de zijde gezichten en kleine gezichten (kleiner dan of gelijk aan 24x24 pixels) lastig zijn.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET-voorbeeld code

Het volgende programma laat zien hoe u de **gecombineerde** redactie modus voor eenmalige door gang gebruikt:

- Maak een Asset en upload een media bestand naar de Asset.
- Configureer de voor instelling face detector die gebruikmaakt van de modus-en blurType-instellingen.
- Een nieuwe trans formatie maken met behulp van de voor instelling face detector
- Down load het video bestand met de uitvoer.

## <a name="download-and-configure-the-sample"></a>Het voorbeeld downloaden en configureren

Kloon met de volgende opdracht een GitHub-opslagplaats met het .NET-voorbeeld op de computer:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

Het voor beeld bevindt zich in de map [FaceRedactor](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor) . Open [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json) in het project dat u hebt gedownload. Vervang de waarden door de referenties die u hebt verkregen via [toegang tot API's](./access-api-howto.md).

U kunt **eventueel ook** het bestand **[sample. env](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env)** in de hoofdmap van de opslag plaats kopiëren en de details hierin invullen en de naam van het bestand wijzigen in **. env** (Let op de punt aan de voor kant!) zodat deze kan worden gebruikt in alle voorbeeld projecten in de opslag plaats.  Dit elimineert de nood zaak om in elk voor beeld een appsettings.jsin het bestand in te vullen en beschermt u ook bij het controleren van de instellingen in uw eigen met git-hub gekloonde opslag plaatsen.

#### <a name="examples"></a>Voorbeelden

Deze code laat zien hoe u de **FaceDetectorPreset** kunt instellen voor een vervaging van de **gecombineerde** modus.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

Dit code voorbeeld laat zien hoe de voor instelling tijdens het maken wordt door gegeven aan een Transform-object. Nadat de trans formatie is gemaakt, kunnen er direct aan de taken worden verzonden.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

