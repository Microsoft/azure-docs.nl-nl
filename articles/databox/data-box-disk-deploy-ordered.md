---
title: Zelfstudie voor bestellen van Azure Data Box Disk | Microsoft Docs
description: Gebruik deze zelfstudie om te leren hoe u zich registreert en een Azure Data Box Disk bestelt waarmee u gegeven in Azure importeert.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 04ae2589e8abfab4826f2286d5f00869c24442f5
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057963"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>Zelfstudie: Een Azure Data Box Disk bestellen

Azure Data Box Disk is een hybride cloudoplossing waarmee u uw on-premises gegevens snel, eenvoudig en betrouwbaar in Azure importeert. U zet uw gegevens op SSD-schijven (solid-state drives) die Microsoft naar u heeft verstuurd en u stuurt de schijven terug. Deze gegevens worden vervolgens geüpload in Azure.

In deze zelfstudie wordt beschreven hoe u een Azure Data Box Disk bestelt. In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Een Data Box Disk bestellen
> * De bestelling volgen
> * De bestelling annuleren

## <a name="prerequisites"></a>Vereisten

Voltooi voordat u met de implementatie begint de volgende configuratievereisten voor Data Box service en Data Box Disk.

### <a name="for-service"></a>Voor de service

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Voor het apparaat

Zorg voordat u begint voor het volgende:

