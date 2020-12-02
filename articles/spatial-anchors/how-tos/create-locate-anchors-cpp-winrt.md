---
title: Ankers in C++/WinRT maken en lokaliseren
description: Uitgebreide uitleg over het maken en lokaliseren van ankers met behulp van Azure Spatial Anchors in C++/WinRT.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: a6b51da695037fd84053ac0bf1b9be091781fbbf
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "96018865"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-cwinrt"></a>Ankers maken en lokaliseren met behulp van Azure Spatial Anchors in C++/WinRT

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

Met Azure Spatial Anchors kunt u ankers delen tussen verschillende apparaten. Er worden meerdere ontwikkelomgevingen ondersteund. In dit artikel gaan we dieper in op het gebruik van de Azure Spatial Anchors SDK in C++/WinRT om:

- een Azure Spatial Anchors-sessie correct in te stellen en te beheren.
- eigenschappen op lokale ankers te maken en in te stellen.
- gegevens naar de cloud te uploaden.
- ruimtelijke ankers in de cloud te vinden en verwijderen.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, moet u ervoor zorgen dat u:

- het [Overzicht Azure Spatial Anchors](../overview.md) hebt doorgelezen.
- een van de [Quickstarts van 5 minuten](../index.yml) hebt voltooid.
- Basiskennis van C++ en de <a href="/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt" target="_blank">Windows Runtime-API's</a>.

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

Meer informatie over de les [CloudSpatialAnchorSession](/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession).

```cpp
    CloudSpatialAnchorSession m_cloudSession{ nullptr };

    m_cloudSession = CloudSpatialAnchorSession();
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

Meer informatie over de les [SessionConfiguration](/cpp/api/spatial-anchors/winrt/sessionconfiguration).

```cpp
    auto configuration = m_cloudSession.Configuration();
    configuration.AccountKey(LR"(MyAccountKey)");
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```cpp
    auto configuration = m_cloudSession.Configuration();
    configuration.AccessToken(LR"(MyAccessToken)");
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

Meer informatie over de gemachtigde [TokenRequiredDelegate](/cpp/api/spatial-anchors/winrt/tokenrequireddelegate).

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [](auto&&, auto&& args) {
        args.AccessToken(LR"(MyAccessToken)");
    });
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [this](auto&&, auto&& args) {
        auto deferral = args.GetDeferral();
        MyGetTokenAsync([&deferral, &args](winrt::hstring const& myToken) {
            if (!myToken.empty()) args.AccessToken(myToken);
            deferral.Complete();
        });
    });
```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```cpp
    auto configuration = m_cloudSession.Configuration();
    configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [](auto&&, auto&& args) {
        args.AuthenticationToken(LR"(MyAuthenticationToken)");
    });
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```cpp
    m_accessTokenRequiredToken = m_cloudSession.TokenRequired(winrt::auto_revoke, [this](auto&&, auto&& args) {
        auto deferral = args.GetDeferral();
        MyGetTokenAsync([&deferral, &args](winrt::hstring const& myToken) {
            if (!myToken.empty()) args.AuthenticationToken(myToken);
            deferral.Complete();
        });
    });
```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-non-ios.md)]

Meer informatie over de methode [Start](/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#start).

```cpp
    m_cloudSession.Start();
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

Meer informatie over de methode [ProcessFrame](/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession).

```cpp
    m_cloudSession->ProcessFrame(ar_frame_);
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

Meer informatie over de gemachtigde [SessionUpdatedDelegate](/cpp/api/spatial-anchors/winrt/sessionupdateddelegate).

```cpp
    m_sessionUpdatedToken = m_cloudSession.SessionUpdated(winrt::auto_revoke, [this](auto&&, auto&& args)
    {
        auto status = args.Status();
        if (status.UserFeedback() == SessionUserFeedback::None) return;
        m_feedback = LR"(Feedback: )" + FeedbackToString(status.UserFeedback()) + LR"( -)" +
            LR"( Recommend Create=)" + FormatPercent(status.RecommendedForCreateProgress() * 100.f);
    });
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

Meer informatie over de les [CloudSpatialAnchor](/cpp/api/spatial-anchors/winrt/cloudspatialanchor).

