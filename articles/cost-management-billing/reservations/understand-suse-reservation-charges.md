---
title: Korting softwareabonnement - Azure | Microsoft Docs
description: Lees hier alles over kortingen voor softwareabonnementen worden toegepast op software op virtuele machines.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: banders
ms.openlocfilehash: 8bf53715b7f19c44d9114150e617f903cd05a51e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566312"
---
# <a name="azure-software-plan-discount"></a>Korting voor abonnement op Azure-software

Abonnementen voor Azure-software voor SUSE en RedHat zijn reserveringen die van toepassing zijn op geïmplementeerde VM's. De korting voor het softwareabonnement wordt toegepast op het softwaregebruik van geïmplementeerde VM's die overeenkomen met de reservering.

Wanneer u een VM afsluit, wordt de korting automatisch toegepast op een andere overeenkomende VM, indien beschikbaar. Een softwareabonnement dekt de kosten voor het uitvoeren van de software op een VM. Andere kosten, zoals die voor berekeningen, opslag en netwerken, worden afzonderlijk in rekening gebracht.

U kunt alleen het juiste abonnement kopen als u een goed beeld hebt van het VM-gebruik en het aantal vCPU's dat op deze VM's wordt uitgevoerd. Gebruik de volgende secties om te bepalen welk abonnement u het beste kunt kopen, op basis van uw gebruiksgegevens.

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Voor een reserveringskorting geldt: '*gebruiken of verliezen*'. Als u voor een bepaald uur geen passende resources hebt, verliest u de reserveringshoeveelheid voor dat uur. U kunt ongebruikte gereserveerde uren niet meenemen.

Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomende resources in het opgegeven bereik worden gevonden, *verliest* u de gereserveerde uren.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Gebruik van Red Hat-VM's controleren voordat u een aankoop doet

Zoek de productnaam op in uw gebruiksgegevens en koop een RedHat-abonnement met hetzelfde type en dezelfde grootte.

Als in de gebruiksgegevens bijvoorbeeld **Red Hat Enterprise Linux - 1-4 vCPU VM License** wordt vermeld, moet u **Red Hat Enterprise Linux** kopen voor **1-4 vCPU VM**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Gebruik van SUSE-VM's controleren voordat u een aankoop doet

Zoek de productnaam op in uw gebruiksgegevens en koop een SUSE-abonnement met hetzelfde type en dezelfde grootte.

Als in de gebruiksgegevens bijvoorbeeld het product **SUSE Linux Enterprise Server Priority - 2-4 vCPU VM Support** wordt vermeld, koopt u **SUSE Linux Enterprise Server Priority** voor **2-4 vCPU**.

![Voorbeeld van het selecteren van het product dat u wilt kopen](./media/understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>Korting is van toepassing op verschillende VM-grootten voor SUSE-abonnementen

Net als bij gereserveerde VM-instanties, biedt het kopen van een SUSE-abonnement flexibiliteit van instantiegrootte. Dit betekent dat uw korting ook van toepassing is wanneer u een VM implementeert met een ander aantal vCPU's. De korting geldt voor verschillende VM-grootten binnen het softwareabonnement.

De korting is afhankelijk van de verhouding die in de volgende tabellen wordt weergegeven. De verhouding vergelijkt de relatieve footprint voor elke meter in die groep. De verhouding is afhankelijk van het aantal VM-vCPU's. Gebruik de verhoudingswaarde om te berekenen hoeveel VM-exemplaren de korting van het SUSE Linux-abonnement krijgen.

Als u bijvoorbeeld een abonnement voor SUSE Linux Enterprise Server for HPC Priority koopt voor een VM met 3 of 4 vCPU's, is de verhouding voor die reservering 2. De korting dekt de kosten van de SUSE-software voor:

- 2 geïmplementeerde VM's met 1 of 2 vCPU's,
- 1 geïmplementeerde VM met 3 of 4 vCPU's
- of 0,77 of ongeveer 77% van een VM machine met 5 of meer vCPU's

De verhouding voor 5 of meer vCPU's is 2,6. Een reservering voor SUSE met een VM met 5 of meer vCPU's dekt dus slechts een gedeelte van de softwarekosten, ongeveer 77%.

In de volgende tabellen ziet u de softwareabonnementen waarvoor u een reservering kunt kopen, de bijbehorende gebruiksmeters en de bijbehorende verhoudingen.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server for HPC Priority

|SUSE-VM | MeterId| Verhouding| Voorbeeld van VM-grootte|
| -------| ------------------------| --- |--- |
|SUSE Linux Enterprise Server for HPC Priority 1-2 Vcpu's|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SUSE Linux Enterprise Server for HPC Priority 3-4 Vcpu's|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SUSE Linux Enterprise Server for HPC Priority 5 + Vcpu's|4edcd5a5-8510-49a8-a9fc-c9721f501913|2,6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

|SUSE-VM | MeterId | Verhouding|Voorbeeld van VM-grootte|
| ------- | --- | ------------------------| --- |
|SUSE Linux Enterprise Server for HPC Standard 1-2 Vcpu's |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SUSE Linux Enterprise Server for HPC Standard 3-4 Vcpu's|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1,92308|D4s_v3|
|SUSE Linux Enterprise Server for HPC Standard 5 + Vcpu's |907a85de-024f-4dd6-969c-347d47a1bdff|2,92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-standard"></a>SUSE Linux Enterprise Server voor SAP Standard

Voorheen werd SUSE Linux Enterprise Server voor SAP Standard de naam SUSE Linux Enterprise Server for SAP Priority.

|SUSE-VM | MeterId | Verhouding|Voorbeeld van VM-grootte|
| ------- |------------------------| --- | --- |
|SUSE Linux Enterprise Server voor SAP Standard 1-2 Vcpu's|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SUSE Linux Enterprise Server voor SAP Standard 3-4 Vcpu's |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SUSE Linux Enterprise Server voor SAP Standard 5 + Vcpu's |18ae79cd-dfce-48c9-897b-ebd3053c6058|2,41176|D8s_v3|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

|SUSE-VM | MeterId | Verhouding|Voorbeeld van VM-grootte|
| ------- |------------------------| --- |--- |
|SUSE Linux Enterprise Server Standard 1-2 kernen Vcpu's |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SUSE Linux Enterprise Server Standard 3-4 kernen Vcpu's |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1,92308|D4s_v3|
|SUSE Linux Enterprise Server Standard 5 + Vcpu's |7b349b65-d906-42e5-833f-b2af38513468|2,30769| D8s_v3|

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie over reserveringen de volgende artikelen:

- [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
- [Vooruitbetalen voor Azure-softwareabonnementen](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Vooruitbetalen voor Virtual Machines met Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Azure-reserveringen beheren](manage-reserved-vm-instance.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)