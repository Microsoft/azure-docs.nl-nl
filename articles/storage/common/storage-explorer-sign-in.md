---
title: Meld u aan bij Azure Storage Explorer | Microsoft Docs
description: Documentatie over aanmelden bij Azure Storage Explorer
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: chuye
ms.openlocfilehash: ef02842d189746a1801d97f91b92f249947c832d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568700"
---
# <a name="sign-in-to-storage-explorer"></a>Meld u aan bij Storage Explorer

Aanmelden is de aanbevolen manier om toegang te krijgen tot uw Azure Storage-resources met Storage Explorer. Door u aan te melden, profiteert u van door Azure AD-ondersteuning ondersteuningsmachtigingen, zoals RBAC- en Gen2 POSIX-ACL's. 

## <a name="how-to-sign-in"></a>Aanmelden

Als u zich wilt aanmelden Storage Explorer, opent u het **dialoogvenster Verbinding maken.** U kunt het dialoogvenster **Verbinding maken** openen via de verticale werkbalk links of door te klikken op **Account toevoegen...** in **het deelvenster Account.**

Zodra het dialoogvenster is geopend, kiest **u Abonnement** als het type resource dat u wilt verbinden en klikt u op **Volgende.**

U moet nu kiezen bij welke Azure-omgeving u zich wilt aanmelden. U kunt kiezen uit een van de bekende omgevingen, zoals Azure of Azure China, of u kunt uw eigen omgeving toevoegen. Nadat u uw omgeving hebt geselecteerd, klikt u op **Volgende.**

Op dit moment wordt de **standaardwebbrowser** van uw besturingssysteem geopend en wordt er een aanmeldingspagina geopend. Voor de beste resultaten laat u dit browservenster geopend zolang u een Storage Explorer of ten minste totdat u alle verwachte MFA hebt uitgevoerd. Wanneer u klaar bent met aanmelden, kunt u terugkeren naar Storage Explorer.

## <a name="managing-accounts"></a>Accounts beheren

U kunt Azure-accounts die u hebt aangemeld, beheren en verwijderen vanuit het **deelvenster Account.** U kunt het deelvenster **Account openen door** te klikken op de knop **Accounts** beheren op de verticale werkbalk links.

In het **deelvenster Account** ziet u alle accounts die u hebt aangemeld. Onder elk account is het volgende te zien:
- De tenants waar het account bij hoort
- Voor elke tenant zijn de abonnementen tot wie u toegang hebt

Standaard wordt u Storage Explorer alleen bij uw thuis-tenant. Als u abonnementen en resources van een andere tenant wilt weergeven, moet u die tenant activeren. Als u een tenant wilt activeren, vink u het selectievakje er naast aan. Wanneer u klaar bent met het werken met een tenant, kunt u het selectievakje uitschakelen om deze te deactiveren. U kunt uw thuis-tenant niet deactiveren.

Nadat u een tenant hebt geactiveerd, moet u mogelijk uw referenties opnieuw ingeven voordat Storage Explorer abonnementen kunnen laden of toegang kunnen krijgen tot resources van de tenant. Het opnieuw inschakelen van uw referenties gebeurt meestal vanwege een beleid voor voorwaardelijke toegang (CA), zoals Meervoudige verificatie (MFA). En hoewel u mogelijk al MFA hebt uitgevoerd voor een andere tenant, moet u dit mogelijk nog wel opnieuw doen. Als u uw referenties opnieuw wilt ingeven, klikt u op **Referenties opnieuw ingeven...**. U kunt ook klikken op **Foutdetails...** om precies te zien waarom het laden van abonnementen is mislukt.

Zodra uw abonnementen zijn geladen, kunt u kiezen welke abonnementen u wilt in-/uitfilteren door de selectievakjes in of uit te checken.

Als u uw hele Azure-account wilt verwijderen, klikt u op **Verwijderen** naast het account.

## <a name="changing-where-sign-in-happens"></a>Wijzigen waar aanmelding gebeurt

De standaard aanmelding vindt standaard in de standaardwebbrowser van **uw besturingssysteem uit.** Aanmelden met uw standaardwebbrowser stroomlijnt hoe u toegang krijgt tot resources die zijn beveiligd via CA-beleid, zoals MFA. Als u zich om de  een of andere reden niet kunt aanmelden met de standaardwebbrowser van uw besturingssysteem, kunt u wijzigen waar of hoe Storage Explorer aanmelding uitvoert.

Zoek **onder Settings**  >  **Application**  >  **Sign-in** naar de instelling Sign in **with.** Er zijn drie opties:
- **Standaardwebbrowser:** aanmelden vindt in de standaardwebbrowser van uw **besturingssysteem uit.** Deze optie wordt aanbevolen.
- **Geïntegreerde aanmelding:** aanmelden vindt in een Storage Explorer plaatsvinden. Deze optie kan handig zijn als u zich probeert aan te melden met meerdere Microsoft-accounts (MSP's) tegelijk. Mogelijk hebt u problemen met bepaalde CA-beleidsregels als u deze optie kiest.
- **Stroom voor apparaatcode:** Storage Explorer geeft u een code om in een browservenster in te voeren. Deze optie wordt niet aanbevolen. De apparaatcodestroom is niet compatibel met veel CA-beleidsregels.

## <a name="troubleshooting-sign-in-issues"></a>Problemen met aanmeldingen oplossen

Als u problemen hebt met aanmelden of als u problemen hebt met een Azure-account na het aanmelden, raadpleegt u de aanmeldingssectie van de Storage Explorer gids voor [probleemoplossing.](./storage-explorer-troubleshooting.md#sign-in-issues)
