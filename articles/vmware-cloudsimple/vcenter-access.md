---
title: Azure VMware-oplossing op CloudSimple-Access vSphere-client
description: Hierin wordt beschreven hoe u toegang krijgt tot de vCenter van uw Privécloud.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2e62042a84f7ac2615762e3d9b036e4340bd8c73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899113"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Toegang tot uw Private Cloud vCenter-Portal

U kunt uw Private Cloud vCenter-Portal starten vanuit Azure Portal of CloudSimple Portal.  met de vCenter-Portal kunt u de VMware-infra structuur beheren in uw Privécloud.

## <a name="before-you-begin"></a>Voordat u begint

Er moet een netwerk verbinding tot stand worden gebracht en DNS-naam omzetting moet zijn ingeschakeld voor toegang tot de vCenter-Portal.  U kunt met behulp van de onderstaande opties netwerk verbinding maken met uw Privécloud.

* [Verbinding maken tussen on-premises en CloudSimple met behulp van ExpressRoute](on-premises-connection.md)
* [Een VPN-verbinding met uw CloudSimple-Privécloud configureren](set-up-vpn.md)

Voor het instellen van de DNS-naam omzetting van uw onderdelen van de VMware-infra structuur van uw Privécloud, raadpleegt u [DNS configureren voor naam omzetting voor Private Cloud vCenter-toegang vanaf on-premises werk stations](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Toegang tot vCenter vanuit Azure Portal

U kunt de vCenter-portal van uw Privécloud starten vanuit Azure Portal.

1. Selecteer **Alle services**.

2. Zoek naar **CloudSimple Services**.

3. Selecteer de CloudSimple-service van de Privécloud waarmee u verbinding wilt maken.

4. Klik op de pagina **overzicht** op **persoonlijke VMware-Clouds weer geven**

    ![Overzicht van de CloudSimple-service](media/cloudsimple-service-overview.png)

5. Selecteer de privécloud in de lijst met persoonlijke Clouds en klik op **vSphere-client starten**.

    ![VSphere-client starten](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Toegang tot vCenter vanuit CloudSimple-Portal

U kunt de vCenter-portal van uw Privécloud starten vanuit de CloudSimple-Portal.

1. Toegang tot uw [CloudSimple-Portal](access-cloudsimple-portal.md).

2. Selecteer in de **resources** de privécloud die u wilt openen en klik op **VSphere-client starten**.

    ![VSphere-client starten-resources](media/cloudsimple-portal-resources-launch-vcenter.png)

3. U kunt ook de vCenter-Portal starten vanuit het overzichts scherm van uw Privécloud.

    ![VSphere-client starten-samen vatting](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Volgende stappen

* [VLAN'S/subnetten voor uw privé-clouds maken en beheren](create-vlan-subnet.md)
* [CloudSimple Private Cloud permission model van VMware vCenter](learn-private-cloud-permissions.md)