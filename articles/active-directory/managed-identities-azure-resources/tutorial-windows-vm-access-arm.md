---
title: Zelfstudie`:` Een beheerde identiteit gebruiken voor toegang tot Azure Resource Manager - Windows - Azure AD
description: Een zelfstudie die u helpt bij het doorlopen van het proces voor het gebruiken van een door het Windows-VM-systeem toegewezen beheerde identiteit om toegang te krijgen tot Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/09/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4555baf658f720bc92e882e141b71f3b8050a1a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093783"
---
# <a name="use-a-windows-vm-system-assigned-managed-identity-to-access-resource-manager"></a>Een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Resource Manager

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

In deze quickstart ziet u hoe u toegang krijgt tot de Azure Resource Manager-API met een virtuele Windows-machine waarop een door het systeem toegewezen beheerde identiteit is ingeschakeld. Beheerde entiteiten voor Azure-resources worden automatisch beheerd in Azure en stellen u in staat om te verifiëren bij services die Microsoft Azure AD-verificatie ondersteunen, zonder referenties in code te hoeven invoegen. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"] 
> * Uw virtuele machine toegang verlenen tot een resourcegroep in Azure Resource Manager 
> * Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en daarmee Azure Resource Manager aanroepen

## <a name="prerequisites"></a>Vereisten

- Basiskennis van beheerde identiteiten. Als u niet bekend bent met de functie voor beheerde identiteiten voor Azure-resources, raadpleegt u dit [overzicht](overview.md).
- Een Azure-account, [meld u aan voor een gratis account](https://azure.microsoft.com/free/).
- 'Eigenaar'-machtigingen voor het juiste bereik (uw abonnement of resourcegroep) om de vereiste stappen voor resource-aanmaak en rolbeheer uit te voeren. Als u hulp nodig hebt bij roltoewijzing, raadpleegt u [Azure-rollen toewijzen om de toegang tot uw Azure-abonnements resources te beheren](../../role-based-access-control/role-assignments-portal.md).
- U hebt ook een virtuele Windows-machine nodig waarvoor door het systeem toegewezen beheerde identiteiten zijn ingeschakeld.
  - Als u voor deze zelfstudie een virtuele machine moet maken, kunt u dat doen met behulp van het artikel [Een virtuele machine maken waarvoor door het systeem toegewezen identiteiten ingeschakeld zijn](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>Uw virtuele machine toegang verlenen tot een resourcegroep in Resource Manager

Met behulp van beheerde identiteiten voor Azure-resources kan uw code toegangstokens ophalen voor verificatie bij resources die ondersteuning bieden voor Microsoft Azure AD-verificatie.  Azure Resource Manager biedt ondersteuning voor Azure AD-verificatie.  Eerst moeten we de door het systeem toegewezen beheerde identiteit van deze VM toegang verlenen tot een resource in Resource Manager, in dit geval de resourcegroep waarin de VM is opgenomen.  

1.  Navigeer naar het tabblad **Resourcegroepen**. 
2.  Selecteer de specifieke **resourcegroep** die u eerder voor uw **virtuele Windows-machine** hebt gemaakt. 
3.  Ga naar **Toegangsbeheer (IAM)** in het linkerpaneel. 
4.  Kies vervolgens **Roltoewijzing toevoegen** om een nieuwe roltoewijzing voor de **virtuele Windows-machine** toe te voegen.  Kies **Rol** als **lezer**. 
5.  Stel in de volgende vervolgkeuzelijst, **Toegang toewijzen aan**, de resource in op **Virtuele machine**. 
6.  Controleer vervolgens of het juiste abonnement wordt weergegeven in de vervolgkeuzelijst **Abonnement**. Bij **Resourcegroep** selecteert u **Alle resourcegroepen**. 
7.  Kies ten slotte bij **Selecteren** uw virtuele Windows-machine in de vervolgkeuzelijst en klik op **Opslaan**.

    ![Alt-tekst voor afbeelding](media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager"></a>Een toegangstoken ophalen met behulp van de door het systeem toegewezen beheerde identiteit van de VM en dit gebruiken om er Azure Resource Manager mee aan te roepen 

In dit gedeelte moet u **PowerShell** gebruiken.  Als **Powershell** nog niet is geïnstalleerd, downloadt u het [hier](/powershell/azure/). 

1.  Navigeer in Azure Portal naar **Virtuele machines**, ga naar uw virtuele Windows-machine en klik op de pagina **Overzicht** op **Verbinden**. 
2.  Voer uw referenties (**gebruikersnaam** en **wachtwoord**) in die u hebt toegevoegd bij het maken van de virtuele Windows-machine. 
3.  Nu u een **Verbinding met extern bureaublad** met de virtuele machine hebt gemaakt, opent u **PowerShell** in de externe sessie. 
4.  Dien met behulp van de cmdlet Invoke-WebRequest een aanvraag in bij de lokaal beheerde identiteit om een toegangstoken voor Azure Resource Manager op te halen voor het Azure-resource-eindpunt.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > De waarde van de parameter 'resource' moet exact overeenkomen met wat er in Azure AD wordt verwacht. Wanneer u de resource-id van Azure Resource Manager gebruikt, moet u de URI opgeven met een slash op het einde.
    
    Extraheer vervolgens de volledige reactie, die is opgeslagen als een tekenreeks in JSON-indeling (JavaScript Object Notation) in het object $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Extraheer vervolgens het toegangstoken uit het antwoord.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    Roep ten slotte Azure Resource Manager aan met behulp van het toegangstoken. In dit voorbeeld gebruiken we de cmdlet Invoke-WebRequest van PowerShell ook om Azure Resource Manager aan te roepen en het toegangstoken in de autorisatie-header op te nemen.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > De URL is hoofdlettergevoelig, dus gebruik precies dezelfde naam van de resourcegroep als hiervoor, met inbegrip van de hoofdletter 'G' in 'resourceGroups'.
        
    De volgende opdracht retourneert de gegevens van de resourcegroep:

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u toegang krijgt tot de Azure Resource Manager-API met een door het systeem beheerde identiteit.  Zie voor meer informatie over Azure Resource Manager:

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)