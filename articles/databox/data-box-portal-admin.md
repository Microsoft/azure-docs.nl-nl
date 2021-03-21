---
title: Azure Data Box beheren, Azure Data Box Heavy via Azure Portal | Microsoft Docs
description: Hierin wordt beschreven hoe u de Azure Portal gebruikt voor het beheren van uw Azure Data Box en Azure Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 46a18cb2b6e1682427d5674be28b240f35b120fe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97678646"
---
# <a name="use-the-azure-portal-to-administer-your-azure-data-box-and-azure-data-box-heavy"></a>Gebruik de Azure Portal om uw Azure Data Box en Azure Data Box Heavy te beheren

Dit artikel is van toepassing op zowel Azure Data Box als Azure Data Box Heavy. In dit artikel worden enkele van de complexe werk stromen en beheer taken beschreven die op het Azure Data Box apparaat kunnen worden uitgevoerd. U kunt het Data Box-apparaat beheren via de Azure Portal of via de lokale web-UI.

Dit artikel richt zich op de taken die u kunt uitvoeren met behulp van de Azure-portal. Gebruik de Azure Portal om orders te beheren, Data Box-apparaat te beheren en de status van de order bij te houden terwijl deze wordt voltooid.

## <a name="cancel-an-order"></a>Een order annuleren

U moet mogelijk een bestelling om verschillende redenen annuleren nadat u deze hebt geplaatst.

Voor zowel import-als export orders kunt u de order alleen annuleren voordat deze wordt verwerkt. Als uw bestelling is verwerkt en het Data Box apparaat is voor bereid, kunt u de volg orde niet annuleren.

Voer de volgende stappen uit om een order te annuleren.

1.  Ga naar **Overzicht > Annuleren**.

    ![Opdracht annuleren op het tabblad Overzicht voor een order](media/data-box-portal-admin/portal-admin-cancel-command.png)

2.  Geef een reden op voor het annuleren van de order.  

    ![Het dialoog venster bestelling annuleren](media/data-box-portal-admin/portal-admin-cancel-order-dbox.png)

3.  Zodra de order is geannuleerd, werkt de portal de status van de order bij naar **Geannuleerd**.

## <a name="clone-an-order"></a>Een order klonen

Klonen is handig in bepaalde situaties. U hebt bijvoorbeeld Data Box gebruikt voor het overdragen van gegevens. Naarmate er meer gegevens worden gegenereerd, moet u een ander Data Box apparaat gebruiken om die gegevens over te brengen naar Azure. In dit geval kunt u dezelfde volg orde klonen.

Voer de volgende stappen uit om een import volgorde te klonen.

1.  Ga naar **Overzicht > Klonen**. 

    ![De opdracht Clone op het tabblad Overzicht voor een order](media/data-box-portal-admin/portal-admin-clone-command.png)

2.  Alle details van de order blijven hetzelfde. De ordernaam is de oorspronkelijke ordernaam met *-Kloon* eraan toegevoegd. Schakel het selectievakje in om te bevestigen dat u de privacyinformatie hebt gelezen. Klik op **Create**.

De kloon wordt binnen enkele minuten gemaakt en de portal wordt bijgewerkt met de nieuwe order.


## <a name="delete-order"></a>Order verwijderen

Wanneer de order is voltooid, wilt u deze wellicht verwijderen. De order bevat uw persoonlijke informatie, zoals naam, adres en contactgegevens. Deze persoonlijke informatie wordt verwijderd wanneer de order wordt verwijderd.

U kunt alleen orders verwijderen die zijn voltooid of geannuleerd. Voer de volgende stappen uit om een order te verwijderen.

1. Ga naar **Alle resources**. Zoek uw order.

2. Klik op de order die u wilt verwijderen en ga naar **Overzicht**. Klik in de opdrachtbalk op **Verwijderen**.

    ![De opdracht verwijderen op het tabblad Overzicht voor een order](media/data-box-portal-admin/portal-admin-delete-command.png)

