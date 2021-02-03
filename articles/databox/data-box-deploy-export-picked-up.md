---
title: Zelfstudie voor het terugsturen van Azure Data Box in exportbestelling | Microsoft Docs
description: Lees hoe u uw Azure Data Box naar Microsoft verzendt nadat de exportbestelling is voltooid
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 9ebf95f0a9c5cfc059f38a13ad99a545dd863d21
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525028"
---
# <a name="tutorial-return-azure-data-box"></a>Zelfstudie: Azure Data Box retourneren

In deze zelfstudie wordt beschreven hoe u uw Azure Data Box retourneert en hoe de gegevens worden gewist zodra het apparaat is ontvangen door het Azure-datacenter.

In deze zelfstudie vindt u informatie over onderwerpen als:

> [!div class="checklist"]
>
> * Vereisten
> * Voorbereiding voor verzending
> * De Data Box naar Microsoft verzenden
> * Gegevens verwijderen uit de Data Box

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

* U hebt de [Zelfstudie: Gegevens kopiëren uit Azure Data Box](data-box-deploy-export-copy-data.md) voltooid.
* Kopieertaken zijn voltooid. Voorbereiding voor verzending kan niet worden uitgevoerd als er nog kopieertaken worden uitgevoerd.

## <a name="prepare-to-ship"></a>Voorbereiding voor verzending

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

De volgende stappen zijn afhankelijk van de locatie waarvandaan u het apparaat retourneert.

## <a name="ship-data-box-back"></a>De Data Box terugsturen

Zorg ervoor dat het kopiëren van gegevens van het apparaat is voltooid en dat de stappen in **Voorbereiding voor verzending** zijn uitgevoerd. De procedure is afhankelijk van de regio waaruit u het apparaat verzendt.

## <a name="us-canada-europe"></a>[VS, Canada, Europa](#tab/in-us-canada-europe)

Voer de volgende stappen uit als u het apparaat vanuit de VS, Canada of Europa retourneert.

1. Zorg ervoor dat het apparaat uit staat en de kabels zijn verwijderd. 
2. Rol het netsnoer op en plaats het veilig aan de achterzijde van het apparaat.
3. Zorg ervoor dat het verzendlabel wordt weergegeven op het E-ink-scherm, en plan met de vervoerder een tijdstip waarop het kan worden opgehaald. Neem contact op met de Microsoft-ondersteuning als het label is beschadigd of ontbreekt, of als het niet wordt weergegeven op het E-ink-scherm. Ga, als het ondersteuningsteam dit aanraadt, in de Azure-portal naar **Overzicht > Verzendlabel downloaden**. Download het verzendlabel en bevestig het op het apparaat. 
4. Als u het apparaat retourneert, spreekt u met UPS een tijdstip af waarop het kan worden opgehaald. Ophalen van apparaat plannen:

   - Bel met UPS (gratis land-/regiospecifiek nummer).
   - Vermeld in uw gesprek het trackingnummer voor de retournering, zoals weergegeven op het E-ink-display of het afgedrukte label. Als u het trackingnummer niet vermeldt, moet u bij het ophalen door UPS extra kosten betalen.
   - Als zich problemen voordoen tijdens het plannen van het ophalen van het apparaat, of als u wordt gevraagd om extra kosten te betalen, neemt u contact op met Azure Data Box Operations. E-mail verzenden naar [adbops@microsoft.com](mailto:adbops@microsoft.com).

    In plaats van het ophalen te plannen, kunt u de Data Box ook zelf naar de dichtstbijzijnde inleverlocatie brengen.
4. Nadat de Data Box door de vervoerder is opgehaald en gescand, verandert de orderstatus in de portal in **Opgehaald**. Er wordt ook een tracerings-id weergegeven.

## <a name="australia"></a>[Australië](#tab/in-australia)

Voor Azure-datacenters in Australië geldt een extra beveiligingsmaatregel. Alle inkomende verzendingen moeten vooraf worden gemeld. Voer de volgende stappen uit voor verzenden vanuit Australië.

1. Bewaar de oorspronkelijke doos om het apparaat terug te sturen.
2. Zorg ervoor dat het kopiëren van gegevens naar het apparaat is voltooid en dat de stappen in **Voorbereiding voor verzending** zijn uitgevoerd.
3. Schakel het apparaat uit en verwijder de kabels.
4. Rol het meegeleverde netsnoer op en plaats het in de opberglade aan de achterkant van het apparaat.
5. Boek via de [DHL-koppeling](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference) online een afspraak voor de retourzending.

## <a name="japan"></a>[Japan](#tab/in-japan)

1. Bewaar de oorspronkelijke doos om het apparaat terug te sturen.
2. Schakel het apparaat uit en verwijder de kabels.
3. Rol het meegeleverde netsnoer op en plaats het in de opberglade aan de achterkant van het apparaat.
4. Schrijf als gegevens van de afzender uw bedrijfsnaam en adresgegevens op de vrachtbrief.
5. Stuur een e-mail naar Quantium Solutions met behulp van de volgende e-mailsjabloon.

    * Als er geen vrachtbrief van Japan Post Chakubarai is meegeleverd of ontbreekt, vermeldt u dit in het e-mailbericht. Quantium Solutions Japan zal Japan Post verzoeken om de vrachtbrief mee te nemen als het pakket wordt opgehaald.
    * Als u meerdere orders hebt, moet u een e-mail verzenden om afzonderlijke pakketten te laten ophalen.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

6. U ontvangt per e-mail een bevestiging van Quantium Solutions nadat u het ophalen hebt afgesproken. De e-mailbevestiging bevat ook informatie over de Chakubarai-vrachtbrief.

Zo nodig kunt u contact opnemen met de ondersteuning van Quantium Solutions (in het Japans) via: 

