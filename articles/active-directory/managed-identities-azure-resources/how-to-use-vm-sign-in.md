---
title: Beheerde identiteiten op een azure-VM gebruiken voor aanmelding - Azure ADV
description: Stapsgewijse instructies en voorbeelden voor het gebruik van een door Azure-VM beheerde identiteiten voor de service-principal voor Azure-resources voor aanmelding bij scriptclients en toegang tot resources.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 59366f1a5b4bd0572af1b36f7be2f5bf91392660
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784781"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Beheerde identiteiten gebruiken voor Azure-resources op een Azure-VM voor aanmelden 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Dit artikel bevat PowerShell- en CLI-scriptvoorbeelden voor aanmelding met behulp van beheerde identiteiten voor de service-principal voor Azure-resources en richtlijnen voor belangrijke onderwerpen zoals foutafhandeling.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Als u van plan bent om de Azure PowerShell of Azure CLI-voorbeelden in dit artikel te gebruiken, moet u de nieuwste versie van [Azure PowerShell](/powershell/azure/install-az-ps) of [Azure CLI installeren.](/cli/azure/install-azure-cli) 

> [!IMPORTANT]
> - In alle voorbeeldscripts in dit artikel wordt ervan uit gegaan dat de opdrachtregelclient wordt uitgevoerd op een VM met beheerde identiteiten voor Azure-resources ingeschakeld. Gebruik de VM-functie 'Verbinding maken' in Azure Portal om extern verbinding te maken met uw VM. Zie Beheerde identiteiten configureren voor Azure-resources op een VM met behulp van de Azure Portal of een van de variantartikelen (met behulp van PowerShell, CLI, een sjabloon of een Azure SDK) voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources op een [VM.](qs-configure-portal-windows-vm.md) 
> - Om fouten tijdens toegang tot resources te voorkomen, moet de beheerde identiteit van de VM ten minste 'Lezer'-toegang krijgen met het juiste bereik (de VM of hoger) om Azure Resource Manager-bewerkingen op de VM toe te staan. Zie [Beheerde identiteiten voor Azure-resources toegang tot een resource](howto-assign-access-portal.md) toewijzen met behulp van de Azure Portal voor meer informatie.

## <a name="overview"></a>Overzicht

Beheerde identiteiten voor Azure-resources bieden een [service-principal-object](../develop/developer-glossary.md#service-principal-object) dat wordt gemaakt bij het inschakelen van beheerde identiteiten voor [Azure-resources](overview.md) op de VM. De service-principal kan toegang krijgen tot Azure-resources en worden gebruikt als een identiteit door script-/opdrachtregel-clients voor aanmelding en toegang tot resources. Normaal gezien moet een scriptclient het volgende doen om toegang te krijgen tot beveiligde resources onder zijn eigen identiteit:  

   - worden geregistreerd bij en toestemming geven met Azure AD als een vertrouwelijke/webclienttoepassing
   - meld u aan onder de service-principal, met behulp van de referenties van de app (die waarschijnlijk zijn ingesloten in het script)

Met beheerde identiteiten voor Azure-resources hoeft uw scriptclient geen van beide meer te doen, omdat deze zich kan aanmelden onder de beheerde identiteiten voor de service-principal voor Azure-resources. 

## <a name="azure-cli"></a>Azure CLI

Het volgende script laat zien hoe u:

1. Meld u aan bij Azure AD onder de beheerde identiteit van de VM voor de service-principal voor Azure-resources  
2. Roep Azure Resource Manager aan en haal de service-principal-id van de VM op. CLI zorgt automatisch voor het beheer van het verkrijgen/gebruiken van tokens. Zorg ervoor dat u de naam van uw virtuele machine vervangt door `<VM-NAME>` .  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Het volgende script laat zien hoe u:

1. Meld u aan bij Azure AD onder de beheerde identiteit van de VM voor de service-principal voor Azure-resources  
2. Roep een Azure Resource Manager-cmdlet aan om informatie over de VM op te halen. PowerShell beheert het tokengebruik automatisch voor u.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Resource-ID's voor Azure-services

Zie [Azure-services die Azure AD-verificatie](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) ondersteunen voor een lijst met resources die Ondersteuning bieden voor Azure AD en die zijn getest met beheerde identiteiten voor Azure-resources en hun respectieve resource-id's.

## <a name="error-handling-guidance"></a>Richtlijnen voor foutafhandeling 

Antwoorden zoals hieronder kunnen erop wijzen dat de beheerde identiteit van de VM voor Azure-resources niet juist is geconfigureerd:

- PowerShell: *Invoke-WebRequest: kan geen verbinding maken met de externe server*
- CLI: MSI: Kan een token niet ophalen van met de fout *`http://localhost:50342/oauth2/token` HTTPConnectionPool(host='localhost', port=50342)* 

Als u een van deze fouten ontvangt, gaat u terug naar  de azure-VM in de [Azure Portal](https://portal.azure.com) en gaat u naar de pagina Identiteit en zorgt u ervoor dat Systeem toegewezen **is** ingesteld op Ja.

## <a name="next-steps"></a>Volgende stappen

- Zie Beheerde identiteiten configureren voor Azure-resources op een [Azure-VM](qs-configure-powershell-windows-vm.md)met behulp van PowerShell als u beheerde identiteiten voor Azure-resources wilt inschakelen op een Azure-VM, of Beheerde identiteiten configureren voor Azure-resources op een [azure-VM](qs-configure-cli-windows-vm.md) met behulp van Azure CLI
