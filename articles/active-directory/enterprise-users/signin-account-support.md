---
title: Worden Microsoft-accounts geaccepteerd op de aanmeldingspagina van Azure Active Directory? | Microsoft Docs
description: Hoe schermberichten het opzoeken van gebruikersnamen weergeven tijdens het aanmelden
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f580e214017a0a599acd290f15f6be8e0a478d22
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95490745"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Aanmeldingsopties voor Microsoft-accounts in Azure Active Directory

De Microsoft 365-aanmeldingspagina voor Azure Active Directory (Azure AD) biedt ondersteuning voor werk- of schoolaccounts en Microsoft-accounts, maar afhankelijk van de situatie van de gebruiker kan dit ook een van de twee of beide zijn. De aanmeldingspagina van Azure Active Directory biedt bijvoorbeeld ondersteuning voor:

* Apps die aanmeldingen van beide accounttypen accepteren
* Organisaties die gasten accepteren

## <a name="identification"></a>Identificatie
U kunt zien of Microsoft-accounts door de aanmeldingspagina van uw organisatie worden ondersteund door naar de hinttekst in het veld Gebruikersnaam te kijken. Als bij de hinttekst 'E-mailadres, telefoonnummer of Skype' wordt vermeld, worden Microsoft-accounts door de aanmeldingspagina ondersteund.

![Verschillen tussen aanmeldingspagina's van accounts](./media/signin-account-support/ui-prompt.png)

[Aanvullende aanmeldingsopties werken alleen voor persoonlijke Microsoft-accounts](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) maar kunnen niet worden gebruikt voor aanmelden bij resources van werk- of schoolaccounts.

## <a name="next-steps"></a>Volgende stappen

[Uw huisstijl voor aanmelden aanpassen](../fundamentals/add-custom-domain.md)