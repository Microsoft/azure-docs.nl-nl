---
title: Een inschrijvings-app voor Android bouwen met reageren
titleSuffix: Azure Cognitive Services
description: Meer informatie over het instellen van uw ontwikkel omgeving en het implementeren van een app voor het inschrijven van een gezicht om toestemming van klanten te krijgen.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: bd2032d565f5bd1fb430449be8b8c08e222f531d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025748"
---
# <a name="build-an-enrollment-app-for-android-with-react"></a>Een inschrijvings-app voor Android bouwen met reageren

In deze hand leiding wordt uitgelegd hoe u aan de slag gaat met de inschrijvings toepassing voor beeld gezicht. De app demonstreert aanbevolen procedures voor het verkrijgen van zinvolle toestemming voor het inschrijven van gebruikers in een gezichts herkennings service en het verkrijgen van gezichts gegevens met hoge nauw keurigheid. Een geïntegreerd systeem kan gebruikmaken van een inschrijvings-app, zoals dit om te voorzien in touch-toegangs beheer, identiteits verificatie, het bijhouden van de aanwezigheid van een persoonlijke voor keur of identiteits verificatie, op basis van hun gezichts gegevens.

Wanneer de toepassing wordt gestart, toont deze gebruikers een gedetailleerd scherm voor toestemming. Als de gebruiker toestemming geeft, vraagt de app om een gebruikers naam en wacht woord en legt deze een afbeelding met een hoge kwaliteit aan met behulp van de camera van het apparaat.

De voor beeld-app voor inschrijving is geschreven met behulp van Java script en het reagerende systeem eigen Framework. Het kan momenteel worden geïmplementeerd op Android-apparaten. meer implementatie opties zijn in de toekomst beschikbaar.

## <a name="prerequisites"></a>Vereisten 

* Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/cognitive-services/).  
* Wanneer u uw Azure-abonnement hebt, [maakt u een gezichts bron](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) in het Azure Portal om uw sleutel en eind punt op te halen. Nadat de app is geïmplementeerd, selecteert u **Ga naar resource**.  
  * U hebt de sleutel en het eind punt nodig van de resource die u hebt gemaakt om de toepassing te verbinden met Face-API.  
  * Voor lokale ontwikkeling en tests kunt u de API-sleutel en het eind punt in het configuratie bestand plakken. Voor de laatste implementatie slaat u de API-sleutel op een veilige locatie op en nooit in de code.  

> [!IMPORTANT]
> Deze abonnementssleutels worden gebruikt om toegang te krijgen tot de API van Cognitive Services. Deel uw sleutels niet. Sla ze veilig op, bijvoorbeeld met behulp van Azure Key Vault. We raden u ook aan om deze sleutels regelmatig opnieuw te genereren. Er is slechts één sleutel nodig om een API-aanroep te maken. Wanneer u de eerste sleutel opnieuw genereert, kunt u de tweede sleutel gebruiken voor verdere toegang tot de service.

## <a name="set-up-the-development-environment"></a>De ontwikkelomgeving instellen

