---
title: Een Azure Stream Analytics-taak testen met voorbeeld gegevens
description: In dit artikel wordt beschreven hoe u met behulp van de Azure Portal een Azure Stream Analytics-taak, voorbeeld invoer en het uploaden van voorbeeld gegevens kunt testen.
author: ajetasin
ms.author: ajetasi
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: eff9103f476e6074ab46198ff8cc78588675569f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020040"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Een Azure Stream Analytics-taak testen in de portal

In Azure Stream Analytics kunt u uw query testen zonder de taak te starten of te stoppen. U kunt query's testen op binnenkomende gegevens van uw streaming-bronnen of voorbeeld gegevens uploaden vanuit een lokaal bestand in azure Portal. U kunt query's ook lokaal testen vanuit uw lokale voorbeeld gegevens of met Live-gegevens in [Visual Studio](stream-analytics-live-data-local-testing.md) en [Visual Studio code](visual-studio-code-local-run-live-input.md).

## <a name="automatically-sample-incoming-data-from-input"></a>Automatisch voor beeld van binnenkomende gegevens van invoer

Azure Stream Analytics haalt automatisch gebeurtenissen op uit uw streaming-invoer. U kunt query's uitvoeren op het standaard voorbeeld of een specifiek tijds bestek instellen voor het voor beeld.

1. Meld u aan bij Azure Portal.

2. Zoek en selecteer uw bestaande Stream Analytics-taak.

3. Selecteer op de pagina Stream Analytics taak onder de kop **taak topologie** de optie **query** om het venster query-editor te openen. 