3. Voer de naam van de order in wanneer u wordt gevraagd de orderverwijdering te bevestigen. Klik op **Verwijderen**.

## <a name="download-shipping-label"></a>Verzendlabel downloaden

Mogelijk moet u het verzend label downloaden als de E-inkt weergave van uw Data Box niet werkt en het retour verzendings label niet weergeeft. Er wordt geen E-inkt weer gegeven op Data Box Heavy, dus deze werk stroom is niet van toepassing op Data Box Heavy.

Voer de volgende stappen uit om een verzendlabel te downloaden.

1.  Ga naar **Overzicht > Verzendlabel downloaden**. Deze optie is alleen beschikbaar nadat het apparaat is verzonden. 

    ![Verzendlabel downloaden](media/data-box-portal-admin/portal-admin-download-shipping-label.png)

2.  Hiermee downloadt u het volgende verzendlabel voor retourzending. Sla het label op en druk het af. Vouw het label in op de heldere hoes op het apparaat. Zorg dat het label zichtbaar is. Verwijder eventuele stickers van de vorige verzending die op het apparaat zitten.

    ![Voorbeeld van verzendlabel](media/data-box-portal-admin/portal-admin-example-shipping-label.png)

## <a name="edit-shipping-address"></a>Verzendadres bewerken

U moet het verzendadres wellicht bewerken nadat de order is geplaatst. Dit kan alleen als het apparaat nog niet is verzonden. Zodra het apparaat is verzonden, is deze optie niet meer beschikbaar.

Voer de volgende stappen uit om de order te bewerken.

1. Ga naar **Ordergegevens > Verzendadres bewerken**.

    ![Opdracht voor het verzend adres bewerken in de details van de order](media/data-box-portal-admin/portal-admin-edit-shipping-address-command.png)

2. Bewerk en valideer het verzendadres en sla de wijzigingen vervolgens op.

    ![Het dialoog venster verzend adres bewerken](media/data-box-portal-admin/portal-admin-edit-shipping-address-dbox.png)

## <a name="edit-notification-details"></a>Meldingsdetails bewerken

Mogelijk moet u de gebruikers wijzigen die de e-mail met de status van de order ontvangen. Een voorbeeld: Een bepaalde gebruiker moet worden geïnformeerd wanneer het apparaat wordt afgeleverd of opgehaald. Een andere gebruiker moet mogelijk een melding ontvangen wanneer het kopiëren van de gegevens is voltooid, zodat de gegevens in het Azure Storage-account kunnen worden gecontroleerd voordat deze uit de bron worden verwijderd. In deze gevallen kunt u de meldingsdetails bewerken.

Voer de volgende stappen uit om meldingsdetails te bewerken.

1. Ga naar **Ordergegevens > Meldingsdetails bewerken**.

    ![De opdracht Details van melding bewerken in de details van de order](media/data-box-portal-admin/portal-admin-edit-notification-details-command.png)

2. U kunt de meldingsdetails nu bewerken en de wijzigingen vervolgens opslaan.
 
    ![Het dialoog venster meldings Details bewerken](media/data-box-portal-admin/portal-admin-edit-notification-details-dbox.png)


## <a name="download-order-history"></a>Ordergeschiedenis downloaden

Nadat de Data Box-order is voltooid, worden de gegevens op de schijven van het apparaat gewist. Wanneer het opschonen van het apparaat is voltooid, kunt u de ordergeschiedenis downloaden in de Azure-portal.

Voer de volgende stappen uit om de ordergeschiedenis te downloaden.

1. Ga in uw Data Box-order naar **Overzicht**. Controleer of de order is voltooid. Als de order is voltooid en het opschonen van het apparaat is voltooid, gaat u naar **Ordergegevens**. De optie **Ordergeschiedenis downloaden** is beschikbaar.

    ![Ordergeschiedenis downloaden](media/data-box-portal-admin/portal-admin-download-order-history.png)

