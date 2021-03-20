---
title: De Azure AD Connect Cloud-inrichtings agent installeren met behulp van Power shell
description: Meer informatie over het installeren van de Azure AD Connect Cloud-inrichtings agent met behulp van Power shell-cmdlets.
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
ms.openlocfilehash: c20cfb96b5cd6e1d05e332fa7157fe6e0cde8656
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613330"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>De Azure AD Connect-inrichtings agent installeren met behulp van Power shell-cmdlets 
In het volgende document wordt uitgelegd hoe u de Azure AD Connect inrichtings Agent installeert met behulp van Power shell-cmdlets.
 

## <a name="prerequisite"></a>Vereiste: 


>[!IMPORTANT]
>In de volgende installatie-instructies wordt ervan uitgegaan dat aan alle [vereisten](how-to-prerequisites.md) is voldaan.
>
> Op de Windows-Server moet TLS 1,2 zijn ingeschakeld voordat u de Azure AD Connect inrichtings Agent installeert met behulp van Power shell-cmdlets. U kunt de stappen die [hier](how-to-prerequisites.md#tls-requirements)worden beschreven, gebruiken om TLS 1,2 in te scha kelen.

 

## <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>De Azure AD Connect-inrichtings agent installeren met behulp van Power shell-cmdlets 


 1. Meld u aan bij de Azure Portal en ga vervolgens naar **Azure Active Directory**.
 2. Selecteer in het linkermenu **Azure AD Connect**.
 3. Selecteer **inrichting beheren (preview)**  >  **Alle agents controleren**.
 4. Down load de Azure AD Connect Provisioning agent van de Azure Portal naar een lokaal bestand.  

   ![On-premises agent downloaden](media/how-to-install/install-9.png)</br>
 5. Voor de doel einden van deze instructies is de agent gedownload naar de volgende map: "C:\ProvisioningSetup". 
 6. ProvisioningAgent installeren in de Stille modus

   ```
   $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
   $installerProcess.WaitForExit()  
   ```
 7. PS-module van inrichtings agent importeren 

   ```
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll" 
   ```
 8. Maak verbinding met AzureAD met behulp van globale beheerders referenties. u kunt deze sectie aanpassen om het wacht woord op te halen uit een beveiligde Store. 

   ```
   $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 

   $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
   ```

   Connect-AADCloudSyncAzureAD-referentie $globalAdminCreds 

 9. Voeg het gMSA-account toe en geef de referenties van de domein beheerder op om een standaard gMSA-account te maken 
 
   ```
   $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 

   Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
   ```
 10. Of gebruik de bovenstaande cmdlet als hieronder om een vooraf gemaakt gMSA-account op te geven 

 
   ```
   Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
   ```
 11. Domein toevoegen 

   ```
   $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
   ```
 12. Of gebruik de bovenstaande cmdlet als hieronder om voorkeurs domein controllers te configureren 

   ```
   $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
   ```
 13. Herhaal de vorige stap om meer domeinen toe te voegen, geef de account namen en domein namen van de respectieve domeinen op 
 14. Start de service opnieuw op 
   ```
   Restart-Service -Name AADConnectProvisioningAgent  
   ```
 15.  Ga naar Azure Portal om de configuratie van de Cloud synchronisatie te maken.

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>Power shell-cmdlets voor de inrichtings agent gMSA
Nu u de agent hebt geïnstalleerd, kunt u meer gedetailleerde machtigingen voor de gMSA Toep assen.  Zie [Azure AD Connect Power shell-cmdlets voor Cloud Provisioning agent gMSA](how-to-gmsa-cmdlets.md) voor informatie en stapsgewijze instructies voor het configureren van de machtigingen.

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Azure AD Connect Power shell-cmdlets voor gMSA Cloud Provisioning agent](how-to-gmsa-cmdlets.md)
- [Wat is Azure AD Connect--cloudsynchronisatie?](what-is-cloud-sync.md)