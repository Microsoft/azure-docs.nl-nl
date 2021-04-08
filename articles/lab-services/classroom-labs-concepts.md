---
title: Labs-concepten-Azure Lab Services | Microsoft Docs
description: Leer de basis concepten van Lab-Services en hoe u hiermee eenvoudig Labs kunt maken en beheren.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa3a8dad195b4b3cbf0786c8923c8b330d148898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435515"
---
# <a name="labs-concepts"></a>Labconcepten

De volgende lijst bevat concepten en definities voor de belangrijkste Lab-Services:

## <a name="quota"></a>Quota

Quota is de tijds limiet (in uren) die een docent kan instellen voor een student om een Lab-VM te gebruiken. De waarde kan worden ingesteld op 0 of een bepaald aantal uren. Als het quotum is ingesteld op 0, kan een student alleen de virtuele machine gebruiken als er een planning wordt uitgevoerd of wanneer een docent de virtuele machine hand matig inschakelt voor de student.  

De quota uren worden geteld wanneer de student de VM van het lab zelf start.  Als een docent de Lab-VM voor een student hand matig start, worden er geen quota uren voor die student gebruikt.

## <a name="schedules"></a>Schema's

Schema's zijn de tijd sleuven die een docent kan maken voor de klasse, zodat de student-Vm's beschikbaar zijn voor klassetijd.  Schema's kunnen een eenmalige of een terugkerende periode zijn.  Quota-uren worden niet gebruikt wanneer een schema wordt uitgevoerd.

Er zijn drie soorten planningen: standaard, alleen starten en alleen stoppen.

- **Standard**.  Met deze planning worden alle student-Vm's gestart op de opgegeven begin tijd en worden alle student-Vm's afgesloten op de opgegeven eind tijd.
- **Alleen starten**.   In dit schema worden alle Vm's van de student op het opgegeven tijdstip gestart.  Student-Vm's worden niet gestopt totdat een student de VM stopt via de Azure Lab Services portal of een planning met alleen een stop.
- **Alleen stoppen**.  Met deze planning worden alle Vm's van de student op het opgegeven tijdstip gestopt.  

## <a name="template-virtual-machine"></a>Virtuele machine van sjabloon

Een sjabloon van een virtuele machine in een Lab is een basis installatie kopie van een virtuele machine waarvan de virtuele machines van alle gebruikers worden gemaakt. Trainers/Lab-makers stellen de virtuele machine van de sjabloon in en configureren deze met de software die ze nodig hebben om deel nemers te trainen om Labs uit te voeren. Wanneer u een sjabloon-VM publiceert, worden in Azure Lab Services Lab-Vm's gemaakt of bijgewerkt op basis van de VM van de sjabloon.

## <a name="user-profiles"></a>Gebruikersprofielen

In dit artikel worden de verschillende gebruikersprofielen in Azure Lab Services beschreven.

### <a name="lab-account-owner"></a>Labeigenaar

Een IT-beheerder van de cloud resources van de organisatie, die eigenaar is van het Azure-abonnement, fungeert doorgaans als eigenaar van een Lab-account en voert de volgende taken uit:

- Een lab-account instellen voor uw organisatie.
- Beleidsregels beheren en configureren voor alle labs.
- Machtigingen verlenen aan personen binnen de organisatie voor het maken van een lab onder het lab-account.

### <a name="educator"></a>Docent

Normaal gesp roken maken gebruikers zoals een docent of een online cursus Labs onder een Lab-account. Een docent voert de volgende taken uit:

- Leslokaallab maken.
- Virtuele machines maken in het lab.
- De juiste software installeren op virtuele machines.
- Instellen wie toegang heeft tot het lab.
- Registratiekoppeling naar het lab doorgeven aan studenten.

### <a name="student"></a>Student

Een student voert de volgende taken uit:

- De registratiekoppeling volgen die is verstuurd door een labmaker om zich te registreren bij het lab.
- Verbinding maken met een virtuele machine in het lab en deze gebruiken voor het maken van klassikale oefeningen, huiswerk en projecten.

## <a name="next-steps"></a>Volgende stappen

Aan de slag met het instellen van een labaccount dat is vereist voor het maken van een leslokaallab met Azure Lab Services:

- [Een lab-account maken](tutorial-setup-lab-account.md)
