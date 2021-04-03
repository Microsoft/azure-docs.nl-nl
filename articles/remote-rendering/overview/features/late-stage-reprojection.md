---
title: Vertraagde fase van het project
description: Informatie over vertraagd stadium van het project en hoe u deze kunt gebruiken.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: f0951415bba22a226dadb7f2a115cede451399bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "92205639"
---
# <a name="late-stage-reprojection"></a>Vertraagde fase van het project

*Reprojecteren op vertraagde fase* (lsr) is een hardware-functie waarmee u hologrammen kunt stabiliseren wanneer de gebruiker zich verplaatst.

Statische modellen worden naar verwachting hun positie visueel behouden wanneer u deze omzeilt. Als ze Insta Biel lijken te zijn, kan dit gedrag leiden tot LSR problemen. Denk eraan dat extra dynamische trans formaties, zoals animaties of explosie weergaven, dit gedrag kunnen maskeren.

U kunt kiezen tussen twee verschillende LSR-modi, namelijk een **vlakke lsr** of een **diepte lsr**. Welke een actief is, wordt bepaald door de vraag of de client toepassing een diepte buffer verzendt.

Beide LSR-modi verbeteren de hologram stabiliteit, maar ze hebben hun specifieke beperkingen. Begin met het proberen van diepte LSR, omdat deze weliswaar in de meeste gevallen betere resultaten oplevert.

## <a name="choose-lsr-mode-in-unity"></a>Kies de LSR-modus in eenheid

Ga in eenheids editor naar *:::no-loc text="File > Build Settings":::* . Selecteer linksonder *:::no-loc text="Player Settings":::* en controleer of het selectie vakje *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* **:::no-loc text="Enable Depth Buffer Sharing":::** is ingeschakeld:

![Vlag voor diepte buffer delen ingeschakeld](./media/unity-depth-buffer-sharing-enabled.png)

Als dat het geval is, gebruikt uw app diepte LSR, anders wordt vlakke LSR gebruikt.

## <a name="depth-lsr"></a>Diepte LSR

Voor een uitgebreide LSR moet de client toepassing een geldige diepte buffer opgeven die alle relevante geometrie bevat die tijdens LSR moet worden gebruikt.

Diepte LSR probeert het video frame te stabiliseren op basis van de inhoud van de opgegeven diepte buffer. Als gevolg hiervan kunnen inhoud die niet is gerenderd, zoals transparante objecten, niet worden aangepast met LSR en kunnen Instabiliteits-en reprojectie-artefacten worden weer gegeven. 

Als u de instabiliteit van de herprojectie voor transparante objecten wilt verhelpen, kunt u de diepte buffer geforceerd schrijven. Zie de *TransparencyWritesDepth* voor de [kleur](color-materials.md) en [PBR](pbr-materials.md) -materiaal. Houd er echter rekening mee dat de visuele kwaliteit van de interactie van transparant/ondoorzichtige objecten kan lijden bij het inschakelen van deze vlag.

## <a name="planar-lsr"></a>Vlakke LSR

Vlakke LSR hebben geen diepte informatie per pixel, zoals diepte LSR wel. In plaats daarvan wordt alle inhoud opnieuw geprojecteerd op basis van een vlak dat u elk frame moet opgeven.

Bij vlakke LSR worden deze objecten het beste opnieuw geprojecteerd die dicht bij het opgegeven vlak liggen. Het verdere weggooien van een object is, hoe stabieler het wordt. Hoewel diepte LSR beter is bij het opnieuw projecteren van objecten met verschillende diepten, kan vlakke LSR beter werken voor inhoud die goed wordt uitgelijnd met een vlak.

### <a name="configure-planar-lsr-in-unity"></a>Vlakke LSR in unit configureren

De para meters voor het vlak worden afgeleid van een zodanige *focus punt*, dat u elk frame via moet opgeven `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` . Zie de [API van het unit-focus punt](/windows/mixed-reality/focus-point-in-unity) voor meer informatie. Als u geen focus punt instelt, wordt er een terugval gekozen. Automatische terugval leidt echter vaak tot het afleiden van de beste resultaten.

U kunt het focus punt zelf berekenen, hoewel het zinvol is om het te baseren op het bedrag dat wordt berekend door de externe rendering-host. Aanroep `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` om die te verkrijgen. U wordt gevraagd een coördinaten frame op te geven waarin het focus punt wordt uitdrukt. In de meeste gevallen kunt u het beste het resultaat `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` hier opgeven.

Normaal gesp roken geven zowel de client als de host inhoud weer waarvan de andere kant geen rekening houdt, zoals UI-elementen op de client. Daarom kan het zinvol zijn het externe focus punt te combi neren met een lokaal berekend item.

De focus punten die in twee opeenvolgende frames worden berekend, kunnen heel verschillend zijn. Als u ze gewoon gebruikt, kan dit ertoe leiden dat er hologrammen worden weer gegeven. U kunt dit probleem voor komen door te interpoleren tussen de vorige en huidige focus punten.

## <a name="next-steps"></a>Volgende stappen

* [Prestatiequery's aan serverzijde](performance-queries.md)