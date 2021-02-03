---
title: Zelfstudie voor het terugsturen van Azure Data Box | Microsoft Docs
description: In deze zelfstudie leert u hoe u de Azure Data Box kunt retourneren, inclusief het voorbereiden op verzending, het verzenden van de Data Box, het verifiëren van gegevensupload en het wissen van gegevens van de Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/02/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 228f837a8826612bbbadf2ca8c5ef339ab248397
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524989"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Zelfstudie: Azure Data Box terugsturen en de gegevens controleren die u naar Azure hebt geüpload

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>Data Box terugsturen en gegevens controleren die u naar Azure hebt geüpload

::: zone-end

::: zone target="docs"

In deze zelfstudie wordt beschreven hoe u de Azure Data Box terugstuurt en hoe u de gegevens kunt controleren die u naar Azure hebt geüpload.

In deze zelfstudie vindt u informatie over onderwerpen als:

> [!div class="checklist"]
>
> * Vereisten
> * Voorbereiding voor verzending
> * De Data Box naar Microsoft verzenden
> * De gegevensupload naar Azure controleren
> * Gegevens verwijderen uit de Data Box

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

* U hebt de [Zelfstudie: Gegevens kopiëren naar Azure Data Box en deze gegevens controleren](data-box-deploy-copy-data.md).
* Kopieertaken zijn voltooid en er zijn geen fouten op de pagina **Verbinding maken en kopiëren**. **Voorbereiding voor verzending** kan niet worden uitgevoerd als er kopieertaken worden uitgevoerd, of als er fouten zijn op de pagina **Verbinding maken en kopiëren**.

## <a name="prepare-to-ship"></a>Voorbereiding voor verzending

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Wanneer het kopiëren van de gegevens is voltooid, kunt u het apparaat voorbereiden en verzenden. Nadat het apparaat is afgeleverd in het Azure-datacenter, worden uw gegevens automatisch geüpload naar Azure.

## <a name="prepare-to-ship"></a>Voorbereiding voor verzending

Voordat u het verzenden gaat voorbereiden, moet u ervoor zorgen dat de kopieertaken zijn voltooid.

1. Ga naar de pagina **Voorbereiden voor verzending** in de lokale webgebruikersinterface en start de voorbereiding voor verzending.
2. Schakel het apparaat uit in de lokale webgebruikersinterface. Verwijder de kabels uit het apparaat.

De volgende stappen zijn afhankelijk van de locatie waarvandaan u het apparaat retourneert.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>De Data Box terugsturen

Zorg ervoor dat het kopiëren van gegevens naar het apparaat is voltooid, en dat de stappen in **Voorbereiding voor verzending** zijn uitgevoerd. De procedure is afhankelijk van de regio waaruit u het apparaat verzendt.

::: zone-end

## <a name="us-canada-europe"></a>[VS, Canada, Europa](#tab/in-us-canada-europe)

Voer de volgende stappen uit als u het apparaat vanuit de VS, Canada of Europa retourneert.

1. Zorg ervoor dat het apparaat uit staat en de kabels zijn verwijderd.
2. Rol het netsnoer op en plaats het veilig aan de achterzijde van het apparaat.
3. Zorg ervoor dat het verzendlabel wordt weergegeven op het E-ink-scherm en plan met de vervoerder een tijdstip waarop het kan worden opgehaald. Neem contact op met de Microsoft-ondersteuning als het label beschadigd is of ontbreekt of niet wordt weergegeven op het e-inkscherm. Als het ondersteuningsteam dit aanraadt, gaat u in de Azure-portal naar **Overzicht > Verzendlabel downloaden**. Download het verzendlabel en bevestig dat op het apparaat. 
4. Als u het apparaat retourneert, spreekt u met UPS een tijdstip af waarop het kan worden opgehaald. Ophalen van apparaat plannen:

    * Bel met UPS (gratis land-/regiospecifiek nummer).
    * Vermeld in uw gesprek het trackingnummer voor de retournering, zoals weergegeven op het E-ink-display of het afgedrukte label. Als u het trackingnummer niet vermeldt, moet u bij het ophalen door UPS extra kosten betalen.
    * Als zich problemen voordoen tijdens het plannen van het ophalen van het apparaat, of als u wordt gevraagd om extra kosten te betalen, neemt u contact op met Azure Data Box Operations. E-mail verzenden naar [adbops@microsoft.com](mailto:adbops@microsoft.com).

    In plaats van het ophalen te plannen, kunt u de Data Box ook zelf naar de dichtstbijzijnde inleverlocatie brengen.
