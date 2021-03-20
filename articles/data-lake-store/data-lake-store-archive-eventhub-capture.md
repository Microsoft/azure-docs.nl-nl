---
title: Gegevens vastleggen van Event Hubs naar Azure Data Lake Storage Gen1
description: Meer informatie over het gebruik van Azure Data Lake Storage Gen1 voor het vastleggen van gegevens die zijn ontvangen door Azure Event Hubs. Controleer eerst de vereisten.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8be242369ecae2c809a38428284c9ddcad440e3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91578237"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Azure Data Lake Storage Gen1 gebruiken om gegevens vast te leggen van Event Hubs

Meer informatie over het gebruik van Azure Data Lake Storage Gen1 voor het vastleggen van gegevens die zijn ontvangen door Azure Event Hubs.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Een Azure data Lake Storage gen1-account**. Zie [aan de slag met Azure data Lake Storage gen1](data-lake-store-get-started-portal.md)voor instructies over het maken van een account.

*  **Een event hubs naam ruimte**. Zie [een event hubs naam ruimte maken](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)voor instructies. Zorg ervoor dat het Data Lake Storage Gen1-account en de Event Hubs naam ruimte zich in hetzelfde Azure-abonnement bevinden.


## <a name="assign-permissions-to-event-hubs"></a>Machtigingen toewijzen aan Event Hubs

In deze sectie maakt u een map in het account waar u de gegevens van Event Hubs wilt vastleggen. U wijst ook machtigingen toe aan Event Hubs zodat deze gegevens naar een Data Lake Storage Gen1 account kan schrijven. 

