---
title: Een beheerde identiteit verifiëren met Azure Active Directory
description: Dit artikel bevat informatie over het verifiëren van een beheerde identiteit met Azure Active Directory voor toegang tot de Azure signalerings service
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: c561bb507a5178f4a838b370a3da8af9447829f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092560"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot Azure signalerings bronnen
De Azure signalerings service ondersteunt Azure Active Directory-verificatie (Azure AD) met [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md). Beheerde identiteiten voor Azure-resources kunnen toegang verlenen tot Azure signalerings service bronnen met behulp van Azure AD-referenties van toepassingen die worden uitgevoerd in azure Virtual Machines (Vm's), functie-apps, Virtual Machine Scale Sets en andere services. Door beheerde identiteiten voor Azure-resources te gebruiken in combi natie met Azure AD-verificatie kunt u voor komen dat referenties worden opgeslagen in uw toepassingen die in de cloud worden uitgevoerd.

In dit artikel wordt uitgelegd hoe u toegang tot een Azure signalerings service kunt machtigen met behulp van een beheerde identiteit van een Azure-VM.

## <a name="enable-managed-identities-on-a-vm"></a>Beheerde identiteiten op een virtuele machine inschakelen
Voordat u beheerde identiteiten voor Azure-resources kunt gebruiken om Azure signalerings service resources van uw virtuele machine te autoriseren, moet u eerst beheerde identiteiten inschakelen voor Azure-resources op de VM. Zie een van de volgende artikelen voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources:

- [Azure-portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Client bibliotheken Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Machtigingen verlenen aan een beheerde identiteit in azure AD
Als u een aanvraag voor de Azure signalerings service wilt machtigen vanuit een beheerde identiteit in uw toepassing, moet u eerst RBAC-instellingen (op rollen gebaseerd toegangs beheer) configureren voor die beheerde identiteit. De Azure signalerings service definieert RBAC-rollen met machtigingen voor het verkrijgen `AccessKey` of `ClientToken` . Wanneer de RBAC-rol is toegewezen aan een beheerde identiteit, wordt de beheerde identiteit toegang verleend tot de Azure signalerings service op het juiste bereik.

Zie [verifiëren met Azure Active Directory voor toegang tot Azure signalerings service bronnen](authorize-access-azure-active-directory.md)voor meer informatie over het toewijzen van RBAC-rollen.

## <a name="connect-to-azure-signalr-service-with-managed-identities"></a>Verbinding maken met de Azure signalerings service met beheerde identiteiten
Als u verbinding wilt maken met de Azure signalerings service met beheerde identiteiten, moet u de identiteit van de rol en het juiste bereik toewijzen. De procedure in deze sectie maakt gebruik van een eenvoudige toepassing die wordt uitgevoerd onder een beheerde identiteit en die toegang heeft tot bronnen van Azure signalerings service.

Hier gebruiken we een voor beeld van een Azure virtual machine-resource.

1. Ga naar **instellingen** en selecteer **identiteit**. 
1. Selecteer de **status** die moet worden **ingeschakeld**. 
1. Selecteer **Opslaan** om de instelling op te slaan. 

    ![Beheerde identiteit voor een virtuele machine](./media/authenticate/identity-virtual-machine.png)

Zodra u deze instelling hebt ingeschakeld, wordt er een nieuwe service-identiteit gemaakt in uw Azure Active Directory (Azure AD) en geconfigureerd in de App Service host.

Wijs deze service-identiteit nu toe aan een rol in het vereiste bereik in de resources van uw Azure signalerings service.

## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure-rollen toewijzen met behulp van de Azure Portal  
Raadpleeg [dit artikel](..//role-based-access-control/role-assignments-portal.md)voor meer informatie over het beheren van de toegang tot Azure-resources met behulp van Azure RBAC en de Azure Portal. 

Nadat u het juiste bereik voor een roltoewijzing hebt bepaald, navigeert u naar die resource in de Azure Portal. Geef de instellingen voor toegangs beheer (IAM) voor de resource weer en volg deze instructies voor het beheren van roltoewijzingen:

1. Navigeer in het [Azure Portal](https://portal.azure.com/)naar uw signaal bron.
1. Selecteer **Access Control (IAM)** om de instellingen voor toegangs beheer voor de Azure-Signa lering weer te geven. 
1. Selectter het tabblad **Roltoewijzingen** om de lijst met roltoewijzingen te zien. Selecteer de knop **toevoegen** op de werk balk en selecteer vervolgens **functie toewijzing toevoegen**. 

    ![Knop toevoegen op de werk balk](./media/authenticate/role-assignments-add-button.png)

1. Voer op de pagina **roltoewijzing toevoegen** de volgende stappen uit:
    1. Selecteer de **Azure-seingevings functie** die u wilt toewijzen. 
    1. Zoek naar de beveiligingsprincipal **(gebruiker** , groep, Service-Principal) waaraan u de rol wilt toewijzen.
    1. Selecteer **Opslaan** om de roltoewijzing op te slaan. 

        ![Rol toewijzen aan een toepassing](./media/authenticate/assign-role-to-application.png)

    1. De identiteit waaraan u de rol hebt toegewezen, wordt weer gegeven onder die rol. De volgende afbeelding toont bijvoorbeeld de toepassing `signalr-dev` en `signalr-service` bevindt zich in de rol van de app-server functie. 
        
        ![De lijst roltoewijzing](./media/authenticate/role-assignment-list.png)

U kunt vergelijk bare stappen volgen om een rol toe te wijzen aan een resource groep of een abonnement. Wanneer u de rol en het bereik ervan hebt gedefinieerd, kunt u dit gedrag testen met voor beelden [in deze github-locatie](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).

## <a name="samples-code-while-configuring-your-app-server"></a>Voorbeeld code tijdens het configureren van uw app server

Voeg de volgende opties toe wanneer `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;";
});
```

## <a name="next-steps"></a>Volgende stappen
- Zie [Wat is Azure Role-based Access Control (Azure RBAC)](../role-based-access-control/overview.md)? voor meer informatie over RBAC.
- Zie de volgende artikelen voor meer informatie over het toewijzen en beheren van Azure-roltoewijzingen met Azure PowerShell, Azure CLI of de REST API:
    - [Op rollen gebaseerd toegangs beheer (RBAC) beheren met Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Op rollen gebaseerd toegangs beheer (RBAC) beheren met Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Op rollen gebaseerd toegangs beheer (RBAC) beheren met de REST API](../role-based-access-control/role-assignments-rest.md)
    - [Op rollen gebaseerd toegangs beheer (RBAC) beheren met Azure Resource Manager sjablonen](../role-based-access-control/role-assignments-template.md)

Raadpleeg de volgende verwante artikelen:
- [Een toepassing met Azure Active Directory verifiëren om toegang te krijgen tot de Azure signalerings service](authenticate-application.md)
- [Toegang tot de Azure signalerings service autoriseren met behulp van Azure Active Directory](authorize-access-azure-active-directory.md)