* E-mail：Customerservice.JP@quantiumsolutions.com
* Telefoon: 03-5755-0150

## <a name="singapore"></a>[Singapore](#tab/in-singapore)

1. Bewaar de oorspronkelijke doos om het apparaat terug te sturen.
2. Noteer het volgnummer (weergegeven als referentienummer op de pagina Voorbereiden voor verzending van de lokale web-UI van Data Box). Dit is beschikbaar nadat de stap voor het voorbereiden van de verzending is voltooid. Download het verzendlabel vanaf deze pagina en plak het op de doos.
3. Schakel het apparaat uit en verwijder de kabels.
4. Rol het meegeleverde netsnoer op en plaats het in de opberglade aan de achterkant van het apparaat. 
5. Stuur een e-mail naar SingPost Customer Service met behulp van de volgende e-mailsjabloon met het volgnummer.

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > Voor boekingsaanvragen op een werkdag:
   >
   > * Vóór 15.00 uur is de ophaaltijd op de volgende werkdag tussen 09.00 en 13.00.
   > * Na 15.00 uur is de ophaaltijd op de volgende werkdag tussen 14.00 en 18.00.  

## <a name="south-africa"></a>[Zuid-Afrika](#tab/in-sa)

1. Bewaar de oorspronkelijke doos om het apparaat terug te sturen.
2. Noteer het referentienummer (vrachtnummer) dat in de lokale webinterface van het apparaat wordt weergegeven. Dit nummer wordt weergegeven zodra **Voorbereiding voor verzending** is voltooid.
3. Download het verzendlabel uit de lokale webinterface van het apparaat. Druk het label af en plak het op het te verzenden pakket.
4. Kies een van de volgende opties om het pakket door DHL te laten ophalen:

    * Bel het contactcentrum van de klantenservice vóór 14.00 uur op nummer **+27(0) 11 9213600**. Kies optie 1 en geef het vrachtnummer op.
    * Stuur een e-mail naar [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) met behulp van de volgende sjabloon:

    ```output
    To: Priority.Support@dhl.com
    Subject: Pickup request for Microsoft Azure
    Body: Need pick up for the below shipment
      *  DHL tracking number (reference number/waybill number)
      *  Requested pickup date: yyyy/mm/dd;time:HH MM
    ```

    * Een andere optie is het pakket af te leveren bij het dichtstbijzijnde DHL-servicepunt.

5. Als u problemen hebt, stuurt u een e-mail naar [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) met details van de problemen die zich hebben voorgedaan. Vermeld het vrachtnummer in de onderwerpregel. U kunt ook bellen naar +27(0)119213902.

## <a name="hong-kong"></a>[Hongkong](#tab/in-hk)

1. Verpak het apparaat in de oorspronkelijke doos om het apparaat terug te sturen.
2. Noteer het referentienummer (het volgnummer voor terugzending) dat in de lokale webinterface van het apparaat wordt weergegeven. Dit nummer wordt weergegeven zodra **Voorbereiding voor verzending** is voltooid.
3. Download het verzendlabel uit de lokale webinterface van het apparaat. Druk het label af en plak het op het te verzenden pakket.
4. Rol het meegeleverde netsnoer op en plaats het in de opberglade aan de achterkant van het apparaat.
5. Bel de hotline van **Quantium Solutions** op **(852) 2318 1213** tijdens kantooruren (van 9.00 tot 18.00 uur, maandag t/m vrijdag).  
6. Vermeld Microsoft Azure pickup en het referentie- en trackingnummer (bovenstaande streepjescode) op het retourlabel om een ophaalafspraak te maken.
7. U krijgt een mondelinge bevestiging over de ophaalafspraak. Als de koerier niet komt om het pakket op te halen, belt u de hotline van Quantium Solutions voor een alternatieve oplossing.
8. Als u bij Quantium een afspraak boekt om het apparaat op te laten halen, gebruikt u de volgende sjabloon om de bevestiging te delen met [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com):

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

Indien u een probleem ondervindt, stuurt u een e-mail naar Data Box Operations Asia [adbo@microsoft.com](mailto:adbo@microsoft.com). Vermeld de taaknaam in de onderwerpregel en het probleem dat u hebt ondervonden.

## <a name="self-managed"></a>[Zelfbeheerd](#tab/in-selfmanaged)

Als u Data Box gebruikt in Japan, Singapore, Korea, India, Zuid-Afrika, Verenigd Konink rijk, Europa-west of Australië en de optie zelf beheerde verzen ding hebt geselecteerd tijdens het maken van de order, volgt u deze instructies.

1. Noteer de autorisatiecode die wordt weergegeven op de pagina Voorbereiden voor verzending van de lokale web-UI van Data Box nadat deze stap is voltooid.
2. Schakel het apparaat uit en verwijder de kabels. Rol het meegeleverde netsnoer op en plaats het in de opberglade aan de achterkant van het apparaat.
3. Wanneer u klaar bent om het apparaat terug te sturen, stuurt u een e-mail naar het Azure Data Box-team met de sjabloon hieronder.
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

---

## <a name="erasure-of-data-from-data-box"></a>Gegevens verwijderen uit de Data Box

Zodra het apparaat het Azure-datacenter heeft bereikt, worden de gegevens door de Data Box van de schijven gewist volgens de [richtlijnen van NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u meer geleerd over onderwerpen zoals:

> [!div class="checklist"]
> * Vereisten
> * Voorbereiding voor verzending
> * De Data Box naar Microsoft verzenden
> * Gegevens verwijderen uit de Data Box

Ga naar het volgende artikel om te lezen hoe u uw Data Box beheert.

> [!div class="nextstepaction"]
> [Data Box beheren via de Azure-portal](./data-box-portal-admin.md)


