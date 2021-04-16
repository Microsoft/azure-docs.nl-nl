---
title: Een functie maken die kan worden geïntegreerd met Azure Logic Apps
description: Maak een functie die kan worden geïntegreerd met Azure Logic Apps en Azure Cognitive Services. Met de resulterende werkstroom worden tweetsentimenten gecategoriseerd en worden er e-mailmeldingen verzonden.
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/10/2021
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: 3517835859de82117de07ad67cdf8027960ab777
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388647"
---
# <a name="tutorial-create-a-function-to-integrate-with-azure-logic-apps"></a>Zelfstudie: Een functie maken om te integreren met Azure Logic Apps

Azure Functions integreert met Azure Logic Apps in de Logic Apps Designer. Met deze integratie kunt u de rekenkracht van Functions gebruiken in orchestrations met andere Azure- en services van derden.

In deze zelfstudie leert u hoe u een werkstroom maakt om Twitter-activiteiten te analyseren. Wanneer tweets worden geëvalueerd, verzendt de werkstroom meldingen wanneer positieve gevoelens worden gedetecteerd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een API-resource voor Cognitive Services maken.
> * Een functie maken die tweetgevoelens categoriseert.
> * Een logische app maken die verbinding maakt met Twitter.
> * Detectie van gevoelens toevoegen aan de logische app.
> * De logische app verbinden met de functie.
> * Een e-mail versturen op basis van de reactie van de functie.

## <a name="prerequisites"></a>Vereisten

