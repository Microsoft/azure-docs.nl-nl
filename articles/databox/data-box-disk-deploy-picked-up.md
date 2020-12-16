---
title: Zelfstudie voor het terugsturen van Azure Data Box Disk | Microsoft Docs
description: In deze zelfstudie wordt beschreven hoe u de Azure Data Box Disk retourneert. De instructies voor het retourneren zijn afhankelijk van waarvandaan u het apparaat retourneert.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 12/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: d529c5900d851368d60099091bb5745660036dea
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029886"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Zelfstudie: Azure Data Box Disk retourneren

In deze zelfstudie wordt beschreven hoe u uw Azure Data Box Disk retourneert. De instructies voor het retourneren zijn afhankelijk van waarvandaan u het apparaat retourneert.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een Data Box Disk naar Microsoft verzenden

## <a name="prerequisites"></a>Vereisten

Voltooi voordat u begint de [Zelfstudie: Gegevens kopiëren naar de Azure Data Box-schijf en deze gegevens controleren](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Een Data Box Disk terugsturen

::: zone-end

::: zone target="chromeless"

## <a name="return-azure-data-box-disk"></a>Azure Data Box Disk retourneren

::: zone-end

1. Koppel de schijven los, nadat de gegevensvalidatie is voltooid. Verwijder de verbindingskabels.
2. Verpak alle schijven en de verbindingskabels in bubbelplastic en plaats deze in de verzenddoos. Er kunnen kosten in rekening worden gebracht als er accessoires ontbreken.
    - Gebruik de verpakking waarin u het pakket hebt gekregen.  
    - U wordt aangeraden schijven te verpakken in stevig bubbelplastic.
    - Zorg ervoor dat alles goed past zodat het product niet in de doos kan gaan schuiven.

De volgende stappen zijn afhankelijk van de locatie waarvandaan u het apparaat retourneert. De instructies zijn verschillend voor de VS/Canada, de Europese Unie (EU), Australië of Aziatische regio's/landen.

### <a name="us-or-canada"></a>[VS of Canada](#tab/in-us-or-canada)

Voer de volgende stappen uit als u het apparaat vanuit de VS of Canada retourneert.

1. Gebruik het retourlabel in de doorzichtige plastic hoes op de doos. Als het label is beschadigd of verloren is gegaan:
    - Ga naar **Overzicht > Verzendlabel downloaden** en download een retourlabel.
    - Bevestig het label op het apparaat.

2. Verzegel de verpakking en zorg ervoor dat het retourlabel zichtbaar is.
3. Maak een afspraak met UPS om het pakket op te laten halen. Ophalen plannen:

    - Bel met UPS in de buurt (gratis land-/regiospecifiek nummer).
    - Vermeld in uw gesprek het trackingnummer voor de retournering, zoals weergegeven op het afgedrukte label.
    - Als het trackingnummer niet wordt vermeld, moet u bij het afhalen door UPS extra kosten betalen.
    - In plaats van het ophalen te plannen, kunt u de Data Box Disk ook zelf naar de dichtstbijzijnde inleverlocatie brengen.


### <a name="europe-or-uk"></a>[Europa of VK](#tab/in-europe-or-uk)

Voer de volgende stappen uit als u het apparaat retourneert vanuit Europa of het VK.

1. Gebruik het retourlabel in de doorzichtige plastic hoes op de doos. Als het label is beschadigd of verloren is gegaan:
    - Ga naar **Overzicht > Verzendlabel downloaden** en download een retourlabel.
    - Bevestig het label op het apparaat.

2. Verzegel de verpakking en zorg ervoor dat het retourlabel zichtbaar is.
3. Ga naar de DHL Express-website voor het land of de regio, en selecteer **Retournering plannen**. Selecteer bij **Hebt u een retourlabel nodig** de optie **Nee** > **Ik heb een DHL-vrachtbriefnummer**.
4. Geef het vrachtbriefnummer op en klik op **Retournering plannen** om een ophaalmoment te plannen.

### <a name="australia"></a>[Australië](#tab/in-australia)

Voor Azure-datacenters in Australië geldt een extra beveiligingsmaatregel. Alle inkomende verzendingen moeten een melding vooraf hebben. Voer de volgende stappen uit voor retourneren vanuit Australië.

1. Gebruik het meegeleverde retourlabel en controleer of de TAU-code (het referentienummer) erop staat. Als het retourlabel ontbreekt of als u andere problemen hebt, kunt u een e-mail sturen naar [Data Box Asia Operations](mailto:adbo@microsoft.com). Geef de ordernaam en details over het probleem op in de onderwerpregel.
2. Bevestig het label op de doos.
3. Boek online een afspraak voor de retourzending via de link https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference.

### <a name="japan"></a>[Japan](#tab/in-japan)

1. Schrijf als gegevens van de afzender uw bedrijfsnaam en adresgegevens op de vrachtbrief.
2. Stuur een e-mail naar Quantium Solutions met behulp van de volgende e-mailsjabloon.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00
        b. 13：00-15：00
        c. 15：00-17：00
        d. 17：00-19：00
    ```
    - **Als u retourneert vanuit Osaka**, wijzigt u het onderwerp in de e-mailsjabloon in: `Pickup request for Microsoft Azure OSA`.
    - Als er geen vrachtbrief van Japan Post Chakubarai is meegeleverd of ontbreekt, vermeldt u dit in het e-mailbericht. Quantium Solutions Japan zal Japan Post verzoeken om de vrachtbrief mee te nemen als het pakket wordt opgehaald.
    - Als u meerdere orders hebt, moet u een e-mail verzenden om afzonderlijke pakketten te laten ophalen.

3. U ontvangt per e-mail een bevestiging van Quantium Solutions nadat u het ophalen hebt afgesproken. De e-mailbevestiging bevat ook informatie over de Chakubarai-vrachtbrief.

Zo nodig kunt u contact opnemen met de ondersteuning van Quantium Solutions (in het Japans) via: 

- E-mail：[Customerservice.JP@quantiumsolutions.com](mailto:Customerservice.JP@quantiumsolutions.com)
- Telefoon: 03-5755-0150 

### <a name="korea"></a>[Korea](#tab/in-korea)

1. Zorg ervoor dat u de vrachtbrief voor de retourzending bijlevert.
2. Ophalen pakket aanvragen indien vrachtbrief aanwezig is:
    1. Bel de hotline van *Quantium Solutions International* op 070-8231-1418 (tijdens kantooruren, 10 tot 17 uur, maandag t/m vrijdag). U noemt *Microsoft Azure pickup* en het serviceaanvraagnummer om een ophaalafspraak te maken.  
    2. Als de hotline bezet is, stuurt u een e-mail aan `microsoft@rocketparcel.com`, met als onderwerp *Microsoft Azure Pickup* en het serviceaanvraagnummer als referentie.
    3. Als de koerier niet komt om het pakket op te halen, belt u de hotline van *Quantium Solutions International* voor een alternatieve oplossing.
    4. U ontvangt een e-mail ter bevestiging van de ophaalafspraak.
3. Voer deze stap alleen uit als de vrachtbrief niet aanwezig is. Ophalen aanvragen:
    1. Bel de hotline van *Quantium Solutions International* op 070-8231-1418 (tijdens kantooruren, 10 tot 17 uur, maandag t/m vrijdag). U noemt *Microsoft Azure pickup* en het serviceaanvraagnummer om een ophaalafspraak te maken. Geef op dat u een nieuwe vrachtbrief wilt hebben om een ophaalafspraak te maken. Vermeld de gegevens van de afzender (klant), de ontvanger (Azure-datacenter) en het referentienummer (serviceaanvraagnummer). 
    2. Als de hotline bezet is, stuurt u een e-mail aan `microsoft@rocketparcel.com`, met als onderwerp *Microsoft Azure Pickup* en het serviceaanvraagnummer als referentie.
    3. Als de koerier niet komt om het pakket op te halen, belt u de hotline van *Quantium Solutions International* voor een alternatieve oplossing.
    4. U krijgt een mondelinge bevestiging als de aanvraag via de telefoon wordt gedaan.

### <a name="singapore"></a>[Singapore](#tab/in-singapore)

1. Druk het verzendlabel af en bevestig het op de doos. Als het label is beschadigd of verloren is gegaan:
    - Ga naar **Overzicht > Verzendlabel downloaden** en download een retourlabel.
    - Bevestig het label op het apparaat. Zorg ervoor dat het label zichtbaar is.

2. Als u een ophaalaanvraag wilt indienen, stuurt u een mail naar de SingPost-klantenservice met de volgende sjabloon met volgnummer (het volgnummer vindt u op het retourlabel in het bezorgde pakket).

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - XZ00001234567
    Body:
     a.    Requestor name
     b.    Requestor contact number
     c.    Requestor collection address
     d.    Preferred collection date
    ```

   > [!NOTE]
   > Voor boekingsaanvragen op een werkdag:
   >
   > * Vóór 15.00 uur is de ophaaltijd op de volgende werkdag tussen 09.00 en 13.00.
   > * Na 15.00 uur is de ophaaltijd op de volgende werkdag tussen 14.00 en 18.00.

   Als u problemen ondervindt, neemt u contact op met Data Box Operations Asia via adbo@microsoft.com. Geef de naam van de taak op in het onderwerp en het probleem dat is opgetreden.

3. Overhandig het pakket aan de koerier.

### <a name="south-africa"></a>[Zuid-Afrika](#tab/in-sa)

Voer de volgende stappen uit als u het apparaat vanuit Zuid-Afrika retourneert.

1. Plak het meegeleverde verzendlabel op de verpakking. Dit label bevat het traceringsnummer. Als het verzendlabel ontbreekt, kunt u een nieuw label downloaden via **Overzicht > Verzendlabel downloaden**.

2. Verzegel de verpakking en zorg ervoor dat het retourlabel zichtbaar is.

3. Vraag een retourcode aan van Azure Data Box-bewerkingen. Er is een retourcode vereist om het pakket terug te sturen naar het datacentrum. E-mail verzenden naar [adbops@microsoft.com](mailto:adbops@microsoft.com). Noteer deze code op het verzendlabel naast het retouradres zodat het duidelijk zichtbaar is.

4. Laat het pakket door DHL ophalen met een van de volgende methodes:
   * Regel het ophalen van het pakket online door naar [DHL Express Zuid-Afrika, **Ophalen plannen**](https://mydhl.express.dhl/za/en/schedule-pickup.html#/schedule-pickup#label-reference) te gaan.
   * Stuur een e-mail naar [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) met behulp van de volgende sjabloon:

     ```output
     To: Priority.Support@dhl.com
     Subject: Pickup request for Microsoft Azure
     Body: Need pick up for the below shipment
       *  DHL tracking number: (reference number/waybill number)
       *  Requested pickup date: yyyy/mm/dd;time:HH MM
       *  Shipper contact: (company name)
       *  Contact person: 
       *  Phone number: 
       *  Full physical address: 
       *  Item to be collected: Azure Dt
     ```

    * Of lever het pakket af bij het dichtstbijzijnde DHL-servicepunt.

5. Als u problemen hebt, stuurt u een e-mail naar [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) met details van de problemen die zich hebben voorgedaan. Vermeld het vrachtnummer in de onderwerpregel. U kunt ook bellen naar +27(0)119213902.

### <a name="china"></a>[China](#tab/in-china)

Voer de volgende stappen uit als het apparaat vanuit China retourneert.

1. Plak het meegeleverde verzendlabel op de doos. Dit label bevat het traceringsnummer. Als het verzendlabel ontbreekt, kunt u een nieuw label downloaden via **Overzicht > Verzendlabel downloaden**.

2. Stuur een e-mail naar FedEx Premier Customer Care met het traceringsnummer (referentienummer van de verzending) om de retournering te regelen. Gebruik hiervoor de volgende e-mailsjabloon:  

   ```output
   To: ying.bao@fedex.com;739951@fedex.com
   Subject: Pickup request for Microsoft Azure : Order Name
   Body: Need pick up for the below shipment
   * FedEx tracking number (reference number)
   * Requested pickup date：yyyy/mm/dd; time: HH MM
   ```

3. Nadat het boeken van de retournering is voltooid, ontvangt u een e-mailbevestiging van FedEx.  

4. Als u problemen hebt, stuurt u een e-mail naar [DL-DC-SHA@oe.21vianet.com](mailto:DL-DC-SHA@oe.21vianet.com) met details van het aangetroffen probleem. Vermeld de ordernaam in de onderwerpregel.

#### <a name="premier-customer-care-contact-information"></a>Contactgegevens van Premier Customer Care

<ins>Primair</ins>

| Contactgegevens | Details |
|---|---|
|Naam:       | Bao Ying|
|Aanduiding | Senior OneCall Representative |
|Telefoon:      | 400.889.6066 ext. 3693 |
|E-mail:     | [ying.bao@fedex.com](mailto:ying.bao@fedex.com) |

<ins>Een back-up maken</ins>

| Contactgegevens | Details |
|---|---|
|Naam:       | He Xun|
|Aanduiding | OneCall Representative |
|Telefoon:      | 400.889.6066 ext. 3603 |
|E-mail:     | [739951@fedex.com](mailto:739951@fedex.com) |

### <a name="self-managed"></a>[Zelfbeheerd](#tab/in-selfmanaged)

Als uAls u Data Box Disk gebruikt in de Amerikaanse overheid, Japan, Singapore, Korea, West-Europa, Zuid-Afrika of India en de bezorgoptie Zelfbeheerde verzending hebt geselecteerd tijdens een bestelling, volgt u deze instructies.

1. Ga naar de blade **Overzicht** van uw bestelling in de Azure-portal. Loop de instructies door die worden weergegeven wanneer u **Ophalen plannen** selecteert. U ziet een autorisatiecode die wordt gebruikt op het moment dat de bestelling wordt bezorgd.

2. Wanneer u klaar bent om het apparaat terug te sturen, stuurt u een e-mail naar het Azure Data Box-team met de volgende sjabloon.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box Disk drop-off for order: 'orderName'
    Body:
     a. Order name
     b. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```
3. Het Azure Data Box-team werkt samen met u verder om de bezorging bij het Azure-datacentrum te regelen.

---

::: zone target="docs"

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie zijn verschillende onderwerpen besproken over de Azure Data Box-schijf, zoals:

> [!div class="checklist"]
>
> * Een Data Box Disk naar Microsoft verzenden

Ga naar de volgende procedure om te leren hoe u het uploaden van gegevens vanuit Data Box Disk naar het Azure Storage-account kunt controleren.

> [!div class="nextstepaction"]
> [Gegevens uploaden vanuit Azure Data Box Disk controleren](./data-box-disk-deploy-upload-verify.md)

::: zone-end
