---
title: BESTURINGSSYSTEEM schijven versleutelen met door de klant beheerde sleutels in Azure DevTest Labs
description: Meer informatie over het versleutelen van besturings systeem schijven (OS) met door de klant beheerde sleutels in Azure DevTest Labs.
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 26ef4ff1529483da9956c6dcc43807af0ffd6463
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96341204"
---
# <a name="encrypt-operating-system-os-disks-using-customer-managed-keys-in-azure-devtest-labs"></a>Besturings systemen (OS)-schijven versleutelen met door de klant beheerde sleutels in Azure DevTest Labs
Met SSE (server side Encryption) beschermt u uw gegevens en kunt u voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Met SSE worden uw gegevens die zijn opgeslagen op Managed disks in azure (OS-en gegevens schijven), op rest standaard automatisch versleuteld wanneer deze in de cloud worden bewaard. Meer informatie over [schijf versleuteling](../virtual-machines/disk-encryption.md) in Azure. 

In DevTest Labs worden alle besturingssysteem schijven en gegevens schijven die zijn gemaakt als onderdeel van een lab, versleuteld met door het platform beheerde sleutels. Als eigenaar van een lab kunt u echter kiezen voor het versleutelen van de besturingssysteem schijven van de virtuele machine met behulp van uw eigen sleutels. Als u ervoor kiest om versleuteling met uw eigen sleutels te beheren, kunt u een door de **klant beheerde sleutel** opgeven die moet worden gebruikt voor het versleutelen van gegevens in Lab-besturingssysteem schijven. Zie door de [klant beheerde sleutels](../virtual-machines/disk-encryption.md#customer-managed-keys)voor meer informatie over de versleuteling aan de server zijde (SSE) met door de klant beheerde sleutels en andere beheerde schijf versleutelings typen. Zie ook [beperkingen voor het gebruik van door de klant beheerde sleutels](../virtual-machines/disks-enable-customer-managed-keys-portal.md#restrictions).

> [!NOTE]
> - Momenteel wordt schijf versleuteling met een door de klant beheerde sleutel alleen ondersteund voor besturingssysteem schijven in DevTest Labs. 
> 
> - De instelling is van toepassing op nieuw gemaakte besturingssysteem schijven in het lab. Als u ervoor kiest om de schijf versleuteling op een bepaald moment te wijzigen, blijven oudere schijven in het lab versleuteld met de vorige schijf versleutelings. 

In de volgende sectie ziet u hoe een Lab-eigenaar versleuteling kan instellen met behulp van een door de klant beheerde sleutel.

## <a name="pre-requisites"></a>Vereisten

1. Als u geen schijf versleuteling hebt ingesteld, volgt u dit artikel om [een Key Vault en een schijf versleutelings](../virtual-machines/disks-enable-customer-managed-keys-portal.md)in te stellen. Houd rekening met de volgende vereisten voor de schijf versleutelings: 

    - De schijf versleutelings moet zich **in dezelfde regio en hetzelfde abonnement bevinden als uw Lab**. 
    - Zorg ervoor dat u (eigenaar van het lab) ten minste toegang hebt tot de schijf versleuteling die wordt gebruikt om Lab **-** besturingssysteem schijven te versleutelen. 
1. Voor Labs die is gemaakt vóór 8/1/2020, moet de Lab-eigenaar ervoor zorgen dat de identiteit van het test systeem wordt ingeschakeld. Om dit te doen, kan de eigenaar van het lab naar hun Lab gaan, op **configuratie en beleid** klikken, klikken op de Blade **identiteit (preview-versie)** , de systeem **status** van de toegewezen identiteit wijzigen in **aan en op** **Opslaan** klikken. Voor nieuwe Labs die zijn gemaakt nadat de door het systeem toegewezen identiteit van 8/1/2020 Lab standaard is ingeschakeld. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/managed-keys.png" alt-text="Beheerde sleutels":::
1. Voor het lab om versleuteling voor alle schijven van het lab-besturings systeem af te handelen, moet de eigenaar van het lab expliciet de door het **systeem toegewezen identiteits** lezer-rol van het lab verlenen op de schijf versleutelings en de rol Inzender voor virtuele machines op het onderliggende Azure-abonnement. De eigenaar van het Lab kan dit doen door de volgende stappen uit te voeren:

   
    1. Zorg ervoor dat u lid bent van de [rol beheerder van gebruikers toegang](../role-based-access-control/built-in-roles.md#user-access-administrator) op het niveau van het Azure-abonnement, zodat u de gebruikers toegang tot Azure-resources kunt beheren. 
    1. Selecteer op de pagina **schijf versleuteling instellen** de optie **toegangs beheer (IAM)** in het menu links. 
    1. Selecteer **+ toevoegen** op de werk balk en selecteer **een roltoewijzing toevoegen**.  

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/add-role-management-menu.png" alt-text="Rollen beheer toevoegen-menu":::
    1. Selecteer op de pagina **roltoewijzing toevoegen** de rol van **lezer** of een rol die meer toegang toestaat. 
    1. Typ de naam van het lab waarvoor de schijf versleuteling is ingesteld en selecteer de naam van het lab (door het systeem toegewezen identiteit voor het lab) in de vervolg keuzelijst. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/select-lab.png" alt-text="Door het systeem beheerde identiteit van het lab selecteren":::        
    1. Selecteer **Opslaan** op de werkbalk. 

        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/save-role-assignment.png" alt-text="Roltoewijzing opslaan":::
3. Voeg de **systeemtoegewezen identiteit** van het test systeem toe aan de rol van **Inzender voor virtuele machines** met behulp van de   ->  **iam-pagina (Subscription Access Control)** . De stappen zijn vergelijkbaar met die in de vorige stappen. 

    
    1. Navigeer naar de pagina **abonnement** in het Azure Portal. 
    1. Klik op **Toegangsbeheer (IAM)** . 
    1. Selecteer **+ toevoegen** op de werk balk en selecteer **een roltoewijzing toevoegen**. 
    
        :::image type="content" source="./media/encrypt-disks-customer-managed-keys/subscription-access-control-page.png" alt-text="De pagina abonnement-> toegangs beheer (IAM)":::
    1. Selecteer op de pagina **roltoewijzing toevoegen** de optie **Inzender voor virtuele machines** voor de rol.
    1. Typ de naam van het lab en selecteer de naam van het **Lab** (door het systeem toegewezen identiteit voor het lab) in de vervolg keuzelijst. 
    1. Selecteer **Opslaan** op de werkbalk. 

## <a name="encrypt-lab-os-disks-with-a-customer-managed-key"></a>Schijven voor het lab-besturings systeem versleutelen met een door de klant beheerde sleutel 

1. Selecteer op de start pagina voor uw Lab in de Azure Portal **configuratie en beleid** in het menu links. 
1. Selecteer op de pagina **configuratie en beleid** de optie **schijven (preview)** in de sectie **versleuteling** . Het **versleutelings type** is standaard ingesteld op **versleuteling op rest met een door het platform beheerde sleutel**.

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disks-page.png" alt-text="Tabblad schijven van de pagina configuratie en beleid":::
1. Voor **versleutelings type** selecteert u **versleuteling op-rest met een door de klant beheerde sleutel uit de** vervolg keuzelijst. 
1. Voor **schijf versleuteling** selecteert u de schijf versleutelings set die u eerder hebt gemaakt. Dit is dezelfde schijf versleutelings die door de systeem eigen identiteit van het Lab kan worden geopend.
1. Selecteer **Opslaan** op de werkbalk. 

    :::image type="content" source="./media/encrypt-disks-customer-managed-keys/disk-encryption-set.png" alt-text="Versleuteling met door de klant beheerde sleutel inschakelen":::
1. In het bericht venster met de volgende tekst: *deze instelling is van toepassing op nieuw gemaakte machines in het lab. De oude besturingssysteem schijf blijft versleuteld met de oude schijf versleutelings*. Selecteer **OK**. 

    Eenmaal geconfigureerd, worden Lab-besturingssysteem schijven versleuteld met de door de klant beheerde sleutel die is opgegeven met behulp van de schijf Encryption set. 
   
## <a name="how-to-validate-if-disks-are-being-encrypted"></a>Controleren of schijven worden versleuteld

1. Ga naar een virtuele machine die is gemaakt met een lab en waarvoor schijf versleuteling is ingeschakeld met een door de klant beheerde sleutel op het lab.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/enabled-encryption-vm.png" alt-text="VM met ingeschakelde schijf versleuteling":::
1. Klik op de resource groep van de virtuele machine en klik op de besturingssysteem schijf.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/vm-resource-group.png" alt-text="VM-resource groep":::
1. Ga naar versleuteling en controleer of versleuteling is ingesteld op door de klant beheerde sleutel met de schijf versleuteling die u hebt geselecteerd.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/encrypt-disks-customer-managed-keys/validate-encryption.png" alt-text="Versleuteling valideren":::
  
## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen: 

- [Azure Disk Encryption](../virtual-machines/disk-encryption.md). 
- [Door klant beheerde sleutels](../virtual-machines/disk-encryption.md#customer-managed-keys)