---
title: 'Azure AD Connect Cloud-inrichtings agent: automatische upgrade | Microsoft Docs'
description: In dit artikel wordt de ingebouwde functie voor automatische upgrades in de Azure AD Connect Cloud inrichtings agent beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26c0b47dd358826b843143aaf23c469d852e93b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613794"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect Cloud-inrichtings agent: automatische upgrade

Zorg ervoor dat uw Azure Active Directory (Azure AD) Connect-installatie van de Cloud-inrichtings agent altijd up-to-date is met de functie voor automatische upgrades.

De agent wordt hier geïnstalleerd: ' Program files\Azure AD Connect inrichtings Agent\AADConnectProvisioningAgent.exe '

Als u uw versie wilt controleren, klikt u met de rechter muisknop op het uitvoer bare bestand en selecteert u eigenschappen en vervolgens Details.

![Versie van agent bestand](media/how-to-automatic-upgrade/agent-1.png)

De Updater voor de agent is hier geïnstalleerd: "Program files\Azure AD Connect inrichtings agent Updater\AzureADConnectAgentUpdater.exe"

Als u uw versie wilt controleren, klikt u met de rechter muisknop op het uitvoer bare bestand en selecteert u eigenschappen en vervolgens Details.

![Versie van agent updater](media/how-to-automatic-upgrade/agent-2.png)

## <a name="uninstall-the-agent"></a>Agent verwijderen
Als u de agent wilt verwijderen, gaat u naar **een programma verwijderen of wijzigen** en verwijdert u het volgende:

- **Microsoft Azure AD Connect Agent Updater**
- **Microsoft Azure AD Connect Provisioning Agent**
- **Microsoft Azure AD Connect Provisioning Agent Package**

![Agent verwijderen](media/how-to-automatic-upgrade/agent-3.png)

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect--cloudsynchronisatie?](what-is-cloud-sync.md)