4. Als u een voor beeld van een lijst met binnenkomende gebeurtenissen wilt zien, selecteert u het pictogram invoer met bestand en worden de voorbeeld gebeurtenissen automatisch weer gegeven in de **invoer voorbeeld**.

   a. Het type serialisatie voor uw gegevens wordt automatisch gedetecteerd als de JSON of CSV. U kunt dit ook hand matig wijzigen in JSON, CSV, AVRO door de optie in het vervolg keuzemenu te wijzigen.
    
   b. Gebruik de selector om uw gegevens in **tabel** -of **RAW** -indeling weer te geven.
    
   c. Als uw weer gegeven gegevens niet actueel zijn, selecteert u **vernieuwen** om de meest recente gebeurtenissen te bekijken.

   De volgende tabel bevat een voor beeld van gegevens in de **tabel indeling**:

   ![Voorbeeld invoer in tabel indeling Azure Stream Analytics](./media/stream-analytics-test-query/asa-sample-table.png)

   De volgende tabel bevat een voor beeld van de gegevens in **RAW-indeling**:

   ![Voorbeeld invoer Azure Stream Analytics in RAW-indeling](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Als u uw query wilt testen met inkomende gegevens, selecteert u **query testen**. Resultaten worden weer gegeven op het tabblad **test resultaten** . U kunt ook **Download resultaten** selecteren om de resultaten te downloaden.

   ![Voorbeeld query resultaten Azure Stream Analytics test](./media/stream-analytics-test-query/asa-test-query.png)

6. Selecteer **tijds bereik selecteren** om uw query te testen op een specifiek tijds bereik van binnenkomende gebeurtenissen.
   
   ![Azure Stream Analytics tijds bereik voor binnenkomende voorbeeld gebeurtenissen](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Stel het tijds bereik in van de gebeurtenissen die u wilt gebruiken om uw query te testen en selecteer voor **beeld**. Binnen dat tijds bestek kunt u Maxi maal 1000 gebeurtenissen of 1 MB ophalen, afhankelijk van wat het eerste komt.

   ![Azure Stream Analytics het tijds bereik voor binnenkomende voorbeeld gebeurtenissen instellen](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Zodra de gebeurtenissen voor het geselecteerde tijds bereik worden bemonsterd, worden ze weer gegeven op het tabblad **invoer voorbeeld** .

   ![Test resultaten Azure Stream Analytics weer geven](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Selecteer **opnieuw instellen** om de voorbeeld lijst met binnenkomende gebeurtenissen weer te geven. Als u **opnieuw instellen** selecteert, gaat de selectie van het tijds bereik verloren. Selecteer **test query** om uw query te testen en Bekijk de resultaten op het tabblad **test resultaten** .

10. Wanneer u wijzigingen aanbrengt in uw query, selecteert u **query opslaan** om de nieuwe query logica te testen. Hierdoor kunt u de query iteratief wijzigen en opnieuw testen om te zien hoe de uitvoer verandert.

11. Nadat u de resultaten hebt gecontroleerd die in de browser worden weer gegeven, bent u klaar om de taak te **starten** .

## <a name="upload-sample-data-from-a-local-file"></a>Voorbeeld gegevens uploaden uit een lokaal bestand

In plaats van live data te gebruiken, kunt u voorbeeld gegevens uit een lokaal bestand gebruiken om uw Azure Stream Analytics-query te testen.

1. Meld u aan bij Azure Portal.
   
2. Zoek uw bestaande Stream Analytics-taak en selecteer deze.

3. Selecteer op de pagina Stream Analytics taak onder de kop **taak topologie** de optie **query** om het venster query-editor te openen.

4. Als u uw query wilt testen met een lokaal bestand, selecteert u **voorbeeld invoer uploaden** op het tabblad **invoer voorbeeld** . 

   ![Scherm afbeelding toont de invoer optie voor het uploaden van voor beelden.](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Upload uw lokale bestand om de query te testen. U kunt alleen bestanden uploaden met de indelingen JSON, CSV of AVRO. Selecteer **OK**.

   ![Scherm afbeelding toont het dialoog venster voorbeeld gegevens uploaden, waarin u een bestand kunt selecteren.](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Zodra u het bestand uploadt, kunt u ook de bestands inhoud in het formulier zien als een tabel of in de RAW-indeling. Als u **opnieuw instellen** selecteert, worden de voorbeeld gegevens weer gegeven in de binnenkomende invoer gegevens die in de vorige sectie zijn beschreven. U kunt elk ander bestand uploaden om de query op elk gewenst moment te testen.

7. Selecteer **test query** om uw query te testen op het geüploade voorbeeld bestand.

8. Test resultaten worden weer gegeven op basis van uw query. U kunt uw query wijzigen en **query opslaan** selecteren om de nieuwe query logica te testen. Hierdoor kunt u de query iteratief wijzigen en opnieuw testen om te zien hoe de uitvoer verandert.

9. Wanneer u meerdere uitvoer in de query gebruikt, worden de resultaten weer gegeven op basis van de geselecteerde uitvoer. 

   ![Geselecteerde uitvoer Azure Stream Analytics](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Nadat u de resultaten hebt gecontroleerd die in de browser worden weer gegeven, kunt u de taak **starten** .

## <a name="limitations"></a>Beperkingen

1.  Tijd beleid wordt niet ondersteund bij het testen van de portal:

    * Out-of-order: alle binnenkomende gebeurtenissen worden gerangschikt.
    * Late aankomst: er is geen gebeurtenis voor een latere aankomst, omdat Stream Analytics alleen bestaande gegevens voor testen kan gebruiken.
   
2.  C# UDF wordt niet ondersteund.

3.  Alle tests worden uitgevoerd met een taak die één streaming-eenheid heeft.

4.  De time-outwaarde is één minuut. Een query met een venster grootte van meer dan één minuut kan dus geen gegevens ophalen.

5.  Machine learning wordt niet ondersteund.

6. De voorbeeld gegevens-API wordt beperkt na vijf aanvragen in een venster van 15 minuten. Na het einde van het venster van 15 minuten kunt u meer voorbeeld gegevens aanvragen uitvoeren. Deze beperking wordt toegepast op het abonnements niveau.

## <a name="troubleshooting"></a>Problemen oplossen

1.  Als deze fout melding wordt weer geven, is er een probleem met de netwerk verbinding opgetreden bij het ophalen van de resultaten. Controleer de netwerk-en Firewall instellingen. Volg de onderstaande stappen:

  * Open in een browser om de verbinding met de service te controleren [https://queryruntime.azurestreamanalytics.com/api/home/index](https://queryruntime.azurestreamanalytics.com/api/home/index) . Als u deze koppeling niet kunt openen, kunt u de firewall instellingen bijwerken.
  
2. Als u deze fout melding krijgt, is de omvang van de aanvraag te groot. Verklein de grootte van de invoer gegevens en probeer het opnieuw. "Voer de volgende stappen uit:

  * Invoer grootte verlagen: test uw query met een voor beeld van een kleiner formaat of met een kleiner tijds bereik.
  * Query grootte verkleinen: als u een selectie query wilt testen, selecteert u een gedeelte van de query en klikt u op **geselecteerde query testen**.


## <a name="next-steps"></a>Volgende stappen
* [Een IOT-oplossing bouwen met behulp van stream Analytics](./stream-analytics-build-an-iot-solution-using-stream-analytics.md): deze zelf studie helpt u bij het bouwen van een end-to-end oplossing met een gegevens generator waarmee verkeer wordt gesimuleerd op een telefoon stand.

* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)

* [Query voorbeelden voor algemene Stream Analytics gebruiks patronen](stream-analytics-stream-analytics-query-patterns.md)

* [Wat is invoer van Azure Stream Analytics?](stream-analytics-add-inputs.md)

* [Meer informatie over de uitvoer van Azure Stream Analytics](stream-analytics-define-outputs.md)
