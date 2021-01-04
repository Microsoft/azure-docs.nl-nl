---
title: Facturen voor Azure Enterprise-inschrijvingen
description: In dit artikel wordt uitgelegd hoe u uw Azure Enterprise-factuur kunt beheren en erop kunt reageren.
author: bandersmsft
ms.author: banders
ms.date: 12/09/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperf-fy21q1
ms.openlocfilehash: c64127f7ebbb416d9dfebb5e417cd27d51697665
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030719"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Facturen voor Azure Enterprise-inschrijvingen

In dit artikel wordt uitgelegd hoe u uw Azure EA (Azure Enterprise Agreement) kunt beheren en erop kunt reageren. Uw factuur is een afspiegeling van de kosten die u hebt gemaakt. Controleer de nauwkeurigheid van de factuur. Raak ook vertrouwd met andere taken die u nodig kunt hebben om uw factuur te beheren.

## <a name="view-usage-summary-and-download-reports"></a>Gebruiksoverzicht weergeven en rapporten downloaden

Ondernemingsbeheerders kunnen een overzicht van hun gebruiksgegevens, gebruikte Azure-vooruitbetaling en de kosten voor aanvullend gebruik bekijken in de Azure Enterprise-portal. De kosten worden weergegeven op overzichtsniveau voor alle accounts en abonnementen.

Als u het gedetailleerde gebruik voor specifieke accounts wilt bekijken, downloadt u het gebruiksgegevensrapport:

1. Meld u aan bij de Azure Enterprise-portal.
1. Selecteer **Rapporten**.
1. Selecteer het tabblad **Gebruiksgegevens downloaden**.
1. Selecteer in de lijst met rapporten de optie **Downloaden** voor het maandelijkse rapport dat u wilt downloaden.

   > [!NOTE]
   > Het gebruiksgegevensrapport bevat geen toepasselijke belastingen.
   >
   > Het duurt maximaal acht uur voordat gebruik wordt weergegeven in het rapport.

U kunt als volgt rapporten en grafieken met het gebruiksoverzicht weergeven:

