---
title: Veelgestelde vragen over Azure Notification Hubs (Faq's) | Microsoft Docs
description: Veelgestelde vragen over het ontwerpen en implementeren van oplossingen op Azure Notification Hubs.
services: notification-hubs
documentationcenter: mobile
author: sethmanheim
manager: femila
keywords: push melding, Push meldingen, iOS-push meldingen, Android-push meldingen, Ios-push, Android-push
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 0f79402956148c566bc34faa88e10895657883c2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591742"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Push meldingen met Azure Notification Hubs: veelgestelde vragen

## <a name="general"></a>Algemeen

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Wat is de resource structuur van Notification Hubs?

Azure Notification Hubs heeft twee resource niveaus: hubs en naam ruimten. Een hub is een enkele push bron die de verschillende platform push informatie van één app kan bevatten. Een naam ruimte is een verzameling hubs in één regio. De aanbevolen toewijzing komt overeen met één naam ruimte met één app. Binnen een naam ruimte kunt u een productie-hub hebben die werkt met uw productie-app, een test-hub die werkt met uw test-app, enzovoort.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Wat is het prijs model voor Notification Hubs?

De meest recente prijs informatie vindt u op de pagina met [Notification hubs prijzen] . Notification Hubs wordt in rekening gebracht op het niveau van de naam ruimte. (Zie ' wat is de resource structuur van Notification Hubs? ' voor de definitie van een naam ruimte. Notification Hubs biedt drie lagen:

* **Gratis**: deze laag is een goed uitgangs punt voor het verkennen van push mogelijkheden. Het wordt niet aanbevolen voor productie-apps. U krijgt 500-apparaten en 1.000.000 pushes per naam ruimte per maand, zonder service level agreement SLA-garantie.
* **Basis**: deze laag (of de Standard-laag) wordt aanbevolen voor kleinere productie-apps. U krijgt 200.000-apparaten en 10.000.000 pushes per naam ruimte per maand als basis lijn.
* **Standaard**: deze laag wordt aanbevolen voor middel grote tot grootschalige productie-apps. U krijgt 10.000.000-apparaten en 10.000.000 pushes per naam ruimte per maand als basis lijn. Inclusief uitgebreide telemetrie (aanvullende gegevens over de geleverde push status).

Functies van de Standard-laag:

* **Uitgebreide telemetrie**: u kunt notification hubs per bericht-telemetrie gebruiken om Push aanvragen en platform Notification System feedback voor fout opsporing bij te houden.
* **Multitenancy: u** kunt werken met platform Notification System referenties op een niveau van de naam ruimte. Met deze optie kunt u eenvoudig tenants splitsen in hubs binnen dezelfde naam ruimte.
* **Geplande push**: u kunt plannen dat er op elk moment meldingen worden verzonden.
* **Bulk bewerkingen**: Hiermee schakelt u de functionaliteit voor het exporteren/importeren van gegevens in, zoals wordt beschreven in het document [registraties exporteren/importeren] .

### <a name="what-is-the-notification-hubs-sla"></a>Wat is de Notification Hubs SLA?

Voor de lagen basis en Standard Notification Hubs kunnen correct geconfigureerde toepassingen Push meldingen verzenden of registratie beheer bewerkingen ten minste 99,9 procent van de tijd uitvoeren. Ga voor meer informatie over de SLA naar de pagina [Notification hubs Sla](https://azure.microsoft.com/support/legal/sla/notification-hubs/) .

> [!NOTE]
> Omdat push meldingen afhankelijk zijn van platform meldings systemen van derden, zoals de service van Apple Push Notification Services (APNs) en Google Firebase Cloud Messa ging (FCM), is er geen SLA-garantie voor het leveren van deze berichten. Nadat Notification Hubs de batches naar platform notification Systems (SLA) hebt verzonden, is het de verantwoordelijkheid van de platform meldings systemen voor het leveren van de pushes (geen SLA Guaranteed).

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>Wilt u de hub of naam ruimte Hoe kan ik upgraden of downgrade uitvoeren naar een andere laag?

Ga naar de **[Azure Portal]**  >  **Notification hubs naam ruimten** of **Notification hubs**. Selecteer de resource die u wilt bijwerken en ga naar de **prijs categorie**. Houd rekening met de volgende vereisten:

* De bijgewerkte prijs categorie is van toepassing op *alle* hubs in de naam ruimte waarmee u werkt.
* Als het aantal apparaten de limiet overschrijdt van de laag die u wilt downgradeen, moet u apparaten verwijderen voordat u een downgrade uitvoert.

## <a name="design-and-development"></a>Ontwerp en ontwikkeling

### <a name="which-server-side-platforms-do-you-support"></a>Welke platformen aan de server zijde ondersteunen u?

Server-Sdk's zijn beschikbaar voor .NET, Java, Node.js, PHP en python. Notification Hubs Api's zijn gebaseerd op REST-interfaces, zodat u rechtstreeks met REST-Api's kunt werken als u verschillende platformen gebruikt of geen extra afhankelijkheid wilt. Ga naar de pagina [Notification hubs rest api's] voor meer informatie.

### <a name="which-client-platforms-do-you-support"></a>Welke client platforms worden ondersteund?

Push meldingen worden ondersteund voor [IOS](ios-sdk-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Android China (via Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin [IOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) en [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)en [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari). Zie de pagina Notification Hubs aan de slag- [zelf studies](ios-sdk-get-started.md) voor meer informatie.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>Ondersteunt u SMS-berichten, e-mail berichten of webmeldingen?

Notification Hubs verzendt meldingen naar apparaten met mobiele apps. Het biedt geen mogelijkheden voor e-mail of tekst berichten. Notification Hubs biedt ook geen bezorgings service voor push meldingen in de browser. Klanten kunnen deze functie implementeren met behulp van Signa lering boven op de ondersteunde platformen aan de server zijde. 

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Hoeveel apparaten kan ik ondersteunen als ik push meldingen verzend via Notification Hubs?

Raadpleeg de pagina met [prijzen voor notification hubs] voor meer informatie over het aantal ondersteunde apparaten.

Als u ondersteuning nodig hebt voor meer dan 10.000.000 geregistreerde apparaten, moet u uw apparaten partitioneren over meerdere naam ruimten.

### <a name="how-many-push-notifications-can-i-send-out"></a>Hoeveel push meldingen kan ik verzenden?

Afhankelijk van de geselecteerde laag, wordt Azure Notification Hubs automatisch geschaald op basis van het aantal meldingen dat door het systeem wordt uitgevoerd.

> [!NOTE]
> De totale gebruiks kosten kunnen toenemen op basis van het aantal verzonden push meldingen. Zorg ervoor dat u op de hoogte bent van de limieten voor lagen die worden beschreven op de pagina met [Notification hubs prijzen] .

Onze klanten gebruiken Notification Hubs om dagelijks miljoenen push meldingen te verzenden. U hoeft niets te doen om het bereik van uw push meldingen te schalen, zolang u Azure Notification Hubs gebruikt.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>Hoe lang duurt het voor verzonden push meldingen om mijn apparaat te bereiken?

In een normaal gebruik, waarbij de binnenkomende belasting consistent en zelfs is, kan Azure Notification Hubs *een minuut verzenden ten minste 1.000.000 push meldingen*. Deze frequentie kan variëren, afhankelijk van het aantal Tags, de aard van de binnenkomende verzen dingen en andere externe factoren.

Tijdens de geschatte leverings tijd, de service berekent de doelen per platform en stuurt berichten naar de Push Notification Service (PNS) op basis van de geregistreerde Tags of label expressies. Het is de verantwoordelijkheid van de PNS om meldingen naar het apparaat te verzenden.

De PNS garandeert geen SLA voor het leveren van meldingen. De meeste push meldingen worden echter binnen enkele minuten in de doel apparaten bezorgd (meestal binnen 10 minuten) vanaf het moment dat ze naar Notification Hubs worden verzonden. Enkele meldingen kunnen meer tijd in beslag nemen.

> [!NOTE]
> Azure Notification Hubs heeft een beleid om Push meldingen te verwijderen die binnen 30 minuten niet aan de PNS worden geleverd. Deze vertraging kan om verschillende redenen optreden, maar meestal omdat de PNS uw toepassing beperkt.

### <a name="is-there-any-latency-guarantee"></a>Is er sprake van een latentie garantie?

Vanwege de aard van push meldingen (deze worden geleverd door een extern platform-specifieke PNS), is er geen latentie garantie. Normaal gesp roken worden de meeste push meldingen binnen een paar minuten bezorgd.

### <a name="where-does-azure-notification-hubs-store-data"></a>Waar worden gegevens opgeslagen in azure Notification Hubs?

In azure Notification Hubs worden klant registratie gegevens opgeslagen in de regio die door de klant is geselecteerd. Notification Hubs biedt nood herstel na een meta gegevens (de Notification Hubs naam, de connection string en andere belang rijke informatie). Voor alle regio's behalve Brazilië-zuid en Zuidoost-Azië wordt de back-up van de meta gegevens gehost in een andere regio (meestal de Azure-gekoppelde regio). Voor de regio's Brazilië-zuid en Zuidoost-Azië worden back-ups in dezelfde regio opgeslagen om de locatie-vereisten voor deze regio's aan te passen.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>Wat moet ik overwegen bij het ontwerpen van een oplossing met naam ruimten en notification hubs?

#### <a name="mobile-appenvironment"></a>Mobiele app/omgeving

* Gebruik één notification hub per mobiele app, per omgeving.
* In een scenario met meerdere tenants moet elke Tenant een afzonderlijke hub hebben.
* Deel nooit dezelfde notification hub voor productie-en test omgevingen. Deze praktijk kan problemen veroorzaken bij het verzenden van meldingen. (Apple biedt sandbox-en productie push-eind punten, elk met afzonderlijke referenties.)
* Standaard kunt u test meldingen naar de geregistreerde apparaten verzenden via de Azure Portal of het geïntegreerde Azure-onderdeel in Visual Studio. De drempel waarde wordt ingesteld op 10 apparaten die wille keurig uit de registratie groep zijn geselecteerd.

> [!NOTE]
> Als uw hub oorspronkelijk is geconfigureerd met een Apple sandbox-certificaat en vervolgens opnieuw werd geconfigureerd voor het gebruik van een Apple-productie certificaat, zijn de oorspronkelijke tokens van het apparaat ongeldig. Ongeldige tokens veroorzaken mislukte pushes. Scheid uw productie-en test omgevingen en gebruik verschillende hubs voor verschillende omgevingen.

#### <a name="pns-credentials"></a>PNS-referenties

Wanneer een mobiele app is geregistreerd bij de ontwikkelaars portal van een platform (bijvoorbeeld Apple of Google), worden er een app-id en beveiligings tokens verzonden. De back-end van de app levert deze tokens aan de PNS van het platform zodat push meldingen naar apparaten kunnen worden verzonden. Beveiligings tokens kunnen in de vorm van certificaten zijn (bijvoorbeeld Apple iOS of Windows Phone) of beveiligings sleutels (bijvoorbeeld Google Android of Windows). Ze moeten worden geconfigureerd in notification hubs. De configuratie wordt doorgaans uitgevoerd op het niveau van de meldings hub, maar kan ook worden uitgevoerd op het niveau van de naam ruimte in een scenario met meerdere tenants.

#### <a name="namespaces"></a>Naamruimten

Naam ruimten kunnen worden gebruikt voor implementatie groepering. Ze kunnen ook worden gebruikt om alle notification hubs voor alle tenants van dezelfde app in een scenario met meerdere tenants weer te geven.

#### <a name="geo-distribution"></a>Geo-distributie

Geo-distributie is niet altijd kritiek in scenario's voor push meldingen. Verschillende PNSes (bijvoorbeeld APNs of FCM) die push meldingen naar apparaten leveren, worden niet gelijkmatig gedistribueerd.

Als u een toepassing hebt die wereld wijd wordt gebruikt, kunt u hubs maken in verschillende naam ruimten door de Notification Hubs-service te gebruiken in verschillende Azure-regio's over de hele wereld.

> [!NOTE]
> Deze regeling wordt niet aanbevolen omdat deze uw beheer kosten, met name voor registraties, verhoogt. U moet dit alleen doen als er een expliciete behoefte is.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>Moet ik registraties uitvoeren vanuit de back-end van de app of rechtstreeks via client apparaten?

Registraties van de back-end van de app zijn handig wanneer u clients moet verifiëren voordat u de registratie maakt. Ze zijn ook handig wanneer u labels hebt die moeten worden gemaakt of gewijzigd door de back-end van de app op basis van app-logica. Ga voor meer informatie naar de pagina's voor het registreren van de [back-end] en de back- [End-registratie richtlijnen 2] .

### <a name="what-is-the-push-notification-delivery-security-model"></a>Wat is het beveiligings model voor levering van push meldingen?

Azure Notification Hubs gebruikt een op hand tekening gebaseerd beveiligings model op basis van een [gedeelde toegang](../storage/common/storage-sas-overview.md). U kunt de tokens voor Shared Access Signature gebruiken op het niveau van de hoofd naam ruimte of op het niveau van de onderliggend notification hub. Shared Access Signature-tokens kunnen worden ingesteld op het volgen van verschillende autorisatie regels, bijvoorbeeld om bericht machtigingen te verzenden of om te Luis teren naar meldings machtigingen. Zie het document [Notification hubs security model] voor meer informatie.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>Hoe kan ik een gevoelige Payload in push meldingen afhandelen?

Alle meldingen worden door de PNS van het platform bezorgd aan de doel apparaten. Wanneer een melding wordt verzonden naar Azure Notification Hubs, wordt deze verwerkt en door gegeven aan de desbetreffende PNS.

Alle verbindingen, van de afzender naar de Azure-Notification Hubs naar de PNS, gebruiken HTTPS.

> [!NOTE]
> De payload van berichten wordt niet door Azure Notification Hubs geregistreerd.

Als u gevoelige nettoladingen wilt verzenden, kunt u het beste een beveiligd push patroon gebruiken. De afzender levert een ping-melding met een bericht-id aan het apparaat zonder de gevoelige nettolading. Wanneer de app op het apparaat de payload ontvangt, roept de app een beveiligde API rechtstreeks aan om de bericht gegevens op te halen. Ga voor een hand leiding voor het implementeren van dit patroon naar de pagina [veilige push-zelf studie van Notification hubs] .

## <a name="operations"></a>Operations

### <a name="what-support-is-provided-for-disaster-recovery"></a>Welke ondersteuning wordt geboden voor herstel na nood gevallen?

We bieden een nood herstel op basis van meta gegevens op het einde (de Notification Hubs naam, de connection string en andere belang rijke informatie). Wanneer een scenario voor herstel na nood gevallen wordt geactiveerd, is registratie gegevens het *enige segment* van de notification hubs-infra structuur dat verloren is gegaan. U moet een oplossing implementeren om deze gegevens opnieuw in te vullen in uw nieuwe hub na herstel:

1. Een secundaire meldingen hub maken in een ander Data Center. We raden u aan een van de begin tot afscherming te maken van een nood herstel gebeurtenis die van invloed kan zijn op uw beheer mogelijkheden. U kunt er ook een maken op het moment van de gebeurtenis nood herstel.

2. Zorg ervoor dat de secundaire notification hub synchroon loopt met de primaire notification hub met behulp van een van de volgende opties:

   * Gebruik een app-back-end die gelijktijdig installaties maakt en bijwerkt in beide notification hubs. Met installaties kunt u uw eigen unieke apparaat-id opgeven, zodat deze geschikter is voor het replicatie scenario. Zie voor meer informatie deze [voorbeeld code](https://github.com/Azure/azure-notificationhubs-dotnet/tree/main/Samples/RedundantHubSample).
   * Gebruik een app-back-end die een reguliere dump van registraties van de primaire notification hub als back-up ophaalt. Vervolgens kan het bulksgewijs worden ingevoegd in de secundaire notification hub.

De secundaire notification hub kan eindigen op verlopen installaties/registraties. Wanneer de push naar een verlopen ingang wordt gemaakt Notification Hubs, wordt de bijbehorende installatie/registratie record automatisch opgeschoond op basis van het antwoord dat is ontvangen van de PNS-server. Als u verlopen records van een secundaire notification hub wilt opschonen, voegt u aangepaste logica toe die de feedback van elke verzen ding verwerkt. Verloopt vervolgens de installatie/registratie in de secundaire notification hub.

Als u niet beschikt over een back-end wanneer de app wordt gestart op doel apparaten, voert deze een nieuwe registratie uit in de secundaire notification hub. Uiteindelijk heeft de secundaire notification hub alle actieve apparaten geregistreerd.

Er wordt een tijds periode weer gegeven wanneer apparaten met niet-geopende apps geen meldingen ontvangen.

### <a name="is-all-of-my-data-stored-in-encrypted-form"></a>Zijn alle gegevens in een versleutelde vorm opgeslagen?

Azure Notification Hubs versleutelt alle klant gegevens op rest met uitzonde ring van registratie codes. Daarom moet u geen persoonlijke of vertrouwelijke gegevens opslaan met behulp van tags.

### <a name="is-there-audit-log-capability"></a>Is er sprake van een controle logboek mogelijkheid?

Ja. Met alle Notification Hubs-beheer bewerkingen wordt het Azure-activiteiten logboek bijgewerkt, waar het wordt weer gegeven in de [Azure Portal]. Het Azure-activiteiten logboek biedt inzicht in de bewerkingen die worden uitgevoerd op resources in uw abonnementen. Met het activiteiten logboek kunt u bepalen wat, wie en wanneer een schrijf bewerking (PUT, POST, DELETE) voor de resources in uw abonnement is uitgevoerd. U kunt ook de status van de bewerkingen en andere relevante eigenschappen begrijpen. Indien. het activiteiten logboek bevat geen lees bewerking (GET).

## <a name="monitoring-and-troubleshooting"></a>Bewaking en problemen oplossen

### <a name="what-troubleshooting-capabilities-are-available"></a>Welke mogelijkheden voor probleem oplossing zijn beschikbaar?

Azure Notification Hubs biedt verschillende functies voor het oplossen van problemen, met name voor het meest voorkomende scenario van verwijderde meldingen. Zie het artikel [Notification hubs Troubleshooting] White Paper (Engelstalig) voor meer informatie.

### <a name="what-telemetry-features-are-available"></a>Welke telemetrie-functies zijn beschikbaar?

Met Azure Notification Hubs kunt u telemetrie-gegevens weer geven in de [Azure Portal]. Details van de metrische gegevens zijn beschikbaar op de pagina metrische gegevens van [Notification hubs] .

U kunt ook programmatisch toegang krijgen tot metrische gegevens. Raadpleeg voor meer informatie de volgende artikelen:

- [Azure monitor metrische gegevens ophalen met .net](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/). In dit voor beeld worden de gebruikers naam en het wacht woord gebruikt. Als u een certificaat wilt gebruiken, overbelast u de FromServicePrincipal-methode om een certificaat op te geven, zoals wordt weer gegeven in [dit voor beeld](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs). 
- [Metrische gegevens en activiteiten logboeken voor een resource ophalen](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Azure Monitoring REST API-overzicht](../azure-monitor/essentials/rest-api-walkthrough.md)

> [!NOTE]
> Geslaagde meldingen betekenen gewoon dat push meldingen zijn bezorgd bij de externe PNS (bijvoorbeeld APNs voor iOS en macOS of FCM voor Android-apparaten). Het is de verantwoordelijkheid van de PNS om de meldingen te leveren aan doel apparaten. Normaal gesp roken worden door de PNS geen metrische gegevens over levering aan derden blootgesteld.  

[Azure-portal]: https://portal.azure.com
[Notification Hubs prijzen]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[REST API's voor Notification Hubs]: /previous-versions/azure/reference/dn530746(v=azure.100)
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[Hulp bij het registreren van back-end]: /previous-versions/azure/azure-services/dn743807(v=azure.100)
[Hulp bij het registreren van back-end 2]: /previous-versions/azure/azure-services/dn530747(v=azure.100)
[Notification Hubs beveiligings model]: /previous-versions/azure/azure-services/dn495373(v=azure.100)
[Notification Hubs Secure push-zelf studie]: ./notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md
[Notification Hubs probleem oplossing]: ./notification-hubs-push-notification-fixer.md
[Notification Hubs metrische gegevens]: ../azure-monitor/essentials/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[Registraties exporteren/importeren]: ./export-modify-registrations-bulk.md
[Azure-portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[App Service Pricing]: https://azure.microsoft.com/pricing/details/app-service/
