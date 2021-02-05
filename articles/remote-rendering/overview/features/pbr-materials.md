---
title: PBR-materialen
description: Hierin wordt het PBR-materiaal type beschreven.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: e9908c106e57801cb1b7def8b3353a983cc97de0
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591936"
---
# <a name="pbr-materials"></a>PBR-materialen

*PBR-materialen* zijn een van de ondersteunde [materiaal typen](../../concepts/materials.md) in de externe rendering van Azure. Ze worden gebruikt voor [netten](../../concepts/meshes.md) die realistische verlichting moeten ontvangen.

PBR staat voor **P** hysically **B** ased **R** endering en houdt in dat het materiaal de visuele eigenschappen van een Opper vlak op een fysiek plausible manier beschrijft, waardoor realistische resultaten onder alle belichtings omstandigheden mogelijk zijn. De meeste moderne game engines en hulpprogram ma's voor het maken van inhoud ondersteunen PBR-materialen omdat ze worden beschouwd als de beste benadering van echte wereld scenario's voor realtime-rendering.

![GlTF-voorbeeld model voor helm gegenereerd door ARR](media/helmet.png)

PBR-materialen zijn echter geen universele oplossing. Afhankelijk van de weergave hoek zijn er materialen die een andere kleur weer geven. Bijvoorbeeld, sommige weefsels of auto verf. Dit soort materialen worden niet verwerkt door het standaard-PBR-model en worden momenteel niet ondersteund door Azure remote rendering. Dit omvat PBR-extensies, zoals *dun-film* (meerkanaals Opper vlakken) en *heldere coating* (voor auto verf).

## <a name="common-material-properties"></a>Algemene materiaal eigenschappen

Deze eigenschappen zijn gebruikelijk voor alle materialen:

* **albedoColor:** Deze kleur wordt vermenigvuldigd met andere kleuren, zoals de *albedoMap* of *:::no-loc text="vertex "::: kleuren*. Als *transparantie* is ingeschakeld voor een materiaal, wordt het Alfa kanaal gebruikt voor het aanpassen van de dekking, met een `1` volledig ondoorzichtigheid en duidelijk `0` doorzichtig. De standaard waarde is wit.

  > [!NOTE]
  > Wanneer een PBR-materiaal volledig transparant is, zoals een perfect gereinigd glas, wordt de omgeving nog steeds weer gegeven. Heldere vlekken zoals de zon zijn nog steeds zichtbaar in de reflectie. Dit wijkt af van [kleur materialen](color-materials.md).

* **albedoMap:** Een [2D-patroon](../../concepts/textures.md) voor albedo waarden per pixel.

* **alphaClipEnabled** en **AlphaClipThreshold:** als *alphaClipEnabled* is ingesteld op True, worden alle pixels waarvan de waarde van albedo alpha lager is dan *alphaClipThreshold* niet getekend. Alfa knipsel kan zelfs worden gebruikt zonder transparantie in te scha kelen en is veel sneller te renderen. Afgekapte Alfa materialen zijn nog steeds langzamer dan volledig dekkende materialen, maar. Alpha-knipsels zijn standaard uitgeschakeld.

* **textureCoordinateScale** en **textureCoordinateOffset:** de schaal wordt vermenigvuldigd met de UV-textuur coördinaten, de offset wordt hieraan toegevoegd. Kan worden gebruikt om de bitmappatronen te spreiden en te verschuiven. De standaard schaal is (1, 1) en de offset is (0, 0).

* **useVertexColor:** Als de mesh :::no-loc text="vertex"::: kleuren bevat en deze optie is ingeschakeld, wordt de kleur van de netten :::no-loc text="vertex"::: vermenigvuldigd met de *albedoColor* en *albedoMap*. Standaard is *useVertexColor* uitgeschakeld.

* **isDoubleSided:** Als Double-sidedness is ingesteld op True, worden drie hoeken met dit materiaal weer gegeven, zelfs als de camera op de achtergrond is. Voor de verlichting van PBR-materialen wordt ook correct berekend voor back-gezichten. Deze optie is standaard uitgeschakeld. Zie ook [ :::no-loc text="Single-sided"::: rendering](single-sided-rendering.md).

