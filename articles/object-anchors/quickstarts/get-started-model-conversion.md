---
title: 'Snelstartgids: een object ankers model maken dat moet worden gebruikt in een app'
description: In deze Quick Start leert u hoe u een object ankers model maakt op basis van een 3D-model.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/22/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 69d23b9d02eb176a2e42985ef5c3673e83d9bb7e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102607897"
---
# <a name="quickstart-create-an-object-anchors-model-from-a-3d-model"></a>Quick Start: een object ankers model maken op basis van een 3D-model

Azure object-ankers is een beheerde Cloud service waarmee 3D-modellen worden omgezet in AI-modellen die ondersteuning bieden voor objecten met gemengde realiteit voor de HoloLens. In deze Quick Start wordt beschreven hoe u een object ankers model maakt op basis van een 3D-model met behulp van de C#/.net-ontwikkeling. core SDK.

U leert het volgende:

> [!div class="checklist"]
> * Een object ankers account maken
> * Een 3D-model converteren om een object ankers model te maken

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:

* Een Windows-machine met <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>.
* <a href="https://git-scm.com" target="_blank">Git voor Windows</a>.
* De <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">.NET Core 3.1-SDK</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-object-anchors-account"></a>Een object ankers account maken

Eerst moet u een account maken met de object ankers-service.

1. Ga naar de [Azure Portal](https://portal.azure.com/) en selecteer **een resource maken**.

   :::image type="content" source="./media/create-aoa-resource-1.png" alt-text="Een nieuwe resource maken":::

2. Zoek de **object ankers** resource.

   Zoek naar ' object ankerpunten '.

   :::image type="content" source="./media/create-aoa-resource-2.png" alt-text="De resource voor object ankerpunten selecteren":::

   Selecteer in de zoek resultaten op de resource **object ankers** de optie **Create-> object ankers**.

   :::image type="content" source="./media/create-aoa-resource-3.png" alt-text="Een resource voor object ankers maken":::

3. In het dialoog venster **object ankers account** :
    * Voer een unieke resource naam in.
    * Selecteer het abonnement waaraan u de resource wilt koppelen.
    * Een bestaande resource groep maken of gebruiken.
    * Selecteer de regio waarin u uw resource wilt bevinden.

    :::image type="content" source="./media/create-aoa-resource-4.png" alt-text="Details van resource account van object-ankers invoeren":::

    Selecteer **maken** om te beginnen met het maken van de resource.

4. Zodra de resource is gemaakt, selecteert u **Naar de resource gaan**.

   :::image type="content" source="./media/create-aoa-resource-5.png" alt-text="Ga naar resource":::

5. Op de pagina overzicht:

   Noteer het **account domein**. U hebt deze later nodig.

   :::image type="content" source="./media/create-aoa-resource-6.1.png" alt-text="Het account domein voor de resource van uw object ankerpunten kopiëren":::

   Noteer de **account-id**. U hebt deze later nodig.

   :::image type="content" source="./media/create-aoa-resource-6.2.png" alt-text="De account-ID voor uw object ankerpunten kopiëren":::

   Ga naar de pagina **toegangs sleutels** en noteer de **primaire sleutel**. U hebt deze later nodig.

   :::image type="content" source="./media/create-aoa-resource-7.png" alt-text="De account sleutel voor uw object ankerpunten kopiëren":::

## <a name="get-the-sample-project"></a>Het voorbeeld project ophalen

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## <a name="convert-a-3d-model"></a>Een 3D-model converteren

U kunt nu door gaan en uw 3D-model converteren.

1. Open `quickstarts/conversion/Conversion.sln` in Visual Studio. Deze oplossing bevat een C#-console project.

2. Open het `Configuration.cs` bestand dat zich in de hoofdmap van het project bevindt en vervang de `set-me` waarden in de volgende velden:

   | Veld         | Beschrijving                                                         |
   |---------------|---------------------------------------------------------------------|
   | AccountDomain | Het **account domein** van het account voor object ankers dat hierboven is gemaakt. |
   | AccountId     | De **account-id** van het object ankers account dat hierboven is gemaakt.     |
   | AccountKey    | De **primaire sleutel** van het object ankers die hierboven zijn gemaakt     |

   Er zijn vier aanvullende velden die moeten worden gecontroleerd:

    | Veld                    | Beschrijving                       |
    | ---                      | ---                               |
    | InputAssetPath           | Het absolute pad naar een 3D-model op de lokale computer. Ondersteunde bestands indelingen zijn `fbx` , `ply` , `obj` , en `glb` `gltf` . |
    | AssetDimensionUnit       | De meet eenheid van uw 3D-model. Alle ondersteunde meet eenheden kunnen worden geopend met behulp van de `Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit` inventarisatie. |
    | Gelopen                  | De richting van de ZWAARTE vector van het 3D-model. Deze 3D-vector biedt de neerwaartse richting van het coördinaten systeem van uw model. Als dit bijvoorbeeld negatief is `y` , is de richting van het model in de 3D-ruimte `Vector3(0.0f, -1.0f, 0.0f)` . |

3. Bouw en voer het project uit om uw 3D-model te uploaden, een nieuwe conversie taak te registreren bij de service en te wachten tot deze is voltooid. Zodra de taak is voltooid, wordt het object ankers model voor objecten gedownload naast het bestand dat is opgegeven in de `InputAssetPath` . Er wordt een fout weer gegeven die vergelijkbaar is met de volgende console-uitvoer:

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   Noteer de **taak-id** voor toekomstige referentie. Dit kan handig zijn bij het opsporen van fouten of het oplossen van problemen.

4. Zodra de taak is voltooid, ziet u een bestand met de indeling `<Model-Filename-Without-Extension>_<JobID>.ou` in de opgegeven uitvoer locatie. Als uw naam van uw 3D-model bijvoorbeeld is `chair.ply` en uw taak-id, `00000000-0000-0000-0000-000000000000` dan is de bestands naam de service-uitvoer `chair_00000000-0000-0000-0000-000000000000.ou` .

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een object ankers account gemaakt en een 3D-model geconverteerd om een object ankers model te maken. Ga door met een van de volgende artikelen voor meer informatie over het integreren van het model met de object ankers SDK in uw app Mixed Reality:

> [!div class="nextstepaction"]
> [De eenheid HoloLens](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [De eenheid HoloLens met MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](get-started-hololens-directx.md)
