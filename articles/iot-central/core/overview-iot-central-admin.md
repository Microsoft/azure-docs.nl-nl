---
title: Azure IoT Central beheerdershandleiding
description: Azure IoT Central is een IoT-toepassingsplatform waarmee het eenvoudiger is om IoT-oplossingen te maken. In dit artikel vindt u een overzicht van de beheerdersrol in IoT Central.
author: philmea
ms.author: philmea
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 658ebe027565a3acaf427a7b3dbf3963701069d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868641"
---
# <a name="iot-central-administrator-guide"></a>IoT Central beheerdershandleiding

*Dit artikel is van toepassing op beheerders.*

Met een IoT Central-toepassing kunt u miljoenen apparaten tijdens hun levenscyclus bewaken en beheren. Deze handleiding is voor beheerders die IoT Central beheren.

In IoT Central een beheerder:

- Beheert gebruikers en rollen in de toepassing.
- Beheert beveiliging zoals apparaatverificatie.
- Hiermee configureert u toepassingsinstellingen.
- Toepassingen worden geupgraded.
- Exporteert en deelt toepassingen.
- Controleert de toepassingstoestand.

## <a name="users-and-roles"></a>Gebruikers en rollen

IoT Central maakt gebruik van een op rollen gebaseerd toegangsbeheersysteem om gebruikersmachtigingen binnen een toepassing te beheren. IoT Central heeft drie ingebouwde rollen voor beheerders, oplossingsbouwers en operators. Een beheerder kan aangepaste rollen maken met specifieke sets machtigingen. Een beheerder is verantwoordelijk voor het toevoegen van gebruikers aan een toepassing en het toewijzen ervan aan rollen.

Zie Manage users and roles in your IoT Central application (Gebruikers en [rollen beheren in uw IoT Central voor meer informatie.](howto-manage-users-roles.md)

## <a name="application-security"></a>Toepassingsbeveiliging

Apparaten die verbinding maken met uw IoT Central gebruiken doorgaans X.509-certificaten of SHARED Access Signatures (SAS) als referenties. De beheerder beheert de groepscertificaten of sleutels waaruit de apparaatreferenties zijn afgeleid.

Zie [X.509-groepsinschrijving,](concepts-get-connected.md#x509-group-enrollment)SAS-groepsinschrijving en [X.509-apparaatcertificaten](how-to-roll-x509-certificates.md)rollen voor meer informatie. [](concepts-get-connected.md#sas-group-enrollment)

De beheerder kan ook de API-tokens maken en beheren die een clienttoepassing gebruikt om te verifiëren bij IoT Central toepassing. Clienttoepassingen gebruiken de REST API om te communiceren met IoT Central.

## <a name="configure-an-application"></a>Een toepassing configureren

De beheerder kan het gedrag en de weergave van een IoT Central configureren. Raadpleeg voor meer informatie:

- [Toepassingsnaam en URL wijzigen](howto-administer.md#change-application-name-and-url)
- [De gebruikersinterface aanpassen](howto-customize-ui.md)
- [Een toepassing verplaatsen naar een ander prijsplan](howto-view-bill.md)
- [Bestandsuploads configureren](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Een toepassing exporteren

Een beheerder kan:

- Maak een kopie van een toepassing als u alleen een dubbele kopie van uw toepassing nodig hebt. U hebt bijvoorbeeld een dubbele kopie nodig voor het testen.
- Maak een toepassingssjabloon op basis van een bestaande toepassing als u meerdere kopieën wilt maken.

Zie Uw [Azure IoT-toepassing](howto-use-app-templates.md)exporteren voor meer informatie.

## <a name="migrate-to-a-new-version"></a>Migreren naar een nieuwe versie

Een beheerder kan een toepassing migreren naar een nieuwere versie. Momenteel is een nieuw gemaakte toepassing een V3-toepassing. Een beheerder moet mogelijk een V2-toepassing migreren naar een V3-toepassing.

Zie Uw [V2-IoT Central migreren naar V3 voor meer informatie.](howto-migrate.md)

## <a name="monitor-application-health"></a>De status van de toepassing bewaken

Een beheerder kan de IoT Central gebruiken om de status van verbonden apparaten en de status van het uitvoeren van gegevensexports te beoordelen.

Om de metrische gegevens weer te geven, kan een beheerder grafieken in de Azure Portal, een REST API of PowerShell- of Azure CLI-query's gebruiken.

Zie Monitor the [overall health of an IoT Central application (De algehele status van een toepassing IoT Central bewaken) voor meer informatie.](howto-monitor-application-health.md)

## <a name="tools"></a>Hulpprogramma's

Veel van de hulpprogramma's die u als beheerder gebruikt, zijn beschikbaar in **de sectie Beheer** van elke IoT Central toepassing. U kunt ook de volgende hulpprogramma's gebruiken om een aantal beheertaken uit te voeren:

- [Azure-CLI](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Azure-portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw Azure IoT Central-toepassing beheert, is de voorgestelde volgende stap meer informatie over Gebruikers en rollen [beheren](howto-manage-users-roles.md) in Azure IoT Central.
