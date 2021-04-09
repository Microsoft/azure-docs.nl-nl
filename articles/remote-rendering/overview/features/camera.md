---
title: Camera
description: Beschrijft camera-instellingen en use cases
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.openlocfilehash: dbe86313054706af974ccb324a39e942e9b5ca44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594126"
---
# <a name="camera"></a>Camera

Om ervoor te zorgen dat lokaal en extern gerenderde inhoud naadloos kunnen worden samengesteld, moeten verschillende camera-eigenschappen synchroon blijven tussen de server en de client. Met name de camera transformatie en projectie. De lokaal gerenderde inhoud moet bijvoorbeeld dezelfde camera transformatie en projectie gebruiken waarmee het meest recente externe frame is gerenderd.

![Lokale en externe camera](./media/camera.png)

In de bovenstaande afbeelding is de lokale camera verplaatst, vergeleken met het externe frame dat door de server is verzonden. Als gevolg hiervan moet de lokale inhoud worden gerenderd op basis van hetzelfde perspectief als het externe frame en ten slotte wordt de resulterende afbeelding opnieuw geprojecteerd naar de ruimte van de lokale camera, die in de [laatste fase van de reprojectie](late-stage-reprojection.md)wordt uitgelegd.

De vertraging tussen de externe en de lokale rendering betekent dat elke wijziging van deze instellingen wordt toegepast met een vertraging. Daarom kan een nieuwe waarde niet direct op hetzelfde frame worden gebruikt.

De bijna-en Far-vlak afstanden kunnen worden ingesteld op de camera-instellingen. Op de HoloLens 2 worden de resterende trans formatie-en projectie gegevens gedefinieerd door de hardware. Wanneer u de [bureaublad simulatie](../../concepts/graphics-bindings.md)gebruikt, worden deze ingesteld via de invoer van de `Update` functie.

De gewijzigde waarden kunnen lokaal worden gebruikt zodra het eerste externe frame arriveert dat ermee is gerenderd. Op HoloLens 2 worden de gegevens die moeten worden gebruikt voor rendering gegeven door de *HolographicFrame* net zoals in andere Holographic-toepassingen. Bij [bureaublad simulatie](../../concepts/graphics-bindings.md)worden ze opgehaald via de uitvoer van de `Update` functie.

## <a name="camera-settings"></a>Camera-instellingen

De volgende eigenschappen kunnen worden gewijzigd in de camera-instellingen:

**Bijna en Far vlak:**

Om ervoor te zorgen dat er geen ongeldige bereiken kunnen worden ingesteld, zijn de eigenschappen **NearPlane** en **FarPlane** alleen-lezen en is er een afzonderlijke functie **SetNearAndFarPlane** aanwezig om het bereik te wijzigen. Deze gegevens worden aan het einde van het frame naar de server verzonden. Bij het instellen van deze waarden moet **NearPlane** kleiner zijn dan **FarPlane**. Anders treedt er een fout op.

> [!IMPORTANT]
> In unit-eenheid wordt dit automatisch verwerkt bij het wijzigen van de hoofd camera in de buurt.

**EnableDepth**:

Soms is het handig om de uitgebreide buffer schrijf bewerking van de externe installatie kopie uit te scha kelen voor fout opsporing. Als u diepte uitschakelt, kan de server ook geen diepte gegevens verzenden, waardoor de band breedte wordt verminderd.

> [!TIP]
> In unit eenheid wordt een debug-onderdeel met de naam **EnableDepthComponent** weer gegeven dat kan worden gebruikt om deze functie te scha kelen in de gebruikers interface van de editor.

**InverseDepth**:

> [!NOTE]
> Deze instelling is alleen van belang als deze `EnableDepth` is ingesteld op `true` . Anders is deze instelling niet van invloed.

Diepte buffers registreren doorgaans z-waarden in een drijvende-komma bereik van [0; 1], waarbij 0 de bijna-vlak diepte aangeeft en 1 die de diepte van het dichtste vlak aangeeft. Het is ook mogelijk om dit bereik en record diepte waarden in het bereik [1; 0] te omkeren, dat wil zeggen dat de bijna-vlak diepte 1 is en dat de diepte van het ondervlak 0 is. Over het algemeen verbetert de laatste de verdeling van drijvende-komma precisie over het niet-lineaire z-bereik.

> [!WARNING]
> Een veelvoorkomende benadering is het omkeren van de bijna-vlak-en Far-waarden op de camera-objecten. Als u dit probeert te doen, mislukt de externe rendering van Azure met een fout `CameraSettings` .

De Azure remote rendering API moet weten over de diepte buffer Conventie van uw lokale renderer om op de juiste wijze externe diepte in de lokale diepte buffer samen te stellen. Als uw diepte buffer bereik [0; 1] is, verlaat u deze vlag als `false` . Als u een omgekeerde diepte buffer met een bereik van [1; 0] gebruikt, stelt u de `InverseDepth` vlag in op `true` .

> [!NOTE]
> Voor unit-eenheid wordt de juiste instelling al toegepast, `RenderingConnection` waardoor er geen hand matige interventie nodig is.

Het wijzigen van de camera-instellingen kan als volgt worden uitgevoerd:

```cs
void ChangeCameraSetting(RenderingSession session)
{
    CameraSettings settings = session.Connection.CameraSettings;

    settings.SetNearAndFarPlane(0.1f, 20.0f);
    settings.EnableDepth = false;
    settings.InverseDepth = false;
}
```

```cpp
void ChangeCameraSetting(ApiHandle<RenderingSession> session)
{
    ApiHandle<CameraSettings> settings = session->Connection()->GetCameraSettings();

    settings->SetNearAndFarPlane(0.1f, 20.0f);
    settings->SetEnableDepth(false);
    settings->SetInverseDepth(false);
}
```

## <a name="api-documentation"></a>API-documentatie

* [C#-CameraSettings](/dotnet/api/microsoft.azure.remoterendering.camerasettings)
* [C++ CameraSettings](/cpp/api/remote-rendering/camerasettings)
* [C# GraphicsBindingSimD3d11. update-functie](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11.update)
* [C++ GraphicsBindingSimD3d11:: functie update](/cpp/api/remote-rendering/graphicsbindingsimd3d11#update)

## <a name="next-steps"></a>Volgende stappen

* [Afbeeldings binding](../../concepts/graphics-bindings.md)
* [Vertraagde fase van het project](late-stage-reprojection.md)