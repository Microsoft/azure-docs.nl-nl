---
title: 'Azure AD Connect: problemen met bron anker tijdens de installatie oplossen | Microsoft Docs'
description: Dit onderwerp bevat stappen voor het oplossen van problemen met het bron anker tijdens de installatie.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 4e6460015430850c11fbf70a005d7440ce1b92fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91306001"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Problemen met bron anker tijdens de installatie oplossen
In dit artikel vindt u informatie over de verschillende bron anker problemen die kunnen optreden tijdens de installatie en die manieren bieden om deze problemen op te lossen.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Ongeldig bron anker in Azure Active Directory

### <a name="custom-installation"></a>Aangepaste installatie

Tijdens een aangepaste installatie wordt Azure AD Connect het bron anker beleid gelezen van Azure Active Directory. Als het beleid bestaat in Azure Active Directory, past Azure AD Connect hetzelfde beleid toe, tenzij het wordt overschreven door de klant. De wizard informeert welk kenmerk is gelezen. Daarnaast wordt de wizard gewaarschuwd als u probeert het bron anker beleid te negeren.

Tijdens deze Lees bewerking is het mogelijk dat het bron anker beleid in Azure Active Directory onverwacht is. In dit geval weet Azure AD Connect niet welk bron anker moet worden gebruikt en moet er hand matig worden overschreven.</br>
![Scherm afbeelding die laat zien waar het bron anker hand matig moet worden overschreven.](media/tshoot-connect-source-anchor/source1.png)

U kunt dit probleem oplossen door het bron anker hand matig te overschrijven door een specifiek kenmerk te selecteren. Ga door met deze optie alleen als u zeker weet welk kenmerk moet worden geselecteerd. Als u niet zeker weet, neemt u contact op met [micro soft ondersteuning](https://support.microsoft.com/contactus/) voor hulp. Als u het bron anker beleid wijzigt, kan dit de koppeling tussen uw on-premises gebruikers en de bijbehorende Azure-resources verstoren.</br>
![Scherm afbeelding met het opgegeven kenmerk dat het bron anker overschrijft.](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Snelle installatie
Tijdens de snelle installatie wordt Azure AD Connect het bron anker beleid gelezen van Azure Active Directory. Als het beleid bestaat in Azure Active Directory, past Azure AD Connect hetzelfde beleid toe. Er is geen optie om hand matig te negeren.

Tijdens deze Lees bewerking is het mogelijk dat het bron anker beleid in Azure Active Directory onverwacht is. In dit geval weet Azure AD Connect niet wat het bron anker moet zijn.</br>
![Scherm afbeelding waarin wordt weer gegeven wat er gebeurt wanneer het bron anker in Azure Active Directory onverwacht is.](media/tshoot-connect-source-anchor/source3.png)

U kunt dit probleem oplossen door opnieuw te installeren met de aangepaste modus en het bron anker hand matig te vervangen door een specifiek kenmerk te selecteren. Ga door met deze optie alleen als u zeker weet welk kenmerk moet worden geselecteerd. Als u niet zeker weet, neemt u contact op met [micro soft ondersteuning](https://support.microsoft.com/contactus/) voor hulp. Als u het bron anker beleid wijzigt, kan dit de koppeling tussen uw on-premises gebruikers en de bijbehorende Azure-resources verstoren.

### <a name="invalid-source-anchor-in-sync-engine"></a>Ongeldig bron anker in de synchronisatie-engine
Tijdens de installatie is het mogelijk dat Azure AD Connect probeert de synchronisatie-engine te configureren met een ongeldig bron anker. Deze bewerking is waarschijnlijk een probleem met het product en de installatie van Azure AD Connect mislukt. Neem contact op met [micro soft ondersteuning](https://support.microsoft.com/contactus/) als u dit probleem ondervindt.</br>
![ongewenste](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
