---
title: Meldingen in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meldingen verzenden naar gebruikers van apps die zijn gebouwd op Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: b368048e5ea34ebfc073b1ae239cbb40724ae393
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684369"
---
# <a name="communication-services-notifications"></a>Communication Services-meldingen

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

De Azure Communication Services-clientbibliotheken voor chatten en bellen maken een realtime berichtenkanaal waarmee het mogelijk is om op een efficiënte, betrouwbare manier signaleringsberichten naar verbonden clients te pushen. Zo kunt u uitgebreide, realtime communicatiefuncties in uw toepassingen inbouwen zonder dat u complexe logica voor HTTP-polling moet implementeren. In mobiele apps blijft dit kanaal echter slechts verbonden zolang uw app actief is op de voorgrond. Als u wilt dat uw gebruikers inkomende oproepen of chatberichten ontvangen wanneer uw app op de achtergrond staat, moet u pushmeldingen gebruiken.

Met pushmeldingen kunt u informatie verzenden van uw toepassing naar de mobiele apparaten van gebruikers. U kunt pushmeldingen gebruiken om een dialoogvenster weer te geven, een geluid af te spelen of de gebruikersinterface voor binnenkomende oproepen weer te geven. Azure Communication Services biedt integraties met [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) en [Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) waarmee u pushmeldingen kunt toevoegen aan uw apps.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Pushmeldingen activeren via Azure Event Grid

Azure Communication Services kan worden geïntegreerd met [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) om realtime gebeurtenismeldingen te sturen op een betrouwbare, schaalbare en veilige manier. U kunt deze integratie gebruiken om een service te maken die mobiele pushmeldingen stuurt naar gebruikers door een Event Grid-abonnement te maken dat een [Azure-functie](https://docs.microsoft.com/azure/azure-functions/functions-overview) of een webhook activeert.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Diagram waarin wordt getoond hoe communicatie services worden geïntegreerd met Event Grid.":::

Lees meer over [verwerking van gebeurtenissen in Azure Communication Services](./event-handling.md).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Pushmeldingen versturen via Azure Notification Hubs

U kunt een Azure Notification Hub verbinden met uw Communication Services-resource om automatisch pushmeldingen naar het mobiele apparaat van een gebruiker te verzenden wanneer ze een inkomende oproep ontvangen. U gebruikt deze pushmeldingen om uw toepassing te activeren vanaf de achtergrond en de gebruikersinterface weer te geven waarmee de gebruiker de oproep kan aannemen of weigeren. 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Diagram waarin wordt getoond hoe Communication Services integreert met de Azure Notification Hub.":::

Communication Services maakt gebruik van Azure Notification Hub als een doorgeefservice om te communiceren met de verschillende platformspecifieke services voor pushmeldingen met behulp van de API [Direct Send](https://docs.microsoft.com/rest/api/notificationhubs/direct-send). Hiermee kunt u uw bestaande Azure Notification Hub-resources en -configuraties hergebruiken om betrouwbare oproepmeldingen met lage latentie aan te bieden in uw toepassingen.

> [!NOTE]
> Momenteel worden alleen pushmeldingen ondersteund.

### <a name="notification-hub-provisioning"></a>Notification Hub-inrichting 

Als u pushmeldingen wilt aanbieden met behulp van Notification Hubs, [maak dan een Notification Hub aan](https://docs.microsoft.com/azure/notification-hubs/create-notification-hub-portal) in hetzelfde abonnement als uw Communication Services-resource. Azure Notification Hubs moet worden geconfigureerd voor de Platform Notifications Service die u wilt gebruiken. Zie [Aan de slag met Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started) en selecteer uw doelclientplatform in de vervolgkeuzelijst aan de bovenkant van de pagina voor meer informatie over het ophalen van pushmeldingen in uw client-app van Notification Hubs.

> [!NOTE]
> Momenteel worden de APNs- en FCM-platforms ondersteund.

Zodra uw Notification Hub is geconfigureerd, kunt u deze koppelen met uw Communication Services-resource door een verbindingsreeks voor de hub op te geven met behulp van de Azure Resource Manager-client of via de Azure-portal. De verbindingsreeks moet machtigingen voor 'Verzenden' bevatten. We raden u aan om een ander toegangsbeleid te maken met machtigingen voor 'Verzenden' specifiek voor uw hub. Meer informatie over het [beveiligings- en toegangsbeleid van Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-security)

> [!IMPORTANT]
> Dit is alleen toepasbaar in de verificatiemodus voor tokens. De verificatiemodus voor certificaten wordt momenteel niet ondersteund.  
Als u APNS VOIP-meldingen wilt inschakelen, moet u de waarde van de bundel-id instellen wanneer u de Notification Hub configureert als de bundel-id voor de toepassing met het achtervoegsel `.voip`. Raadpleeg [APNS VOIP gebruiken via Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/voip-apns) voor meer details.

#### <a name="using-the-azure-resource-manager-client-to-configure-the-notification-hub"></a>De Azure Resource Manager-client gebruiken om de Notification Hub te configureren

Als u zich wilt aanmelden bij Azure Resource Manager, voert u het volgende uit en meldt u zich aan met uw referenties.

```console
armclient login
```

 Zodra u bent aangemeld, voert u het volgende uit om de Notification Hub in te richten:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-configure-the-notification-hub"></a>Het Azure-portaal gebruiken om de Notification Hub te configureren

Navigeer in het portaal naar uw Azure Communication Services-resource. Selecteer in de Communication Services-resource push meldingen in het menu links van de pagina Communication Services en koppel de Notification Hub die u eerder hebt ingericht. U moet uw verbindingsreeks en resource-ID hier opgeven:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Schermopname met de instellingen voor pushmeldingen in het Azure-portaal.":::

> [!NOTE]
> Als de Azure Notification Hub-verbindingsreeks wordt bijgewerkt, moet de Azure Communication Services-resource ook worden bijgewerkt.

#### <a name="device-registration"></a>Apparaatregistratie 

Raadpleeg de [quickstart spraakoproepen](../quickstarts/voice-video-calling/getting-started-with-calling.md) voor meer informatie over de registratie van uw apparaat bij Communication Services.

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event Grid?](https://docs.microsoft.com/azure/event-grid/overview) voor een inleiding tot Azure Event Grid.
* Bekijk de [Azure Notification Hubs-documentatie](https://docs.microsoft.com/azure/notification-hubs/) voor meer informatie over de concepten van de Azure Notification Hub.
