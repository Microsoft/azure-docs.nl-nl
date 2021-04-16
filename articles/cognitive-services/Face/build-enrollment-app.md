---
title: Een React-app bouwen om gebruikers toe te voegen aan een Face-service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het instellen van uw ontwikkelomgeving en het implementeren van een Face-app om toestemming te krijgen van klanten.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: 39a74c7f3d5fb8f8b60a66947fcce9837ed6ee13
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505102"
---
# <a name="build-a-react-app-to-add-users-to-a-face-service"></a>Een React-app bouwen om gebruikers toe te voegen aan een Face-service

In deze handleiding ziet u hoe u aan de slag gaat met de voorbeeldtoepassing voor Face-inschrijving. De app demonstreert best practices voor het verkrijgen van zinvolle toestemming om gebruikers toe te voegen aan een gezichtsherkenningsservice en om gezichtsgegevens met hoge nauwkeurigheid te verkrijgen. Een geïntegreerd systeem kan een app als deze gebruiken om op basis van gezichtsgegevens aanraakloos toegangsbeheer, identiteitsverificatie, bijhouden van aanwezigheid, personalisatiekiosk of identiteitsverificatie te bieden.

Wanneer de toepassing wordt gestart, krijgen gebruikers een gedetailleerd toestemmingsscherm te zien. Als de gebruiker toestemming geeft, vraagt de app om een gebruikersnaam en wachtwoord en legt vervolgens een gezichtsafbeelding van hoge kwaliteit vast met behulp van de camera van het apparaat.

De voorbeeld-app is geschreven met behulp van JavaScript en het React Native-framework. Het kan momenteel worden geïmplementeerd op Android-apparaten; meer implementatieopties worden in de toekomst beschikbaar.

## <a name="prerequisites"></a>Vereisten 