2. Klik op **Ordergeschiedenis downloaden**. De gedownloade geschiedenis bevat een record met tracerings logboeken voor draag signaal. Er worden twee sets logboeken die overeenkomen met de twee knoop punten op een Data Box Heavy apparaat. Als u naar de onderkant van dit logboek bladert, kunt u koppelingen zien naar:
    
   - **Logboeken kopiëren** : laat de lijst met bestanden die zijn opgetreden tijdens het kopiëren van de gegevens uit het data box naar uw Azure Storage-account (import volgorde) of van uw opslag account naar de data box (export volgorde).
   - **Audit logboeken** : bevatten informatie over het inschakelen van de data box en het openen van toegang tot shares wanneer de data Box zich buiten het Azure-Data Center bevindt.
   - **Stuk lijst bestanden in import volgorde** : hebben de lijst met bestanden (ook wel het bestand manifest genoemd) die u tijdens **voorbereiding voor verzending** kunt downloaden en heeft bestands namen, bestands grootten en bestands controlesom.
   - **Uitgebreide Logboeken in export volgorde** : bevatten de lijst met bestanden met bestands namen, bestands grootten en reken kundige berekeningen wanneer de gegevens uit de Azure Storage accounts naar de data box zijn gekopieerd.

   Hier volgt een voor beeld van een order geschiedenis van een import volgorde.

    ```output
    -------------------------------
    Microsoft Data Box Order Report
    -------------------------------
    Name                                               : DataBoxTestOrder                              
    StartTime(UTC)                                     : 10/31/2018 8:49:23 AM +00:00                       
    DeviceType                                         : DataBox                                           
    -------------------
    Data Box Activities
    -------------------
    Time(UTC)                 | Activity                       | Status          | Description  
    
    10/31/2018 8:49:26 AM     | OrderCreated                   | Completed       |                                                   
    11/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |                                                   
    11/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 11/3/2018 1:36:43        PM at AMSTERDAM-NLD                                                                                
    11/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 11/4/2018        8:23:30 PM at AMSTERDAM-NLD                                                                        
    11/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time :          11/4/2018 11:43:34 PM at AMSTERDAM-NLD                                                               
    11/5/2018 1:38:20 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LEIPZIG-DEU. Local Time :        11/5/2018 1:38:20 AM at LEIPZIG-DEU                                                                
    11/5/2018 2:31:07 AM      | ShippingToCustomer             | InProgress      | Processed at LEIPZIG-DEU. Local Time : 11/5/2018          2:31:07 AM at LEIPZIG-DEU                                                                            
    11/5/2018 4:05:58 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LEIPZIG-DEU. Local Time :            11/5/2018 4:05:58 AM at LEIPZIG-DEU                                                                    
    11/5/2018 4:35:43 AM      | ShippingToCustomer             | InProgress      | Transferred through LUTON-GBR. Local Time :              11/5/2018 4:35:43 AM at LUTON-GBR                                                                         
    11/5/2018 4:52:15 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LUTON-GBR. Local Time :              11/5/2018 4:52:15 AM at LUTON-GBR                                                                        
    11/5/2018 5:47:58 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 10/5/2018 5:47:58 AM at LONDON-HEATHROW-GBR                                                
    11/5/2018 6:27:37 AM      | ShippingToCustomer             | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            11/5/2018 6:27:37 AM at LONDON-HEATHROW-GBR                                                            
    11/5/2018 6:39:40 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 11/5/2018 6:39:40 AM at LONDON-HEATHROW-GBR                                                    
    11/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in LAMBETH-GBR. Local        Time : 11/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
    11/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 11/5/2018            9:13:24 AM at LAMBETH-GBR                                                                               
    11/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 11/5/2018        12:03:04 PM at LAMBETH-GBR                                                                          
    1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25        PM at LAMBETH-GBR                                                                                       
    1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at LAMBETH-GBR. Local Time : 1/25/2019          8:03:55 PM at LAMBETH-GBR                                                                            
    1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in LAMBETH-GBR. Local Time :            1/25/2019 8:04:58 PM at LAMBETH-GBR                                                                    
    1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
    1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
    1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR                                                   
    1/26/2019 2:17:10 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility BRUSSELS-BEL. Local Time        : 1/26/2019 2:17:10 PM at BRUSSELS-BEL                                                              
    1/26/2019 2:31:57 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        2:31:57 PM at BRUSSELS-BEL                                                                          
    1/26/2019 3:37:53 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        3:37:53 PM at BRUSSELS-BEL                                                                          
    1/27/2019 11:01:45 AM     | ShippingToDataCenter           | InProgress      | Departed Facility in BRUSSELS-BEL. Local Time :          1/27/2019 11:01:45 AM at BRUSSELS-BEL                                                                 
    1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD.            Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
    1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019            9:07:57 AM at AMSTERDAM-NLD                                                                             
    1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019            1:35:56 PM at AMSTERDAM-NLD                                                                            
    1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019        2:57:48 PM at AMSTERDAM-NLD                                                                         
    1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |                                              
    1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully                  
    1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.                               
    1/29/2019 4:55:00 PM      | DataCopy                       | Started         |                                                 
    2/2/2019 7:07:34 PM       | DataCopy                       | Completed       | Copy Completed.                                   
    2/4/2019 7:47:32 PM       | SecureErase                    | Started         |                                                  
    2/4/2019 8:01:10 PM       | SecureErase                    | Completed       | Azure Data Box:DEVICESERIALNO has been sanitized          according to NIST 800-88 Rev 1.                                                                       

    ------------------
    Data Box Log Links
    ------------------

    Account Name         : Gus                                                       
    Copy Logs Path       : databoxcopylog/DataBoxTestOrder_CHC533180024_CopyLog_73a81b2d613547a28ecb7b1612fe93ca.xml
    Audit Logs Path      : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024
    BOM Files Path       : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024      
    ```

    U kunt vervolgens naar uw opslagaccount gaan en de logboeken met kopieerbewerkingen bekijken.

   ![De Kopieer logboeken voor een opslag account](media/data-box-portal-admin/portal-admin-storage-account-copy-logs.png)

   U kunt ook de keten van Bewaar logboeken weer geven, waaronder de audit logboeken en de stuk lijst bestanden.

   ![Keten van Bewaar logboeken voor een opslag account](media/data-box-portal-admin/portal-admin-storage-account-chain-of-custody-logs.png)

