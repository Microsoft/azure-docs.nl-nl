---
title: Micro soft Defender voor Office 365 (voorheen Office 365 ATP)-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Opname van micro soft Defender voor Office 365-gegevens in azure Sentinel om inzicht te krijgen in en ontwikkel scenario's voor automatische reacties.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: d5140bcd85606213f00185d4ba1f50dbe70dba63
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655491"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>Verbinding maken met waarschuwingen van micro soft Defender voor Office 365 

> [!IMPORTANT]
>
> - **Micro soft Defender voor office 365** was voorheen bekend als **Office 365 Advanced Threat Protection (ATP)**.
>
>     Mogelijk ziet u de oude naam die nog steeds wordt gebruikt in het product (inclusief de gegevens connector in azure Sentinel) gedurende een bepaalde periode.
>
> - De opname van micro soft Defender voor Office 365-waarschuwingen is momenteel beschikbaar als open bare preview. Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
 
[Micro soft Defender voor Office 365](/office365/servicedescriptions/office-365-advanced-threat-protection-service-description) beveiligt uw organisatie tegen nul dagen en andere geavanceerde bedreigingen die worden veroorzaakt door onbekende malware in e-mail berichten, koppelingen naar schadelijke URL en samenwerkings hulpprogramma's. Door micro soft Defender voor Office 365-waarschuwingen op te nemen in azure Sentinel, kunt u gebruikmaken van informatie over e-mail, het delen van bestanden en bedreigingen op basis van URL'S in uw beveiligings activiteiten. U kunt vervolgens de beveiligings gebeurtenissen in uw organisatie uitgebreid analyseren en playbooks bouwen voor een effectief en direct antwoord.

De connector importeert de volgende waarschuwingen:

- Er is een mogelijk schadelijke URL gedetecteerd 

- E-mail berichten met malware verwijderd na levering

- E-mail berichten met phishing-Url's die na de levering zijn verwijderd 

- E-mail bericht gerapporteerd door gebruiker als malware of phishing 

- Verdachte e-mail voor het verzenden van patronen gedetecteerd 

- Gebruikers-e-mail verzenden is beperkt 

Deze waarschuwingen kunnen worden gezien door Office-klanten in het **beveiligings-en nalevings centrum van Office**.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte wanneer u de connector inschakelt.

- U moet een globale beheerder of een beveiligings beheerder zijn op de Azure-Tenant van de Sentinel-werk ruimte.

- U moet beschikken over een geldige licentie voor [office 365 ATP-abonnement 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) (opgenomen in Office 365 E5, Office 365 A5 en Microsoft 365 E5-licenties en kunnen afzonderlijk worden aangeschaft). 

## <a name="connect-to-microsoft-defender-for-office-365"></a>Verbinding maken met micro soft Defender voor Office 365

Als micro soft Defender voor Office 365 wordt geïmplementeerd en als er beleids regels zijn geconfigureerd, kunnen de waarschuwingen eenvoudig worden opgenomen in azure Sentinel.

1. Selecteer in azure Sentinel **Data connectors** in het navigatie menu.

1. Selecteer **micro soft Defender voor Office 365** (kan nog steeds *Office 365 Advanced Threat Protection* worden genoemd) in de connectors galerie en selecteer **pagina connector openen**.

1. Klik in de sectie **configuratie** op **verbinding maken**. 

1. Klik in de sectie **incidenten maken** op **inschakelen**.

1. Als u het relevante schema voor het opvragen van Office 365 ATP-waarschuwingen wilt gebruiken, zoekt u naar **SecurityAlert** en geeft u de naam van de **provider** op als **OATP**.

1. Selecteer het tabblad **volgende stappen** voor het weer geven en gebruiken van de query voorbeelden en analyse regel sjablonen die zijn gebundeld met de micro soft Defender voor Office 365-connector.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u met micro soft Defender voor Office 365 verbinding maakt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](./tutorial-detect-threats-built-in.md).