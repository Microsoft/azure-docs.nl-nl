---
title: 'PowerShell-voorbeeld: apps exporteren met geheimen en certificaten die verlopen na de vereiste datum in Azure Active Directory tenant.'
description: PowerShell-voorbeeld dat alle apps exporteert met geheimen en certificaten die verlopen na de vereiste datum voor de opgegeven apps in uw Azure Active Directory tenant.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 692ab2cfd480fd15760c13c63922fe76d23058ea
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375386"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Apps exporteren met geheimen en certificaten die na de vereiste datum verlopen

In dit PowerShell-voorbeeldscript worden alle geheimen en certificaten voor app-registraties die buiten een vereiste periode verlopen voor de opgegeven apps uit uw map in een CSV-bestand, niet-interactief geexporteerd.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Uitleg van het script

Dit script werkt niet-interactief. De beheerder die deze gebruikt, moet de waarden in de sectie '#PARAMETERS TO CHANGE' wijzigen met hun eigen app-id, toepassingsgeheim, tenantnaam, de periode voor het verlopen van de app-referenties en het pad waar de CSV wordt geëxporteerd.
Dit script maakt gebruik [van de Client_Credential Oauth-stroom.](../../develop/v2-oauth2-client-creds-grant-flow.md) Met de functie RefreshToken wordt het toegangstoken gebouwd op basis van de waarden van de parameters die door de beheerder zijn gewijzigd.

De opdracht Add-Member is verantwoordelijk voor het maken van de kolommen in het CSV-bestand.

| Opdracht | Opmerkingen |
|---|---|
| [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1&preserve-view=true) | Verzendt HTTP- en HTTPS-aanvragen naar een webpagina of webservice. Het parseert het antwoord en retourneert verzamelingen koppelingen, afbeeldingen en andere belangrijke HTML-elementen. |

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie Azure AD PowerShell-voorbeelden voor toepassingsbeheer voor andere PowerShell-voorbeelden [voor toepassingsbeheer.](../app-management-powershell-samples.md)
