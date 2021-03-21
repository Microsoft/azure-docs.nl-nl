---
title: Een Azure CDN-eind punt maken | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een nieuw Azure Content Delivery Network (CDN)-eind punt maakt, met inbegrip van geavanceerde instellingen.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/12/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 7bbd54ed68863a4704319a25af96b6463ad2377b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539701"
---
# <a name="create-an-azure-cdn-endpoint"></a>Een Azure CDN-eind punt maken
In dit artikel worden alle instellingen beschreven voor het maken van een [Azure Content Delivery Network (CDN)-](cdn-overview.md) eind punt in een bestaand CDN-profiel. Nadat u een profiel en een eind punt hebt gemaakt, kunt u beginnen met het leveren van inhoud aan uw klanten. Voor een Snelstartgids voor het maken van een profiel en een eind punt, Zie [Quick Start: een Azure CDN profiel en een eind punt maken](cdn-create-new-endpoint.md).

## <a name="prerequisites"></a>Vereisten
Voordat u een CDN-eind punt kunt maken, moet u ten minste één CDN-profiel hebben gemaakt dat een of meer CDN-eind punten kan bevatten. U kunt meerdere profielen gebruiken om de CDN-eindpunten te ordenen op basis van het internetdomein, de webtoepassing of andere criteria. Omdat de CDN-prijzen worden toegepast op het niveau van het CDN-profiel, moet u meerdere CDN-profielen maken als u een combi natie van Azure CDN prijs categorieën wilt gebruiken. Zie [een nieuw CDN-profiel maken](cdn-create-new-endpoint.md#create-a-new-cdn-profile)voor het maken van een CDN-profiel.

## <a name="log-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal
Meld u met uw Azure-account aan bij het [Azure Portal](https://portal.azure.com).

## <a name="create-a-new-cdn-endpoint"></a>Nieuwe CDN-eindpunten maken

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw CDN-profiel. Mogelijk hebt u het profiel in de vorige stap vastgemaakt aan het dashboard. Zo niet, dan kunt u het vinden door **Alle services** en vervolgens **CDN-profielen** te selecteren. Selecteer in het deelvenster **CDN-profielen** het profiel waaraan u uw eindpunt wilt toevoegen. 
   
    Het deelvenster CDN-profiel wordt weergegeven.

2. Selecteer **eind punt**.
   
    ![CDN-eind punt selecteren](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    De pagina **Een eindpunt toevoegen** wordt weergegeven.
   
    ![Eindpunt pagina toevoegen](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. Voer voor **Naam** een unieke naam voor het nieuwe CDN-eindpunt in. Deze naam wordt gebruikt om toegang te krijgen tot uw resources in de cache in het domein _\<endpointname>_ . azureedge.net.

4. Kies voor **type oorsprong** een van de volgende oorsprongs typen: 
   - **Opslag** voor Azure Storage
   - **Cloud service** voor Azure Cloud Services
   - Web Apps van **Web-app** voor Azure
   - **Aangepaste oorsprong** voor alle andere openbaar toegankelijke webservers (gehost in azure of ergens anders)

5. Selecteer of typ uw bron-Server domein voor de **oorsprongs hostnaam**. De vervolg keuzelijst bevat alle beschik bare originele servers van het type dat u in stap 4 hebt opgegeven. Als u **aangepaste oorsprong** als uw oorsprongs type hebt geselecteerd, voert u het domein van de aangepaste oorspronkelijke server in.
    
6. Voer bij **bronpad** het pad in naar de resources die u wilt opslaan in de cache. Als u caching wilt toestaan van alle resources in het domein dat u in stap 5 hebt opgegeven, laat u deze instelling leeg.
    
7. Geef bij **Host-header van oorsprong** de host-header op die voor elke aanvraag door Azure CDN moet worden verzonden of laat de standaardinstelling staan.
   
   > [!NOTE]
   > Bepaalde oorsprongtypen, zoals Azure Storage en Web Apps, vereisen dat de hostheader overeenkomt met het domein van de oorsprong. Tenzij u een oorsprong hebt waarvoor een andere host-header is vereist dan die van het domein, laat u de standaardwaarde ongewijzigd.
   > 
    
8. Geef voor **protocol** en **poort van oorsprong** de protocollen en poorten op die moeten worden gebruikt voor toegang tot uw resources op de oorspronkelijke server. Er moet minimaal één protocol (HTTP of HTTPS) worden geselecteerd. Gebruik het door CDN verschafte domein ( _\<endpointname>_ . azureedge.net) voor toegang tot HTTPS-inhoud. 
   
   > [!NOTE]
   > De **poort waarde oorsprong** bepaalt alleen de poort die door het eind punt wordt gebruikt om informatie op te halen van de oorspronkelijke server. Het eindpunt zelf is alleen beschikbaar voor eindclients op de standaard-HTTP- en -HTTPS-poorten (80 en 443), ongeacht de waarde voor **Poort van oorsprong**.  
   > 
   > Eindpunten in **Azure CDN van Akamai**-profielen staan niet het volledige TCP-poortbereik voor oorsprongpoorten toe. Zie [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100)) (Door Azure CDN van Akamai toegestane poorten van oorsprong) voor een lijst met poorten van oorsprong die niet zijn toegestaan.  
   > 
   > HTTPS-ondersteuning voor Azure CDN aangepaste domeinen wordt niet ondersteund op **Azure CDN van Akamai** -producten. Zie [HTTPS op een aangepast Azure CDN-domein configureren](cdn-custom-ssl.md) voor meer informatie.
    
9. Selecteer voor **geoptimaliseerd voor** een optimalisatie type dat het beste overeenkomt met het scenario en type inhoud dat door het eind punt moet worden geleverd. Zie voor meer informatie [Optimize Azure CDN voor het type content delivery](cdn-optimization-overview.md).

    De volgende optimalisatie type-instellingen worden ondersteund, afhankelijk van het profiel type:
    - **Azure CDN standaard van micro soft** -profielen:
       - [**Algemene Internet levering**](cdn-optimization-overview.md#general-web-delivery)

    - **Azure CDN standaard van Verizon** en **Azure CDN Premium van Verizon** -profielen:
       - [**Algemene Internet levering**](cdn-optimization-overview.md#general-web-delivery)
       - [**Dynamische siteversnelling**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - **Azure CDN standaard van Akamai** profielen:
       - [**Algemene Internet levering**](cdn-optimization-overview.md#general-web-delivery)
       - [**Algemene mediastreaming**](cdn-optimization-overview.md#general-media-streaming)
       - [**Video on demand mediastreaming**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**Grote bestanden downloaden**](cdn-optimization-overview.md#large-file-download)
       - [**Dynamische siteversnelling**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. Selecteer **Toevoegen** om het nieuwe eindpunt te maken.
   
    Zodra het eindpunt is gemaakt, wordt deze weergegeven in de lijst met eindpunten voor het profiel.
    
    ![CDN-eindpunt](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    Het eindpunt is niet onmiddellijk beschikbaar voor gebruik, aangezien het enige tijd vergt om de registratie door te geven: 
    - Voor profielen van **Azure CDN Standard van Microsoft** is het doorgeven gewoonlijk binnen 10 minuten voltooid. 
    - Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
    - Voor **Azure CDN standaard van Verizon** en **Azure CDN Premium van Verizon** -profielen, wordt de doorgifte doorgaans binnen 30 minuten voltooid. 
   
    Als u de CDN-domein naam probeert te gebruiken voordat de eindpunt configuratie is door gegeven aan de POP-servers (Point-of-Presence), ontvangt u mogelijk een HTTP 404-reactie status. Als het enige uur is nadat u uw eind punt hebt gemaakt en u nog steeds een 404-reactie status ontvangt, raadpleegt u [troubleshooting Azure CDN-eind punten die een 404-status code retour neren](cdn-troubleshoot-endpoint.md).

## <a name="clean-up-resources"></a>Resources opschonen
Als u een eind punt wilt verwijderen wanneer het niet meer nodig is, selecteert u dit en selecteert u vervolgens **verwijderen**. 

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over aangepaste domeinen gaat u verder met de zelf studie voor het toevoegen van een aangepast domein aan uw CDN-eind punt.

> [!div class="nextstepaction"]
> [Een aangepast domein toevoegen](cdn-map-content-to-custom-domain.md)