* **TransparencyWritesDepth:** Als de vlag TransparencyWritesDepth is ingesteld op het materiaal en het materiaal transparant is, kunnen objecten die dit materiaal gebruiken, ook bijdragen aan de uiteindelijke diepte buffer. Zie de sectie voor het PBR-materiaal *transparant* in het volgende gedeelte. Het inschakelen van deze functie wordt aanbevolen als voor uw use-case een meer plausible [te late fase](late-stage-reprojection.md) ring van volledig transparante scènes nodig is. Voor gemengde dekkende/transparante scènes kan deze instelling leiden tot implausible-projectie gedrag of reprojectie-artefacten. Daarom is het uitschakelen van de standaard en aanbevolen instelling voor de algemene use-case dat deze vlag wordt uitgeschakeld. De geschreven diepte waarden worden opgehaald uit de laag diepte per pixel van het object dat zich het dichtst bij de camera bevindt.

* **FresnelEffect:** Met deze materiaal vlag wordt het additief [Fresnel-effect](../../overview/features/fresnel-effect.md) op het respectieve materiaal ingeschakeld. De weer gave van het effect is onderhevig aan de andere Fresnel-para meters die in het volgende worden uitgelegd. 

* **FresnelEffectColor:** De Fresnel kleur die wordt gebruikt voor dit materiaal. Alleen belang rijk wanneer de Fresnel-effect bit is ingesteld op dit materiaal (zie hierboven). Deze eigenschap bepaalt de basis kleur van de Fresnel schijnt (Zie [Fresnel-effect](../../overview/features/fresnel-effect.md) voor een volledige uitleg). Momenteel zijn alleen de RGB-kanaal waarden belang rijk en de alpha-waarde wordt genegeerd.

* **FresnelEffectExponent:** De Fresnel-exponent die wordt gebruikt voor dit materiaal. Alleen belang rijk wanneer de Fresnel-effect bit is ingesteld op dit materiaal (zie hierboven). Deze eigenschap bepaalt de sprei ding van de Fresnel schijnt. De minimale waarde 0,01 veroorzaakt een verspreiding over het hele object. De maximum waarde 10,0 beperkt de glanzende tot alleen de meest Gracing randen die zichtbaar zijn.

## <a name="pbr-material-properties"></a>Eigenschappen van het PBR-materiaal

