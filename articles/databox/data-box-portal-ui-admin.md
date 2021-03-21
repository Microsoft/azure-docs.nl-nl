---
title: Beheerhandleiding voor de Azure Data Box Disk-portal | Microsoft Docs
description: Meer informatie over het beheren van de Data Box Disk met behulp van de Azure Portal. Beheer orders, beheer schijven en volg de status van een order terwijl deze wordt uitgevoerd.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 538a650c6063422f89c8ed3d1753981a293693b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94338038"
---
# <a name="use-azure-portal-to-administer-your-data-box-disk"></a>De Azure-portal gebruiken om uw Data Box te beheren

De zelfstudies in dit artikel zijn van toepassing op de preview-versie van de Microsoft Azure Data Box Disk. In dit artikel worden enkele van de complexe werkstromen en beheertaken beschreven die kunnen worden uitgevoerd in de Data Box Disk. 

U kunt de Data Box Disk beheren via de Azure-portal. Dit artikel richt zich op de taken die u kunt uitvoeren met behulp van de Azure-portal. Gebruik de Azure-portal voor het beheren van orders, beheren van apparaten en bijhouden van de status van de order terwijl deze naar de definitieve fase gaat.

## <a name="cancel-an-order"></a>Een order annuleren

Soms moet u een order om een bepaalde reden annuleren nadat u deze hebt geplaatst. U kunt de order alleen annuleren als de schijfvoorbereiding nog niet is gestart. Zodra de schijven zijn voorbereid en de order is verwerkt, is het niet meer mogelijk de order te annuleren. 

Voer de volgende stappen uit om een order te annuleren.

1.  Ga naar **Overzicht > Annuleren**. 

    ![Opdracht annuleren op het tabblad Overzicht voor een order](media/data-box-portal-ui-admin/portal-ui-admin-cancel-command.png)

2.  Geef een reden op voor het annuleren van de order.  

    ![Reden voor het annuleren van een order](media/data-box-portal-ui-admin/portal-ui-admin-cancel-order-reason.png)

3.  Zodra de order is geannuleerd, werkt de portal de status van de order bij naar **Geannuleerd**.

    ![Geannuleerde order](media/data-box-portal-ui-admin/portal-ui-admin-canceled-order.png)

U ontvangt geen e-mailmelding wanneer de order is geannuleerd.

## <a name="clone-an-order"></a>Een order klonen

Klonen is handig in bepaalde situaties. Een voorbeeld: Een gebruiker heeft Data Box Disk gebruikt om wat gegevens over te dragen. Naarmate er meer gegevens worden gegenereerd, zijn er meer schijven nodig om die gegevens naar Azure over te dragen. In dit geval kan dezelfde order gewoon worden gekloond.

Voer de volgende stappen uit om een order te klonen.

1.  Ga naar **Overzicht > Klonen**. 

    ![De opdracht Clone op het tabblad Overzicht voor een order](media/data-box-portal-ui-admin/portal-ui-admin-clone-command.png)

2.  Alle details van de order blijven hetzelfde. De ordernaam is de oorspronkelijke ordernaam met *-Kloon* eraan toegevoegd. Schakel het selectievakje in om te bevestigen dat u de privacyinformatie hebt gelezen. Klik op **Create**.    

De kloon wordt binnen enkele minuten gemaakt en de portal wordt bijgewerkt met de nieuwe order.

