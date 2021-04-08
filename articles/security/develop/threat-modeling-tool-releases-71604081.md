---
title: Microsoft Threat Modeling Tool versie 4/9/2019
titleSuffix: Azure
description: Documenteer de release opmerkingen voor de 7.1.60408.1 van het hulp programma voor het maken van bedreigings modellen.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 69dd2c6cdba41779849b4eb6b889cde9b1d6e5c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913565"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Threat Modeling Tool Update release 7.1.60408.1-4/9/2019

Versie 7.1.60408.1 van de Microsoft Threat Modeling Tool (TMT) is uitgebracht op 9 2019 april en bevat de volgende wijzigingen:

- Nieuwe stencils voor Azure Key Vault en Azure Traffic Manager
- Het versie nummer van TMT wordt nu weer gegeven op het Start scherm
- Ondersteunings koppelingen zijn bijgewerkt
- Opgeloste fouten

## <a name="feature-changes"></a>Functie wijzigingen

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Nieuwe stencils voor Azure Key Vault en Azure Traffic Manager

![Scherm afbeelding toont pictogrammen voor Azure Key Vault en Azure Traffic Manager.](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Nieuwe stencils en bedreigingen voor Azure Key Vault en Azure Traffic Manager zijn toegevoegd aan de Azure-stencilset. Bij het openen van modellen gebaseerd op de Azure-stencilset, wordt gebruikers gevraagd de sjabloon bij te werken die is gekoppeld aan het model. Het bijwerken van een model op basis van de Azure-stencilset kan ook hand matig worden gestart met behulp van de opdracht ' sjabloon Toep assen ' in het menu bestand en het meest recente Azure Cloud Services. tb7-bestand opnieuw Toep assen.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Het versie nummer van TMT wordt nu weer gegeven op het Start scherm

De client versie van de Threat Modeling Tool wordt nu weer gegeven op het Start scherm van de toepassing van voor toegankelijkheid.

![Scherm afbeelding toont de Microsoft Threat Modeling Tool met het versie nummer van de client.](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Ondersteunings koppelingen zijn bijgewerkt

Alle ondersteunings koppelingen in het hulp programma zijn bijgewerkt zodat gebruikers worden doorgestuurd naar [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) in plaats van een MSDN-forum.

## <a name="system-requirements"></a>Systeemvereisten

- Ondersteunde besturingssystemen
  - [Micro soft Windows 10 jubileum update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) of hoger
- .NET-versie vereist
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) of hoger
- Aanvullende vereisten
  - Er is een Internet verbinding vereist voor het ontvangen van updates voor het hulp programma en voor sjablonen.

## <a name="documentation-and-feedback"></a>Documentatie en feedback

- Documentatie voor de Threat Modeling Tool bevindt zich op [docs.Microsoft.com](threat-modeling-tool.md)en bevat informatie [over het gebruik van het hulp programma](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Volgende stappen

Down load de nieuwste versie van de [Microsoft Threat Modeling tool](https://aka.ms/threatmodelingtool).
