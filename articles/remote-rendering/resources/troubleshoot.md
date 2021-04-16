---
title: Problemen oplossen
description: Informatie over probleemoplossing voor Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 8f0fb9ab5c53c3fd1bfb32ac7b112a116301cba7
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575340"
---
# <a name="troubleshoot"></a>Problemen oplossen

Op deze pagina vindt u veelvoorkomende problemen Azure Remote Rendering problemen en manieren om deze op te lossen.

## <a name="cant-link-storage-account-to-arr-account"></a>Kan opslagaccount niet koppelen aan ARR-account

Soms wordt [het opslagaccount niet vermeld tijdens](../how-tos/create-an-account.md#link-storage-accounts) het koppelen Remote Rendering opslagaccount. Als u dit probleem wilt oplossen, gaat u naar het ARR-account in het Azure Portal selecteert u **Identiteit** onder **de** groep Instellingen aan de linkerkant. Zorg ervoor **dat Status** is ingesteld op **Aan.**
![Unity-frameopsporing](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>Client kan geen verbinding maken met server

Zorg ervoor dat uw firewalls (op het apparaat, binnen routers, enzovoort) de poorten die worden vermeld in de [Systeemvereisten niet blokkeren.](../overview/system-requirements.md#network-firewall)

## <a name="error-disconnected-videoformatnotavailable"></a>Fout ' `Disconnected: VideoFormatNotAvailable` ' '

Controleer of uw GPU ondersteuning biedt voor het decoderen van hardwarevideo's. Zie [Ontwikkel-pc.](../overview/system-requirements.md#development-pc)

Als u op een laptop met twee GPU's werkt, is het mogelijk dat de GPU die u standaard gebruikt, geen functionaliteit biedt voor het decoderen van hardwarevideo's. Zo ja, probeer dan af te dwingen dat uw app de andere GPU gebruikt. Dit is vaak mogelijk in de instellingen van het GPU-stuurprogramma.

## <a name="retrieve-sessionconversion-status-fails"></a>Sessie-/conversiestatus ophalen mislukt

Als REST API opdrachten te vaak worden verzonden, wordt de server beperkt en wordt uiteindelijk een fout retourneren. De HTTP-statuscode in het beperkingsgeval is 429 ('te veel aanvragen'). Als vuistregel moet er een vertraging van **5-10 seconden tussen opeenvolgende aanroepen** in acht worden genomen.

Houd er rekening mee dat deze limiet niet alleen van invloed is op de REST API aanroepen wanneer deze rechtstreeks worden aangeroepen, maar ook hun C#/C++-tegenhangers, zoals `Session.GetPropertiesAsync` `Session.RenewAsync` , of `Frontend.GetAssetConversionStatusAsync` .

Als u last hebt van beperking aan de serverzijde, wijzigt u de code om de aanroepen minder vaak uit te voeren. De server stelt de beperkingstoestand elke minuut opnieuw in, dus het is veilig om de code na een minuut opnieuw uit te voeren.

## <a name="h265-codec-not-available"></a>H265-codec niet beschikbaar

Er zijn twee redenen waarom de server kan weigeren om verbinding te maken met een `codec not available` fout.

**De H265-codec is niet geïnstalleerd:**

Zorg er eerst voor dat u de **Uitbreidingen voor HEVC-video** zoals vermeld in de [sectie Software](../overview/system-requirements.md#software) van de systeemvereisten installeert.

Als u nog steeds problemen ondervindt, moet u ervoor zorgen dat uw grafische kaart H265 ondersteunt en dat u het meest recente grafische stuurprogramma hebt geïnstalleerd. Zie de [sectie Ontwikkel-pc](../overview/system-requirements.md#development-pc) van de systeemvereisten voor leverancierspecifieke informatie.

**De codec is geïnstalleerd, maar kan niet worden gebruikt:**

De reden voor dit probleem is een onjuiste beveiligingsinstelling op de DLL's. Dit probleem doet zich niet voor bij het bekijken van video's die zijn gecodeerd met H265. Als u codec opnieuw installeert, wordt het probleem ook niet opgelost. Voer in plaats daarvan de volgende stappen uit:

1. Open een **PowerShell met beheerdersrechten en** voer uit

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Met deze opdracht moet de `InstallLocation` van de codec worden uitgevoerd, iets als:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Open die map in Windows Verkenner
1. Er moet een **x86- en** **een x64-submap** zijn. Klik met de rechtermuisknop op een van de mappen en kies **Eigenschappen**
    1. Selecteer het **tabblad Beveiliging** en klik op **de knop** Geavanceerde instellingen
    1. Klik **op Wijzigen** voor de **eigenaar**
    1. Typ **Beheerders in** het tekstveld
    1. Klik **op Namen controleren** en **OK**
1. Herhaal de bovenstaande stappen voor de andere map
1. Herhaal ook de bovenstaande stappen voor elk DLL-bestand in beide mappen. Er moeten vier DLL's zijn.

Als u wilt controleren of de instellingen nu juist zijn, doet u dit voor elk van de vier DLL's:

1. Selecteer **Eigenschappen > Beveiliging > bewerken**
1. Neem de lijst met alle **groepen/gebruikers** door en zorg ervoor dat elke groep de  juiste set **Read & Execute** heeft (het vinkje in de kolom toestaan moet worden getikt)

## <a name="low-video-quality"></a>Lage videokwaliteit

De kwaliteit van de video kan worden aangetast door de netwerkkwaliteit of de ontbrekende H265-videocodec.

* Zie de stappen voor het [identificeren van netwerkproblemen.](#unstable-holograms)
* Zie de [systeemvereisten voor](../overview/system-requirements.md#development-pc) het installeren van het meest recente grafische stuurprogramma.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Video die is opgenomen met MRC geeft de kwaliteit van de live-ervaring niet weer

Een video kan worden opgenomen op HoloLens [via Vastleggen in mixed reality (MRC).](/windows/mixed-reality/mixed-reality-capture-for-developers) De resulterende video heeft echter om twee redenen een slechtere kwaliteit dan de live-ervaring:
* De framesnelheid van de video is 30 Hz in plaats van 60 Hz.
* De videoafbeeldingen gaan niet door [de late stage reprojection](../overview/features/late-stage-reprojection.md) verwerkingsstap, dus de video lijkt lastiger te zijn.

Beide zijn inherente beperkingen van de opnametechniek.

## <a name="black-screen-after-successful-model-loading"></a>Zwart scherm na het laden van het model

Als u bent verbonden met de renderingruntime en een model hebt geladen, maar daarna alleen een zwart scherm ziet, kan dit enkele verschillende oorzaken hebben.

We raden u aan de volgende dingen te testen voordat u een uitgebreidere analyse gaat uitvoeren:

* Is de H265-codec geïnstalleerd? Hoewel er een terugval naar de H264-codec moet zijn, hebben we gezien dat deze terugval niet goed werkt. Zie de [systeemvereisten voor](../overview/system-requirements.md#development-pc) het installeren van het nieuwste grafische stuurprogramma.
* Wanneer u een Unity-project gebruikt,  sluit u Unity, verwijdert u de tijdelijke bibliotheek en *obj-mappen* in de projectmap en laadt/bouwt u het project opnieuw. In sommige gevallen werkt het voorbeeld niet goed om een voor de hand liggende reden omdat de gegevens in de cache zijn opgeslagen.

Als deze twee stappen niet helpen, is het nodig om erachter te komen of videoframes al dan niet door de client worden ontvangen. Dit kan programmatisch worden opgevraagd, zoals wordt uitgelegd in het hoofdstuk [prestatiequery's aan](../overview/features/performance-queries.md) de serverzijde. De `FrameStatistics struct` heeft een lid dat aangeeft hoeveel videoframes er zijn ontvangen. Als dit aantal groter is dan 0 en in de tijd toeneemt, ontvangt de client daadwerkelijke videoframes van de server. Daarom moet het een probleem zijn aan de clientzijde.

### <a name="common-client-side-issues"></a>Veelvoorkomende problemen aan de clientzijde

**Het model overschrijdt de limieten van de geselecteerde VM, met name het maximum aantal veelhoeken:**

Zie specifieke [limieten voor servergrootten.](../reference/limits.md#overall-number-of-polygons)

**Het model zit niet in het frustum van de camera:**

In veel gevallen wordt het model correct weergegeven, maar bevindt het zich buiten het frustum van de camera. Een veelvoorkomende reden is dat het model is geëxporteerd met een draaipunt ver buiten het midden, zodat het wordt afgekapt door het verafkapsende vlak van de camera. Het helpt om programmatisch een query uit te voeren op het begrensingsvak van het model en het vak te visualiseren met Unity als een lijnvak of de waarden ervan af te drukken in het foutopsporingslogboek.

Daarnaast genereert het conversieproces een [JSON-uitvoerbestand naast](../how-tos/conversion/get-information.md) het geconverteerde model. Als u problemen met de positie van het model wilt opsporen, is het de moeite waard om de vermelding in de `boundingBox` [sectie outputStatistics te bekijken:](../how-tos/conversion/get-information.md#the-outputstatistics-section)

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

Het begrensingsvak wordt beschreven als een `min` positie `max` in 3D-ruimte, in meters. Een coördinaat van 1000,0 betekent dus dat deze 1 kilometer van de oorsprong is verwijderd.

Er kunnen zich twee problemen met dit begrensingsvak voor doen die leiden tot een onzichtbaar geometrie:
* **De doos kan ver uit het midden zijn,** zodat het object helemaal wordt afgekapt vanwege het ver knippen van het vlak. De waarden zien er in dit geval als volgende uit: , met behulp van een grote offset op de `boundingBox` `min = [-2000, -5,-5], max = [-1990, 5,5]` x-as als voorbeeld hier. Schakel de optie in de modelconversieconfiguratie in om dit `recenterToOrigin` type probleem op te [lossen.](../how-tos/conversion/configure-model-conversion.md)
* **De doos kan worden gecentreerd, maar de grootte is te groot.** Dat betekent dat, hoewel de camera in het midden van het model begint, de geometrie in alle richtingen wordt afgekapt. Typische `boundingBox` waarden zien er in dit geval als volgende uit: `min = [-1000,-1000,-1000], max = [1000,1000,1000]` . De reden voor dit type probleem is meestal een niet-overeenkomende eenheidsschaal. Geef ter compensatie een [schaalwaarde op tijdens de conversie](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) of markeer het bronmodel met de juiste eenheden. Schalen kan ook worden toegepast op het hoofd-knooppunt bij het laden van het model tijdens runtime.

**De Render-pijplijn van Unity bevat geen render-hooks:**

Azure Remote Rendering in de Unity-render-pijplijn om het frame samen te maken met de video en om het project opnieuw uit te kunnen werken. Open het menu om te controleren of deze hooks *:::no-loc text="Window > Analysis > Frame debugger":::* bestaan. Schakel deze in en zorg ervoor dat er twee vermeldingen zijn voor `HolographicRemotingCallbackPass` de in de pijplijn:

![Unity-render-pijplijn](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>Checkerboard-patroon wordt weergegeven na het laden van het model

Als de weergegeven afbeelding er als volgende uitziet: Schermopname van een raster met ![ zwarte en witte vierkanten met het menu Extra.](../reference/media/checkerboard.png)
Vervolgens bereikt de renderer de [veelhoeklimieten voor de standaardconfiguratiegrootte](../reference/vm-sizes.md). Als u dit wilt beperken, schakelt u over **naar de Premium-configuratiegrootte** of vermindert u het aantal zichtbare veelhoeken.

## <a name="the-rendered-image-in-unity-is-upside-down"></a>De gerenderde afbeelding in Unity wordt omgedraaid

Zorg ervoor dat u de [Unity-zelfstudie: Externe modellen exact weergeven](../tutorials/unity/view-remote-models/view-remote-models.md) volgt. Een omlaag gedraaide afbeelding geeft aan dat Unity vereist is voor het maken van een renderdoel buiten het scherm. Dit gedrag wordt momenteel niet ondersteund en heeft een grote invloed op de prestaties HoloLens 2.

Redenen voor dit probleem kunnen MSAA, ISSUE of het inschakelen van naverwerking zijn. Zorg ervoor dat het profiel van lage kwaliteit is geselecteerd en als standaard is ingesteld in Unity. Ga om dit te doen *naar Projectinstellingen > bewerken... > Quality*.

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Unity-code die de Remote Rendering API gebruikt, wordt niet ge compileerd

### <a name="use-debug-when-compiling-for-unity-editor"></a>Foutopsporing gebruiken bij het compileren voor Unity Editor

Zet het *buildtype van* de Unity-oplossing op **Fouten opsporen.** Bij het testen van ARR in de Unity-editor is de definitie `UNITY_EDITOR` alleen beschikbaar in builds met foutopsporing. Houd er rekening mee dat dit niet gerelateerd is aan het buildtype dat wordt gebruikt voor geïmplementeerde [toepassingen,](../quickstarts/deploy-to-hololens.md)waarbij u de voorkeur geeft aan 'Release'-builds.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Compilatiefouten bij het compileren van Unity-voorbeelden voor HoloLens 2

We hebben fouten gezien bij het compileren van Unity-voorbeelden (quickstart, ShowCaseApp, ..) voor HoloLens 2. Visual Studio over het niet kunnen kopiëren van sommige bestanden, ook al zijn ze er wel. Als dit probleem zich voordeed:
* Verwijder alle tijdelijke Unity-bestanden uit het project en probeer het opnieuw. Dat wil zeggen, sluit Unity, verwijder de tijdelijke *bibliotheek* en *obj-mappen* in de projectmap en laad/bouw het project opnieuw.
* Zorg ervoor dat de projecten zich bevinden in een map op schijf met een redelijk kort pad, omdat de kopieerstap soms problemen lijkt te hebben met lange bestandsnamen.
* Als dat niet helpt, kan het zijn dat MS Sense de kopieerstap verstoort. Als u een uitzondering wilt instellen, moet u deze registeropdracht uitvoeren vanaf de opdrachtregel (hiervoor zijn beheerdersrechten vereist):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>Arm64-builds voor Unity-projecten mislukken omdat AudioPluginMsHRTF.dll ontbreekt

De `AudioPluginMsHRTF.dll` voor Arm64 is toegevoegd aan het *Windows Mixed Reality-pakket* *(com.unity.xr.windowsmr.metro)* in versie 3.0.1. Zorg ervoor dat versie 3.0.1 of hoger is geïnstalleerd via de Unity-Pakketbeheer. Navigeer in de Unity-menubalk naar *Window > Pakketbeheer* zoek naar *het Windows Mixed Reality* pakket.

## <a name="native-c-based-application-does-not-compile"></a>De native C++-toepassing wordt niet ge compileerd

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>Fout 'Bibliotheek niet gevonden' voor UWP-toepassing of DLL

In het C++ NuGet-pakket is er een bestand dat definieert welke binaire `microsoft.azure.remoterendering.Cpp.targets` smaak moet worden gebruikt. Om te `UWP` identificeren, controleert u de voorwaarden in het bestand op `ApplicationType == 'Windows Store'` . Er moet dus voor worden gezorgd dat dit type is ingesteld in het project. Dat moet het geval zijn bij het maken van een UWP-toepassing of DLL via Visual Studio van de projectwizard van uw toepassing.

## <a name="unstable-holograms"></a>Instabiele hologrammen

Als gerenderde objecten samen met hoofdbewegingen lijken te bewegen, kunnen er problemen zijn met Late *Stage Reprojection* (LSR). Raadpleeg de sectie over [late-fase-reprojectie](../overview/features/late-stage-reprojection.md) voor hulp bij het aanpakken van een dergelijke situatie.

Een andere reden voor instabiele hologrammen (wiebelen, wiebelen, jittering of jump hologrammen) kan een slechte netwerkverbinding zijn, met name onvoldoende netwerkbandbreedte of een te hoge latentie. Een goede indicator voor de kwaliteit van uw netwerkverbinding is de waarde [voor prestatiestatistieken.](../overview/features/performance-queries.md) `ServiceStatistics.VideoFramesReused` Hergebruikte frames geven situaties aan waarin een oud videoframe opnieuw moest worden gebruikt aan de clientzijde omdat er geen nieuw videoframe beschikbaar was, bijvoorbeeld vanwege pakketverlies of vanwege variaties in netwerklatentie. Als `ServiceStatistics.VideoFramesReused` vaak groter is dan nul, duidt dit op een netwerkprobleem.

Een andere waarde om naar te kijken is `ServiceStatistics.LatencyPoseToReceiveAvg` . Deze moet consistent lager zijn dan 100 ms. Als u hogere waarden ziet, kan dit erop wijzen dat u bent verbonden met een datacenter dat te ver weg is.

Zie de richtlijnen voor netwerkconnectiviteit voor een lijst [met mogelijke oplossingen.](../reference/network-requirements.md#guidelines-for-network-connectivity)

## <a name="z-fighting"></a>Z-ing

Hoewel ARR [beperkingsfunctionaliteit voor z-beperking](../overview/features/z-fighting-mitigation.md)biedt, kan z-ing nog steeds in de scène worden aangetroffen. Deze handleiding is gericht op het oplossen van deze resterende problemen.

### <a name="recommended-steps"></a>Aanbevolen stappen

Gebruik de volgende werkstroom om z-strijd te beperken:

1. Test de scène met de standaardinstellingen van ARR (z-mitigation mitigation on)

1. Beperking van z-beperking uitschakelen via de [API](../overview/features/z-fighting-mitigation.md) 

1. De camera in de buurt en ver van het vlak naar een dichter bereik wijzigen

1. Problemen met de scène oplossen via de volgende sectie

### <a name="investigating-remaining-z-fighting"></a>Resterende z-ing onderzoeken

Als de bovenstaande stappen zijn uitgeput en de resterende z-strijding onacceptabel is, moet de onderliggende oorzaak van de z-strijd worden onderzocht. Zoals vermeld op de pagina [z-mitigation](../overview/features/z-fighting-mitigation.md)mitigatiefunctie, zijn er twee belangrijke redenen voor z-ing: diepte precisieverlies aan het eind van het dieptebereik en oppervlakken die elkaar kruisen terwijl ze coplanar zijn. Verlies van diepteprecisie is een wiskundig mogelijkheid en kan alleen worden vereenigd door de bovenstaande stap 3 te volgen. Coplanaire oppervlak duiden op een fout in de bron-asset en zijn beter vast in de brongegevens.

ARR heeft een functie om te bepalen of er met oppervlak kan worden gedreed: [Checkerboard markeren.](../overview/features/z-fighting-mitigation.md) U kunt ook visueel bepalen wat de oorzaak is van de z-strijd. De volgende eerste animatie toont een voorbeeld van diepte-precisieverlies in de afstand en de tweede animatie toont een voorbeeld van bijna coplanaire oppervlakten:

![Animatie van een voorbeeld van diepte-precisieverlies in de afstand.](./media/depth-precision-z-fighting.gif)  ![Animatie van een voorbeeld van bijna coplanaire oppervlakten.](./media/coplanar-z-fighting.gif)

Vergelijk deze voorbeelden met uw z-ing om de oorzaak te bepalen of volg eventueel deze stapsgewijs werkstroom:

1. Plaats de camera boven de z-oppervlakken om rechtstreeks naar het oppervlak te kijken.
1. Beweeg de camera langzaam naar achteren, weg van de oppervlakken.
1. Als de z-heid steeds zichtbaar is, zijn de oppervlakken perfect coplanar. 
1. Als de z-heid het grootste deel van de tijd zichtbaar is, zijn de oppervlakken bijna coplanar.
1. Als de z-ing alleen van ver weg zichtbaar is, is de oorzaak een gebrek aan diepteprecisie.

Coplanarvlakken kunnen een aantal verschillende oorzaken hebben:

* Een object is gedupliceerd door de exporttoepassing vanwege een fout of verschillende werkstroommethoden.

    Controleer deze problemen met de respectieve toepassings- en toepassingsondersteuning.

* Surfaces worden gedupliceerd en gespiegeld om dubbelzijd te worden weergegeven in renderers die gebruikmaken van front-face- of back-face-verering.

    Importeren via de [modelconversie bepaalt](../how-tos/conversion/model-conversion.md) de principal sidedness van het model. Er wordt uitgegaan van dubbelzijdheid als de standaardinstelling. Het oppervlak wordt weergegeven als een thin wall met fysiek juiste belichting aan beide zijden. Eenzijdige zijde kan worden geïmpliceerd door vlaggen in de bronactivum of expliciet geforceerd tijdens de [modelconversie](../how-tos/conversion/model-conversion.md). Bovendien, maar optioneel, kan [de modus met](../overview/features/single-sided-rendering.md) één zijde worden ingesteld op 'normaal'.

* Objecten worden door elkaar gewisseld in de bronactiva.

     Objecten die zijn getransformeerd op een manier die sommige van hun oppervlakken overlappen, zorgen ook voor z-ing. Het transformeren van delen van de scènestructuur in de geïmporteerde scène in ARR kan dit probleem ook doen.

* Oppervlakken zijn doelbewust geschreven om aan te raken, zoals decals of tekst op muren.

## <a name="graphics-artifacts-using-multi-pass-stereo-rendering-in-native-c-apps"></a>Grafische artefacten met multi-pass stereo rendering in native C++ apps

In sommige gevallen kunnen aangepaste, native C++-apps die gebruikmaken van een multi-pass stereo rendering-modus voor lokale inhoud (rendering naar het linker- en rechteroog in afzonderlijke passes) na het aanroepen van [**HebttRemoteFrame**](../concepts/graphics-bindings.md#render-remote-image) een stuurprogramma-fout activeren. De fout resulteert in niet-deterministische rasteriseringsstoringen, waardoor afzonderlijke driehoeken of delen van driehoeken van de lokale inhoud willekeurig verdwijnen. Uit prestatieoverwegingen wordt het toch aanbevolen om lokale inhoud weer te geven met een modernere single-pass stereo rendering-techniek, bijvoorbeeld met **behulp van SV_RenderTargetArrayIndex**.

## <a name="conversion-file-download-errors"></a>Downloadfouten conversiebestand

De Conversieservice kan fouten ondervinden bij het downloaden van bestanden uit blobopslag vanwege padlengtelimieten die door Windows en de service zijn opgelegd. Bestandspaden en bestandsnamen in uw blobopslag mogen niet langer zijn dan 178 tekens. Bijvoorbeeld bij een `blobPrefix` van `models/Assets` 13 tekens:

`models/Assets/<any file or folder path greater than 164 characters will fail the conversion>`

De Conversieservice downloadt alle bestanden die zijn opgegeven onder `blobPrefix` de , niet alleen de bestanden die worden gebruikt in de conversie. De bestanden/map die problemen veroorzaken, zijn in dergelijke gevallen mogelijk minder duidelijk. Het is dus belangrijk om alles in het opslagaccount onder te `blobPrefix` controleren. Zie de onderstaande voorbeeldinvoeren voor wat er wordt gedownload.
``` json
{
  "settings": {
    "inputLocation": {
      "storageContainerUri": "https://contosostorage01.blob.core.windows.net/arrInput",
      "blobPrefix": "models/Assets",
      "relativeInputAssetPath": "myAsset.fbx"
    ...
  }
}
```

```
models
├───Assets
│   │   myAsset.fbx                 <- Asset
│   │
│   └───Textures
│   |       myTexture.png           <- Used in conversion
│   |
|   └───MyFiles
|          myOtherFile.txt          <- File also downloaded under blobPrefix      
|           
└───OtherFiles
        myReallyLongFileName.txt    <- Ignores files not under blobPrefix             
```
## <a name="next-steps"></a>Volgende stappen

* [Systeemvereisten](../overview/system-requirements.md)
* [Netwerkvereisten](../reference/network-requirements.md)