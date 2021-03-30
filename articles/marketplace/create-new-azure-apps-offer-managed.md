---
title: Een beheerd toepassings abonnement configureren
description: Meer informatie over het configureren van een beheerd toepassings abonnement voor uw Azure-toepassings aanbieding in Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a67a721b8d94112347282e1cd9be4d43f4ac85da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96744632"
---
# <a name="configure-a-managed-application-plan"></a>Een beheerd toepassings abonnement configureren

Dit artikel is alleen van toepassing op beheerde toepassings abonnementen voor een Azure-toepassings aanbieding. Als u een oplossings sjabloon plan wilt configureren, gaat u naar [een oplossings sjabloon plan configureren](create-new-azure-apps-offer-solution.md).

## <a name="define-markets-pricing-and-availability"></a>Markten, prijzen en beschik baarheid definiëren

Elk plan moet beschikbaar zijn op ten minste één markt. Op het tabblad **prijzen en beschik baarheid** kunt u de markten configureren waarop dit abonnement beschikbaar is, de prijs en of u het plan zichtbaar wilt maken voor iedereen of alleen voor specifieke klanten (ook wel een persoonlijk abonnement genoemd).

1. Onder **markten** selecteert u de koppeling **markt bewerkings** .
1. Selecteer in het dialoog venster dat wordt weer gegeven de markt locaties waar u uw plan beschikbaar wilt maken. U moet mini maal één en Maxi maal 141 markten selecteren.

    > [!NOTE]
    > Dit dialoog venster bevat een zoekvak en een optie voor het filteren van alleen de landen ' BTW geremitteerd ', waarbij micro soft namens u verkoop-en gebruiks belasting verkent.

1. Selecteer **Opslaan** om het dialoog venster te sluiten.

## <a name="define-pricing"></a>Prijzen definiëren

Geef in het vak **prijs** de prijs per maand voor dit abonnement op. Deze prijs is een aanvulling op de kosten voor Azure-infra structuur of op basis van gebruik die worden gemaakt door de resources die door deze oplossing worden geïmplementeerd.

Naast de prijs per maand kunt u ook prijzen voor het verbruik van niet-standaard eenheden instellen met behulp van [facturering via een Data limiet](partner-center-portal/azure-app-metered-billing.md). U kunt de prijs per maand instellen op nul en kosten in rekening gebracht als u wilt.

Prijzen worden ingesteld in USD (USD = Verenigde Staten dollar) worden omgezet in de lokale valuta van alle geselecteerde markten met de huidige wissel koersen wanneer deze worden opgeslagen. Valideer deze prijzen vóór de publicatie door het prijs werk blad te exporteren en de prijs op elke markt te bekijken. Als u aangepaste prijzen wilt instellen op een afzonderlijke markt, wijzigt en importeert u de prijs informatie in het werk blad.

### <a name="add-a-custom-meter-dimension-optional"></a>Een aangepaste meter dimensie toevoegen (optioneel)

1. Selecteer onder **service dimensies Marketplace-meting** de koppeling **een aangepaste meter dimensie toevoegen (Maxi maal 18)** .
1. Voer in het vak **id** de verwijzing onveranderbare id in terwijl u gebruiks gebeurtenissen verzendt.
1. Voer in het vak **weergave naam** de weergave naam in die is gekoppeld aan de dimensie. Bijvoorbeeld ' verzonden tekst berichten '.
1. Voer in het vak **maat eenheid** de beschrijving van de facturerings eenheid in. Bijvoorbeeld ' per SMS-bericht ' of ' per 100 e-mail berichten '.
1. Voer in het vak **prijs per eenheid in USD** de prijs voor één eenheid van de dimensie in.
1. Voer in het vak **basis bedrag per** maand de hoeveelheid (als een geheel getal) in van de dimensie die wordt opgenomen in de maandelijkse kosten. Als u een onbeperkt aantal wilt instellen, schakelt u het selectie vakje in.
1. Herhaal de stappen 1 tot en met 7 om een andere aangepaste meter dimensie toe te voegen.

### <a name="set-custom-prices-optional"></a>Aangepaste prijzen instellen (optioneel)

Prijzen die zijn ingesteld in USD (USD = Verenigde Staten dollar) worden omgezet in de lokale valuta van alle geselecteerde markten met de huidige wissel koersen wanneer deze worden opgeslagen. Valideer deze prijzen vóór de publicatie door het prijs werk blad te exporteren en de prijs op elke markt te bekijken. Als u aangepaste prijzen wilt instellen op een afzonderlijke markt, wijzigt en importeert u de prijs informatie in het werk blad.