Het belangrijkste idee van het samen stellen van fysiek gebaseerd is het gebruik van *BaseColor*, *metaaling* en *Grove* eigenschappen om een breed scala aan reële materialen te emuleren. Een gedetailleerde beschrijving van PBR valt buiten het bereik van dit artikel. Zie [andere bronnen](http://www.pbr-book.org)voor meer informatie over PBR. De volgende eigenschappen zijn specifiek voor PBR-materialen:

* **baseColor:** In PBR-materialen wordt de *albedo-kleur* aangeduid als de *basis kleur*. In azure rendering op afstand de eigenschap *albedo Color* is al aanwezig via de algemene eigenschappen van het materiaal, dus er is geen aanvullende eigenschap Base Color.

* **ruw** en **roughnessMap:** de juistheid van het Opper vlak. Ruwe Opper vlakken verstrooien het licht in meer richtingen dan gladde Opper vlakken, waardoor reflecties wazig zijn in plaats van scherp. Het waardebereik is van `0.0` tot `1.0` . Als `roughness` gelijk is `0.0` , zijn reflecties scherper. Als `roughness` gelijk is `0.5` , worden reflecties wazig.

  Als zowel een grove waarde als een grove kaart wordt opgegeven, is de uiteindelijke waarde het product van de twee.

* **metaal** -en **metalnessMap:** bij fysieke fysica komt deze eigenschap overeen met het feit of een Opper vlak een uitvoerbaar of dielectric is. Het uitvoeren van materialen heeft verschillende reflecterende eigenschappen, en ze zijn meestal reflecteel zonder albedo kleur. In PBR-materialen is deze eigenschap van invloed op het gedeelte van het Opper vlak van de omgeving. De waarden variëren van `0.0` tot `1.0` . Wanneer de metaaling is `0.0` , is de kleur van de albedo volledig zichtbaar en ziet het materiaal eruit als plastic of keramische stoffen. Wanneer de metaaling is `0.5` , ziet deze eruit als geschilderd metaal. Wanneer de metaaling is `1.0` , verliest het Opper vlak bijna de albedo kleur en wordt alleen de omgeving weer gegeven. Bijvoorbeeld, als `metalness` is `1.0` en is `roughness` , `0.0` ziet een Opper vlak eruit als een echte mirror.

  Als er zowel een waarde voor metaaliteit als een koppeling naar een metaliteit is opgegeven, is de uiteindelijke waarde het product van de twee.

  ![De bol wordt gerenderd met verschillende waarden voor de metaal-en ruwheid](./media/metalness-roughness.png)

  In de bovenstaande afbeelding ziet de bol in de rechter benedenhoek eruit als een echt metaal materiaal, de linkerbenedenhoek ziet eruit als keramisch of plastic. De albedo-kleur wordt ook gewijzigd op basis van de fysieke eigenschappen. Met een grotere groveheid verliest het materiaal de reflectie scherpte.

* **normalMap:** Voor het simuleren van nauw keurige details kan er een [normale kaart](https://en.wikipedia.org/wiki/Normal_mapping) worden gegeven.

* **occlusionMap** en **aoScale:** [omgevings bedekking](https://en.wikipedia.org/wiki/Ambient_occlusion) maken objecten met crevices er realistischer uit door scha duwen aan occluded gebieden toe te voegen. Bedekking-waarde `0.0` is van tot `1.0` , waarbij ' donker ' `0.0` (occluded) en `1.0` betekent dat er geen occlusions is. Als een 2D-structuur wordt gegeven als een bedekking-kaart, wordt het effect ingeschakeld en fungeert *aoScale* als een vermenigvuldiger.

  ![Een object dat wordt weer gegeven met en zonder omgevings bedekking](./media/boom-box-ao2.gif)

* **transparant:** Voor PBR-materialen is er slechts één transparantie-instelling: deze is ingeschakeld of niet. De dekking wordt gedefinieerd door het Alfa kanaal van de albedo kleur. Wanneer deze functie is ingeschakeld, wordt een complexere rendering-pijp lijn opgeroepen om semi-transparante Opper vlakken te tekenen. Met de externe rendering van Azure wordt de oit ( [nabestel onafhankelijke transparantie](https://en.wikipedia.org/wiki/Order-independent_transparency) ) geïmplementeerd.

  Transparante geometrie is duur om weer te geven. Als u alleen gaten in een Opper vlak nodig hebt, bijvoorbeeld voor de bladeren van een structuur, is het beter om in plaats daarvan alpha-knipsels te gebruiken.

  ![In de bovenstaande afbeelding is de weer gave van een bol met nul tot volledige transparantie ](./media/transparency.png) duidelijk, hoe de meest rechtse sfeer volledig transparant is, maar de reflectie is nog steeds zichtbaar.

  > [!IMPORTANT]
  > Als materialen moeten worden overgeschakeld van dekkend naar transparant tijdens runtime, moet de renderer gebruikmaken van de rendermethode *TileBasedComposition* [](../../concepts/rendering-modes.md). Deze beperking is niet van toepassing op materialen die worden geconverteerd als transparante materialen om te beginnen met.

## <a name="technical-details"></a>Technische details

Azure remote rendering maakt gebruik van de Cook-Torrance micro facet BRDF met GGX NDF, Schlick Fresnel en een GGX Smith gecorreleerde zichtbaarheids termijn met een Lambert diffuse term. Dit model is op dit moment de meest facto industrie norm. Raadpleeg dit artikel: [fysieke rendering-Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx) voor meer gedetailleerde informatie.

 Een alternatief voor het in azure externe rendering geplaatste PBR *-* model is het *onspiegelde Glossiness PBR-* model. Dit model kan een breder scala aan materialen vertegenwoordigen. Het is echter duurder en werkt normaal gesp roken niet goed voor real-time cases.
Het is niet altijd mogelijk om van *reflecterende Glossiness* te converteren *naar een* onscherpheid als er paren zijn *(diffuus, reflecterend)* die niet kunnen worden geconverteerd naar *(BaseColor, metaaling)*. De conversie in de andere richting is eenvoudiger en nauw keuriger, omdat alle paren *(BaseColor, metaaling)* overeenkomen met de goed gedefinieerde paren *(diffuus, reflecterend)* .

## <a name="api-documentation"></a>API-documentatie

* [C# PbrMaterial-klasse](/dotnet/api/microsoft.azure.remoterendering.pbrmaterial)
* [C# RenderingConnection. CreateMaterial ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.creatematerial)
* [C++ PbrMaterial-klasse](/cpp/api/remote-rendering/pbrmaterial)
* [C++ RenderingConnection:: CreateMaterial ()](/cpp/api/remote-rendering/renderingconnection#creatematerial)

## <a name="next-steps"></a>Volgende stappen

* [Kleurmaterialen](color-materials.md)
* [Patronen](../../concepts/textures.md)
* [Meshes](../../concepts/meshes.md)