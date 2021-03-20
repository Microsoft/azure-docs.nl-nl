---
title: Toegang tot beheer van Azure beheren met voorwaardelijke toegang in azure AD
description: Meer informatie over het gebruik van voorwaardelijke toegang in azure AD om de toegang tot Azure management te beheren.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 2a52635dbaa7a76034f3a535b099320a901e8c07
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83758772"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Toegang tot beheer van Azure beheren met voorwaardelijke toegang

> [!CAUTION]
> Zorg ervoor dat u begrijpt hoe voorwaardelijke toegang werkt voordat u een beleid instelt om de toegang tot Azure management te beheren. Zorg ervoor dat u geen voor waarden maakt die uw eigen toegang tot de portal kunnen blok keren.

Voorwaardelijke toegang in Azure Active Directory (Azure AD) beheert de toegang tot Cloud-apps op basis van specifieke voor waarden die u opgeeft. Als u toegang wilt toestaan, maakt u beleid voor voorwaardelijke toegang om toegang toe te staan of te blok keren op basis van het feit of aan de vereisten in het beleid wordt voldaan. 

Normaal gesp roken gebruikt u voorwaardelijke toegang om de toegang tot uw Cloud-apps te beheren. U kunt ook beleids regels instellen om de toegang tot het beheer van Azure te beheren op basis van bepaalde voor waarden (zoals aanmeldings risico, locatie of apparaat) en om vereisten als multi-factor Authentication af te dwingen.

Als u een beleid voor Azure-beheer wilt maken, selecteert u **Microsoft Azure beheer** onder **Cloud-apps** bij het kiezen van de app waarop u het beleid wilt Toep assen.

![Voorwaardelijke toegang voor Azure-beheer](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Het beleid dat u maakt, is van toepassing op alle Azure Management-eind punten, met inbegrip van het volgende:

- Azure Portal
- Azure Resource Manager provider
- Klassieke service management-Api's
- Azure PowerShell
- Beheerders portal voor Visual Studio-abonnementen
- Azure DevOps
- Azure Data Factory Portal

Houd er rekening mee dat het beleid van toepassing is op Azure PowerShell, waarmee de Azure Resource Manager-API wordt aangeroepen. Het is niet van toepassing op [Azure AD Power shell](/powershell/azure/active-directory/install-adv2), die Microsoft Graph aanroept.

Zie voor meer informatie over het instellen van een voorbeeld beleid voor het inschakelen van voorwaardelijke toegang voor Microsoft Azure beheer het artikel [voorwaardelijke toegang: MFA vereisen voor Azure-beheer](../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md).
