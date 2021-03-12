---
title: Een statische website hosten in Azure Storage
description: Azure Storage statische website-hosting, die een rendabele en schaal bare oplossing biedt voor het hosten van moderne webtoepassingen.
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 09/04/2020
ms.subservice: blobs
ms.custom: devx-track-js
ms.openlocfilehash: bbb996362df1f99a8702de310bc11f6828cc303a
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008447"
---
# <a name="static-website-hosting-in-azure-storage"></a>Een statische website hosten in Azure Storage

U kunt statische inhoud (HTML-, CSS-, java script-en afbeeldings bestanden) rechtstreeks vanuit een opslag container met de naam *$Web* verwerken. Door uw inhoud in Azure Storage te hosten, kunt u serverloze architecturen gebruiken die [Azure functions](../../azure-functions/functions-overview.md) en andere PaaS-Services (platform as a Service) bevatten. Azure Storage statische website-hosting is een uitstekende optie in gevallen waarin u geen webserver nodig hebt om inhoud te renderen.

[App service statische web apps](https://azure.microsoft.com/services/app-service/static/) is een geweldig alternatief voor het Azure Storage van statische website-hosting en is ook geschikt in gevallen waarin u geen webserver nodig hebt om inhoud te renderen. App Service statische Web Apps bieden u een volledig beheerde werk stroom voor continue integratie en doorlopende levering (CI/CD) van GitHub-bron tot wereld wijde implementatie.

Als u een webserver nodig hebt om inhoud weer te geven, kunt u [Azure app service](https://azure.microsoft.com/services/app-service/)gebruiken.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up-a-static-website"></a>Instellen van een statische website

Het hosten van statische websites is een functie die u moet inschakelen op het opslag account.

Als u statische website-hosting wilt inschakelen, selecteert u de naam van het standaard bestand en geeft u desgewenst een pad op naar een aangepaste 404-pagina. Als er geen blob storage-container met de naam **$Web** al in het account bestaat, wordt er een voor u gemaakt. Voeg de bestanden van uw site toe aan deze container.

Zie [een statische website hosten in azure Storage](storage-blob-static-website-how-to.md)voor stapsgewijze instructies.

![Metrische gegevens voor metrische gegevens van statische websites Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Bestanden in de **$Web** -container zijn hoofdletter gevoelig, worden aangeboden via anonieme toegangs aanvragen en zijn alleen beschikbaar via Lees bewerkingen.

## <a name="uploading-content"></a>Inhoud uploaden

U kunt elk van deze hulpprogram ma's gebruiken om inhoud te uploaden naar de container **$Web** :

> [!div class="checklist"]
> * [Azure-CLI](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Azure PowerShell-module](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure-opslagverkenner](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure-pijplijnen](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code-extensie](/azure/developer/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Inhoud weer geven

Gebruikers kunnen site-inhoud vanuit een browser weer geven met behulp van de open bare URL van de website. U kunt de URL vinden met behulp van de Azure Portal, Azure CLI of Power shell. Zie [de URL van de website zoeken](storage-blob-static-website-how-to.md#portal-find-url).

Als de server een 404-fout retourneert en u geen fout document hebt opgegeven toen u de website inschakelde, wordt een standaard-404-pagina naar de gebruiker geretourneerd.

> [!NOTE]
> [CORS-ondersteuning (cross-Origin Resource Sharing) voor Azure Storage](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) wordt niet ondersteund met een statische website.

### <a name="regional-codes"></a>Regionale codes

De URL van uw site bevat een regionale code. De URL bevat bijvoorbeeld `https://contosoblobaccount.z22.web.core.windows.net/` regionale code `z22` .

Hoewel die code in de URL moet blijven, is deze alleen voor intern gebruik en hoeft u deze code niet op een andere manier te gebruiken.

Het index document dat u opgeeft wanneer u de functie voor het hosten van statische websites inschakelt, wordt weer gegeven wanneer gebruikers de site openen en geen specifiek bestand opgeven (bijvoorbeeld: `https://contosoblobaccount.z22.web.core.windows.net` ).

### <a name="secondary-endpoints"></a>Secundaire eind punten

Als u [redundantie in een secundaire regio](../common/storage-redundancy.md#redundancy-in-a-secondary-region)instelt, hebt u ook toegang tot website-inhoud door gebruik te maken van een secundair eind punt. Omdat gegevens asynchroon naar secundaire regio's worden gerepliceerd, zijn de bestanden die beschikbaar zijn op het secundaire eind punt niet altijd gesynchroniseerd met de bestanden die beschikbaar zijn op het primaire eind punt.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Gevolgen van het instellen van het open bare toegangs niveau van de webcontainer

U kunt het open bare toegangs niveau van de **$Web** -container wijzigen, maar dit heeft geen invloed op het primaire statische website-eind punt omdat deze bestanden via anonieme toegangs aanvragen worden bediend. Dit betekent dat open bare (alleen-lezen) toegang tot alle bestanden.

De volgende scherm afbeelding toont de instelling voor het niveau van open bare toegang in de Azure Portal:

![Scherm afbeelding die laat zien hoe u het niveau van open bare toegang instelt in de portal](./media/anonymous-read-access-configure/configure-public-access-container.png)

Terwijl het eind punt van de primaire statische website niet wordt beïnvloed, heeft een wijziging van het open bare toegangs niveau invloed op het eind punt van de primaire BLOB-service.

Als u bijvoorbeeld het open bare toegangs niveau van de container **$Web** van **privé (geen anonieme toegang)** wijzigt in **BLOB (anonieme lees toegang voor blobs)**, wordt het niveau van de open bare toegang tot het eind punt van de primaire statische website `https://contosoblobaccount.z22.web.core.windows.net/index.html` niet gewijzigd.

De open bare toegang tot het eind punt van de primaire BLOB-service `https://contosoblobaccount.blob.core.windows.net/$web/index.html` wordt echter gewijzigd van persoonlijk in openbaar. Gebruikers kunnen dit bestand nu openen met behulp van een van deze twee eind punten.

Het uitschakelen van open bare toegang op een opslag account heeft geen invloed op statische websites die worden gehost in dat opslag account. Zie [anonieme open bare Lees toegang voor containers en blobs configureren](anonymous-read-access-configure.md)voor meer informatie.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Een aangepast domein toewijzen aan een statische website-URL

U kunt uw statische website beschikbaar maken via een aangepast domein.

Het is eenvoudiger om HTTP-toegang in te scha kelen voor uw aangepaste domein, omdat deze door Azure Storage systeem eigen ondersteuning biedt. Als u HTTPS wilt inschakelen, moet u Azure CDN gebruiken, omdat Azure Storage nog geen systeem eigen ondersteuning biedt voor HTTPS met aangepaste domeinen. Zie [een aangepast domein toewijzen aan een Azure Blob Storage-eind punt](storage-custom-domain-name.md) voor stapsgewijze instructies.

Als het opslag account is geconfigureerd voor het [vereisen van beveiligde overdracht](../common/storage-require-secure-transfer.md) via https, moeten gebruikers het HTTPS-eind punt gebruiken.

> [!TIP]
> U kunt uw domein hosten in Azure. Zie [uw domein hosten in azure DNS](../../dns/dns-delegate-domain-azure-dns.md)voor meer informatie.

## <a name="adding-http-headers"></a>HTTP-headers toevoegen

Het is niet mogelijk om headers als onderdeel van de functie statische website te configureren. U kunt Azure CDN echter gebruiken om koptekst waarden toe te voegen en te toevoegen (of te overschrijven). Zie de [Naslag informatie voor de standaard regels-engine voor Azure CDN](../../cdn/cdn-standard-rules-engine-reference.md).

Als u headers wilt gebruiken voor het beheren van de cache, raadpleegt u [beheer Azure CDN caching met cache regels](../../cdn/cdn-caching-rules.md).

## <a name="multi-region-website-hosting"></a>Hosting van websites met meerdere regio's

Als u van plan bent om een website in meerdere locaties te hosten, raden we u aan om een [Content Delivery Network](../../cdn/index.yml) te gebruiken voor regionale caching. Gebruik de [voor deur van Azure](../../frontdoor/index.yml) als u in elke regio verschillende inhoud wilt aanbieden. Het biedt ook mogelijkheden voor failover. [Azure Traffic Manager](../../traffic-manager/index.yml) wordt niet aanbevolen als u van plan bent een aangepast domein te gebruiken. Problemen kunnen zich voordoen als gevolg van de manier waarop Azure Storage aangepaste domein namen verifieert.


## <a name="pricing"></a>Prijzen

U kunt statische website-hosting gratis inschakelen. U wordt alleen gefactureerd voor de Blob-opslag die uw site gebruikt en de operationele kosten. Bekijk de [pagina met prijzen voor azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over de prijzen voor Azure Blob Storage.

## <a name="metrics"></a>Metrische gegevens

U kunt metrische gegevens inschakelen op de vaste website pagina's. Wanneer u metrische gegevens hebt ingeschakeld, worden verkeers statistieken voor bestanden in de **$Web** container gerapporteerd in het dash board metrische gegevens.

Zie [metrische gegevens inschakelen op de pagina's van een statische website](storage-blob-static-website-how-to.md#metrics)voor informatie over het inschakelen van metrische gegevens op uw statische website pagina's.

## <a name="next-steps"></a>Volgende stappen

* [Een statische website hosten in Azure Storage](storage-blob-static-website-how-to.md)
* [Een aangepast domein toewijzen aan een Azure Blob Storage-eindpunt](storage-custom-domain-name.md)
* [Azure Functions](../../azure-functions/functions-overview.md)
* [Azure App Service](../../app-service/overview.md)
* [Uw eerste serverloze web-app bouwen](/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Zelfstudie: Uw domein hosten in Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)