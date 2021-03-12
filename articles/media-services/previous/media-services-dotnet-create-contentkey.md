---
title: ContentKeys maken met .NET
description: In dit artikel wordt beschreven hoe u inhouds sleutels maakt met behulp van .NET. Deze sleutels bieden beveiligde toegang tot assets.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 225b05e5-7d30-409c-b5b7-3ef0634310c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 05bf928490e94f43b755e1958213899e9e1e98e9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014167"
---
# <a name="create-contentkeys-with-net"></a>ContentKeys maken met .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Met Media Services kunt u versleutelde assets maken en leveren. Een **ContentKey** biedt veilige toegang tot uw **Asset** s. 

Wanneer u een nieuw activum maakt (bijvoorbeeld voordat u [bestanden uploadt](media-services-dotnet-upload-files.md)), kunt u de volgende versleutelings opties opgeven: **StorageEncrypted**, **CommonEncryptionProtected** of **EnvelopeEncryptionProtected**. 

Wanneer u assets levert aan uw clients, kunt u [configureren dat activa dynamisch moeten worden versleuteld](media-services-dotnet-configure-asset-delivery-policy.md) met een van de volgende twee versleuteling: **DynamicEnvelopeEncryption** of **DynamicCommonEncryption**.

Versleutelde assets moeten worden gekoppeld aan **ContentKey** s. In dit artikel wordt beschreven hoe u een inhouds sleutel maakt.

> [!NOTE]
> Wanneer u een nieuw **StorageEncrypted** -Asset maakt met behulp van de .NET-SDK van Media Services, wordt de **ContentKey** automatisch gemaakt en gekoppeld aan de Asset.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Een van de waarden die u moet instellen wanneer u een inhouds sleutel maakt, is het type inhouds sleutel. Kies een van de volgende waarden. 

```csharp
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }
```

## <a name="create-envelope-type-contentkey"></a><a id="envelope_contentkey"></a>Envelop type ContentKey maken
Het volgende code fragment maakt een inhouds sleutel van het type envelop versleuteling. Vervolgens wordt de sleutel gekoppeld aan de opgegeven Asset.

```csharp
    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

call

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);
```


## <a name="create-common-type-contentkey"></a><a id="common_contentkey"></a>Algemeen type ContentKey maken
Het volgende code fragment maakt een inhouds sleutel van het gemeen schappelijke versleutelings type. Vervolgens wordt de sleutel gekoppeld aan de opgegeven Asset.

```csharp
    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
call

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 
```

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

