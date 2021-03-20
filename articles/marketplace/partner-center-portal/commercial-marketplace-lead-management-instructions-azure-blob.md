---
title: Lead beheer met Azure Blob Storage-micro soft Commercial Marketplace
description: Meer informatie over het gebruik van Azure Blob voor het configureren van leads voor Microsoft AppSource en Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/01/2020
ms.openlocfilehash: bd2f3d40b1aea1c0133f95e069ebfd527d30bd59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491100"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>Azure Blob-opslag gebruiken om leads voor commerciële Marketplace te beheren

>[!Caution]
>Ondersteuning voor commerciële Marketplace voor Azure Blob-opslag is afgeschaft en is niet langer een optie om leads van uw aanbieding te verwerken. Als u momenteel een commerciële Marketplace-aanbieding hebt met lead beheer dat is geconfigureerd voor Azure Blob, ontvangt u geen leads meer van klanten. Werk de configuratie van uw lead beheer bij naar een van de andere opties voor het beheer van leads. Meer informatie over de andere opties op de [landings pagina voor het beheer van leads](./commercial-marketplace-get-customer-leads.md).

 Als uw Customer Relationship Management-systeem (CRM) niet expliciet wordt ondersteund in het partner centrum voor het ontvangen van Microsoft AppSource en Azure Marketplace-leads, kunt u Azure Blob-opslag gebruiken. U kunt de gegevens vervolgens exporteren en importeren in uw CRM-systeem. De instructies in dit artikel geven u door het proces van het maken van een Azure Storage-account en een BLOB onder dat account. Daarnaast kunt u een nieuwe stroom maken met behulp van automatische stroom voor het verzenden van een e-mail melding wanneer uw aanbieding een lead ontvangt.

>[!NOTE]
>Voor de stroom voor het automatisch uitvoeren van connectors die in deze instructies worden gebruikt, is een betaald abonnement vereist om te automatiseren. Zorg ervoor dat u dit account voordat u de instructies in dit artikel volgt.

## <a name="configure-azure-blob-storage"></a>Azure Blob-opslag configureren

1. Als u geen Azure-account hebt, kunt u [een gratis proef account maken](https://azure.microsoft.com/pricing/free-trial/).

2. Nadat uw Azure-account actief is, meldt u zich aan bij de [Azure Portal](https://portal.azure.com).

3. Maak in de Azure Portal een opslag account met behulp van de volgende procedure.  
    1. Selecteer **+ een resource maken** in de menu balk links.  Het **nieuwe** deel venster (Blade) wordt aan de rechter kant weer gegeven.
    2. Selecteer **opslag** in het deel venster **Nieuw** .  Er wordt een **Aanbevolen** lijst aan de rechter kant weer gegeven.
    3. Selecteer het **opslag account** om te beginnen met het maken van het account.  Volg de instructies in het artikel [een opslag account maken](../../storage/common/storage-account-create.md?tabs=azure-portal).

    ![Stappen voor het maken van een Azure Storage-account](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    Zie deze [Quick](../../storage/blobs/storage-quickstart-blobs-portal.md)start voor meer informatie over opslag accounts.  Zie [prijzen voor opslag](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over prijzen voor opslag.

4. Wacht tot uw opslag account is ingericht, een proces dat doorgaans een paar minuten in beslag neemt.  Ga vervolgens naar uw opslag account vanaf de **Start** pagina van de Azure portal door **alle resources weer geven** te selecteren of door **alle resources** te selecteren in de linker navigatie balk van de Azure Portal.

    ![Toegang tot uw Azure Storage-account](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. Selecteer in het deel venster opslag account de optie **toegangs sleutels** en kopieer de *verbindings reeks* waarde voor de sleutel. Sla deze waarde op als dit de waarde voor de *verbindings reeks voor het opslag account* is die u moet opgeven in de portal voor publiceren om leads voor uw Marketplace-aanbieding te ontvangen.

     Een voor beeld van een connection string is:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Scherm opname van de pagina ' toegangs sleutels ' met het tekstvak ' verbindings reeks ' gemarkeerd.](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. Selecteer op de pagina opslag account de optie **blobs**.

   ![Scherm afbeelding van de pagina Azure Storage-account met blobs geselecteerd](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Selecteer de knop **+ container** op de pagina blobs.

8. Voer een **naam** in voor de nieuwe container. De containernaam mag alleen kleine letters bevatten, moet beginnen met een letter of cijfer en mag alleen letters, cijfers en het streepje (-) bevatten. Zie [Naamgeving van en verwijzen naar containers, blobs en metagegevens](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de namen van containers en blobs.

    Sla deze waarde op omdat dit de waarde voor de *container naam* is die u in de portal voor publiceren moet opgeven om leads voor uw Marketplace-aanbieding te ontvangen.

9. Stel het niveau van open bare toegang tot de container in als **privé (geen anonieme toegang)**.

10. Selecteer **OK** om de container te maken.

    ![Nieuwe container](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>Uw aanbieding configureren voor het verzenden van leads naar Azure Blob-opslag

Wanneer u klaar bent om de informatie over het beheer van leads voor uw aanbieding te configureren in de portal voor publiceren, volgt u de onderstaande stappen:

1. Navigeer naar de pagina voor het instellen van de **aanbieding** voor uw aanbieding.
2. Selecteer in het gedeelte **klant leads** de optie **verbinding maken**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-blob/customer-leads.png" alt-text="Leads van klanten":::

3. Selecteer in het pop-upvenster verbindings Details de optie **Azure Blob** voor de doel locatie van de lead.

    ![Details verbinden](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Geef de **container naam** en de **verbindings reeks voor het opslag account** op die u hebt gevolgd door deze instructies.

    * Container naam voor beeld: `marketplaceleadcontainer`
    * Voor beeld van verbindings reeks voor opslag account: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![ Details van verbinding](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Selecteer **Opslaan**.

    > [!NOTE]
    > U moet de configuratie van de rest van de aanbieding volt ooien en publiceren voordat u leads voor de aanbieding kunt ontvangen.