1. Meld u aan bij de Azure Enterprise-portal.
1. Selecteer een vooruitbetalingsperiode.
   Als u de periode voor **Gebruiksoverzicht** wilt wijzigen, schakelt u rechtsboven van **M** (Maandelijks) naar **A** (Aangepast). Geef vervolgens de aangepaste begin- en einddatums op.  
   ![Gebruiksoverzicht maken en weergeven en rapporten downloaden in aangepaste weergave](./media/ea-portal-enrollment-invoices/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Selecteer een periode of maand in de grafiek om aanvullende gegevens weer te geven.
   - De grafiek toont het MoM-gebruik (maand over maand), uitgesplitst in ingezet voor gebruik, te veel in rekening gebrachte service, afzonderlijke gefactureerde kosten en Azure Marketplace-kosten.
   - U kunt voor de geselecteerde maand gebruikmaken van de velden onder de grafiek om te filteren op afdelingen, accounts en abonnementen.
   - U kunt schakelen tussen **Kosten per services** en **Kosten per hiërarchie**.
   - U kunt gegevens over **Azure-services**, **afzonderlijk gefactureerde kosten** en **Azure Marketplace** bekijken door de betreffende gedeelten uit te breiden.

Bekijk deze video voor meer informatie over hoe u gebruik kunt weergeven:

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>CSV-rapporten downloaden

Ondernemingsbeheerders gebruiken de pagina Maandelijks rapport downloaden om de volgende rapporten te downloaden als CSV-bestanden:

- Saldo en kosten
- Gebruiksgegevens
- Kosten voor Azure Marketplace
- Prijzenoverzicht

Rapporten downloaden:

1. Selecteer in de Azure Enterprise-portal **Rapporten**.
2. Selecteer bovenaan de pagina **Gebruiksgegevens downloaden**.
3. Selecteer naast het maandrapport de optie **Downloaden**.

   > [!NOTE]
   > Het kan na de werkelijke gebruiksdatum nog maximaal 72 uur duren voordat het gebruik wordt weergegeven in de rapporten.
   >
   > Gebruikers die CSV-bestanden met Safari downloaden naar Excel, ervaren mogelijk indelingsfouten. Open het bestand met een teksteditor om fouten te voorkomen.

![Voorbeeld met de pagina Gebruiksgegevens downloaden](./media/ea-portal-enrollment-invoices/create-ea-download-csv-reports.png)

Bekijk deze video waarin wordt uitgelegd hoe u gebruiksgegevens downloadt:

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Geavanceerde rapporten downloaden

Gebruik de optie Geavanceerde rapporten downloaden om rapporten te downloaden voor specifieke perioden of accounts. Het uitvoerbestand heeft de indeling CSV, waardoor het geschikt is voor grote recordsets.

1. Selecteer in de Azure Enterprise-portal **Geavanceerde rapporten downloaden**.
1. Selecteer de relevante periode en accounts.
1. Selecteer **Gebruiksgegevens aanvragen**.
1. Selecteer de knop **Vernieuwen** totdat de rapportstatus wordt bijgewerkt naar **Downloaden**.
1. Download het rapport.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Gebruiksrapporten en factureringsgegevens downloaden voor een eerdere inschrijving

U kunt gebruiksrapporten en factureringsgegevens voor een eerdere inschrijving downloaden nadat de inschrijving is overgedragen. Historische rapportage is beschikbaar in de Azure Enterprise-portal en kostenbeheer.

Inactieve inschrijvingen worden niet weergegeven in de Azure Enterprise-portal. Verwijder het vinkje uit het vak **Actief** om inactieve overgedragen inschrijvingen weer te geven.  

![Vinkje uit het vak Actief verwijderen zodat de gebruiker inactieve inschrijvingen kan zien](./media/ea-portal-enrollment-invoices/unchecked-active-box.png)

## <a name="change-a-po-number-for-an-upcoming-overage-invoice"></a>Een inkoopordernummer wijzigen voor een aanstaande overschrijdingsfactuur

In de Azure Enterprise-portal wordt automatisch een standaardnummer voor een inkooporder (PO) gegenereerd, tenzij de ondernemingsbeheerder er een instelt vóór de factuurdatum. Een ondernemingsbeheerder kan het inkoopordernummer bijwerken tot zeven dagen na ontvangst van een automatische factuurmelding per e-mail.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Het inkoopordernummer van de Azure-services bijwerken:

1. Selecteer in de Azure Enterprise-portal **Rapport** > **Gebruiksoverzicht**.
1. Selecteer **Inkoopordernummers bewerken** in de rechterbovenhoek.
1. Selecteer het keuzerondje **Azure-services**.
1. Selecteer een **Factuurperiode** uit de datumbereiken in het drop-down menu.

   U kunt een inkoopordernummer nog gedurende 7 dagen nadat u een factuurmelding hebt ontvangen bewerken, mits dit is vóórdat u de factuur hebt betaald.
1. Voer een nieuw inkoopordernummer in het veld **Inkoopordernummer** in.
1. Selecteer **Opslaan** om de wijziging te verzenden.

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>Het inkoopordernummer voor Azure Marketplace bijwerken:

1. Selecteer in de Azure Enterprise-portal **Rapport** > **Gebruiksoverzicht**.
1. Selecteer **Inkoopordernummers bewerken** in de rechterbovenhoek.
1. Selecteer het keuzerondje **Marketplace**.
1. Selecteer een **Factuurperiode** uit de datumbereiken in het drop-down menu.

   U kunt een inkoopordernummer nog gedurende 7 dagen nadat u een factuurmelding hebt ontvangen bewerken, mits dit is vóórdat u de factuur hebt betaald.
1. Voer een nieuw inkoopordernummer in het veld **Inkoopordernummer** in.
1. Selecteer **Opslaan** om de wijziging te verzenden.

## <a name="azure-enterprise-billing-frequency"></a>Factureringsfrequentie van Azure Enterprise

Microsoft factureert jaarlijks, op de ingangsdatum van de inschrijving, voor alle vooruitbetaalde aankopen van de Microsoft Azure-services. Voor alle gebruik dat de vooruitbetaalde bedragen overschrijdt, brengt Microsoft achteraf kosten in rekening.

- Vooruitbetalingskosten worden opgegeven op basis van een maandbedrag en worden vooraf per jaar in rekening gebracht.
- Overschrijdingskosten worden elke maand berekend en achteraf aan het einde van de factureringsperiode in rekening gebracht.

### <a name="billing-intervals"></a>Factureringsintervallen

Uw factureringsinterval is afhankelijk van hoe u vooruitbetaalde aankopen wilt doen. Uw jaarlijkse vooruitbetaling valt samen met een van deze twee datums:

- De jubileumdatum van de inschrijving
- De ingangsdatum van het Amendement-abonnement van één jaar.

De datum waarop u de overschrijdingsfactuur ontvangt, is afhankelijk van de startdatum en de opzet van de inschrijving:

- **Directe inschrijvingen met een startdatum vóór 1 mei 2018**:
  - Als u gebruikmaakt van een directe EA (Enterprise Agreement), hebt u een jaarlijkse factureringsperiode voor Azure-services, met uitzondering van Azure Marketplace-services. De factureringsperiode is gebaseerd op de jubileumdatum: de datum waarop de overeenkomst van kracht werd.
  - Als u de drempelwaarde van uw Azure-vooruitbetaling voor EA met 150% overschrijdt, wordt de factureringsperiode automatisch geconverteerd naar per kwartaal, gebaseerd op de jubileumdatum. U ontvangt ook een overschrijdingsfactuur voor de Azure-service.
  - Als u de drempelwaarde van uw Azure-vooruitbetaling niet met 150% overschrijdt, behoudt de inschrijving een jaarlijkse factureringsperiode. De overschrijdingsfactuur ontvangt u aan het einde van het vooruitbetaalde jaar.

- **Directe inschrijvingen met een startdatum na 1 mei 2018**:
  - Uw Azure-verbruik en de kosten worden afzonderlijk in rekening gebracht tijdens een maandelijkse factureringsperiode.
  - Kosten die niet onder uw Azure-vooruitbetaling vallen, vallen onder de overschrijdingsbetaling.  

- **Indirecte inschrijvingen die zijn gestart vóór 1 mei 2018**:

  Als u een indirecte EA-klant (Enterprise Agreement) bent met een begindatum van vóór 1 mei 2018, hebt u een factureringsperiode van een kwartaal. De kanaalpartner (CP) brengt kosten rechtstreeks bij u in rekening.  

- **Indirecte inschrijvingen met een startdatum na 1 mei 2018**:

  U hebt een maandelijkse factureringsperiode.  

### <a name="increase-your-azure-prepayment"></a>Uw Azure-vooruitbetaling verhogen

U kunt uw vooruitbetaling op elk gewenst moment verhogen. De resterende maanden in de vooruitbetalingsperiode van dat jaar worden bij u in rekening gebracht. Als u zich bijvoorbeeld aanmeldt voor een abonnement van één jaar en vervolgens de vooruitbetaling verhoogt in zes maanden, wordt u gefactureerd voor de resterende zes maanden van deze termijn. Uw vooruitbetaalde hoeveelheden worden vervolgens bijgewerkt voor de laatste zes maanden van de vooruitbetaalde termijn. Deze nieuwe hoeveelheden worden gebruikt om eventuele overschrijdingskosten te bepalen.

### <a name="overage"></a>Overschrijding

Voor overschrijding wordt u gefactureerd voor het gebruik of de reserveringen die gedurende de factureringsperiode de vooruitbetaling hebben overschreden. Als u wilt zien hoe de overschrijdingsaantallen voor afzonderlijke items zijn berekend, raadpleegt u het rapport met het gebruiksoverzicht of neemt u contact op met uw kanaalpartner.

Voor elk item op de factuur ziet u het volgende:

- **Berekend bedrag**: de totale kosten
- **Vooruitbetaald gebruik**: het vooruitbetaalde bedrag dat wordt gebruikt om de kosten te dekken
- **Nettobedrag**: de kosten die de vooruitbetaling overschrijden

Toepasselijke belastingen worden alleen berekend over het nettobedrag dat de vooruitbetaling overschrijdt.

Facturering van overschrijding is geautomatiseerd. De timing van meldingen en facturen is afhankelijk van de einddatum van uw factuurperiode.

- Een overschrijdingsmelding wordt doorgaans zeven dagen na de einddatum van uw facturering verzonden.
- Overschrijdingsfacturen worden zeven tot negen dagen na de melding verzonden.
- U kunt de kosten bekijken en in het systeem gegenereerde inkoopordernummers nog 7 dagen bijwerken, tussen de overschrijdingsmelding en de facturering.

### <a name="azure-marketplace"></a>Azure Marketplace

Vanaf de factureringsperiode van april 2019 ontvangen klanten één Azure-factuur waarin alle Azure- en Azure Marketplace-kosten worden gecombineerd, in plaats van twee afzonderlijke facturen. Deze wijziging is niet van toepassing op klanten in Australië, Japan of Singapore.

Tijdens de overgang naar een gecombineerde factuur ontvangt u een gedeeltelijke Azure Marketplace-factuur. In deze definitieve afzonderlijke factuur worden de Azure Marketplace-kosten weergegeven van vóór de datum van de factureringsupdate. Azure Marketplace-kosten die na deze datum zijn gemaakt, worden weergegeven op uw Azure-factuur. Na de overgangsperiode worden alle kosten voor Azure en Azure Marketplace weergegeven op de gecombineerde factuur.  

### <a name="adjust-billing-frequency"></a>De factureringsfrequentie aanpassen

De factureringsfrequentie van een klant is per jaar, per kwartaal of per maand. De factureringscyclus wordt bepaald wanneer een klant de overeenkomst ondertekent. Maandelijkse facturering is het kleinste factureringsinterval.

- **Goedkeuring** van een ondernemingsbeheerder is vereist om een factureringsperiode van per jaar te wijzigen in per kwartaal voor directe inschrijvingen. Er is goedkeuring van een partnerbeheerder nodig voor indirecte inschrijvingen. De wijziging gaat in aan het einde van het huidige factureringskwartaal.
- **Een Amendement** van de overeenkomst is vereist, als u een factureringsperiode van per jaar of per kwartaal wilt wijzigen in per maand.  Voor elke wijziging in de bestaande factureringsperiode van de inschrijving is goedkeuring vereist van een beheerder of van uw factuurcontact.
- **Verzend** uw goedkeuring naar de [Azure Enterprise-ondersteuningsportal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Selecteer de categorie van het probleem: **Facturering en facturen**.

De wijziging gaat in aan het einde van het huidige factureringskwartaal.

Als er een Amendement M503 is ondertekend, kunt u voor alle overeenkomsten elke frequentie wijzigen in maandelijkse facturering. 

### <a name="request-an-invoice-copy"></a>Een kopiefactuur aanvragen

Als u een kopie van uw factuur wilt aanvragen, neemt u contact op met uw partner.

## <a name="credits-and-adjustments"></a>Tegoeden en aanpassingen

U kunt alle tegoeden of aanpassingen die zijn toegepast op uw inschrijving, bekijken in de sectie **Rapporten** van [de Azure Enterprise-portal](https://ea.azure.com).

Tegoeden bekijken:

1. Selecteer in [de Azure Enterprise-portal](https://ea.azure.com) de sectie **Rapporten**.
1. Selecteer **Gebruiksoverzicht**.
1. Wijzig **M** in **C** in de rechterbovenhoek.
1. Vouw het aanpassingsveld in de vooruitbetalingstabel voor Azure-services uit.
1. U ziet tegoeden die zijn toegepast op uw inschrijving, en een korte uitleg. Bijvoorbeeld: Tegoed van Service Level Agreement.

## <a name="pay-your-overage-with-your-azure-prepayment"></a>Betaal uw overschrijding met uw Azure-vooruitbetaling

Als u uw Azure-vooruitbetaling wilt toepassen op overschrijdingen, moet u aan een van de volgende criteria voldoen:

- U hebt overschrijdingskosten die nog niet zijn betaald, en die binnen één jaar na de einddatum van de gefactureerde service vallen.
- Uw beschikbare bedrag van de Azure-vooruitbetaling dekt de volledige gefactureerde kosten, inclusief alle eerdere niet-betaalde Azure-facturen.
- De te voltooien factureringstermijn moet volledig zijn afgesloten. De facturering wordt volledig afgesloten na de vijfde dag van elke maand.
- De factureringsperiode die u wilt verrekenen, moet volledig zijn afgesloten.
- Uw APD-korting (Azure Prepayment Discount) is gebaseerd op de werkelijke nieuwe vooruitbetaling min eventuele fondsen die zijn gepland voor het vorige verbruik. Deze vereiste geldt alleen voor gemaakte overschrijdingskosten. Het is alleen geldig voor services die Azure-vooruitbetaling verbruiken, en is dus niet van toepassing op Azure Marketplace-kosten. Azure Marketplace-kosten worden afzonderlijk in rekening gebracht.

Als u een overschrijdingsverrekening wil voltooien, kunnen u of het accountteam een ondersteuningsaanvraag openen. Een per e-mail verzonden goedkeuring van de ondernemingsbeheerder of factuurcontact is vereist.

## <a name="move-charges-to-another-enrollment"></a>Kosten naar een andere enrollment verplaatsen

Gebruiksgegevens worden alleen verplaatst wanneer de tijd van een overdracht wordt teruggezet. Er zijn twee opties om gebruiksgegevens van de ene enrollment te verplaatsen naar een andere:

- Accountoverdracht van de ene enrollment naar een andere enrollment
- Inschrijvingsoverdracht van de ene inschrijving naar de andere

Voor beide opties moet u voor ondersteuning een [ondersteuningsaanvraag](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) indienen bij het EA-ondersteuningsteam. 

## <a name="enterprise-agreement-usage-calculations"></a>Berekeningen voor Enterprise Agreement-gebruik

Raadpleeg [Azure-services](https://azure.microsoft.com/services/) en [Azure-prijzen](https://azure.microsoft.com/pricing/) voor algemene informatie over de prijzen, maateenheden, veelgestelde vragen en richtlijnen voor gebruiksrapportage voor elke afzonderlijke service. In de volgende secties vindt u meer informatie over EA-berekeningen.

### <a name="enterprise-agreement-units-of-measure"></a>Maateenheden voor Enterprise Agreement

De maateenheden voor Enterprise Agreement zijn vaak anders dan in onze andere programma's, zoals MOSA (Microsoft Online Subscription Overeenkomst). Dit verschil betekent dat voor een aantal services de maateenheid wordt samengevoegd om de standaardprijzen te leveren. De maateenheid die wordt weergegeven in de weergave Gebruiksoverzicht van de Azure Enterprise-portal, is altijd de Enterprise-meting. Het Excel-bestand [Beschrijvende servicenamen](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) biedt een volledige lijst met huidige maateenheden en conversies voor elke service.

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Conversie tussen het gebruiksgegevensrapport en de pagina gebruiksoverzicht

In het gebruiksgegevensrapport voor downloaden kunt u het gebruik van onbewerkte resources bekijken tot maximaal zes decimalen. De gebruiksgegevens die zichtbaar zijn in de Azure Enterprise-portal, worden echter afgerond op vier decimalen voor vooruitbetalingseenheden, en afgekapt tot nul decimalen voor overschrijdingseenheden. Onbewerkte gebruiksgegevens worden eerst afgerond op vier cijfers, vóór de conversie naar eenheden die worden gebruikt in de Azure Enterprise-portal. Vervolgens worden de geconverteerde ondernemingseenheden opnieuw afgerond op vier cijfers. U kunt de werkelijke verbruikte uren vóór de conversie alleen zien in het gebruiksrapport voor downloaden, en niet in de Azure Enterprise-portal.

Bijvoorbeeld: Stel, er zijn 694,533404 werkelijke SQL Server-uren gerapporteerd in het gebruiksgegevensrapport. Deze eenheden worden geconverteerd naar 6,94533404 van 100 rekenuren, en vervolgens afgerond op 6,9453 en weergegeven in de Azure Enterprise-portal.

- De weergegeven eenheden worden vermenigvuldigd met de prijs voor de vooruitbetalingseenheid, en het resultaat wordt afgekapt tot twee decimalen. Voor Japanse yen (JPY) en Koreaanse won (KRW) wordt het berekende bedrag afgerond op nul decimalen.
- Voor overschrijding worden factureerbare eenheden afgekapt tot zes cijfers en vervolgens vermenigvuldigd met de eenheidsprijs van overschrijding, om het berekende bedrag voor de factuur te bepalen.
- Voor het factureren van een provider voor beheerde service (MSP), wordt al het gebruik dat is gekoppeld aan een afdeling die is gemarkeerd als MSP, afgekapt tot nul decimalen na de conversie naar de EA-eenheid van de meting. Als gevolg hiervan is de som van dit gebruik mogelijk lager dan de som van alle gebruik dat is vastgelegd in de Azure Enterprise-portal. Dit is afhankelijk van het feit of de MSP binnen het saldo voor hun Azure-vooruitbetaling valt, of deel uitmaakt van de overschrijding.

### <a name="graduated-pricing"></a>Gestaffelde prijs

De Enterprise Agreement-prijzen bevatten momenteel geen gestaffelde prijzen (waarbij de prijzen per eenheid afnemen naarmate het gebruik toeneemt). Wanneer u van een MOSA-programma met gestaffelde prijzen overstapt op een Enterprise Agreement, worden uw prijzen ingesteld evenredig met de basislaag van de service, min alle eventuele toepasselijke aanpassingen voor Enterprise Agreement-kortingen.

### <a name="partial-hour-billing"></a>Gedeeltelijke facturering per uur

Al het gefactureerde gebruik is gebaseerd op minuten die worden geconverteerd naar gedeeltelijke uren, en niet op toenamen van hele uren. De vermelde Enterprise-uurtarieven worden toegepast op het totale aantal uren, plus gedeeltelijke uren.

### <a name="average-daily-consumption"></a>Gemiddeld dagelijks verbruik

Sommige services zijn maandelijks geprijsd, terwijl het gebruik dagelijks wordt gerapporteerd. In deze gevallen wordt het gebruik eenmaal per dag geëvalueerd, gedeeld door 31, en opgeteld over het aantal dagen in de betreffende factureringsmaand. De tarieven zijn dus nooit hoger dan verwacht voor een maand en enigszins lager voor maanden met minder dan 31 dagen.

### <a name="compute-hours-conversion"></a>Omzetten van rekenuren

Vóór 28 januari 2016 werd elk gebruik voor virtuele machines van het type A0, A2, A3 en A4, en cloudservices van de categorie Standard en Basic, verzonden als berekende minuten van virtuele A1-machines. A0-VM's werden geteld als fracties van A1-VM-minuten, terwijl A2’s, A3’ s en A4’s werden geconverteerd naar meerdere. Omdat dit beleid voor verwarring heeft gezorgd bij klanten, brengen we een wijziging aan en is gebruik per minuut ingesteld voor speciale A0-, A2-, A3- en A4-meters.

De nieuwe meting is tussen 27 januari en 28 januari 2016 van kracht geworden. In het gedownloade CSV-bestand waarin het gebruik tijdens deze overgangsperiode wordt weergegeven, ziet u:

- Gebruik verzonden op de A1-meter
- Gebruik verzonden op de nieuwe specifieke meter die overeenkomt met de grootte van uw implementatie.

| **Implementatiegrootte** | **Gebruik verzonden als meervoud van A1 tot en met 26 januari 2016** | **Gebruik verzonden voor speciale meter met ingang van 27 januari 2016** |
| --- | --- | --- |
| A0 | 0,25 van een uur A1 | 1 van een uur A0 |
| A2 | 2 van een uur A1 | 1 van een uur A2 |
| A3 | 4 van een uur A1 | 1 van een uur A3 |
| A4 | 8 van een uur A1 | 1 van een uur A4 |

### <a name="regions"></a>Regio's

Voor de services waarbij zones en regio's van invloed zijn op de prijsstelling bekijkt u de volgende tabel voor een kaart met geografische gebieden en regio's:

| Geo | Regio's voor de gegevensoverdracht | CDN-regio's |
| --- | --- | --- |
| Zone 1 | Europa - noord <br> Europa - west <br> US - west <br> US - oost <br> US - noord-centraal <br> US - zuid-centraal <br> US - oost 2 <br> US - centraal | Noord-Amerika <br> Europa |
| Zone 2 | Azië en Stille Oceaan - oost <br> Azië en Stille Oceaan - zuidoost <br> Japan - oost <br> Japan - west <br> Australië - oost <br> Australië - zuidoost | Azië en Stille Oceaan <br> Japan <br> Latijns-Amerika <br> Midden-Oosten / Afrika <br> Australië - oost <br> Australië - zuidoost |
| Zone 3 | Brazilië - zuid |   |

Er worden geen kosten in rekening gebracht voor uitgaande gegevens tussen services die zijn gehuisvest binnen hetzelfde datacentrum. Bijvoorbeeld, Microsoft 365 en Azure.

### <a name="azure-prepayment-and-unbilled-usage"></a>Azure-vooruitbetaling en niet-gefactureerd gebruik

Azure-vooruitbetaling is een bedrag dat vooraf wordt betaald voor Azure-services. De Azure-vooruitbetaling wordt verbruikt als er services worden gebruikt. Eigen Azure-services worden gefactureerd op basis van de Azure-vooruitbetaling. Sommige kosten worden echter afzonderlijk gefactureerd, en Azure Marketplace-services verbruiken geen Azure-vooruitbetaling.

### <a name="charges-billed-separately"></a>Afzonderlijk gefactureerde kosten

Voor sommige producten en services die worden meegeleverd met externe bronnen, wordt de Azure-vooruitbetaling niet gebruikt. Deze items worden in plaats hiervan afzonderlijk gefactureerd als onderdeel van de overschrijdingsfactuur van de standaardfactureringsperiode.

We hebben alle Azure- en Azure Marketplace-kosten gecombineerd in één factuur die overeenkomt met de factuurperiode van de inschrijving. De gecombineerde factuur is niet van toepassing op klanten in Australië, Japan of Singapore.

Op de gecombineerde factuur wordt eerst het Azure-gebruik weergegeven, gevolgd door eventuele Azure Marketplace-kosten. Klanten in Australië, Japan of Singapore zien de Azure Marketplace-kosten op een aparte factuur.

Als er aan het einde van de standaardfactureringsperiode geen overschrijdingsgebruik is, worden kosten afzonderlijk gefactureerd. Dit proces is van toepassing op klanten in Australië, Japan en Singapore.

De volgende services worden afzonderlijk gefactureerd:

- Canonical
- Citrix XenApp Essentials
- Geregistreerde gebruiker Citrix XenDesktop
- OpenLogic
- Geregistreerde gebruiker van Remote Access Rights XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (maandelijks)
- Visual Studio Enterprise (jaarlijks)
- Visual Studio Professional (maandelijks)
- Visual Studio Professional (jaarlijks)

## <a name="what-to-expect-after-change-of-channel-partner"></a>Wat u kunt verwachten na een wijziging van kanaalpartner

Als de wijziging van kanaalpartner (COCP) midden in de maand plaatsvindt, ontvangt een klant een factuur voor gebruik onder de vorige gekoppelde partner en een tweede factuur voor het gebruik onder de nieuwe partner.

De facturen worden opgemaakt in de maand die volgt op de beëindiging van de factureringsperiode. Bij maandelijkse facturering wordt de factuur van september voor beide partners in oktober opgemaakt. Bij een factureringsperiode van een kwartaal of een jaar ontvangt de klant een factuur voor de eerder gekoppelde partner voor het gebruik in de bijbehorende periode, en is de rest voor de nieuwe partner op basis van het factureringsinterval.

## <a name="next-steps"></a>Volgende stappen

- In de volgende Excel-bestanden ziet u details van Azure-services. Deze worden elke 6e en 20e dag van de maand bijgewerkt:

   | Titel | Beschrijving | Bestandsnaam |
   | --- | --- | --- |
   | [Beschrijvende servicenamen](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Hierin worden alle actieve services vermeld en omvat ook: <br>  <ul><li>servicecategorie</li>   <li>beschrijvende servicenamen</li>   <li>Vooruitbetalingsnaam en onderdeelnummer</li> <li>verbruiksnaam en onderdeelnummer</li>   <li>meeteenheid</li>   <li>conversiefactoren tussen gerapporteerd gebruik en weergegeven gebruik van de Enterprise-portal</li></ul> | Friendly\_Service\_Names.xlsx |
   | [Service Download Fields](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Dit werk blad bevat een lijst met alle mogelijke combinaties van de velden die aan de service zijn gerelateerd in het rapport voor het downloaden van het gebruik. | Service\_Download\_Fields.xlsx |

- Zie [Meer informatie over uw Azure Enterprise-factuur](../understand/review-enterprise-agreement-bill.md) voor informatie over uw factuur en kosten.
- Zie [Aan de slag met Azure Enterprise-portal](ea-portal-get-started.md) om te beginnen met het gebruik van Azure Enterprise-portal.
