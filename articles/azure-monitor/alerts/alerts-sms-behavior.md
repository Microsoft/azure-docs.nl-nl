---
title: Gedrag van SMS-waarschuwingen in actie groepen
description: SMS-bericht indeling en reageren op SMS-berichten om u af te melden, u opnieuw aan te melden of om hulp te vragen.
author: dkamstra
ms.author: dukek
services: monitoring
ms.topic: conceptual
ms.date: 02/16/2018
ms.subservice: alerts
ms.openlocfilehash: 3ca776a1869874042a6a97cdd59dc00d3a917d33
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609573"
---
# <a name="sms-alert-behavior-in-action-groups"></a>Gedrag van waarschuwingen via sms in actiegroepen

## <a name="overview"></a>Overzicht 
Met actie groepen kunt u een lijst met acties configureren. Deze groepen worden gebruikt bij het definiëren van waarschuwingen. controleren of een bepaalde actie groep wordt gewaarschuwd wanneer de waarschuwing wordt geactiveerd. Een van de ondersteunde acties is SMS; SMS-meldingen bieden ondersteuning voor bidirectionele communicatie. Een gebruiker kan reageren op een SMS om:

- **Afmelden bij waarschuwingen:** Een gebruiker kan zich afmelden voor alle SMS-waarschuwingen voor alle actie groepen of voor één actie groep.
- Opnieuw **Abonneren op waarschuwingen:** Een gebruiker kan opnieuw worden geabonneerd op alle SMS-waarschuwingen voor alle actie groepen of in één actie groep.  
- **Hulp vragen:** Een gebruiker kan om meer informatie vragen over de SMS. Deze worden omgeleid naar dit artikel.

In dit artikel wordt het gedrag van de SMS-waarschuwingen beschreven en de reactie acties die de gebruiker kan uitvoeren op basis van de land instelling van de gebruiker:

## <a name="receiving-an-sms-alert"></a>Een SMS-bericht ontvangen
Een SMS-ontvanger die is geconfigureerd als onderdeel van een actie groep ontvangt een SMS wanneer een waarschuwing wordt geactiveerd. De SMS bevat de volgende informatie:
* De kortenaam van de actie groep waaraan deze waarschuwing is verzonden
* Titel van de waarschuwing

| REPLY | Description |
| ----- | ----------- |
| SCHA `<Action Group Short name>` | Schakelt verdere SM'S uit van de actie groep |
| KUNT `<Action Group Short name>` | Hiermee schakelt u SMS opnieuw in voor de actie groep |
| TAB | Schakelt verdere SM'S uit van alle actie groepen |
| STARTEN | Hiermee schakelt u SMS van alle actie groepen opnieuw in |
| HELP | Er wordt een antwoord naar de gebruiker verzonden met een koppeling naar dit artikel. |

>[!NOTE]
>Als een gebruiker zich afmeldt bij SMS-waarschuwingen, maar vervolgens wordt toegevoegd aan een nieuwe actie groep; ze ontvangen SMS-waarschuwingen voor die nieuwe actie groep, maar blijven afgemeld bij alle vorige actie groepen.

## <a name="next-steps"></a>Volgende stappen
Bekijk een [overzicht van waarschuwingen voor activiteiten logboeken](../platform/alerts-overview.md) en lees hoe u waarschuwingen ontvangt  
Meer informatie over de [limiet voor SMS-tarieven](alerts-rate-limiting.md)  
Meer informatie over [actie groepen](../platform/action-groups.md)

