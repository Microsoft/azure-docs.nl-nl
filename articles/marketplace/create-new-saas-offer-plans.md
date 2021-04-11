---
title: Plannen maken voor uw SaaS-aanbieding in het micro soft partner centrum
description: Het maken van plannen voor een nieuwe SaaS-aanbieding (Software as a Service) met behulp van de micro soft Commercial Marketplace-Portal in het partner centrum.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: fb1514c146dcd352451597e6182c6d99ed8836d6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044716"
---
# <a name="how-to-create-plans-for-your-saas-offer"></a>Plannen maken voor uw SaaS-aanbieding

Aanbiedingen die via micro soft Commercial Marketplace worden verkocht, moeten ten minste één abonnement hebben. U kunt diverse plannen maken met verschillende opties binnen dezelfde aanbieding. Deze plannen (ook wel Sku's genoemd) kunnen verschillen qua versie, verdiensten maximaliseren of service lagen. Zie [Abonnementen en prijzen voor commerciële Marketplace-aanbiedingen](plans-pricing.md)voor gedetailleerde richt lijnen voor abonnementen.

> [!NOTE]
> Als u ervoor kiest om trans acties onafhankelijk te verwerken, wordt deze optie niet weer geven. Ga in plaats daarvan naar [de verkoop van uw SaaS-aanbieding](create-new-saas-offer-marketing.md).

## <a name="create-a-plan"></a>Een plan maken

1. Selecteer boven aan het tabblad **plan overzicht** de optie **+ nieuw plan maken**.

1. Voer in het dialoog venster dat wordt weer gegeven in het vak **plan-id** een unieke plan-id in. Gebruik Maxi maal 50 kleine letters, streepjes of onderstrepings tekens. U kunt de plan-ID niet wijzigen nadat u **maken** hebt geselecteerd.

1. Voer in het vak **naam van abonnement** een unieke naam in voor dit abonnement. Gebruik Maxi maal 50 tekens.

1. Selecteer **Maken**.

## <a name="define-the-plan-listing"></a>De plan vermelding definiëren

Op het tabblad **plan vermelding** kunt u de naam en beschrijving van het plan definiëren zoals u wilt dat deze in de commerciële Marketplace worden weer gegeven.

1. In het vak **naam van abonnement** is de naam die u eerder hebt ingesteld voor dit plan hier weer gegeven. U kunt deze op elk gewenst moment wijzigen. Deze naam wordt weer gegeven in de commerciële Marketplace als de titel van het software abonnement van uw aanbieding.

1. In het vak **plan beschrijving** wordt uitgelegd wat dit software plan uniek is en wat de verschillen zijn van andere abonnementen binnen uw aanbieding. Deze beschrijving mag Maxi maal 500 tekens bevatten.
1. Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad: **prijzen en beschik baarheid**.

## <a name="define-markets-pricing-and-availability"></a>Markten, prijzen en beschik baarheid definiëren

Elk plan moet beschikbaar zijn op ten minste één markt. Op het tabblad **prijzen en beschik baarheid** kunt u de markten configureren waarop dit abonnement beschikbaar is, het gewenste verdiensten maximaliseren model, de prijs en de facturerings voorwaarden. Daarnaast kunt u aangeven of u het plan zichtbaar wilt maken voor iedereen of alleen voor specifieke klanten (ook wel een persoonlijk abonnement genoemd).

1. Onder **markten** selecteert u de koppeling **markt bewerkings** .
1. Selecteer in het dialoog venster dat wordt weer gegeven de markt locaties waar u uw plan beschikbaar wilt maken. U moet mini maal één en Maxi maal 141 markten selecteren.

   > [!NOTE]
   > Dit dialoog venster bevat een zoekvak en een optie voor het filteren van alleen de landen ' BTW geremitteerd ', waarbij micro soft namens u verkoop-en gebruiks belasting verkent.

1. Selecteer **Opslaan** om het dialoog venster te sluiten.

## <a name="define-a-pricing-model"></a>Een prijs model definiëren

U moet een prijs model koppelen aan elk abonnement: een _vast tarief_ of _per gebruiker_. Alle abonnementen in hetzelfde aanbod moeten gebruikmaken van hetzelfde prijs model. Een aanbieding kan bijvoorbeeld niet één abonnement hebben dat een vast tempo heeft en een ander abonnement dat per gebruiker is. Zie voor meer informatie [SaaS-prijs modellen](plan-saas-offer.md#saas-pricing-models).

> [!IMPORTANT]
> Nadat uw aanbieding is gepubliceerd, kunt u het prijs model niet meer wijzigen. Daarnaast moeten alle abonnementen voor hetzelfde aanbod hetzelfde prijs model delen.

### <a name="configure-flat-rate-pricing"></a>Prijzen voor vaste tarieven configureren

1. Selecteer op het tabblad **prijzen en beschik baarheid** onder **prijzen** de optie **vast tarief**.
1. Schakel het selectie vakje **maandelijks** of **jaarlijks** in of geef beide op en voer vervolgens de prijs in.

### <a name="add-a-custom-meter-dimension"></a>Een aangepaste meter dimensie toevoegen

Deze optie is alleen beschikbaar als u vaste tarief prijzen hebt geselecteerd. Zie voor meer informatie [gecontroleerde facturering voor SaaS met behulp van de commerciële Marketplace meter service](./partner-center-portal/saas-metered-billing.md).

1. Selecteer onder **service dimensies Marketplace-meting** de koppeling **een aangepaste meter dimensie toevoegen (Maxi maal 30)** .
1. Voer in het vak **id** de verwijzing onveranderbare id in terwijl u gebruiks gebeurtenissen verzendt.
1. Voer in het vak **weergave naam** de weergave naam in die is gekoppeld aan de dimensie. Bijvoorbeeld ' verzonden tekst berichten '.
1. Voer in het vak **maat eenheid** de beschrijving van de facturerings eenheid in. Bijvoorbeeld ' per SMS-bericht ' of ' per 100 e-mail berichten '.
1. Voer in het vak **prijs per eenheid in USD** de prijs voor één eenheid van de dimensie in.
1. Voer in het vak **basis bedrag per** maand de hoeveelheid (als een geheel getal) in van de dimensie die wordt opgenomen in de maandelijkse kosten. Als u een onbeperkt aantal wilt instellen, schakelt u het selectie vakje in.
1. In het **jaarlijkse aantal dat is opgenomen in de basis** doos, voert u de hoeveelheid van de dimensie (als een geheel getal) in die wordt opgenomen in elke maand voor klanten die de terugkerend jaarlijkse kosten betalen. Als u een onbeperkt aantal wilt instellen, schakelt u het selectie vakje in.
1. Als u een andere aangepaste meter dimensie wilt toevoegen, selecteert u de koppeling **een andere dimensie toevoegen** en herhaalt u stap 1 tot en met 7.

### <a name="configure-per-user-pricing"></a>Prijzen per gebruiker configureren

1. Op het tabblad **prijzen en beschik baarheid** onder **prijzen** selecteert u **per gebruiker**.
2. Geef, indien van toepassing, onder **gebruikers limieten** het minimale en maximale aantal gebruikers voor dit abonnement op.
3. Geef onder **facturerings termijn** een maandelijks tarief, een jaarlijkse prijs op of beide.

### <a name="validate-custom-prices"></a>Aangepaste prijzen valideren

Als u aangepaste prijzen wilt instellen in een afzonderlijke markt, exporteert, wijzigt en importeert u vervolgens de prijs informatie in het werk blad. U bent verantwoordelijk voor het valideren van deze prijzen en de eigenaar van deze instellingen. Zie [aangepaste prijzen](plans-pricing.md#custom-prices)voor gedetailleerde informatie.

1. U moet uw prijs wijzigingen eerst opslaan om het exporteren van prijs gegevens in te scha kelen. Klik onder aan het tabblad **prijzen en beschik baarheid** op **concept opslaan**.
1. Selecteer onder **prijzen** de koppeling **prijs gegevens exporteren** .
1. Open het exportedPrice.xlsx-bestand in micro soft Excel.
1. Breng de gewenste wijzigingen aan in de prijs informatie in het werk blad en sla het op. CSV-bestand.<br> Mogelijk moet u bewerken in Excel inschakelen voordat u het bestand kunt bijwerken.
2. Selecteer op het tabblad **prijzen en beschik baarheid** onder **prijzen** de koppeling **prijs gegevens importeren** .
3. In het dialoog venster dat wordt weer gegeven, klikt u op **Ja**.
4. Selecteer het exportedPrice.xlsx bestand dat u hebt bijgewerkt en klik vervolgens op **openen**.

### <a name="enable-a-free-trial"></a>Een gratis proef versie inschakelen

U kunt een gratis proef versie configureren voor elk abonnement in uw aanbieding. Schakel het selectie vakje in om een gratis proef versie van één maand toe te staan. Dit selectie vakje is niet beschikbaar voor abonnementen die gebruikmaken van de Marketplace-meter service. Zie [gratis proef versies](plans-pricing.md#free-trials)voor meer informatie.

> [!IMPORTANT]
> Nadat uw voor stel is gepubliceerd met een gratis proef versie, kan het niet worden uitgeschakeld voor dat abonnement. Zorg ervoor dat deze instelling juist is voordat u de aanbieding publiceert om te voor komen dat u het abonnement opnieuw moet maken.

- Schakel onder **gratis proef versie** het selectie vakje **een gratis proef versie van één maand toestaan** in.

## <a name="choose-who-can-see-your-plan"></a>Kies wie uw abonnement kan zien

U kunt elk plan zodanig configureren dat het zichtbaar is voor iedereen of alleen voor een specifieke doel groep. U verleent toegang tot een privé-abonnement met behulp van Tenant-Id's met de optie om een beschrijving op te geven van elke Tenant-ID die u toewijst. U kunt Maxi maal 10 Tenant-Id's hand matig of Maxi maal 20.000 Tenant-Id's toevoegen met behulp van een. CSV-bestand.

> [!NOTE]
> Als u een privé-abonnement publiceert, kunt u de zicht baarheid later wijzigen in openbaar. Wanneer u echter een openbaar abonnement publiceert, kunt u de zicht baarheid niet wijzigen in privé.

### <a name="make-your-plan-public"></a>Uw plan openbaar maken

1. Onder **plan zicht baarheid** selecteert u het **open bare** vak.
1. Selecteer **concept opslaan** en selecteer vervolgens in de linkerbovenhoek van het tabblad **plan overzicht** om terug te keren naar het tabblad **overzicht** van het plan.
1. Als u een ander abonnement voor deze aanbieding wilt maken, selecteert u **+ nieuw plan maken** boven aan het tabblad Overzicht van het **plan** . Herhaal vervolgens de stappen in de sectie [een plan maken](#create-a-plan) . Als dat niet het geval is, gaat u naar [uw plannen weer geven](#view-your-plans).

### <a name="manually-add-tenant-ids-for-a-private-plan"></a>Tenant-Id's hand matig toevoegen voor een privé-abonnement 

1. Schakel onder **zicht baarheid van plan** het selectie vakje **Dit is een privé plan** in.
1. In het vak **Tenant-id** dat wordt weer gegeven, voert u de Azure AD-Tenant-id in van de doel groep die u toegang wilt verlenen tot dit privé-abonnement. Er is mini maal één Tenant-ID vereist.
1. Beschrijving Voer een beschrijving van deze doel groep in het vak **Beschrijving** in.
1. Herhaal stap 2 en 3 om een andere Tenant-ID toe te voegen.
1. Wanneer u klaar bent met het toevoegen van Tenant-Id's, selecteert u **concept opslaan** en selecteert u in de linkerbovenhoek van het tabblad **plan overzicht** om terug te keren naar het tabblad Overzicht van het **plan** .
1. Als u een ander abonnement voor deze aanbieding wilt maken, selecteert u **+ nieuw plan maken** boven aan het tabblad Overzicht van het **plan** . Herhaal vervolgens de stappen in de sectie [een plan maken](#create-a-plan) . Als dat niet het geval is, gaat u naar [uw plannen weer geven](#view-your-plans).

### <a name="use-a-csv-file-for-a-private-plan"></a>Gebruik een. CSV-bestand voor een privé-abonnement

1. Schakel onder **zicht baarheid van plan** het selectie vakje **Dit is een privé plan** in.
2. Selecteer de koppeling **doel groep exporteren (CSV)** .
3. Open de. CSV-bestand en voeg de Azure-Id's toe die u toegang wilt verlenen tot de persoonlijke aanbieding aan de **id-** kolom.
4. Voer desgewenst een beschrijving in voor elke doel groep in de kolom **Beschrijving** .
5. Voeg ' TenantID ' toe aan de kolom **type** voor elke rij met een Azure-id.
6. Sla het op. CSV-bestand.
7. Selecteer op het tabblad **prijzen en beschik baarheid** onder **plan zicht baarheid** de koppeling **doel groep importeren (CSV)** .
8. In het dialoog venster dat wordt weer gegeven, selecteert u **Ja**.
9. Selecteer de. CSV-bestand en selecteer **openen**.
10. Selecteer **concept opslaan** en selecteer vervolgens in de linkerbovenhoek van het tabblad **plan overzicht** om terug te keren naar het tabblad **overzicht** van het plan.
11. Als u een ander abonnement voor deze aanbieding wilt maken, selecteert u **+ nieuw plan maken** bovenaan het tabblad Overzicht van het **plan** . Herhaal vervolgens de stappen in de sectie [een plan maken](#create-a-plan) . Als u klaar bent met het maken van plannen, gaat u naar de volgende sectie: **uw plannen weer geven**.

## <a name="view-your-plans"></a>Uw plannen weer geven

Nadat u een of meer abonnementen hebt gemaakt, ziet u de naam van het abonnement, de plan-ID, het prijs model, de beschik baarheid (openbaar of privé), de huidige publicatie status en eventuele beschik bare acties op het tabblad Overzicht van het **plan** .

De acties die beschikbaar zijn in de kolom **actie** van het tabblad **plan overzicht** variëren afhankelijk van de status van uw abonnement en kunnen het volgende omvatten:

- Als de plan status **concept** is, wordt in de kolom **actie** **concept verwijderen** weer te zeggen.
- Als de status van het abonnement **Live** is, is de koppeling in de kolom **actie** **stoppen** of wordt een **persoonlijke doel groep gesynchroniseerd**. Met de koppeling **persoonlijke doel groep synchroniseren** worden alleen de wijzigingen in uw persoonlijke doel groepen gepubliceerd, zonder dat u andere updates publiceert die u mogelijk hebt gemaakt in de aanbieding.

## <a name="before-you-publish-your-offer"></a>Voordat u uw aanbieding publiceert

Als u dit nog niet hebt gedaan, maakt u een aanbieding voor ontwikkelen en testen (DEV) om uw aanbieding te testen voordat u uw productie aanbieding Live publiceert. Zie [een ontwikkel-en test aanbieding maken](create-saas-dev-test-offer.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Leer [hoe u uw SaaS-aanbieding kunt verkopen](create-new-saas-offer-marketing.md) **met micro soft** en door te verkopen **via csp's** -Program ma's.
- [Het testen en publiceren van een SaaS-aanbieding naar de commerciële Marketplace](test-publish-saas-offer.md).