## <a name="view-order-status"></a>Orderstatus bekijken

Wanneer de apparaatstatus wordt gewijzigd in de portal, ontvangt u een melding via een e-mail bericht.

### <a name="statuses-for-import-order"></a>Statussen voor import order

Dit zijn de statussen voor een import volgorde.

|Orderstatus |Beschrijving |
|---------|---------|
|Besteld     | De order is geplaatst. <br>Als het apparaat beschikbaar is, identificeert Microsoft het apparaat dat moet worden verzonden en bereidt Microsoft het apparaat voor. <br> Als het apparaat niet onmiddellijk beschikbaar is, wordt de volg orde verwerkt wanneer het apparaat weer beschikbaar wordt. Het kan enkele dagen tot een paar maanden duren voordat de order is verwerkt. Als de order niet binnen 90 dagen kan worden afgehandeld, wordt de order geannuleerd en wordt u hiervan op de hoogte gesteld.         |
|Verwerkt     | De order is verwerkt. In overeenstemming met uw order wordt het apparaat in het datacenter voorbereid voor verzending.         |
|Verzonden     | De order is verzonden. Gebruik het volgnummer dat in uw order in de portal wordt weergegeven om de verzending bij te houden.        |
|Afgeleverd     | De verzending is afgeleverd op het adres dat in de order is opgegeven.        |
|Opgehaald     |Uw retourzending is opgehaald en gescand door de vervoerder.         |
|Ontvangen     | Uw apparaat is ontvangen en gescand in het Azure-datacenter. <br> Zodra de verzending is geïnspecteerd, wordt de apparaatupload gestart.      |
|Gegevens kopiëren     | Het kopiëren van gegevens is in voortgang. Houd de kopieervoortgang voor uw order bij in de Azure-portal. <br> Wacht totdat het kopiëren van gegevens is voltooid. |
|Voltooid       |De order is voltooid.<br> Verifieer dat uw gegevens zich in Azure bevinden voordat u de on-premises gegevens van servers verwijdert.         |
|Voltooid met fouten| Het kopiëren van gegevens is voltooid, maar er zijn fouten opgetreden tijdens het kopiëren. <br> Bekijk de logboeken met kopieerbewerkingen via het pad in de Azure-portal. Zie [voor beelden van Kopieer Logboeken wanneer het uploaden is voltooid met fouten](./data-box-logs.md#upload-completed-with-errors).   |
|Voltooid met waarschuwingen| De gegevens kopie is voltooid, maar de gegevens zijn gewijzigd. De gegevens bevatten niet-kritieke BLOB-of bestandsnaam fouten die zijn opgelost door het wijzigen van de naam van het bestand of de blob. <br> Bekijk de logboeken met kopieerbewerkingen via het pad in de Azure-portal. Noteer de wijzigingen in uw gegevens. Zie [voor beelden van Kopieer Logboeken wanneer het uploaden is voltooid met waarschuwingen](./data-box-logs.md#upload-completed-with-warnings).   |
|Geannuleerd            |De order is geannuleerd. <br> U hebt de volg orde geannuleerd of de service heeft de volg orde geannuleerd nadat er een fout is opgetreden. Als de order niet binnen 90 dagen kan worden afgehandeld, wordt de order ook geannuleerd en wordt u hiervan op de hoogte gesteld.     |
|Opschonen | De gegevens op het apparaat worden gewist. Het opruimen van het apparaat wordt als voltooid beschouwd zodra de ordergeschiedenis beschikbaar is om te downloaden in de Azure-portal.|

### <a name="statuses-for-export-order"></a>Statussen voor export volgorde

Dit zijn de statussen voor een export volgorde.

|Orderstatus |Beschrijving |
|---------|---------|
|Besteld     | Er is een export volgorde geplaatst. <br>Als het apparaat beschikbaar is, identificeert Microsoft het apparaat dat moet worden verzonden en bereidt Microsoft het apparaat voor. <br> Als het apparaat niet onmiddellijk beschikbaar is, wordt de order verwerkt zodra het apparaat beschikbaar is. Het kan enkele dagen tot een paar maanden duren voordat de order is verwerkt. Als de order niet binnen 90 dagen kan worden voltooid, wordt deze geannuleerd en wordt u op de hoogte gesteld.         |
|Geannuleerd            |De order is geannuleerd. <br> U hebt de volg orde geannuleerd (u kunt alleen annuleren voordat de order wordt verwerkt) of er is een fout opgetreden. de order is geannuleerd door de service. Als de order niet binnen 90 dagen kan worden voltooid, wordt deze ook geannuleerd en wordt u op de hoogte gesteld.     |
|Verwerkt     | De order is verwerkt. Op basis van uw bestelling wordt het apparaat voor bereid voor het kopiëren van gegevens in het Data Center. Er worden apparaat shares gemaakt.         |
|De gegevens kopie wordt uitgevoerd     | Er wordt een gegevens kopie van de opgegeven Azure Storage accounts naar het apparaat uitgevoerd. Houd de kopieervoortgang voor uw order bij in de Azure-portal. <br> Wacht totdat het kopiëren van gegevens is voltooid. |
|Kopiëren is voltooid     | De gegevens kopie van de opgegeven Azure Storage accounts naar het apparaat is voltooid. Een uitgebreid logboek bestand (als de optie in de volg orde is ingeschakeld) en er in uw opslag account een kopie logboek wordt gemaakt. Het uitgebreide logboek bevat de informatie over alle bestanden (naam, pad, reken controlesom) die naar het apparaat worden gekopieerd. Het Kopieer logboek bevat de samen vatting van het kopieer proces, met inbegrip van een lijst met bestanden die niet kunnen worden gekopieerd vanwege fouten. <br> De gegevens van het opslag account blijven ongewijzigd. |
|Kopiëren voltooid met fouten| Het kopiëren van gegevens is voltooid, maar er zijn fouten opgetreden tijdens het kopiëren. <br> Controleer de Kopieer Logboeken in het Azure Storage-account met behulp van het pad in de Azure Portal. Zie [voor beelden van Kopieer Logboeken wanneer het downloaden is voltooid met fouten](./data-box-logs.md#upload-completed-with-errors).   |
|Kopiëren voltooid met waarschuwingen| De gegevens kopie van Azure Storage account is voltooid, maar de gegevens bevatten niet-kritieke fouten. <br> Bekijk de logboeken met kopieerbewerkingen via het pad in de Azure-portal. Noteer de niet-kritieke fouten. Zie [voor beelden van Kopieer Logboeken wanneer het downloaden is voltooid met waarschuwingen](./data-box-logs.md#upload-completed-with-warnings).   |
|Kopiëren is mislukt met fouten| Het kopiëren van gegevens uit Azure Storage account is mislukt en de volg orde wordt beëindigd. Er wordt geen apparaat verzonden. <br> Controleer de Kopieer Logboeken in het Azure Storage-account met behulp van het pad in de Azure Portal. Zie [voor beelden van Kopieer Logboeken wanneer het downloaden is mislukt met fouten](./data-box-logs.md#upload-completed-with-errors).   |
|Verzonden     |De order is verzonden. Gebruik het volgnummer dat in uw order in de portal wordt weergegeven om de verzending bij te houden.        |
|Afgeleverd     |De verzending is afgeleverd op het adres dat in de order is opgegeven.        |
|Opgehaald     |Uw retourzending is opgehaald en gescand door de vervoerder.         |
|Ontvangen     | Uw apparaat is ontvangen en gescand in het Azure-datacenter. <br> De verzen ding wordt geïnspecteerd.      |
|Voltooid           |De volg orde is voltooid.     |
|Opschonen | De gegevens op het apparaat worden gewist. Het opruimen van het apparaat wordt als voltooid beschouwd zodra de ordergeschiedenis beschikbaar is om te downloaden in de Azure-portal.|

> [!NOTE]
> Als de Kopieer taak voor het exporteren van gegevens van Azure Storage accounts naar Data Box is voltooid met fouten of waarschuwingen, wordt het apparaat nog steeds geleverd. Alleen als er een Kopieer fout is opgetreden, wordt de order beëindigd en wordt het apparaat niet verzonden.


Als u gebruikmaakt van zelf-beheerde verzen ding nadat de kopie is voltooid en voordat u het apparaat ontvangt, ziet u de volgende statussen (in plaats van de items die in de voor gaande tabel worden genoemd):

|Orderstatus |Beschrijving |
|---------|---------|
|Gereed voor ophalen in azure Data Center      |Het apparaat kan worden opgenomen in het Azure-Data Center.        |
|Opgehaald    |U hebt het apparaat geselecteerd.         |
|Klaar om te ontvangen in azure Data Center     |Het apparaat kan worden ontvangen in het Azure-Data Center.        |
|Ontvangen     |Het apparaat is ontvangen op het Azure-Data Center.      |





## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het oplossen van problemen [met data box en data Box Heavy](data-box-troubleshoot.md).