---
title: Beheerde identiteiten inschakelen op uw Lab-Vm's in Azure DevTest Labs
description: In dit artikel wordt beschreven hoe een Lab-eigenaar door de gebruiker toegewezen beheerde identiteiten kan inschakelen op uw virtuele lab-machines.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4bf2900acebaeecd5cbc4cb65635aee6de87dda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88717626"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Door de gebruiker toegewezen beheerde identiteiten inschakelen op virtuele lab-machines in Azure DevTest Labs
Als eigenaar van een lab kunt u door de gebruiker toegewezen beheerde identiteiten inschakelen op uw virtuele lab-machines (Vm's) in Azure DevTest Labs.

Een beheerde identiteit kan worden gebruikt om te verifiëren bij elke service die ondersteuning biedt voor Azure Active Directory (AD)-verificatie, met inbegrip van Key Vault, zonder dat er referenties in de code worden door gegeven. Zie [Wat is beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten.

Met deze functie kunnen Lab-gebruikers Azure-resources, zoals Azure SQL Database, delen in de context van het lab. De verificatie voor de resource wordt door de identiteit zelf uitgevoerd. Zodra de configuratie is geconfigureerd, wordt elke bestaande/nieuw gemaakte Lab-VM ingeschakeld met deze identiteit. Lab-gebruikers kunnen toegang krijgen tot bronnen wanneer ze zijn aangemeld op hun computers.

> [!NOTE]
> U kunt meerdere door de gebruiker toegewezen beheerde identiteiten toevoegen die moeten worden ingeschakeld op uw Lab-Vm's.

## <a name="use-azure-portal"></a>Azure Portal gebruiken
Voer de volgende stappen uit om een door de gebruiker toegewezen beheerde identiteit voor Lab-Vm's toe te voegen:

1. [Een door de gebruiker toegewezen beheerde identiteit maken in uw abonnement](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Navigeer naar de pagina **configuratie en beleid** voor uw Lab.
1. Selecteer **identiteit (preview)** in het menu links.
1. Selecteer het tabblad **virtuele machine** .
1. Selecteer **toevoegen** om een bestaande identiteit te selecteren in een vooraf gevulde vervolg keuzelijst. 

    > [!div class="mx-imgBorder"]
    > ![Knop identiteit toevoegen](./media/enable-managed-identities-lab-vms/add-identity-button.png)
1. Selecteer een bestaande door de **gebruiker beheerde identiteit** in de vervolg keuzelijst en selecteer **OK**. 

    > [!div class="mx-imgBorder"]
    > ![Identiteit toevoegen](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>API gebruiken

1.  Nadat u een identiteit hebt gemaakt, noteert u de resource-ID van de identiteit. Deze moet eruitzien als in het volgende voor beeld: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Voer een PUT HTTPS-methode uit om een nieuwe **ServiceRunner** -resource toe te voegen aan het lab, zoals wordt weer gegeven in het volgende voor beeld. 

    Resource voor het uitloper van Services is een proxy bron voor het beheer en beheer van beheerde identiteiten in DevTest Labs. De naam van het service loper kan een geldige naam zijn, maar we raden u aan de naam van de beheerde identiteits bron te gebruiken.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>Volgende stappen
Zie [Wat is beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten.







