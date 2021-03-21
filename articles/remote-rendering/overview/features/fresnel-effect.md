---
title: Fresnel-effect
description: Pagina met uitleg over functies voor het effect Fresnel material
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f63cd3b50642c3cf531387b4446992b6f15116f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594109"
---
# <a name="fresnel-effect"></a>Fresnel-effect

De functie Fresnel effect materiaal is een niet-fysiek juist, ad-hoc-effect. De functie is gebaseerd op de fysieke observatie van objecten die bij deze hoeken meer reflecties oplopen. Fresnel reflectie zelf is al fysiek opgenomen in het [PBR-materiaal model](../../overview/features/pbr-materials.md) dat wordt gebruikt in azure rendering op afstand. Daarentegen is de functie Fresnel effect materiaal slechts een additief kleur effect zonder afhankelijkheid van [verlichting](../../overview/features/lights.md) of de [lucht omgeving](../../overview/features/sky.md).

Het Fresnel-effect geeft objecten die zijn gekleurd als gevolg van de randen toe. Informatie over het aanpassen van het effect en voor beelden van de weergave resultaten vindt u in de volgende secties.

## <a name="enabling-the-fresnel-effect"></a>Het effect Fresnel inschakelen

Als u de functie Fresnel effect wilt gebruiken, moet deze worden ingeschakeld op de desbetreffende materialen. U kunt deze inschakelen door de FresnelEffect-bit van de [PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures) in te stellen op het [PBR-materiaal](../../overview/features/pbr-materials.md). Hetzelfde patroon is van toepassing op de [ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering) en het [kleur materiaal](../../overview/features/color-materials.md). Zie de sectie code voorbeelden voor een demonstratie van het gebruik.

Na het inschakelen is het Fresnel-effect direct zichtbaar. Standaard is de glanzend wit (1, 1, 1, 1) en een exponent van 1. U kunt deze instellingen aanpassen met behulp van de onderstaande para meter-Setters.

## <a name="customizing-the-effect-appearance"></a>De effect vormgeving aanpassen

Op dit moment kan het Fresnel-effect per materiaal worden aangepast met de volgende eigenschappen:

| Eigenschap material | Type | Uitleg |
|-------------------|------|-------------|
| FresnelEffectColor | Color4 | De kleur die Maxi maal wordt toegevoegd als de Fresnel schijnt. Het Alfa kanaal wordt momenteel genegeerd. |
| FresnelEffectExponent | float | De sprei ding van de Fresnel schijnt. Bereiken van 0,01 (verspreid over alle objecten) tot 10 (alleen de meest Gracing hoeken). |

In de praktijk zien verschillende instellingen voor kleur en exponent er als volgt uit:

![Voor beelden van Fresnel-effecten](./media/fresnel-effect-examples.png)

De exponent van het Fresnel-effect wordt progressief verhoogd van 1 tot 10 voor elke Color-rij. Als u dit doet, wordt de fresnele schijnen geleidelijk naar de randen van de weer gegeven objecten getrokken. Het effect Fresnel wordt ook niet beïnvloed door transparantie, zoals u kunt zien in het volgende voor beeld:

![Voor beelden van transparantie van Fresnel-effect](./media/fresnel-effect-transparent-examples.png)

Zoals u ziet, zijn de objecten op de diagonaal volledig transparant, maar fresnele schijnt blijft. Het effect imiteert fysiek gebaseerd Fresnel in dat opzicht, dat ook in deze scherm afbeeldingen wordt weer gegeven.

## <a name="code-samples"></a>Codevoorbeelden

De volgende code voorbeelden tonen het inschakelen en aanpassen van het Fresnel-effect voor zowel een [PBR-materiaal](../../overview/features/pbr-materials.md) als een [kleuren materiaal](../../overview/features/color-materials.md):

```cs
    void SetFresnelEffect(RenderingSession session, Material material)
    {
        if (material.MaterialSubType == MaterialType.Pbr)
        {
            var pbrMaterial = material as PbrMaterial;
            pbrMaterial.PbrFlags |= PbrMaterialFeatures.FresnelEffect;
            pbrMaterial.FresnelEffectColor = new Color4(1.0f, 0.5f, 0.1f, 1.0f);
            pbrMaterial.FresnelEffectExponent = 3.141592f;
        }
        else if (material.MaterialSubType == MaterialType.Color)
        {
            var colorMaterial = material as ColorMaterial;
            colorMaterial.ColorFlags |= ColorMaterialFeatures.FresnelEffect;
            colorMaterial.FresnelEffectColor = new Color4(0.25f, 1.0f, 0.25f, 1.0f);
            colorMaterial.FresnelEffectExponent = 7.654321f;
        }
    }
```

```cpp
void SetFresnelEffect(ApiHandle<RenderingSession> session, ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        auto pbrMaterial = material.as<PbrMaterial>();
        auto featureFlags = PbrMaterialFeatures((int32_t)pbrMaterial->GetPbrFlags() | (int32_t)PbrMaterialFeatures::FresnelEffect);
        pbrMaterial->SetPbrFlags(featureFlags);
        pbrMaterial->SetFresnelEffectColor(Color4{ 1.f, 0.5f, 0.1f, 1.f });
        pbrMaterial->SetFresnelEffectExponent(3.141592f);

    }
    else if (material->GetMaterialSubType() == MaterialType::Color)
    {
        auto colorMaterial = material.as<ColorMaterial>();
        auto featureFlags = ColorMaterialFeatures((int32_t)colorMaterial->GetColorFlags() | (int32_t)ColorMaterialFeatures::FresnelEffect);
        colorMaterial->SetColorFlags(featureFlags);
        colorMaterial->SetFresnelEffectColor(Color4{ 0.25f, 1.f, 0.25f, 1.f });
        colorMaterial->SetFresnelEffectExponent(7.654321f);
    }
}
```

## <a name="api-documentation"></a>API-documentatie

* [C#-PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [C++ PbrMaterialFeatures](/cpp/api/remote-rendering/pbrmaterialfeatures)
* [C#-ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [C++ ColorMaterialFeatures](/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>Volgende stappen

* [Materialen](../../concepts/materials.md)
* [PBR-materialen](../../overview/features/pbr-materials.md)
* [Kleur materiaal](../../overview/features/color-materials.md)