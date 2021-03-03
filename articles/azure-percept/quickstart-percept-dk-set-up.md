---
title: Uw Azure percept DK instellen
description: Verbind uw dev kit met Azure en implementeer uw eerste AI-model
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/15/2021
ms.custom: template-quickstart
ms.openlocfilehash: 3540204d66bb589c567514f92a9a8acb2159e343
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665420"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Stel uw Azure percept DK in en implementeer uw eerste AI-model

Ga aan de slag met Azure percept DK en Azure percept Studio met behulp van de Azure percept DK Setup-ervaring om uw apparaat te verbinden met Azure en uw eerste AI-model te implementeren. Nadat u hebt gecontroleerd of uw Azure-account compatibel is met Azure percept Studio, voltooit u de installatie-ervaring om te controleren of uw Azure percept DK is geconfigureerd voor het maken van een Edge AI-proef versie van concepten.

Raadpleeg de hand leiding voor het [oplossen](./troubleshoot-dev-kit.md) van mogelijke oplossingen als u problemen ondervindt tijdens deze snel starten.

## <a name="prerequisites"></a>Vereisten

- Een Azure percept DK.
- Een op Windows, Linux of OS X gebaseerde hostcomputer met Wi-Fi-functionaliteit en een webbrowser.
- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Het Azure-account moet de rol ' eigenaar ' of ' Inzender ' hebben voor het abonnement. Meer informatie over definities van Azure-rollen

### <a name="prerequisite-check"></a>Controle van vereisten

Volg deze stappen om te controleren of uw Azure-account een ' eigenaar ' of ' Inzender ' is voor het abonnement.

1. Ga naar de Azure Portal en meld u aan met hetzelfde Azure-account dat u wilt gebruiken met Azure percept Studio. 

    > [!NOTE]
    > Als u meerdere Azure-accounts hebt, kan uw browser referenties van een ander account in de cache opslaan. Raadpleeg de gids voor probleem oplossing voor meer informatie over hoe u kunt controleren of u bent aangemeld met het juiste account.

1. Vouw het hoofd menu uit de linkerbovenhoek van het scherm uit en klik op abonnementen of selecteer abonnementen in het menu met pictogrammen op de start pagina. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-01-subscription.png" alt-text="supscription icon in Azure portal.":::
    --->
1. Selecteer uw abonnement in de lijst. Als uw abonnement niet in de lijst wordt weer geven, moet u ervoor zorgen dat u bent aangemeld met het juiste Azure-account. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-02-sub-list.png" alt-text="supscription list in Azure portal.":::
    --->
Als u een nieuw abonnement wilt maken, voert u [de volgende stappen uit](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription).

1. Selecteer toegangs beheer (IAM) in het menu abonnement
1. Klik op de knop mijn toegang weer geven
1. De rol controleren
    - Als de rol van lezer wordt weer gegeven of als u een bericht ontvangt met de melding dat u geen machtigingen hebt om rollen weer te geven, moet u het nodige proces in uw organisatie volgen om de rol van uw account op te halen.
    - Als de rol wordt weer gegeven als ' eigenaar ' of ' Inzender ', werkt uw account met Azure percept Studio. 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>De Setup-ervaring van Azure percept DK starten

<!---
> [!NOTE]
> Connecting over ethernet? See [this how-to guide](<link needed>) for detailed instructions.
--->
1. Verbind uw hostcomputer rechtstreeks met het Wi-Fi-toegangs punt van de dev kit. Dit is net zoals bij het maken van verbinding met een ander Wi-Fi-netwerk
    - **netwerk naam**: SCZ-xxxx (waarbij ' xxxx ' de laatste vier cijfers van het Mac-netwerk adres van de dev kit is)
    - **wacht woord**: is te vinden op de welkomst kaart die is geleverd bij de dev kit

    > [!WARNING]
    > Tijdens de verbinding met het Azure percept DK Wi-Fi-toegangs punt, zal de hostcomputer tijdelijk geen verbinding met internet hebben. Actieve video vergaderingen, webstreaming of andere op het netwerk gebaseerde ervaringen worden onderbroken totdat stap 3 van de Azure percept DK Setup-ervaring is voltooid.

