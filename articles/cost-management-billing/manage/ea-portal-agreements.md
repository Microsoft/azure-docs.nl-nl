---
title: Azure EA-overeenkomsten en -aanpassingen
description: In dit wordt uitleg gegeven over hoe de Azure EA-overeenkomsten en -aanpassingen van invloed zijn op uw gebruik van Azure EA Portal.
author: bandersmsft
ms.author: banders
ms.date: 01/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: d03ae5062cc81b3e042ed3b714838e14929dbba2
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778216"
---
# <a name="azure-ea-agreements-and-amendments"></a>Azure EA-overeenkomsten en -aanpassingen

In het artikel wordt beschreven hoe Azure EA-overeenkomsten en -aanpassingen van invloed kunnen zijn op uw toegang, gebruik en betalingen voor de Azure-services.

## <a name="enrollment-provisioning-status"></a>De inrichtingsstatus voor inschrijving

De begindatum van een nieuwe Azure-vooruitbetaling (voorheen financiële toezegging) wordt gedefinieerd op basis van de datum waarop het regionale Operations Center de vooruitbetaling verwerkt. Omdat orders voor Azure-vooruitbetaling via Azure EA Portal worden verwerkt in de tijdzone UTC, kan er enige vertraging optreden als uw inkooporder voor Azure-vooruitbetaling in een andere regio is verwerkt. De begindatum van de dekking op de inkooporder staat gelijk aan de begindatum van de Azure-vooruitbetaling. De begindatum van de dekking is dezelfde datum als de datum waarop de Azure-vooruitbetaling wordt weergegeven in Azure EA Portal.

## <a name="support-for-enterprise-customers"></a>Ondersteuning voor Enterprise-klanten

 De Azure-[ondersteuningsplanaanbieding voor Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/) is voor sommige klanten beschikbaar.

## <a name="enrollment-status"></a>Status van inschrijving

Een inschrijving heeft één van de volgende statuswaarden. Elke waarde bepaalt hoe u een inschrijving kunt gebruiken en openen. De inschrijvingsstatus bepaalt in welke fase uw inschrijving zich bevindt. U krijgt een melding als de inschrijving moet worden geactiveerd voordat deze kan worden gebruikt. Of het geeft aan dat de initiële periode is verlopen en er kosten in rekening worden gebracht voor overschrijdend gebruik.

**In behandeling**: de inschrijvingsbeheerder moet zich aanmelden bij Azure EA Portal. Na het aanmelden krijgt de inschrijving de status **Actief**.

**Actief**: de inschrijving is toegankelijk en bruikbaar. U kunt accounts en abonnementen maken in Azure EA Portal. De inschrijving blijft actief tot de einddatum van de Enterprise Agreement.

**Onbeperkte verlengde termijn**: de status voor een onbeperkte verlengde termijn wordt toegekend nadat de einddatum van de Enterprise Agreement is bereikt. Voordat de einddatum van de Enterprise Agreement is bereikt voor de EA-inschrijving, moet de inschrijvingsbeheerder een beslissing nemen:

- De inschrijving vernieuwen door een extra Azure-vooruitbetaling toe te voegen
- De bestaande inschrijving overzetten naar een nieuwe inschrijving
- Migreren naar het Microsoft Online Subscription Program (MOSP)
- Het uitschakelen van alle services die aan de inschrijving zijn gekoppeld, bevestigen

**Verlopen**: de EA-inschrijving verloopt wanneer de einddatum van de Enterprise Agreement is bereikt. De EA-klant wordt uitgeschreven voor de verlengde termijn en alle services worden uitgeschakeld.