4. Nadat de Data Box door de vervoerder is opgehaald en gescand, verandert de orderstatus in de portal in **Opgehaald**. Er wordt ook een tracerings-id weergegeven.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Gegevens verwijderen uit de Data Box

Nadat de gegevens naar Azure zijn geüpload, worden de gegevens door de Data Box van de schijven gewist volgens de [richtlijnen van NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="australia"></a>[Australië](#tab/in-australia)

Voor Azure-datacenters in Australië geldt een extra beveiligingsmaatregel. Alle inkomende verzendingen moeten vooraf worden gemeld. Voer de volgende stappen uit voor verzenden vanuit Australië.

1. Bewaar de oorspronkelijke doos om het apparaat terug te sturen.
2. Zorg ervoor dat het kopiëren van gegevens naar het apparaat is voltooid en dat de stappen in **Voorbereiding voor verzending** zijn uitgevoerd.
3. Schakel het apparaat uit en verwijder de kabels.
4. Rol het meegeleverde netsnoer op en plaats het in de opberglade aan de achterkant van het apparaat.
5. Boek via de [DHL-link](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference) online een afspraak voor de retourzending.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Gegevens verwijderen uit de Data Box

Nadat de gegevens naar Azure zijn geüpload, worden de gegevens door de Data Box van de schijven gewist volgens de [richtlijnen van NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

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

Indien nodig kunt u contact opnemen met de ondersteuning van Quantium Solutions (voertaal Japans) via:

* E-mail：Customerservice.JP@quantiumsolutions.com 
* Telefoon: 03-5755-0150 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Gegevens verwijderen uit de Data Box
 
Nadat de gegevens naar Azure zijn geüpload, worden de gegevens door de Data Box van de schijven gewist volgens de [richtlijnen van NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="singapore"></a>[Singapore](#tab/in-singapore)

1. Bewaar de oorspronkelijke doos om het apparaat terug te sturen.
2. Noteer het volgnummer (weergegeven als referentienummer op de pagina **Voorbereiding voor verzending** van de lokale web-UI van Data Box). Dit is beschikbaar nadat de stap **Voorbereiding voor verzending** volledig is voltooid. Download het verzendlabel vanaf deze pagina en plak het op de doos.
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
   > * Vóór 15.00 uur is de ophaaltijd op de volgende werkdag tussen 09.00 en 13.00.
   > * Na 15.00 uur is de ophaaltijd op de volgende werkdag tussen 14.00 en 18.00.  

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Gegevens verwijderen uit de Data Box

Nadat de gegevens naar Azure zijn geüpload, worden de gegevens door de Data Box van de schijven gewist volgens de [richtlijnen van NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="south-africa"></a>[Zuid-Afrika](#tab/in-sa)

1. Verpak het apparaat in de oorspronkelijke doos om het apparaat terug te sturen.
2. Rol het meegeleverde netsnoer op en plaats het in de opberglade aan de achterkant van het apparaat.
3. Noteer het volgnummer (weergegeven als referentienummer op de pagina **Voorbereiden voor verzending** van de lokale web-UI van Data Box). Dit is beschikbaar nadat de stap Voorbereiding voor verzending volledig is voltooid. Download het verzendlabel vanaf deze pagina en plak het vervolgens op de doos.
4. Vraag een retourcode aan van Azure Data Box-bewerkingen. Er is een retourcode vereist om het pakket terug te sturen naar het datacentrum. E-mail verzenden naar [adbops@microsoft.com](mailto:adbops@microsoft.com). Noteer deze code op het verzendlabel naast het retouradres, waar het duidelijk zichtbaar is.
5. Laat het pakket door DHL ophalen met een van de volgende methodes:
 
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

6. Als u problemen ondervindt, stuurt u een e-mail naar [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) met details van de problemen. Vermeld het vrachtnummer in de onderwerpregel. U kunt ook bellen naar +27(0)119213902.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Gegevens verwijderen uit de Data Box

Nadat de gegevens naar Azure zijn geüpload, worden de gegevens door de Data Box van de schijven gewist volgens de [richtlijnen van NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

## <a name="hong-kong"></a>[Hongkong](#tab/in-hk)

1. Verpak het apparaat in de oorspronkelijke doos om het apparaat terug te sturen.
2. Rol het meegeleverde netsnoer op en plaats het in de opberglade aan de achterkant van het apparaat.
3. Bel de hotline van **Quantium Solutions** op **(852) 2318 1213** tijdens kantooruren (van 9.00 tot 18.00 uur, maandag t/m vrijdag).  
4. Vermeld Microsoft Azure pickup en het referentie- en trackingnummer (bovenstaande streepjescode) op het retourlabel om een ophaalafspraak te maken.
5. U krijgt een mondelinge bevestiging van de ophaalafspraak. Als de koerier niet komt om het pakket op te halen, belt u de hotline van Quantium Solutions voor een alternatieve oplossing.
6. Als u bij Quantium Solutions een afspraak boekt om het apparaat op te laten halen, gebruikt u de volgende sjabloon om de bevestiging te delen met [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com):

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium Solutions.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

Als u problemen ondervindt, stuurt u een e-mail naar Data Box Operations Asia [adbo@microsoft.com](mailto:adbo@microsoft.com) met details van de problemen. Vermeld de taaknaam in de onderwerpregel.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Gegevens verwijderen uit de Data Box
 
Nadat de gegevens naar Azure zijn geüpload, worden de gegevens door de Data Box van de schijven gewist volgens de [richtlijnen van NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

<!--## [In Korea](#tab/in-korea) 

1. Retain the original box used to ship the device for return shipment.
2. Note down the tracking number (shown as reference number on the **Prepare to Ship** page of the Data Box local web UI). The tracking number is available after the **Prepare to ship** step successfully completes. Download the shipping label from this page and paste on the packing box. 
3. Power off the device and remove the cables.
4. Spool and securely place the power cord that was provided with the device in the back of the device. 

Request pickup  
If consignment note is present:  

1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number to arrange for a collection.
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.  
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.  
4. You will receive an email confirmation for the pickup schedule.  

Exception process
If the consignment note is not present:
1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number. Specify that you need a new consignment note to arrange for a collection. Provide sender (customer), receiver information (Azure datacenter), and reference number (service request number).
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.
4. You get a verbal confirmation if request is made via telephone.  
::: zone target="chromeless"

## Verify data upload to Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## Erasure of data from Data Box
 
Once the upload to Azure is complete, the Data Box erases the data on its disks as per the [NIST SP 800-88 Revision 1 guidelines](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end
-->

## <a name="self-managed"></a>[Zelfbeheerd](#tab/in-selfmanaged)

Als u Data Box gebruikt in Amerikaanse overheid, Japan, Singapore, Korea, India, Zuid-Afrika, Verenigd Konink rijk, Europa-west of Australië en u zelf-beheerde verzen ding hebt geselecteerd tijdens het maken van uw bestelling, volgt u deze instructies.

1. Noteer de autorisatiecode die wordt weergegeven op de pagina **Voorbereiding voor verzending** van de lokale web-UI voor de Data Box, nadat deze stap is voltooid.
2. Schakel het apparaat uit en verwijder de kabels. Rol het meegeleverde netsnoer op en plaats het in de opberglade aan de achterkant van het apparaat.
3. Wanneer u klaar bent om het apparaat terug te sturen, stuurt u een e-mail naar het Azure Data Box-team met behulp van de onderstaande sjabloon.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box drop-off for order: 'orderName'
    Body:
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Gegevens verwijderen uit de Data Box
 
Nadat de gegevens naar Azure zijn geüpload, worden de gegevens door de Data Box van de schijven gewist volgens de [richtlijnen van NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

---

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

