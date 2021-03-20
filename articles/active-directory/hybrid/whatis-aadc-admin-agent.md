---
title: Wat is de Azure AD Connect-beheerderagent - Azure AD Connect | Microsoft Docs
description: Hier worden de hulpprogramma's beschreven die worden gebruikt om uw on-premises omgeving met Azure AD te synchroniseren en bewaken.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da4e1cfc68dff4ad2bc0552c6d35fe1230779306
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91312954"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Wat is de Azure AD Connect-beheerderagent? 
De Azure AD Connect-beheerderagent is een nieuw onderdeel van Azure Active Directory Connect dat kan worden geïnstalleerd op een Azure Active Directory Connect-server. Het wordt gebruikt om specifieke gegevens te verzamelen uit uw Active Directory-omgeving waarmee een Microsoft-ondersteuningstechnicus problemen kan oplossen wanneer u een ondersteuningscase opent. 

>[!NOTE]
>De beheerderagent is niet standaard geïnstalleerd en ingeschakeld.  U moet de agent installeren om gegevens te kunnen verzamelen voor ondersteuningscases.

Wanneer deze is geïnstalleerd, wacht de Azure AD Connect-beheerderagent op specifieke aanvragen voor gegevens van Azure Active Directory. Het haalt de gevraagde gegevens op uit de synchronisatie-omgeving en verstuurt deze naar Azure Active Directory, waar de Microsoft-ondersteuningstechnicus ze kan raadplegen. 

De informatie die door de Azure AD Connect beheerderagent uit uw omgeving wordt opgehaald, wordt op geen enkele manier opgeslagen. Deze wordt enkel weergegeven aan de ondersteuningstechnici van Microsoft om hen te helpen om de ondersteuningscase voor Azure Active Directory Connect die u hebt geopend te onderzoeken en op te lossen. De Azure AD Connect-beheerderagent is niet standaard geïnstalleerd op de Azure AD Connect-server. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>De Azure AD Connect-beheerderagent installeren op de Azure AD Connect-server 

Vereisten:
1.    Azure AD Connect is geïnstalleerd op de server
2.    Azure AD Connect Health is geïnstalleerd op de server

![beheerderagent](media/whatis-aadc-admin-agent/adminagent0.png)

De binaire bestanden van Azure AD Connect-beheerderagent worden op de AAD Connect-server geplaatst. Ga als volgt te werk om de agent te installeren:

1.    Open PowerShell in beheermodus
2.    Navigeer naar de map waar de toepassing zich bevindt cd 'C:\Program Files\Microsoft Azure Active Directory Connect\Tools'
3.    ConfigureAdminAgent.ps1 uitvoeren

Voer uw globale beheerdersreferenties voor Azure AD in wanneer daarom gevraagd wordt. Dit zouden dezelfde referenties moeten zijn die u heeft ingevoerd bij de installatie van Azure AD Connect.

Nadat de agent is geïnstalleerd, ziet u de volgende twee nieuwe programma's in de lijst 'Programma's toevoegen/verwijderen' in het configuratiescherm van uw server: 

![Schermopname van de lijst met Programma's toevoegen/verwijderen met de nieuwe programma's die u hebt toegevoegd.](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Welke gegevens in mijn Synchronisatieservice worden getoond aan de Microsoft-servicetechnicus? 
Wanneer u een ondersteuningscase opent, kan de Microsoft-ondersteuningstechnicus de relevante gegevens in Active Directory, de Active Directory Connector-ruimte op de Azure Active Directory Connect-server, de Azure Active Directory Connector-ruimte op de Azure Active Directory Connect-server en de Metaverse op de Azure Active Directory Connect-server zien voor een bepaalde gebruiker. 

De Microsoft-ondersteuningstechnicus kan geen gegevens in uw systeem wijzigen en kan geen wachtwoorden zien. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Wat moet ik doen als ik niet wil dat de ondersteuningstechnicus van Microsoft toegang heeft tot mijn gegevens? 
Als u de agent hebt geïnstalleerd en u niet wilt dat de servicetechnicus van Microsoft toegang krijgt tot uw gegevens tijdens een ondersteuningsoproep, dan kunt u de functie uitschakelen door het serviceconfiguratiebestand te wijzigen, zoals hieronder wordt beschreven: 

1. Open **C:\Program Files\Microsoft Azure AD Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config** in kladblok.
2. Schakel de instelling **UserDataEnabled** uit zoals hieronder wordt weergegeven. Als de instelling **UserDataEnabled** bestaat en is ingesteld op true, stelt u deze in op false. Als de instelling niet bestaat, voegt u de instelling toe zoals hieronder wordt weergegeven.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3. Sla het configuratiebestand op.
4. Start de service voor de Azure AD Connect-beheerdersagent opnieuw op zoals hieronder wordt weergegeven

![Schermafbeelding die laat zien waar de Azure AD Administrator Agent-service opnieuw kan worden gestart.](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
