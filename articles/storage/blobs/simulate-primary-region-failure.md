---
title: 'Zelfstudie: een fout simuleren bij het lezen van gegevens uit de primaire regio'
titleSuffix: Azure Storage
description: Simuleer een fout bij het lezen van gegevens uit de primaire regio wanneer het opslagaccount is geconfigureerd voor geografische zone-redundante opslag met leestoegang (RA-GZRS).
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: devx-track-js
ms.openlocfilehash: 5a0bef4fbfd4ee9aa720dab430a33bbfcd0b918b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91280348"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>Zelfstudie: Een fout simuleren bij het lezen van gegevens uit de primaire regio

Deze zelfstudie is deel twee van een serie. Er wordt uitgelegd wat de voordelen zijn van [geografische zone-redundante opslag met leestoegang](../common/storage-redundancy.md) (RA-GZRS) door een fout te simuleren.

Voor het simuleren van een fout kunt u [statische routering](#simulate-a-failure-with-an-invalid-static-route) of [Fiddler](#simulate-a-failure-with-fiddler) gebruiken. In beide gevallen kunt u fouten simuleren voor aanvragen naar het primaire eindpunt van uw [geografisch redundante opslag met leestoegang](../common/storage-redundancy.md) (RA-GZRS) en de toepassing gegevens laten lezen van het secundaire eindpunt.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

In deel twee van de serie leert u het volgende:

> [!div class="checklist"]
> * De toepassing uitvoeren en onderbreken
> * Een fout simuleren met [een ongeldige statische route](#simulate-a-failure-with-an-invalid-static-route) of [Fiddler](#simulate-a-failure-with-fiddler)
> * Herstel van het primaire eindpunt simuleren

## <a name="prerequisites"></a>Vereisten

Begin pas met deze zelfstudie nadat u de vorige zelfstudie hebt voltooid: [Uw toepassingsgegevens maximaal beschikbaar maken met Azure-opslag][previous-tutorial].

Als u een fout met statische routering wilt simuleren, gebruikt u een opdrachtprompt met verhoogde bevoegdheden.

Als u een fout met Fiddler wilt simuleren, moet u [Fiddler downloaden en installeren](https://www.telerik.com/download/fiddler)

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Een fout simuleren met een ongeldige statische route

U kunt een ongeldige statische route maken voor alle aanvragen naar het primaire eindpunt van uw [geografisch redundante opslagaccount met leestoegang](../common/storage-redundancy.md) (RA-GZRS). In deze zelfstudie wordt de lokale host gebruikt als de gateway voor routeringsaanvragen voor de opslagaccount. Het gebruik van de lokale host als de gateway zorgt ervoor dat alle aanvragen naar het primaire eindpunt van uw opslagaccount in een lusvorm terugkeren naar de host, wat vervolgens tot een fout leidt. Voer de volgende stappen uit om een fout en herstel van het primaire eindpunt met een ongeldige statische route te simuleren.

### <a name="start-and-pause-the-application"></a>De toepassing starten en onderbreken

Volg de instructies in de [vorige zelfstudie][previous-tutorial] om het voorbeeld te starten, het testbestand te downloaden en te controleren of het afkomstig is van de primaire opslag. Afhankelijk van uw doelplatform kunt u het voorbeeld handmatig onderbreken of wachten bij een prompt.

### <a name="simulate-failure"></a>Fout simuleren

Open terwijl de toepassing is onderbroken een opdrachtprompt van Windows als een beheerder of voer terminal als root uit op Linux.

Vraag informatie op over het primaire-eindpuntdomein van het opslagaccount door de volgende opdracht in te voeren bij een opdrachtprompt of in een terminalvenster. Vervang `STORAGEACCOUNTNAME` door de naam van uw opslagaccount.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

Kopieer het IP-adres van uw opslagaccount naar een teksteditor voor later gebruik.

Als u het IP-adres van uw lokale host wilt ophalen, typt u `ipconfig` in de Windows-opdrachtprompt of `ifconfig` in de Linux-terminal.

Als u een statische route voor een doelhost wilt toevoegen, typt u de volgende opdracht bij een Windows-opdrachtprompt of in een Linux-terminal. Vervang `<destination_ip>` door het IP-adres van uw opslagaccount en `<gateway_ip>` door het IP-adres van uw lokale host.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

In het venster met het actieve voorbeeld hervat u de toepassing of drukt u op de juiste toets om het voorbeeldbestand te downloaden en te controleren of het afkomstig is van de secundaire opslag. Vervolgens kunt u het voorbeeld opnieuw onderbreken of wachten bij de prompt.

### <a name="simulate-primary-endpoint-restoration"></a>Herstel van het primaire eindpunt simuleren

Om te simuleren dat het primaire eindpunt weer werkt, verwijdert u de ongeldige statische route uit de routeringstabel. Hiermee worden alle aanvragen naar het primaire eindpunt via de standaardgateway gerouteerd. Typ de volgende opdracht in een Windows-opdrachtprompt of Linux-terminal.

#### <a name="linux"></a>Linux

```bash
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```console
route delete <destination_ip>
```

U kunt de toepassing vervolgens hervatten of op de juiste toets drukken om het voorbeeldbestand opnieuw te downloaden. Ditmaal controleert u of het weer afkomstig is van de primaire opslag.

## <a name="simulate-a-failure-with-fiddler"></a>Een fout simuleren met Fiddler

Als u een fout wilt simuleren met Fiddler, voert u een mislukte reactie voor aanvragen in op het primaire eindpunt van uw RA-GZRS-opslagaccount met leestoegang.

In de volgende gedeelten wordt uitgelegd hoe u een fout simuleert en het primaire eindpunt herstelt met Fiddler.

### <a name="launch-fiddler"></a>Start Fiddler

Open Fiddler en selecteer **Rules** en **Customize Rules**.

![Fiddler-regels aanpassen](media/simulate-primary-region-failure/figure1.png)

De Fiddler ScriptEditor wordt gestart en u ziet het bestand **SampleRules.js**. Dit bestand wordt gebruikt om Fiddler aan te passen.

Plak het volgende codevoorbeeld in de functie `OnBeforeResponse`, waarbij u `STORAGEACCOUNTNAME` vervangt door de naam van uw opslagaccount. Afhankelijk van het voorbeeld moet u mogelijk ook `HelloWorld` vervangen door de naam van het testbestand (of een voorvoegsel zoals `sampleFile`) dat wordt gedownload. De nieuwe code is uitgeschakeld om ervoor te zorgen dat deze niet onmiddellijk wordt uitgevoerd.

Selecteer als u klaar bent **File** en **Save** om uw wijzigingen op te slaan. Laat het venster van ScriptEditor openstaan voor gebruik in de volgende stappen.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![De aangepaste regel plakken](media/simulate-primary-region-failure/figure2.png)

### <a name="start-and-pause-the-application"></a>De toepassing starten en onderbreken

Volg de instructies in de [vorige zelfstudie][previous-tutorial] om het voorbeeld te starten, het testbestand te downloaden en te controleren of het afkomstig is van de primaire opslag. Afhankelijk van uw doelplatform kunt u het voorbeeld handmatig onderbreken of wachten bij een prompt.

### <a name="simulate-failure"></a>Fout simuleren

Terwijl de toepassing is onderbroken, keert u terug naar Fiddler en schakelt u de aangepaste regel die u hebt opgeslagen in de functie `OnBeforeResponse` weer in. Selecteer **File** en **Save** om uw wijzigingen op te slaan, zodat de regel van kracht wordt. Deze code zoekt naar aanvragen voor het RA-GZRS-opslagaccount met leestoegang en als het pad de naam van het bestand bevat, wordt de antwoordcode `503 - Service Unavailable` geretourneerd.

In het venster met het actieve voorbeeld hervat u de toepassing of drukt u op de juiste toets om het voorbeeldbestand te downloaden en te controleren of het afkomstig is van de secundaire opslag. Vervolgens kunt u het voorbeeld opnieuw onderbreken of wachten bij de prompt.

### <a name="simulate-primary-endpoint-restoration"></a>Herstel van het primaire eindpunt simuleren

Verwijder de aangepaste regel opnieuw of schakel deze opnieuw uit in Fiddler. Selecteer **File** en **Save** om ervoor te zorgen dat de regel niet meer van kracht is.

In het venster met het actieve voorbeeld hervat u de toepassing of drukt u op de juiste toets om het voorbeeldbestand te downloaden en te controleren of het weer afkomstig is van de primaire opslag. Vervolgens kunt u het voorbeeld afsluiten.

## <a name="next-steps"></a>Volgende stappen

In deel twee van de serie hebt u geleerd over het simuleren van een fout om geografisch redundante opslag met leestoegang te testen.

Lees het volgende artikel voor meer informatie over de werking van RA-GZRS-opslag en de bijbehorende risico's:

> [!div class="nextstepaction"]
> [HA-apps met RA-GZRS ontwerpen](../common/geo-redundant-design.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
