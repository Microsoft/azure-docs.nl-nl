---
title: PTA uitschakelen bij het gebruik van Azure AD Connect "niet configureren" | Microsoft Docs
description: In dit artikel wordt beschreven hoe u PTA kunt uitschakelen met de Azure AD Connect functie niet configureren.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26112b1e799cbde3145e7137c686b4b336db4bab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919932"
---
# <a name="disable-pta-when-using-azure-ad-connect"></a>PTA uitschakelen bij het gebruik van Azure AD Connect

Als u Pass-Through-verificatie gebruikt met Azure AD Connect en u deze hebt ingesteld op **niet configureren**, kunt u dit uitschakelen. 

>[!NOTE]
>Als PHS al is ingeschakeld, resulteert het uitschakelen van PTA in de Tenant terugval op PHS.

Het uitschakelen van PTA kan worden uitgevoerd met de volgende cmdlets. 

## <a name="prerequisites"></a>Vereisten
De volgende vereisten zijn vereist:
- Een Windows-computer waarop de PTA-agent is geïnstalleerd. 
- De agent moet de versie 1.5.1742.0 of hoger hebben. 
- Een Azure Global Administrator-account om de Power shell-cmdlets uit te voeren om PTA uit te scha kelen.

>[!NOTE]
> Als uw agent ouder is, beschikt u mogelijk niet over de cmdlets die nodig zijn om deze bewerking te volt ooien. U kunt een nieuwe agent verkrijgen via Azure Portal. Installeer deze op een Windows-computer en geef de beheerders referenties op. (Het installeren van de agent heeft geen invloed op de status van de PTA in de Cloud)

> [!IMPORTANT]
> Als u de Azure Government Cloud gebruikt, moet u de para meter ENVIRONMENTnaam door geven met de volgende waarde. 
>
>| Naam van omgeving | Cloud |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>PTA uitschakelen
Gebruik in een Power shell-sessie het volgende om PTA uit te scha kelen:
1. PS C:\Program Files\Microsoft Azure AD Connect-Verificatie agent> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` of `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` of `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Als u geen toegang hebt tot een agent

Als u geen agent machine hebt, kunt u de volgende opdracht gebruiken om een agent te installeren.

1. Down load de nieuwste auth-agent van portal.azure.com.
2. De functie installeren: `.\AADConnectAuthAgentSetup.exe` of `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Volgende stappen

- [Gebruikersaanmelding met Pass Through-verificatie in Azure Active Directory](how-to-connect-pta.md)
