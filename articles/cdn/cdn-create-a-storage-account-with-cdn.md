---
title: 'Quickstart: Een Azure Storage-account integreren met Azure CDN'
description: Leer hoe u Azure Content Delivery Network (CDN) kunt gebruiken en hoe u inhoud met een hoge bandbreedte via CDN kunt leveren door blobs van Azure Storage in de cache op te slaan.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: b80437fa1ac41bb240565923eb40e562c8cf8c5b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018610"
---
# <a name="quickstart-integrate-an-azure-storage-account-with-azure-cdn"></a>Snelstart: Een Azure Storage-account integreren met Azure CDN

In deze quickstart schakelt u de optie in om [Azure CDN (Content Delivery Network)](cdn-overview.md) inhoud van Azure Storage in de cache te laten opslaan. Azure CDN biedt ontwikkelaars een mondiale oplossing voor het leveren van inhoud met hoge bandbreedte. Het kan worden gebruikt om blobs en statische inhoud van rekeninstanties in de cache op fysieke knooppunten in de Verenigde Staten, Europa, Azië, Australië en Zuid-Amerika op te slaan.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-a-storage-account"></a>Create a storage account

Een opslagaccount biedt toegang tot Azure Storage-services. Het opslagaccount vertegenwoordigt het hoogste niveau van de naamruimte voor het verkrijgen van toegang tot elk van de onderdelen van de Azure Storage-service: Azure Blob Storage, Queue Storage en Table Storage. Zie [Inleiding tot Microsoft Azure Storage](../storage/common/storage-introduction.md) voor meer informatie.

Als u een opslagaccount wilt maken, moet u de servicebeheerder of een co-beheerder voor het gekoppelde abonnement zijn.

1. Selecteer linksboven in de Azure-portal **Een resource maken**. Het deelvenster **Nieuw** verschijnt.

1. Zoek **Opslagaccount** en selecteer in de vervolgkeuzelijst **Opslagaccount: blob, bestand, tabel, wachtrij**. Selecteer vervolgens **Maken**:
    
    ![Opslagresource selecteren](./media/cdn-create-a-storage-account-with-cdn/cdn-select-new-storage-account.png)

1. Voer in het **deelvenster Opslagaccount maken** de volgende gegevens in:

    | Instelling | Waarde | 
    | --- | --- |
    | Projectdetails > Resourcegroep | Selecteer **Nieuwe maken** en gebruik de naam *CDNQuickstart-rg*. U kunt ook een bestaande resourcegroep gebruiken, als dit uw voorkeur heeft. |
    | Exemplaardetails > Opslagaccountnaam | Voer een naam in voor het account met alleen 3-24 kleine letters en cijfers. De naam moet uniek zijn in Azure, en wordt de hostnaam binnen de URL die wordt gebruikt om te verwijzen naar blob-, wachtrij- of tabelresources in het abonnement. Gebruik een URI in de volgende indeling om te verwijzen naar een containerresource in Blob-opslag: http:// *&lt;storageaccountname&gt;* .blob.core.windows.net/ *&lt;container-name&gt;* .
    | Exemplaardetails > Locatie | Selecteer in de vervolgkeuzelijst een Azure-regio bij u in de buurt. |
    
    Laat voor alle andere details de standaardinstellingen staan. Selecteer vervolgens **Controleren en maken**.

1. Het kan enkele minuten duren voordat het opslagaccount is voltooid. Nadat het maken is voltooid, selecteert u **Ga naar resource** om de pagina van het opslagaccount te openen voor de volgende stap.

## <a name="enable-azure-cdn-for-the-storage-account"></a>Azure CDN inschakelen voor het opslagaccount

1. Selecteer op de pagina voor het opslagaccount in het linkermenu achtereenvolgens **Blob-service** > **Azure CDN**. De pagina **Azure CDN** wordt weergegeven.

    ![Een CDN-eindpunt maken](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png)
    
1. Voer in de sectie **Nieuw eindpunt** de volgende gegevens in:

    | Instelling  | Waarde |
    | -------- | ----- |
    | **CDN-profiel** | Selecteer **Nieuwe maken** en voer uw profielnaam in, bijvoorbeeld: *cdn-profile-123*. Een profiel is een verzameling eindpunten. |
    | **Prijscategorie** | Selecteer een van de **Standard-opties**, bijvoorbeeld **Standard Microsoft**. |
    | **Naam van CDN-eindpunt** | Voer de naam voor het eindpunt in, bijvoorbeeld *cdn-endpoint-123*. Deze naam moet wereldwijd uniek zijn in Azure, omdat deze toegang biedt tot uw resources in de cache op URL _&lt;endpoint-name&gt;_ .azureedge.net. |
    | **Hostnaam van oorsprong** | Een nieuw CDN-eindpunt gebruikt standaard de hostnaam van uw opslagaccount als de bronserver. |

