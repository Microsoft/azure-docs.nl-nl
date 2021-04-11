---
title: 'Programma voor Azure Certified Device-zelf studie: uw apparaat testen'
description: Een stapsgewijze hand leiding voor het testen van het apparaat met de AICS-service in de Azure Certified Device-Portal
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 4cc9e37e95c6402bc535d818e994327e7d526047
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969648"
---
# <a name="tutorial-test-and-submit-your-device"></a>Zelf studie: uw apparaat testen en verzenden

De volgende belang rijke fase van het certificerings proces (hoewel het kan worden voltooid voordat uw apparaatgegevens worden toegevoegd) moet uw apparaat testen. Via de portal gebruikt u de Azure IoT-certificerings service (AICS) om de prestaties van uw apparaten te demonstreren volgens onze certificerings vereisten. Zodra u de test fase hebt door lopen, verzendt u uw apparaat voor definitieve controle en goed keuring door het Azure-certificerings team.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Uw apparaat verbinden met IoT Hub met behulp van de Device Provisioning Service (DPS)
> * Uw apparaat testen volgens uw geselecteerde certificerings programma (s)
> * Uw apparaat voor beoordeling indienen door het Azure-certificerings team

## <a name="prerequisites"></a>Vereisten

- U moet zijn aangemeld en een project voor uw apparaat hebben gemaakt in de [Azure-portal voor gecertificeerde apparaten](https://certify.azure.com). Raadpleeg de [zelf studie](tutorial-01-creating-your-project.md)voor meer informatie.
- Beschrijving Wij adviseren u om uw apparaat voor te bereiden en hand matig de prestaties te verifiëren volgens de vereisten voor certificering. Dit komt doordat u een nieuw project moet maken als u opnieuw wilt testen met een ander apparaatcode of certificerings programma.

## <a name="connecting-your-device-using-dps"></a>Uw apparaat verbinden met DPS

Alle gecertificeerde apparaten zijn vereist om te demonstreren dat er verbinding kan worden gemaakt met IoT Hub met behulp van DPS. De volgende stappen helpen u bij het aansluiten van uw apparaat voor het testen op de portal.

1. Als u de test fase wilt starten, selecteert u de `Connect & test` koppeling op de pagina project samenvatting:  

    ![Koppeling maken en testen](./media/images/connect-and-test-link.png)

1. Afhankelijk van de geselecteerde certificering (en), ziet u de vereiste tests op de pagina ' verbinding maken & test '. U kunt deze controleren om ervoor te zorgen dat u van toepassing bent op het juiste certificerings programma.  

    ![Pagina verbinding maken en testen](./media/images/connect-and-test.png)

1. Verbind uw apparaat met IoT Hub met behulp van de Device Provisioning Service (DPS). DPS ondersteunt connectiviteits opties van symmetrische sleutels, X. 509-certificering en een Trusted Platform Module (TPM). Dit is vereist voor alle certificeringen.

    - *Voor meer informatie over het verbinden van uw apparaat met Azure IoT Hub met DPS gaat u naar [inrichtings apparaten Overview](../iot-dps/about-iot-dps.md "Overzicht van Device Provisioning Service").*
    
1. Als symmetrische sleutels worden gebruikt, wordt u gevraagd om de DPS te configureren met het opgegeven DPS-ID-bereik, de apparaat-ID, de verificatie sleutel en het DPS-eind punt. Anders wordt u gevraagd om X. 509-certificaat of goedkeurings sleutel op te geven.

1. Nadat u uw apparaat met DPS hebt geconfigureerd, bevestigt u de verbinding door te klikken op de `Connect` knop onder aan de pagina. Wanneer de verbinding is geslaagd, kunt u door gaan naar de test fase door te klikken op de `Next` knop.  

    ![Verbinding maken en testen](./media/images/connected.png)

## <a name="testing-your-device"></a>Uw apparaat testen

Wanneer u uw apparaat hebt verbonden met AICS, bent u nu klaar om de certificerings tests uit te voeren die specifiek zijn voor het certificerings programma dat u wilt Toep assen.

1. **Voor certificering voor Azure Certified-apparaten**: op het tabblad Selecteer het apparaat kunt u zien welke tests u op uw apparaat wilt uitvoeren.
1. **Voor IoT Plug en Play-certificering**: Controleer zorgvuldig de para meters die worden gecontroleerd tijdens de test die u hebt gedeclareerd in het model van uw apparaat.
1. **Voor door de rand beheerde certificering**: er zijn geen extra stappen nodig om de connectiviteit te demonstreren.
1. Zodra u de benodigde voor bereidingen voor het opgegeven certificerings programma hebt voltooid, selecteert u `Next` om door te gaan naar de fase ' test '.
1. Selecteer `Run tests` op de pagina om te beginnen met het uitvoeren van AICS met uw apparaat.
1. Zodra u een melding hebt ontvangen dat u de tests hebt door lopen, selecteert u `Finish` om terug te gaan naar uw overzichts pagina.

![Test geslaagd](./media/images/test-pass.png)

7. Als u aanvullende vragen hebt of hulp nodig hebt bij het oplossen van problemen met AICS, gaat u naar onze probleemoplossings handleiding.

> [!NOTE]
> Hoewel u het online certificerings proces voor IoT Plug en Play en Edge kunt volt ooien zonder dat u uw apparaat hoeft in te dienen voor hand matige controle, kunt u contact opnemen met een lid van een Azure Certified-teamlid voor verdere validatie van het apparaat dan wat wordt getest via onze Automation-Service.

## <a name="submitting-your-device-for-review"></a>Uw apparaat ter beoordeling indienen

Zodra u alle verplichte velden in de sectie ' apparaatgegevens ' hebt voltooid en de automatische tests in het proces ' Connect & test ' hebt door lopen, kunt u nu een melding ontvangen van het Azure Certified Device team dat u gereed bent voor certificerings beoordeling.

1. Selecteer `Submit for review` op de pagina project samenvatting:  

    ![Koppeling controleren en certificeren](./media/images/review-and-certify.png)

1. Bevestig uw inzending in het pop-upvenster. Zodra een apparaat is verzonden, worden alle details van het apparaat alleen-lezen totdat de bewerking is aangevraagd. (Zie [informatie over uw apparaat bewerken na het publiceren](./how-to-edit-published-device.md).)  

    ![Het dialoog venster certificerings controle starten](./media/images/start-certification-review.png)

1. Zodra het project is verzonden, wordt in de pagina project overzicht aangegeven dat het project afkomstig is `Under Certification Review` van het Azure-certificerings team:  

    ![Onder controleren](./media/images/review-and-certify-under-review.png)

1. Binnen 5-7 werk dagen verwachten we een e-mail antwoord van het Azure-certificerings team naar het adres dat is opgenomen in uw bedrijfs profiel met betrekking tot de status van de verzen ding van het apparaat.

    - Goedgekeurde verzen ding  
        Zodra het project is beoordeeld en goedgekeurd, ontvangt u een e-mail. Het e-mail bericht bevat een set bestanden met inbegrip van de Azure Certified Device badge, de richt lijnen voor badge gebruik en andere informatie over het bepalen van het bericht dat uw apparaat is gecertificeerd. Gefeliciteerd

    - Inzending in behandeling  
        In het geval uw project niet is goedgekeurd, kunt u wijzigingen aanbrengen in de project gegevens en het apparaat vervolgens opnieuw verzenden voor certificering. Er wordt een e-mail bericht verzonden met informatie over waarom het project niet is goedgekeurd en de stappen voor het opnieuw indienen voor certificering.

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd! Het apparaat heeft nu alle tests door lopen en is goedgekeurd via het Azure Certified Device-programma. U kunt uw apparaat nu publiceren naar onze Azure Certified-catalogus, waar klanten uw producten kunnen kopen met vertrouwen in hun prestaties met Azure.
> [!div class="nextstepaction"]
> [Zelf studie: uw apparaat publiceren](tutorial-04-publishing-your-device.md)

