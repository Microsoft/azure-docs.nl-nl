---
title: Uw Azure Stack Edge Pro-apparaat retour neren | Microsoft Docs
description: Meer informatie over het wissen van de gegevens en het retour neren van uw Azure Stack Edge Pro-apparaat en het verwijderen van de bron die aan het apparaat is gekoppeld.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.openlocfilehash: cb11d7d3b2da9ab793cb18814e4021ea7afeb806
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443587"
---
# <a name="return-your-azure-stack-edge-pro-device"></a>Uw Azure Stack Edge Pro-apparaat retour neren

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u de gegevens kunt wissen en vervolgens uw Azure Stack Edge Pro-apparaat kunt herstellen. Nadat u het apparaat hebt geretourneerd, kunt u ook de resource verwijderen die aan het apparaat is gekoppeld.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * De gegevens van de gegevens schijven op het apparaat wissen
> * Retourzending van apparaat starten in de Azure-portal
> * Het apparaat inpakken en een ophaling plannen
> * De resource in Azure Portal verwijderen

## <a name="erase-data-from-the-device"></a>Gegevens van het apparaat wissen

Als u de gegevens van de gegevens schijven van uw apparaat wilt wissen, moet u uw apparaat opnieuw instellen.

Voordat u opnieuw instelt, maakt u indien nodig een kopie van de lokale gegevens op het apparaat. U kunt de gegevens van het apparaat naar een Azure Storage-container kopiëren. 

U kunt het retour neren van het apparaat starten, zelfs voordat het apparaat opnieuw wordt ingesteld.

