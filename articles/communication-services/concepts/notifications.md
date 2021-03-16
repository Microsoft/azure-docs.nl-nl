---
title: Meldingen in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meldingen verzenden naar gebruikers van apps die zijn gebouwd op Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8910797631dd86d75619c7a4691a25e88b8a9e09
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495824"
---
# <a name="communication-services-notifications"></a>Communication Services-meldingen

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


De Azure Communication Services-clientbibliotheken voor chatten en bellen maken een realtime berichtenkanaal waarmee het mogelijk is om op een efficiënte, betrouwbare manier signaleringsberichten naar verbonden clients te pushen. Zo kunt u uitgebreide, realtime communicatiefuncties in uw toepassingen inbouwen zonder dat u complexe logica voor HTTP-polling moet implementeren. In mobiele apps blijft dit kanaal echter slechts verbonden zolang uw app actief is op de voorgrond. Als u wilt dat uw gebruikers inkomende oproepen of chatberichten ontvangen wanneer uw app op de achtergrond staat, moet u pushmeldingen gebruiken.

Met pushmeldingen kunt u informatie verzenden van uw toepassing naar de mobiele apparaten van gebruikers. U kunt pushmeldingen gebruiken om een dialoogvenster weer te geven, een geluid af te spelen of de gebruikersinterface voor binnenkomende oproepen weer te geven. Azure Communication Services biedt integraties met [Azure Event Grid](../../event-grid/overview.md) en [Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-overview.md) waarmee u pushmeldingen kunt toevoegen aan uw apps.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Pushmeldingen activeren via Azure Event Grid

Azure Communication Services kan worden geïntegreerd met [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) om realtime gebeurtenismeldingen te sturen op een betrouwbare, schaalbare en veilige manier. U kunt deze integratie gebruiken om een service te maken die mobiele pushmeldingen stuurt naar gebruikers door een Event Grid-abonnement te maken dat een [Azure-functie](../../azure-functions/functions-overview.md) of een webhook activeert.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Diagram waarin wordt getoond hoe communicatie services worden geïntegreerd met Event Grid.":::

Lees meer over [verwerking van gebeurtenissen in Azure Communication Services](./event-handling.md).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Pushmeldingen versturen via Azure Notification Hubs

U kunt een Azure Notification Hub verbinden met uw Communication Services-resource om automatisch pushmeldingen naar het mobiele apparaat van een gebruiker te verzenden wanneer ze een inkomende oproep ontvangen. U gebruikt deze pushmeldingen om uw toepassing te activeren vanaf de achtergrond en de gebruikersinterface weer te geven waarmee de gebruiker de oproep kan aannemen of weigeren.

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Diagram waarin wordt getoond hoe Communication Services integreert met de Azure Notification Hubs.":::

Communication Services maakt gebruik van Azure Notification Hub als een doorgeefservice om te communiceren met de verschillende platformspecifieke services voor pushmeldingen met behulp van de API [Direct Send](/rest/api/notificationhubs/direct-send). Hiermee kunt u uw bestaande Azure Notification Hub-resources en -configuraties hergebruiken om betrouwbare oproepmeldingen met lage latentie aan te bieden in uw toepassingen.

> [!NOTE]
> Momenteel worden alleen pushmeldingen ondersteund.

### <a name="notification-hub-provisioning"></a>Notification Hub-inrichting

Als u pushmeldingen wilt aanbieden met behulp van Notification Hubs, [maak dan een Notification Hub aan](../../notification-hubs/create-notification-hub-portal.md) in hetzelfde abonnement als uw Communication Services-resource. Configureer Azure Notification Hub voor het Platform Notification System dat u wilt gebruiken. Zie [Aan de slag met Notification Hubs](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md) en selecteer uw doelclientplatform in de vervolgkeuzelijst aan de bovenkant van de pagina voor meer informatie over het ophalen van pushmeldingen in uw client-app van Notification Hubs.

> [!NOTE]
> Momenteel worden de APNs- en FCM-platforms ondersteund.
Het APNs-platform moet zijn geconfigureerd met de modus voor tokenverificatie. De verificatiemodus voor certificaten wordt momenteel niet ondersteund.