* U hebt een clientcomputer beschikbaar van waaruit u de gegevens kunt kopiëren. De clientcomputer moet voldoen aan deze vereisten:
  * Een [ondersteund besturingssysteem](data-box-disk-system-requirements.md#supported-operating-systems-for-clients) worden uitgevoerd.
  * Andere [vereiste software](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) is geïnstalleerd in het geval van een Windows-client.  

## <a name="order-data-box-disk"></a>Data Box Disk bestellen

Meld u aan bij:

* Azure Portal bij deze URL: https://portal.azure.com om Data Box Disk te bestellen.
* Of de Azure Government-portal op deze URL: https://portal.azure.us. Ga naar [Verbinding maken met Azure Government met behulp van de portal](../azure-government/documentation-government-get-started-connect-with-portal.md) voor meer informatie.

Voer de volgende stappen uit om Data Box Disk te bestellen.

1. Klink linksboven in de hoek van de portal op **+ Een resource maken** en zoek naar *Azure Data Box*. Klik op **Azure Data Box**.

   ![Zoek naar Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Klik op **Create**.

3. Controleer of de Data Box-service beschikbaar is in uw regio. Voer de volgende gegevens in of selecteer deze en klik op **Toepassen**.

    ![De optie Data Box Disk selecteren](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Instelling|Waarde|
    |---|---|
    |Abonnement|Selecteer het abonnement waarvoor Data Box is ingeschakeld.<br> Het abonnement is gekoppeld aan uw factureringsrekening. |
    |Type overdracht| Importeren in Azure|
    |Bronland/-regio | Selecteer het land/de regio waar uw gegevens zich momenteel bevinden.|
    |Doel-Azure-regio|Selecteer de Azure-regio waarnaar u uw gegevens wilt overdragen.|
  
4. Selecteer **Data Box Disk**. De maximumcapaciteit van de oplossing voor één bestelling van 5 schijven is 35 TB. U kunt meerdere bestellingen doen voor grotere gegevensgrootten.

     ![Optie 2 voor Data Box Disk selecteren](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

5. Voer in **Bestellen** de **Orderdetails** in. Voer de volgende informatie in of selecteer deze.

    |Instelling|Waarde|
    |---|---|
    |Naam|Geef een beschrijvende naam op om de bestelling te volgen.<br> De naam kan tussen 3 en 24 tekens bevatten (letters, cijfers en afbreekstreepjes). <br> De naam moet beginnen en eindigen met een letter of cijfer. |
    |Resourcegroep| Gebruik een bestaande of maak een nieuwe. <br> Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd. |
    |Doel-Azure-regio| Selecteer een regio voor uw opslagaccount.<br> Momenteel worden opslagaccounts in alle regio's in de VS, West- en Noord-Europa, Canada en Australië ondersteund. |
    |Geschatte gegevensgrootte in TB| Voer een schatting in TB in. <br>Op basis van de gegevensgrootte stuurt Microsoft u het juiste aantal SSD-schijven van 8 TB (7 TB aan bruikbare capaciteit). <br>De maximale bruikbare capaciteit van 5 schijven is 35 TB. |
    |Wachtwoordsleutel voor schijf| Geef de wachtwoordsleutel voor schijf op als u het selectievakje **Aangepaste sleutel gebruiken in plaats van door Azure gegenereerde sleutel** inschakelt. <br> Geef een alfanumerieke sleutel tussen 12 tot 32 tekens lang op met ten minste één numeriek en één speciaal teken. Alleen de speciale tekens `@?_+` zijn toegestaan. <br> U kunt deze optie overslaan en de door Azure gegenereerde wachtwoordsleutel gebruiken om uw schijven te ontgrendelen.|
    |Opslaglocatie     | Kies een opslagaccount, beheerde schijven of beide. <br> Selecteer een opslagaccount in de gefilterde lijst van een bestaand opslagaccount, gebaseerd op de opgegeven Azure-regio. Data Box Disk kan worden gekoppeld aan maximaal 1 opslagaccount. <br> U kunt ook een nieuw account van het type **Algemeen gebruik v1**, **Algemeen gebruik v2** of **Blob-opslag** maken. <br>Opslagaccounts met virtuele netwerken worden ondersteund. Als u wilt dat de Data Box-service kan werken met beveiligde opslagaccounts, schakelt u in de firewallinstellingen van het opslagaccount de vertrouwde services in. Zie [Azure Data Box toevoegen als een vertrouwde service](../storage/common/storage-network-security.md#exceptions) voor meer informatie.|

    Als u een opslagaccount selecteert als de opslaglocatie, ziet u het volgende scherm:

    ![Data Box Disk-bestelling voor opslagaccount](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Als u Data Box Disk gebruikt om beheerde schijven te maken op basis van de on-premises VHD's, moet u ook de volgende informatie opgeven:

    |Instelling  |Waarde  |
    |---------|---------|
    |Resourcegroep     | Maak een nieuwe resourcegroep als u beheerde schijven wilt maken van on-premises VHD's. Gebruik een bestaande resourcegroep alleen als deze is gemaakt voor de Data Box Disk-bestelling voor beheerde schijven via de Data Box-service. <br> Er wordt slechts één resourcegroep ondersteund.|

    ![Data Box Disk-bestelling voor beheerde schijf](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    Het opslagaccount dat is opgegeven voor beheerde schijven wordt gebruikt als een opslagaccount waarin de gegevens worden klaargezet. De Data Box-service uploadt de virtuele harde schijven naar het faseringsopslagaccount waarna deze worden omgezet in beheerde schijven en naar de resourcegroepen worden verplaatst. Zie [Uploaden van gegevens naar Azure controleren](data-box-disk-deploy-upload-verify.md#verify-data-upload-to-azure) voor meer informatie.

6. Klik op **Volgende**.

    ![Ordergegevens invoeren](media/data-box-disk-deploy-ordered/data-box-order-details.png)

7. In het tabblad **Verzendadres** geeft u uw voor- en achternaam, de naam en het postadres van het bedrijf en een geldig telefoonnummer op. Klik op **Adres valideren**. De service controleert of de service beschikbaar is voor de regio van het verzendadres. Als de service beschikbaar is voor het opgegeven verzendadres, ontvangt u daarover een melding.

   Nadat de bestelling is verwerkt, ontvangt u een e-mailmelding. Zie [Zelfbeheerde verzending gebruiken](data-box-disk-portal-customer-managed-shipping.md) als u voor zelfbeheerde verzending hebt gekozen.

    ![Verzendadres opgeven](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
8. In de **Meldingsdetails** geeft u een e-mailadres op. De service stuurt e-mailmeldingen naar het opgegeven e-mailadres over updates van de bestelstatus.

    We raden u aan een e-mailadres van een groep te gebruiken, zodat u meldingen blijft ontvangen als een beheerder de groep verlaat.

9. Bekijk de gegevens met betrekking tot de bestelling, het contact, de meldingen en de privacyvoorwaarden in **Samenvatting**. Vink het selectievakje aan waarmee u akkoord gaat met de privacyvoorwaarden.

10. Klik op **Bestellen**. Het duurt een paar minuten voordat de bestelling is gemaakt.

## <a name="track-the-order"></a>De bestelling volgen

Nadat u uw bestelling hebt geplaatst, kunt u de status van de bestelling volgen via de Azure-portal. Ga naar uw bestelling en vervolgens naar **Overzicht** om de status te bekijken. In de portal ziet u dat de taak de status **Besteld** heeft.

![Status bestel van Data Box Disk](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png)

Als de schijven niet beschikbaar zijn, ontvangt u een melding. Als de schijven beschikbaar zijn, identificeert Microsoft de schijven die moeten worden verzonden en bereidt Microsoft een schijfpakket voor. Tijdens de voorbereiding van de schijf vinden de volgende acties plaats:

* De schijven worden versleuteld met AES-128 BitLocker-versleuteling.  
* De schijven worden vergrendeld om onbevoegde toegang tot de schijven te voorkomen.
* De sleutel die de schijven ontgrendelt, wordt tijdens dit proces gegenereerd.

Wanneer de schijfvoorbereiding is voltooid, wordt de bestelling in de portal weergegeven met de status **Verwerkt**.

Microsoft bereidt uw schijven vervolgens voor en verzendt deze met een regionale vervoerder. U ontvangt uw volgnummer wanneer de schijven zijn verzonden. In de portal wordt bestelling weergegeven met de status **Verzonden**.

## <a name="cancel-the-order"></a>De bestelling annuleren

Als u deze bestelling wilt annuleren, gaat u in de Azure-portal naar **Overzicht** en klikt u in de opdrachtbalk op **Annuleren**.

U kunt alleen annuleren wanneer de schijven zijn besteld en de bestelling nog wordt verwerkt voor verzending. Nadat de bestelling is verwerkt, kunt u de bestelling niet meer annuleren.

![Bestelling annuleren](media/data-box-disk-deploy-ordered/cancel-order1.png)

Als u een geannuleerde bestelling wilt verwijderen, gaat u naar **Overzicht** en klikt u in de opdrachtbalk op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Data Box, zoals:

> [!div class="checklist"]
>
> * Data Box Disk bestellen
> * De bestelling volgen
> * De bestelling annuleren

Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box Disk instelt.

> [!div class="nextstepaction"]
> [Uw Azure Data Box Disk instellen](./data-box-disk-deploy-set-up.md)