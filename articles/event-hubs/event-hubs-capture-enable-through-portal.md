---
title: Streaming-gebeurtenissen Event Hubs vastleggen met behulp van Azure Portal
description: In dit artikel wordt beschreven hoe u het vastleggen van streaming-gebeurtenissen via Azure Event Hubs kunt inschakelen met behulp van Azure Portal.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 8c89a6e56c2d0bc7b7ff09d5fd63737009239eb9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "100653948"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Vastleggen van streaming-gebeurtenissen via Azure Event Hubs inschakelen

Met Azure [Event hubs Capture][capture-overview] kunt u automatisch de streaminggegevens in Event hubs leveren aan een [Azure Blob-opslag](https://azure.microsoft.com/services/storage/blobs/) of een [Azure data Lake Storage gen1-of gen 2](https://azure.microsoft.com/services/data-lake-store/) -account van uw keuze.

Wanneer u de gebeurtenishub maakt, kunt u Capture configureren met behulp van de [Azure-portal](https://portal.azure.com). U kunt de gegevens vastleggen in een Azure [Blob-opslag](https://azure.microsoft.com/services/storage/blobs/) container of op een [Azure data Lake Storage gen 1-of gen 2](https://azure.microsoft.com/services/data-lake-store/) -account.

Zie voor meer informatie het [overzicht van Event Hubs Capture][capture-overview].

> [!IMPORTANT]
> Het Azure Storage-of Azure Data Lake Storage-account van de doel opslag moet zich in hetzelfde abonnement als de Event Hub bevallen.

## <a name="capture-data-to-azure-storage"></a>Gegevens vastleggen in Azure Storage

Wanneer u een Event Hub maakt, kunt u Capture inschakelen door op het scherm **Event Hub maken** in de portal te klikken op de knop **Aan**. Vervolgens geeft u een opslagaccount en een container op door in de lijst **Capture-provider** te klikken op **Azure Storage**. Omdat Event Hubs Capture gebruikmaakt van service-naar-serviceverificatie met opslag, hoeft u geen verbindingsreeks voor opslag op te geven. De objectkiezer selecteert automatisch de resource-URI voor uw opslagaccount. Als u Azure Resource Manager gebruikt, moet u deze URI expliciet als een tekenreeks opgeven.

Het standaardtijdvenster is 5 minuten. De minimale waarde is 1, de maximale 15. Het venster **Grootte** heeft een bereik van 10-500 MB.

![Tijdvenster voor vastleggen][1]

> [!NOTE]
> U kunt het verzenden van lege bestanden in- of uitschakelen wanneer er geen gebeurtenissen optreden tijdens de periode voor vastleggen. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Gegevens vastleggen voor Azure Data Lake Storage gen 2 

1. Volg een artikel voor het maken van [een opslag account](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) om een Azure Storage account te maken. Stel **hiërarchische naam ruimte** in op **ingeschakeld** op het tabblad **geavanceerd** om het account van Azure data Lake Storage gen 2 te maken.
2. Voer de volgende stappen uit bij het maken van een Event Hub: 

    1. Selecteer **aan** voor **vastleggen**. 
    2. Selecteer **Azure Storage** als de Capture-provider. De **Azure data Lake Store** optie die u voor de **Capture-provider** ziet, is voor de gen 1 van Azure data Lake Storage. Als u een generatie 2 van Azure Data Lake Storage wilt gebruiken, selecteert u **Azure Storage**.
    2. Selecteer de knop **container selecteren** . 

        ![Vastleggen naar Data Lake Storage gen 2 inschakelen](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Selecteer het **Azure data Lake Storage gen 2** -account in de lijst. 

    ![Data Lake Storage gen 2 selecteren](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Selecteer de **container** (bestands systeem in data Lake Storage gen 2).

    ![Bestands systeem in de opslag selecteren](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. Selecteer op de pagina **Event hub maken** de optie **maken**. 

    ![Knop maken selecteren](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > De container die u in een Azure Data Lake Storage gen 2 maakt met behulp van deze gebruikers interface (UI) wordt weer gegeven onder **bestands systemen** in **Storage Explorer**. Op dezelfde manier wordt het bestands systeem dat u in een Data Lake Storage gen 2-account maakt, weer gegeven als een container in deze gebruikers interface. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Gegevens vastleggen voor Azure Data Lake Storage gen 1 

Als u gegevens wilt vastleggen voor Azure Data Lake Storage gen 1, maakt u een Data Lake Storage gen 1-account en een Event Hub:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Een Azure Data Lake Storage gen 1-account en-mappen maken

1. Maak een Data Lake Storage-account en volg de instructies in aan [de slag met Azure data Lake Storage gen 1 met behulp van de Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md).
2. Volg de instructies in de sectie [machtigingen toewijzen aan Event hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) om een map te maken binnen het data Lake Storage gen 1-account waarin u de gegevens van Event hubs wilt vastleggen en wijs machtigingen toe aan Event hubs, zodat deze gegevens kunnen schrijven naar uw data Lake Storage gen 1-account.  


### <a name="create-an-event-hub"></a>Een Event Hub maken

1. De Event Hub moeten zich in hetzelfde Azure-abonnement benemen als het Azure Data Lake Storage gen 1-account dat u hebt gemaakt. Maak de Event Hub door te klikken op de knop **Aan**, onder **Vastleggen** op de pagina **Event Hub maken** van de portal. 
2. Selecteer **Azure Data Lake Store** in de lijst **Capture-provider** op de pagina **Event Hub maken** van de portal.
3. Geef in **Archief selecteren** naast de vervolg keuzelijst **Data Lake Store** het data Lake Storage gen 1-account op dat u eerder hebt gemaakt en voer in het veld **Data Lake pad** het pad in naar de gegevensmap die u hebt gemaakt.

    ![Data Lake Storage-account selecteren][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Capture toevoegen of configureren op een bestaande Event Hub

U kunt Capture configureren op bestaande Event Hubs in Event Hubs-naamruimten. Om Capture in te schakelen op een bestaande Event Hub of de Capture-instellingen te wijzigen, klikt u op de naamruimte om het overzichtsscherm te laden en vervolgens klikt u op de Event Hub waarvan u de Capture-instelling wilt inschakelen of wijzigen. Ten slotte klikt u op de optie **Capture** links in de geopende pagina en wijzigt u de instellingen, zoals weergegeven in de volgende afbeelding:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Azure Blob Storage configureren][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![Azure Data Lake Storage gen 2 configureren](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 

![Azure Data Lake Storage configureren][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over Event Hubs Capture vindt u in het [overzicht van Event Hubs Capture][capture-overview].
- U kunt ook Event Hubs Capture configureren met behulp van Azure Resource Manager-sjablonen. Zie voor meer informatie [Capture inschakelen met behulp van een Azure Resource Manager-sjabloon](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Informatie over het maken van een Azure Event Grid-abonnement met een Event Hubs-naamruimte als bron](store-captured-data-data-warehouse.md)
- [Aan de slag met Azure Data Lake Store met behulp van de Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
