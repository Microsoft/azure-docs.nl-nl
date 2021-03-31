---
title: Microsoft Graph Power shell SDK en Azure Active Directory Identity Protection
description: Meer informatie over het opvragen van Microsoft Graph risico detecties en bijbehorende informatie uit Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2db8cfe652c0fca4b68b00d846e345c1b60cd05d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880233"
---
# <a name="azure-active-directory-identity-protection-and-the-microsoft-graph-powershell-sdk"></a>Azure Active Directory Identity Protection en de Microsoft Graph Power shell SDK

Microsoft Graph is het micro soft Unified API-eind punt en de start van [Azure Active Directory Identity Protection](./overview-identity-protection.md) -api's. In dit artikel wordt uitgelegd hoe u de [Microsoft Graph Power shell SDK](/graph/powershell/get-started) gebruikt om Risk ante gebruikers details te krijgen met behulp van Power shell. Organisaties die de Microsoft Graph Api's rechtstreeks willen doorzoeken, kunnen het artikel gebruiken, [zelf studie: Risico's identificeren en oplossen met behulp van Microsoft Graph api's](/graph/tutorial-riskdetection-api) om te beginnen met de reis.


## <a name="connect-to-microsoft-graph"></a>Verbinding maken met Microsoft Graph

Er zijn vier stappen voor het verkrijgen van toegang tot identiteits beveiligings gegevens via Microsoft Graph:

- [Een certificaat maken](#create-a-certificate)
- [Een nieuwe app-registratie maken](#create-a-new-app-registration)
- [API-machtigingen configureren](#configure-api-permissions)
- [Een geldige referentie configureren](#configure-a-valid-credential)

### <a name="create-a-certificate"></a>Een certificaat maken

In een productie omgeving gebruikt u een certificaat van uw productie certificerings instantie, maar in dit voor beeld wordt een zelfondertekend certificaat gebruikt. Maak en exporteer het certificaat met behulp van de volgende Power shell-opdrachten.

```powershell
$cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"
```

### <a name="create-a-new-app-registration"></a>Een nieuwe app-registratie maken

1. Ga in het Azure Portal naar **Azure Active Directory**  >  **app-registraties**.
1. Selecteer **Nieuwe registratie**.
1. Voer de volgende stappen uit op de pagina **maken** :
   1. Typ in het tekstvak **naam** een naam voor de toepassing (bijvoorbeeld: Azure AD-risico detectie-API).
   1. Selecteer onder **ondersteunde account typen** het type accounts dat de api's gaat gebruiken.
   1. Selecteer **Registreren**.
1. Noteer de toepassings-id van de **toepassing (client)** en de **map (Tenant)** , omdat u deze items later nodig hebt.

### <a name="configure-api-permissions"></a>API-machtigingen configureren

In dit voor beeld configureren we toepassings machtigingen zodat dit voor beeld zonder toezicht kan worden gebruikt. Als u machtigingen verleent aan een gebruiker die wordt aangemeld, kiest u in plaats daarvan gedelegeerde machtigingen. Meer informatie over verschillende machtigings typen vindt u in het artikel, [machtigingen en toestemming in het micro soft Identity-platform](../develop/v2-permissions-and-consent.md#permission-types).

1. Selecteer de **API-machtigingen** van de **toepassing** die u hebt gemaakt.
1. Klik op de pagina **geconfigureerde machtigingen** in de werk balk aan de bovenkant op **een machtiging toevoegen**.
1. Klik op de pagina **API-toegang toevoegen** op **een API selecteren**.
1. Selecteer **Microsoft Graph** op de pagina **een API selecteren** en klik vervolgens op **selecteren**.
1. Op de pagina **API-machtigingen voor aanvragen** : 
   1. Selecteer **Toepassingsmachtigingen**.
   1. Schakel de selectie vakjes naast `IdentityRiskEvent.Read.All` en in `IdentityRiskyUser.Read.All` .
   1. Selecteer **Machtigingen toevoegen**.
1. **Toestemming van beheerder voor domein verlenen** selecteren 

### <a name="configure-a-valid-credential"></a>Een geldige referentie configureren

1. Selecteer **certificaten & geheimen** uit de **toepassing** die u hebt gemaakt.
1. Selecteer **certificaat uploaden** onder **certificaten**.
   1. Selecteer het eerder geëxporteerde certificaat in het venster dat wordt geopend.
   1. Selecteer **Toevoegen**.
1. Noteer de **vinger afdruk** van het certificaat, aangezien u deze informatie in de volgende stap nodig hebt.

## <a name="list-risky-users-using-powershell"></a>Risk ante gebruikers vermelden met behulp van Power shell

Als u de mogelijkheid wilt bieden om Microsoft Graph op te vragen, moeten we de `Microsoft.Graph` module in het Power shell-venster installeren met behulp van de `Install-Module Microsoft.Graph` opdracht.

Wijzig de volgende variabelen zodat de gegevens die in de vorige stappen zijn gegenereerd, worden weer geven en voer ze als geheel uit om Risk ante gebruikers details te krijgen met behulp van Power shell.

```powershell
$ClientID       = "<your client ID here>"        # Application (client) ID gathered when creating the app registration
$tenantdomain   = "<your tenant domain here>"    # Directory (tenant) ID gathered when creating the app registration
$Thumbprint     = "<your client secret here>"    # Certificate thumbprint gathered when configuring your credential

Select-MgProfile -Name "beta"
  
Connect-MgGraph -ClientId $ClientID -TenantId $tenantdomain -CertificateThumbprint $Thumbprint

Get-MgRiskyUser -All
```

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met de Microsoft Graph Power shell SDK](/graph/powershell/get-started)
- [Zelf studie: Risico's identificeren en oplossen met behulp van Microsoft Graph-Api's](/graph/tutorial-riskdetection-api)
- [Overzicht van Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Toegang krijgen zonder een gebruiker](/graph/auth-v2-service)
- [Hoofdmap van Azure AD Identity Protection-Service](/graph/api/resources/identityprotectionroot)
- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
