---
title: Realtime-statistieken in Azure CDN | Microsoft Docs
description: Real-time statistieken bieden realtime gegevens over de prestaties van Azure CDN bij het leveren van inhoud aan uw klanten.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 3af2e849aa6658e539b0b5bdbda4428cc28e5ce5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84887226"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Realtime-statistieken in Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Overzicht
In dit document worden real-time statistieken in Microsoft Azure CDN uitgelegd.  Deze functionaliteit biedt realtime gegevens, zoals band breedte, cache status en gelijktijdige verbindingen met uw CDN-profiel bij het leveren van inhoud aan uw klanten. Hiermee wordt de status van uw service op elk gewenst moment doorlopend gecontroleerd, met inbegrip van Go-Live-gebeurtenissen.

De volgende grafieken zijn beschikbaar:

* [Bandbreedte](#bandwidth)
* [Statuscodes](#status-codes)
* [Cache statussen](#cache-statuses)
* [Verbindingen](#connections)

## <a name="accessing-real-time-stats"></a>Real-time statistieken openen
1. Blader in [Azure Portal](https://portal.azure.com)naar uw CDN-profiel.
   
    ![Blade CDN-profiel](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. Klik op de Blade CDN-profiel op de knop **beheren** .
   
    ![De knop Blade van het CDN-profiel beheren](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    De CDN-beheer portal wordt geopend.
3. Beweeg de muis aanwijzer over het tabblad **Analytics** en Beweeg vervolgens de muis aanwijzer over de **Realtime statistieken-** flyout.  Klik op **http-large object**.
   
    ![CDN-beheer Portal](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    De real-time statistieken grafieken worden weer gegeven.

In elk van de grafieken worden realtime statistieken weer gegeven voor de geselecteerde tijds periode, beginnend bij het laden van de pagina.  De grafieken worden automatisch om de paar seconden bijgewerkt.  Met de knop **grafiek vernieuwen** , indien aanwezig, wordt de grafiek gewist, waarna alleen de geselecteerde gegevens worden weer gegeven.

## <a name="bandwidth"></a>Bandbreedte
![Bandbreedte grafiek](./media/cdn-real-time-stats/cdn-bandwidth.png)

In de grafiek **band breedte** wordt de hoeveelheid band breedte weer gegeven die voor het huidige platform is gebruikt gedurende de geselecteerde tijds periode. Het gearceerde gedeelte van de grafiek geeft het bandbreedte gebruik aan. De exacte hoeveelheid band breedte die momenteel wordt gebruikt, wordt direct onder de lijn grafiek weer gegeven.

## <a name="status-codes"></a>Statuscodes
![Grafiek status code](./media/cdn-real-time-stats/cdn-status-codes.png)

In de grafiek **status codes** wordt aangegeven hoe vaak bepaalde HTTP-antwoord codes worden uitgevoerd gedurende de geselecteerde tijds periode.

> [!TIP]
> Zie [Azure CDN HTTP-status codes](/previous-versions/azure/mt759238(v=azure.100))voor een beschrijving van de opties voor de HTTP-status code.
> 
> 

Een lijst met HTTP-status codes wordt direct boven de grafiek weer gegeven. In deze lijst wordt elke status code aangegeven die in de lijn grafiek kan worden opgenomen en het huidige aantal exemplaren per seconde voor die status code. Standaard wordt een regel weer gegeven voor elk van deze status codes in de grafiek. U kunt er echter voor kiezen om alleen de status codes te bewaken die een speciale betekenis hebben voor uw CDN-configuratie. U doet dit door de gewenste status codes te controleren en alle andere opties te wissen en vervolgens op **grafiek vernieuwen** te klikken. 

U kunt de logboek gegevens voor een bepaalde status code tijdelijk verbergen.  Klik in de legenda direct onder de grafiek op de status code die u wilt verbergen. De status code wordt direct verborgen in de grafiek. Als u nogmaals op die status code klikt, wordt deze optie opnieuw weer gegeven.

## <a name="cache-statuses"></a>Cache statussen
![Grafiek statussen cache](./media/cdn-real-time-stats/cdn-cache-status.png)

In de grafiek **status van cache** wordt aangegeven hoe vaak bepaalde typen cache statussen worden uitgevoerd gedurende de geselecteerde tijds periode. 

> [!TIP]
> Zie [Azure CDN cache-status codes](/previous-versions/azure/mt759237(v=azure.100))voor een beschrijving van elke cache status code optie.
> 
> 

Een lijst met cache status codes wordt direct boven de grafiek weer gegeven. In deze lijst wordt elke status code aangegeven die in de lijn grafiek kan worden opgenomen en het huidige aantal exemplaren per seconde voor die status code. Standaard wordt een regel weer gegeven voor elk van deze status codes in de grafiek. U kunt er echter voor kiezen om alleen de status codes te bewaken die een speciale betekenis hebben voor uw CDN-configuratie. U doet dit door de gewenste status codes te controleren en alle andere opties te wissen en vervolgens op **grafiek vernieuwen** te klikken. 

U kunt de logboek gegevens voor een bepaalde status code tijdelijk verbergen.  Klik in de legenda direct onder de grafiek op de status code die u wilt verbergen. De status code wordt direct verborgen in de grafiek. Als u nogmaals op die status code klikt, wordt deze optie opnieuw weer gegeven.

## <a name="connections"></a>Verbindingen
![Grafiek verbindingen](./media/cdn-real-time-stats/cdn-connections.png)

In deze grafiek wordt aangegeven hoeveel verbindingen tot stand zijn gebracht met uw edge-servers. Elke aanvraag voor een Asset die via onze CDN-resultaten wordt door gegeven, resulteert in een verbinding.

## <a name="next-steps"></a>Volgende stappen
* Ontvang een melding met [real-time waarschuwingen in azure CDN](cdn-real-time-alerts.md)
* Dieper met [geavanceerde HTTP-rapporten](cdn-advanced-http-reports.md)
* [Gebruiks patronen](cdn-analyze-usage-patterns.md) analyseren