* Een Azure-abonnement: [maak er gratis een.](https://azure.microsoft.com/free/cognitive-services/)  
* Zodra u uw Azure-abonnement hebt, [maakt u een Face-resource](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) in de Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, selecteert u **Ga naar resource**.  
  * U hebt de sleutel en het eindpunt nodig van de resource die u hebt gemaakt om uw toepassing te verbinden met de Face-API.  
  * Voor lokale ontwikkeling en testen kunt u de API-sleutel en het eindpunt in het configuratiebestand plakken. Voor de uiteindelijke implementatie moet u de API-sleutel opslaan op een veilige locatie en nooit in de code.  

> [!IMPORTANT]
> Deze abonnementssleutels worden gebruikt om toegang te krijgen tot de API van Cognitive Services. Deel uw sleutels niet. Bewaar ze veilig, bijvoorbeeld met behulp van Azure Key Vault. We raden u ook aan om deze sleutels regelmatig opnieuw te genereren. Er is slechts één sleutel nodig om een API-aanroep te maken. Wanneer u de eerste sleutel opnieuw genereert, kunt u de tweede sleutel gebruiken voor verdere toegang tot de service.

## <a name="set-up-the-development-environment"></a>De ontwikkelomgeving instellen

1. Kloon de Git-opslagplaats voor de [voorbeeld-app](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Volg de React Native-documentatie voor React Native-documentatie om uw ontwikkelomgeving <a href="https://reactnative.dev/docs/environment-setup"  title=" "  target="_blank"> in te </a> stellen. Selecteer **React Native CLI Quickstart** als uw ontwikkelbesturingssysteem en selecteer **Android** als het doelbesturingssysteem. Voltooi de secties **Afhankelijkheden en Android-ontwikkelomgeving** **installeren.**
1. Open het env.jsbestand in de gewenste teksteditor, zoals [Visual Studio Code,](https://code.visualstudio.com/)en voeg uw eindpunt en sleutel toe. U kunt uw eindpunt en sleutel in de Azure Portal onder het **tabblad** Overzicht van uw resource. Deze stap is alleen bedoeld voor lokale &mdash; testdoeleinden en controleert niet uw Face-API-sleutel bij uw externe opslagplaats.
1. Voer de app uit met behulp van de emulator voor virtuele Android-apparaten Android Studio uw eigen Android-apparaat. Als u uw app wilt testen op een fysiek apparaat, volgt u de relevante <a href="https://reactnative.dev/docs/running-on-device"  title=" React Native-documentatie "  target="_blank"> voor React Native-documentatie. </a>  


## <a name="create-a-user-add-experience"></a>Een gebruikerservaring voor toevoegen maken  

Nu u de voorbeeld-app hebt ingesteld, kunt u deze aanpassen aan uw eigen behoeften.

U kunt bijvoorbeeld situatiespecifieke informatie toevoegen op uw toestemmingspagina:

> [!div class="mx-imgBorder"]
> ![app-toestemmingspagina](./media/enrollment-app/1-consent-1.jpg)

De service biedt kwaliteitscontroles voor afbeeldingen om u te helpen kiezen of de afbeelding van voldoende kwaliteit is om de klant toe te voegen of gezichtsherkenning te proberen. Deze app laat zien hoe u frames kunt openen vanuit de camera van het apparaat, de frames van de hoogste kwaliteit selecteert en het gedetecteerde gezicht toevoegt aan de Face-API-service. 

Veel problemen met gezichtsherkenning worden veroorzaakt door referentieafbeeldingen van lage kwaliteit. Enkele factoren die de prestaties van het model kunnen verslechteren, zijn:
* Gezichtsgrootte (gezichten die ver van de camera liggen)
* Gezichtsstand (gezichten die zijn omgedraaid of gekanteld van de camera)
* Slechte lichtomstandigheden (weinig licht of achtergrondverlichting) waarbij de afbeelding slecht wordt blootgesteld of te veel ruis heeft
* Occlusie (gedeeltelijk verborgen of geblokkeerde gezichten) met inbegrip van accessoires zoals hoeden of een bril met een dikte)
* Wazig (bijvoorbeeld door snelle gezichtsver movement wanneer de foto is gemaakt). 

> [!div class="mx-imgBorder"]
> ![instructiepagina voor het vastleggen van app-afbeeldingen](./media/enrollment-app/4-instruction.jpg)

U ziet dat de app ook functionaliteit biedt voor het verwijderen van de gegevens van de gebruiker en de optie om opnieuw toe te voegen.

> [!div class="mx-imgBorder"]
> ![pagina profielbeheer](./media/enrollment-app/10-manage-2.jpg)

Als u de functionaliteit van de app wilt [](enrollment-overview.md) uitbreiden voor de volledige ervaring, leest u het overzicht voor aanvullende functies die u wilt implementeren en best practices.

## <a name="deploy-the-app"></a>De app implementeren

### <a name="android"></a>Android

Zorg er eerst voor dat uw app gereed is voor productie-implementatie: verwijder sleutels of geheimen uit de app-code en zorg ervoor dat u de best [practices voor beveiliging hebt gevolgd.](../cognitive-services-security.md?tabs=command-line%2ccsharp)

Wanneer u klaar bent om uw app uit te brengen voor productie, genereert u een release-ready APK-bestand. Dit is de pakketbestandsindeling voor Android-apps. Dit APK-bestand moet zijn ondertekend met een persoonlijke sleutel. Met deze release-build kunt u de app rechtstreeks naar uw apparaten distribueren. 

Volg de documentatie Voorbereiden voor release voorbereiden voor release voor meer informatie over het genereren van een persoonlijke sleutel, het ondertekenen van uw toepassing en <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" het genereren van een "  target="_blank"> </a> release-APK.  

Nadat u een ondertekende APK hebt gemaakt, bekijkt u de documentatie Uw app publiceren Uw app publiceren voor meer informatie over <a href="https://developer.android.com/studio/publish"  title=" het vrijgeven van uw "  target="_blank"> </a> app.

## <a name="next-steps"></a>Volgende stappen  

In deze handleiding hebt u geleerd hoe u uw ontwikkelomgeving in kunt stellen en aan de slag kunt gaan met de voorbeeld-app. Als u nog geen ervaring hebt met React Native, kunt u de aan de [slag-documenten](https://reactnative.dev/docs/getting-started) lezen voor meer achtergrondinformatie. Het kan ook handig zijn om vertrouwd te raken met de [Face-API.](Overview.md) Lees de andere secties over het toevoegen van gebruikers voordat u begint met ontwikkelen.