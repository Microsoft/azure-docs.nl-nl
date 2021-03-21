---
title: Modellen
description: Beschrijft wat een model in azure remote rendering is
author: jakrams
ms.author: jakras
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d1e66d09db3e3934871ed15493feb685d1cbe6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593871"
---
# <a name="models"></a>Modellen

Een *model* in azure rendering op afstand verwijst naar een volledige object weergave, samengesteld uit [entiteiten](entities.md) en [onderdelen](components.md). Modellen vormen de belangrijkste manier om aangepaste gegevens op te halen in de externe rendering-service.

## <a name="model-structure"></a>Modelstructuur

Een model heeft precies één [entiteit](entities.md) als het hoofd knooppunt. Hieronder ziet u mogelijk een wille keurige hiërarchie van onderliggende entiteiten. Bij het laden van een model wordt een verwijzing naar deze hoofd entiteit geretourneerd.

Aan elke entiteit kunnen [onderdelen](components.md) zijn gekoppeld. In het meest voorkomende geval hebben de entiteiten *MeshComponents*, die verwijzen naar [netresources](meshes.md).

## <a name="creating-models"></a>Modellen maken

Het maken van modellen voor runtime wordt bereikt door het [converteren van invoer modellen](../how-tos/conversion/model-conversion.md) van bestands indelingen, zoals FBX en GLTF. Het conversie proces extraheert alle resources, zoals bitmappatronen, materialen en netten, en converteert deze naar geoptimaliseerde runtime-indelingen. Ook wordt de structuur informatie geëxtraheerd en wordt deze geconverteerd naar de grafiek structuur van de entiteit/het onderdeel van ARR.

> [!IMPORTANT]
> [Model conversie](../how-tos/conversion/model-conversion.md) is de enige manier om [netten](meshes.md)te maken. Hoewel netten kunnen worden gedeeld tussen entiteiten tijdens runtime, is er geen andere manier om een net in de runtime op te halen, met uitzonde ring van het laden van een model.

## <a name="loading-models"></a>Modellen laden

Zodra een model is geconverteerd, kan het worden geladen vanuit Azure Blob-opslag in de runtime.

Er zijn twee verschillende laad functies die verschillen van de manier waarop de Asset wordt opgelost in Blob Storage:

* Het model kan rechtstreeks worden aangepakt door de Blob Storage-para meters, [als de Blob-opslag aan het account is gekoppeld](../how-tos/create-an-account.md#link-storage-accounts). Relevante laad functie in dit geval is een `LoadModelAsync` para meter `LoadModelOptions` .
* Het model kan worden aangepakt door de SAS-URI. Relevante laad functie is `LoadModelFromSasAsync` met para meter `LoadModelFromSasOptions` . Gebruik deze variant ook bij het laden van [ingebouwde modellen](../samples/sample-model.md).

De volgende code fragmenten laten zien hoe u modellen met beide functies kunt laden. Als u een model wilt laden met behulp van de Blob Storage-para meters, gebruikt u de code zoals hieronder wordt beschreven:


```cs
async void LoadModel(RenderingSession session, Entity modelParent, string storageAccount, string containerName, string assetFilePath)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelOptions(
        storageAccount, // storage account name + '.blob.core.windows.net', e.g., 'mystorageaccount.blob.core.windows.net'
        containerName,  // name of the container in your storage account, e.g., 'mytestcontainer'
        assetFilePath,  // the file path to the asset within the container, e.g., 'path/to/file/myAsset.arrAsset'
        modelParent
    );

    var loadOp = session.Connection.LoadModelAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string storageAccount, std::string containerName, std::string assetFilePath)
{
    LoadModelOptions modelOptions;
    modelOptions.Parent = modelParent;
    modelOptions.Blob.StorageAccountName = std::move(storageAccount);
    modelOptions.Blob.BlobContainerName = std::move(containerName);
    modelOptions.Blob.AssetPath = std::move(assetFilePath);

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Als u een model wilt laden met behulp van een SAS-token, gebruikt u code die vergelijkbaar is met het volgende fragment:

```cs
async void LoadModel(RenderingSession session, Entity modelParent, string modelUri)
{
    // load a model that will be parented to modelParent
    var modelOptions = new LoadModelFromSasOptions(modelUri, modelParent);

    var loadOp = session.Connection.LoadModelFromSasAsync(modelOptions, (float progress) =>
    {
        Debug.WriteLine($"Loading: {progress * 100.0f}%");
    });

    await loadOp;
}
```

```cpp
void LoadModel(ApiHandle<RenderingSession> session, ApiHandle<Entity> modelParent, std::string modelUri)
{
    LoadModelFromSasOptions modelOptions;
    modelOptions.ModelUri = modelUri;
    modelOptions.Parent = modelParent;

    ApiHandle<LoadModelResult> result;
    session->Connection()->LoadModelFromSasAsync(modelOptions,
        // completion callback
        [](Status status, ApiHandle<LoadModelResult> result)
        {
            printf("Loading: finished.");
        },
        // progress callback
        [](float progress)
        {
            printf("Loading: %.1f%%", progress * 100.f);
        }
    );
}
```

Daarna kunt u de entiteits hiërarchie door lopen en de entiteiten en onderdelen wijzigen. Als hetzelfde model meerdere keren wordt geladen, worden meerdere exemplaren gemaakt, elk met een eigen kopie van de entiteit/onderdeel structuur. Omdat mazen, materialen en bitmappatronen [gedeelde resources](../concepts/lifetime.md)zijn, worden hun gegevens niet opnieuw geladen. Daarom kan een model meer dan één keer worden gemodelleerd met relatief weinig geheugen overhead.

## <a name="api-documentation"></a>API-documentatie

* [C# RenderingConnection. LoadModelAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelasync)
* [C# RenderingConnection. LoadModelFromSasAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.loadmodelfromsasasync)
* [C++ RenderingConnection:: LoadModelAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelasync)
* [C++ RenderingConnection:: LoadModelFromSasAsync ()](/cpp/api/remote-rendering/renderingconnection#loadmodelfromsasasync)

## <a name="next-steps"></a>Volgende stappen

* [Entiteiten](entities.md)
* [Meshes](meshes.md)