1. Kloon de Git-opslag plaats voor de voor [beeld-app voor inschrijving](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Als u uw ontwikkel omgeving wilt instellen, volgt u de <a href="https://reactnative.dev/docs/environment-setup"  title=" reageren systeem eigen "  target="_blank"> documentatie <span class="docon docon-navigate-external x-hidden-focus"></span> </a> . Selecteer **systeem eigen cli-Quick** start als uw ontwikkel besturingssysteem en selecteer **Android** als het doel besturingssysteem. Voltooi de secties voor het **installeren van afhankelijkheden** en **Android-ontwikkel omgeving**.
1. Open de env.jsin het bestand in uw voorkeurs tekst editor, zoals [Visual Studio code](https://code.visualstudio.com/), en voeg uw eind punt en sleutel toe. U kunt het eind punt en de sleutel ophalen in het Azure Portal op het tabblad **overzicht** van uw resource. Deze stap is alleen van toepassing op lokale tests &mdash; . Controleer uw face-API sleutel niet in uw externe opslag plaats.
1. Voer de app uit met behulp van de emulator van de virtuele Android-apparaten van Android Studio of uw eigen Android-apparaat. Als u uw app op een fysiek apparaat wilt testen, volgt u de relevante <a href="https://reactnative.dev/docs/running-on-device"  title=" reageren systeem eigen "  target="_blank"> documentatie <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .  


## <a name="create-an-enrollment-experience"></a>Een inschrijvings ervaring maken  

Nu u de voor beeld-app voor inschrijving hebt ingesteld, kunt u deze aanpassen aan uw eigen behoeften voor de inschrijvings ervaring.

U kunt bijvoorbeeld specifieke informatie over de situatie toevoegen op uw pagina met toestemming:

> [!div class="mx-imgBorder"]
> ![pagina app-toestemming](./media/enrollment-app/1-consent-1.jpg)

De service biedt controle van de kwaliteit van afbeeldingen om u te helpen kiezen of de installatie kopie voldoende kwaliteit heeft om de klant te registreren of om te controleren of het apparaat wordt herkend. Deze app laat zien hoe u toegang krijgt tot frames van de camera van het apparaat, selecteert u de frames met de hoogste kwaliteit en schrijft u het gedetecteerde gezicht in voor de Face-API-service. 

Veel problemen met gezichts herkenning worden veroorzaakt door installatie kopieën met een lage kwaliteit. Enkele factoren die de model prestaties kunnen afnemen, zijn:
* Gezichts grootte (gezichten van de camera)
* Gezichts stand (gezichten die van de camera zijn gedraaid of gekanteld)
* Slechte belichtings omstandigheden (laag licht of achtergrond verlichting) waarbij de afbeelding slecht kan worden blootgesteld of te veel ruis heeft
* Bedekking (gedeeltelijk verborgen of obstructieve gezichten) inclusief accessoires zoals hoedjes of dik-met rand-glas)
* Vervagen (bijvoorbeeld door snelle beweging op het vlak van het moment waarop de foto is gemaakt). 

> [!div class="mx-imgBorder"]
> ![pagina vastleggen van de app-installatie kopie](./media/enrollment-app/4-instruction.jpg)

U ziet dat de app ook beschikt over functionaliteit voor het verwijderen van de registratie van de gebruiker en de optie om opnieuw in te schrijven.

> [!div class="mx-imgBorder"]
> ![pagina Profiel beheer](./media/enrollment-app/10-manage-2.jpg)

Lees het [overzicht](enrollment-overview.md) voor meer informatie over het implementeren en best practices om de functionaliteit van de app uit te breiden voor de volledige inschrijvings ervaring.

## <a name="deploy-the-enrollment-app"></a>De inschrijvings-app implementeren

### <a name="android"></a>Android

Zorg er eerst voor dat uw app gereed is voor productie-implementatie: Verwijder alle sleutels of geheimen uit de app-code en zorg ervoor dat u de [aanbevolen beveiligings procedures](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security?tabs=command-line%2Ccsharp)hebt gevolgd.

Wanneer u klaar bent om uw app uit te geven voor productie, genereert u een APK-bestand dat klaar is voor vrijgave. Dit is de bestands indeling van het pakket voor Android-apps. Dit APK-bestand moet zijn ondertekend met een persoonlijke sleutel. Met deze release-build kunt u de app rechtstreeks naar uw apparaten distribueren. 

Volg de <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title=" voor bereiding voor het vrijgeven van de release "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> -documentatie voor meer informatie over het genereren van een persoonlijke sleutel, het ondertekenen van uw toepassing en het genereren van een release-APK.  

Wanneer u een ondertekende APK hebt gemaakt, raadpleegt u de documentatie uw app <a href="https://developer.android.com/studio/publish"  title=" publiceren publiceren "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> om meer te weten te komen over het vrijgeven van uw app.

## <a name="next-steps"></a>Volgende stappen  

In deze hand leiding hebt u geleerd hoe u uw ontwikkel omgeving instelt en aan de slag gaat met de voor beeld-app voor inschrijving. Als u niet bekend bent met het reageren van systeem eigen, kunt u de [documenten](https://reactnative.dev/docs/getting-started) aan de slag lezen om meer informatie over de achtergrond te krijgen. Het kan ook handig zijn om vertrouwd te raken met [Face-API](Overview.md). Lees de andere secties in de documentatie bij het registreren van apps voordat u begint met de ontwikkeling.
