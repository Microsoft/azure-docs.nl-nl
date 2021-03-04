---
title: Een toegewezen Event Hubs-cluster maken met behulp van de Azure-portal
description: In deze quickstart leert u hoe u met de Azure-portal een Azure Event Hubs-cluster maakt.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 6ff4ee1f098407ba8b3cd2727410bdfc842db89a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040087"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Quickstart: Een toegewezen Event Hubs-cluster maken met behulp van de Azure-portal 
Event Hubs-clusters bieden implementaties met één tenant voor klanten met de meest veeleisende streaming-behoeften. Deze aanbieding heeft een gegarandeerde SLA van 99,99% en is alleen beschikbaar voor de prijscategorie Dedicated. Een [Event Hubs-cluster](event-hubs-dedicated-overview.md) kan miljoenen gebeurtenissen per seconde opnemen met gegarandeerde capaciteit en latentie van minder dan een seconde. Naamruimten en Event Hubs die in een cluster worden gemaakt, omvatten alle functies van de aanbieding voor Standard en meer, en hebben geen opnamelimieten. De Dedicated-aanbieding omvat ook de populaire functie [Event Hubs Capture](event-hubs-capture-overview.md), die gratis beschikbaar is, voor automatische batchverwerking en vastlegging van gegevensstromen in [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) of [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md).

Dedicated-clusters worden ingericht en gefactureerd door **capaciteitseenheden**, een vooraf toegewezen hoeveelheid CPU en geheugenbronnen. U kunt voor elk cluster 1, 2, 4, 8, 12, 16 of 20 capaciteitseenheden kopen. In deze quickstart wordt een stapsgewijze beschrijving gegeven van het maken van een Event Hubs-cluster met 1 capaciteitseenheid via de Azure-portal.

