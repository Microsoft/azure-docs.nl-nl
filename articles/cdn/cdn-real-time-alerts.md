---
title: Waarschuwingen in realtime Azure CDN | Microsoft Docs
description: Real-time waarschuwingen in Microsoft Azure CDN. Realtime waarschuwingen bieden meldingen over de prestaties van de eind punten in uw CDN-profiel.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6811a06eb3483fd53b6e566033935c3b2e00ceca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84887251"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Real-time waarschuwingen in Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Overzicht
In dit document worden real-time waarschuwingen in Microsoft Azure CDN uitgelegd. Deze functionaliteit biedt realtime meldingen over de prestaties van de eind punten in uw CDN-profiel.  U kunt e-mail-of HTTP-waarschuwingen instellen op basis van:

* Bandbreedte
* Statuscodes
* Cache statussen
* Verbindingen

## <a name="creating-a-real-time-alert"></a>Een real-time waarschuwing maken
1. Blader in het [Azure Portal](https://portal.azure.com)naar uw CDN-profiel.
   
    ![CDN-profiel](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. Klik op de Blade CDN-profiel op de knop **beheren** .
   
    ![Beheer knop voor CDN-profiel](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    De CDN-beheer portal wordt geopend.
3. Beweeg de muis aanwijzer over het tabblad **Analytics** en Beweeg vervolgens de muis aanwijzer over de **Realtime statistieken-** flyout.  Klik op **real-time waarschuwingen**.
   
    ![CDN-beheer Portal](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    De lijst met bestaande waarschuwings configuraties (indien van toepassing) wordt weer gegeven.
4. Klik op de knop **waarschuwing toevoegen** .
   
    ![Knop voor waarschuwing toevoegen](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Er wordt een formulier voor het maken van een nieuwe waarschuwing weer gegeven.
   
    ![Nieuw waarschuwings formulier](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Als u wilt dat deze waarschuwing actief is wanneer u op **Opslaan** klikt, schakelt u het selectie vakje **waarschuwing ingeschakeld** in.
6. Voer in het veld **naam** een beschrijvende naam in voor de waarschuwing.
7. Selecteer in de vervolg keuzelijst **media type** de optie **http-large object**.
   
    ![Media type waarvoor HTTP-Large Object geselecteerd](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > U moet **http-large object** als het **media type** selecteren.  De andere opties worden niet gebruikt door **Azure CDN van Verizon**.  Als u **http-large object** niet selecteert, wordt uw waarschuwing nooit geactiveerd.
   > 
   > 
8. Maak een **expressie** om te bewaken door een **metriek**, **operator** en **trigger waarde** te selecteren.
   
   * Selecteer bij **metriek** het type voor waarde dat u wilt controleren.  **Bandbreedte Mbps** is de hoeveelheid bandbreedte gebruik in megabits per seconde.  **Totale verbindingen** is het aantal gelijktijdige http-verbindingen met onze edge-servers.  Zie [Azure CDN cache status codes](/previous-versions/azure/mt759237(v=azure.100)) en [Azure CDN HTTP-status](/previous-versions/azure/mt759238(v=azure.100)) codes voor definities van de verschillende cache statussen en status codes.
   * **Operator** is de reken kundige operator die de relatie tussen de metriek en de trigger waarde tot stand brengt.
   * **Trigger waarde** is de drempel waarde waaraan moet worden voldaan voordat een melding wordt verzonden.
     
     In het volgende voor beeld geeft de gemaakte expressie aan dat er een melding wordt verzonden wanneer het aantal status codes van 404 groter is dan 25.
     
     ![Voorbeeld expressie in realtime-waarschuwing](./media/cdn-real-time-alerts/cdn-expression.png)
9. Geef bij **interval** op hoe vaak u wilt dat de expressie wordt geëvalueerd.
10. Selecteer in de vervolg keuzelijst **aanmelden op** wanneer u een melding wilt ontvangen wanneer de expressie waar is.
    
    * **Begin van voor waarde** geeft aan dat er een melding wordt verzonden wanneer de opgegeven voor waarde voor het eerst wordt gedetecteerd.
    * **Voor waarde-einde** geeft aan dat er een melding wordt verzonden wanneer de opgegeven voor waarde niet meer wordt gedetecteerd. Deze melding kan alleen worden geactiveerd nadat het netwerk bewakings systeem heeft gedetecteerd dat de opgegeven voor waarde is opgetreden.
    * **Doorlopend** geeft aan dat er telkens een melding wordt verzonden wanneer het netwerk bewakings systeem de opgegeven voor waarde detecteert. Houd er wel voor dat het netwerk bewakings systeem slechts eenmaal per interval controleert voor de opgegeven voor waarde.
    * **Begin en einde van de voor waarde** geeft aan dat er een melding wordt verzonden wanneer de opgegeven voor waarde voor het eerst wordt gedetecteerd en eenmaal wanneer de voor waarde niet meer wordt gedetecteerd.
1. Als u meldingen per e-mail wilt ontvangen, schakelt u het selectie vakje **bericht per e-mail** in.  
    
    ![E-mail formulier per E-mail verzenden](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    Voer in het veld **aan** het e-mail adres in waar u de meldingen wilt verzenden. Voor het **onderwerp** en de **hoofd tekst** kunt u de standaard instelling verlaten of u kunt het bericht aanpassen met behulp van de lijst met **beschik bare tref woorden** om waarschuwings gegevens dynamisch in te voegen wanneer het bericht wordt verzonden.
    
    > [!NOTE]
    > U kunt de e-mail melding testen door te klikken op de knop **melding testen** , maar pas nadat de configuratie van de waarschuwing is opgeslagen.
    > 
    > 
12. Als u wilt dat meldingen worden geplaatst op een webserver, schakelt u het selectie vakje **bericht over http post in** .
    
    ![Melding via HTTP post-formulier](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    Voer in het veld **URL** de URL in van de locatie waar het HTTP-bericht moet worden geplaatst. Voer in het tekstvak **headers** de HTTP-headers in die moeten worden verzonden in de aanvraag.  Voor de **hoofd tekst** kunt u het bericht aanpassen met behulp van de lijst met **beschik bare tref woorden** om waarschuwings gegevens dynamisch in te voegen wanneer het bericht wordt verzonden.  **Kopteksten** en **hoofd tekst** worden standaard ingesteld op een XML-nettolading zoals in het volgende voor beeld:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > U kunt de HTTP Post-melding testen door te klikken op de knop **melding testen** , maar pas nadat de configuratie van de waarschuwing is opgeslagen.
    > 
    > 
13. Klik op de knop **Opslaan** om uw waarschuwings configuratie op te slaan.  Als u in stap 5 de **waarschuwing hebt ingeschakeld** , is uw waarschuwing nu actief.

## <a name="next-steps"></a>Volgende stappen
* [Real-time statistieken in azure CDN](cdn-real-time-stats.md) analyseren
* Dieper met [geavanceerde HTTP-rapporten](cdn-advanced-http-reports.md)
* [Gebruiks patronen](cdn-analyze-usage-patterns.md) analyseren

