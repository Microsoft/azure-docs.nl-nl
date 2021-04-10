---
title: Advertenties invoegen aan de client zijde | Microsoft Docs
description: In dit artikel wordt beschreven hoe u advertenties kunt invoegen in uw media aan de client zijde.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 2d942099d0abbdfc4ddfa0276184500166250728
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014953"
---
# <a name="inserting-ads-on-the-client-side"></a>Advertenties invoegen aan de client zijde

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Dit artikel bevat informatie over het invoegen van verschillende soorten advertenties aan de client zijde.

Voor informatie over ondertiteling en AD-ondersteuning in live streaming Video's, Zie [ondersteunde ondertiteling en normen voor AD-invoeging](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Azure Media Player biedt momenteel geen ondersteuning voor advertenties.
> 
> 

## <a name="inserting-ads-into-your-media"></a><a id="insert_ads_into_media"></a>Advertenties invoegen in uw media
Azure Media Services biedt ondersteuning voor AD-invoeging via het Windows Media-platform: Player frameworks. Er zijn Windows 8-, Silverlight-, Windows Phone 8-en iOS-apparaten beschikbaar voor de service kaders met AD-ondersteuning. Elk Framework van de speler bevat voorbeeld code die laat zien hoe u een speler-toepassing implementeert. Er zijn drie verschillende soorten advertenties die u kunt invoegen in uw media: lijst.

* **Lineair** : volledige frame advertenties die de hoofd video pauzeren.
* Niet- **lineaire** , overlay-advertenties die worden weer gegeven als de hoofd video wordt afgespeeld, meestal een logo of een andere statische afbeelding die in de speler is geplaatst.
* **Companion** : advertenties die buiten de speler worden weer gegeven.

Advertenties kunnen op elk punt in de tijd lijn van de hoofd video worden geplaatst. U moet de speler vertellen wanneer u de AD wilt afspelen en welke advertenties moeten worden afgespeeld. Dit wordt gedaan met behulp van een set standaard XML-bestanden: video ad service Temp late (VAST), Digital Video multiple AD Play List (VMAP), media abstract sequencing Temp late (MAST) en Digital Video Player AD Interface definition (VPAID). Met grote bestanden kunt u opgeven welke advertenties moeten worden weer gegeven. VMAP-bestanden geven aan wanneer verschillende advertenties moeten worden afgespeeld en die grote XML bevatten. MAST-bestanden zijn een andere manier om advertenties aan te bieden die ook grote XML kunnen bevatten. VPAID-bestanden definiëren een interface tussen de video speler en de AD-of ad-server.

Elk Framework van de speler werkt anders en wordt elk behandeld in een eigen artikel. In dit artikel worden de basis mechanismen beschreven die worden gebruikt voor het invoegen van advertenties. Video speler-toepassingen vragen Ads van een ad-server. De ad-server kan op een aantal manieren reageren:

* Een VAST bestand retour neren
* Een VMAP-bestand retour neren (met Inge sloten vaste)
* Een MAST-bestand retour neren (met Inge sloten vaste)
* Een VAST bestand retour neren met VPAID Ads

