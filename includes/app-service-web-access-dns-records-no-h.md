---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 2f490a5b12484a91e963d068810b292d7761521a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95986535"
---
> [!NOTE]
> U kunt Azure DNS gebruiken om een aangepaste DNS-naam voor Azure App Service te configureren. Zie [Use Azure DNS to provide custom domain settings for an Azure service](../articles/dns/dns-custom-domain.md#app-service-web-apps) (Azure DNS gebruiken om aangepaste domeininstellingen te verstrekken voor een Azure-service) voor meer informatie.
>
>

1. Meld u aan bij de website van uw domeinprovider.

1. Ga naar de pagina voor het beheren van DNS-records. Elke domeinprovider heeft zijn eigen interface voor het beheren van DNS-records. Raadpleeg daarom de documentatie van de provider. Doorgaans heeft het sitegedeelte waar u moet zijn, een naam als **Domain Name**, **DNS** of **Name Server Management**.

   Vaak kunt u de pagina met DNS-records vinden door uw accountgegevens te bekijken en te zoeken naar een link als **My domains** (of iets vergelijkbaars). Ga naar de betreffende pagina en kijk of u daar een link ziet zoals **Zone file**, **DNS Records** of **Advanced configuration**.

   In de schermafbeelding hieronder wordt een voorbeeld van een pagina met DNS-records weergegeven:

   ![Schermopname met een voorbeeld van de pagina DNS-records.](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Selecteer in het voorbeeld **Add** om een record te maken. Sommige providers hebben afzonderlijke links voor verschillende typen records. Raadpleeg ook hiervoor de documentatie van de provider.

> [!NOTE]
> Bij bepaalde providers, zoals GoDaddy, worden wijzigingen in DNS-records pas van kracht wanneer u op een afzonderlijke link **Save Changes** klikt.