* Een actief [Twitter](https://twitter.com/)-account.
* Een [Outlook.com](https://outlook.com/)-account (om meldingen te verzenden).

> [!NOTE]
> Als u de Gmail-connector wilt gebruiken, kunnen alleen bedrijfsaccounts van G Suite deze connector zonder beperkingen in logische apps gebruiken. Als u een Gmail-consumentenaccount hebt, kunt u de Gmail-connector alleen gebruiken met specifieke door Google goedgekeurde apps en services, of u kunt [een Google-client-app maken voor verificatie in uw Gmail-connector](/connectors/gmail/#authentication-and-bring-your-own-application). <br><br>Zie [Beleid voor gegevensbeveiliging en privacybeleid voor Google-connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md) voor meer informatie.

## <a name="create-text-analytics-resource"></a>Een Text Analytics maken

De Cognitive Services-API's zijn als afzonderlijke resources beschikbaar in Azure. Gebruik de Text Analytics-API om het gevoel van geplaatste tweets te detecteren.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer in de linkerbovenhoek van Azure Portal **Een resource maken**.

1. Selecteer _ai_+ Machine Learning onder **Categorieën**

1. Selecteer _Text Analytics_ onder **Maken.**

1. Voer de volgende waarden in het _scherm Text Analytics_ in.

    | Instelling | Waarde | Opmerkingen |
    | ------- | ----- | ------- |
    | Abonnement | De naam van uw Azure-abonnement | |
    | Resourcegroep | Maak een nieuwe resourcegroep met de **naam tweet-sentiment-tutorial** | Later verwijdert u deze resourcegroep om alle resources te verwijderen die tijdens deze zelfstudie zijn gemaakt. |
    | Regio | Selecteer de regio het dichtst bij u in de buurt | |
    | Name | **TweetSentimentApp** | |
    | Prijscategorie | Selecteer **Gratis F0** | |

1. Selecteer **Controleren + maken**.

1. Selecteer **Maken**.

1. Zodra de implementatie is voltooid, selecteert **u Ga naar resource**.

## <a name="get-text-analytics-settings"></a>Instellingen voor Text Analytics op halen

Nu Text Analytics resource hebt gemaakt, kopieert u enkele instellingen en stelt u deze in voor later gebruik.

1. Selecteer **Sleutels en eindpunt.**

1. Kopieer **sleutel 1** door op het pictogram aan het einde van het invoervak te klikken.

1. Plak de waarde in een teksteditor.

1. Kopieer het **eindpunt door** op het pictogram aan het einde van het invoervak te klikken.

1. Plak de waarde in een teksteditor.

## <a name="create-the-function-app"></a>De functie-app maken

1. Zoek en selecteer Functie-app in het **bovenste zoekvak.**

1. Selecteer **Maken**.

1. Voer de volgende waarden in.

    | Instelling | Voorgestelde waarde | Opmerkingen |
    | ------- | ----- | ------- |
    | Abonnement | De naam van uw Azure-abonnement | |
    | Resourcegroep | **tweet-sentiment-tutorial** | Gebruik dezelfde naam voor de resourcegroep in deze zelfstudie. |
    | Functions App-naam | **TweetSentimentAPI** + een uniek achtervoegsel | Namen van functie-toepassingen zijn wereldwijd uniek. Geldige tekens zijn `a-z` (hoofdlettergevoelig), `0-9` en `-`. |
    | Publiceren | **Code** | |
    | Runtimestack | **.NET** | De voor u opgegeven functiecode is in C#. |
    | Versie | Selecteer het meest recente versienummer | |
    | Regio | Selecteer de regio het dichtst bij u in de buurt | |

1. Selecteer **Controleren + maken**.

1. Selecteer **Maken**.

1. Zodra de implementatie is voltooid, selecteert **u Ga naar resource**.

## <a name="create-an-http-triggered-function"></a>Een met HTTP geactiveerde functie maken  

1. Selecteer functies in het linkermenu _van_ het **venster Functions.**

1. Selecteer **Toevoegen** in het bovenste menu en voer de volgende waarden in.

    | Instelling | Waarde | Opmerkingen |
    | ------- | ----- | ------- |
    | Ontwikkelomgeving | **Ontwikkelen in portal** | |
    | Sjabloon | **HTTP-trigger** | |
    | Nieuwe functie | **TweetSentimentFunction** | Dit is de naam van uw functie. |
    | Autorisatieniveau | **Functie** | |

1. Selecteer de knop **Add**.

1. Selecteer de **knop Code + Testen.**

1. Plak de volgende code in het venster van de code-editor.

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
    
        string requestBody = String.Empty;
        using (StreamReader streamReader =  new  StreamReader(req.Body))
        {
            requestBody = await streamReader.ReadToEndAsync();
        }
    
        dynamic score = JsonConvert.DeserializeObject(requestBody);
        string value = "Positive";
    
        if(score < .3)
        {
            value = "Negative";
        }
        else if (score < .6) 
        {
            value = "Neutral";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(value)
           : new BadRequestObjectResult("Pass a sentiment score in the request body.");
    }
    ```

    Er wordt een gevoelsscore doorgegeven aan de functie , die een categorienaam voor de waarde retourneert.

1. Selecteer de **knop Opslaan** op de werkbalk om uw wijzigingen op te slaan.

    > [!NOTE]
    > Als u de functie wilt testen, **selecteert u Testen/Uitvoeren** in het bovenste menu. Voer op _het_ tabblad Invoer een waarde `0.9` van in het invoervak _Hoofdinvoer_ en selecteer vervolgens **Uitvoeren.** Controleer of de waarde _Positief_ wordt geretourneerd in het _vak HTTP-antwoordinhoud_ in de _sectie_ Uitvoer.

Maak vervolgens een logische app die kan worden geïntegreerd met Azure Functions, Twitter en Cognitive Services API.

## <a name="create-a-logic-app"></a>Een logische app maken

1. Zoek in het bovenste zoekvak naar en selecteer **Logic Apps**.

1. Selecteer **Toevoegen**.

1. Selecteer **Verbruik** en voer de volgende waarden in.

    | Instelling | Voorgestelde waarde |
    | ------- | --------------- |
    | Abonnement | De naam van uw Azure-abonnement |
    | Resourcegroep | **tweet-sentiment-tutorial** |
    | Naam van logische app | **TweetSentimentApp** |
    | Region | Selecteer de regio het dichtst bij u in de buurt, bij voorkeur dezelfde regio die u in de vorige stappen hebt geselecteerd. |

    Accepteer de standaardwaarden voor alle andere instellingen.

1. Selecteer **Controleren + maken**.

1. Selecteer **Maken**.

1. Zodra de implementatie is voltooid, selecteert **u Ga naar resource**.

1. Selecteer de **knop Lege logische app.**

    :::image type="content" source="media/functions-twitter-email/blank-logic-app-button.png" alt-text="Knop Lege logische app":::

1. Selecteer de **knop Opslaan** op de werkbalk om de voortgang op te slaan.

U kunt nu de Logic Apps designer gebruiken om services en triggers toe te voegen aan uw toepassing.

## <a name="connect-to-twitter"></a>Verbinding maken met Twitter

Maak een verbinding met Twitter zodat uw app pollt naar nieuwe tweets.

1. Zoek naar **Twitter** in het bovenste zoekvak.

1. Selecteer het **Twitter-pictogram.**

1. Selecteer de trigger **Wanneer een nieuwe tweet word geplaatst**.

1. Voer de volgende waarden in om de verbinding in te stellen.

    | Instelling |  Waarde |
    | ------- | ---------------- |
    | Verbindingsnaam | **MyTwitterConnection** |
    | Verificatietype | **Standaard gedeelde toepassing gebruiken** |

1. Selecteer **Aanmelden**.

1. Volg de aanwijzingen in het pop-upvenster om het aanmelden bij Twitter te voltooien.

1. Voer vervolgens de volgende waarden in het _vak Wanneer een nieuwe tweet wordt geplaatst_ in.

    | Instelling | Waarde |
    | ------- | ----- |
    | Zoektekst | **#my-twitter-tutorial** |
    | Hoe wilt u controleren op items? | **15** in het tekstvak, en <br> **Minuut** in de vervolgkeuzekeuze |

1. Selecteer de **knop Opslaan** op de werkbalk om de voortgang op te slaan.

Maak vervolgens verbinding met text analytics om het gevoel van verzamelde tweets te detecteren.

## <a name="add-text-analytics-sentiment-detection"></a>Detectie van Text Analytics toevoegen

1. Selecteer **Nieuwe stap**.

1. Zoek naar **Text Analytics** in het zoekvak.

1. Selecteer het **Text Analytics** pictogram.

1. Selecteer **Gevoel detecteren** en voer de volgende waarden in.

    | Instelling | Waarde |
    | ------- | ----- |
    | Verbindingsnaam | **TextAnalyticsConnection** |
    | Accountsleutel | Plak de sleutel Text Analytics account die u eerder hebt gereserveerd. |
    | Site-URL | Plak het eindpunt Text Analytics u eerder hebt gereserveerd. |

1. Selecteer **Maken**.

1. Klik in _het vak Nieuwe parameter_ toevoegen en vink het selectievakje aan naast **documenten** die in het pop-upvenster worden weergegeven.

1. Klik in het _tekstvak Id - 1 van_ de documenten om de pop-up van dynamische inhoud te openen.

1. Zoek in _het zoekvak voor_ dynamische inhoud naar **id** en klik op **Tweet-id.**

1. Klik in het _tekstvak Text - 1 van_ de documenten om de pop-up van dynamische inhoud te openen.

1. Zoek in _het zoekvak_ voor dynamische inhoud naar **tekst** en klik op **Tweettekst.**

1. Typ **Tekstanalyse** in **Een actie kiezen** en klik vervolgens op de actie **Gevoel detecteren**.

1. Selecteer de **knop Opslaan** op de werkbalk om de voortgang op te slaan.

Het _vak Gevoel detecteren_ moet lijken op de volgende schermopname.

:::image type="content" source="media/functions-twitter-email/detect-sentiment.png" alt-text="Gevoelsinstellingen detecteren":::

## <a name="connect-sentiment-output-to-function-endpoint"></a>Gevoelsuitvoer verbinden met het functie-eindpunt

1. Selecteer **Nieuwe stap**.

1. Zoek naar **Azure Functions** in het zoekvak.

1. Selecteer het **Azure Functions** pictogram.

1. Zoek de naam van uw functie in het zoekvak. Als u de bovenstaande richtlijnen hebt gevolgd, begint uw functienaam met **TweetSentimentAPI.**

1. Selecteer het functiepictogram.

1. Selecteer het **item TweetSentimentFunction.**

1. Klik in het _vak Aanvraag body_ en selecteer het item _Gevoelsscore_  detecteren in het pop-upvenster.

1. Selecteer de **knop Opslaan** op de werkbalk om de voortgang op te slaan.

## <a name="add-conditional-step"></a>Voorwaardelijke stap toevoegen

1. Selecteer de **knop Een actie** toevoegen.

1. Klik in het _vak_ Besturingselement en zoek en selecteer **Control** in het pop-upvenster.

1. Selecteer **Voorwaarde**.

1. Klik in _het vak Een waarde_ kiezen en selecteer het item _TweetSentimentFunction_ **Body** in het pop-upvenster.

1. Typ **Positief** in het _vak Een waarde_ kiezen.

1. Selecteer de **knop Opslaan** op de werkbalk om de voortgang op te slaan.

## <a name="add-email-notifications"></a>E-mailmeldingen toevoegen

1. Selecteer onder _het_ vak Waar de knop **Een actie** toevoegen.

1. Zoek en selecteer **Office 365 Outlook** in het tekstvak.

1. Zoek naar **verzenden** en selecteer **Een e-mail verzenden** in het tekstvak.

1. Selecteer de **knop Aanmelden.**

1. Volg de aanwijzingen in het pop-upvenster om het aanmelden bij Office 365 Outlook te voltooien.

1. Voer uw e-mailadres in het _vak Aan_ in.

1. Klik in _het vak_ Onderwerp en klik op het item **Body** onder _TweetSentimentFunction._ Als het _item Body_ niet wordt weergegeven in de lijst, klikt u op de **koppeling Meer** bekijken om de lijst met opties uit te vouwen.

1. Voer na het item _Body_ in _het onderwerp_ de tekst Tweet van **in:**.

1. Klik na _de tekst Tweet van:_ opnieuw op het vak en selecteer Gebruikersnaam **in** de optieslijst Wanneer _een nieuwe tweet wordt_ geplaatst.

1. Klik in het _vak Hoofdtekst_ en selecteer **Tweettekst** onder de optieslijst _Wanneer een nieuwe tweet_ wordt geplaatst. Als het _tekstitem_ Tweet niet wordt weergegeven in de lijst, klikt u op de koppeling **Meer** bekijken om de lijst met opties uit te vouwen.

1. Selecteer de **knop Opslaan** op de werkbalk om de voortgang op te slaan.

Het e-mailvak moet er nu uitzien als deze schermopname.

:::image type="content" source="media/functions-twitter-email/email-notification.png" alt-text="E-mailmeldingen":::

## <a name="run-the-workflow"></a>De werkstroom uitvoeren

1. Tweet vanuit uw Twitter-account de volgende tekst: **I'm enjoying #my-twitter-tutorial**.

1. Ga terug naar Logic Apps Designer en selecteer de **knop** Uitvoeren.

1. Controleer uw e-mail op een bericht uit de werkstroom.

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle Azure-services en -accounts wilt ops schonen die tijdens deze zelfstudie zijn gemaakt, verwijdert u de resourcegroep.

1. Zoek in het bovenste zoekvak naar **Resourcegroepen.**

1. Selecteer de **zelfstudie tweet-sentiment.**

1. Resourcegroep **verwijderen selecteren**

1. Voer **tweet-sentiment-tutorial** in het tekstvak in.

1. Selecteer de knop **Verwijderen**.

U kunt eventueel terugkeren naar uw Twitter-account en eventuele testtweets uit uw feed verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een serverloze API maken met behulp van Azure Functions](functions-create-serverless-api.md)
