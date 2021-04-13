---
title: Azure AD Connect Power shell-cmdlets voor gMSA Cloud Provisioning agent
description: Meer informatie over het gebruik van de Azure AD Connect Power shell-cmdlets voor gMSA Cloud Provisioning agent.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46b5c94c84bdbd0e5bb0d7b08ba9296ebfcd1838
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306055"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>Azure AD Connect Power shell-cmdlets voor gMSA Cloud Provisioning agent

Het doel van dit document is de Azure AD Connect Power shell-cmdlets voor Cloud Provisioning agent gMSA te beschrijven. Met deze cmdlets kunt u meer granulatie hebben voor de machtigingen die worden toegepast op het service account (gmsa). Azure AD Connect Cloud Sync geldt standaard alle machtigingen die vergelijkbaar zijn met Azure AD Connect op de standaard gmsa of een aangepaste gmsa. 

In dit document worden de volgende cmdlets behandeld:  

`Set-AADCloudSyncRestrictedPermissions`

`Set-AADCloudSyncPermissions` 

## <a name="how-to-use-the-cmdlets"></a>De cmdlets gebruiken:  

De volgende vereisten zijn vereist voor het gebruik van deze cmdlets.

1. Installeer de inrichtings agent. 
2. Importeer de PS-module van de inrichtings agent in een Power shell-sessie. 

 ```PowerShell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
 ```
3. Bestaande machtigingen verwijderen.  Alle bestaande machtigingen voor het service account verwijderen, behalve zelf gebruiken: `Set-AADCloudSyncRestrictedPermission` .  

    Voor deze cmdlet is een para meter vereist `Credential` die kan worden door gegeven, of wordt gevraagd of deze zonder deze is aangeroepen.

    Een variabele-gebruik maken  

   `$credential = Get-Credential` 

   Hiermee wordt de gebruiker gevraagd om een gebruikers naam en wacht woord in te voeren. De referenties moeten zich in een minimum domein beheerder bevindt (van het domein waar de agent is geïnstalleerd), kan ook ondernemings Administrator zijn. 

4.  Vervolgens kunt u de cmdlet aanroepen om extra machtigingen te verwijderen: 
   ```PowerShell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```
5. Of u kunt gewoon bellen 

   `Set-AADCloudSyncRestrictedPermissions` Hiermee wordt om referenties gevraagd. 

 6.  Voeg een specifiek machtigings type toe.  Machtigingen die zijn toegevoegd, zijn hetzelfde als Azure AD Connect.  Zie [set-AADCloudSyncPermissions hieronder gebruiken](#using-set-aadcloudsyncpermissions) voor voor beelden over het instellen van machtigingen.

## <a name="using-set-aadcloudsyncpermissions"></a>Set-AADCloudSyncPermissions gebruiken 
`Set-AADCloudSyncPermissions` ondersteunt de volgende machtigings typen die identiek zijn aan de machtigingen die door Azure AD Connect worden gebruikt. De volgende machtigings typen worden ondersteund: 

|Machtigings type|Description|
|-----|-----|
|BasicRead| Zie [BasicRead](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions) -machtigingen voor Azure AD Connect|
|PasswordHashSync|Zie [PasswordHashSync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization) -machtigingen voor Azure AD Connect|
|PasswordWriteBack|Zie [PasswordWriteBack](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback) -machtigingen voor Azure AD Connect|
|HybridExchangePermissions|Zie [HybridExchangePermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment) -machtigingen voor Azure AD Connect| 
|ExchangeMailPublicFolderPermissions| Zie [ExchangeMailPublicFolderPermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders-preview) -machtigingen voor Azure AD Connect| 
|CloudHR| Hiermee worden ' volledig beheer ' toegepast op ' onderliggende gebruikers objecten ' en ' gebruikers objecten maken/verwijderen ' op ' dit object en alle onderliggende objecten '| 
|Alles|Hiermee worden alle bovenstaande machtigingen toegevoegd.| 



U kunt AADCloudSyncPermissions op een van de volgende twee manieren gebruiken:
- [Een bepaalde machtiging verlenen aan alle geconfigureerde domeinen](#grant-a-certain-permission-to-all-configured-domains) 
- [Een bepaalde machtiging verlenen aan een specifiek domein](#grant-a-certain-permission-to-a-specific-domain) 
## <a name="grant-a-certain-permission-to-all-configured-domains"></a>Een bepaalde machtiging verlenen aan alle geconfigureerde domeinen 
Voor het verlenen van bepaalde machtigingen voor alle geconfigureerde domeinen is het gebruik van een ondernemings Administrator-account vereist.


 ```PowerShell
Set-AADCloudSyncPermissions -PermissionType “Any mentioned above” -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>Een bepaalde machtiging verlenen aan een specifiek domein 
Voor het verlenen van bepaalde machtigingen voor een specifiek domein is het gebruik van vereist ten minste een domein beheerders account van het domein dat u probeert toe te voegen.


 ```PowerShell
Set-AADCloidSyncPermissions -PermissionType “Any mentioned above” -TargetDomain “FQDN of domain” (has to be already configured through wizard) -TargetDomaincredential $credential(same as above) 
```
 

Opmerking: voor 1. De referenties moeten mini maal een ondernemings beheerder zijn. 

Voor 2. De referenties kunnen een domein beheerder of ondernemings beheerder zijn. 

  

