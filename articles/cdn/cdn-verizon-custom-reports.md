---
title: Aangepaste rapporten van Verizon | Microsoft Docs
description: 'U kunt gebruiks patronen voor uw CDN weer geven met behulp van de volgende rapporten: band breedte, verzonden gegevens, treffers, cache status, verhouding van cache treffers, IPV4/IPV6-gegevens overgedragen.'
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
ms.topic: how-to
ms.date: 10/11/2017
ms.author: allensu
ms.openlocfilehash: cff355aeb74efc9dec2780840ae57fea4c5764cc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84888710"
---
# <a name="custom-reports-from-verizon"></a>Aangepaste rapporten van Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Door aangepaste Verizon-rapporten te gebruiken via de profielen portal beheren voor Verizon kunt u het type gegevens definiëren dat moet worden verzameld voor de Edge CNAME-rapporten.


## <a name="accessing-verizon-custom-reports"></a>Aangepaste Verizon-rapporten openen
1. Klik op de Blade CDN-profiel op de knop **beheren** .
   
    ![Beheer knop voor CDN-profiel](./media/cdn-reports/cdn-manage-btn.png)
   
    De CDN-beheer portal wordt geopend.
2. Beweeg de muis aanwijzer over het tabblad **Analytics** en Beweeg vervolgens de muis aanwijzer over het vervolg van het **aangepaste rapport** . Klik op **Edge CNAMES**.
   
    ![CDN-beheer Portal-menu met aangepaste rapporten](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Aangepast rapport van Edge CNAMEs
Het aangepaste rapport van de Edge-CNAME bevat treffers en gegevens overgedragen statistieken voor rand-CNAMEs waarvoor aangepaste rapport registratie is ingeschakeld. Edge-CNAMEs bestaan uit Azure CDN eindpunt hostnamen en alle bijbehorende aangepaste domein hostnamen. 

Logboek registratie van aangepaste rapport gegevens begint één uur nadat u de aangepaste rapportage mogelijkheid van een Edge CNAME hebt ingeschakeld. U kunt rapport gegevens weer geven door een Edge CNAMEs-rapport te genereren voor een specifiek platform of voor alle platforms. De dekking voor dit rapport is beperkt tot de rand-CNAME waarvoor aangepaste rapport gegevens zijn verzameld tijdens de opgegeven periode. Het rand CNAME-rapport bestaat uit een grafiek en een gegevens tabel voor de Top 10 van de rand-CNAME volgens de metrische gegevens die in de optie metrische gegevens zijn gedefinieerd. 

Genereer een aangepast rapport door de volgende rapport opties te definiëren:

- Metrische gegevens: de volgende opties worden ondersteund:

   - Hits: geeft het totale aantal aanvragen aan dat wordt omgeleid naar een Edge CNAME waarop de aangepaste rapportage mogelijkheid is ingeschakeld. Deze metrische waarde bevat niet de status code die wordt geretourneerd naar de client.

   - Overgedragen gegevens: geeft de totale hoeveelheid gegevens aan die is overgedragen van de Edge-servers naar de HTTP-clients (bijvoorbeeld webbrowsers) voor aanvragen die worden omgeleid naar een Edge CNAME waarop de aangepaste rapportage mogelijkheid is ingeschakeld. De hoeveelheid overgedragen gegevens wordt berekend door HTTP-reactie headers toe te voegen aan de hoofd tekst van het antwoord. Als gevolg hiervan is de hoeveelheid gegevens die voor elke Asset wordt overgedragen groter dan de werkelijke bestands grootte.

- Groeperingen: bepaalt het type statistieken dat onder het staaf diagram wordt weer gegeven. De volgende opties worden ondersteund:

   - HTTP-antwoord codes: organiseert statistieken op HTTP-antwoord code (bijvoorbeeld 200, 403, enzovoort) die naar de client wordt geretourneerd. 

   - Cache status: organiseert statistieken op cache status.


Als u het datum bereik voor het rapport wilt instellen, kunt u een vooraf gedefinieerd datum bereik, zoals **vandaag** of **deze week**, selecteren in de vervolg keuzelijst of u kunt **aangepast** selecteren en hand matig een datum bereik invoeren door te klikken op de kalender pictogrammen. 

Nadat u het datum bereik hebt geselecteerd, klikt u op **Ga** om het rapport te genereren.

U kunt de gegevens in Excel-indeling exporteren door rechts van de knop **Ga** op het Excel-symbool te klikken.

![CNAME-rapport](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Aangepaste rapport velden van Edge-CNAME

| Veld                     | Beschrijving   |
|---------------------------|---------------|
| 2xx                       | Hiermee wordt het totale aantal verzonden aanvragen of gegevens (MB) voor de rand-CNAME aangegeven dat resulteert in een 2xx HTTP-status code (bijvoorbeeld 200 OK). |
| 3xx                       | Hiermee wordt het totale aantal aanvragen of overgedragen gegevens (MB) aangegeven voor de Edge CNAME die resulteert in een 3xx HTTP-status code (bijvoorbeeld 302 gevonden of 304 niet gewijzigd. |
| 4xx                       | Hiermee wordt het totale aantal verzonden aanvragen of gegevens (MB) voor de rand-CNAME aangegeven dat resulteert in een 4xx HTTP-status code (bijvoorbeeld 400 ongeldige aanvraag, 403 verboden of 404 niet gevonden). |
| 5xx                       | Hiermee wordt het totale aantal verzonden aanvragen of gegevens (MB) voor de rand-CNAME aangegeven dat resulteert in een 5xx HTTP-status code (bijvoorbeeld 500 interne server fout of 502 ongeldige gateway). |
| Percentage cache treffers               | Hiermee wordt het percentage van cache aanvragen aangegeven dat rechtstreeks vanuit de cache naar de aanvrager is verzonden. |
| Cachetreffers                | Hiermee wordt het totale aantal verzonden aanvragen of gegevens (MB) voor de rand-CNAME aangegeven dat resulteert in een cache treffer (bijvoorbeeld TCP_EXPIRED_HIT, TCP_HIT of TCP_PARTIAL_HIT). Er treedt een cache treffer op wanneer een versie van de aangevraagde inhoud in de cache wordt gevonden. |
| Overgedragen gegevens (MB)     | Hiermee wordt de totale hoeveelheid verzonden gegevens (MB) van de Edge-servers naar HTTP-clients (webbrowsers) voor de Edge CNAME aangegeven. De hoeveelheid overgedragen gegevens wordt berekend door de HTTP-antwoord headers toe te voegen aan de hoofd tekst van het antwoord. Als gevolg hiervan is de hoeveelheid gegevens die voor elke Asset wordt overgedragen groter dan de werkelijke bestands grootte. |
| Beschrijving               | Identificeert een Edge CNAME op basis van de hostnaam |
| Treffers                      | Hiermee wordt het totale aantal aanvragen voor de rand-CNAME aangegeven |
| Missers                    | Hiermee wordt het totale aantal verzonden aanvragen of gegevens (MB) voor de rand-CNAME aangegeven dat resulteert in een Missing in een cache (bijvoorbeeld TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS of TCP_MISS). Er treedt een cache-Misser op wanneer de aangevraagde inhoud niet is opgeslagen in de cache op de rand server die de aanvraag heeft geaccepteerd. | 
| Geen cache                  | Hiermee wordt het totale aantal aanvragen of overgedragen gegevens (MB) aangegeven voor de Edge CNAME die resulteert in een CONFIG_NOCACHE cache-status code.  |
| Anders                     | Hiermee wordt het totale aantal aanvragen of overgedragen gegevens (MB) aangegeven voor de Edge CNAME aangegeven dat resulteert in een HTTP-status code die buiten het bereik van de 2xx-5xx valt. |
| Platform                  | Hiermee wordt het platform aangegeven dat het Edge CNAME-verkeer afhandelt. |
| Niet toegewezen               | Hiermee wordt het totale aantal verzonden aanvragen of gegevens (MB) aangegeven voor de Edge CNAME waarvoor de cache status code of de gegevens van de HTTP-status code niet zijn geregistreerd.  |
| Niet in cache               | Hiermee wordt het totale aantal verzonden aanvragen of gegevens (MB) aangegeven voor de Edge CNAME die resulteert in een niet-CACHE geheugen status code.  |


## <a name="considerations"></a>Overwegingen
Rapporten kunnen alleen in de afgelopen 18 maanden worden gegenereerd.

