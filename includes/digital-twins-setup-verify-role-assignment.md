---
author: baanders
description: include-bestand voor het controleren van de roltoewijzing in de Azure Digital Apparaatdubbels-installatie
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b4dfd154ff3fb7af48ef43b0a1dca168c5a93481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "92489032"
---
Een manier om te controleren of u de roltoewijzing hebt ingesteld, is door de roltoewijzingen voor het Azure Digital Apparaatdubbels-exemplaar in het [Azure Portal](https://portal.azure.com)weer te geven. Ga naar uw Azure Digital Apparaatdubbels-exemplaar in de Azure Portal (u kunt het vinden op de pagina met [digitale apparaatdubbels-instanties van Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) of zoek de naam in de portal-zoek balk).

Bekijk vervolgens alle toegewezen rollen onder *toegangs beheer (IAM) > roltoewijzingen*. De gebruiker moet in de lijst worden weer gegeven met een rol van de eigenaar van de *Azure Digital apparaatdubbels-gegevens*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Weer gave van de roltoewijzingen voor een Azure Digital Apparaatdubbels-exemplaar in Azure Portal":::