---
title: De Azure VMware Solution-jumpbox maken
description: Stappen om de Azure VMware Solution-jumpbox te maken.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f746e11763e1df1686f3134960dea167bf1c9908
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462242"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. Selecteer in de resourcegroep **+ Toevoegen**, zoek en selecteer **Microsoft Windows 10** en selecteer vervolgens **Maken**.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Een nieuwe virtuele Windows 10-machine toevoegen voor een jumpbox." border="true":::

1. Voer de vereiste gegevens in de velden in en selecteer **Controleren + maken**. 

   Raadpleeg de volgende tabel voor meer informatie over deze items.

   | Veld | Waarde |
   | --- | --- |
   | **Abonnement** | Waarde is automatisch ingevuld met het Abonnement dat bij de Resourcegroep hoort. |
   | **Resourcegroep** | De waarde is automatisch ingevuld voor de huidige Resourcegroep, die u in de vorige zelfstudie hebt gemaakt.  |
   | **Naam van virtuele machine** | Voer een unieke naam voor de VM in. |
   | **Regio** | Selecteer de geografische locatie van de virtuele machine. |
   | **Beschikbaarheidsopties** | Laat de standaardwaarde ingeschakeld. |
   | **Installatiekopie** | Selecteer de VM-installatiekopie. |
   | **Grootte** | Laat de waarde van de standaardgrootte staan. |
   | **Verificatietype**  | Selecteer **Wachtwoord**. |
   | **Gebruikersnaam** | Voer de gebruikersnaam in voor aanmelding bij de virtuele machine. |
   | **Wachtwoord** | Voer het wachtwoord in om u aan te melden bij de virtuele machine. |
   | **Wachtwoord bevestigen** | Voer het wachtwoord in om u aan te melden bij de virtuele machine. |
   | **Openbare binnenkomende poorten** | Selecteer **Geen**. Als u Geen selecteert, kunt u [JIT-toegang](../../security-center/security-center-just-in-time.md#jit-configure) gebruiken om de toegang tot de virtuele machine alleen te beheren wanneer u toegang wilt. U kunt ook een [Azure-Bastion](../../bastion/tutorial-create-host-portal.md) gebruiken als u op een veilige manier toegang wilt krijgen tot de Jump Box server zonder netwerk poort weer te geven.  |


1. Wanneer de validatie is geslaagd, selecteert u **Maken** om het proces voor het maken van de virtuele machine te starten.

