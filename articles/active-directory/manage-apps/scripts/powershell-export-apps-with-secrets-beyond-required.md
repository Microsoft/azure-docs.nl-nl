---
title: 'Power shell-voor beeld: Exporteer apps met geheimen en certificaten die na de vereiste datum in Azure Active Directory Tenant verlopen.'
description: Power shell-voor beeld van het exporteren van alle apps met geheimen en certificaten die na de vereiste datum voor de opgegeven apps in uw Azure Active Directory-Tenant verlopen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: daeea48758a9f08e7eedbfcaddcde3815f5c1e16
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729096"
---
# <a name="export-apps-with-secrets-and-certificates-expiring-beyond-the-required-date"></a>Apps exporteren met geheimen en certificaten die na de vereiste datum verlopen

Met dit Power shell-voorbeeld script worden alle app-registraties en certificaten die na een vereiste periode voor de opgegeven apps in een CSV-bestand niet-interactief zijn, geëxporteerd.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-secrets-beyond-required.ps1 "Exports all apps with secrets and certificates expiring beyond the required date for the specified apps in your directory.")]

## <a name="script-explanation"></a>Uitleg van het script

Dit script werkt niet-interactief. De beheerder die hiervoor gebruikmaakt, moet de waarden in de sectie ' #PARAMETERS gewijzigd in wijzigen ' wijzigen met hun eigen App-ID, toepassings geheim, Tenant naam, de periode voor de verval datum van de apps en het pad waar het CSV-bestand moet worden geëxporteerd.
Dit script maakt gebruik van de [Client_Credential OAuth-stroom](../../develop/v2-oauth2-client-creds-grant-flow.md) met de functie ' RefreshToken ' wordt het toegangs token gemaakt op basis van de waarden van de para meters die door de beheerder zijn gewijzigd.

De opdracht ' lid toevoegen ' is verantwoordelijk voor het maken van de kolommen in het CSV-bestand.

| Opdracht | Opmerkingen |
|---|---|
| [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1&preserve-view=true) | Verzendt HTTP-en HTTPS-aanvragen naar een webpagina of webservice. Hiermee wordt het antwoord geparseerd en worden verzamelingen van koppelingen, afbeeldingen en andere belang rijke HTML-elementen geretourneerd. |

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van de Azure PowerShell-module](/powershell/azure/active-directory/overview) voor meer informatie over de Azure AD PowerShell-module.

Zie [Azure AD Power shell-voor beelden voor toepassings beheer](../app-management-powershell-samples.md)voor andere Power shell-voor beelden voor toepassings beheer.