1. Open het Data Lake Storage Gen1 account waar u gegevens van Event Hubs wilt vastleggen en klik vervolgens op **Data Explorer**.

    ![Data Lake Storage Gen1 Data Explorer](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Storage Gen1 Data Explorer")

1.  Klik op **nieuwe map** en voer een naam in voor de map waarin u de gegevens wilt vastleggen.

    ![Een nieuwe map maken in Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Een nieuwe map maken in Data Lake Storage Gen1")

1. Machtigingen toewijzen in de hoofdmap van Data Lake Storage Gen1. 

    a. Klik op **Data Explorer**, selecteer de hoofdmap van het data Lake Storage gen1 account en klik vervolgens op **toegang**.

    ![Scherm opname van de Data Explorer met de hoofdmap van het account en de toegangs optie.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Machtigingen toewijzen voor de Data Lake Storage Gen1 root")

    b. Klik onder **toegang** op **toevoegen**, klik op **gebruiker of groep selecteren** en zoek vervolgens naar `Microsoft.EventHubs` . 

    ![Scherm afbeelding van de Access-pagina met de optie toevoegen, Selecteer gebruiker of groep en de optie micro soft Event hubs.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Machtigingen toewijzen voor de Data Lake Storage Gen1 root")
    
    Klik op **Selecteren**.

    c. Klik onder **machtigingen toewijzen** op **machtigingen selecteren**. Stel **machtigingen** in om uit te **voeren**. Stel **toevoegen in** op **deze map en alle onderliggende items**. Stel **toevoegen als** in op **een toegangs machtigings vermelding en een standaard machtigings vermelding**.

    > [!IMPORTANT]
    > Bij het maken van een nieuwe maphiërarchie voor het vastleggen van gegevens die zijn ontvangen door Azure Event Hubs, is dit een eenvoudige manier om toegang tot de doelmap te garanderen.  Het toevoegen van machtigingen aan alle onderliggende mappen van een map op het hoogste niveau met veel onderliggende bestanden en mappen kan veel tijd in beslag nemen.  Als uw hoofdmap een groot aantal bestanden en mappen bevat, is het wellicht sneller om **uitvoerings** machtigingen toe te voegen voor afzonderlijke `Microsoft.EventHubs` mappen in het pad naar de uiteindelijke doelmap. 

    ![Scherm afbeelding van de sectie Machtigingen toewijzen met de optie machtigingen selecteren. De sectie machtigingen selecteren bevindt zich naast de optie uitvoeren, toevoegen aan optie en toevoegen als optie.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Machtigingen toewijzen voor de Data Lake Storage Gen1 root")

    Klik op **OK**.

1. Wijs machtigingen toe voor de map onder het Data Lake Storage Gen1-account waarin u gegevens wilt vastleggen.

    a. Klik op **Data Explorer**, selecteer de map in het data Lake Storage gen1 account en klik vervolgens op **toegang**.

    ![Scherm opname van de Data Explorer met een map in het account en de toegangs optie.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Machtigingen toewijzen voor de map Data Lake Storage Gen1")

    b. Klik onder **toegang** op **toevoegen**, klik op **gebruiker of groep selecteren** en zoek vervolgens naar `Microsoft.EventHubs` . 

    ![Scherm afbeelding van de Access-pagina van Data Explorer met de optie toevoegen, Selecteer gebruiker of groep optie en de optie micro soft Event hubs.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Machtigingen toewijzen voor de map Data Lake Storage Gen1")
    
    Klik op **Selecteren**.

    c. Klik onder **machtigingen toewijzen** op **machtigingen selecteren**. Stel **machtigingen** in voor **lezen, schrijven en** **uitvoeren**. Stel **toevoegen in** op **deze map en alle onderliggende items**. Stel ten slotte de vermelding **toevoegen als** in op **een toegangs machtiging en een standaard machtiging**.

    ![Scherm afbeelding van de sectie Machtigingen toewijzen met de optie machtigingen selecteren. De sectie machtigingen selecteren bevindt zich naast de opties lezen, schrijven en uitvoeren, de optie toevoegen aan en de optie toevoegen als.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Machtigingen toewijzen voor de map Data Lake Storage Gen1")
    
    Klik op **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Event Hubs configureren om gegevens vast te leggen voor Data Lake Storage Gen1

In deze sectie maakt u een event hub binnen een Event Hubs naam ruimte. U kunt ook de Event hub configureren om gegevens vast te leggen voor een Azure Data Lake Storage Gen1-account. In deze sectie wordt ervan uitgegaan dat u al een Event Hubs naam ruimte hebt gemaakt.

1. Klik in het deel venster **overzicht** van de naam ruimte Event hubs op **+ Event hub**.

    ![Scherm opname van het overzichts venster met de optie Event hub.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Event Hub maken")

1. Geef de volgende waarden op om Event Hubs te configureren voor het vastleggen van gegevens in Data Lake Storage Gen1.

    ![Scherm afbeelding van het dialoog venster Event hub maken met het tekstvak naam, de vastleg optie, de optie voor het vastleggen van de provider, de optie Data Lake Store selecteren en de optie Data Lake pad wordt aangeroepen.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Event Hub maken")

    a. Geef een naam op voor de Event hub.
    
    b. Voor deze zelf studie stelt u het **aantal partities** en het **bewaren van berichten** in op de standaard waarden.
    
    c. Stel **vastleggen** in **op aan**. Stel het **tijd venster** (hoe vaak moet worden vastgelegd) en het **formaat venster** (gegevens grootte voor vastleggen) in. 
    
    d. Selecteer **Azure data Lake Store** voor de **Capture-provider** en selecteer vervolgens het data Lake Storage gen1 account dat u eerder hebt gemaakt. Voer bij **Data Lake pad** de naam in van de map die u hebt gemaakt in het data Lake Storage gen1-account. U hoeft alleen het relatieve pad naar de map op te geven.

    e. Wijzig de voor **beelden van bestands namen** voor het vastleggen in de standaard waarde. Met deze optie bepaalt u de mapstructuur die wordt gemaakt in de map Capture.

    f. Klik op **Create**.

## <a name="test-the-setup"></a>De installatie testen

U kunt de oplossing nu testen door gegevens te verzenden naar de Azure Event hub. Volg de instructies bij het [verzenden van gebeurtenissen naar Azure Event hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Zodra u begint met het verzenden van de gegevens, ziet u de gegevens die worden weer gegeven in Data Lake Storage Gen1 met behulp van de door u opgegeven mapstructuur. U ziet bijvoorbeeld een mapstructuur, zoals weer gegeven in de volgende scherm afbeelding, in uw Data Lake Storage Gen1-account.

![Voor beeld van EventHub-gegevens in Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Voor beeld van EventHub-gegevens in Data Lake Storage Gen1")

> [!NOTE]
> Zelfs als u geen berichten in Event Hubs hebt, schrijft Event Hubs lege bestanden met alleen de koppen in het Data Lake Storage Gen1-account. De bestanden worden geschreven met hetzelfde tijds interval dat u hebt gegeven tijdens het maken van de Event Hubs.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Gegevens in Data Lake Storage Gen1 analyseren

Zodra de gegevens zich in Data Lake Storage Gen1 bevindt, kunt u analytische taken uitvoeren om de gegevens te verwerken en te beheersen. Zie [USQL Avro voor beeld](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) over hoe u dit doet met Azure data Lake Analytics.
  

## <a name="see-also"></a>Zie ook
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Gegevens kopiëren van Azure Storage-blobs naar Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
