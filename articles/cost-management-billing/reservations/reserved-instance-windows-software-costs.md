---
title: Reserveringen van softwarekosten voor Azure
description: Meer informatie over welke softwaremeters niet zijn opgenomen in de kosten voor gereserveerde VM-instanties van Azure.
author: yashesvi
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 625c40ea17cf20449f1be241a1605a80bf6a73f1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352997"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Kosten van software zijn niet inbegrepen in Azure Reserved Virtual Machine Instances

Gereserveerde exemplaren van virtuele machines en korting op gereserveerde capaciteit van SQL zijn alleen van toepassing op de kosten van de infrastructuur en niet op de softwarekosten. Als u Windows VM gebruikt en geen Azure Hybrid Benefit hebt op uw gereserveerde instanties van virtuele machines, worden de softwaremeters in de volgende sectie in rekening gebracht. Voor SQL PaaS-implementaties worden de IP-kosten in rekening gebracht met behulp van een afzonderlijke meter als Azure Hybrid Benefit niet is geselecteerd.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Windows-softwaremeters die niet zijn opgenomen in de reserveringskosten

| MeterId | Meternaam in het gebruiksbestand | Gebruikt door VM |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Reservering-Windows Svr-Burst (1 Core) | B-serie |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Reservering-Windows Svr-Burst (2 Core) | B-serie |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Reservering-Windows Svr-Burst (4 Core) | B-serie |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Reservering-Windows Svr-Burst (8 Core) | B-serie |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Reservering-Windows Svr (1 Core) | Alle behalve de B-serie |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Reservering-Windows Svr (2 Core) | Alle behalve de B-serie |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Reservering-Windows Svr (4 Core) | Alle behalve de B-serie |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Reservering-Windows Svr (6 Core) | Alle behalve de B-serie |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Reservering-Windows Svr (8 Core) | Alle behalve de B-serie |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Reservering-Windows Svr (12 Core) | Alle behalve de B-serie |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Reservering-Windows Svr (16 Core) | Alle behalve de B-serie |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Reservering-Windows Svr (20 Core) | Alle behalve de B-serie |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Reservering-Windows Svr (24 Core) | Alle behalve de B-serie |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Reservering-Windows Svr (32 Core) | Alle behalve de B-serie |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Reservering-Windows Svr (40 Core) | Alle behalve de B-serie |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Reservering-Windows Svr (64 Core) | Alle behalve de B-serie |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Reservering-Windows Svr (72 Core) | Alle behalve de B-serie |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Reservering-Windows Svr (128 Core) | Alle behalve de B-serie |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Reservation-Windows Svr (256 Core) | Alle behalve de B-serie |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Reservering-Windows Svr (96 Core) | Alle behalve de B-serie |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>Cloud Services-softwaremeters die niet zijn opgenomen in de reserveringskosten

| MeterId | Meternaam in het gebruiksbestand |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Cloud Services 1 vCPU-licentie|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Cloud Services 2 vCPU-licentie|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Cloud Services 4 vCPU-licentie|
|13103090-ca72-4825-ab12-7f16c4931d95|Cloud Services 8 vCPU-licentie|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Cloud Services 16 vCPU-licentie|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Cloud Services 20 vCPU-licentie|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Cloud Services 32 vCPU-licentie|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Cloud Services 64 vCPU-licentie|
|7a803026-244c-4659-834c-11e6b2d6b76f|Cloud Services 80 vCPU-licentie|

## <a name="get-rates-for-azure-meters"></a>Tarieven voor Azure-meters ophalen

U kunt de kosten van elk van deze meters ophalen via de Azure RateCard-API. Zie [De informatie over prijzen en metagegevens ophalen voor bronnen die worden gebruikt in een Azure-abonnement](/previous-versions/azure/reference/mt219004(v=azure.100)) voor meer informatie over het ophalen van de tarieven voor een Azure-meter.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg voor meer informatie over reserveringen voor Azure de volgende artikelen:

- [Wat zijn reserveringen voor Azure?](save-compute-costs-reservations.md)
- [Vooruitbetalen voor Virtual Machines met Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Reserveringen voor Azure beheren](manage-reserved-vm-instance.md)
- [Begrijpen hoe de reserveringskorting wordt toegepast](../manage/understand-vm-reservation-charges.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).