### <a name="using-a-video-ad-service-template-vast-file"></a>Een bestand met een video ad-service sjabloon (VAST) gebruiken
Een VAST bestand geeft aan welke AD of advertenties moeten worden weer gegeven. De volgende XML is een voor beeld van een VAST bestand voor een lineaire AD:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
```

De lineaire advertentie wordt beschreven in het element <**lineaire**>. Hiermee geeft u de duur van de AD op, gevolgd door gebeurtenissen, klikt u op bijhouden en een aantal **MediaFile** -elementen. Tracerings gebeurtenissen worden opgegeven in het **TrackingEvents**>-element van <en kunnen een ad-server bij het volgen van verschillende gebeurtenissen die optreden tijdens het weer geven van de AD. In dit geval worden de gebeurtenissen start, midden, volt ooien en uitvouwen bijgehouden. De gebeurtenis starten vindt plaats wanneer de AD wordt weer gegeven. De punt gebeurtenis treedt op wanneer ten minste 50% van de tijd lijn van de advertentie is bekeken. De gebeurtenis complete vindt plaats wanneer de AD aan het eind is uitgevoerd. De gebeurtenis Expand treedt op wanneer de gebruiker de video speler uitbreidt naar het volledige scherm. Clickthroughs worden opgegeven met **een <door** klikken> element binnen een <**VideoClicks** -element> en geeft een URI aan een resource die moet worden weer gegeven wanneer de gebruiker op de AD klikt. ClickTracking is opgegeven in een <**ClickTracking**>-element, ook binnen het element <**VideoClicks**> en geeft een tracerings resource op die de speler kan aanvragen wanneer de gebruiker op de AD klikt. De <**MediaFile**> elementen geven informatie op over een specifieke code ring van een ad-advertentie. Wanneer er meer dan één <**MediaFile**> element, kan de video speler de beste code ring voor het platform kiezen.

Lineaire advertenties kunnen in een opgegeven volg orde worden weer gegeven. U doet dit door extra elementen toe te voegen `<Ad>` aan het bestand vast en de volg orde op te geven met behulp van het Sequence-kenmerk. Het volgende voorbeeld illustreert dit:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
```

Er zijn ook niet-lineaire advertenties opgegeven in een- `<Creative>` element. In het volgende voor beeld ziet u een- `<Creative>` element dat een niet-lineaire AD beschrijft.

```xml
    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>
```

Het element <**NonLinearAds**> kan een of meer <niet- **lineaire**> elementen bevatten, die elk een niet-lineaire advertentie kunnen beschrijven. Met het element <niet- **lineaire**> geeft u de resource voor de niet-lineaire AD op. De resource kan een <**StaticResource**>, een <**IFrameResource**> of een <**HTMLResource** ->. \<**StaticResource**> beschrijft een niet-HTML-resource en definieert een creativeType-kenmerk dat aangeeft hoe de resource wordt weer gegeven:

Afbeelding/GIF, afbeelding/JPEG, afbeelding/PNG: de resource wordt weer gegeven in een HTML-<**img**>-tag.

Application/x-java script: de resource wordt weer gegeven in een HTML-<**script**> tag.

Application/x-Shock Wave-Flash: de resource wordt weer gegeven in een Flash Player.

**IFrameResource** beschrijft een HTML-resource die kan worden weer gegeven in een IFRAME. **HTMLResource** beschrijft een stukje HTML-code dat in een webpagina kan worden ingevoegd. **TrackingEvents** geven tracerings gebeurtenissen en de URI op die moeten worden aangevraagd wanneer de gebeurtenis optreedt. In dit voor beeld worden de gebeurtenissen acceptInvitation en samen vouwen bijgehouden. Zie IAB.NET/VAST voor meer informatie over het **NonLinearAds** -element en de bijbehorende onderliggende elementen. Houd er rekening mee dat het element **TrackingEvents** zich in het element **NonLinearAds** bevindt in plaats van het niet- **lineaire** element.

