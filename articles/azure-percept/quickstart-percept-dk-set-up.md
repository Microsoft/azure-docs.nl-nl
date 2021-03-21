---
title: Uw Azure percept DK instellen
description: Verbind uw dev kit met Azure en implementeer uw eerste AI-model
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 9567ec2458a01825568cb853728f71db10228ee3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608258"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Stel uw Azure percept DK in en implementeer uw eerste AI-model

Voltooi de installatie van Azure percept DK om uw dev kit te configureren en uw eerste AI-model te implementeren. Nadat u hebt gecontroleerd of uw Azure-account compatibel is met Azure percept, doet u het volgende:

- Verbind uw dev kit met een Wi-Fi netwerk
- Een SSH-aanmelding instellen voor externe toegang tot uw dev kit
- Een nieuwe IoT Hub maken voor gebruik met Azure percept
- Verbind uw dev kit met uw IoT Hub en Azure-account

Als u problemen ondervindt tijdens dit proces, raadpleegt u de [hand leiding](./how-to-troubleshoot-setup.md) voor het installeren van problemen voor mogelijke oplossingen.

## <a name="prerequisites"></a>Vereisten

- Een Azure percept DK (dev kit).
- Een Windows-, Linux-of OS X-hostcomputer met Wi-Fi mogelijkheden en een webbrowser.
- Een Azure-account met een actief abonnement. [Maak gratis een account.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Het Azure-account moet de rol **eigenaar** of **Inzender** hebben binnen het abonnement. Volg de onderstaande stappen om de rol van uw Azure-account te controleren. Raadpleeg de [documentatie voor toegangs beheer op basis van rollen](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles#azure-roles)voor meer informatie over Azure Role-definities.

    > [!CAUTION]
    > Als u meerdere Azure-accounts hebt, kan uw browser referenties van een ander account in de cache opslaan. Om Verwar ring te voor komen, is het raadzaam om alle ongebruikte browser vensters te sluiten en u aan te melden bij de [Azure Portal](https://portal.azure.com/) voordat u de installatie-ervaring start. Raadpleeg de [hand leiding](./how-to-troubleshoot-setup.md) voor het oplossen van problemen voor aanvullende informatie over hoe u zeker weet dat u bent aangemeld met het juiste account.

### <a name="check-your-azure-account-role"></a>Controleer de rol van uw Azure-account

Voer de volgende stappen uit om te controleren of uw Azure-account een ' eigenaar ' of ' Inzender ' is binnen het abonnement:

1. Ga naar de [Azure Portal](https://portal.azure.com/) en meld u aan met hetzelfde Azure-account dat u wilt gebruiken met Azure percept.

1. Klik op het pictogram **abonnementen** (dit lijkt op een gele toets).

1. Selecteer uw abonnement in de lijst. Als u uw abonnement niet ziet, zorg er dan voor dat u bent aangemeld met het juiste Azure-account. Als u een nieuw abonnement wilt maken, voert u [de volgende stappen uit](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription).

1. Selecteer **toegangs beheer (IAM)** in het menu abonnement.
1. Klik op **Mijn toegang weer geven**.
1. Controleer de rol:
    - Als uw rol wordt vermeld als **lezer** of als u een bericht ontvangt met de melding dat u niet gemachtigd bent om rollen weer te geven, moet u het nodige proces in uw organisatie volgen om de rol van uw account te verhogen.
    - Als uw rol wordt vermeld als **eigenaar** of **Inzender**, werkt uw account met Azure percept en kunt u de installatie-ervaring voortzetten.

## <a name="launch-the-azure-percept-dk-setup-experience"></a>De Setup-ervaring van Azure percept DK starten

1. Sluit uw hostcomputer rechtstreeks aan op het Wi-Fi toegangs punt van de dev kit. Net als bij het maken van verbinding met een ander Wi-Fi netwerk opent u het netwerk en Internet instellingen op uw computer, klikt u op het volgende netwerk en voert u het netwerk wachtwoord in wanneer u hierom wordt gevraagd:

    - **Netwerk naam**: afhankelijk van de versie van het besturings systeem van de dev kit is de naam van het Wi-Fi toegangs punt **SCZ-xxxx** of **apd-xxxx** (waarbij "xxxx" de laatste vier cijfers van het MAC-adres van de dev kit is)
    - **Wacht woord**: is te vinden op de welkomst kaart die is geleverd bij de dev kit

    > [!WARNING]
    > Tijdens de verbinding met het Azure percept DK Wi-Fi toegangs punt wordt de verbinding van de hostcomputer met Internet tijdelijk verbroken. Actieve video vergaderingen, webstreaming of andere op het netwerk gebaseerde ervaringen worden onderbroken.

1. Als de hostcomputer eenmaal verbinding heeft met het Wi-Fi toegangs punt van de dev kit, wordt de installatie-ervaring in een nieuw browser venster automatisch gestart met **uw. nieuwe. apparaat/** in de adres balk. Als het tabblad niet automatisch wordt geopend, start u de installatie-ervaring door naar te gaan [http://10.1.1.1](http://10.1.1.1) . Zorg ervoor dat uw browser is aangemeld met dezelfde referenties voor het Azure-account die u wilt gebruiken met Azure percept.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Welkomst pagina.":::

    > [!CAUTION]
    > De webservice Setup Experience wordt afgesloten na 30 minuten van niet-gebruik. Als dit het geval is, start u de dev kit opnieuw om verbindings problemen te voor komen met het Wi-Fi toegangs punt van de dev kit.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>De Setup-ervaring van Azure percept DK volt ooien

1. Klik op **volgende** in het **welkomst** scherm.

1. Klik op de pagina **netwerk verbinding** op **verbinding maken met een nieuw Wi-Fi-netwerk**.

    Als u uw dev kit al hebt verbonden met uw Wi-Fi netwerk, klikt u op **overs Laan**.

1. Selecteer uw Wi-Fi netwerk in de lijst met beschik bare netwerken en klik op **verbinding maken**. Voer uw netwerk wachtwoord in wanneer u hierom wordt gevraagd.

1. Zodra uw dev kit verbinding heeft gemaakt met het netwerk van uw keuze, wordt op de pagina het IPv4-adres weer gegeven dat is toegewezen aan uw dev kit. **Noteer het IPv4-adres dat op de pagina wordt weer gegeven.** U hebt het IP-adres nodig wanneer u via SSH verbinding maakt met uw dev kit voor het oplossen van problemen en updates van apparaten.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="IP-adres kopiëren.":::

    > [!NOTE]
    > Het IP-adres kan tijdens het opstarten van elk apparaat worden gewijzigd.

1. Lees de gebruiksrecht overeenkomst, selecteer **Ik heb de licentie overeenkomst gelezen en ga hiermee akkoord** (u moet naar de onderkant van de overeenkomst schuiven) en klik op **volgende**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="OVEREENKOMST accepteren.":::

1. Voer de naam en het wacht woord van een SSH-account **in en noteer uw aanmeldings gegevens voor later gebruik**.

    > [!NOTE]
    > SSH (Secure Shell) is een netwerk protocol waarmee u op afstand verbinding met de dev kit kunt maken via een hostcomputer.

1. Klik op de volgende pagina op **instellen als nieuw apparaat** om een nieuw apparaat te maken binnen uw Azure-account.

1. Klik op **kopiëren** om de code van uw apparaat te kopiëren. Klik daarna op **Aanmelden bij Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Kopieer de apparaatcode.":::

1. Er wordt een nieuw browser tabblad geopend met een venster met de tekst **code invoeren**. Plak de code in het venster en klik op **volgende**. Sluit het tabblad **Welkom** niet met het installatie proces.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Voer de code van het apparaat in.":::

1. Meld u aan bij Azure percept met de referenties van het Azure-account die u wilt gebruiken met uw dev kit. Zorg dat het browser tabblad geopend blijft wanneer dit is voltooid.

    > [!CAUTION]
    > Uw browser kan automatisch andere referenties in de cache opslaan. Controleer of u zich met het juiste account aanmeldt.

    Nadat u zich hebt aangemeld bij Azure% op het apparaat, keert u terug naar het **welkomst** tabblad om door te gaan met de installatie.

1. Wanneer de pagina **uw apparaat toewijzen aan uw Azure IOT hub** wordt weer gegeven op het tabblad **Welkom** , voert u een van de volgende acties uit:

    - Als u al een IoT Hub hebt dat u wilt gebruiken met Azure percept en dit op deze pagina wordt weer gegeven, selecteert u het item en gaat u naar stap 15.
    - Als u geen IoT Hub hebt of een nieuw item wilt maken, klikt u op **een nieuwe Azure-IOT hub maken**.

    > [!IMPORTANT]
    > Als u een IoT Hub hebt, maar deze niet in de lijst wordt weer gegeven, bent u mogelijk aangemeld bij Azure percept met de verkeerde referenties. Raadpleeg de [hand leiding](./how-to-troubleshoot-setup.md) voor het oplossen van problemen bij het installeren van.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Selecteer een IoT Hub.":::

1. Als u een nieuwe IoT Hub wilt maken, voert u de volgende velden in:

    - Selecteer het Azure-abonnement dat u wilt gebruiken met Azure percept.
    - Selecteer een bestaande resource groep. Als er nog geen bestaat, klikt u op **Nieuw maken** en volgt u de aanwijzingen.
    - Selecteer de Azure-regio die het dichtst bij uw fysieke locatie ligt.
    - Geef uw nieuwe IoT Hub een naam.
    - Selecteer de prijs categorie S1 (standaard).

    > [!NOTE]
    > Als u uiteindelijk een hogere [bericht doorvoer](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling#message-throughput) nodig hebt voor uw Edge AI-toepassingen, kunt u uw IOT hub op elk gewenst moment [bijwerken naar een hogere standaard categorie](https://docs.microsoft.com/azure/iot-hub/iot-hub-upgrade) in azure Portal. B-en F-lagen bieden geen ondersteuning voor Azure percept.

1. IoT Hub implementatie kan enkele minuten duren. Wanneer de implementatie is voltooid, klikt u op **registreren**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT Hub is geïmplementeerd.":::

1. Voer een apparaatnaam in voor de dev kit en klik op **volgende**.

1. Wacht totdat de apparaten zijn gedownload. Dit kan enkele minuten duren.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="De installatie wordt voltooid.":::

1. Wanneer de installatie van het **apparaat is voltooid.** de dev kit is gekoppeld aan uw IoT Hub en de benodigde software is gedownload. Uw dev kit verbreekt automatisch de verbinding met het Wi-Fi toegangs punt, wat resulteert in de volgende twee meldingen:

    <!---
    > [!NOTE]
    > The onboarding process and connection to the device Wifi access to your host computer shuts down at this point, but your dev kit will stay connected to the internet.   You can restart the onboarding experience with a dev kit reboot, which will allow you to go back through the onboarding and reconnect the device to a different IOT hub associated with the same or a different Azure Subscription..
    --->

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Waarschuwing voor het instellen van de verbinding.":::

1. Verbind uw hostcomputer met het Wi-Fi netwerk waarmee uw Devkit is verbonden in stap 2.

1. Klik op **door gaan naar de Azure Portal**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Ga naar Azure percept Studio.":::

## <a name="view-your-dev-kit-video-stream-and-deploy-a-sample-model"></a>Bekijk de video stroom van uw dev kit en implementeer een voorbeeld model

1. De [overzichts pagina van Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) is uw start punt voor het openen van veel verschillende werk stromen voor de ontwikkeling van de eerste en geavanceerde Edge AI-oplossing. Om aan de slag te gaan, klikt u op **apparaten** in het menu links.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="De lijst met apparaten weer geven.":::

1. Controleer of uw dev kit wordt weer gegeven als **verbonden** en klik erop om de pagina apparaat weer te geven.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Selecteer uw apparaat.":::

1. Klik op **de stream van uw apparaat weer geven**. Als dit de eerste keer is dat de video stroom van uw apparaat wordt weer gegeven, ziet u een melding dat er een nieuw model in de rechter bovenhoek wordt geïmplementeerd. Dit kan enkele minuten duren.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Bekijk uw video stroom.":::

    Zodra het model is geïmplementeerd, krijgt u een andere melding met een koppeling naar een **weergave stroom** . Klik op de koppeling om de video stroom van uw Azure percept Vision-camera in een nieuw browser venster weer te geven. De dev kit is vooraf geladen met een AI-model dat automatisch object detectie van veel algemene objecten uitvoert.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Zie object detectie.":::

1. Azure percept Studio heeft ook een aantal voor beelden van AI-modellen. Als u een voorbeeld model wilt implementeren in uw dev kit, gaat u terug naar de pagina apparaat en klikt u op **een voorbeeld model implementeren**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Verken vooraf ontwikkelde modellen.":::

1. Selecteer een voorbeeld model in de bibliotheek en klik op **implementeren op apparaat**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Zie object detectie in actie.":::

1. Zodra het model is geïmplementeerd, ziet u een melding met een koppeling in de **weergave stroom** in de rechter bovenhoek van het scherm. Als u het model dezicht in actie, klikt u op de koppeling in de melding of gaat u terug naar de pagina apparaat en klikt u op de **Stream van uw apparaat weer geven**. Alle modellen die eerder in de dev kit werden uitgevoerd, worden nu vervangen door het nieuwe model.

## <a name="video-walkthrough"></a>Video-overzicht

Voor een visueel overzicht van de stappen die hierboven worden beschreven, raadpleegt u de volgende video (installatie-ervaring begint bij 0:50):

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een oplossing voor No-code Vision maken](./tutorial-nocode-vision.md)