```cpp
    // Initialization
    SpatialStationaryFrameOfReference m_stationaryReferenceFrame = nullptr;
    HolographicDisplay defaultHolographicDisplay = HolographicDisplay::GetDefault();
    SpatialLocator spatialLocator = defaultHolographicDisplay.SpatialLocator();
    m_stationaryReferenceFrame = spatialLocator.CreateStationaryFrameOfReferenceAtCurrentLocation();

    // Create a local anchor, perhaps by positioning a SpatialAnchor a few meters in front of the user
    SpatialAnchor localAnchor{ nullptr };
    PerceptionTimestamp timestamp = PerceptionTimestampHelper::FromHistoricalTargetTime(DateTime::clock::now());
    SpatialCoordinateSystem currentCoordinateSystem = m_attachedReferenceFrame.GetStationaryCoordinateSystemAtTimestamp(timestamp);
    SpatialPointerPose pose = SpatialPointerPose::TryGetAtTimestamp(currentCoordinateSystem, timestamp);

    // Get the gaze direction relative to the given coordinate system.
    const float3 headPosition = pose.Head().Position();
    const float3 headDirection = pose.Head().ForwardDirection();

    // The anchor is positioned two meter(s) along the user's gaze direction.
    constexpr float distanceFromUser = 2.0f; // meters
    const float3 gazeAtTwoMeters = headPosition + (distanceFromUser * headDirection);

    localAnchor = SpatialAnchor::TryCreateRelativeTo(currentCoordinateSystem, gazeAtTwoMeters);

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    CloudSpatialAnchor cloudAnchor = CloudSpatialAnchor();
    cloudAnchor.LocalAnchor(localAnchor);
    co_await m_cloudSession.CreateAnchorAsync(cloudAnchor);
    m_feedback = LR"(Created a cloud anchor with ID=)" + cloudAnchor.Identifier();
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

Meer informatie over de methode [GetSessionStatusAsync](/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#getsessionstatusasync).

```cpp
    SessionStatus status = co_await m_cloudSession.GetSessionStatusAsync();
    if (value.RecommendedForCreateProgress() < 1.0f) return;
    // Issue the creation request ...
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

Meer informatie over de methode [AppProperties](/cpp/api/spatial-anchors/winrt/cloudspatialanchor#appproperties).

```cpp
    CloudSpatialAnchor cloudAnchor = CloudSpatialAnchor();
    cloudAnchor.LocalAnchor(localAnchor);
    auto properties = m_cloudAnchor.AppProperties();
    properties.Insert(LR"(model-type)", LR"(frame)");
    properties.Insert(LR"(label)", LR"(my latest picture)");
    co_await m_cloudSession.CreateAnchorAsync(cloudAnchor);
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

Meer informatie over de methode [UpdateAnchorPropertiesAsync](/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#updateanchorpropertiesasync).

```cpp
    CloudSpatialAnchor anchor = /* locate your anchor */;
    anchor.AppProperties().Insert(LR"(last-user-access)", LR"(just now)");
    co_await m_cloudSession.UpdateAnchorPropertiesAsync(anchor);
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

Meer informatie over de methode [GetAnchorPropertiesAsync](/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#getanchorpropertiesasync).

```cpp
    CloudSpatialAnchor anchor = co_await m_cloudSession.GetAnchorPropertiesAsync(LR"(anchorId)");
    if (anchor != nullptr)
    {
        anchor.AppProperties().Insert(LR"(last-user-access)", LR"(just now)");
        co_await m_cloudSession.UpdateAnchorPropertiesAsync(anchor);
    }
```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

Meer informatie over de methode [Expiration](/cpp/api/spatial-anchors/winrt/cloudspatialanchor#expiration).

```cpp
    const int64_t oneWeekFromNowInHours = 7 * 24;
    const DateTime oneWeekFromNow = DateTime::clock::now() + std::chrono::hours(oneWeekFromNowInHours);
    cloudAnchor.Expiration(oneWeekFromNow);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

Meer informatie over de methode [CreateWatcher](/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#createwatcher).

```cpp
    AnchorLocateCriteria criteria = AnchorLocateCriteria();
    criteria.Identifiers({ LR"(id1)", LR"(id2)", LR"(id3)" });
    auto cloudSpatialAnchorWatcher = m_cloudSession.CreateWatcher(criteria);
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

Meer informatie over de gemachtigde [AnchorLocatedDelegate](/cpp/api/spatial-anchors/winrt/anchorlocateddelegate).

```cpp
    m_anchorLocatedToken = m_cloudSession.AnchorLocated(winrt::auto_revoke, [this](auto&&, auto&& args)
    {
        switch (args.Status())
        {
            case LocateAnchorStatus::Located:
            {
                CloudSpatialAnchor foundAnchor = args.Anchor();
            }
                break;
            case LocateAnchorStatus::AlreadyTracked:
                // This anchor has already been reported and is being tracked
                break;
            case LocateAnchorStatus::NotLocatedAnchorDoesNotExist:
                // The anchor was deleted or never existed in the first place
                // Drop it, or show UI to ask user to anchor the content anew
                break;
            case LocateAnchorStatus::NotLocated:
                // The anchor hasn't been found given the location data
                // The user might in the wrong location, or maybe more data will help
                // Show UI to tell user to keep looking around
                break;
        }
    });
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

Meer informatie over de methode [DeleteAnchorAsync](/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#deleteanchorasync).

```cpp
    co_await m_cloudSession.DeleteAnchorAsync(cloudAnchor);
    // Perform any processing you may want when delete finishes
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

Meer informatie over de methode [Stop](/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#stop).

```cpp
    m_cloudSession.Stop();
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

Meer informatie over de methode [Reset](/cpp/api/spatial-anchors/winrt/cloudspatialanchorsession#reset).

```cpp
    m_cloudSession.Reset();
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-others.md)]

```cpp
    m_cloudSession = nullptr;
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]