Controleer uw prijzen zorgvuldig voordat u deze publiceert. er zijn enkele beperkingen voor wat er kan worden gewijzigd nadat een plan is gepubliceerd.

> [!NOTE]
> Nadat een prijs voor een markt in uw abonnement is gepubliceerd, kunt u deze later niet meer wijzigen.

Als u aangepaste prijzen wilt instellen in een afzonderlijke markt, exporteert, wijzigt en importeert u vervolgens de prijs informatie in het werk blad. U bent verantwoordelijk voor het valideren van deze prijzen en de eigenaar van deze instellingen. Zie [aangepaste prijzen](plans-pricing.md#custom-prices)voor gedetailleerde informatie.

1. U moet uw prijs wijzigingen eerst opslaan om het exporteren van prijs gegevens in te scha kelen. Klik onder aan het tabblad **prijzen en beschik baarheid** op **concept opslaan**.
1. Selecteer onder **prijzen** de koppeling **prijs gegevens exporteren** .
1. Open het exportedPrice.xlsx-bestand in micro soft Excel.
1. Breng in het werk blad de gewenste wijzigingen aan in de prijs informatie en sla het bestand op.

   Mogelijk moet u bewerken in Excel inschakelen voordat u het bestand kunt bijwerken.

1. Selecteer op het tabblad **prijzen en beschik baarheid** onder **prijzen** de koppeling **prijs gegevens importeren** .
1. In het dialoog venster dat wordt weer gegeven, klikt u op **Ja**.
1. Selecteer het exportedPrice.xlsx bestand dat u hebt bijgewerkt en klik vervolgens op **openen**.

## <a name="choose-who-can-see-your-plan"></a>Kies wie uw abonnement kan zien

U kunt elk plan zodanig configureren dat het zichtbaar is voor iedereen of alleen voor een specifieke doel groep. U verleent toegang tot een persoonlijke doel groep met behulp van Azure-abonnement-Id's met de optie om een beschrijving op te nemen van elke abonnements-ID die u toewijst. U kunt Maxi maal 10 abonnements-Id's hand matig of Maxi maal 10.000 abonnement-Id's toevoegen met behulp van een. CSV-bestand. Azure-abonnements-Id's worden weer gegeven als GUID'S en letters moeten kleine letters zijn.

> [!NOTE]
> Als u een privé-abonnement publiceert, kunt u de zicht baarheid later wijzigen in openbaar. Wanneer u echter een openbaar abonnement publiceert, kunt u de zicht baarheid niet wijzigen in privé.

Voer een van de volgende handelingen uit onder **plan zicht baarheid**:

- Als u het plan openbaar wilt maken, selecteert u het **open bare** keuze rondje (ook wel een _keuze rondje_ genoemd).
- Als u het plan privé wilt maken, selecteert u het **persoonlijke** keuze rondje en voegt u de id's van het Azure-abonnement hand matig of met een CSV-bestand toe.

> [!NOTE]
> Een persoonlijke of beperkte doel groep wijkt af van de preview-doel groep die u hebt gedefinieerd op het tabblad **Preview** . Een preview-doel groep heeft toegang tot uw aanbieding voordat deze live op Marketplace wordt gepubliceerd. Hoewel de keuze van de privé doelgroep alleen van toepassing is op een specifiek abonnement, kan de preview-doel groep alle plannen (privé of niet) voor validatie doeleinden bekijken.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Hand matig Azure-abonnements-Id's voor een privé-abonnement toevoegen

1. Selecteer onder **plan zicht baarheid** het keuze rondje **persoonlijk** .
1. Voer in het vak ID van het **Azure-abonnement** dat wordt weer gegeven, de Azure-abonnements-id in van de doel groep die u toegang wilt verlenen tot dit privé-abonnement. Er is mini maal één abonnements-ID vereist.
1. Beschrijving Voer een beschrijving van deze doel groep in het vak **Beschrijving** in.
1. Als u nog een abonnements-ID wilt toevoegen, selecteert u de koppeling **id toevoegen (Maxi maal 10)** en herhaalt u stap 2 en 3.

### <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Gebruik een. CSV-bestand voor het toevoegen van Azure-abonnement-Id's voor een privé abonnement

1. Selecteer onder **plan zicht baarheid** het keuze rondje **persoonlijk** .
1. Selecteer de koppeling **doel groep exporteren (CSV)** .
1. Open de. CSV-bestand en voeg de Id's van het Azure-abonnement toe die u toegang wilt verlenen tot de persoonlijke aanbieding aan de **id-** kolom.
1. Voer desgewenst een beschrijving in voor elke doel groep in de kolom **Beschrijving** .
1. Voeg ' SubscriptionId ' toe aan de kolom **type** voor elke rij met een abonnements-id.
1. Sla het op. CSV-bestand.
1. Selecteer op het tabblad **Beschik baarheid** onder **plan zicht baarheid** de koppeling **doel groep importeren (CSV)** .
1. In het dialoog venster dat wordt weer gegeven, selecteert u **Ja**.
1. Selecteer de. CSV-bestand en selecteer **openen**. Er wordt een bericht weer gegeven waarin wordt aangegeven dat de. Het CSV-bestand is geïmporteerd.

## <a name="define-the-technical-configuration"></a>De technische configuratie definiëren

Op het tabblad **technische configuratie** uploadt u het implementatie pakket waarmee klanten uw plan kunnen implementeren en een versie nummer voor het pakket kunnen opgeven. U kunt ook andere technische informatie opgeven.

> [!NOTE]
> Dit tabblad wordt niet weer gegeven als u ervoor hebt gekozen om pakketten van een ander abonnement opnieuw te gebruiken op het tabblad **installatie plannen** . Als dit het geval is, gaat u naar [uw plannen weer geven](#view-your-plans).

### <a name="assign-a-version-number-for-the-package"></a>Een versie nummer voor het pakket toewijzen

Geef in het vak **versie** de huidige versie van de technische configuratie op. Verhoog deze versie telkens wanneer u een wijziging op deze pagina publiceert. Het versie nummer moet de volgende indeling hebben: integer. integer. integer. Bijvoorbeeld `1.0.2`.

### <a name="upload-a-package-file"></a>Een pakket bestand uploaden

Sleep het pakket bestand onder **pakket bestand (. zip)** naar het grijze vak of selecteer de koppeling **zoeken naar uw bestand (en)** .

> [!NOTE]
> Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de door `https://upload.xboxlive.com` Partner Center gebruikte service niet blokkeert.

#### <a name="previously-published-packages"></a>Eerder gepubliceerde pakketten

Op het tabblad **eerder gepubliceerde pakketten** kunt u alle gepubliceerde versies van uw technische configuratie weer geven.

### <a name="enable-just-in-time-jit-access-optional"></a>Just-in-time-toegang inschakelen (optioneel)

Schakel het selectie vakje **just-in-time-toegang inschakelen** in om JIT-toegang in te scha kelen voor dit abonnement. Als u wilt dat gebruikers van uw beheerde toepassing uw account permanente toegang geven, laat u deze optie uitgeschakeld. Zie just [-in-time-toegang (JIT)](plan-azure-app-managed-app.md#just-in-time-jit-access)voor meer informatie over deze optie.

### <a name="select-a-deployment-mode"></a>Een implementatie modus selecteren

Selecteer de **volledige** of **incrementele** implementatie modus.

- In de **volledige** modus leidt een herimplementatie van de toepassing door de klant tot het verwijderen van resources in de beheerde resource groep als de resources niet zijn gedefinieerd in de [mainTemplate.jsop](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template).
- In de **incrementele** modus blijven bestaande resources ongewijzigd wanneer de toepassing opnieuw wordt geïmplementeerd.

Zie [Azure Resource Manager-implementatie modi](../azure-resource-manager/templates/deployment-modes.md)voor meer informatie over de implementatie modi.

### <a name="provide-a-notification-endpoint-url"></a>Een URL voor een meldings eindpunt opgeven

Geef in het vak URL van het **meldings eindpunt** een HTTPS-webhook-eind punt op om meldingen te ontvangen over alle ruwe bewerkingen op exemplaren van beheerde toepassingen van deze plan versie.

### <a name="customize-allowed-customer-actions-optional"></a>Toegestane klant acties aanpassen (optioneel)

1. Om op te geven welke acties klanten kunnen uitvoeren op de beheerde resources naast de `*/read` acties die standaard beschikbaar zijn, schakelt u het selectie vakje **toegestane klant acties aanpassen** in.
1. Geef in de vakken die worden weer gegeven, de extra besturings acties en toegestane gegevens acties op die u wilt toestaan dat de klant kan worden uitgevoerd, gescheiden door punt komma's. Als u bijvoorbeeld wilt toestaan dat gebruikers virtuele machines opnieuw opstarten, voegt `Microsoft.Compute/virtualMachines/restart/action` u toe aan het vak **toegestane besturings acties** .

### <a name="choose-who-can-manage-the-application"></a>Kies wie de toepassing kan beheren

Geef aan wie beheer toegang moet hebben tot deze beheerde toepassing in elke geselecteerde Azure-regio: _wereld wijd Azure_ en _Azure Government Cloud_. U maakt gebruik van Azure AD-identiteiten om de gebruikers, groepen of toepassingen te identificeren die u wilt machtigen voor de beheerde resource groep. Zie [een door Azure beheerde toepassing plannen voor een Azure-toepassing aanbieding](plan-azure-application-offer.md)voor meer informatie.

Voer de volgende stappen uit voor wereld wijd Azure en Azure Government Cloud, indien van toepassing.

1. Voer in het vak **Azure Active Directory Tenant-id** de Azure AD-Tenant-id (ook wel bekend als Directory-id) in die de identiteiten bevat van de gebruikers, groepen of toepassingen waaraan u machtigingen wilt verlenen.
1. Geef in het vak **Principal-id** de Azure AD-object-id op van de gebruiker, groep of toepassing waaraan u machtigingen wilt verlenen voor de beheerde resource groep. Identificeer de gebruiker op basis van de principal-ID, die u kunt vinden op de [blade Azure Active Directory gebruikers](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) op de Azure Portal.
1. Selecteer in de lijst **roldefinitie** een ingebouwde Azure AD-rol. Met de rol die u selecteert, worden de machtigingen beschreven die de principal heeft op de resources in het abonnement van de klant.
1. Als u nog een autorisatie wilt toevoegen, selecteert u de koppeling **autorisatie toevoegen (max. 100)** en herhaalt u stap 1 tot en met 3.

### <a name="policy-settings-optional"></a>Beleids instellingen (optioneel)

U kunt Maxi maal vijf beleids regels configureren, en slechts één exemplaar van elke beleids optie. Voor sommige beleids regels zijn aanvullende para meters vereist.

1. Selecteer onder **beleids instellingen** de koppeling **+ beleid toevoegen (max. 5)** .
1. Voer in het vak **naam** de naam van de beleids toewijzing in (beperkt tot 50 tekens).
1. Selecteer in het vak **beleids** lijst het Azure-beleid dat wordt toegepast op resources die zijn gemaakt door de beheerde toepassing in het abonnement van de klant.
1. Geef in het vak **beleids parameters** de para meter op waarop het beleid voor controle en diagnostische instellingen moet worden toegepast.
1. Selecteer in de keuze lijst **beleids-SKU** het SKU-type beleid.

    > [!NOTE]
    > De _standaard beleids_ -SKU is vereist voor controle beleid.

## <a name="view-your-plans"></a>Uw plannen weer geven

- Selecteer **concept opslaan** en selecteer in de linkerbovenhoek van de pagina **plan overzicht** om terug te keren naar de **overzichts** pagina van het abonnement.

Nadat u een of meer plannen hebt gemaakt, ziet u de naam van het abonnement, de plan-ID, het plan type, de beschik baarheid (openbaar of privé), de huidige publicatie status en eventuele beschik bare acties op het tabblad Overzicht van het **plan** .

De acties die beschikbaar zijn in de kolom **actie** van het tabblad **plan overzicht** variëren afhankelijk van de status van uw abonnement en kunnen het volgende omvatten:

- Als de plan status **concept** is, wordt in de kolom **actie** **concept verwijderen** weer te zeggen.
- Als de status van het abonnement **Live** is, wordt de koppeling in de kolom **actie** **stoppen** of **privé publiek synchroniseren**. Met de koppeling **persoonlijke doel groep synchroniseren** worden alleen de wijzigingen in uw persoonlijke doel groepen gepubliceerd, zonder dat u andere updates publiceert die u mogelijk hebt gemaakt in de aanbieding.
- Als u een ander abonnement voor deze aanbieding wilt maken, selecteert u **+ nieuw plan maken** bovenaan het tabblad Overzicht van het **plan** . Herhaal de stappen in de [procedure voor het maken van plannen voor uw Azure-toepassings aanbieding](create-new-azure-apps-offer-plans.md). Als u klaar bent met het maken van plannen, gaat u naar de volgende sectie: volgende stappen.

## <a name="next-steps"></a>Volgende stappen

- [Uw Azure-toepassing-aanbieding testen en publiceren](create-new-azure-apps-offer-test-publish.md).
- Meer informatie [over het verkopen van uw Azure-toepassing-aanbod](create-new-azure-apps-offer-marketing.md) via het samen verkopen met micro soft en door verkopen via csp's-Program ma's.