---
title: Inzicht in korting voor gereserveerde VM-instanties in Azure
description: Meer informatie over hoe korting voor gereserveerde VM-instanties in Azure wordt toegepast op het gebruik van virtuele machines.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: bda93712dd9a7501fbfddf0e75b8ae3c0088ed55
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341237"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Toepassing van de reserveringskorting op virtuele machines

Wanneer u een gereserveerde VM-instantie in Azure aanschaft, wordt de reserveringskorting automatisch toegepast op virtuele machines die overeenkomen met de kenmerken en hoeveelheid van de reservering. Een reservering dekt de berekeningskosten van uw virtuele machines.

Een reserveringskorting is van toepassing op de basis-VM's die u aanschaft op de Azure Marketplace.

Voor gereserveerde capaciteit voor SQL-databases, zie [Inzicht in korting voor gereserveerde instanties in Azure](../reservations/understand-reservation-charges.md).

De volgende tabel illustreert de kosten voor uw virtuele machine na aankoop van een gereserveerde VM-instantie. In alle gevallen worden er opslag- en netwerkkosten in rekening gebracht tegen de normale tarieven.

| Type virtuele machine  | Kosten met gereserveerde VM-instanties |
|-----------------------|--------------------------------------------|
|Linux-VM's zonder aanvullende software | De reservering dekt de kosten van uw VM-infrastructuur.|
|Linux-VM's met softwarekosten (bijvoorbeeld Red Hat) | De reservering dekt de infrastructuurkosten. Voor aanvullende software worden kosten in rekening gebracht.|
|Windows-VM's zonder aanvullende software |De reservering dekt de infrastructuurkosten. Voor Windows-software worden kosten in rekening gebracht.|
|Windows-VM's met aanvullende software (bijvoorbeeld SQL-server) | De reservering dekt de infrastructuurkosten. Voor Windows-software en aanvullende software worden kosten in rekening gebracht.|
|Windows-VM's met [Azure Hybrid Benefit](../../virtual-machines/windows/hybrid-use-benefit-licensing.md) | De reservering dekt de infrastructuurkosten. De kosten van de Windows-software worden gedekt door de Azure Hybrid Benefit. Eventuele aanvullende software wordt apart in rekening gebracht.|

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Voor een reserveringskorting geldt: '*gebruiken of verliezen*'. Als u voor een bepaald uur geen passende resources hebt, verliest u de reserveringshoeveelheid voor dat uur. U kunt ongebruikte gereserveerde uren niet meenemen.

Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, *verliest* u de gereserveerde uren.

## <a name="reservation-discount-for-non-windows-vms"></a>Reserveringskorting voor niet-Windows-VM's

 De Azure-reserveringskorting wordt toegepast op actieve VM-instanties op uurbasis. De door u aangekochte reserveringen worden gekoppeld aan het gebruik dat de actieve VM’s verzenden, om daarop de reserveringskorting toe te passen. Voor VM's die mogelijk niet het hele uur actief zijn, wordt de reservering ingevuld vanuit andere VM's die geen gebruik maken van een reservering, inclusief VM's die tegelijkertijd actief zijn. Aan het einde van het uur wordt de toepassing van de reservering voor VM's in het betreffende uur geblokkeerd. Indien een VM niet een uur lang actief is of indien gelijktijdig actieve VM's het reserveringsuur niet opvullen, wordt de reservering voor dat uur onderbenut. De volgende grafiek illustreert de toepassing van een reservering voor facturabel VM-gebruik. Het voorbeeld is gebaseerd op één aangekochte reservering en twee gekoppelde VM-instanties.

![Schermopname van één toegepaste reservering en twee gekoppelde VM-instanties](./media/understand-vm-reservation-charges/billing-reserved-vm-instance-application.png)

1. Elk gebruik boven de reserveringslijn wordt gefactureerd tegen de normale tarieven op gebruiksbasis. Er worden geen kosten in rekening gebracht voor gebruik onder de reserveringslijn, aangezien dat al is betaald in het kader van de reservering.
2. In uur 1 is instantie 1 gedurende 0,75 uur actief en instantie 2 gedurende 0,5 uur. Het totale gebruik voor uur 1 bedraagt 1,25 uur. U betaalt het tarief op gebruiksbasis voor de resterende 0,25 uur.
3. In uur 2 en uur 3 waren beide instanties gedurende 1 uur actief. Eén instantie valt onder de reservering en de andere wordt gefactureerd volgens het tarief op gebruiksbasis.
4. In uur 4 is instantie 1 gedurende 0,5 uur actief en instantie 2 gedurende 1 uur. Instantie 1 wordt volledig gedekt door de reservering en van instantie 2 wordt 0,5 uur gedekt. U betaalt het tarief op gebruiksbasis voor de resterende 0,5 uur.

