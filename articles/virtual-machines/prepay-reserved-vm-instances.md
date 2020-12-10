---
title: Vooruitbetalen voor Azure virtual machines om geld te besparen
description: Meer informatie over het kopen van Azure Reserved Virtual Machine Instances om uw reken kosten op te slaan.
author: vikramdesai01
manager: vikramdesai01
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2017
ms.author: vikdesai
ms.openlocfilehash: e513bdaa7efa2f1d0f18ff3548d9abfdc5e085d7
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007395"
---
# <a name="save-costs-with-azure-reserved-vm-instances"></a>Bespaar kosten met Azure Reserved VM Instances



Wanneer u een gereserveerde VM-instantie voor Azure doorvoert, kunt u geld besparen. De reserverings korting wordt automatisch toegepast op het aantal actieve virtuele machines die overeenkomen met het reserverings bereik en de kenmerken. U hoeft geen reserve ring aan een virtuele machine toe te wijzen om de kortingen te krijgen. Voor een gereserveerde instantie aankoop geldt alleen het reken onderdeel van uw VM-gebruik. Voor virtuele Windows-machines wordt de gebruiks meter gesplitst in twee afzonderlijke meters. Er is een compute-meter, die gelijk is aan de Linux meter en een Windows IP-meter. De kosten die u ziet wanneer u de aankoop uitvoert, zijn alleen voor de reken kosten. Kosten zijn niet van toepassing op Windows-software. Zie [software kosten die niet zijn opgenomen in azure reserved VM instances](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)voor meer informatie over software kosten.

## <a name="determine-the-right-vm-size-before-you-buy"></a>De juiste grootte van de virtuele machine bepalen voordat u overgaat tot aankoop

Voordat u een reserve ring koopt, moet u de grootte van de virtuele machine bepalen die u nodig hebt. De volgende secties helpen u bij het bepalen van de juiste VM-grootte.

### <a name="use-reservation-recommendations"></a>Aanbevelingen voor reserverings vereisten gebruiken

U kunt reserverings aanbevelingen gebruiken om te helpen bij het bepalen van de reserve ringen die u moet aanschaffen.

