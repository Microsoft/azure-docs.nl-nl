---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
ms.openlocfilehash: 0b6a6cbf51ef2ff1f1ef53b53a2b84c7a4f9510d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185531"
---
1. Selecteer in [Azure Portal](https://portal.azure.com/) uw Azure Stack Edge-resource en ga naar het **Overzicht**. Als het goed is, is uw apparaat online.

2. Selecteer **+ Opslagaccount toevoegen** op de opdrachtbalk van het apparaat. 

   ![Een opslagaccount toevoegen](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. Geef in het deelvenster **Edge-opslagaccount toevoegen** de volgende instellingen op:

    a. Een unieke naam voor het Edge-opslagaccount op uw apparaat. Namen van opslagaccounts mogen alleen kleine letters en cijfers bevatten. Speciale tekens zijn niet toegestaan. De naam van het opslagaccount moet uniek zijn binnen het apparaat (niet over apparaten heen).

    b. Een optionele beschrijving voor de informatie over de gegevens die het opslagaccount bevat.  
    
    c. Het Edge-opslagaccount is standaard toegewezen aan een Azure Storage-account in de cloud en de gegevens uit het opslagaccount worden automatisch naar de cloud gepusht. Geef het Azure-opslagaccount op waaraan uw Edge-opslagaccount is toegewezen.  

    d. Maak vervolgens een nieuwe container of selecteer een bestaande container in het Azure-opslagaccount. Alle gegevens van het apparaat die worden weggeschreven naar het Edge-opslagaccount, worden automatisch geüpload naar de geselecteerde opslagcontainer in het toegewezen Azure-opslagaccount.

    <!--![Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->

    e. Nadat alle opties voor het opslagaccount zijn opgegeven, selecteert u **Toevoegen** om het Edge-opslagaccount te maken. U ontvangt een melding wanneer het Edge-opslagaccount is gemaakt. Het nieuwe Edge-opslagaccount wordt vervolgens weergegeven in de lijst met opslagaccounts in de Azure Portal. 

    
4. Als u dit nieuwe opslagaccount selecteert en naar **Toegangssleutels** gaat, kunt u het eindpunt van de blob-service en de bijbehorende opslagaccountnaam vinden. Als deze gegevens samen met de toegangssleutels worden gekopieerd, kunt u verbinding maken met het Edge-opslagaccount.

    ![Een opslagaccount toevegen 2](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    U krijgt de toegangssleutels door [Verbinding te maken met de lokale API's van het apparaat met behulp van Azure Resource Manager](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md). 
