---
title: Overzicht van Red Hat-workloads op Azure | Microsoft Docs
description: Meer informatie over de productaanbiedingen van Red Hat die beschikbaar zijn op Azure.
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: bd8f2e5b825f88d1b0e04ed231b85296ac47e998
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676051"
---
# <a name="red-hat-workloads-on-azure"></a>Red Hat-workloads op Azure

Red Hat-workloads worden ondersteund door diverse aanbiedingen op Azure. Red Hat Enterprise Linux (RHEL)-installatiekopieën zijn de kern van RHEL-workloads, zoals de Red Hat Update Infrastructure (RHUI).

## <a name="red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux-installatiekopieën

Azure biedt een breed aanbod aan RHEL-installatiekopieën in Azure. Deze installatiekopieën worden beschikbaar gesteld via twee verschillende licentiemodellen: betalen per gebruik en bring-your-own-subscription (BYOS). Nieuwe RHEL-installatiekopieën in Azure worden gepubliceerd wanneer nieuwe RHEL-versies in hun levensduur worden uitgebracht en bijgewerkt, indien nodig.

### <a name="pay-as-you-go-images"></a>Betalen per gebruik-installatiekopieën

Azure biedt een aantal RHEL betalen per gebruik-installatiekopieën. Deze installatiekopieën zijn geschikt voor RHEL en zijn gekoppeld aan een bron van updates (Red Hat Update Infrastructure). Met deze installatiekopieën worden Premium-kosten in rekening gebracht voor het RHEL-recht en de updates. RHEL-varianten voor betalen per gebruik zijn onder andere:

* Standaard RHEL.
* RHEL for SAP.
* RHEL for SAP met hoge beschikbaarheid en Update Services.

U kunt de betalen per gebruik-installatiekopieën gebruiken als u zich geen zorgen meer wilt maken over het aantal afzonderlijke abonnementen.

### <a name="red-hat-gold-images"></a>Red Hat Gold-installatiekopieën

Azure biedt ook Red Hat Gold-installatiekopieën (`rhel-byos`). Deze installatiekopieën kunnen nuttig zijn voor klanten die bestaande Red Hat-abonnementen hebben en ze willen gebruiken in Azure. U bent verplicht uw bestaande Red Hat-abonnementen voor Red Hat Cloud Access in te schakelen voordat u deze kunt gebruiken in Azure. Toegang tot deze installatiekopieën wordt automatisch verleend wanneer uw Red Hat-abonnementen zijn ingeschakeld voor Cloudtoegang en voldoen aan de geschiktheidsvereisten. Door gebruik te maken van deze installatiekopieën kan een klant dubbele facturering voorkomen die kan ontstaan door het gebruik van de betalen per gebruik-installatiekopie.
* Lees hoe u uw [Red Hat-abonnementen kunt gebruiken voor Cloud-toegang met Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/enabling-and-maintaining-subs_cloud-access).
* Meer informatie over [het vinden van de Red Hat Gold-installatiekopieën in Azure Portal, Azure CLI of de PowerShell-cmdlet](./byos.md).

> [!NOTE]
> Dubbele facturering komt voor wanneer een gebruiker twee keer betaalt voor RHEL-abonnementen. Dit scenario treedt meestal op wanneer een klant een Red Hat Subscription-Manager gebruikt om een recht te koppelen aan een RHEL-VM met betalen per gebruik. Een klant die bijvoorbeeld gebruikmaakt van Subscription-Manager om een recht te koppelen voor SAP-pakketten op een RHEL betalen per gebruik-installatiekopie, wordt indirect dubbel gefactureerd, omdat hij twee keer betaalt voor RHEL. Hij betaalt eenmaal via de betalen per gebruik-tarieven en eenmaal via zijn SAP-abonnement. Dit scenario komt niet voor bij gebruikers van de BYOS-installatiekopie.

### <a name="generation-2-images"></a>Installatiekopieën van de 2e generatie

Virtuele machines (VM's) van de 2e generatie bieden een aantal nieuwere functies in vergelijking met virtuele machines van de 1e generatie. Voor meer informatie raadpleegt u de [documentatie over de 2e generatie](../../generation-2.md). Het belangrijkste verschil van een RHEL-installatiekopieperspectief is dat virtuele machines van de 2e generatie een UEFI gebruiken in plaats van BIOS-firmware-interface. Ze gebruiken ook een GUID-partitietabel (GPT) in plaats van een Master Boot Record (MBR) bij het opstarten. Het gebruik van een GPT maakt onder andere een besturingssysteemschijf die groter is dan 2 TB mogelijk. Daarnaast worden de [VM's van de Mv2-serie](../../mv2-series.md) alleen uitgevoerd op installatiekopieën van de 2e generatie.

RHEL installatiekopieën van de 2e generatie zijn beschikbaar via Azure Marketplace. Zoek naar Gen2 in de installatiekopie-SKU in de lijst met alle installatiekopieën die worden weergegeven wanneer u Azure CLI gebruikt. Ga naar het tabblad **Geavanceerd** in het implementatieproces van de VM om een virtuele machine van de 2e generatie te implementeren.

## <a name="red-hat-update-infrastructure"></a>Update-infrastructuur voor Red Hat

Azure biedt alleen een Red Hat Update-infrastructuur voor RHEL VM's met betalen per gebruik. RHUI is in feite een mirror van de Red Hat CDN's, maar is alleen toegankelijk voor de RHEL VM's van Azure betalen per gebruik. U hebt toegang tot de juiste pakketten, afhankelijk van de RHEL-installatiekopie die u hebt geïmplementeerd. Zo heeft een RHEL voor een SAP-installatiekopie ook toegang tot de SAP-pakketten en basis RHEL-pakketten.

### <a name="rhui-update-behavior"></a>Gedrag van RHUI-updates

RHEL-installatiekopieën die zijn verbonden met RHUI, worden standaard bijgewerkt naar de meest recente secundaire versie van RHEL wanneer een `yum update` wordt uitgevoerd. Dit gedrag houdt in dat een RHEL 7.4-VM kan worden bijgewerkt naar RHEL 7.7 als er een `yum update` bewerking wordt uitgevoerd. Dit gedrag is standaard voor RHUI. Als u dit upgradegedrag wilt beperken, schakelt u over van reguliere RHEL-opslagplaatsen naar [opslagplaatsen met uitgebreide ondersteuning voor bijwerken](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [RHEL-installatiekopieën in Azure](./redhat-images.md).
* Meer informatie over [Red Hat Update-infrastructuur](./redhat-rhui.md).
* Meer informatie over de [Red Hat Gold image (`rhel-byos`)-aanbieding](./byos.md).