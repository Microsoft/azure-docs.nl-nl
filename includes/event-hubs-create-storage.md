---
title: bestand opnemen
description: bestand opnemen
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bf107da82fb3f772a341e70ce472f08ea674a450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75692525"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Een opslagaccount voor Event Processor Host maken
De Event Processor Host is een intelligente agent die het ontvangen van gebeurtenissen van Event Hubs vereenvoudigt door permanente controlepunten en parallelle ontvangsten te beheren. Voor controlepunten heeft de Event Processor Host een opslagaccount nodig. In het volgende voorbeeld ziet u hoe u een opslagaccount maakt en hoe u de sleutels krijgt voor toegang:

1. Selecteer **Een resource maken** in het menu van de Azure-portal.

    ![Een menuopdracht voor een resource maken, Microsoft Azure Portal](./media/event-hubs-create-storage/create-resource.png)

2. Selecteer **Opslag** > **Opslagaccount**.
   
    ![Selecteer Opslagaccount, Microsoft Azure Portal](./media/event-hubs-create-storage/select-storage-account.png)

3. Voer op de pagina **Opslagaccount maken** de volgende stappen uit: 

   1. Voer de **naam van het opslagaccount** in.
   2. Kies een Azure-**abonnement** dat de Event Hub bevat.
   3. Kies of maak de **resourcegroep** waar de Event Hub deel van uitmaakt.
   4. Selecteer een **Locatie** waar u de resource wilt maken. 
   5. Selecteer **Controleren + maken**.
   
        ![Controleren + maken, Opslagaccount maken, Microsoft Azure Portal](./media/event-hubs-create-storage/review-create.png)

4. Op de pagina **Controleren + maken** controleert u de waarden en selecteert u **Maken**. 

    ![Opslagaccountinstellingen controleren en maken, Microsoft Azure Portal](./media/event-hubs-create-storage/create-storage-account.png)
5. Nadat u bij uw meldingen het bericht ziet dat de **implementaties zijn geslaagd**, selecteert u **Naar de resource gaan** om de pagina Opslagaccount te openen. Als alternatief kunt u ook **Implementatiedetails** uitvouwen en vervolgens uw nieuwe resource selecteren in de lijst met resources.  

    ![Naar de resource gaan, implementatie van opslagaccount, Microsoft Azure Portal](./media/event-hubs-create-storage/go-to-resource.png) 
6. Selecteer **Containers**.

    ![De Blobs-containerservice, opslagaccounts selecteren, Microsoft Azure Portal](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Selecteer bovenaan **+ Container**, voer een **naam** in voor de container en selecteer **OK**. 

    ![Een nieuwe blobcontainer maken, opslagaccounts, Microsoft Azure Portal](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Kies **Toegangssleutels** in het menu op de pagina **Opslagaccount** en kopieer de waarde van **key1**.

    Sla de volgende waarden op in Kladblok of op een andere tijdelijke locatie.
    - Naam van het opslagaccount
    - De toegangssleutel voor het opslagaccount
    - Naam van de container
