---
title: Azure peering service-verbinding
description: Meer informatie over Microsoft Azure peering service-verbinding
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0540fa9c7a29456aaf343adce83509b2d0e390e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91398917"
---
# <a name="peering-service-connection"></a>Peering service-verbinding

Een verbinding verwijst doorgaans naar een set met logische gegevens, waarbij een peering-service wordt geïdentificeerd. Dit wordt gedefinieerd door de volgende kenmerken op te geven:

- Logische naam
- Connectiviteits partner
- Fysieke locatie van klant
- IP-voor voegsels

De klant kan een enkele verbinding of meerdere verbindingen tot stand brengen conform de vereiste. Een verbinding wordt ook gebruikt als een eenheid voor het verzamelen van telemetrie. Als u bijvoorbeeld wilt dat telemetrie-waarschuwingen worden gewaarschuwd, moet de klant de verbinding definiëren die wordt bewaakt.

> [!Note]
> Wanneer u zich aanmeldt voor de peering-service, analyseren we uw Windows-en Microsoft 365-telemetrie om u latentie metingen te bieden voor de geselecteerde voor voegsels.
>Voor meer informatie over verbindings-telemetrie, verwijzen wij u naar de [telemetrie van de peering-service verbinding](connection-telemetry.md).
>

>## <a name="how-to-register-a-connection"></a>Hoe kan ik een verbinding registreren?

**Scenario** : Stel dat een filiaal is verdeeld over verschillende geografische locaties, zoals wordt weer gegeven in de afbeelding hieronder. Hier moet de klant een logische naam, de naam van de service provider (SP), de fysieke locatie van de klant en IP-voor voegsels opgeven die zijn gekoppeld aan één verbinding (eigendom van de klant of worden toegewezen door de service provider). Dit proces moet worden herhaald voor het registreren van de peering-service voor afzonderlijke geo-redundante verbindingen.

![Geo-redundante verbindingen](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> Status niveau: filtratie wordt overwogen voor de fysieke locatie van de klant wanneer de verbinding geografisch in de Verenigde Staten.
>

## <a name="next-steps"></a>Volgende stappen

Zie [peering service registreren met behulp van de Azure Portal](azure-portal.md)voor meer informatie over het registreren van een peering-service verbinding.

Zie [Peering Service-verbindingstelemetrie](connection-telemetry.md) voor meer informatie over de Peering Service-verbindingstelemetrie.

Zie [Verbindingstelemetrie meten](measure-connection-telemetry.md) om telemetrie te meten.