> [!NOTE]
> Deze selfservice-ervaring is momenteel als preview-versie beschikbaar in de [Azure-portal](https://aka.ms/eventhubsclusterquickstart). Als u vragen over de Dedicated-aanbieding hebt, neemt u contact op met het [Event Hubs-team](mailto:askeventhubs@microsoft.com).


## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:

- Een Azure-account. Als u nog geen account hebt, [koopt u een account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) voordat u begint. Deze functie wordt niet ondersteund met een gratis Azure-account. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 update 3 (versie 15.3, 26730.01) of hoger.
- [.NET Standard SDK](https://dotnet.microsoft.com/download), versie 2.0 of hoger.
- [Er is een resourcegroep gemaakt](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Een toegewezen Event Hubs-cluster maken
Een Event Hubs-cluster biedt een unieke scoping container waarin u een of meer naamruimten kunt maken. In deze preview-fase van de selfservice-ervaring in de portal kunt u in bepaalde regio’s clusters met 1 capaciteitseenheid maken. Als u een cluster met meer dan 1 capaciteitseenheid nodig hebt, kunt u een Azure-ondersteuningsaanvraag indienen om uw cluster omhoog te schalen nadat het is gemaakt.

> [!IMPORTANT]
> U kunt het cluster niet ten minste vier uur verwijderen nadat u het hebt gemaakt. Daarom worden er kosten in rekening gebracht voor een minimum van 4 uur gebruik van het cluster. Zie [Event hubs-prijzen](https://azure.microsoft.com/pricing/details/event-hubs/)voor meer informatie over prijzen. 

Voer de volgende stappen uit om een cluster in uw resource groep te maken met behulp van de Azure Portal:

1. Volg [deze koppeling](https://aka.ms/eventhubsclusterquickstart) om een cluster te maken in de Azure-portal. Of selecteer **Alle services** in het linkernavigatievenster, typ vervolgens ‘Event Hubs-clusters’ in de zoekbalk en selecteer ‘Event Hubs-clusters’ in de lijst met resultaten.
2. Configureer op de pagina **cluster maken** de volgende instellingen:
    1. Voer een **naam voor het cluster** in. In het systeem wordt onmiddellijk gecontroleerd of de naam beschikbaar is.
    2. Selecteer het **abonnement** waarin u het cluster wilt maken.
    3. Selecteer de **resourcegroep** waarin u het cluster wilt maken.
    4. Selecteer een **locatie** voor het cluster. Als uw voorkeursregio grijs wordt weergegeven, heeft deze tijdelijk geen capaciteit en kunt u een [ondersteuningsaanvraag](#submit-a-support-request) indienen bij het Event Hubs-team.
    5. Selecteer de knop **Volgende: Tags** onderaan de pagina. U moet een paar minuten wachten voordat het systeem de resources volledig heeft ingericht.

        ![De pagina Event Hubs-cluster maken - Basisinformatie](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Configureer het volgende op de pagina **Tags**:
    1. Voer een **naam** en een **waarde** in voor de tag die u wilt toevoegen. Deze stap is **optioneel**.  
    2. Selecteer de knop **Controleren en maken**.

        ![De pagina Event Hubs-cluster maken - Tags](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. Op de pagina **Controleren en maken** controleert u de details en selecteert u **Maken**. 

    ![De pagina Event Hubs-cluster maken - Controleren en maken](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Een naamruimte en Event Hub in een cluster maken

1. Als u een naamruimte in een cluster wilt maken, selecteert u op de pagina **Event Hubs-cluster** voor uw cluster de optie **+Naamruimte** in het bovenste menu.

    ![De pagina Clusterbeheer - De knop Naamruimte toevoegen](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Voer op de pagina **een naam ruimte maken** de volgende stappen uit:
    1. Voer een **naam in voor de naamruimte**.  In het systeem wordt gecontroleerd of de naam beschikbaar is.
    2. De naamruimte neemt de volgende eigenschappen over:
        1. Abonnements-id
        2. Resourcegroep
        3. Locatie
        4. Clusternaam
    3. Selecteer **Maken** om de naamruimte te maken. U kunt uw cluster nu beheren.  

        ![De pagina Naamruimte in het cluster maken](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Zodra uw naamruimte is gemaakt, kunt u [een Event Hub maken](event-hubs-create.md#create-an-event-hub) zoals u er een zou maken in een naamruimte. 


## <a name="submit-a-support-request"></a>Een ondersteuningsaanvraag indienen

Als u de grootte van het cluster na het maken wilt wijzigen of als uw voorkeurs regio niet beschikbaar is, kunt u een ondersteunings aanvraag indienen door de volgende stappen uit te voeren:

1. Selecteer in het linkermenu van de [Azure-portal](https://portal.azure.com) de optie **Help en ondersteuning**.
2. Selecteer **+ Nieuwe ondersteuningsaanvraag** in het menu Ondersteuning.
3. Voer deze stappen uit op de pagina Ondersteuning:
    1. Bij **Type probleem** selecteert u **Technisch** in de vervolgkeuzelijst.
    2. Bij **Abonnement** selecteert u uw abonnement.
    3. Bij **Service** selecteert u **Mijn services** en selecteert u vervolgens **Event Hubs**.
    4. Bij **Resource** selecteert u uw cluster als het al bestaat, en anders selecteert u **Algemene vraag/resource niet beschikbaar**.
    5. Bij **Probleemtype** selecteert u **Quota**.
    6. Bij **Subtype van het probleem** selecteert u een van de volgende waarden in de vervolgkeuzelijst:
        1. Selecteer **Aanvraag voor toegewezen SKU** om te vragen of de functie kan worden ondersteund in uw regio.
        2. Selecteer **Aanvraag voor het omhoog of omlaag schalen van toegewezen cluster** als u uw toegewezen cluster omhoog of omlaag wilt schalen. 
    7. Bij **Onderwerp** beschrijft u het probleem.

        ![De pagina Ondersteuningsticket](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Een toegewezen cluster verwijderen
 
1. Als u het cluster wilt verwijderen, selecteert u **Verwijderen** in het bovenste menu. 

    > [!IMPORTANT]
    > U kunt het cluster niet ten minste vier uur verwijderen nadat u het hebt gemaakt. Daarom worden er kosten in rekening gebracht voor een minimum van 4 uur gebruik van het cluster. Zie [Event hubs-prijzen](https://azure.microsoft.com/pricing/details/event-hubs/)voor meer informatie over prijzen.     
1. Er wordt een bericht weergegeven waarin u wordt gevraagd te bevestigen dat u het cluster wilt verwijderen.
1. Typ de **naam van het cluster** en selecteer **Verwijderen** om het cluster te verwijderen.

    ![De pagina Cluster verwijderen](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een Event Hubs-cluster gemaakt. Voor stapsgewijze instructies om gebeurtenissen van een Event Hub te verzenden en ontvangen, en om gebeurtenissen vast te leggen in Azure Storage of Azure Data Lake Store, bekijkt u de volgende zelfstudies:

- Gebeurtenissen verzenden en ontvangen 
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
- [De Azure-portal gebruiken om Event Hubs Capture in te schakelen](event-hubs-capture-enable-through-portal.md)
- [Azure Event Hubs voor Apache Kafka gebruiken](event-hubs-for-kafka-ecosystem-overview.md)