Zodra uw Notification Hub is geconfigureerd, kunt u deze koppelen met uw Communication Services-resource door een verbindingsreeks voor de hub op te geven met behulp van de Azure Resource Manager-client of via de Azure-portal. De verbindingsreeks moet `Send`-machtigingen bevatten. U wordt aangeraden een ander toegangsbeleid te maken met machtigingen voor `Send` alleen, specifiek voor uw hub. Meer informatie over het [beveiligings- en toegangsbeleid van Notification Hubs](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="using-the-azure-resource-manager-client-to-link-your-notification-hub"></a>De Azure Resource Manager-client gebruiken om de Notification Hub te koppelen

Als u zich wilt aanmelden bij Azure Resource Manager, voert u het volgende uit en meldt u zich aan met uw referenties.

```console
armclient login
```

 Zodra u bent aangemeld, voert u het volgende uit om de Notification Hub in te richten:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-link-your-notification-hub"></a>Azure Portal gebruiken om de Notification Hub te koppelen

Navigeer in het portaal naar uw Azure Communication Services-resource. Selecteer in de Communication Services-resource push meldingen in het menu links van de pagina Communication Services en koppel de Notification Hub die u eerder hebt ingericht. U moet uw verbindingsreeks en resourceId hier opgeven:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Schermopname met de instellingen voor pushmeldingen in Azure Portal.":::

> [!NOTE]
> Als de Azure Notification Hub-verbindingsreeks wordt bijgewerkt, moet de Azure Communication Services-resource ook worden bijgewerkt.
Eventuele wijzigingen in de manier waarop de hub is gekoppeld, worden weergegeven in het gegevensvlak (d.w.z. bij het verzenden van een melding) binnen een maximum periode van ``10`` minuten. Dit is ook van toepassing wanneer de hub is gekoppeld voor de eerste keer **als** er eerder meldingen zijn verzonden.

### <a name="device-registration"></a>Apparaatregistratie

Raadpleeg de [quickstart spraakoproepen](../quickstarts/voice-video-calling/getting-started-with-calling.md) voor meer informatie over de registratie van uw apparaat bij Communication Services.

### <a name="troubleshooting-guide-for-push-notifications"></a>Probleemoplossingsgids voor pushmeldingen

Wanneer u geen pushmeldingen op uw apparaat ziet, zijn er drie locaties waar de meldingen kunnen zijn neergezet:

- De melding van Azure Communication Services is niet geaccepteerd door Azure Notification Hubs
- Platform Notification System (bijvoorbeeld APNs en FCM) hebben de melding van Azure Notification Hubs niet geaccepteerd
- Platform Notification System heeft de melding niet aan het apparaat geleverd.

De eerste plek waar een melding kan worden neergezet (Azure Notification Hubs heeft de meldingen van Azure Communication Services niet geaccepteerd) wordt hieronder behandeld. Zie [Een diagnose stellen voor verwijderde meldingen in Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-fixer.md) voor de andere twee locaties.

Bekijk de metrische gegevenswaarde van `incoming messages` van de gekoppelde [metrische gegevens van Azure Notification Hub](../../azure-monitor/essentials/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs) om te zien of de Communication Services-resource meldingen naar Azure Notification Hubs stuurt.

Hierna volgen enkele veelvoorkomende onjuiste configuraties die mogelijk de oorzaak zijn waarom Azure Notification Hub geen meldingen van uw Communication Services-resource accepteert.

#### <a name="azure-notification-hub-not-linked-to-the-communication-services-resource"></a>Azure Notification Hub is niet gekoppeld aan de Communication Services-resource

Het kan zijn dat u uw Azure Notification Hub niet hebt gekoppeld aan uw Communication Services-resource. Kijk in de sectie [Notification Hub inrichten](#notification-hub-provisioning) om te zien hoe u deze koppelt.

#### <a name="the-linked-azure-notification-hub-isnt-configured"></a>De gekoppelde Azure Notification Hub is niet geconfigureerd

U moet de gekoppelde Notification Hub configureren met de Platform Notification System-referenties voor het platform dat u wilt gebruiken (bijvoorbeeld iOS of Android). Kijk bijvoorbeeld eens in [Pushmeldingen instellen in een Notification Hub](../../notification-hubs/configure-notification-hub-portal-pns-settings.md) voor meer informatie over hoe u dit kunt doen.

#### <a name="the-linked-azure-notification-hub-doesnt-exist"></a>De gekoppelde Notification Hub bestaat niet

De Azure Notification Hub die is gekoppeld aan uw Communication Service-resource, bestaat niet meer. Controleer of de gekoppelde Notification Hub nog bestaat.

#### <a name="the-azure-notification-hub-apns-platform-is-configured-with-certificate-authentication-mode"></a>Het APNs-platform van Azure Notification Hub is geconfigureerd met verificatiemodus voor certificaten

Houd er rekening mee dat als u het APNs-platform wilt gebruiken met de verificatiemodus voor certificaten, dit momenteel niet ondersteund. U moet het APNs-platform configureren met de verificatiemodus voor tokens, zoals opgegeven in [Pushmeldingen instellen in een Notification Hub](../../notification-hubs/configure-notification-hub-portal-pns-settings.md).

#### <a name="the-linked-connection-string-doesnt-have-send-permission"></a>De gekoppelde verbindingsreeks beschikt niet over een `Send`-machtiging

De verbindingsreeks die u hebt gebruikt om uw Notification Hub aan uw Communication Services-resource te koppelen, moet beschikken over de `Send`-machtiging. Kijk in [Beveiligings- en toegangsbeleid voor Notification Hubs](../../notification-hubs/notification-hubs-push-notification-security.md) voor meer informatie over het maken van een nieuwe verbindingsreeks of als u de huidige verbindingsreeks vanuit de Azure Notification Hub wilt weergeven

#### <a name="the-linked-connection-string-or-azure-notification-hub-resourceid-arent-valid"></a>De gekoppelde verbindingsreeks of de Azure Notification Hub-resourceId is niet geldig

Zorg ervoor dat u de Communication Services-resource configureert met de juiste verbindingsreeks en Azure Notification Hub-resourceId

#### <a name="the-linked-connection-string-is-regenerated"></a>De gekoppelde verbindingsreeks wordt opnieuw gegenereerd

Als u de verbindingsreeks van uw gekoppelde Azure Notification Hub opnieuw hebt gegenereerd, moet u de verbindingsreeks bijwerken met de nieuwe reeks in uw Communication Services-resource door [de Notification Hub opnieuw te koppelen](#notification-hub-provisioning).

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event Grid?](../../event-grid/overview.md) voor een inleiding tot Azure Event Grid.
* Bekijk de [Azure Notification Hubs-documentatie](../../notification-hubs/index.yml) voor meer informatie over de concepten van de Azure Notification Hub.
