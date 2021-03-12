---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 0c7e011cf8445164e0931f71e390813c9134dd89
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200930"
---
1. Selecteer in [Azure Portal](https://portal.azure.com/) uw Azure Stack Edge-resource en ga naar het **Overzicht**. Als het goed is, is uw apparaat online. Ga naar **Cloudopslag-gateway > Opslagaccounts**.

2. Selecteer **+ Opslagaccount toevoegen** op de opdrachtbalk van het apparaat. 

   ![Een opslagaccount toevoegen](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. Geef in het deelvenster **Edge-opslagaccount toevoegen** de volgende instellingen op:

    1. Geef een unieke naam op voor het Edge-opslag account op het apparaat. Namen van opslagaccounts mogen alleen kleine letters en cijfers bevatten. Speciale tekens zijn niet toegestaan. De naam van het opslagaccount moet uniek zijn binnen het apparaat (niet over apparaten heen).

    2. Geef een optionele beschrijving op voor de informatie over de gegevens die het opslag account bevat.  
    
    3. Het Edge-opslag account is standaard toegewezen aan een Azure Storage-account in de Cloud en de gegevens uit het opslag account worden automatisch naar de Cloud gepusht. Geef het Azure-opslagaccount op waaraan uw Edge-opslagaccount is toegewezen.

    4. Maak een nieuwe container of selecteer een bestaande container in het Azure-opslag account. Alle gegevens van het apparaat die worden weggeschreven naar het Edge-opslagaccount, worden automatisch geüpload naar de geselecteerde opslagcontainer in het toegewezen Azure-opslagaccount.

    5. Nadat alle opties voor het opslagaccount zijn opgegeven, selecteert u **Toevoegen** om het Edge-opslagaccount te maken. U wordt gewaarschuwd wanneer het Edge-opslag account is gemaakt. Het nieuwe Edge-opslagaccount wordt vervolgens weergegeven in de lijst met opslagaccounts in de Azure Portal.

    <!--[Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->
    
4. Als u dit nieuwe opslagaccount selecteert en naar **Toegangssleutels** gaat, kunt u het eindpunt van de blob-service en de bijbehorende opslagaccountnaam vinden. Als deze gegevens samen met de toegangssleutels worden gekopieerd, kunt u verbinding maken met het Edge-opslagaccount.

    ![Een opslagaccount toevegen 2](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    U krijgt de toegangssleutels door [Verbinding te maken met de lokale API's van het apparaat met behulp van Azure Resource Manager](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md). 
