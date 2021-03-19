---
title: Azure Functions ontwerpen voor identieke invoer
description: Azure Functions bouwen om te worden idempotent
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.openlocfilehash: 15af60ac5a862e6fb20e65ba6fbb92482420b7c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74226860"
---
# <a name="designing-azure-functions-for-identical-input"></a>Azure Functions ontwerpen voor identieke invoer

De realiteit van op gebeurtenissen gebaseerde architectuur en op berichten gebaseerde architecturen bepaalt dat er identieke aanvragen moeten worden geaccepteerd terwijl de integriteit van gegevens en de stabiliteit van het systeem behouden blijven.

Als u wilt illustreren, kunt u de knop voor het aanroepen van een lift beschouwen. Wanneer u op de knop drukt, wordt het oplicht en wordt een lift naar uw vloer verzonden. Een paar minuten later neemt iemand anders mee aan de lobby. Deze persoon komt bij u geglim lach en drukt een tweede keer op de verlichtings knop. U krijgt een glim lach en chuckle als u eraan wordt herinnerd dat de opdracht voor het aanroepen van een lift idempotent is.

Als u op een lift oproep knop klikt, is de tweede, derde of vierde tijd niet van invloed op het uiteindelijke resultaat. Wanneer u op de knop drukt, ongeacht het aantal keren dat de lift naar uw vloer wordt verzonden. Idempotent-systemen, zoals de Lift, resulteren in hetzelfde resultaat, ongeacht hoe vaak identieke opdrachten worden gegeven.

Bij het bouwen van toepassingen moet u rekening houden met de volgende scenario's:

- Wat gebeurt er als uw inventarisatie controleprogramma meerdere keren probeert om hetzelfde product te verwijderen?
- Hoe gedraagt uw HRM-toepassing zich als er meerdere aanvragen zijn voor het maken van een werknemers record voor dezelfde persoon?
- Waar gaat het geld uit als uw bank-app 100 aanvragen voor het maken van dezelfde intrekking krijgt?

Er zijn veel contexten waarbij aanvragen naar een functie identieke opdrachten kunnen ontvangen. Enkele situaties zijn onder andere:

- Beleid voor opnieuw proberen, waarbij dezelfde aanvraag meermaals wordt verzonden
- In cache geplaatste opdrachten die aan de toepassing zijn gespeld
- Toepassings fouten bij het verzenden van meerdere identieke aanvragen

Ter bescherming van gegevens integriteit en systeem status bevat een idempotent-toepassing logica die mogelijk het volgende gedrag kan bevatten:

- Controleren of er gegevens bestaan voordat u een verwijdering probeert uit te voeren
- Controleren of er al gegevens bestaan voordat u een actie maken uitvoert
- Het afstemmen van logica die uiteindelijke consistentie in gegevens maakt
- Gelijktijdigheids besturings elementen
- Duplicaten detectie
- Validatie van gegevens vernieuwing
- Logica voor het verifiëren van invoer gegevens

Uiteindelijk idempotentie wordt bereikt door ervoor te zorgen dat een bepaalde actie kan worden uitgevoerd.