Companion-advertenties worden gedefinieerd in een- `<CompanionAds>` element. Het `<CompanionAds>` element kan een of meer `<Companion>` elementen bevatten. Elk `<Companion>` element beschrijft een aanvullende AD en kan een `<StaticResource>` , bevatten `<IFrameResource>` of `<HTMLResource>` worden opgegeven op dezelfde manier als in een niet-lineaire ad-advertentie. Een VAST bestand kan meerdere Companion-advertenties bevatten en de toepassing van de speler kan de meest geschikte AD kiezen om weer te geven. Zie voor meer informatie over vaste [3,0](https://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Een VMAP-bestand (Digital Video multiple AD Play List) gebruiken
Met een VMAP-bestand kunt u opgeven wanneer er sprake is van AD-onderbrekingen, hoe lang elke onderbreking is, hoeveel advertenties kunnen worden weer gegeven binnen een pauze en welke soorten advertenties tijdens een onderbreking kunnen worden weer gegeven. Het volgende in een voor beeld van een VMAP-bestand dat één AD-afbreek punt definieert:

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[https://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scalable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Een VMAP-bestand begint met een `<VMAP>` -element dat een of meer `<AdBreak>` elementen bevat, waarbij elk een AD-afbreek proces definieert. Elke AD-afbreek periode geeft een type en een Verbreek nummer en een tijd-offset. Het kenmerk breakType geeft het type aan van de AD die tijdens het onderbreken kan worden afgespeeld: lineair, niet-lineair of weer gave. Geef een Ads-toewijzing weer voor een grote Companion-advertentie. Er kan meer dan één AD-type worden opgegeven in een door komma's gescheiden lijst (geen spaties). De breakID is een optionele id voor de AD. De timeOffset geeft aan wanneer de AD moet worden weer gegeven. Dit kan op een van de volgende manieren worden opgegeven:

1. Time: in de notatie uu: mm: SS of uu: mm: SS. mmm waarbij. mmm de milliseconden is. De waarde van dit kenmerk geeft de tijd aan vanaf het begin van de video tijdlijn tot het begin van de AD-einde.
2. Percentage – in n% notatie waarbij n het percentage van de video tijdlijn dat moet worden afgespeeld voordat de AD wordt afgespeeld
3. Begin/einde – Hiermee geeft u op dat een AD moet worden weer gegeven voor of nadat de video is weer gegeven
4. Position: Hiermee geeft u de volg orde van de AD-onderbrekingen op wanneer de timing van de AD-onderbrekingen onbekend is, zoals in live streamen. De volg orde van elke AD-afbreeking wordt opgegeven in de #n notatie waarbij n een geheel getal 1 of hoger is. 1 geeft aan dat de advertentie moet worden afgespeeld bij de eerste mogelijkheid, 2 betekent dat de advertentie moet worden afgespeeld bij de tweede opportuniteit, enzovoort.

Binnen het `<AdBreak>` -element kan er één <element **AdSource**>. Het **AdSource** -element <> bevat de volgende kenmerken:

1. Id: Hiermee geeft u een id op voor de AD-bron
2. allowMultipleAds: een Booleaanse waarde die aangeeft of meerdere advertenties kunnen worden weer gegeven tijdens de AD-afbreek periode
3. followRedirects: een optionele Booleaanse waarde die aangeeft of de video speler omleidingen binnen een AD-antwoord moet naleven

Het **AdSource** -element van de <-> biedt de speler een inline AD-antwoord of een verwijzing naar een AD-antwoord. Dit kan een van de volgende elementen bevatten:

* `<VASTAdData>` Hiermee wordt aangegeven dat een VAST AD-antwoord is inge sloten in het VMAP-bestand
* `<AdTagURI>` een URI die verwijst naar een reactie van een AD van een ander systeem
* `<CustomAdData>` -een wille keurige teken reeks die een niet-enorme reactie vertegenwoordigt

In dit voor beeld wordt een in line AD-antwoord opgegeven met een- `<VASTAdData>` element dat een vast antwoord op ad bevat. Zie [VMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap)voor meer informatie over de andere elementen.

Het element <**AdBreak**> kan ook een <**TrackingEvents** -> element bevatten. Met het element <**TrackingEvents**> kunt u het begin of einde van een AD-einde bijhouden of er is een fout opgetreden tijdens de AD-einde. Het element <**TrackingEvents**> bevat een of meer <**Tracking**> elementen, die elk een tracerings gebeurtenis en een tracerings-URI opgeven. De mogelijke tracking gebeurtenissen zijn:

1. breakStart: Hiermee wordt het begin van een AD-einde bijgehouden
2. breakEnd: de voltooiing van een AD-einde bijhouden
3. fout: houdt een fout bij die is opgetreden tijdens de AD-afbreek periode

In het volgende voor beeld ziet u een VMAP-bestand met tracerings gebeurtenissen

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Zie voor meer informatie over het <**TrackingEvents**> element en de onderliggende items. http://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Een MAST-bestand (Media abstract sequencing Temp late) gebruiken
Met een MAST-bestand kunt u triggers opgeven die bepalen wanneer een AD wordt weer gegeven. Hier volgt een voor beeld van een MAST-bestand dat triggers bevat voor een Preroll AD, een Mid-Roll AD en een advertentie met de Volg kant.

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>
```


Een MAST-bestand begint met een **MAST** -element dat één **Triggers** -element bevat. Het `<triggers>` element bevat een of meer **trigger** -elementen die bepalen wanneer een AD moet worden afgespeeld.

Het **trigger** -element bevat een **startConditions** -element dat aangeeft wanneer een AD moet beginnen te spelen. Het **startConditions** -element bevat een of meer `<condition>` elementen. Wanneer elk `<condition>` wordt geëvalueerd als waar, wordt een trigger geïnitieerd of ingetrokken, afhankelijk van het feit of de `<condition>` zich in respectievelijk een **StartConditions** -of **endConditions** -element bevindt. Als `<condition>` er meerdere elementen aanwezig zijn, worden deze behandeld als impliciet of wordt de trigger gestart als een voor waarde die wordt geëvalueerd als waar. `<condition>` elementen kunnen worden genest. Wanneer onderliggende `<condition>` elementen vooraf worden ingesteld, worden deze behandeld als impliciet en moeten alle voor waarden worden geëvalueerd als waar om de trigger te initiëren. Het `<condition>` element bevat de volgende kenmerken die de voor waarde definiëren:

1. **type** : Hiermee geeft u het type voor waarde, gebeurtenis of eigenschap op
2. **naam** : de naam van de eigenschap of gebeurtenis die tijdens de evaluatie moet worden gebruikt
3. **waarde** : de waarde die een eigenschap moet worden geëvalueerd
4. **operator** – de bewerking die moet worden gebruikt tijdens de evaluatie: EQ (gelijk), NEQ (niet gelijk aan), GTR (groter), GEQ (groter of gelijk aan), lt (kleiner dan), LEQ (kleiner dan of gelijk aan), mod (modulo)

**endConditions** bevatten ook `<condition>` elementen. Wanneer een voor waarde wordt geëvalueerd als waar, wordt de trigger opnieuw ingesteld. Het `<trigger>` element bevat ook een `<sources>` element dat een of meer `<source>` elementen bevat. De `<source>` elementen definiëren de URI voor het antwoord van de AD en het type van het AD-antwoord. In dit voor beeld krijgt een URI een enorme reactie.

```xml
    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
```

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Video Player-Ad interface definitie (VPAID) gebruiken
VPAID is een API voor het inschakelen van uitvoer bare ad-eenheden om te communiceren met een video speler. Hierdoor kunnen zeer interactieve AD-ervaringen worden geboden. De gebruiker kan communiceren met de AD en de AD kan reageren op acties die door de viewer worden uitgevoerd. Een AD kan bijvoorbeeld knoppen weer geven waarmee de gebruiker meer informatie of een langere versie van de AD kan weer geven. De video speler moet ondersteuning bieden voor de VPAID-API en de uitvoer bare AD moet de API implementeren. Wanneer een speler een AD-bericht van een ad-server aanvraagt, kan de server reageren met een grote reactie die een VPAID AD bevat.

Een uitvoer bare AD wordt gemaakt in code die moet worden uitgevoerd in een runtime-omgeving zoals Adobe Flash™ of Java script dat in een webbrowser kan worden uitgevoerd. Wanneer een ad-server een VAST antwoord retourneert dat een VPAID AD bevat, moet de waarde van het kenmerk apiFramework in het `<MediaFile>` element ' VPAID ' zijn. Dit kenmerk geeft aan dat de Inge sloten AD een VPAID uitvoerbaar AD-bestand is. Het kenmerk type moet worden ingesteld op het MIME-type van het uitvoer bare bestand, zoals ' Application/x-Shock Wave-Flash ' of ' Application/x-java script '. Het volgende XML-fragment toont het- `<MediaFile>` element van een enorme reactie met een VPAID-uitvoerbaar bestand.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type="application/x-shockwaveflash"
                  width="640" height="480" apiFramework="VPAID">
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Een uitvoer bare AD kan worden geïnitialiseerd met behulp van het- `<AdParameters>` element binnen de `<Linear>` or- `<NonLinear>` elementen in een enorme reactie. Zie voor meer informatie over het `<AdParameters>` element [vaste 3,0](https://www.iab.net/media/file/VASTv3.0.pdf). Zie [VPAID 2,0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf)voor meer informatie over de VPAID-API.

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Een Windows-of Windows Phone 8-Player implementeren met AD-ondersteuning
Het micro soft media platform: Player Framework voor Windows 8 en Windows Phone 8 bevat een verzameling voorbeeld toepassingen die laten zien hoe u een video speler-toepassing kunt implementeren met behulp van het Framework. U kunt het Framework van de speler en de voor beelden van [Player Framework voor Windows 8 en Windows Phone 8](https://playerframework.codeplex.com)downloaden.

Wanneer u de oplossing micro soft. PlayerFramework. xaml. samples opent, ziet u een aantal mappen in het project. De map Adverteren bevat de voorbeeld code die relevant is voor het maken van een video speler met AD-ondersteuning. In de map Adverteren vindt u een aantal XAML/CS-bestanden die elk laat zien hoe u advertenties kunt invoegen op een andere manier. De volgende lijst beschrijft elk:

* AdPodPage. xaml toont hoe een AD-pod wordt weer gegeven.
* AdSchedulingPage. xaml laat zien hoe u advertenties plant.
* FreeWheelPage. xaml laat zien hoe u de FreeWheel-invoeg toepassing gebruikt om Ads te plannen.
* MastPage. xaml laat zien hoe u advertenties plant met een MAST-bestand.
* ProgrammaticAdPage. xaml laat zien hoe u via een programma advertenties kunt plannen in een video.
* ScheduleClipPage. xaml laat zien hoe u een AD plant zonder een VAST bestand.
* VastLinearCompanionPage. xaml laat zien hoe u een lineaire en aanvullende ad-advertentie kunt invoegen.
* VastNonLinearPage. xaml laat zien hoe u een niet-lineaire ad-advertentie kunt invoegen.
* VmapPage. xaml laat zien hoe u advertenties kunt opgeven met een VMAP-bestand.

In elk van deze voor beelden wordt de Media Player-klasse gebruikt die door het Player-Framework is gedefinieerd. De meeste voor beelden gebruiken invoeg toepassingen die ondersteuning voor verschillende indelingen van AD-antwoorden toevoegen. Het ProgrammaticAdPage-voor beeld werkt programmatisch samen met een Media Player-exemplaar.

### <a name="adpodpage-sample"></a>AdPodPage-voor beeld
In dit voor beeld wordt de AdSchedulerPlugin gebruikt om te definiëren wanneer een AD wordt weer gegeven. In dit voor beeld wordt een mid-Rolling aankondiging gepland om na vijf seconden te worden afgespeeld. De AD-pod (een groep advertenties die in volg orde worden weer gegeven) is opgegeven in een VAST bestand dat door een ad-server wordt geretourneerd. De URI naar het enorme bestand is opgegeven in het- `<RemoteAdSource>` element.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>
```

Zie voor meer informatie over de AdSchedulerPlugin [reclame in het Framework van Windows 8 en Windows Phone 8](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
In dit voor beeld wordt ook gebruikgemaakt van de AdSchedulerPlugin. Het plant drie advertenties, een Preroll-advertentie, een Mid-Roll AD en een advertentie met de advertentie. De URI naar de enorme voor elke AD is opgegeven in een- `<RemoteAdSource>` element.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="freewheelpage"></a>FreeWheelPage
In dit voor beeld wordt het FreeWheelPlugin gebruikt dat een bron kenmerk specificeert dat een URI specificeert die verwijst naar een SmartXML-bestand dat AD-inhoud opgeeft, evenals informatie over de AD-planning.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
In dit voor beeld wordt gebruikgemaakt van de MastSchedulerPlugin waarmee u een MAST-bestand kunt gebruiken. Het bron kenmerk bevat de locatie van het MAST-bestand.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Dit voor beeld werkt programmatisch samen met de Media Player. In het bestand ProgrammaticAdPage. xaml wordt de Media Player geïnstantieerd:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

Met het bestand ProgrammaticAdPage. xaml. cs maakt u een AdHandlerPlugin, voegt u een TimelineMarker in om op te geven wanneer een AD moet worden weer gegeven. vervolgens voegt u een handler voor de gebeurtenis MarkerReached toe waarmee een RemoteAdSource wordt geladen waarmee een URI naar een VAST bestand wordt opgegeven, waarna de AD wordt afgespeeld.

```csharp
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }
```

### <a name="scheduleclippage"></a>ScheduleClipPage
In dit voor beeld wordt de AdSchedulerPlugin gebruikt om een Mid-Roll ad te plannen door een WMV-bestand op te geven dat de AD bevat.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage
In dit voor beeld ziet u hoe u AdSchedulerPlugin kunt gebruiken om een mid-Rolling lineaire advertentie te plannen met een aanvullende AD. Het `<RemoteAdSource>` element geeft de locatie van het bestand vast.

```xml
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
In dit voor beeld wordt de AdSchedulerPlugin gebruikt om een lineaire en een niet-lineaire advertentie te plannen. De vaste bestands locatie wordt opgegeven met het- `<RemoteAdSource>` element.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vmappage"></a>VMAPPage
In dit voor beeld wordt VmapSchedulerPlugin gebruikt om advertenties te plannen met behulp van een VMAP-bestand. De URI naar het VMAP-bestand is opgegeven in het bron kenmerk van het `<VmapSchedulerPlugin>` element.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Een iOS-video speler implementeren met AD-ondersteuning
Het micro soft media platform: Player Framework voor iOS bevat een verzameling voorbeeld toepassingen waarin wordt uitgelegd hoe u een video speler-toepassing implementeert met behulp van het Framework. U kunt het Framework van de speler en de voor beelden van [Azure Media Player Framework](https://github.com/CloudMetal/azure-media-player-framework)downloaden. De pagina GitHub bevat een koppeling naar een wiki met aanvullende informatie over het Framework van de speler en een inleiding tot het voor beeld van de speler: [Azure Media Player wiki](https://github.com/CloudMetal/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Advertenties plannen met VMAP
In het volgende voor beeld ziet u hoe u advertenties plant met behulp van een VMAP-bestand.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }
            }
```

### <a name="scheduling-ads-with-vast"></a>Advertenties plannen met een enorme
Het volgende voor beeld laat zien hoe u een vertraagde binding van AD plant.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

   In het volgende voor beeld ziet u hoe u een vervroegd binden van een VAST AD-netwerk plant.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
    {
        [self logFrameworkError];
    }
    else
    {
        adLinearTime.startTime = 7;
        adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }
```

Het volgende voor beeld laat zien hoe u een advertentie kunt invoegen met behulp van ruw knippen (BRONCOMPUTER)

```csharp
    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }
```

In het volgende voor beeld ziet u hoe u een AD-pod plant.

```csharp
    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

In het volgende voor beeld ziet u hoe u een niet-plakve ad-advertentie plant. Een niet-Sticky AD wordt slechts één keer afgespeeld, ongeacht de zoek actie die de viewer uitvoert.

```csharp
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

In het volgende voor beeld ziet u hoe u een plakve ad-advertentie plant. Er wordt een plak-AD weer gegeven wanneer het opgegeven punt op de video tijdlijn wordt bereikt.

```csharp
    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

In het volgende voor beeld ziet u hoe u een advertentie kunt plannen.

```csharp
    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

In het volgende voor beeld ziet u hoe u een pre-Rolling AD plant.

```csharp
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Het volgende voor beeld laat zien hoe u een mid-upoverlay-advertentie plant.

```csharp
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
