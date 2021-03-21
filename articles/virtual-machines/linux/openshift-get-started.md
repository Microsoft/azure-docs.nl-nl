---
title: Overzicht van open Shift in azure
description: Een overzicht van open Shift in Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: aba01fc2317372438bc0d93a6618d518ab03ed0d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672316"
---
# <a name="openshift-in-azure"></a>Open Shift in azure

Openshift is een open en uitbreidbaar container toepassings platform dat docker en Kubernetes naar de onderneming brengt.  

Open Shift bevat Kubernetes voor container indeling en-beheer. Hiermee worden hulpprogram ma's voor ontwikkel aars en bewerkingen toegevoegd waarmee het volgende kan worden uitgevoerd:

- Snelle ontwikkeling van toepassingen.
- Eenvoudig implementeren en schalen.
- Onderhoud van de levens cyclus op lange termijn voor teams en toepassingen.

Er zijn meerdere versies van open Shift beschikbaar.  Van deze versies zijn er slechts twee beschikbaar voor klanten om te implementeren in Azure: open Shift container platform en OKD (voorheen open Shift-oorsprong).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat open Shift is een volledig beheerd aanbod van open Shift dat wordt uitgevoerd in Azure. Deze service wordt gezamenlijk beheerd en ondersteund door micro soft en Red Hat. Zie de documentatie van [Azure Red Hat open Shift service](../../openshift/index.yml) voor meer informatie.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container platform is een [commerciële versie](https://www.openshift.com) van bedrijfs eigendom van en wordt ondersteund door Red Hat. Met deze versie kopen klanten de benodigde rechten voor open Shift container platform en zijn ze verantwoordelijk voor de installatie en het beheer van de volledige infra structuur.

Omdat klanten ' eigenaar ' van het hele platform zijn, kunnen ze het installeren in hun on-premises Data Center of in een open bare Cloud (zoals Azure).

## <a name="okd"></a>OKD

OKD is een [open-source](https://www.okd.io/) upstream-project van openshift dat wordt ondersteund door de community. OKD kan worden geïnstalleerd op CentOS of Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Volgende stappen

- [Algemene vereisten voor open Shift configureren in azure](./openshift-container-platform-3x-prerequisites.md)
- [Open Shift container platform implementeren in azure](./openshift-container-platform-3x.md)
- [Self-Managed Marketplace-aanbieding voor open Shift container platform implementeren](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Open Shift in Azure Stack implementeren](./openshift-azure-stack.md)
- [Taken na de implementatie](./openshift-container-platform-3x-post-deployment.md)
- [Problemen met de openshift-implementatie oplossen](./openshift-container-platform-3x-troubleshooting.md)
