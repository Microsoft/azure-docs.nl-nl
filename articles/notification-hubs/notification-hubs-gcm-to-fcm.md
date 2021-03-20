---
title: Migratie van Azure Notification Hubs en de Google Firebase Cloud Messa ging (FCM)
description: Hierin wordt beschreven hoe Azure Notification Hubs het adres van de Google GCM naar FCM-migratie.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80127027"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Migratie van Azure Notification Hubs en Google Firebase Cloud Messa ging

## <a name="current-state"></a>Huidige status

Wanneer Google de migratie van Google Cloud Messaging (GCM) heeft aangekondigd naar Firebase Cloud Messa ging (FCM), moeten push services zoals onze berichten aanpassen hoe we meldingen naar Android-apparaten hebben verzonden om de wijziging aan te passen.

We hebben onze service-back-end bijgewerkt en vervolgens de updates op de API en Sdk's gepubliceerd waar nodig. Met onze implementatie hebben we besloten om de compatibiliteit met bestaande GCM-meldings schema's te hand haven om de impact van de klant te minimaliseren. Dit betekent dat we momenteel meldingen naar Android-apparaten verzenden met behulp van FCM in de legacy-modus FCM. Uiteindelijk willen we echte ondersteuning voor FCM toevoegen, met inbegrip van de nieuwe functies en de indeling van de nettolading. Dat is een langere termijn wijziging en de huidige migratie is gericht op het onderhouden van compatibiliteit met bestaande toepassingen en Sdk's. U kunt de GCM-of FCM-Sdk's in uw app gebruiken (samen met onze SDK) en er wordt gecontroleerd of de melding correct wordt verzonden.

Sommige klanten hebben onlangs een e-mail ontvangen van Google-waarschuwing over apps met behulp van een GCM-eind punt voor meldingen. Dit was slechts een waarschuwing en er zijn geen onderbrekingen. de Android-meldingen van uw app worden nog steeds verzonden naar Google voor verwerking en Google verwerkt deze nog steeds. Sommige klanten die het GCM-eind punt expliciet in hun service configuratie hebben opgegeven, hebben nog steeds het afgeschafte eind punt gebruikt. Deze onderbreking is al geïdentificeerd en er werd gewerkt aan het oplossen van het probleem toen Google het e-mail bericht heeft verzonden.

Het afgeschafte eind punt is vervangen en de oplossing is geïmplementeerd.

## <a name="going-forward"></a>Gaat verder

In de veelgestelde vragen over FCM van Google wordt aangegeven dat u niets hoeft te doen. In de [Veelgestelde vragen over FCM](https://developers.google.com/cloud-messaging/faq)worden Google zei ' client-SDK'S en GCM-tokens voor onbepaalde tijd blijven werken. Het is echter niet mogelijk om de nieuwste versie van Google Play Services in uw Android-app te richten, tenzij u migreert naar FCM.

Als uw app gebruikmaakt van de GCM-bibliotheek, gaat u verder met het volgen van de instructies van Google om een upgrade uit te voeren naar de FCM-bibliotheek in uw app. Onze SDK is compatibel met ofwel. u hoeft dus niets in uw app bij onze kant bij te werken (op voor waarde dat u met onze SDK-versie up-to-date bent).

## <a name="questions-and-answers"></a>Vragen en antwoorden

Hier volgen enkele antwoorden op veelgestelde vragen die we van klanten hebben gehoord:

**V:** Wat moet ik doen om compatibel te zijn met de afsluit datum (de huidige afsluit datum van Google kan 29 en kunnen worden gewijzigd)?

**A:** Nul. De compatibiliteit met het bestaande GCM-meldings schema wordt behouden. Uw GCM-sleutel blijft werken zoals bij elke GCM-Sdk's en-bibliotheken die door uw toepassing worden gebruikt.

Als u besluit om een upgrade uit te voeren naar de FCM-Sdk's en-bibliotheken om te profiteren van nieuwe functies, zal uw GCM-sleutel nog steeds werken. U kunt desgewenst een FCM-sleutel gebruiken, maar zorg ervoor dat u Firebase toevoegt aan uw bestaande GCM-project bij het maken van het nieuwe Firebase-project. Dit zorgt voor achterwaartse compatibiliteit met uw klanten waarop oudere versies van de app worden uitgevoerd die nog steeds gebruikmaken van GCM-Sdk's en-bibliotheken.

Als u een nieuw FCM-project maakt en niet koppelt aan het bestaande GCM-project en u Notification Hubs met het nieuwe FCM-geheim bijwerkt, verliest u de mogelijkheid om meldingen naar uw huidige app-installaties te pushen omdat de nieuwe FCM-sleutel geen koppeling naar het oude GCM-project bevat.

**V:** Waarom krijg ik dit e-mail bericht over oude GCM-eind punten die worden gebruikt? Wat moet ik doen?

**A:** Nul. We zijn gemigreerd naar de nieuwe eind punten en worden binnenkort voltooid, dus u hoeft niets te wijzigen. Er is geen fout opgetreden, onze ene gemist eind punt heeft simpelweg waarschuwings berichten veroorzaakt van Google.

**V:** Hoe kan ik overstappen op de nieuwe FCM-Sdk's en-bibliotheken zonder bestaande gebruikers te verbreken?

A: op elk moment een upgrade uitvoeren. Google heeft nog geen afschaffing van bestaande GCM-Sdk's en-bibliotheken aangekondigd. Om ervoor te zorgen dat u geen push meldingen naar uw bestaande gebruikers verbreekt, moet u ervoor zorgen dat u het nieuwe Firebase-project maakt dat u aan uw bestaande GCM-project koppelt. Dit zorgt ervoor dat nieuwe Firebase geheimen werken voor gebruikers die de oudere versies van uw app met GCM Sdk's en bibliotheken gebruiken, evenals nieuwe gebruikers van uw app met FCM Sdk's en bibliotheken.

**V:** Wanneer kan ik nieuwe FCM-functies en-schema's voor mijn meldingen gebruiken?

**A:** Nadat we een update voor onze API en Sdk's hebben gepubliceerd, blijven deze goed afgestemd. we verwachten dat we in de komende maanden iets voor u hebben.
