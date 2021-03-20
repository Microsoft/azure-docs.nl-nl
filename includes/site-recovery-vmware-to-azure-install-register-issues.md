---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 5f794420eebb052538de0903727951de39f44a48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001548"
---
### <a name="installation-failures"></a>Installatiefouten
| **Voorbeeld fout bericht** | **Aanbevolen actie** |
|--------------------------|------------------------|
|FOUT   Kan de accounts niet laden. Fout: System.IO.IOException: Kan geen gegevens lezen uit de transportverbinding bij het installeren en registreren van de CS-server.| Controleer of TLS 1.0 is ingeschakeld op de computer. |

### <a name="registration-failures"></a>Registratiefouten
Registratiefouten kunnen worden opgespoord aan de hand van de logboeken in de map **%ProgramData%\ASRLogs**.

| **Voorbeeld fout bericht** | **Aanbevolen actie** |
|--------------------------|------------------------|
|**09:20:06**: InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>Bericht: ACS50008: SAML-token is ongeldig.<br>Traceer-id: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>Correlatie-id: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97 ><br>Tijdstempel: **2016-12-12 14:50:08Z<br>** | Zorg ervoor dat de tijd op de systeemklok niet meer dan 15 minuten afwijkt van de lokale tijd. Voer het installatieprogramma opnieuw uit om de registratie te voltooien.|
|**09:35:27** : DRRegistrationException tijdens het ophalen van de gehele kluis voor herstel na noodgeval voor het geselecteerde certificaat: : Threw Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message: ACS50008: SAML-token is ongeldig.<br>Traceer-id: e5ad1af1-2d39-4970-8eef-096e325c9950<br>Correlatie-id: abe9deb8-3e64-464d-8375-36db9816427a<br>Tijdstempel: **2016-05-19 01:35:39Z**<br> | Zorg ervoor dat de tijd op de systeemklok niet meer dan 15 minuten afwijkt van de lokale tijd. Voer het installatieprogramma opnieuw uit om de registratie te voltooien.|
|06:28:45:Kan geen certificaat maken<br>06:28:45:Setup kan niet worden voortgezet. Een certificaat dat is vereist voor verificatie bij Site Recovery, kan niet worden gemaakt. Setup opnieuw uitvoeren | Zorg ervoor dat u het installatie programma uitvoert als een lokale beheerder. |
