---
title: Een Internet Analyzer-test maken met behulp van Portal | Microsoft Docs
description: In dit artikel leert u hoe u uw eerste Internet Analyzer-test kunt maken.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "73501769"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Een Internet Analyzer-test maken met behulp van Portal (Preview)

Er zijn twee manieren om een Internet Analyzer-resource te maken: met behulp van de Azure Portal of met behulp van [CLI](internet-analyzer-cli.md). Dit gedeelte helpt u bij het maken van een nieuwe Azure Internet Analyzer-resource met onze Portal-ervaring.

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Voordat u begint

De openbare preview is beschikbaar voor gebruik wereldwijd; gegevensopslag is tijdens de preview echter beperkt tot *VS - west 2*.

## <a name="basics"></a>Basisbeginselen

1. Krijg toegang tot Internet Analyzer-preview door de instructies in **Hoe kan ik deelnemen aan de preview?** van de [Veelgestelde vragen over Azure Internet Analyzer](internet-analyzer-faq.md) te volgen.
2. Op de startpagina in de [Azure Portal](https://preview.portal.azure.com) klikt u op **+ Een resource maken**. Internet Analyzer is momenteel alleen beschikbaar vanuit de preview-versie van de Azure Portal.
3. Zoek op de pagina **Nieuw** naar 'Internet Analyzer' in het veld *Zoeken in de marketplace*.
4. Klik op **Internet Analyzer (preview)** . Controleer of de uitgever *Microsoft* is en of de categorie *Netwerken* is.
5. Op dat pagina **Internet Analyzer (preview)** klikt u op **Maken** om de pagina **Een Internet Analyzer maken** te openen.
6. Geef de volgende configuratie-instellingen op voor uw Internet Analyzer-resource:

    * **Abonnement:** Het Azure-abonnement voor het hosten van de nieuwe Internet Analyzer-resource. **_Gebruik dezelfde abonnements-ID die wordt gebruikt om Preview-toegang aan te vragen._**
    * **Resourcegroep:** De Azure-resourcegroep waarin de nieuwe Internet Analyzer-resource wordt gemaakt. Als u geen bestaande resourcegroep hebt, kunt u een nieuwe maken.
    * **Naam:** De naam van het nieuwe Internet Analyzer-resourceprofiel.
    * **Regio:** De openbare Azure-regio waarin de resource wordt gemaakt. Tijdens de preview is alleen *VS - west 2* beschikbaar.

7. Wanneer u klaar bent met het opgeven van de profielinstellingen, klikt u op **Bekijken + maken**.

## <a name="configuration"></a>Configuratie

Het voltooien van de basisstappen is een vereiste voor het configureren van een test en het insluiten van de JavaScript-client. Nadat u een profiel hebt gemaakt, gaat u naar **Instellingen > Configuratie** om uw eerste test te configureren.

1. Geef uw test een naam in het vak **Testnaam**.
2. Voeg een beschrijving voor uw test toe in het veld **Beschrijving**.
3. Klik op **Eindpunt configureren** - aan de rechterkant wordt een tabblad weergegeven. Selecteer het type eindpunt dat u wilt configureren: één Azure-regio, meerdere Azure-regio's of een aangepast eindpunt.

    >
    ***Vooraf geconfigureerde eindpunten: een combinatie van één en meerdere Azure-regio's***
    * Selecteer een regio of set regio's uit een [vooraf geconfigureerde lijst met Azure-eindpunten](internet-analyzer-faq.md).
    * Selecteer vervolgens het type toepassing of de architectuur voor het leveren van inhoud die u wilt evalueren.
        * Eén Azure-regio: Siteversnelling ([Azure Front Door](https://azure.microsoft.com/services/frontdoor/)), statische inhoud in de cache opslaan ([Azure CDN voor Microsoft](https://azure.microsoft.com/services/cdn/)) of geen.
        * Meerdere Azure-regio's: Siteversnelling ([Azure Front Door](https://azure.microsoft.com/services/frontdoor/)), DNS-besturing ([Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/))  

    ***Aangepaste eindpunten***
    * Volg de instructies op de pagina [Aangepast eindpunt maken](internet-analyzer-custom-endpoint.md).
    * Plak de locatie van de HTTPS-URL van de afbeelding met één pixel in de portal.
    >

4. Klik op **Toevoegen** om het eindpunt toe te voegen aan uw test.
5. Herhaal de stappen 1-4 om uw tweede eindpunt te configureren. Eindpunt B wordt altijd relatief gemeten ten opzichte van Eindpunt A: houd bij het configureren van eindpunten rekening met het eindpunt dat u wilt testen.
6. Klik op de knop **Opslaan** om uw test op te slaan. Wanneer u een test hebt opgeslagen, kunt u de eindpunten van een bepaalde test niet meer bewerken.
7. Selecteer de test(s) die u wilt starten en klik op **Test starten**. Hiermee wordt de **_status_*_ van uw test (s) gewijzigd in _*_met_**. U kunt tests op elk gewenst moment starten, maar de JavaScript-client moet zijn ingesloten voor de test om metingen te kunnen verzamelen.
8. Voeg op elk gewenst moment meer tests toe. Houd er rekening mee dat de unieke JavaScript-client pas wordt gegenereerd als er een test is gemaakt.

## <a name="embed-client"></a>Client insluiten

Als u een test wilt starten, moet de JavaScript-client zijn ingesloten in uw webtoepassing. Nadat u ten minste één test hebt geconfigureerd, klikt u op **Bekijken + maken**, gaat u naar **Instellingen > Configuratie** en kopieert u de JavaScript-client. Specifieke instructies vindt u op de pagina [Internet Analyzer-client insluiten](internet-analyzer-embed-client.md).  

## <a name="next-steps"></a>Volgende stappen

* Lees de [Veelgestelde vragen over Internet Analyzer](internet-analyzer-faq.md)
* Meer informatie over het insluiten van de [Internet Analyzer-client](internet-analyzer-embed-client.md) en het maken van een [aangepast eindpunt](internet-analyzer-custom-endpoint.md).
