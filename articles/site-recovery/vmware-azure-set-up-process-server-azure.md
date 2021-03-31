---
title: Een VMware/fysieke failback van een proces server instellen in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u een proces server in azure instelt om failback van virtuele Azure-machines in VMware te maken.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008470"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Een processerver voor failback in Azure instellen

Nadat u een failover hebt uitgevoerd voor VMware-Vm's of fysieke servers naar Azure met [site Recovery](site-recovery-overview.md), kunt u een failback uitvoeren naar de on-premises site wanneer deze weer actief is. Als u een failback wilt uitvoeren, moet u een tijdelijke proces server in azure instellen om replicatie van Azure naar on-premises af te handelen. U kunt deze virtuele machine verwijderen nadat de failback is voltooid.

## <a name="before-you-start"></a>Voordat u begint

Meer informatie over het proces voor opnieuw [beveiligen](vmware-azure-reprotect.md) en [failback](vmware-azure-failback.md) .

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Een proces server in azure implementeren

1. Selecteer de configuratie server in de kluis > **site Recovery-infra structuur** >    >  **configuratie servers** beheren.
2. Klik op de pagina Server op **+ proces server**
3. Op de pagina **proces server toevoegen** en selecteer om de proces server in azure te implementeren.
4. Geef de Azure-instellingen op, waaronder het abonnement dat wordt gebruikt voor failover, een resource groep, de Azure-regio die wordt gebruikt voor failover en het virtuele netwerk waarin de Azure-Vm's zich bevinden. Als u meerdere Azure-netwerken hebt gebruikt, hebt u een proces server in elk bestand nodig.

   ![Galerie-item proces server toevoegen](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. In **Server naam**, **gebruikers naam** en **wacht woord**, geeft u een naam op voor de proces server en referenties waaraan beheerders machtigingen op de server worden toegewezen.
5. Geef een opslag account op dat moet worden gebruikt voor de VM-schijven van de server, het subnet waarin de virtuele machine van de proces server zich bevindt en het IP-adres van de server dat wordt toegewezen wanneer de virtuele machine wordt gestart.
6. Klik op de knop **OK** om te beginnen met de implementatie van de proces Server-VM. De proces server wordt geïmplementeerd op Standard_A8_v2 SKU. Zorg ervoor dat deze VM-SKU beschikbaar is voor uw abonnement.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>De proces server registreren (die in azure wordt uitgevoerd) naar een configuratie server (die on-premises wordt uitgevoerd)

Nadat de virtuele machine van de proces server actief is, moet u deze als volgt registreren bij de on-premises configuratie server:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


