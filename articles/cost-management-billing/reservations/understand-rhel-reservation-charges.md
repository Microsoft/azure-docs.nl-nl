---
title: Abonnementskortingen voor Red Hat-reserveringen - Azure
description: Meer informatie over hoe Red Hat-abonnementskortingen worden toegepast op Red Hat-software op virtuele machines.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 8e735d623cc991d2e3720e0ec418eee77aa8c46c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350854"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Meer informatie over hoe de abonnementskorting voor Red Hat Linux Enterprise-softwarereservering wordt toegepast voor Azure

Nadat u een Red Hat Linux-abonnement hebt gekocht, wordt de korting automatisch toegepast op geïmplementeerde virtuele machines (VM's) met Red Hat die overeenkomen met de reservering. Een Red Hat Linux-abonnement dekt de kosten voor het uitvoeren van de Red Hat-software op een virtuele Azure-machine.

Als u het juiste Red Hat Linux-abonnement wilt kopen, moet u weten wat voor virtuele Red Hat-machines u uitvoert en hoeveel vCPU's deze VM's gebruiken. Gebruik de volgende secties om met behulp van het CSV-bestand over uw gebruik te bepalen welk abonnement u het beste kunt kopen.

## <a name="discount-applies-to-different-vm-sizes"></a>Korting is van toepassing op verschillende VM-grootten

Net als gereserveerde VM-instanties, biedt het kopen van een Red Hat-abonnement flexibiliteit van instantiegrootte. Dit betekent dat uw korting ook van toepassing is wanneer u een VM implementeert met een ander aantal vCPU's. De korting geldt voor verschillende VM-grootten binnen het softwareabonnement.

De korting is afhankelijk van de verhouding die in de volgende tabellen wordt weergegeven. De verhouding vergelijkt de relatieve footprint voor elke VM-grootte in die groep. De verhouding is afhankelijk van het aantal VM-vCPU's. Gebruik de verhoudingswaarde om te berekenen hoeveel VM-exemplaren de Red Hat Linux-abonnementskorting krijgen.

Als u bijvoorbeeld een abonnement voor Red Hat Linux Enterprise Server koopt voor een virtuele machine met 1 tot 4 vCPU's, is de verhouding voor die reservering 1. De korting geldt voor de kosten van de Red Hat-software voor:

- 1 geïmplementeerde VM met 1 tot 4 vCPU's
- of 0,46 of ongeveer 46% van de Red Hat Enterprise Linux kosten voor een virtuele machine met 5 of meer vCPU’s.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Namen op Marketplace van Azure-portal:
- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 8.2

[Red Hat Enterprise Linux meters controleren waarop het abonnement van toepassing is](https://isfratio.blob.core.windows.net/isfratio/RHELRatios.csv)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie over reserveringen de volgende artikelen:

- [Wat zijn reserveringen voor Azure?](save-compute-costs-reservations.md)
- [Vooruitbetalen voor Red Hat-softwareabonnementen met Azure-reserveringen](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Vooruitbetalen voor Virtual Machines met Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Reserveringen voor Azure beheren](manage-reserved-vm-instance.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).