1. Wanneer het apparaat is verbonden met het Wi-Fi-toegangs punt van de dev kit, wordt de Azure percept DK Setup-ervaring automatisch gestart in een nieuw browser venster. Als de app niet automatisch wordt geopend, kunt u deze hand matig starten door een browser venster te openen en naar te navigeren [http://10.1.1.1](http://10.1.1.1) . 

1. Nu u de Azure percept Setup-ervaring hebt gestart, kunt u door gaan met de installatie. 

    > [!NOTE]
    > De Azure percept DK Setup Experience-webservice wordt afgesloten na 30 minuten van niet-gebruik en het volt ooien van de installatie-ervaring. Als dit gebeurt, wordt het aanbevolen om de dev kit opnieuw te starten om verbindings problemen met het Wi-Fi-toegangs punt van de dev kit te voor komen.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>De Setup-ervaring van Azure percept DK volt ooien

1. Aan de slag: Klik op **volgende** in het welkomst scherm.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Welkomst pagina.":::

1. Verbinding maken met Wi-Fi: Klik op de pagina netwerk verbinding op **verbinding maken met een nieuw WiFi-netwerk** om uw Devkit te verbinden met uw Wi-Fi-netwerk.

    Als u deze dev kit eerder hebt verbonden met uw Wi-Fi-netwerk of als u al verbinding hebt met de Azure percept DK Setup-ervaring via uw Wi-Fi-netwerk, klikt u op de koppeling **overs Laan** .

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-02-connect-to-wi-fi.png" alt-text="Verbinding maken met Wi-Fi.":::

1. Selecteer uw Wi-Fi-netwerk in de beschik bare verbindingen en maak verbinding. (Uw lokale Wi-Fi-wacht woord is vereist)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-03-select-wi-fi.png" alt-text="Selecteer Wi-Fi-netwerk."::: 

1. Uw IP-adres kopiëren: Zodra uw Devkit is verbonden met het netwerk van uw keuze, noteert u het **IPv4-adres** dat op de pagina wordt weer gegeven. **U hebt dit IP-adres later nodig in deze Snelstartgids**. 

    > [!NOTE]
    > Het IP-adres kan tijdens het opstarten van elk apparaat worden gewijzigd.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="IP-adres kopiëren.":::

1. Lees en accepteer de gebruiksrecht overeenkomst-Lees de gebruiksrecht overeenkomst, selecteer ik heb de **licentie overeenkomst gelezen en ga hiermee akkoord** (moet naar de onderkant van de overeenkomst schuiven) en klik op **volgende**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="OVEREENKOMST accepteren.":::

1. Maak uw SSH-aanmeldings account: Stel SSH in voor externe toegang tot uw Devkit. Maak een SSH-gebruikers naam en-wacht woord. 

    > [!NOTE]
    > SSH (Secure Shell) is een netwerk protocol dat wordt gebruikt voor beveiligde communicatie tussen het hostapparaat en de dev kit. Zie [dit artikel](https://en.wikipedia.org/wiki/SSH_(Secure_Shell))voor meer informatie over SSH.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-06-ssh-login.png" alt-text="SSH-account maken."::: 

1. Start het verbindings proces van de dev kit: Klik in het volgende scherm op **verbinding maken met een nieuw apparaat** om het proces van het verbinden van uw dev kit met Azure IOT hub te starten. 

    <!---
    Connecting with an existing IoT Edge device connection string? See this [how-to guide](<link needed>) for reference.
    --->
    :::image type="content" source="./media/quickstart-percept-dk-setup/main-07-connect-device.png" alt-text="Verbinding maken met Azure."::: 

1. De apparaatcode kopiëren: Klik op de koppeling **kopiëren** om de code van uw apparaat te kopiëren. Klik vervolgens op **Aanmelden bij Azure**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Kopieer de apparaatcode."::: 

1. De code van het apparaat plakken: er wordt een nieuw browser tabblad geopend met een venster waarin u wordt gevraagd om de gekopieerde apparaatcode. Plak de code in het venster en klik op **volgende**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Voer de code van het apparaat in."::: 

1. Aanmelden bij Azure: in het volgende venster moet u zich aanmelden met het Azure-account dat u aan het begin van de Snel starten hebt gecontroleerd. Voer de aanmeldings gegevens in en klik op **volgende**. 

    > [!NOTE]
    > Uw browser kan automatisch andere referenties in de cache opslaan. Controleer of u zich met het juiste account aanmeldt.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-10-azure-sign-in.png" alt-text="Aanmelden bij Azure.":::
 
1. Sluit het tabblad Setup-ervaring niet op deze stap-nadat u zich hebt aangemeld, wordt een scherm bevestiging weer gegeven waarin u bent aangemeld. Hoewel u het venster veel sluiten hebt, wordt u **aangeraden geen Windows-vensters te sluiten**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-11-sign-in-success.png" alt-text="Aanmelden geslaagd."::: 

1. Ga terug naar het browser tabblad dat als host fungeert voor de installatie-ervaring.
1. Selecteer uw IoT Hub optie
    - Als u al een IoT Hub hebt en deze wordt weer gegeven op deze pagina, kunt u deze selecteren en naar **stap 17 gaan**.
    - Als u geen IoT Hub hebt of als u een nieuwe wilt maken, gaat u naar de onderkant van de lijst en klikt u op **een nieuwe Azure-IOT hub maken**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Selecteer een IoT Hub."::: 

1. Maak uw nieuwe IoT Hub: Vul alle velden op deze pagina in. 
    - Selecteer het Azure-abonnement dat u wilt gebruiken met Azure percept Studio
    - Selecteer een bestaande resource groep. Als er nog geen bestaat, klikt u op nieuwe maken en volgt u de aanwijzingen. 
    - Selecteer de Azure-regio. 
    - Uw nieuwe IoT Hub een naam geven
    - De prijs categorie selecteren (deze komt meestal overeen met het abonnement)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-14-create-iot-hub.png" alt-text="Maak een nieuwe IoT Hub."::: 

1. Wacht totdat de IoT Hub geïmplementeerd is. Dit kan enkele minuten duren

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-15-iot-hub-deploy.png" alt-text="IoT Hub implementeren."::: 

1. Uw dev kit registreren: wanneer de implementatie is voltooid, klikt u op de knop **registreren**

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT Hub is geïmplementeerd."::: 

1. Geef een naam op voor de dev kit: Voer een apparaatnaam in voor de dev kit en klik op **volgende**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-17-device-name.png" alt-text="Geef het apparaat een naam."::: 

1. Wacht tot de standaard AI-modellen zijn gedownload. Dit kan enkele minuten duren

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="De installatie wordt voltooid."::: 

1. Zie Vision AI in actie-uw Devkit is gekoppeld aan uw Azure IoT Hub en heeft het standaard Vision AI-object detectie model ontvangen. De Azure percept Vision-camera kan nu een interferentie voor object detectie maken zonder verbinding met de Cloud. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-2-preview-video-output.png" alt-text="Klik op Preview video-uitvoer."::: 
       
    > [!NOTE]
    > Het voorbereidings proces en de verbinding met de Wi-Fi-toegang van het apparaat op de hostcomputer worden op dit moment uitgeschakeld, maar uw dev kit blijft verbonden met internet.   U kunt de voorbereidings ervaring opnieuw starten met het opnieuw opstarten van de dev kit, waarmee u de onboarding en het apparaat opnieuw verbindt met een andere IOT-hub die is gekoppeld aan hetzelfde of een ander Azure-abonnement.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Waarschuwing voor het instellen van de verbinding."::: 

1. Ga door naar Azure Portal. Ga terug naar het venster Setup-ervaring en klik op de knop **door gaan naar de Azure Portal** om uw aangepaste AI-modellen in azure percept Studio te maken.

    > [!NOTE]
    > Controleer of de hostcomputer niet meer is verbonden met het toegangs punt van de dev kit in uw WiFi-instellingen en of de computer nu opnieuw verbinding maakt met uw lokale WiFi.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Ga naar Azure percept Studio."::: 

## <a name="view-your-device-in-the-azure-percept-studio-and-deploy-common-prebuilt-sample-apps"></a>Uw apparaat weer geven in azure percept Studio en veelgebruikte, vooraf gebouwde voor beeld-Apps implementeren

1. Bekijk uw lijst met apparaten op de pagina overzicht van Azure percept. De overzichts pagina van Azure percept is uw start punt voor toegang tot veel verschillende werk stromen voor zowel het begin als het geavanceerde AI-rand model en de ontwikkeling van oplossingen

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="De lijst met apparaten weer geven.":::
    
1. Controleer of het apparaat dat u zojuist hebt gemaakt, is verbonden en klik erop.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Selecteer uw apparaat.":::

1. Bekijk de stream van uw apparaat om te zien wat uw ontwikkelaars Kit-camera te zien krijgt. Een standaard object detectie model wordt uit het vak geïmplementeerd en detecteert een aantal algemene objecten.

    > [!NOTE]
    > de eerste keer dat u dit doet op een nieuw apparaat, krijgt u een melding dat er een nieuwe module in de rechter bovenhoek wordt geïmplementeerd.  Zodra dit is voltooid, kunt u het venster met de camera video stream starten. 
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Bekijk uw video stroom.":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Zie object detectie.":::

1. Verken vooraf gemaakte voor beeld-AI-modellen.   Azure precept Studio heeft een aantal gemeen schappelijke vooraf gemaakte steek proeven die met één klik kunnen worden geïmplementeerd.   Selecteer een voorbeeld model implementeren om deze te bekijken en te implementeren.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Verken vooraf ontwikkelde modellen.":::
    
1. Implementeer een nieuw vooraf ontworpen voor beeld op het aangesloten apparaat. Selecteer een voor beeld in de bibliotheek en klik op implementeren op apparaat.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-1-select-prebuilt.png" alt-text="Selecteer een vooraf gemaakte.":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Zie object detectie in actie.":::

## <a name="next-steps"></a>Volgende stappen

U kunt een vergelijk bare stroom volgen om [vooraf gebouwde spraak modellen](./tutorial-no-code-speech.md)uit te proberen.