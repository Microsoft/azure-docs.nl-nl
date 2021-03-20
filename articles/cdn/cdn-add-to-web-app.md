---
title: 'Zelfstudie: Azure CDN toevoegen aan een Azure App Service-web-app | Microsoft Docs'
description: In deze zelfstudie wordt Azure Content Delivery Network (CDN) toegevoegd aan een Azure App Service-web-app, zodat uw statische bestanden altijd in de cache worden opgeslagen en geleverd vanaf een server dicht bij uw klanten, overal ter wereld.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 3f1dacb57931913edfb181f023bdf98717777d50
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97562239"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>Zelfstudie: Azure CDN toevoegen aan een Azure App Service-web-app

In deze zelfstudie leest u hoe u [Azure Content Delivery Network (CDN)](cdn-overview.md) toevoegt aan een [web-app in Azure App Service](../app-service/overview.md). Web Apps is een service voor het hosten van webtoepassingen, REST-API's en mobiele back-ends. 

Dit is de startpagina van de statische HTML-voorbeeldsite die u gaat gebruiken:

![Startpagina van voorbeeld-app](media/cdn-add-to-web-app/sample-app-home-page.png)

Wat u leert:

> [!div class="checklist"]
> * Een CDN-eindpunt maken.
> * Assets in cache vernieuwen.
> * Queryreeksen gebruiken voor het beheren van versies in cache.


## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

