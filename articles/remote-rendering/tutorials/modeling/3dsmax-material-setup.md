---
title: PBR-materialen instellen in 3ds Max
description: In deze zelfstudie wordt uitgelegd hoe u PBR-materialen instelt in 3ds Max, en deze exporteert in een FBX-indeling.
author: FlorianBorn71
ms.author: flborn
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 12407d6344c69c747230e9db6fa4d53b4520dc82
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020276"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3ds-max"></a>Zelfstudie: PBT-materialen instellen in 3ds Max

## <a name="overview"></a>Overzicht
In deze zelfstudie leert u het volgende:

>[!div class="checklist"]
>
> * Materialen met geavanceerde belichting toewijzen aan objecten in een scène.
> * Objecten en materialen ruimtelijk weergeven.
> * Een scène exporteren naar een FBX-indeling en belangrijke opties selecteren.

> [!Note]
> De in deze zelfstudie beschreven procedure werkt in 3ds Max 2019 en 3ds Max 2020.
> Door een wijziging in de manier waarop 3ds Max. 2021 grijskaarten exporteert, worden normale kaarten niet meer door de conversieservice gevonden als deze versie wordt gebruikt.

Het maken van [PBR-materialen (Physically Based Rendering)](../../overview/features/pbr-materials.md) in 3ds Max is een eenvoudige taak. Het is in veel opzichten vergelijkbaar met het instellen van PBR in andere apps voor het maken van inhoud, zoals Maya. Deze zelfstudie is een handleiding voor de installatie van een eenvoudige PBR-shader en FBX-export voor Azure Remote Rendering-projecten.

De voorbeeldscène in deze zelfstudie bevat een aantal polygone box-objecten. Er worden verschillende materialen aan toegewezen, zoals hout, metaal, geverfd metaal, plastic en rubber. In brede zin bevat elk materiaal alle of de meeste van de volgende patronen:

* **Albedo**, de kleurkaart van de materialen, ook wel **Diffuus** of **BaseColor** genoemd.
* **Metaligheid**, bepaalt of een materiaal van metaal is en welke delen van metaal zijn. 
* **Ruwheid**, bepaalt hoe ruw of glad een oppervlak is.
Dit is ook van invloed op de scherpte of wazigheid van de reflecties en glanspunten op een oppervlak.
* **Normaal**, wat details toevoegt aan een oppervlak zonder dat er meer polygonen worden toegevoegd. Voorbeelden van details zijn putjes en deukjes in een metalen oppervlak of de nerf in hout.
* **Omgevingsocclusie**, wordt gebruikt om zachte schaduwen en contactschaduwen aan een model toe te voegen. Het is een kaart in grijstinten die aangeeft welke gebieden van het model volledige belichting (wit) of volledige schaduw (zwart) krijgen.

## <a name="prepare-the-scene"></a>De scène voorbereiden
In 3ds Max gaat het instellen van een PBR-materiaal als volgt.

Eerst maken we een aantal vakobjecten. Elk object vertegenwoordigt een ander type materiaal.

>[!TIP]
>Voordat u begint met het maken van assets voor Remote Rendering, is het handig te weten dat er wordt gemeten in meters.  
>
>Het is daarom een goed idee om de systeemeenheden van uw scène in te stellen op meters. Het is ook een goed idee om **Eenheden** in te stellen op meters in de FBX-exportinstellingen BX wanneer u een scène exporteert.

In de volgende schermopname ziet u de stappen voor het instellen van de systeemeenheden op meters in 3ds Max. 

1. Ga in het hoofdmenu naar **Aanpassen** > **Instelling van eenheden** > **Instelling van systeemeenheden**. Selecteer in **Schaal van systeemeenheid** de optie **Meters**: ![Schermopname waarin wordt weergegeven hoe u systeemeenheden instelt.](media/3dsmax/system-units.jpg)

1. U kunt nu beginnen met het maken van de modellen. In de voorbeeldscène maken we diverse vakobjecten. Elk object vertegenwoordigt een ander materiaaltype. Bijvoorbeeld metaal, rubber en plastic. 

   >[!TIP]
   >Wanneer u assets maakt, wordt aanbevolen deze een passende naam te geven. Zo kunt u ze later gemakkelijker vinden als de scène veel objecten bevat.