- Aanbevelingen voor aankopen en aanbevolen hoeveelheid worden weer gegeven wanneer u een gereserveerde VM-instantie in de Azure Portal koopt.
- Azure Advisor biedt inkoop aanbevelingen voor afzonderlijke abonnementen.  
- U kunt de Api's gebruiken voor het verkrijgen van inkoop aanbevelingen voor zowel het gedeelde bereik als het bereik van één abonnement. Zie voor meer informatie [gereserveerde instanties aankoop aanbeveling api's voor zakelijke klanten](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Voor Enterprise Agreement (EA) en klanten overeenkomst (MCA) van micro soft, zijn er aanbevelingen voor het delen van gedeelde en enkelvoudige abonnementen beschikbaar met de [Azure Consumption Insights Power bi inhouds pakket](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Services waarop VM-reserveringskortingen van toepassing zijn

Uw VM-reserveringen zijn toepasbaar op het gebruik van VM’s dat vanuit meerdere services wordt verzonden – niet alleen op uw VM-implementaties. Resources die reserveringskortingen krijgen, veranderen afhankelijk van de instelling voor de flexibiliteit van de instantiegrootte.

#### <a name="instance-size-flexibility-setting"></a>Instelling voor de flexibiliteit van de instantiegrootte

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

Zie [Flexibiliteit van de VM-grootte met gereserveerde VM-instanties](reserved-vm-instance-size-flexibility.md) voor meer informatie over flexibiliteit met instantiegrootten.

### <a name="analyze-your-usage-information"></a>Uw gebruiks gegevens analyseren

Analyseer uw gebruiks gegevens om te helpen bepalen welke reserve ringen u moet aanschaffen. Gebruiks gegevens zijn beschikbaar in het gebruiks bestand en Api's. Gebruik ze samen om te bepalen welke reserve ring moet worden gekocht. Controleren op VM-exemplaren met hoog gebruik op dagelijkse basis om het aantal te kopen reserve ringen te bepalen. Vermijd de `Meter` subcategorie en `Product` velden in gebruiks gegevens. Ze maken geen onderscheid tussen VM-grootten die Premium-opslag gebruiken. Als u deze velden gebruikt om de grootte van de virtuele machine te bepalen voor de reserve ring, kunt u de verkeerde grootte aanschaffen. Vervolgens krijgt u niet de verwachte reserverings korting. In plaats daarvan raadpleegt u het `AdditionalInfo` veld in uw gebruiks bestand of gebruiks-API om de juiste VM-grootte te bepalen.

Uw gebruiks bestand toont uw kosten per facturerings periode en dagelijks gebruik. Zie [uw Azure-gebruik en-kosten bekijken en downloaden](../cost-management-billing/understand/download-azure-daily-usage.md)voor meer informatie over het downloaden van uw gebruiks bestand. Vervolgens kunt u met behulp van de informatie in het gebruiks bestand [bepalen welke reserve ring moet worden gekocht](../cost-management-billing/reservations/determine-reservation-purchase.md).

### <a name="purchase-restriction-considerations"></a>Overwegingen voor de aankoop beperking

Gereserveerde VM-instanties zijn beschikbaar voor de meeste VM-grootten met enkele uitzonde ringen. Er zijn geen reserverings kortingen van toepassing op de volgende Vm's:

- **VM-reeks** -A-serie, Av2-serie of G-serie.

- **Preview-of promotie vm's** : elke VM-serie of-grootte die in preview is of een promotie meter gebruikt.

- **Clouds** -reserve ringen zijn niet beschikbaar voor aankopen in de regio's Duitsland en China.

- **Onvoldoende quotum** : voor een reserve ring die is toegewezen aan één abonnement, moet vCPU quotum beschikbaar zijn in het abonnement voor de nieuwe ri. Als het doel abonnement bijvoorbeeld een quotum limiet heeft van 10 Vcpu's voor de D-serie, kunt u geen reserve ring kopen voor elf Standard_D1 exemplaren. De quotum controle voor reserve ringen omvat de Vm's die al zijn geïmplementeerd in het abonnement. Als het abonnement bijvoorbeeld een quotum heeft van 10 Vcpu's voor de D-serie en er twee standard_D1 instanties zijn geïmplementeerd, kunt u een reserve ring kopen voor 10 standard_D1-instanties in dit abonnement. U kunt een [offerte verhogings aanvraag maken](../azure-portal/supportability/resource-manager-core-quotas-request.md) om dit probleem op te lossen.

- **Capaciteits beperkingen** : in zeldzame omstandigheden beperkt Azure de aanschaf van nieuwe reserve ringen voor SUBSET van VM-grootten, vanwege een geringe capaciteit in een regio.

## <a name="buy-a-reserved-vm-instance"></a>Een gereserveerde VM-instantie kopen

U kunt een gereserveerde VM-instantie kopen in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Betaal [vooraf of per maand](../cost-management-billing/reservations/prepare-buy-reservation.md) voor de reservering.
Deze vereisten zijn van toepassing op het kopen van een gereserveerde VM-instantie:

- U moet een rol van eigenaar zijn voor ten minste één EA-abonnement of een abonnement met een betalen naar gebruik-tarief.
- Voor EA-abonnementen moet de optie **gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA-Portal](https://ea.azure.com/). Of, als deze instelling is uitgeschakeld, moet u een EA-beheerder van het abonnement zijn.
- Voor het programma Cloud Solution Provider (CSP) kunnen alleen de beheerders of verkoop medewerkers reserve ringen kopen.

Een instantie kopen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Alle services** > **Reserveringen**.
1. Selecteer **Toevoegen** om een nieuwe reservering te kopen en klik op **Virtuele machine**.
1. Vul de verplichte velden in. Actieve VM-instanties die overeenkomen met de kenmerken die u selecteert, komen in aanmerking voor de reserveringskorting. Het werkelijke aantal VM-exemplaren dat de korting krijgt, is afhankelijk van het bereik en de geselecteerde hoeveelheid.

Als u een EA-overeenkomst hebt, kunt u de **optie meer toevoegen** gebruiken om snel extra instanties toe te voegen. De optie is niet beschikbaar voor andere typen abonnementen.


| Veld      | Beschrijving|
|------------|--------------|
|Abonnement|Het abonnement dat wordt gebruikt om te betalen voor de reserve ring. Via de betalingswijze voor het abonnement worden de kosten voor de reservering in rekening gebracht. Het abonnements type moet een Enter prise Agreement zijn (nummer van de aanbieding: MS-AZR-0017P of MS-AZR-0148P) of een micro soft-klant overeenkomst of een afzonderlijk abonnement met betalen per gebruik-tarieven (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). De kosten worden in mindering gebracht op het saldo van het reserveringsbedrag, indien beschikbaar, of in rekening gebracht als overschrijding. Voor een abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht op basis van de credit card-of factuur betalings methode voor het abonnement.|    
|Bereik       |Het bereik van de reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u het volgende selecteert: <ul><li>**Bereik van één resourcegroep**: de reserveringskorting wordt alleen toegepast op de overeenkomende resources in de geselecteerde resourcegroep.</li><li>**Bereik van één abonnement**: de reserveringskorting wordt toegepast op de overeenkomende resources in het geselecteerde abonnement.</li><li>**Gedeeld bereik**: de reserveringskorting wordt toegepast op overeenkomende resources binnen in aanmerking komende abonnementen die zich in de factureringscontext bevinden. Voor EA-klanten is de facturerings context de inschrijving. Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.</li></ul>|
|Regio    |De Azure-regio die wordt gedekt door de reserve ring.|    
|VM-grootte     |De grootte van de VM-exemplaren.|
|Optimaliseren voor     |De flexibiliteit van VM-instantie grootte is standaard geselecteerd. Klik op **Geavanceerde instellingen** om de waarde voor de flexibiliteit van de instantie grootte te wijzigen om de reserverings korting toe te passen op andere virtuele machines in dezelfde [VM-grootte groep](reserved-vm-instance-size-flexibility.md). Met de capaciteitsprioriteit wordt prioriteit toegekend aan de datacentercapaciteit voor uw implementaties. Het biedt extra vertrouwen in uw vermogen om de VM-exemplaren te starten wanneer u ze nodig hebt. Capaciteits prioriteit is alleen beschikbaar wanneer het reserverings bereik één abonnement is. |
|Term        |Eén jaar of drie jaar. Er is ook een periode van 5 jaar die alleen beschikbaar is voor HBv2 Vm's.|
|Aantal    |Het aantal exemplaren dat wordt aangeschaft binnen de reserve ring. De hoeveelheid is het aantal actieve VM-exemplaren waarmee de factuur korting kan worden verkregen. Als u bijvoorbeeld tien Standard_D2 Vm's uitvoert in het VS-Oost, geeft u hoeveelheid op als 10 om het voor deel voor alle actieve Vm's te maximaliseren. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Gebruiks gegevens en reserverings gebruik

Uw gebruiksgegevens kosten niets voor het deel waarvoor u een reserveringskorting verkrijgt. U kunt zien welke VM-instantie de reserverings korting voor elke reserve ring heeft ontvangen.

Zie het [gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) als u een EA-klant bent voor meer informatie over hoe reserverings kortingen worden weer gegeven in gebruiks gegevens. Zie [het gebruik van Azure-reserve ringen voor uw abonnement op basis van betalen per gebruik voor meer informatie](../cost-management-billing/reservations/understand-reserved-instance-usage.md)over een afzonderlijk abonnement.

## <a name="change-a-reservation-after-purchase"></a>Een reserve ring wijzigen na aankoop

Na aankoop kunt u de volgende typen wijzigingen aanbrengen in een reservering:

- Het bereik van de reservering bijwerken
- Flexibiliteit van de instantie grootte (indien van toepassing)
- Eigendom

U kunt ook een reserve ring in kleinere segmenten splitsen en al gesplitste reserve ringen samen voegen. Geen van de wijzigingen veroorzaakt een nieuwe commerciële trans actie of wijzigt de eind datum van de reserve ring.

U kunt de volgende typen wijzigingen na de aankoop niet rechtstreeks aanbrengen:

- Een bestaande reserverings regio
- SKU
- Aantal
- Duur

U kunt echter wel een reserve ring *uitwisselen* als u wijzigingen wilt aanbrengen.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure-reserveringen beheren](../cost-management-billing/reservations/manage-reserved-vm-instance.md) voor meer informatie over het beheren van een reservering.
- Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:
    - [Wat zijn Azure-reserveringen?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Reserveringen beheren in Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [Begrijpen hoe de reserveringskorting wordt toegepast](../cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Kosten van Windows-software zijn niet inbegrepen in reserveringen](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [Azure-reserveringen in CSP-programma (Cloud Solution Provider) van partnercentrum](/partner-center/azure-reservations)