Vanaf 1 augustus 2019 worden de nieuwe uitschrijvingsformulieren niet geaccepteerd voor commerciële klanten van Azure. In plaats daarvan worden alle inschrijvingen voor onbepaalde tijd verlengd. Als u de Azure-services niet meer wilt gebruiken, zegt u uw abonnement via [Azure Portal](https://portal.azure.com) op. Uw partner kan ook een beëindigingsaanvraag indienen. Er zijn geen wijzigingen voor klanten met een overeenkomst van het overheidstype.

**Overgedragen**: de status Overgedragen wordt toegepast op inschrijvingen waarvan de bijbehorende accounts en services zijn overgedragen naar een nieuwe inschrijving. Inschrijvingen worden niet automatisch overgedragen als er tijdens het verlengen een nieuw inschrijvingsnummer wordt gegenereerd. Het voorafgaande inschrijvingsnummer moet worden opgenomen in de verlengingsaanvraag van de klant voor een automatische overdracht.

## <a name="partner-markup"></a>Prijsverhoging door partners

In Azure EA Portal kunt u door prijsverhogingen van partners beter over kosten rapporteren voor klanten. Azure EA Portal toont het gebruik en de prijzen die door partners voor hun klanten zijn geconfigureerd.

Met de prijsverhoging kunnen beheerders van partners een prijsverhogingspercentage toevoegen aan hun indirecte Enterprise Agreements. Het prijsverhogingspercentage is van toepassing op alle servicegegevens van Microsoft in Azure EA Portal, zoals metertarieven, Azure-vooruitbetaling en orders. Zodra de prijsverhoging wordt gepubliceerd door de partner, worden voor de klant de Azure-kosten weergegeven in Azure EA Portal. Voorbeelden: gebruiksoverzicht, prijslijsten en gedownloade gebruiksrapporten.

Vanaf september 2019 kunnen partners te allen tijde tijdens een termijn een prijsverhoging toepassen. Ze hoeven niet te wachten tot de volgende termijn om een prijsverhoging toe te passen.

Microsoft heeft voor geen enkel doeleinde toegang tot de opgegeven prijsverhoging en de bijbehorende prijzen, tenzij dit expliciet wordt geautoriseerd door de kanaalpartner.

### <a name="how-the-calculation-works"></a>Hoe werkt de berekening?

De LSP geeft één percentagegetal op in EA Portal.    Alle commerciële gegevens op de portal worden verhoogd met het percentage dat is opgegeven door de LSP. Voorbeeld:

- Klant ondertekent een EA met een Azure-vooruitbetaling van USD 100.000.
- Het metertarief voor service A is 10 USD/uur.
- De LSP stelt in EA Portal het verhogingspercentage in op 10%.
- Het volgende voorbeeld laat zien hoe de commerciële gegevens voor de klant worden weergegeven:
    - Financieel saldo: 110.000 USD.
    - Metertarief voor service A: 11 USD/uur.
    - Informatie over het gebruik/de hosting voor service A als deze gedurende 100 uur wordt gebruikt: 1100 USD.
    - Financieel saldo dat beschikbaar is voor de klant na kostenaftrek voor het gebruik van service A: 108.900 USD.

### <a name="when-to-use-a-markup"></a>Wanneer gebruikt u een prijsverhoging?

Gebruik de functie als u hetzelfde prijsverhogingspercentage instelt voor ALLE commerciële transacties in de EA. Dat wil zeggen, als de prijsverhoging van toepassing is op de gegevens voor de Azure-vooruitbetaling, de metertarieven, de ordergegevens, enzovoort.

Gebruik de prijsverhogingsfunctie niet als:
- U verschillende tarieven gebruikt voor de Azure-vooruitbetaling en de metertarieven.
- U verschillende tarieven gebruikt voor verschillende meters.

Als u verschillende tarieven voor verschillende meters gebruikt, is het raadzaam een aangepaste oplossing te ontwikkelen op basis van de API-sleutel, die door de klant kan worden verstrekt, om verbruiksgegevens op te halen en rapporten te verstrekken.

### <a name="other-important-information"></a>Andere belangrijke informatie

Deze functie dient om een schatting te maken van de Azure-kosten voor de eindklant. De LSP is verantwoordelijk voor alle financiële transacties met de klant die onder de EA vallen.

Controleer de commerciële gegevens (gegevens over het financiële saldo, de prijslijst, enzovoort) voordat u de verhoogde prijzen publiceert voor de eindklant.

### <a name="how-to-add-a-price-markup"></a>Een prijsverhoging toevoegen

**Eerste stap: de prijsverhoging toevoegen**

1. Klik in Enterprise Portal in het linkernavigatievenster op **Rapporten**.
1. Klik onder _Gebruiksoverzicht_ op de blauwe tekst **Prijsverhoging**.
1. Voer het prijsverhogingspercentage (tussen -100 en 100) in en klik op **Voorbeeld**.


**Tweede stap: controleren en valideren**

Controleer de prijsverhoging in het _Gebruiksoverzicht_ voor de vooruitbetalingsperiode in de klantweergave. De Microsoft-prijs wordt nog steeds weergegeven in de partnerweergave. De weergaven kunnen worden in- of uitgeschakeld met de schakeloptie voor de partnerprijsverhoging 'personen' in de rechterbovenhoek.

1. Controleer de prijzen in het prijzenoverzicht.
1. U kunt wijzigingen aanbrengen voordat u publiceert door **Bewerken** te selecteren op het tabblad _Gebruiksoverzicht bekijken > Klantweergave_. 
   
Zowel de serviceprijzen als de vooruitbetalingssaldi worden met dezelfde percentages verhoogd. Als u verschillende percentages gebruikt voor het financiële saldo en de metertarieven of verschillende percentages gebruikt voor verschillende services, kunt u deze functie niet gebruiken.

**Derde stap: publiceren**

Nadat de prijzen zijn gecontroleerd en gevalideerd, klikt u op **Publiceren**.
  
De prijzen met verhogingen zijn direct beschikbaar voor ondernemingsbeheerders nadat u deze hebt gepubliceerd. U kunt geen wijzigingen aanbrengen in de prijsverhoging. U moet de prijsverhoging uitschakelen en weer bij de eerste stap beginnen.

### <a name="which-enrollments-have-a-markup-enabled"></a>Voor welke inschrijvingen is een prijsverhoging ingeschakeld?

Als u wilt controleren of er voor een inschrijving een prijsverhoging is gepubliceerd, klikt u in het linkernavigatievenster op **Beheren** en klikt u vervolgens op het tabblad **Inschrijving**. Schakel het selectievakje in voor de inschrijving die u wilt controleren en bekijk de status van de prijsverhoging onder _Inschrijvingsgegevens_. Hier wordt de huidige status van de prijsverhogingsfunctie voor die EA weergegeven. Mogelijke statussen zijn: Uitgeschakeld, Preview-versie of Gepubliceerd.

### <a name="how-can-the-customer-download-usage-estimates"></a>Hoe kan de klant gebruiksramingen downloaden?

Zodra de partnerprijsverhoging is gepubliceerd, heeft de indirecte klant toegang tot de maandelijkse CSV-bestanden met het saldo en de kosten en de CSV-bestanden met gebruiksgegevens. De bestanden met gebruiksgegevens bevatten de resourcetarieven en de uitgebreide kosten.

### <a name="how-can-i-as-partner-apply-markup-to-existing-ea-customers-that-was-earlier-with-another-partner"></a>Hoe kan ik als partner een prijsverhoging toepassen op bestaande EA-klanten die eerder bij een andere partner zaten?
Partners kunnen de prijsverhogingsfunctie (in Azure EA) gebruiken nadat een wijziging van de kanaalpartner is verwerkt. U hoeft niet te wachten tot de huidige termijn is verstreken.


## <a name="resource-prepayment-and-requesting-quota-increases"></a>Resourcevooruitbetaling en het aanvragen van een hoger quotum

**De volgende standaardquota's worden automatisch per abonnement afgedwongen:**

| **Resource** | **Standaardquotum** | **Opmerkingen** |
| --- | --- | --- |
| Microsoft Azure Compute-instanties | Twintig gelijktijdige kleine computinginstanties of hun equivalent van de andere grootten voor computinginstanties. | In de volgende tabel wordt getoond hoe het equivalente aantal kleine instanties wordt berekend:<ul><li> Extra klein: één equivalente kleine instantie </li><li> Klein: één equivalente kleine instantie </li><li> Middelgroot: twee equivalente kleine instanties </li><li> Groot: vier equivalente kleine instanties </li><li> Extra groot: acht equivalente kleine instanties </li> </ul>|
| V2-VM's voor Microsoft Azure Compute-instanties | EA: 350 kernen | V2-VM's voor IaaS voor algemene beschikbaarheid:<ul><li> A0\_A7-serie - 350 kernen </li><li> B\_A0\_A4-serie - 350 kernen </li><li> A8\_A9-serie - 350 kernen </li><li> DF-serie - 350 kernen</li><li> GF - 350 kernen </li></ul>|
| Gehoste services van Microsoft Azure | Zes gehoste services | Het aantal gehoste services per afzonderlijk abonnement is maximaal zes services. Als u aanvullende gehoste services nodig hebt, moet u extra abonnementen toevoegen. |
| Microsoft Azure Storage | Vijf opslagaccounts, elk met een maximale grootte van 100 TB. | U kunt het aantal opslagaccounts verhogen tot twintig accounts per abonnement. Als u aanvullende opslagaccounts nodig hebt, moet u extra abonnementen toevoegen. |
| SQL Azure | 149 databases van een van de beschikbare typen (dat wil zeggen, Web Edition of Business Edition). |   |
| Toegangsbeheer | 50 naamruimten per account. 100 miljoen toegangsbeheertransacties per maand |   |
| Service Bus | 50 naamruimten per account. 40 servicebusverbindingen | Klanten die servicebusverbindingen kopen via verbindingspakketten, beschikken over quota met een aantal dat halverwege ligt tussen het verbindingspakket dat ze hebben gekocht en het verbindingspakket met het opeenvolgende hogere aantal verbindingen. Klanten die een pakket met 500 verbindingen kiezen, beschikken over een quotum van 750 verbindingen. |

## <a name="resource-prepayment"></a>Resourcevooruitbetaling

Microsoft biedt u services ten minste tot aan het niveau van het aangenomen gebruik behorend bij de maandelijkse vooruitbetaling die u hebt gekocht (de servicevooruitbetaling), maar alle andere verhogingen in de gebruiksniveaus van serviceresources (bijvoorbeeld verhoging van het aantal actieve rekeninstanties of verhoging van de hoeveelheid gebruikte opslag) zijn afhankelijk van de beschikbaarheid van deze serviceresources.

De hierboven beschreven quota zijn geen servicevooruitbetalingen. Om het aantal gelijktijdige kleine rekeninstanties (of een equivalent daarvan) te bepalen dat Microsoft biedt als onderdeel van een servicevooruitbetaling, wordt dit bepaald door het aantal toegezegde kleine rekeninstantie-uren dat voor een maand is gekocht te delen door het aantal uren in de kortste maand van het jaar (dus februari: 672 uur).

## <a name="requesting-a-quota-increase"></a>Een verhoging van het quotum aanvragen

U kunt op elk gewenst moment een verhoging van het quotum aanvragen door een [online aanvraag](https://ms.portal.azure.com/) in te dienen. Geef de volgende gegevens op om uw aanvraag te verwerken:

- Het Microsoft-account of het werk- of schoolaccount dat aan de accounteigenaar van uw abonnement is gekoppeld. Dit is het e-mailadres waarmee u zich aanmeldt bij Azure Portal om uw abonnement(en) te beheren. Geef ook aan dat dit account is gekoppeld aan een EA-inschrijving.
- De resource(s) en het bedrag waarvoor u een quotum wilt verhogen.
- De abonnements-id van de Azure-ontwikkelaarsportal die is gekoppeld aan uw service.
  - [Neem contact op met de ondersteuning](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) voor meer informatie over het verkrijgen van uw abonnements-id.

## <a name="plan-skus"></a>Abonnements-SKU's

Abonnements-SKU's bieden de mogelijkheid om een pakket geïntegreerde services te kopen tegen een gereduceerd tarief. De abonnements-SKU's vullen elkaar aan via andere geïntegreerde producten en maken grotere kostenbesparingen mogelijk.

Een voorbeeld hiervan is het OMS-abonnement (Operations Management Suite). OMS biedt op eenvoudige wijze toegang tot een volledige verzameling cloudbeheerfuncties, zoals analyses, configuratie, automatisering, beveiliging, back-ups en herstel na noodgevallen. OMS-abonnementen geven rechten op gebruik van System Center-onderdelen, zodat een volledige oplossing voor hybride cloudomgevingen wordt geboden.

Ondernemingsbeheerders kunnen accounteigenaren toewijzen om eerder gekochte abonnements-SKU's in Enterprise Portal in te richten door de volgende stappen uit te voeren:

### <a name="view-the-price-sheet-to-check-included-quantity"></a>Het prijzenoverzicht weergeven om de inbegrepen hoeveelheid te controleren

1. Meld u aan als ondernemingsbeheerder.
1. Klik in het linkernavigatievenster op **Rapporten**.
1. Klik op het tabblad **Prijzenoverzicht**.
1. Klik op het downloadpictogram in de rechterbovenhoek.
1. Zoek de betreffende SKU-onderdeelnummers voor het abonnement met het filter in de kolom Inbegrepen hoeveelheid, en selecteer waarden die groter zijn dan 0.

### <a name="existingnew-account-owners-to-create-new-subscriptions"></a>Bestaande/nieuwe accounteigenaars die nieuwe abonnementen maken

**Eerste stap: u aanmelden bij uw account**
1. Selecteer in Azure EA Portal het tabblad **Beheren** en navigeer naar **Abonnement** in het menu bovenaan.
1. Controleer of u bent aangemeld als de accounteigenaar van dit account.
1. Klik op **+Abonnement toevoegen**.
1. Klik op **Kopen**.

De eerste keer dat u een abonnement aan een account toevoegt, moet u uw contactgegevens opgeven. Wanneer u daarna aanvullende abonnementen toevoegt, worden uw contactgegevens automatisch voor u ingevuld.

De eerste keer dat u een abonnement aan uw account toevoegt, wordt u gevraagd de MOSA-overeenkomst en een tariefplan te accepteren. Deze secties zijn NIET van toepassing op Enterprise Agreement-klanten, maar zijn momenteel noodzakelijk om uw abonnement in te richten. Uw aangepaste Microsoft Azure Enterprise Agreement-inschrijving vervangt de bovenstaande items en uw contractuele relatie blijft ongewijzigd. Schakel het selectievakje in om aan te geven dat u akkoord gaat met de voorwaarden.

**Tweede stap: abonnementsnaam bijwerken**

Alle nieuwe abonnementen worden toegevoegd met de standaardabonnementsnaam Microsoft Azure Enterprise. Het is belangrijk dat u de abonnementsnaam bijwerkt zodat u deze kunt onderscheiden van de andere abonnementen in uw Enterprise-inschrijving en zodat deze kan worden herkend in rapporten op ondernemingsniveau.

Klik achtereenvolgens op **Abonnementen**, het abonnement dat u hebt gemaakt en **Abonnementsgegevens bewerken**.

Werk de abonnementsnaam en servicebeheerder bij en klik vervolgens op het vinkje. De abonnementsnaam wordt weergegeven in rapporten en is ook de naam van het project dat is gekoppeld aan het abonnement in de ontwikkelaarsportal.
Het kan tot 24 uur duren voordat nieuwe abonnementen worden vermeld in de abonnementenlijst.

Alleen accounteigenaren kunnen abonnementen bekijken en beheren.

### <a name="troubleshooting"></a>Problemen oplossen

**Accounteigenaar wordt aangegeven met de status In behandeling**

Wanneer nieuwe accounteigenaren (AO) de eerste keer worden toegevoegd aan de inschrijving, krijgen ze altijd de status In behandeling. Wanneer de accounteigenaar de welkomstmail voor de activering ontvangt, kan deze zich aanmelden om het account te activeren. Met deze activering wordt de accountstatus bijgewerkt van In behandeling naar Actief.

**Gebruik waarvoor kosten in rekening wordt gebracht na aanschaf van de abonnements-SKU's**

Dit scenario vindt plaats wanneer de klant services heeft geïmplementeerd onder het verkeerde inschrijvingsnummer of als deze de verkeerde services heeft geselecteerd.

Als u wilt controleren of u services implementeert voor de juiste inschrijving, kunt u de gegevens voor de inbegrepen eenheden controleren via het prijzenoverzicht. Meld u aan als Enterprise-beheerder, klik op **Rapporten** in het linkernavigatievenster en selecteer het tabblad **Prijzenoverzicht**. Klik op het downloadpictogram in de rechterbovenhoek, zoek de betreffende SKU-onderdeelnummers voor het abonnement met het filter in de kolom Inbegrepen hoeveelheid en selecteer waarden die groter zijn dan 0.

Controleer of uw OMS-abonnement wordt weergegeven in het prijzenoverzicht onder Inbegrepen eenheden. Als er geen inbegrepen eenheden voor het OMS-abonnement worden weergegeven voor uw inschrijving, is uw OMS-abonnement mogelijk opgenomen in een andere inschrijving. Neem contact op met Azure Enterprise Portal Support via [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

Als de inbegrepen eenheden voor de services in het prijzenoverzicht niet overeenkomen met wat u hebt geïmplementeerd, bijvoorbeeld Geanalyseerde Premium-gegevens voor Operational Insights versus Geanalyseerde standaardgegevens voor Operational Insights, hebt u mogelijk services geïmplementeerd die niet onder het abonnement vallen. Neem contact op met Azure Enterprise Portal Support op [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) zodat we u verder kunnen helpen.

**Services van een abonnements-SKU ingericht voor de verkeerde inschrijving**

Als u meerdere inschrijvingen hebt en services hebt geïmplementeerd onder het verkeerde inschrijvingsnummer dat geen OMS-abonnement bevat, neemt u contact op met Azure Enterprise Portal Support op [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

## <a name="next-steps"></a>Volgende stappen

- Zie [Aan de slag met Azure EA Portal](ea-portal-get-started.md) om te beginnen met het gebruik van Azure EA Portal.
- Azure EA Portal-beheerders moeten [Beheer van Azure EA Portal](ea-portal-administration.md) lezen voor meer informatie over algemene beheertaken.