- [Git installeren](https://git-scm.com/)
- [Azure CLI installeren](/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>De web-app maken

Volg de [snelstart voor statische HTML](../app-service/quickstart-html.md) om de web-app die u gaat gebruiken te maken tot en met de stap **Browse to the app** (Bladeren naar de app).

## <a name="log-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open een browser en navigeer naar de [Azure Portal](https://portal.azure.com).

### <a name="dynamic-site-acceleration-optimization"></a>Optimalisatie van dynamische siteversnelling
Als u uw CDN-eindpunt wilt optimaliseren voor dynamische siteversnelling (DSA), gebruikt u de [CDN-portal](cdn-create-new-endpoint.md) om het profiel en het eindpunt te maken. Met [DSA-optimalisatie](cdn-dynamic-site-acceleration.md) worden de prestaties van webpagina's met dynamische inhoud aantoonbaar verbeterd. Zie [Configuratie van CDN-eindpunt voor sneller aanbieden van dynamische bestanden](cdn-dynamic-site-acceleration.md#cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files) voor instructies voor het optimaliseren van een CDN-eindpunt voor DSA vanuit de CDN-portal. Als u het nieuwe eindpunt niet wilt optimaliseren, kunt u de web-app-portal gebruiken om dit te maken aan de hand van de stappen in de volgende sectie. Voor profielen van **Azure CDN van Verizon** geldt dat u de optimalisatie van een CDN-eindpunt niet kunt wijzigen nadat deze is gemaakt.

## <a name="create-a-cdn-profile-and-endpoint"></a>Een CDN-profiel en -eindpunt maken

Selecteer in het navigatiedeelvenster links **App Services** en selecteer vervolgens de app die u hebt gemaakt in de [Quickstart voor statische HTML](../app-service/quickstart-html.md).

![Een App Service-app in de portal selecteren](media/cdn-add-to-web-app/portal-select-app-services.png)

Selecteer op de pagina **App Service** in het gedeelte **Instellingen** de optie **Netwerken > Azure CDN configureren voor uw app**.

![CDN selecteren in de portal](media/cdn-add-to-web-app/portal-select-cdn.png)

Geef op de pagina **Azure Content Delivery Network** de instellingen voor **Nieuw eindpunt** op zoals aangegeven in de tabel.

![Een profiel en eindpunt maken in de portal](media/cdn-add-to-web-app/portal-new-endpoint.png)

| Instelling | Voorgestelde waarde | Beschrijving |
| ------- | --------------- | ----------- |
| **CDN-profiel** | myCDNProfile | Een CDN-profiel is een verzameling van CDN-eindpunten van dezelfde prijscategorie. |
| **Prijscategorie** | Standard Akamai | De [prijscategorie](cdn-features.md) geeft de provider en de beschikbare functies aan. In deze zelfstudie wordt gebruikgemaakt van *Standard Akamai*. |
| **Naam van CDN-eindpunt** | Een unieke naam in het domein azureedge.net | U hebt toegang tot uw resources in de cache via het domein *&lt;naamvaneindpunt&gt;* .azureedge.net.

Selecteer **Maken** om een CDN-profiel te maken.

Azure maakt het profiel en het eindpunt. Het nieuwe eindpunt wordt weergegeven in de lijst **Eindpunten**, en wanneer het eindpunt is ingericht, heeft het de status **Actief**.

![Nieuw eindpunt in lijst](media/cdn-add-to-web-app/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Het CDN-eindpunt testen

 Het eindpunt is niet onmiddellijk beschikbaar voor gebruik, aangezien het enige tijd vergt om de registratie door te geven: 
   - Voor profielen van **Azure CDN Standard van Microsoft** is het doorgeven gewoonlijk binnen 10 minuten voltooid. 
   - Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
   - Profielen van **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** worden doorgaans binnen 90 minuten doorgegeven, maar in sommige gevallen kan dit langer duren. 

De voorbeeld-app heeft een *index.html*-bestand en de mappen *css*, *img* en *js* met andere statische assets. De inhoudspaden voor al deze bestanden zijn op het CDN-eindpunt hetzelfde. De volgende URL's bieden bijvoorbeeld toegang tot het bestand *bootstrap.css* in de map *css*:

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

Navigeer in een browser naar de volgende URL:

```
http://<endpointname>.azureedge.net/index.html
```

![Startpagina van voorbeeldapp waarvoor de gegevens vanuit CDN worden geleverd](media/cdn-add-to-web-app/sample-app-home-page-cdn.png)

 U ziet dezelfde pagina die u eerder in een Azure-web-app hebt uitgevoerd. Azure CDN heeft de assets van de oorspronkelijke web-app opgehaald en verstuurt gegevens vanaf het CDN-eindpunt.

Vernieuw de pagina om ervoor te zorgen dat deze pagina is opgeslagen in de cache in het CDN. Er zijn soms twee aanvragen van hetzelfde asset vereist voordat het CDN de gevraagde inhoud in de cache opslaat.

Zie voor meer informatie over het maken van Azure CDN-profielen en -eindpunten [Aan de slag met Azure CDN](cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>Het CDN leegmaken

Het CDN vernieuwt periodiek de behorende resources uit de oorspronkelijke web-app op basis van de configuratie van de time-to-live (TTL). De standaard-TTL is zeven dagen.

Soms moet u wellicht het CDN vernieuwen voordat de TTL is verlopen. Bijvoorbeeld wanneer u bijgewerkte inhoud in de web-app implementeert. Wis de resources in het CDN handmatig om te vernieuwen. 

In dit gedeelte van de zelfstudie implementeert u een wijziging in de web-app en schoont u het CDN op zodat de bijbehorende cache wordt vernieuwd.

### <a name="deploy-a-change-to-the-web-app"></a>Een wijziging implementeren in de web-app

Open het *index.html*-bestand en voeg *- V2* toe aan de kop H1, zoals wordt weergegeven in het volgende voorbeeld: 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Voer uw wijziging door en implementeer deze naar de web-app.

```bash
git commit -am "version 2"
git push azure main
```

Als de implementatie is voltooid, ziet u de wijziging als u naar de URL van de web-app bladert.

```
http://<appname>.azurewebsites.net/index.html
```

![V2 in de titel van de web-app](media/cdn-add-to-web-app/v2-in-web-app-title.png)

Als u naar de URL van het CDN-eindpunt voor de startpagina bladert, wordt de wijziging niet weergegeven omdat de in de cache opgeslagen versie in het CDN nog niet is verlopen. 

```
http://<endpointname>.azureedge.net/index.html
```

![Geen V2 in de titel van het CDN](media/cdn-add-to-web-app/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Het CDN leegmaken in de portal

Schoon het CDN op zodat het de in de cache opgeslagen versie vernieuwt.

Selecteer in de linkernavigatie in de portal de optie **Resourcegroepen** en selecteer vervolgens de resourcegroep die u hebt gemaakt voor uw web-app (myResourceGroup).

![Resourcegroep selecteren](media/cdn-add-to-web-app/portal-select-group.png)

Selecteer uw CDN-eindpunt in de lijst met resources.

![Eindpunt selecteren](media/cdn-add-to-web-app/portal-select-endpoint.png)

Selecteer boven aan de pagina **Eindpunt** de optie **Leegmaken**.

![Leegmaken selecteren](media/cdn-add-to-web-app/portal-select-purge.png)

Voer de inhoudspaden in die u wilt leegmaken. Als u een afzonderlijk bestand wilt verwijderen, geeft u het volledige bestandspad door. Als u een map wilt leegmaken en vernieuwen, geeft u een padsegment door. Omdat u *index.html* hebt gewijzigd, moet u controleren of het bestand zich op een van de paden bevindt.

Klik onder aan de pagina op **Leegmaken**.

![Pagina leegmaken](media/cdn-add-to-web-app/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Controleren of het CDN is bijgewerkt

Wacht totdat de aanvraag voor leegmaken is verwerkt. Dit duurt doorgaans enkele minuten. Selecteer het belpictogram boven aan de pagina om de huidige status te bekijken. 

![Melding voor leegmaken](media/cdn-add-to-web-app/portal-purge-notification.png)

Wanneer u naar de URL van het CDN-eindpunt voor *index.html* bladert, ziet u de *V2* die u hebt toegevoegd aan de titel op de startpagina. Dit betekent dat de CDN-cache is vernieuwd.

```
http://<endpointname>.azureedge.net/index.html
```

![V2 in de titel van het CDN](media/cdn-add-to-web-app/v2-in-cdn-title.png)

Zie voor meer informatie [Een Azure CDN-eindpunt leegmaken](../cdn/cdn-purge-endpoint.md). 

## <a name="use-query-strings-to-version-content"></a>Queryreeksen gebruiken om inhoud een versie te geven

Azure CDN biedt de volgende opties voor cachegedrag:

* Queryreeksen negeren
* Queryreeksen in de cache opslaan overslaan
* Elke unieke URL in de cache opslaan 

De eerste optie is de standaardoptie. Hierbij bevat de cache slechts één versie van een asset, ongeacht de gebruikte queryreeks in de URL. 

In dit gedeelte van de zelfstudie wijzigt u het cachegedrag zodat elke unieke URL in de cache wordt opgeslagen.

### <a name="change-the-cache-behavior"></a>Het gedrag van de cache wijzigen

Selecteer in de Azure Portal op de pagina **CDN-eindpunt** de optie **Cache**.

Selecteer **Elke unieke URL in de cache opslaan** in de vervolgkeuzelijst **Cachegedrag van queryreeks**.

Selecteer **Opslaan**.

![Selecteer cachegedrag van queryreeks](media/cdn-add-to-web-app/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Controleren of de unieke URL's afzonderlijk in de cache worden opgeslagen

Navigeer in een browser naar de startpagina op het CDN-eindpunt, en voeg een queryreeks toe: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

Met Azure CDN wordt de huidige inhoud van de web-app geretourneerd, met *V2* in de kop. 

Vernieuw de pagina om ervoor te zorgen dat deze pagina is opgeslagen in de cache in het CDN. 

Open *index.html*, wijzig *V2* in *V3*, en implementeer vervolgens de wijziging. 

```bash
git commit -am "version 3"
git push azure main
```

Ga in een browser naar de URL van het CDN-eindpunt met een nieuwe queryreeks zoals `q=2`. Met Azure CDN wordt het huidige *index.html*-bestand opgehaald en *V3* weergegeven. Als u echter naar het CDN-eindpunt navigeert met de queryreeks `q=1`, ziet u *V2*.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![V3 in de titel in het CDN, queryreeks 2](media/cdn-add-to-web-app/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 in de titel in het CDN, queryreeks 1](media/cdn-add-to-web-app/v2-in-cdn-title-qs1.png)

Deze uitvoer laat zien dat elke queryreeks anders wordt behandeld:

* q = 1 werd eerder gebruikt, waardoor in cache opgeslagen inhoud wordt geretourneerd (V2).
* q = 2 is nieuw, waardoor de meest recente inhoud van de web-app wordt opgehaald en geretourneerd (V3).

Zie [Cachegedrag in Azure CDN bepalen met queryreeksen](../cdn/cdn-query-string.md) voor meer informatie.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

Wat u hebt geleerd:

> [!div class="checklist"]
> * Een CDN-eindpunt maken.
> * Assets in cache vernieuwen.
> * Queryreeksen gebruiken voor het beheren van versies in cache.

In de volgende artikelen vindt u informatie over het optimaliseren van CDN:

> [!div class="nextstepaction"]
> [Zelfstudie: Een aangepast domein toevoegen aan uw Azure CDN-eindpunt](cdn-map-content-to-custom-domain.md)