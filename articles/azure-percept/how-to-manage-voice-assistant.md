---
title: De toepassing Voice Assistant configureren in azure percept Studio
description: De toepassing Voice Assistant configureren in azure percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 8f22379049b74428787b738af832802081be7bf8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022888"
---
# <a name="managing-your-voice-assistant"></a>Uw Voice Assistant beheren

In dit artikel wordt beschreven hoe u het tref woord en de opdrachten van uw Voice Assistant-toepassing in [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)kunt configureren. Raadpleeg dit [artikel](./how-to-configure-voice-assistant.md)voor instructies over het configureren van uw tref woord in IOT hub in plaats van de portal.

Als u nog geen Voice Assistant-toepassing hebt gemaakt, kunt u [een no-code Voice Assistant bouwen met Azure percept Studio en Azure percept audio](./tutorial-no-code-speech.md).

## <a name="keyword-configuration"></a>Trefwoord configuratie

Een tref woord is een woord of korte zin die wordt gebruikt om een Voice Assistant te activeren. "Hey Cortana" is bijvoorbeeld het tref woord voor de Cortana-assistent. Met de functie voor spraak activering kunnen uw gebruikers hun product volledig zelf door nemen door het tref woord te spreken. Als uw product voortdurend luistert naar het tref woord, wordt alle audio lokaal op het apparaat verwerkt totdat er een detectie plaatsvindt om ervoor te zorgen dat gebruikers gegevens zo privé kunnen blijven.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Configuratie in het venster van de Voice Assistant-demo

1. Klik op de pagina demo op **wijzigen** naast **aangepast tref woord** .

    :::image type="content" source="./media/manage-voice-assistant/hospitality-demo.png" alt-text="Scherm afbeelding van het horeca-demo venster.":::

    Als u de voorbeeld pagina niet hebt geopend, gaat u naar de pagina apparaat (zie hieronder) en klikt u op **uw Voice-assistent testen** onder **acties** om toegang te krijgen tot de demo.

1. Selecteer een van de beschik bare tref woorden en klik op **Opslaan** om de wijzigingen toe te passen.

1. De drie LED-lichten op het audio apparaat van Azure percept worden gewijzigd in helder blauw (geen flitsen) wanneer de configuratie is voltooid en uw Voice Assistant gereed is voor gebruik.

### <a name="configuration-within-the-device-page"></a>Configuratie op de pagina apparaat

1. Klik op de pagina overzicht van [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)op **apparaten** in het linkerdeel venster.

    :::image type="content" source="./media/manage-voice-assistant/portal-overview-devices.png" alt-text="Scherm afbeelding van de overzichts pagina van Azure percept Studio met apparaten gemarkeerd.":::

1. Selecteer het apparaat waarop uw Voice Assistant-toepassing is geïmplementeerd.

1. Open het tabblad **spraak** .

    :::image type="content" source="./media/manage-voice-assistant/device-page.png" alt-text="Scherm afbeelding van de pagina rand apparaat met het tabblad spraak gemarkeerd.":::

1. Klik op **wijzigen** naast **sleutel woord**.

    :::image type="content" source="./media/manage-voice-assistant/change-keyword-device.png" alt-text="Scherm afbeelding van de acties voor de beschik bare spraak oplossing.":::

1. Selecteer een van de beschik bare tref woorden en klik op **Opslaan** om de wijzigingen toe te passen.

1. De drie LED-lichten op het audio apparaat van Azure percept worden gewijzigd in helder blauw (geen flitsen) wanneer de configuratie is voltooid en uw Voice Assistant gereed is voor gebruik.

## <a name="create-a-custom-keyword"></a>Een aangepast tref woord maken

Met [Speech Studio](https://speech.microsoft.com/)kunt u een aangepast tref woord maken voor uw telefoon assistent. Het duurt Maxi maal 30 minuten om een basis model voor aangepaste tref woorden te trainen.

Volg de [documentatie van speech Studio](../cognitive-services/speech-service/custom-keyword-basics.md) voor hulp bij het maken van een aangepast tref woord. Zodra het nieuwe tref woord is geconfigureerd, wordt het in de project Santa Cruz-Portal gebruikt voor gebruik met uw toepassing voor spraak assistentie.

## <a name="commands-configuration"></a>Configuratie van opdrachten

Aangepaste opdrachten maken het eenvoudig om geavanceerde spraak opdrachten te bouwen die zijn geoptimaliseerd voor spraak-eerste interactie. Aangepaste opdrachten zijn het meest geschikt voor het volt ooien van taken of voor scenario's met opdrachten en besturings elementen.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Configuratie in het venster van de Voice Assistant-demo

1. Klik op de pagina demo op **wijzigen** naast **aangepaste opdracht** . Als u de voorbeeld pagina niet hebt geopend, gaat u naar de pagina apparaat (zie hieronder) en klikt u op **uw Voice-assistent testen** onder **acties** om toegang te krijgen tot de demo.

1. Selecteer een van de beschik bare aangepaste opdrachten en klik op **Opslaan** om de wijzigingen toe te passen.

### <a name="configuration-within-the-device-page"></a>Configuratie op de pagina apparaat

1. Klik op de pagina overzicht van [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)op **apparaten** in het linkerdeel venster.

1. Selecteer het apparaat waarop uw Voice Assistant-toepassing is geïmplementeerd.

1. Open het tabblad **spraak** .

1. Klik op **wijzigen** naast **opdracht**.

1. Selecteer een van de beschik bare aangepaste opdrachten en klik op **Opslaan** om de wijzigingen toe te passen.

## <a name="create-custom-commands"></a>Aangepaste opdrachten maken

Met [Speech Studio](https://speech.microsoft.com/)kunt u aangepaste opdrachten voor het uitvoeren van uw telefoon assistent maken.

Volg de [documentatie van speech Studio](../cognitive-services/speech-service/quickstart-custom-commands-application.md) voor hulp bij het maken van aangepaste opdrachten. Na de configuratie zijn uw nieuwe opdrachten beschikbaar in azure percept Studio, zodat u deze kunt gebruiken met uw Voice Assistant-toepassing.

## <a name="next-steps"></a>Volgende stappen

Nadat u een toepassing voor spraak assistenten hebt gemaakt, kunt u een [visuele oplossing zonder code](./tutorial-nocode-vision.md) ontwikkelen met uw Azure percept DK.