[![Gekloonde volg orde](media/data-box-portal-ui-admin/portal-ui-admin-cloned-order.png)](media/data-box-portal-ui-admin/portal-ui-admin-cloned-order.png#lightbox) 

## <a name="delete-order"></a>Order verwijderen

Wanneer de order is voltooid, wilt u deze wellicht verwijderen. De order bevat uw persoonlijke informatie, zoals naam, adres en contactgegevens. Deze persoonlijke informatie wordt verwijderd wanneer de order wordt verwijderd.

U kunt alleen orders verwijderen die zijn voltooid of geannuleerd. Voer de volgende stappen uit om een order te verwijderen.

1. Ga naar **Alle resources**. Zoek uw order.

    ![Zoek opdrachten](media/data-box-portal-ui-admin/portal-ui-admin-search-data-box-disk-orders.png)

2. Klik op de order die u wilt verwijderen en ga naar **Overzicht**. Klik in de opdrachtbalk op **Verwijderen**.

    ![Een bestelling verwijderen](media/data-box-portal-ui-admin/portal-ui-admin-delete-command.png)

3. Voer de naam van de order in wanneer u wordt gevraagd de orderverwijdering te bevestigen. Klik op **Verwijderen**.

     ![Verwijdering van een bestelling bevestigen](media/data-box-portal-ui-admin/portal-ui-admin-confirm-deletion.png)


## <a name="download-shipping-label"></a>Verzendlabel downloaden

Als het verzendlabel voor retourzending dat bij uw schijven is meegeleverd is kwijtgeraakt, kunt u het downloaden. 

Voer de volgende stappen uit om een verzendlabel te downloaden.
1.  Ga naar **Overzicht > Verzendlabel downloaden**. Deze optie is alleen beschikbaar nadat de schijf is verzonden. 

    ![Verzendlabel downloaden](media/data-box-portal-ui-admin/portal-ui-admin-download-shipping-label.png)

2.  Hiermee downloadt u het volgende verzendlabel voor retourzending. Sla het label op, druk het af en bevestig het aan de retourzending.

    ![Voorbeeld van verzendlabel](media/data-box-portal-ui-admin/portal-ui-admin-example-shipping-label.png)

## <a name="edit-shipping-address"></a>Verzendadres bewerken

U moet het verzendadres wellicht bewerken nadat de order is geplaatst. Dit kan alleen als de schijf nog niet is verzonden. Zodra de schijf is verzonden, is deze optie niet meer beschikbaar.

Voer de volgende stappen uit om de order te bewerken.

1. Ga naar **Ordergegevens > Verzendadres bewerken**.

    ![Opdracht voor het verzend adres bewerken in de details van de order](media/data-box-portal-ui-admin/portal-ui-admin-edit-shipping-address-command.png)

2. U kunt het verzendadres nu bewerken en de wijzigingen vervolgens opslaan.

    ![Het dialoog venster verzend adres bewerken](media/data-box-portal-ui-admin/portal-ui-admin-edit-shipping-address-dbox.png)

## <a name="edit-notification-details"></a>Meldingsdetails bewerken

U moet wellicht wijzigen welke gebruikers de e-mails met de orderstatus ontvangen. Een voorbeeld: Een bepaalde gebruiker moet worden geïnformeerd wanneer de schijf wordt afgeleverd of opgehaald. Een andere gebruiker moet mogelijk een melding ontvangen wanneer het kopiëren van de gegevens is voltooid, zodat de gegevens in het Azure Storage-account kunnen worden gecontroleerd voordat deze uit de bron worden verwijderd. In deze gevallen kunt u de meldingsdetails bewerken.

Voer de volgende stappen uit om meldingsdetails te bewerken.

1. Ga naar **Ordergegevens > Meldingsdetails bewerken**.

    ![De opdracht Details van melding bewerken in de details van de order](media/data-box-portal-ui-admin/portal-ui-admin-edit-notification-details-command.png)

2. U kunt de meldingsdetails nu bewerken en de wijzigingen vervolgens opslaan.
 
    ![Het dialoog venster meldings Details bewerken](media/data-box-portal-ui-admin/portal-ui-admin-edit-notification-details-dbox.png)

## <a name="view-order-status"></a>Orderstatus bekijken

|Orderstatus |Beschrijving |
|---------|---------|
|Besteld     | De order is geplaatst. <br> Als de schijven niet beschikbaar zijn, ontvangt u een melding. <br>Als de schijven beschikbaar zijn, identificeert Microsoft een schijf die moet worden verzonden en bereidt Microsoft een schijfpakket voor.        |
|Verwerkt     | De order is verwerkt. <br> Tijdens de orderverwerking vinden de volgende acties plaats:<li>De schijven worden versleuteld met AES-128 BitLocker-versleuteling. </li> <li>De Data Box Disks worden vergrendeld om onbevoegde toegang te voorkomen.</li><li>De sleutel die de schijven ontgrendelt, wordt tijdens dit proces gegenereerd.</li>        |
|Verzonden     | De order is verzonden. U moet de order binnen 1 tot 2 dagen ontvangen.        |
|Afgeleverd     | De order is afgeleverd op het adres dat in de order is opgegeven.        |
|Opgehaald     |Uw retourzending is opgehaald. <br> Zodra de retourzending is ontvangen in het Azure-datacenter, worden de gegevens automatisch geüpload naar Azure.         |
|Ontvangen     | Uw schijven zijn ontvangen door het Azure-datacenter. Het kopiëren van gegevens zal binnenkort starten.        |
|Gegevens gekopieerd     |Het kopiëren van gegevens is in voortgang.<br> Wacht totdat het kopiëren van gegevens is voltooid.         |
|Voltooid       |De order is voltooid.<br> Verifieer dat uw gegevens zich in Azure bevinden voordat u de on-premises gegevens van servers verwijdert.         |
|Voltooid met fouten| Het kopiëren van gegevens is voltooid, maar er zijn fouten ontvangen. <br> Controleer de fouten logboeken voor uploaden met behulp van het pad dat wordt vermeld in het **overzicht**. Ga naar [down load Upload-fout logboeken](data-box-disk-troubleshoot-upload.md#download-logs)voor meer informatie.   |
|Geannuleerd            |De order is geannuleerd. <br> U hebt de order zelf geannuleerd, of er is een fout opgetreden waardoor de service de order heeft geannuleerd.     |



## <a name="next-steps"></a>Volgende stappen

- Leren hoe u [problemen met Data Box Disk oplost](data-box-disk-troubleshoot.md).
