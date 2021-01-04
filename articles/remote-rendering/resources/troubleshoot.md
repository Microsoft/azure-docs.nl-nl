---
title: Problemen oplossen
description: Probleemoplossings informatie voor de externe rendering van Azure
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 34a947a2a0f6d8c87c0580f273130b671b4f17fc
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97722229"
---
# <a name="troubleshoot"></a>Problemen oplossen

Op deze pagina vindt u veelvoorkomende problemen met betrekking tot de externe rendering van Azure en manieren om deze op te lossen.

## <a name="cant-link-storage-account-to-arr-account"></a>Kan opslagaccount niet koppelen aan ARR-account

Soms wordt tijdens het [koppelen van een opslag account](../how-tos/create-an-account.md#link-storage-accounts) het externe rendering-account niet weer gegeven. Als u dit probleem wilt oplossen, gaat u naar het ARR-account in de Azure Portal en selecteert u **identiteit** in de groep **instellingen** aan de linkerkant. Zorg ervoor dat de **status** is ingesteld op **aan**.
![Unity frame debugger](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>Client kan geen verbinding maken met de server

Zorg ervoor dat de firewalls (op het apparaat, binnen routers, enzovoort) de poorten die worden vermeld in de [systeem vereisten](../overview/system-requirements.md#network-firewall), niet blok keren.

## <a name="error-disconnected-videoformatnotavailable"></a>Fout ' `Disconnected: VideoFormatNotAvailable` '

Controleer of uw GPU hardware-video-decodering ondersteunt. Zie [ontwikkel-PC](../overview/system-requirements.md#development-pc).

Als u werkt met een laptop met twee Gpu's, is het mogelijk dat de GPU waarop u uitvoert standaard niet de functionaliteit voor het decoderen van hardware-video biedt. Als dit het geval is, probeert u uw app te dwingen de andere GPU te gebruiken. Dit is vaak mogelijk in de instellingen van het GPU-stuur programma.

## <a name="retrieve-sessionconversion-status-fails"></a>Status van ophalen van sessie/conversie mislukt

Het te vaak verzenden van REST API opdrachten leidt ertoe dat de server uiteindelijk kan worden vertraagd en geretourneerd. De HTTP-status code in de beperkings case is 429 ("te veel aanvragen"). Als vuistregel moet er een vertraging van **5-10 seconden tussen opeenvolgende aanroepen** in acht worden genomen.

Houd er rekening mee dat deze limiet niet alleen van invloed is op de REST API-aanroepen wanneer deze rechtstreeks worden aangeroepen, maar ook de bijbehorende C#/C + +-equivalenten, zoals `Session.GetPropertiesAsync` , `Session.RenewAsync` of `Frontend.GetAssetConversionStatusAsync` .

Als u beperkingen aan de server zijde ondervindt, wijzigt u de code zodat de aanroepen minder vaak worden uitgevoerd. De server zal de vertragings status elke minuut opnieuw instellen, zodat de code na een minuut veilig kan worden uitgevoerd.

## <a name="h265-codec-not-available"></a>De H265-codec is niet beschikbaar

Er zijn twee redenen waarom de server geen verbinding kan maken met een `codec not available` fout.

**De H265-codec is niet geïnstalleerd:**

Zorg er eerst voor dat u de **HEVC-video-uitbrei dingen** installeert zoals vermeld in de sectie [Software](../overview/system-requirements.md#software) van de systeem vereisten.

Als u nog steeds problemen ondervindt, moet u ervoor zorgen dat uw grafische kaart ondersteuning biedt voor H265 en dat het nieuwste grafische stuur programma is geïnstalleerd. Zie de sectie [ontwikkel-PC](../overview/system-requirements.md#development-pc) van de systeem vereisten voor leverancierspecifieke informatie.

**De codec is geïnstalleerd, maar kan niet worden gebruikt:**

De reden voor dit probleem is een onjuiste beveiligings instelling voor de Dll's. Dit probleem treedt niet op bij het bekijken van Video's die zijn gecodeerd met H265. Als u de codec opnieuw installeert, wordt het probleem niet opgelost. Voer in plaats daarvan de volgende stappen uit:

1. Open een **Power shell met beheerders rechten** en voer uit

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Met deze opdracht moet de `InstallLocation` van de codec worden uitgevoerd, ongeveer als volgt:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Deze map openen in Windows Verkenner
1. Er moet een **x86** -en een **x64** -submap zijn. Klik met de rechter muisknop op een van de mappen en kies **Eigenschappen**
    1. Selecteer het tabblad **beveiliging** en klik op de knop **Geavanceerde** instellingen.
    1. Klik op **wijzigen** voor de **eigenaar**
    1. Typ **beheerders** in het tekst veld
    1. Klik op **Namen controleren** en vervolgens op **OK**
1. Herhaal de bovenstaande stappen voor de andere map
1. Herhaal ook de bovenstaande stappen voor elk DLL-bestand in beide mappen. Er moeten vier dll-bestanden aanwezig zijn.

Ga als volgt te werk om te controleren of de instellingen nu juist zijn, voor elk van de vier Dll's:

1. **Eigenschappen > beveiliging > bewerken** selecteren
1. Door loop de lijst met alle **groepen/gebruikers** en zorg ervoor dat elke groep de machtiging **lezen & uitvoeren** is ingesteld (het vinkje in de kolom **toestaan** moet worden getickd)

## <a name="low-video-quality"></a>Lage video kwaliteit

De video kwaliteit kan worden aangetast door netwerk kwaliteit of de ontbrekende H265-videocodec.

* Zie de stappen om [netwerk problemen te identificeren](#unstable-holograms).
* Zie de [systeem vereisten](../overview/system-requirements.md#development-pc) voor het installeren van het nieuwste grafische stuur programma.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Video die is opgenomen in MRC weerspiegelt niet de kwaliteit van de Live-ervaring

Een video kan worden vastgelegd op HoloLens via [Mixed Reality Capture (MRC)](/windows/mixed-reality/mixed-reality-capture-for-developers). De resulterende video heeft echter een slechterere kwaliteit dan de Live-ervaring om twee redenen:
* De video weergave snelheid wordt beperkt tot 30 Hz, in tegens telling tot 60 Hz.
* De video-installatie kopieën worden niet door de verwerkings stap voor het [vertraagd](../overview/features/late-stage-reprojection.md) verwerken van taken uitgevoerd, dus de video lijkt choppier te zijn.

Beide zijn inherente beperkingen van de opname techniek.

## <a name="black-screen-after-successful-model-loading"></a>Zwart scherm nadat het laden van het model is geslaagd

Als u verbonden bent met de rendering-runtime en een model hebt geladen, maar er een zwart scherm wordt weer gegeven, kan dit een aantal verschillende oorzaken hebben.

Het is raadzaam om de volgende dingen te testen voordat u een uitgebreidere analyse uitvoert:

* Is de H265-codec geïnstalleerd? Hoewel er sprake is van een terugval van de H264-codec, hebben we gevallen gezien waarin deze terugval niet goed werkt. Zie de [systeem vereisten](../overview/system-requirements.md#development-pc) voor het installeren van het nieuwste grafische stuur programma.
* Wanneer u een unit-project gebruikt, sluit eenheid, verwijdert u de map tijdelijke *bibliotheek* en *obj* in de projectmap en laadt u het project opnieuw en bouwt u het opnieuw. In sommige gevallen hebben gegevens in de cache het voor beeld niet goed functioneren om een duidelijke reden.

Als deze twee stappen niet helpen, is het nodig om erachter te komen of video frames zijn ontvangen door de client of niet. Dit kan via een programma worden opgevraagd, zoals wordt uitgelegd in het hoofd stuk [server-side performance query's](../overview/features/performance-queries.md) . De `FrameStatistics struct` bevat een lid dat aangeeft hoeveel video frames zijn ontvangen. Als dit aantal groter is dan 0 en de tijd toeneemt, ontvangt de client daad werkelijke video frames van de server. Daarom moet het een probleem zijn aan de client zijde.

### <a name="common-client-side-issues"></a>Veelvoorkomende problemen aan de client zijde

**Het model overschrijdt de limieten van de geselecteerde virtuele machine, met name het maximum aantal veelhoeken:**

Zie specifieke [limieten voor Server grootte](../reference/limits.md#overall-number-of-polygons).

**Het model bevindt zich niet in de camera frustum:**

In veel gevallen wordt het model correct weer gegeven, maar buiten de camera frustum. Een veelvoorkomende reden is dat het model is geëxporteerd met een uit de weg geplaatste draaiing, zodat het wordt geknipt door het ver knip vlak van de camera. Het helpt het model in het kader van een programma op te vragen en het vak met eenheid als een lijn vak te visualiseren of de waarden in het logboek voor fout opsporing af te drukken.

Bovendien genereert het conversie proces een [JSON-uitvoer bestand](../how-tos/conversion/get-information.md) naast het geconverteerde model. Als u fouten wilt opsporen in model positionering, is het een goed idee om de `boundingBox` vermelding in de [sectie outputStatistics](../how-tos/conversion/get-information.md#the-outputstatistics-section)te bekijken:

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

Het selectie kader wordt beschreven als een `min` en `max` -positie in 3D-ruimte, uitgedrukt in meters. Een coördinaat van 1000,0 betekent dus 1 kilo meter weg van de oorsprong.

Er kunnen twee problemen zijn met dit selectie kader die leiden tot onzichtbare geometrie:
* **Het vak kan niet uit het midden worden bevinden**, zodat het object volledig is afgekapt vanwege de meeste knip knippen. De `boundingBox` waarden in dit geval zien er als volgt uit: `min = [-2000, -5,-5], max = [-1990, 5,5]` , met behulp van een grote offset op de x-as als hier een voor beeld. Schakel de `recenterToOrigin` optie in de configuratie van de [model conversie](../how-tos/conversion/configure-model-conversion.md)in om dit type probleem op te lossen.
* **Het vak kan worden gecentreerd, maar de volg orde van de grootte is te groot**. Dit betekent dat hoewel de camera wordt gestart in het midden van het model, de geometrie ervan in alle richtingen is afgekapt. Typische `boundingBox` waarden in dit geval zien er als volgt uit: `min = [-1000,-1000,-1000], max = [1000,1000,1000]` . De reden voor dit type probleem is doorgaans een niet-overeenkomende eenheids schaal. Als u wilt compenseren, geeft u een [schaal waarde op tijdens de conversie](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) of markeert u het bron model met de juiste eenheden. Schalen kan ook worden toegepast op het hoofd knooppunt wanneer het model tijdens runtime wordt geladen.

**De unit weergave-pijp lijn bevat geen render-hooks:**

Azure remote rendering hooks in de unit weergave-pijp lijn voor het samen stellen van de kader samenstelling met de video en om de herprojectie uit te voeren. Als u wilt controleren of deze hooks bestaan, opent u het menu *:::no-loc text="Window > Analysis > Frame debugger":::* . Schakel dit in en zorg ervoor dat er twee vermeldingen zijn voor de `HolographicRemotingCallbackPass` in de pijp lijn:

![Unit weergave-pijp lijn](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>Dambord patroon wordt weer gegeven na laden van model

Als de gerenderde afbeelding er als volgt uitziet: ![ scherm opname toont een raster met zwarte en witte vier kanten met een extra menu.](../reference/media/checkerboard.png)
vervolgens krijgt de renderer de [grenzen van de polygoon voor de standaard configuratie grootte](../reference/vm-sizes.md). U kunt dit oplossen door over te scha kelen naar de grootte van **Premium** -configuraties of het aantal zicht bare veelhoeken te verlagen.

## <a name="the-rendered-image-in-unity-is-upside-down"></a>De gerenderde afbeelding in unit is ondersteboven

Zorg ervoor dat u de [eenheids handleiding volgt: Geef de externe modellen precies weer](../tutorials/unity/view-remote-models/view-remote-models.md) . Een afbeelding ondersteboven geeft aan dat er een eenheid is vereist voor het maken van een niet-scherm weergave doel. Dit gedrag wordt momenteel niet ondersteund en maakt een enorme prestatie-impact op HoloLens 2.

Oorzaken van dit probleem kunnen MSAA, HDR of het inschakelen van post verwerking zijn. Zorg ervoor dat het profiel met lage kwaliteit is geselecteerd en als standaard waarde is ingesteld in de eenheid. Ga hiervoor naar *> project instellingen bewerken... > kwaliteit*.

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Unit code met behulp van de remote rendering API wordt niet gecompileerd

### <a name="use-debug-when-compiling-for-unity-editor"></a>Fout opsporing gebruiken bij het compileren van Unity editor

Schakel het *type Build* van unit Solution in op **debug**. Bij het testen van ARR in de eenheids editor is de definitie `UNITY_EDITOR` alleen beschikbaar in builds voor fout opsporing. Houd er rekening mee dat dit niet gerelateerd is aan het build-type dat wordt gebruikt voor [geïmplementeerde toepassingen](../quickstarts/deploy-to-hololens.md), waarbij u de voor keur geeft aan release-builds.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Fouten compileren bij het compileren van unit-samples voor HoloLens 2

Er zijn onlogische fouten gedetecteerd bij het compileren van unit-voor beelden (Quick Start, ShowCaseApp,..) voor HoloLens 2. Visual Studio klagen over het al dan niet kunnen kopiëren van bestanden, maar ze zijn er wel. Als u dit probleem ondervindt:
* Verwijder alle tijdelijke unit-bestanden uit het project en probeer het opnieuw. Dat wil zeggen, sluit eenheid, verwijder de tijdelijke *bibliotheek* en *obj* -mappen in de projectmap en laad/bouw het project opnieuw.
* Zorg ervoor dat de projecten zich in een map op schijf bevinden met redelijk korte pad, omdat de Kopieer stap soms problemen met lange bestands namen ondervindt.
* Als dat niet helpt, kan het zijn dat MS Sense de Kopieer stap verstoort. Als u een uitzonde ring wilt instellen, voert u deze register opdracht uit vanaf de opdracht regel (hiervoor zijn beheerders rechten vereist):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>Arm64-builds voor Unity-projecten mislukken omdat AudioPluginMsHRTF.dll ontbreekt

De `AudioPluginMsHRTF.dll` for-Arm64 is toegevoegd aan het *Windows Mixed Reality* -pakket *(com. unit. XR. windowsmr. metro)* in versie 3.0.1. Zorg ervoor dat u versie 3.0.1 of hoger hebt geïnstalleerd via unit package manager. Navigeer in de menu balk Unit naar *Window > package manager* en zoek naar het *Windows Mixed Reality* -pakket.

## <a name="native-c-based-application-does-not-compile"></a>Op systeem eigen C++ gebaseerde toepassing wordt niet gecompileerd

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>Fout ' bibliotheek niet gevonden ' voor UWP-toepassing of-dll

In het C++ NuGet-pakket bevindt zich een bestands `microsoft.azure.remoterendering.Cpp.targets` bestand dat definieert welke binaire waarde moet worden gebruikt. Voor `UWP` het identificeren van de voor waarden in het bestand controleren op `ApplicationType == 'Windows Store'` . Daarom moet worden gegarandeerd dat dit type is ingesteld in het project. Dit moet het geval zijn bij het maken van een UWP-toepassing of-dll via de project wizard van Visual Studio.

## <a name="unstable-holograms"></a>Onstabiele hologrammen

In het geval dat gerenderde objecten samen met de hoofd bewegingen worden verplaatst, kunnen er problemen optreden met de *vertraagde fase* van het project (lsr). Raadpleeg de sectie over [vertraagde fase](../overview/features/late-stage-reprojection.md) ring van het project voor richt lijnen voor het aanpaken van een dergelijke situatie.

Een andere reden voor onstabiele hologrammen (Wobbling, krom trekken, jitteren of springende Holograms) kan een slechte netwerk verbinding zijn, met name onvoldoende netwerk bandbreedte of te hoge latentie. Een goede indicator voor de kwaliteit van uw netwerk verbinding is de waarde voor [prestatie statistieken](../overview/features/performance-queries.md) `ARRServiceStats.VideoFramesReused` . Hergebruikte frames geven situaties aan waarbij een oud video frame moet worden hergebruikt aan de client zijde omdat er geen nieuw video frame beschikbaar was, bijvoorbeeld vanwege pakket verlies of vanwege afwijkingen in de netwerk latentie. Als `ARRServiceStats.VideoFramesReused` het regel matig groter is dan nul, duidt dit op een netwerk probleem.

U kunt ook een andere waarde zoeken in `ARRServiceStats.LatencyPoseToReceiveAvg` . Deze moet consistent zijn onder 100 MS. Als u hogere waarden ziet, kan dit erop wijzen dat u verbonden bent met een Data Center dat te ver weg is.

Zie de [richt lijnen voor netwerk connectiviteit](../reference/network-requirements.md#guidelines-for-network-connectivity)voor een lijst met mogelijke oplossingen.

## <a name="z-fighting"></a>Z-vechten

Terwijl ARR een [z-bestrij ding van de beperkende functionaliteit](../overview/features/z-fighting-mitigation.md)biedt, kan z-vechten nog steeds in de scène worden weer gegeven. Deze hand leiding is gericht op het oplossen van deze resterende problemen.

### <a name="recommended-steps"></a>Aanbevolen stappen

Gebruik de volgende werk stroom om de z-vechten te beperken:

1. Test de scène met de standaard instellingen van ARR (z-bestrij ding van problemen op)

1. De z-bestrij ding van de beperking via de [API](../overview/features/z-fighting-mitigation.md) uitschakelen 

1. Wijzig de camera bijna en Far in een dichterd bereik

1. Problemen met de scène oplossen via de volgende sectie

### <a name="investigating-remaining-z-fighting"></a>De resterende z-bestrijding wordt onderzocht

Als de bovenstaande stappen zijn uitgeput en de resterende z-vechten onaanvaardbaar is, moet de onderliggende oorzaak van de z-vechten worden onderzocht. Zoals vermeld op de [functie pagina z-bestrij](../overview/features/z-fighting-mitigation.md)ding van Risico's, zijn er twee belang rijke redenen voor z-vechten: diepte nauwkeurigheid aan het einde van het diepte bereik en de Opper vlakken die elkaar Intersect tijdens het coplanar. Het verlies van nauw keurigheid is een mathematisch risico en kan alleen worden verholpen door stap 3 hierboven te volgen. Coplanar-Opper vlakken geven een probleem met de bron activa aan en zijn beter in de bron gegevens opgelost.

ARR heeft een functie waarmee kan worden bepaald of Opper vlakken z-bestrij ding van een [dambord patroon](../overview/features/z-fighting-mitigation.md)zijn. U kunt ook visueel bepalen wat het voor de z-vechten veroorzaakt. In de volgende eerste animatie ziet u een voor beeld van een diepte nauwkeurigheids verlies op de afstand en de tweede een voor beeld van bijna coplanar-Opper vlakken:

![Animatie toont een voor beeld van het verlies van nauw keurigheid op afstand.](./media/depth-precision-z-fighting.gif)  ![Animatie toont een voor beeld van bijna coplanar-Opper vlakken.](./media/coplanar-z-fighting.gif)

Vergelijk deze voor beelden met uw z-vechten om de oorzaak te bepalen of voer deze stapsgewijze werk stroom eventueel uit:

1. Plaats de camera boven de z-vecht oppervlakken om direct op het Opper vlak te kijken.
1. Verplaats de camera langzaam naar achteren, weg van de Opper vlakken.
1. Als de z-vechten al zo lang zichtbaar is, zijn de Opper vlakken perfect coplanar. 
1. Als de z-vechten de meeste tijd zichtbaar is, zijn de Opper vlakken bijna coplanar.
1. Als de z-vechten alleen van ver zichtbaar is, is de oorzaak geen nauw keurige diepte.

Coplanar-Opper vlakken kunnen een aantal verschillende oorzaken hebben:

* Er is een object gedupliceerd door de export toepassing vanwege een fout of verschillende werk stroom benaderingen.

    Controleer deze problemen met de betreffende toepassings-en toepassings ondersteuning.

* De Opper vlakken worden gedupliceerd en gespiegeld zodat ze dubbelzijdig worden weer gegeven in renderers die gebruikmaken van front-face of Back-face.

    Importeren via de [model conversie](../how-tos/conversion/model-conversion.md) bepaalt de principal-sidedness van het model. Dubbele sidedness wordt aangenomen als de standaard waarde. Het Opper vlak wordt weer gegeven als een dunne wand met een fysiek juiste verlichting van beide zijden. Eén sidedness kan worden geïmpliceerd door vlaggen in het bron activum of expliciet worden geforceerd tijdens de [model conversie](../how-tos/conversion/model-conversion.md). Daarnaast kan de [Single-Side modus](../overview/features/single-sided-rendering.md) worden ingesteld op ' normaal '.

* Objecten die elkaar kruisen in de bron assets.

     Objecten die zijn getransformeerd op een manier waarop sommige van hun Opper vlakken elkaar overlappen, maken ook een z-vechten. Als u delen van de scène structuur in de geïmporteerde scène in ARR transformeert, kan dit probleem ook worden gemaakt.

* Opper vlakken zijn purposefully ontworpen om te raken, zoals Decals of tekst op wanden.

## <a name="graphics-artifacts-using-multi-pass-stereo-rendering-in-native-c-apps"></a>Grafische artefacten die gebruikmaken van stereo rendering met meerdere slagen in systeem eigen C++-apps

In sommige gevallen worden aangepaste systeem eigen C++-apps die gebruikmaken van een stereo rendering-modus met meerdere slagen voor lokale inhoud (weer gave naar links en rechts in afzonderlijke fasen) na het aanroepen van [**BlitRemoteFrame**](../concepts/graphics-bindings.md#render-remote-image) , een stuurprogrammafout kunnen activeren. De fout resulteert in niet-deterministische raster storingen, waardoor afzonderlijke drie hoeken of delen van drie hoeken van de lokale inhoud wille keurig verdwijnen. Uit prestatie overwegingen verdient het de voor keur om lokale inhoud te renderen met een moderne stereo weergave techniek met één stap, bijvoorbeeld met behulp van **SV_RenderTargetArrayIndex**.

## <a name="next-steps"></a>Volgende stappen

* [Systeemvereisten](../overview/system-requirements.md)
* [Netwerkvereisten](../reference/network-requirements.md)