Voor meer informatie over de toepassing van uw Azure-reserveringen in uw gebruiksrapporten voor facturering, raadpleegt u [Inzicht in reserveringsgebruik](../reservations/understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Reserveringskorting voor Windows-VM's

Wanneer u Windows-VM-instanties uitvoert, wordt de reservering toegepast om de infrastructuurkosten te dekken. De toepassing van de reservering op de infrastructuurkosten voor Windows-VM’s werkt hetzelfde als voor niet-Windows-VM's. Voor Windows-software worden de kosten afzonderlijk gefactureerd op vCPU-basis. Zie [Windows-softwarekosten met reserveringen](../reservations/reserved-instance-windows-software-costs.md). U kunt uw Windows-licentiekosten dekken met [Azure Hybrid Benefit for Windows Server](../../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Korting kan van toepassing zijn op verschillende grootten

Wanneer u een gereserveerde VM-instantie aanschaft en **Geoptimaliseerd voor flexibiliteit van instantiegrootte** selecteert, is de kortingsdekking van toepassing op de door u geselecteerde VM-grootte. De dekking kan ook van toepassing zijn op andere VM-grootten die zich in dezelfde flexibiliteitsgroep/-reeks van de instantiegrootte bevinden. Zie [Flexibiliteit van de VM-grootte met gereserveerde VM-instanties](../../virtual-machines/reserved-vm-instance-size-flexibility.md) voor meer informatie.

## <a name="premium-storage-vms-dont-get-non-premium-discounts"></a>Voor VM’s met Premium-opslag gelden geen niet-premium-kortingen

Hier volgt een voorbeeld. Stel dat u een reservering hebt gekocht voor vijf Standard_D1-VM’s. De reserveringskorting is alleen van toepassing op Standard_D1-VM’s of andere VM’s in dezelfde instantiefamilie. De korting is niet van toepassing op Standard_DS1-VM’s of andere grootten in de DS1-flexibiliteitsgroep van de instantiegrootte.

Bij het toepassen van de reserveringskorting wordt de voor VM's gebruikte meter genegeerd, en wordt alleen gekeken naar ServiceType. Bekijk de waarde `ServiceType` in `AdditionalInfo` om de informatie voor de instantieflexibiliteitsgroep/-reeks voor uw VM´s te bepalen. De waarden bevinden zich in het CSV-bestand voor gebruik.

U kunt de instantieflexibiliteitsgroep/-reeks van de reservering niet rechtstreeks wijzigen na de aankoop. U kunt echter een VM-reservering *wisselen* van de ene instantieflexibiliteitsgroep/-reeks naar een andere.

## <a name="services-that-get-vm-reservation-discounts"></a>Services waarop VM-reserveringskortingen van toepassing zijn

Uw VM-reserveringen zijn toepasbaar op het gebruik van VM’s dat vanuit meerdere services wordt verzonden – niet alleen op uw VM-implementaties. Resources die reserveringskortingen krijgen, veranderen afhankelijk van de instelling voor de flexibiliteit van de instantiegrootte.

### <a name="instance-size-flexibility-setting"></a>Instelling voor de flexibiliteit van de instantiegrootte

De instelling voor de flexibiliteit van de instantiegrootte bepaalt welke services de kortingen voor de gereserveerde instanties ontvangen.

Of de instelling nu aan of uit staat, reserveringskortingen worden automatisch toegepast op elk overeenkomend VM-gebruik wanneer de *ConsumedService*`Microsoft.Compute` is. Controleer daarom uw gebruiksgegevens voor de *ConsumedService*-waarde. Voorbeelden zijn:

- Virtuele machines
- Virtuele-machineschaalsets
- Containerservice
- Azure Batch-implementaties (in de modus gebruikersabonnementen)
- Azure Kubernetes Service (AKS)
- Service Fabric

Als de instelling aan staat, worden de reserveringskortingen automatisch toegepast op elk overeenkomstig VM-gebruik wanneer het bij de *ConsumedService* om een van de volgende varianten gaat:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Controleer de *ConsumedService*-waarde in uw gebruiksgegevens om te bepalen of het gebruik in aanmerking komt voor reserveringskortingen.

Zie [Flexibiliteit van de VM-grootte met gereserveerde VM-instanties](../../virtual-machines/reserved-vm-instance-size-flexibility.md) voor meer informatie over flexibiliteit met instantiegrootten.


## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

- [Wat zijn reserveringen voor Azure?](../reservations/save-compute-costs-reservations.md)
- [Vooruitbetalen voor Virtual Machines met Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Vooruitbetalen voor compute-resources van SQL Database met gereserveerde capaciteit voor Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Reserveringen voor Azure beheren](../reservations/manage-reserved-vm-instance.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](../reservations/understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../reservations/understand-reserved-instance-usage-ea.md)
- [Inzicht in het gebruik van reserveringen voor CSP-abonnementen](/partner-center/azure-reservations)
- [Kosten van Windows-software zijn niet inbegrepen in reserveringen](../reservations/reserved-instance-windows-software-costs.md)