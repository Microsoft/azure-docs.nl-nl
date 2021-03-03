---
title: Het Remote Rendering-pakket voor Unity installeren
description: Uitleg over het installeren van de externe rendering client-Dll's voor Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 9454bef52798650fc431f8df994e1a964662b453
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720818"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Het Remote Rendering-pakket voor Unity installeren

Azure remote rendering maakt gebruik van een eenheids pakket om de integratie in Unit te integreren.
Dit pakket bevat de volledige C# API en alle binaire bestanden voor invoeg toepassingen die vereist zijn voor het gebruik van externe rendering van Azure met eenheid.
Het naam schema van de volgende eenheid voor pakketten wordt het pakket **com. micro soft. Azure. remote-rendering** genoemd.

U kunt een van de volgende opties kiezen om het Unity-pakket te installeren.

## <a name="install-remote-rendering-package-using-the-mixed-reality-feature-tool"></a>Extern rendering-pakket installeren met het onderdeel hulp programma Mixed Reality

[Het hulp programma Mixed Reality](https://aka.ms/MRFeatureToolDocs) ([down load](https://aka.ms/mrfeaturetool)) is een hulp programma dat wordt gebruikt om de functie pakketten voor gemengde realiteit te integreren in Unity-projecten. Het pakket maakt geen deel uit van de [opslag plaats ARR](https://github.com/Azure/azure-remote-rendering)-voor beelden en is niet beschikbaar in het interne pakket register van de eenheid.

Als u het pakket aan een project wilt toevoegen, moet u het volgende doen:
1. [Het hulp programma voor de functie voor gemengde realiteit downloaden](https://aka.ms/mrfeaturetool)
1. Volg de [volledige instructies](https://aka.ms/MRFeatureToolDocs) voor het gebruik van het hulp programma.
1. Tik op de pagina **functies ontdekken** het selectie vakje voor het **Microsoft Azure externe rendering** -pakket in en selecteer de versie van het pakket dat u aan uw project wilt toevoegen

![Mixed_Reality_feature_tool_package](media/mixed-reality-feature-tool-package.png)

Als u uw lokale pakket wilt bijwerken, selecteert u een nieuwere versie van het onderdeel hulp programma voor gemengde realiteit en installeert u dit. Het pakket bijwerken kan soms tot consolefouten leiden. Als dit het geval is, sluit u het project en opent u het opnieuw.

## <a name="install-remote-rendering-package-manually"></a>Het pakket voor de externe rendering hand matig installeren

Als u het externe rendering-pakket hand matig wilt installeren, moet u het volgende doen:

1. Down load het pakket van de NPM-feed van de Mixed Reality-pakketten op `https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry` .
    * U kunt [NPM](https://www.npmjs.com/get-npm) gebruiken en de volgende opdracht uitvoeren om het pakket te downloaden naar de huidige map.
      ```
      npm pack com.microsoft.azure.remote-rendering --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry
      ```

    * U kunt ook het Power shell-script gebruiken in `Scripts/DownloadUnityPackages.ps1` de [github-opslag plaats Azure-remote-rendering](https://github.com/Azure/azure-remote-rendering).
        * De inhoud van wijzigen `Scripts/unity_sample_dependencies.json` in
          ```json
          {
            "packages": [
              {
                "name": "com.microsoft.azure.remote-rendering", 
                "version": "latest", 
                "registry": "https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry"
              }
            ]
          }
          ```

        * Voer de volgende opdracht uit in Power shell om het pakket te downloaden naar de beschik bare doelmap.
          ```
          DownloadUnityPackages.ps1 -DownloadDestDir <destination directory>
          ```

1. [Installeer het gedownloade pakket](https://docs.unity3d.com/Manual/upm-ui-tarball.html) met package manager unit.

Als u uw lokale pakket wilt bijwerken, voert u de desbetreffende opdracht die u hebt gebruikt opnieuw uit en importeert u het pakket opnieuw. Het pakket bijwerken kan soms tot consolefouten leiden. Als dit het geval is, sluit u het project en opent u het opnieuw.

## <a name="unity-render-pipelines"></a>Unit weergave-pijp lijnen

Externe rendering werkt met zowel de **:::no-loc text="Universal render pipeline":::** als **:::no-loc text="Standard render pipeline":::** . Uit prestatie overwegingen wordt de universele rendering-pijp lijn aanbevolen.

Als u het wilt gebruiken, moet het **:::no-loc text="Universal render pipeline":::** bijbehorende pakket worden geïnstalleerd in eenheid. Dit kan worden gedaan in de gebruikers interface van de **Package Manager** van Unit (pakket naam **Universal RP**, version 7.3.1 of hoger) of via het `Packages/manifest.json` bestand, zoals beschreven in de [zelf studie over unit project Setup](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package).

## <a name="next-steps"></a>Volgende stappen

* [Unit-Game objecten en-onderdelen](objects-components.md)
* [Zelf studie: externe modellen weer geven](../../tutorials/unity/view-remote-models/view-remote-models.md)
