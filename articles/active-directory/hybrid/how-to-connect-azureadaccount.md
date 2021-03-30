---
title: Het wacht woord van het Azure AD Connector-account wijzigen | Microsoft Docs
description: In dit onderwerp vindt u informatie over het herstellen van het Azure AD-Connector account.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: e4f31c560fe3dd91689b361ed520e466fd52da1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85360007"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Het wachtwoord voor het Azure AD Connector-account wijzigen
Het account van de Azure AD-connector moet gratis service zijn. Als u de referenties opnieuw wilt instellen, is dit onderwerp voor u. Als een globale beheerder bijvoorbeeld per ongeluk het wacht woord voor het account opnieuw instelt met behulp van Power shell.

## <a name="reset-the-credentials"></a>De referenties opnieuw instellen
Als het account van de Azure AD-connector geen contact kan maken met Azure AD vanwege verificatie problemen, kan het wacht woord opnieuw worden ingesteld.

1. Meld u aan bij de Azure AD Connect-synchronisatie server en start Power shell.
2. Voer `Add-ADSyncAADServiceAccount` uit.
   ![Power shell-cmdlet addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Globale beheerders referenties voor Azure AD opgeven.

Met deze cmdlet wordt het wacht woord voor het service account opnieuw ingesteld en in azure AD en in de synchronisatie-engine bijgewerkt.

## <a name="known-issues-these-steps-can-solve"></a>Bekende problemen met deze stappen kunnen worden opgelost
Deze sectie bevat een lijst met fouten die zijn gerapporteerd door klanten die zijn hersteld door het opnieuw instellen van een referenties voor het Azure AD Connector-account.

---
Gebeurtenis 6900 de server heeft een onverwachte fout aangetroffen tijdens het verwerken van een melding voor wachtwoord wijzigingen: AADSTS70002: fout bij het valideren van referenties. AADSTS50054: het oude wacht woord wordt gebruikt voor authenticatie.

---
Gebeurtenis 659 fout bij het ophalen van synchronisatie configuratie voor wachtwoord beleid. Micro soft. Identity model. clients. ActiveDirectory. AdalServiceException: AADSTS70002: fout bij het valideren van referenties. AADSTS50054: het oude wacht woord wordt gebruikt voor authenticatie.

## <a name="next-steps"></a>Volgende stappen
**Overzichts onderwerpen**

* [Azure AD Connect synchroniseren: Synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
