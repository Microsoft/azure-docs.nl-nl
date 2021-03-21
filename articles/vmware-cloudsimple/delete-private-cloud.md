---
title: Een Azure VMware-oplossing verwijderen via CloudSimple Private Cloud
description: Meer informatie over het verwijderen van een CloudSimple-Privécloud. Wanneer u een Privécloud verwijdert, worden alle clusters verwijderd.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7db967955dc86db39db4dcb2b3a2baf8906efb20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97896257"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Een CloudSimple-Privécloud verwijderen

CloudSimple biedt de flexibiliteit voor het verwijderen van een Privécloud.  Een Privécloud bestaat uit een of meer vSphere-clusters. Elk cluster kan 3 tot 16 knoop punten bevatten. Wanneer u een Privécloud verwijdert, worden alle clusters verwijderd.

## <a name="before-you-begin"></a>Voordat u begint

Als een Privécloud wordt verwijderd, wordt de volledige Privécloud verwijderd.  Alle onderdelen van de Privécloud worden verwijderd.  Als u gegevens wilt bewaren, moet u ervoor zorgen dat u een back-up hebt gemaakt van de gegevens naar on-premises opslag of Azure Storage.

De onderdelen van een Privécloud zijn:

* CloudSimple-knoop punten
* Virtuele machines
* VLAN's/subnetten
* Alle gebruikers gegevens die in de Privécloud zijn opgeslagen
* Alle bijlagen van firewall regels naar een VLAN/subnet

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-a-private-cloud"></a>Een privécloud verwijderen

1. [Toegang tot de CloudSimple-Portal](access-cloudsimple-portal.md).

2. Open de pagina **resources** .

3. Klik op de Privécloud die u wilt verwijderen

4. Klik op de pagina samen vatting op **verwijderen**.

    ![Privécloud verwijderen](media/delete-private-cloud.png)

5. Voer op de pagina Bevestiging de naam van de Privécloud in en klik op **verwijderen**. 

    ![Privécloud verwijderen-bevestigen](media/delete-private-cloud-confirm.png)

De Privécloud is gemarkeerd voor verwijdering.  Het verwijderings proces wordt na drie uur gestart en de Privécloud wordt verwijderd.

> [!CAUTION]
> Knoop punten moeten worden verwijderd na het verwijderen van de Privécloud.  Door knoop punten te meten, worden de kassa knooppunten verwijderd uit uw abonnement.

## <a name="next-steps"></a>Volgende stappen

* [Knooppunten verwijderen](delete-nodes.md)