1. Wijzig de namen van de objecten zoals wordt weergegeven in de volgende schermopname: 

   ![Schermopname waarin wordt weergegeven hoe u de namen van objecten wijzigt.](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Materialen toewijzen

Nu de scène wat objecten bevat, in dit geval een aantal kubussen, kunnen we starten met het instellen van PBR:

1. Selecteer in de hoofdwerkbalk het pictogram van de **materiaaleditor**, zoals wordt weergegeven in de volgende schermopname. U kunt ook **M** op het toetsenbord selecteren om de editor te openen. De materiaaleditor heeft twee modi die u kunt selecteren in de lijst **Modi**: De modus **Compacte materiaaleditor** en de modus **Slate-materiaaleditor**. Omdat deze scène betrekkelijk eenvoudig is, gebruiken we de compacte modus.

1. In de materiaaleditor ziet u een aantal bollen. Deze bollen zijn de materialen. Aan elk object (elk vak) in de scène wordt één materiaal toegewezen. Om de materialen toe te wijzen selecteert u om te beginnen een van de objecten in de hoofd-viewport. Selecteer vervolgens de eerste bol in de materiaaleditor. Nadat deze bol is toegewezen aan een object, wordt het geselecteerde materiaal gemarkeerd, zoals wordt weergegeven in de volgende afbeelding.

1. Selecteer **Materiaal toewijzen aan selectie**, zoals weergegeven. Het materiaal is nu toegewezen aan het geselecteerde object.

   ![Schermopname waarin wordt weergegeven hoe u materialen toewijst.](media/3dsmax/assign-material.jpg)

    In de materiaaleditor kunt u kiezen uit een brede selectie materiaaltypen, afhankelijk van uw behoeften. Normaal gesproken wordt het type materiaal standaard ingesteld op **Standaard**. Dit materiaal is een basismateriaal dat niet geschikt is voor het instellen van PBR. Daarom moeten we het materiaaltype wijzigen in een PBR-materiaal. Het 3ds Max-voorkeursmateriaal voor Azure Remote Rendering-projecten is het fysieke materiaal.

1. Selecteer in de materiaaleditor het tabblad **Standaard**. Selecteer in de **Materiaal-/kaartenbrowser** de optie **Fysiek materiaal**. Met deze actie wordt het toegewezen **Standaard** materiaal geconverteerd naar een fysiek PBR-materiaal.

   ![Schermopname waarin wordt weergegeven hoe u materiaal toewijst.](media/3dsmax/physical-material.jpg)

    In de materiaaleditor ziet u nu de eigenschappen voor het fysieke materiaal, zoals wordt weergegeven in de volgende schermopname. U kunt nu beginnen met het toewijzen van patronen aan de asset.

   ![Schermopname waarin de lijst met patronen wordt weergegeven.](media/3dsmax/textures-list.jpg)

Zoals u kunt zien, is er een breed scala aan kaarten en patronen die u kunt toevoegen aan het materiaal. Voor deze zelfstudie gebruiken we slechts vijf patroonsleuven in het materiaal.

>[!TIP]
>Het is een goed idee om uw materialen een handige naam te geven, zoals wordt weergegeven in de vorige schermopname.

Hoe u patronen genereert, kan variëren afhankelijk van uw voorkeur of gebruik. U kunt bijvoorbeeld tegelpatronen gebruiken die kunnen worden toegepast op elke willekeurige asset. Of misschien wilt u dat specifieke onderdelen van een project of asset hun eigen aangepaste sets patronen hebben. U wilt mogelijk algemene tegelpatronen gebruiken die u online kunt verkrijgen. U kunt ze ook zelf maken in apps zoals Photoshop, Quixel Suite en Substance Suite.

Voordat we patronen gaan toewijzen, kijken we naar de patrooncoördinaten (UVW) van de asset. Het is een aanbevolen procedure wanneer u patronen toepast op een model om ervoor te zorgen dat het model is uitgepakt. (Patronen worden niet juist weergegeven zonder dat het model juist is uitgepakt met UV.) Dit is met name belangrijk voor onze doeleinden, omdat we een AO-kaart (Ambient Occlusion) willen gebruiken in ons model. In tegenstelling tot Stingray Shader in Maya heeft fysiek materiaal in 3ds Max geen toegewezen AO-patroonsleuf. Daarom passen we de AO-kaart toe op een andere sleuf. Om ervoor te zorgen dat deze afzonderlijk van de andere patronen (zoals tegelpatronen) kan worden gebruikt, wijzen we deze toe aan een eigen UVW-kaartkanaal. 

Eerst wijzen we een UVW-wijzigingsfunctie voor uitpakken toe aan het model, zoals wordt weergegeven in de volgende schermopname. 

- In de geselecteerde editor voor objecteigenschappen selecteer u de wijzigingsfunctielijst. Schuif naar beneden in de vervolgkeuzelijst die wordt weergegeven, en selecteer **UVW voor uitpakken**. Met deze actie wordt een UVW-wijzigingsfunctie voor uitpakken toegepast op de asset.
![Schermopname waarin wordt weergegeven hoe u UVW voor uitpakken selecteert.](media/3dsmax/unwrap-modifier.jpg)

  Het kaartkanaal is ingesteld op 1. Normaal gesproken voert u het belangrijkste uitpakken uit op kaartkanaal 1. In dit geval is het object uitgepakt zonder overlappende patrooncoördinaten (UV).
![Schermopname waarin uitgepakte patrooncoördinaten (UVW) worden weergegeven.](media/3dsmax/unwrapped-uvw.jpg)

De volgende stap is het maken van een tweede UV-kaartkanaal.

1. Sluit de UV-editor als deze is geopend. Wijzig in de sectie **Kanaal** van het menu **UV’s bewerken** het kanaalnummer in **2**. Kaartkanaal 2 is het verwachte kanaal voor AO-kaarten. 

1. In het dialoogvenster **Waarschuwing over kanaalwijziging** kunt u de bestaande UV's in kanaal 1 **Verplaatsen** naar het nieuwe kanaal 2, of de bestaande UV's **Verlaten**, waardoor er automatisch een nieuwe UV-uitpaktaak wordt gemaakt. Selecteer **Verlaten** alleen als u van plan bent een nieuwe UV-uitpaktaak voor de AO-kaart te maken die verschilt van de UV’s in kaartkanaal 1. (Bijvoorbeeld als u tegelpatronen wilt gebruiken in kanaal 1.) In deze zelfstudie verplaatsen we de UV’s van kanaal 1 naar kanaal 2, omdat het nieuwe UV-kanaal niet hoeft te worden bewerkt.

   >[!NOTE]
   >Zelfs als u de UV-uitpaktaak hebt gekopieerd (verplaatst) van kaartkanaal 1 naar kaartkanaal 2, kunt u nog wijzigingen aanbrengen aan de nieuwe kanaal-UV's zonder dat dit van invloed is op het oorspronkelijke kaartkanaal.

   ![Schermopname waarin Waarschuwing over kanaalwijziging wordt weergegeven.](media/3dsmax/channel-change.jpg)

Nu we het nieuwe kaartkanaal hebben gemaakt, kunnen we teruggaan naar het fysieke materiaal in de materiaaleditor en beginnen met toevoegen van de patronen. Eerst voegen we de AO-kaart toe, omdat er nog een stap is om ervoor te zorgen dat deze juist werkt. Nadat de AO-kaart is gekoppeld aan ons materiaal, moeten we deze configureren om kanaal 2 te gebruiken.

Zoals eerder is vermeld, is er geen toegewezen slot voor AO-kaarten in het fysieke materiaal van 3ds Max. In plaats hiervan wordt de AO-kaart toegepast op de sleuf **Diffuse ruwheid**.

1. Selecteer in de lijst **Algemene kaarten** van het fysieke materiaal de sleuf **Geen kaart** naast **Diffuse ruwheid**, en laad de AO-kaart.

1. In de eigenschappen van het AO-patroon is het kaartkanaal standaard ingesteld op **1**. Wijzig deze waarde in **2**. Met deze actie worden de stappen voltooid die nodig zijn om de AO-kaart toe te voegen.

   >[!IMPORTANT]
   >Dit is een belangrijke stap, met name als uw UV's in kanaal 2 verschillen van die in kanaal 1, aangezien de AO niet juist in kaart wordt gebracht als het verkeerde kanaal is geselecteerd.

   ![Schermopname waarin wordt weergegeven hoe u een AO-kaart toewijst.](media/3dsmax/assign-ao-map.jpg)

We wijzen nu de normale kaart toe aan het PBR-materiaal. Deze actie wijkt enigszins af van het proces in Maya. De normale kaart wordt niet rechtstreeks toegepast op de bump-kaartsleuf. (Er is geen normale kaartsleuf in het fysieke materiaal van 3ds Max.) In plaats hiervan voegt u de normale kaart toe aan een normale kaartwijzigingsfunctie die zelf is aangesloten op de normale sleuf.

1. Selecteer in de sectie **Speciale kaarten** van de eigenschappen van het fysieke materiaal (in de materiaaleditor) de sleuf **Geen kaart** naast **Bump-kaart**. 

1. Zoek en selecteer in de **Materiaal-/kaartbrowser** de optie **Normale bump**. Met deze actie wordt een **Normale bump**-wijzigingsfunctie toegevoegd aan het materiaal.

1. Selecteer in de **Normale bump**-wijzigingsfunctie de optie **Geen kaart** naast **Normaal**. Zoek en laad de normale kaart.

1. Zorg ervoor dat de methode is ingesteld op **Tangens**. (Dit is standaard ingesteld.) Schakel, indien nodig, **Groen spiegelen (Y)** in of uit.

   ![Schermopname waarin wordt weergegeven hoe u Normale bump selecteert.](media/3dsmax/normal-bump.jpg)
   ![Schermopname waarin het laden van de normale kaart wordt weergegeven.](media/3dsmax/load-normal-map.jpg)

Nu de normale kaart juist is toegewezen, kunnen we de resterende patronen toewijzen om het instellen van het fysieke materiaal te voltooien. Dit is een eenvoudig proces. Er zijn geen speciale instellingen om rekening mee te houden. In de volgende schermopname wordt de volledige set patronen weergegeven die is toegewezen aan het materiaal: 

![Schermopname waarin de volledige set patronen wordt weergegeven die is toegewezen aan het materiaal.](media/3dsmax/all-textures.jpg)

Nu de PBR-materialen zijn gemaakt en ingesteld, kunt u zich richten op het instantiëren van objecten in de scène. Zoek vergelijkbare objecten in de scène, zoals bouten, moeren, schroeven en sluitringen. Alle gelijke objecten kunnen zorgen voor een aanzienlijke vermindering van de bestandsgrootte. Instanties van een hoofdobject kunnen hun eigen schaal, draaiing en transformaties hebben, zodat u deze naar behoefte in de scène kunt plaatsen. In 3ds Max is het instantiëringsproces eenvoudig.

1. Selecteer in de hoofd-viewport het object of de objecten die u wilt exporteren.

1. Houd **Shift** ingedrukt en sleep de assets omhoog met het transformatieprogramma (voor verplaatsen). 

1. Stel in het dialoogvenster **Opties klonen** de optie **Object** in op **Instantie**, en selecteer **OK**:

   ![Schermopname van het dialoogvenster Opties klonen.](media/3dsmax/instance-object.jpg)

Met deze actie wordt een instantie van uw object gemaakt dat u onafhankelijk van het bovenliggende item en andere instanties van dit bovenliggende item kunt verplaatsen, draaien of schalen.

>[!IMPORTANT]
>Eventuele wijzigingen die u aanbrengt in een instantie terwijl u in de subobjectmodus bent, worden doorgevoerd voor alle instanties van het object. Als u werkt met de onderdelen van een geïnstantieerd object, zoals hoekpunten en zijden van veelhoeken, moet u dus zeker weten dat u eventuele wijzigingen wilt toepassen op alle instanties. Houd er rekening mee dat elk geïnstantieerd object op elk gewenst moment kan worden veranderd in een uniek object. 

>[!TIP]
>Bij het instantiëren van de scène is het een goed idee om tijdens het proces instanties te maken. Het is lastig om later nog kopieën te vervangen door geïnstantieerde objecten. 

Een van de laatste dingen om te overwegen, voordat we verdergaan met het exportproces, is hoe u uw scène/asset gaat inpakken voor delen. In het ideale geval geeft u de asset door aan klanten of teamleden, waarna zij deze met een minimale hoeveelheid rompslomp kunnen openen en weergeven. Daarom is het belangrijk dat u de patroonpaden van de assets afstemt op het scènebestand. Als de patroonpaden voor de asset verwijzen naar een lokaal station of een absoluut pad/absolute locatie, worden ze niet geladen in de scène als deze is geopend op een andere computer, zelfs niet als het .max-bestand zich in dezelfde map bevindt als de patronen. Als u de patroonpaden relatief maakt in 3ds Max, kan dit probleem redelijk eenvoudig worden opgelost.

1. Ga in de hoofdwerkbalk naar **Bestand** > **Verwijzing** > **Assets bijhouden**. 

1. In het venster Assets bijhouden worden (bijna) alle patronen die u hebt toegepast op uw PBR-materialen, weergegeven in de kolom **Kaarten/shaders**.

1. Ernaast, in de kolom **Volledig pad**, ziet u het pad naar de locatie van de patronen. Dit is waarschijnlijk het pad naar de locatie op uw lokale computer.

1. Ten slotte ziet u een kolom met de naam **Status**. In deze kolom wordt aangegeven of een bepaald patroon is gevonden en toegepast op de scène. De structuur wordt gemarkeerd met een van de volgende termen: **OK**, **Gevonden** of **Bestand ontbreekt**. De eerste twee geven aan dat het bestand is gevonden en geladen. De laatste term duidt erop dat de tracker het bestand niet heeft gevonden.
 
   ![Schermopname van het venster Assets bijhouden.](media/3dsmax/texture-paths.jpg)

U merkt wellicht dat niet alle patronen worden weergegeven in het venster Assets bijhouden wanneer u deze voor het eerst opent. Hier hoeft zich geen zorgen over te maken. Meestal kunt u alle scènepatronen wel vinden als u het proces voor het vinden van alle patronen een of twee keer uitvoert. Het proces voor het zoeken van paden is als volgt: 

1. Houd in het venster Assets bijhouden **Shift** ingedrukt, en selecteer het bovenste patroon in de lijst **Kaarten/shaders**. Houd **Shift** nog steeds ingedrukt en selecteer vervolgens het laatste patroon in de lijst. Met deze actie worden alle patronen in de lijst geselecteerd. De geselecteerde patronen worden blauw gemarkeerd. (Bekijk de vorige schermopname.)

1. Klik met de rechtermuisknop op de selectie en selecteer **Pad instellen**.

1. Selecteer in het vak **Assetpad opgeven** het lokale pad naar uw patronen en vervang deze door `.\`.  Selecteer **OK**. 

    Het venster Assets bijhouden wordt bijgewerkt zoals weergegeven in de volgende schermopname. Deze update kan enige tijd duren, afhankelijk van het aantal patronen in uw scène en hoe groot uw scène is.
![Schermopname waarin het bijgewerkte het venster Assets bijhouden wordt weergegeven.](media/3dsmax/resolve-textures.jpg)

De kolom **Volledig pad** is nu leeg. Dit betekent dat de scène niet meer naar de relevante patronen op een specifieke (absolute) locatie kijkt. De patronen worden altijd gevonden zoals het .max-bestand of gerelateerde FBX-bestand zich in dezelfde map bevindt als de patronen. 

>[!NOTE]
>Mogelijk moet u dit proces een paar keer herhalen om alle patronen en paden te vinden en om te zetten. Hier hoeft zich geen zorgen over te maken. Herhaal dit proces gewoon totdat alle relevante assets zijn gevonden. In sommige gevallen worden bepaalde bestanden niet gevonden. In dit geval selecteert u alle assets in de lijst en selecteert u vervolgens **Ontbrekende paden verwijderen**. (Zie de vorige afbeelding.)

## <a name="fbx-export"></a>FBX-export

Nu we de patroonpaden relatief hebt gemaakt, kunnen we beginnen met de FBX-export. Ook dit proces is eenvoudig en kan op verschillende manieren worden uitgevoerd. 

>[!TIP]
>Tenzij u de hele scène wilt exporteren, is het een goed idee om alleen de assets te selecteren die u nodig hebt. In resource-intensieve scènes kan het enige tijd duren voordat het exporteren is voltooid.
>
>Als u wijzigingsfuncties hebt gebruikt zoals Turbosmooth of Open SubDiv, is het raadzaam om deze samen te vouwen voordat u exporteert, omdat deze tot problemen kunnen leiden tijdens het exporteren. Zorg ervoor dat u de scène opslaat voordat u deze samenvouwt. 

1. Selecteer in de scène de assets die u wilt exporteren. Ga op de hoofdwerkbalk naar **Bestand** > **Exporteren** > **Selectie exporteren**.

1. Typ of selecteer in het dialoogvenster **Bestand selecteren om te exporteren** de naam van een uitvoerbestand. Selecteer in de lijst **Opslaan als type** de optie **Autodesk (*.fbx)** . Met deze actie wordt het venster FBX exporteren geopend.

  >[!IMPORTANT] 
  >Als u instanties hebt gemaakt in de scène, is het belangrijk om **Instanties behouden** te selecteren in de FBX-exportinstellingen. 

  ![Schermopname waarin wordt weergegeven hoe u exporteert naar FBX.](media/3dsmax/fbx-export.jpg)

  Onthoud dat er verschillende manieren zijn om het bestand te exporteren. Als u de FBX wilt delen samen met de bijbehorende patroonbestanden in een map, zijn de instellingen in de volgende schermopname geschikt. 

   Als u liever geen grote mappen met patronen samen met de FBX wilt delen, kunt u ervoor kiezen om de patronen in te sluiten in de FBX. Als u de patronen insluit, wordt de hele asset, inclusief patronen toegevoegd aan één FBX. Als u dit doet, wordt de export gecombineerd tot één enkele asset, maar het FBX-bestand is als gevolg hiervan redelijk groot.

   >[!IMPORTANT]
   >Als het resulterende FBX-bestand groter is dan 2,4 GB, moet de minimale versie die is opgegeven in de FBX-exportinstellingen, 2016 of hoger zijn. (Bekijk de vorige schermopname.) Nieuwere versies bieden ondersteuning voor 64 bits en ondersteunen dus grotere bestanden.

1. Als u de scène wilt exporteren inclusief de patronen, selecteert u in het venster *FBX exporteren de optie **Media insluiten**. 

1. Selecteer de overige instellingen en selecteer vervolgens **OK**:

    ![Schermopname waarin de FBX-exportinstellingen worden weergegeven.](media/3dsmax/fbx-settings.jpg)


   Wanneer u exporteert naar FBX terwijl u een fysiek materiaal gebruikt, ziet u, nadat u **OK** hebt geselecteerd, waarschijnlijk de volgende waarschuwing in het venster FBX exporteren: 

   ![Schermopname waarin een waarschuwing wordt weergegeven over het mislukken van een materiaalexport.](media/3dsmax/export-warnings.jpg)

   Deze waarschuwing laat u weten dat het geëxporteerde materiaal mogelijk niet compatibel is met andere softwarepakketten. Omdat het fysieke materiaal compatibel is met Azure Remote Rendering, hoeft u zich geen zorgen te maken over deze waarschuwing. 

1. Selecteer **OK** om het proces te voltooien en het venster te sluiten.

## <a name="conclusion"></a>Conclusie

Over het algemeen lijken materialen van dit type realistisch, omdat ze zijn gebaseerd op natuurlijk licht. Hierdoor ontstaat een extra onderdompelingseffect dat ervoor zorgt dat de scène werkelijk lijkt te bestaan.

## <a name="next-steps"></a>Volgende stappen

U weet nu hoe u materialen kunt instellen met geavanceerde belichting voor objecten in een scène. U weet ook hoe u objecten kunt exporteren naar de FBX-indeling die wordt ondersteund voor Azure Remote Rendering. De volgende stap bestaat uit het converteren van het FBX-bestand en visualiseren in Azure Remote Rendering.

>[!div class="nextstepaction"]
>[Snelstart: een model converteren voor rendering](../../quickstarts\convert-model.md)
