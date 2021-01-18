---
title: 'Quickstart: Een Azure CDN-profiel en een eindpunt maken'
description: In deze quickstart ziet u hoe u Azure CDN inschakelt door een nieuw CDN-profiel en CDN-eindpunt te maken.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 7a3c4bc2a0445a2821e212986b495993695652a6
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165923"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Snelstart: Een Azure CDN-profiel en een eindpunt maken

In deze quickstart wordt beschreven hoe u Azure Content Delivery Network (CDN) inschakelt door een nieuw CDN-profiel te maken dat een verzameling is van een of meerdere CDN-eindpunten. Nadat u een profiel en een eindpunt hebt gemaakt, kunt u beginnen met het leveren van inhoud aan uw klanten.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een Azure Storage-account met de naam *cdnstorageacct123* dat u gebruikt als hostnaam van de bron. Om deze vereiste te voltooien, raadpleegt u [een Azure Storage-account integreren met Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com).

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Nieuwe CDN-eindpunten maken

Nadat u een CDN-profiel hebt gemaakt, kunt u het gebruiken om een eindpunt te maken.

1. Selecteer in het dashboard het CDN-profiel dat u hebt gemaakt in het Azure Portal. Als u het niet kunt vinden, kunt u de resourcegroep openen waarin u deze hebt gemaakt, of de zoekbalk boven in de portal gebruiken, de profielnaam invoeren en het profiel selecteren in de lijst met resultaten.
   
1. Selecteer op de pagina CDN-profiel de optie **+ Eindpunt**.
   
    ![CDN-profiel](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    Het deelvenster **Een eindpunt toevoegen** wordt weergegeven.

3. Voer de volgende instelwaarden in:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Naam** | Voer *cdn-endpoint-123* in als hostnaam van uw eindpunt. Deze naam moet uniek in Azure zijn. Als deze al in gebruik is, kunt u een andere naam invoeren. Deze naam wordt gebruikt voor toegang tot uw resources in de cache van de domein- _&lt;eindpuntnaam&gt;_ .azureedge.net.|
    | **Oorsprongtype** | Selecteer **Opslag**. | 
    | **Hostnaam van oorsprong** | Selecteer de hostnaam van het Azure Storage-account dat u gebruikt in de vervolgkeuzelijst, zoals *cdnstorageacct123.blob.core.windows.net*. |
    | **Pad voor de oorsprong** | Leeg laten. |
    | **Host-header van oorsprong** | Wijzig de standaardwaarde (dit is de hostnaam voor het opslagaccount). |  
    | **Protocol** | Laat de standaard geselecteerde opties **HTTP** en **HTTPS** staan. |
    | **Poort van oorsprong** | Laat de standaard gegenereerde poortwaardes staan. | 
    | **Geoptimaliseerd voor** | Laat de standaardselectie **Algemene weboverdracht** staan. |

    ![Deelvenster Eindpunt toevoegen](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

3. Selecteer **Toevoegen** om het nieuwe eindpunt te maken. Zodra het eindpunt is gemaakt, wordt deze weergegeven in de lijst met eindpunten voor het profiel.
    
   ![CDN-eindpunt](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   Hoe lang het duurt voordat het eindpunt wordt doorgegeven, is afhankelijk van de prijscategorie die is geselecteerd bij het maken van het profiel. **Standard Akamai** is gewoonlijk binnen een minuut voltooid, **Standard Microsoft** in tien minuten en **Standard Verizon** en **Premium Verizon** binnen dertig minuten.

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u een CDN-profiel en een eindpunt in een resourcegroep gemaakt. Sla deze resources op als u naar [Vervolgstappen](#next-steps) wilt gaan en informatie wilt krijgen over het toevoegen van een aangepast domein aan uw eindpunt. Als u echter denkt deze resources in de toekomst niet meer nodig te hebben, kunt u ze verwijderen door de resourcegroep te verwijderen. Op deze manier voorkomt u bijkomende kosten:

1. Selecteer in het menu links in Azure Portal **Resourcegroepen** en selecteer vervolgens **CDNQuickstart-rg**.

2. Selecteer op de **Resourcegroep**-pagina **Resourcegroep verwijderen**, voer *CDNQuickstart-rg* in het tekstvak in en selecteer **Verwijderen**. Deze actie zal de resourcegroep, het profiel en het eindpunt dat u in deze quickstart hebt gemaakt, verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: CDN gebruiken om statische inhoud van een web-app weer te geven](cdn-add-to-web-app.md)

> [!div class="nextstepaction"]
> [Zelfstudie: Een aangepast domein toevoegen aan uw Azure CDN-eindpunt](cdn-map-content-to-custom-domain.md)
