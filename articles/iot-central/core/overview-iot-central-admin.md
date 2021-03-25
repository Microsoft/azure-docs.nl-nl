---
title: Beheerders handleiding voor Azure IoT Central
description: Azure IoT Central is een IoT-toepassingsplatform waarmee het eenvoudiger is om IoT-oplossingen te maken. Dit artikel bevat een overzicht van de rol beheerder in IoT Central.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 16a8aecae70d73399acb3878d7088e5086c053a1
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110488"
---
# <a name="iot-central-administrator-guide"></a>Beheerders handleiding IoT Central

*Dit artikel is van toepassing op beheerders.*

Met een IoT Central-toepassing kunt u miljoenen apparaten tijdens hun levenscyclus bewaken en beheren. Deze hand leiding is voor beheerders die IoT Central toepassingen beheren.

Een beheerder in IoT Central:

- Beheert gebruikers en rollen in de toepassing.
- Beheert beveiliging, zoals verificatie van apparaten.
- Hiermee configureert u toepassings instellingen.
- Hiermee worden toepassingen bijgewerkt.
- Exporteert en deelt toepassingen.
- Controleert de status van de toepassing.

## <a name="users-and-roles"></a>Gebruikers en rollen

IoT Central gebruikt een op rollen gebaseerd toegangscontrole systeem voor het beheer van gebruikers machtigingen binnen een toepassing. IoT Central heeft drie ingebouwde rollen voor beheerders, ontwikkelers van oplossingen en Opera tors. Een beheerder kan aangepaste rollen maken met specifieke sets machtigingen. Een beheerder is verantwoordelijk voor het toevoegen van gebruikers aan een toepassing en het toewijzen van deze aan rollen.

Zie [gebruikers en rollen beheren in uw IOT Central-toepassing](howto-manage-users-roles.md)voor meer informatie.

## <a name="application-security"></a>Toepassingsbeveiliging

Apparaten die verbinding maken met uw IoT Central-toepassing gebruiken meestal X. 509-certificaten of SAS (Shared Access signatures) als referenties. De beheerder beheert de groeps certificaten of sleutels waarvan de referenties van het apparaat zijn afgeleid.

Zie voor meer informatie [X. 509 groep](concepts-get-connected.md#x509-group-enrollment), registratie van [SAS-Groep](concepts-get-connected.md#sas-group-enrollment)en [het Roll X. 509-apparaat certificaten](how-to-roll-x509-certificates.md).

De beheerder kan ook de API-tokens maken en beheren die een client toepassing gebruikt om te verifiëren met uw IoT Central-toepassing. Client toepassingen gebruiken de REST API om te communiceren met IoT Central.

## <a name="configure-an-application"></a>Een toepassing configureren

De beheerder kan het gedrag en de weer gave van een IoT Central toepassing configureren. Raadpleeg voor meer informatie:

- [Toepassings naam en-URL wijzigen](howto-administer.md#change-application-name-and-url)
- [De gebruikersinterface aanpassen](howto-customize-ui.md)
- [Een toepassing verplaatsen naar een ander prijs plan](howto-view-bill.md)
- [Uploads van bestanden configureren](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Een toepassing exporteren

Een beheerder kan het volgende doen:

- Maak een kopie van een toepassing als u een kopie van uw toepassing hebt die u wilt dupliceren. Het is bijvoorbeeld mogelijk dat u een kopie van een duplicaat nodig hebt om te testen.
- Maak een toepassings sjabloon op basis van een bestaande toepassing als u van plan bent meerdere kopieën te maken.

Zie [uw Azure IOT-toepassing exporteren](howto-use-app-templates.md)voor meer informatie.

## <a name="migrate-to-a-new-version"></a>Migreren naar een nieuwe versie

Een-beheerder kan een toepassing migreren naar een nieuwere versie. Op dit moment is een nieuwe toepassing een v3-toepassing. Een-beheerder moet mogelijk een v2-toepassing migreren naar een v3-toepassing.

Zie [uw V2 IOT Central-toepassing migreren naar v3](howto-migrate.md)voor meer informatie.

## <a name="monitor-application-health"></a>De status van de toepassing bewaken

Een beheerder kan IoT Central metrische gegevens gebruiken om de status van verbonden apparaten en de status van het uitvoeren van gegevens uitvoer te beoordelen.

Een beheerder kan de metrische gegevens weer geven door grafieken te gebruiken in de Azure Portal, een REST API of Power shell-of Azure CLI-query's.

Zie [de algehele status van een IOT Central toepassing bewaken](howto-monitor-application-health.md)voor meer informatie.

## <a name="tools"></a>Hulpprogramma's

Veel van de hulpprogram ma's die u als beheerder gebruikt, zijn beschikbaar in de sectie **beheer** van elke IOT Central-toepassing. U kunt ook de volgende hulpprogram ma's gebruiken om een aantal beheer taken uit te voeren:

- [Azure-CLI](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Azure-portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw Azure IoT Central-toepassing beheert, is de voorgestelde volgende stap meer informatie over het [beheren van gebruikers en rollen](howto-manage-users-roles.md) in azure IOT Central.