U kunt uw apparaat opnieuw instellen in de lokale webgebruikersinterface of in Power shell. Zie [uw apparaat opnieuw instellen](./azure-stack-edge-connect-powershell-interface.md#reset-your-device)voor instructies voor Power shell.

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

> [!NOTE]
> - Als u uitwisselt of een upgrade naar een nieuw apparaat uitvoert, raden wij u aan uw apparaat opnieuw in te stellen nadat u het nieuwe apparaat hebt ontvangen.
> - Bij het opnieuw instellen van het apparaat worden alleen alle lokale gegevens van het apparaat verwijderd. De gegevens die zich in de Cloud bevindt, worden niet verwijderd en er worden [kosten](https://azure.microsoft.com/pricing/details/storage/)in rekening gebracht. Deze gegevens moeten afzonderlijk worden verwijderd met behulp van een beheer programma voor Cloud opslag, zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="initiate-device-return"></a>Retour neren van apparaat starten

Voer de volgende stappen uit om het retour proces te starten.

1. Ga in Azure Portal naar uw Azure Stack Edge Pro/Data Box Gateway-resource. In het **overzicht** gaat u naar de opdracht balk in het rechterdeel venster en selecteert u **apparaat retour neren**. 

    ![Apparaat 1 retour neren](media/azure-stack-edge-return-device/return-device-1.png)  

2. Op de Blade **retour apparaat** , onder **basis Details**:

    1. Geef het serie nummer van het apparaat op. Als u het serie nummer van het apparaat wilt ophalen, gaat u naar de lokale web-UI van het apparaat en gaat u naar **overzicht**.  
    
       ![Serie nummer 1 van apparaat](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. Voer het servicetag nummer in. Het servicetag nummer is een id met vijf of meer tekens die uniek is voor uw apparaat. De servicetag bevindt zich in de rechter benedenhoek van het apparaat (als u het apparaat bewaart). Haal het informatie label uit (dit is een deel venster voor een uitgaand label). Dit paneel bevat systeem informatie zoals service tags, NIC, MAC-adres, enzovoort. 
    
       ![Servicetag nummer 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. Kies in de vervolg keuzelijst een reden voor de retour nering.

       ![Apparaat 2 retour neren](media/azure-stack-edge-return-device/return-device-2.png) 

3. Onder **Verzend gegevens**:

    1. Geef uw naam, bedrijfs naam en volledig bedrijfs adres op. Voer een werk telefoonnummer in, inclusief het netnummer en een e-mail-ID voor de melding.
    2. Als u een verzend doos nodig hebt, kunt u deze aanvragen. Antwoord **Ja** op de vraag **moet een leeg vak zijn om te retour neren**.

    ![Apparaat 3 retour neren](media/azure-stack-edge-return-device/return-device-3.png)

4. Lees de **Privacy-voor waarden** en schakel het selectie vakje in voor de opmerking die u hebt bekeken en ga akkoord met de voor waarden van de privacyverklaring.

5. Selecteer **return starten**.

    ![Apparaat 4 retour neren](media/azure-stack-edge-return-device/return-device-4.png) 

6. Zodra de details van het apparaat zijn vastgelegd, kunt u via een e-mail aan het Azure Stack Edge pro-team door sturen. U kunt uw e-mail toepassing gebruiken als u ervan uitgaat dat de e-mail toepassing is geïnstalleerd en geconfigureerd. U kunt ook de gegevens kopiëren om deze te maken en een e-mail te verzenden.

    ![Apparaat 5 retour neren](media/azure-stack-edge-return-device/return-device-5.png) 

7. Zodra het Azure Stack Edge Pro Operations-team het e-mail bericht ontvangt, wordt er een terugkerend verzend label verzonden. Wanneer u dit label ontvangt, kunt u het ophalen van het apparaat plannen met de provider. 

## <a name="schedule-a-pickup"></a>Een ophaling plannen

Als u een ophaling wilt plannen, voert u de volgende stappen uit.

1. Schakel het apparaat uit. Ga in de lokale web-UI naar **onderhoud > energie-instellingen**.
2. Selecteer **Afsluiten**. Wanneer u om bevestiging wordt gevraagd, klikt u op **Ja** om door te gaan. Zie [energie beheren](../databox-gateway/data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)voor meer informatie.
3. Koppel de stroom kabels los en verwijder alle netwerk kabels van het apparaat.
4. Bereid het verzend pakket voor met behulp van uw eigen box of het lege vak dat u van Azure hebt ontvangen. Plaats het apparaat en de stroom snoeren die met het apparaat in het vak zijn geleverd.
5. Breng het verzend label op dat u hebt ontvangen van Azure op het pakket.
6. Maak een afspraak met een transportbedrijf voor het ophalen van de zending. Als u het apparaat in ons retourneert, kan uw provider UPS of FedEx zijn. Een ophaling plannen met UPS:

    1. Bel met UPS (gratis land-/regiospecifiek nummer).
    2. Neem in uw gesprek het tracerings nummer van omgekeerde verzen ding op zoals op het afgedrukte label wordt weer gegeven.
    3. Als het tracking nummer niet wordt vermeld, moet u tijdens het ophalen een extra kosten betalen.

    In plaats van het ophalen te plannen, kunt u ook de Azure Stack Edge Pro verwijderen op de dichtstbijzijnde uitval locatie.

## <a name="delete-the-resource"></a>De resource verwijderen

Nadat het apparaat is ontvangen in het Azure-Data Center, wordt het apparaat geïnspecteerd op beschadiging of op een geknoeid tijdstip.

- Als het apparaat intact is en een goede vorm heeft, stopt de facturerings meter voor die bron. Azure Stack Edge Pro Operations-team neemt contact met u op om te bevestigen dat het apparaat is geretourneerd. U kunt vervolgens de resource die aan het apparaat is gekoppeld, verwijderen in de Azure Portal.
- Als het apparaat aanzienlijk is aangekomen, kunnen de verfijningen van toepassing zijn. Raadpleeg de [Veelgestelde vragen op verloren of beschadigd apparaat](https://azure.microsoft.com/pricing/details/databox/edge/) en [product service voorwaarden](https://www.microsoft.com/licensing/product-licensing/products)voor meer informatie.  


U kunt het apparaat verwijderen in de Azure Portal:

- Nadat u een bestelling hebt geplaatst en voordat het apparaat door micro soft wordt voor bereid.
- Nadat u een apparaat hebt geretourneerd naar micro soft, en het Azure Stack Edge Pro Operations-team heeft opgeroepen om te bevestigen dat het apparaat is geretourneerd. Het operations-team belt pas wanneer het geretourneerde apparaat de fysieke inspectie in het Azure-Data Center doorgeeft.

Als u het apparaat hebt geactiveerd op basis van een ander abonnement of een andere locatie, zal micro soft uw bestelling binnen één werkdag naar het nieuwe abonnement of deze locatie verplaatsen. Nadat de order is verplaatst, kunt u deze resource verwijderen.


Voer de volgende stappen uit om het apparaat en de resource in Azure Portal te verwijderen.

1. Ga in het Azure Portal naar uw resource en klik vervolgens op **overzicht**. Selecteer **verwijderen** op de opdracht balk.

    ![Verwijderen selecteren](media/azure-stack-edge-return-device/delete-resource-1.png)

2. Typ op de Blade **apparaat verwijderen** de naam van het apparaat dat u wilt verwijderen en selecteer **verwijderen**.

    ![De verwijdering bevestigen](media/azure-stack-edge-return-device/delete-resource-2.png)

U wordt gewaarschuwd wanneer het apparaat en de bijbehorende resource zijn verwijderd.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [verkrijgen van een vervangend Azure stack Edge Pro-apparaat](azure-stack-edge-replace-device.md).