1. Selecteer **Maken**. Zodra het eindpunt is gemaakt, wordt dit weergegeven in de lijst met eindpunten.

    ![Opslag nieuw CDN-eindpunt](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png)

> [!TIP]
> Als u voor uw CDN-eindpunt geavanceerde configuratie-instellingen wilt opgeven, zoals [ optimalisatie voor het downloaden van grote bestanden](cdn-optimization-overview.md#large-file-download), kunt u in plaats daarvan de [Azure CDN-extensie](cdn-create-new-endpoint.md) gebruiken om een CDN-profiel en -eindpunt te maken.


## <a name="enable-additional-cdn-features"></a>Aanvullende CDN-functies inschakelen

Vanuit de pagina **Azure CDN** van het opslagaccount, selecteert u het CDN-eindpunt in de lijst om de configuratiepagina van het CDN-eindpunt te openen.

Op deze pagina kunt u extra CDN-functies inschakelen voor uw levering, zoals [Compressie](cdn-improve-performance.md), [Queryreeksen opslaan in cache](cdn-query-string.md) en [Geo-filteren](cdn-restrict-access-by-country.md). 
    
## <a name="enable-sas"></a>SAS inschakelen

Als u beperkte toegang tot persoonlijke opslagcontainers wilt verlenen, kunt u de SAS-functie (Shared Access Signature) van uw Azure-opslagaccount gebruiken. Een SAS is een URI die beperkte toegangsrechten verleent aan uw Azure Storage-resources zonder uw accountsleutel prijs te geven. Zie [Azure CDN gebruiken met SAS](cdn-sas-storage-support.md) voor meer informatie.

## <a name="access-cdn-content"></a>Toegang tot CDN-inhoud

Gebruik de CDN-URL op de portal om toegang te krijgen tot inhoud van de cache op het CDN. Het adres van een blob in de cache heeft de volgende indeling:

http://<*endpoint-name*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> Nadat u ervoor hebt gezorgd dat Azure CDN toegang tot een opslagaccount heeft, komen alle openbaar beschikbare objecten in aanmerking om in de cache van CDN POP te worden geplaatst. Als u een object wijzigt dat zich momenteel in de cache op het CDN bevindt, is de nieuwe inhoud niet beschikbaar via Azure CDN totdat de inhoud van Azure CDN wordt vernieuwd na het verstrijken van de Time-to-Live-periode voor inhoud in de cache.

## <a name="remove-content-from-azure-cdn"></a>Inhoud verwijderen uit Azure CDN

Als u niet langer een object in de cache in Azure CDN nodig hebt, kunt u een van de volgende stappen uitvoeren:

- Maak de container persoonlijk in plaats van openbaar. Zie [Anonieme leestoegang tot containers en blobs beheren](../storage/blobs/anonymous-read-access-configure.md) voor meer informatie.
- Schakel het CDN-eindpunt uit of verwijder het met behulp van Azure Portal.
- Wijzig de gehoste service zodat deze niet meer reageert op aanvragen voor het object.

Een object dat al in de cache in Azure CDN is geplaatst, blijft in de cache totdat de Time-to-Live-periode voor het object is verstreken of totdat het eindpunt is [opgeschoond](cdn-purge-endpoint.md). Als de Time-to-Live-periode is verstreken, bepaalt Azure CDN of het CDN-eindpunt nog geldig is en het object nog steeds anoniem toegankelijk is. Als dat niet het geval is, wordt het object niet langer in de cache bewaard.

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u een CDN-profiel en een eindpunt in een resourcegroep gemaakt. Sla deze resources op als u naar [Vervolgstappen](#next-steps) wilt gaan en informatie wilt krijgen over het toevoegen van een aangepast domein aan uw eindpunt. Als u echter denkt deze resources in de toekomst niet meer nodig te hebben, kunt u ze verwijderen door de resourcegroep te verwijderen. Op deze manier voorkomt u bijkomende kosten:

1. Selecteer in het menu links in de Azure-portal **Resourcegroepen**, en selecteer vervolgens **CDNQuickstart-rg**.

2. Selecteer op de pagina **Resourcegroep** de optie **Resourcegroep verwijderen**. Voer *CDNQuickstart-rg* in het tekstvak in en selecteer **Verwijderen**.

    Deze actie zal de resourcegroep, het profiel en het eindpunt dat u in deze snelstart hebt gemaakt verwijderen.

3. Als u uw opslagaccount wilt verwijderen, selecteert u dit in het dashboard en selecteert u vervolgens **Verwijderen** in het menu bovenaan.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Azure CDN-profiel en -eindpunt maken](cdn-create-new-endpoint.md)

> [!div class="nextstepaction"]
> [Zelfstudie: CDN gebruiken om statische serverinhoud van een web-app weer te geven](cdn-add-to-web-app.md)