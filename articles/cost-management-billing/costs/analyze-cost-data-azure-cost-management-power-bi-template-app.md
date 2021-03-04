---
title: Azure-kosten analyseren met de Power BI-app
description: In dit artikel wordt uitgelegd hoe de Azure Cost Management Power BI-app wordt geïnstalleerd en gebruikt.
author: bandersmsft
ms.author: banders
ms.date: 02/19/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: b08ff57f964ef7bc3712c930c222a10ed0f89ef4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045049"
---
# <a name="analyze-cost-with-the-azure-cost-management-power-bi-app-for-enterprise-agreements-ea"></a>Analyseer kosten met de Azure Cost Management Power BI-app voor Enterprise Agreements (EA)

In dit artikel wordt uitgelegd hoe de Azure Cost Management Power BI-app wordt geïnstalleerd en gebruikt. De app helpt u bij het analyseren en beheren van uw Azure-kosten in Power BI. U kunt de app gebruiken voor het bewaken van kosten, het volgen van gebruikstrends en het vaststellen van opties voor kostenoptimalisatie om uw uitgaven te reduceren.

De app Azure Cost Management Power BI ondersteunt momenteel alleen klanten met een [Enterprise overeenkomst](https://azure.microsoft.com/pricing/enterprise-agreement/).

De app beperkt aanpassings mogelijkheden. Als u de standaard filters, weer gaven en visualisaties wilt wijzigen en uitbreiden om aan uw behoeften aan te passen, gebruikt u in plaats daarvan [Azure Cost Management-connector in Power bi Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management) . Met de Azure Cost Management-connector kunt u aanvullende gegevens uit andere bronnen samen voegen om aangepaste rapporten te maken voor het verkrijgen van holistische weer gaven van uw totale bedrijfs kosten. De connector biedt ook ondersteuning voor micro soft-klant overeenkomsten.

> [!NOTE]
> Power BI-sjabloon-apps bieden geen ondersteuning voor het downloaden van het PBIX-bestand.

## <a name="prerequisites"></a>Vereisten

- U hebt een [Power BI Pro-licentie](/power-bi/service-self-service-signup-for-power-bi) nodig om de app te installeren en te gebruiken.
- Als u verbinding wilt maken met gegevens, moet u een [Enterprise Administrator](../manage/understand-ea-roles.md)-account gebruiken. De rol Ondernemingsbeheerder (alleen-lezen) wordt ondersteund.

## <a name="installation-steps"></a>Installatiestappen

De app installeren:

1. Open [Azure Cost Management Power BI-app](https://aka.ms/costmgmt/ACMApp).
1. Selecteer **Nu downloaden** op de pagina Power BI AppSource.
1. Selecteer **Doorgaan** om de gebruiksvoorwaarden en het privacybeleid te accepteren.
1. Selecteer in het vak **Deze Power BI-app installeren** de optie **Installeren**.
1. Maak zo nodig een werkruimte en selecteer **Doorgaan**.
1. Wanneer de installatie is voltooid, wordt er een melding weergegeven dat de nieuwe app gereed is.
1. Selecteer de app die u hebt geïnstalleerd.
1. Selecteer op de pagina aan de slag **de optie verbinding maken met uw gegevens**.
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-your-data.png" alt-text="Scherm opname van de koppeling uw gegevens verbinden." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-your-data.png" :::
1. Voer in het weergegeven dialoogvenster uw EA-inschrijvingsnummer in voor **BillingProfileIdOrEnrollmentNumber**. Geef het aantal maanden aan gegevens op dat moet worden opgehaald. Behoud de standaardwaarde voor **Bereik** van **Inschrijvingsnummer** en selecteer **Volgende**.  
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png" alt-text="Scherm afbeelding die laat zien waar u uw E registratie gegevens invoert." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png" :::
1. In het volgende dialoog venster wordt verbinding gemaakt met Azure en worden gegevens opgehaald. *Wijzig de standaard waarden zoals geconfigureerd* en selecteer **Aanmelden en door gaan**.  
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png" alt-text="Scherm opname van het dialoog venster verbinding maken met Azure Cost Management-App met standaard waarden." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png" :::
1. De laatste installatiestap maakt verbinding met uw EA-inschrijving en vereist een [Enterprise Administrator](../manage/understand-ea-roles.md)-account. Alle standaard waarden behouden. Selecteer **Aanmelden en verbinden**.  
    :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png" alt-text="Scherm opname van het dialoog venster verbinding maken met Azure Cost Management App met standaard waarden om verbinding mee te maken." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png" :::
1. U wordt gevraagd om u te verifiëren met uw EA-inschrijving. Verifiëren met Power BI. Nadat u bent geverifieerd, wordt het vernieuwen van gegevens Power BI gestart.
    > [!NOTE]
    > Het proces voor gegevensvernieuwing kan enige tijd duren. De duur is afhankelijk van het aantal opgegeven maanden en de hoeveelheid gegevens die nodig is om te synchroniseren.

Nadat het vernieuwen van de gegevens is voltooid, selecteert u de Azure Cost Management-app om de vooraf gemaakte rapporten weer te geven.

## <a name="reports-available-with-the-app"></a>Rapporten die beschikbaar zijn in de app

De volgende rapporten zijn beschikbaar in de app.

**Aan de slag**: bevat handige koppelingen naar documentatie en koppelingen om feedback te geven.

**Accountoverzicht**: het rapport bevat een maandoverzicht met informatie zoals:

- Kosten tegen tegoed
- Nieuwe aankopen
- Kosten voor Azure Marketplace
- Overschrijdingen en totale kosten

**Gebruik op basis van abonnementen en resourcegroepen**: bevat een overzicht van het verloop van kosten en grafieken met kosten per abonnement en resourcegroep.

**Gebruik op basis van services**: bevat het verloop van gebruik per MeterCategory. U kunt uw gebruiksgegevens bijhouden en afwijkingen weergeven om inzicht te krijgen in pieken en dalen.

**Top 5 drijfveren voor gebruik**: het rapport bevat een gefilterd kostenoverzicht met de top 5 voor MeterCategory en de bijbehorende MeterName.

**Gebruik van Windows Server AHB**: het rapport bevat het aantal virtuele machines waarvoor Azure Hybrid Benefit is ingeschakeld. Ook wordt het aantal kernen/vCPU's weergegeven dat door de virtuele machines wordt gebruikt.

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png" alt-text="Scherm afbeelding met het volledige Azure Hybrid bene fits-rapport." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png" :::

Het rapport identificeert ook Windows-VM's waarvoor Hybrid Benefit is **ingeschakeld**, maar wanneer er _minder dan_ 8 vCPU's zijn. Het rapport laat ook zien waar Hybrid Benefit **niet is ingeschakeld** met 8 _of meer_ vcPU's. Deze informatie helpt u om volledig gebruik te kunnen maken van Hybrid Benefit. Pas het voordeel toe op uw duurste virtuele machines voor een maximale besparing.

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png" alt-text="Scherm afbeelding met het gebied minder dan 8 Vcpu's en Vcpu's niet ingeschakeld van het Azure Hybrid bene fits-rapport." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png" :::

**Toerekenen aan een gereserveerde instantie**: het rapport laat zien waar en hoeveel van een gereserveerde instantie (RI) voordeel wordt toegepast per regio, abonnement, resourcegroep of resource. In het rapport wordt voor de weergave gebruikgemaakt van afgeschreven gebruiksgegevens.

U kunt een filter toepassen op _chargetype_ om gegevens over onderbenutting van gereserveerde instanties weer te geven.

Zie [Kosten en gebruik van Enterprise Agreement-reserveringen ophalen](../reservations/understand-reserved-instance-usage-ea.md) voor meer informatie over afgeschreven kosten.

**Besparingen op gereserveerde instanties**: het rapport toont de gerealiseerde besparingen op basis van reserveringen voor abonnement, resourcegroep en resourceniveau. Hiermee wordt het volgende weergegeven:

- Kosten met reservering
- Geschatte kosten op aanvraag als de reservering niet van toepassing is op het gebruik
- Kostenbesparingen berekend op basis van de reservering

 In het rapport worden alle kosten door verspilling vanwege onderbenutting van de totale besparingen afgetrokken. Deze verspilling zou niet plaatsvinden zonder reservering.

U kunt met de gegevens over afgeschreven gebruik de gegevens verder uitbouwen.

<a name="shared-recommendation"></a>
**VM-RI-dekking (gedeelde aanbeveling)** : het rapport wordt opgesplitst tussen het gebruik van VM's op aanvraag en het gebruik van RI-VM'S gedurende de geselecteerde periode. Het rapport bevat aanbevelingen voor aankoop van VMI-RI's bij een gedeeld bereik.

U gebruikt het rapport door het filter voor inzoomen te selecteren.

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png" alt-text="Scherm afbeelding met de optie Inzoomen selecteren in het VM-gebied voor de behoefte planning." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png" :::

Selecteer de regio die u wilt analyseren. Selecteer vervolgens de groep voor flexibiliteit van instantiegrootte, enzovoort.

Voor elk inzoomniveau worden de volgende filters toegepast op het rapport:

- De dekkingsgegevens aan de rechterkant fungeren als het filter dat laat zien hoeveel gebruik in rekening wordt gebracht bij gebruik van het tarief op aanvraag vergeleken met de hoeveelheid die door de reservering wordt gedekt.
- Aanbevelingen worden eveneens gefilterd.

De tabel met aanbevelingen bevat aanbevelingen voor de aankoop van de reservering, op basis van de gebruikte VM-grootten.

Met de _Genormaliseerde grootte_ en de waarden voor _Aanbevolen genormaliseerd aantal_ kunt u de aankoop normaliseren tot de kleinste grootte voor de flexibiliteitsgroep van de instantiegrootte. De informatie is handig als u van plan bent om slechts één reservering te kopen voor alle grootten in de flexibiliteitsgroep van de instantiegrootte.

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recommendations.png" alt-text="Scherm afbeelding van het rapport met aanbevelingen voor de RI." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recommendations.png" :::

**VM-RI-dekking (één aanbeveling)** : het rapport wordt opgesplitst tussen het gebruik van VM's op aanvraag en het gebruik van RI-VM'S gedurende de geselecteerde periode. Het rapport bevat aanbevelingen voor aankoop van VMI-RI's bij een abonnementsbereik.

Zie de sectie [VM-RI-dekking (gedeelde aanbeveling)](#shared-recommendation) voor meer informatie over het gebruik van het rapport.

**RI-aankopen**: in het rapport worden RI-aankopen weergegeven voor de opgegeven periode.

**Prijzenoverzicht**: het rapport bevat een gedetailleerde lijst met specifieke prijzen voor een factureringsaccount of EA-inschrijving.

## <a name="troubleshoot-problems"></a>Problemen oplossen

Als u nog steeds problemen hebt met de Power BI-app, kan de volgende probleemoplossingsinformatie daarbij mogelijk helpen.

### <a name="error-processing-the-data-in-the-dataset"></a>Fout bij het verwerken van gegevens in de gegevensset

U krijgt mogelijk een fout met het volgende bericht:

```
There was an error when processing the data in the dataset.
Data source error: {"error":{"code":"ModelRefresh_ShortMessage_ProcessingError","pbi.error":{"code":"ModelRefresh_ShortMessage_ProcessingError","parameters":{},"details":[{"code":"Message","detail":{"type":1,"value":"We cannot convert the value \"Required Field: 'Enr...\" to type List."}}],"exceptionCulprit":1}}} Table: <TableName>.
```

Er wordt een tabelnaam weer gegeven in plaats van `<TableName>`.

#### <a name="cause"></a>Oorzaak

De standaardwaarde `Enrollment Number` van **Bereik** is veranderd in de verbinding met Cost Management.

#### <a name="solution"></a>Oplossing

Maak opnieuw verbinding met Cost Management en stel de waarde voor **Bereik** in op `Enrollment Number`. Voer het inschrijvingsnummer van uw organisatie niet in. Typ in plaats daarvan `Enrollment Number` exact zoals dit in de volgende afbeelding verschijnt.

:::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number-troubleshoot.png" alt-text="Scherm opname waarin wordt getoond dat de standaard tekst van het inschrijvings nummer niet mag worden gewijzigd." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number-troubleshoot.png" :::

### <a name="budgetamount-error"></a>Fout in BudgetAmount

U krijgt mogelijk een fout met het volgende bericht:

```
Something went wrong
There was an error when processing the data in the dataset.
Please try again later or contact support. If you contact support, please provide these details.
Data source error: The 'budgetAmount' column does not exist in the rowset. Table: Budgets.
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt op door een fout in de onderliggende metagegevens. Het probleem treedt op omdat er geen budget beschikbaar is onder **Cost Management > Budget** in de Azure-portal. Deze fout wordt geïmplementeerd in de Power BI Desktop en Power BI-service.

#### <a name="solution"></a>Oplossing

- Tot de fout is opgelost, kunt u om het probleem heen werken door een testbudget toe te voegen in de Azure-portal op het factureringsrekening-/EA-registrateniveau. Het testbudget kunt u weer verbinding maken met Power BI. Raadpleeg voor meer informatie over een budget maken [Zelfstudie: Azure-budgetten maken en beheren](tutorial-acm-create-budgets.md).

### <a name="invalid-credentials-for-azureblob-error"></a>Ongeldige referenties voor AzureBlob-fout

U krijgt mogelijk een fout met het volgende bericht:

```
Failed to update data source credentials: The credentials provided for the AzureBlobs source are invalid.
```

#### <a name="cause"></a>Oorzaak

Deze fout treedt op als u de verificatie methode voor de gegevens bron verbinding wijzigt.

#### <a name="solution"></a>Oplossing

1. Maak verbinding met uw gegevens.
1. Nadat u uw EA-registratie en aantal maanden invoert, moet u ervoor zorgen dat u de standaardwaarden **Anoniem** voor Verificatiemethode en **Geen** voor Privacyniveau behoudt.  
  :::image type="content" source="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png" alt-text="Schermopname van het dialoogvenster Verbinding maken met Azure Cost Management met de ingevoerde waarden Anoniem en Geen." lightbox="./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png" :::
1. Stel op de volgende pagina **OAuth2** voor de verificatiemethode in en **Geen** voor het Privacyniveau. Meld vervolgens aan om uw inschrijving te verifiëren. Met deze stap wordt ook een gegevensvernieuwing in Power BI gestart.

## <a name="data-reference"></a>Verwijzing naar gegevens

De volgende informatie bevat een overzicht van de gegevens die beschikbaar zijn via de app. Er zijn ook koppelingen naar API's die gedetailleerde informatie geven over gegevensvelden en waarden.

| **Tabelreferentie** | **Beschrijving** |
| --- | --- |
| **AutoFitComboMeter** | Gegevens die in de app zijn opgenomen om de aanbeveling en het gebruik van RI te normaliseren op basis van de kleinste grootte in de familiegroep van de instantie. |
| [**Saldo-overzicht**](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary#response) | Samenvatting van het saldo voor Enterprise Agreements. |
| [**Budgetten**](/rest/api/consumption/budgets/get#definitions) | Budgetgegevens om de werkelijke kosten of het gebruik te bekijken voor bestaande budgetdoelen. |
| [**Prijzenoverzichten**](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet#see-also) | Toepasselijke metertarieven voor het verstrekte factureringsprofiel of de verstrekte EA-inschrijving. |
| [**RI-kosten**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges#response) | Kosten die zijn gekoppeld aan uw gereserveerde instanties in de afgelopen 24 maanden. |
| [**RI-aanbevelingen (gedeeld)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response) | Aankoopaanbevelingen voor gereserveerde instanties op basis van de trends in het gebruik van al uw abonnementen voor de afgelopen 7 dagen. |
| [**RI-aanbevelingen (enkelvoudig)**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response-1) | Aankoopaanbevelingen voor gereserveerde instanties op basis van uw trends in het gebruik van uw abonnement voor de afgelopen 7 dagen. |
| [**Gegevens over RI-gebruik**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#response) | Verbruiksgegevens voor uw bestaande gereserveerde instanties in de afgelopen maand. |
| [**Overzicht van het gebruik van RI**](/rest/api/consumption/reservationssummaries/list) | Percentage dagelijks reserveringsgebruik van Azure |
| [**Gebruiksgegevens**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | Een uitsplitsing van verbruikte hoeveelheden en geschatte kosten voor het gegeven factureringsprofiel in de EA-inschrijving. |
| [**Details van afgeschreven gebruik**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | Een uitsplitsing van verbruikte hoeveelheden en geschatte kosten voor afgeschreven gebruik voor het gegeven factureringsprofiel in de EA-inschrijving. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het configureren en vernieuwen van gegevens, het delen van rapporten en aanvullende rapportaanpassingen:

- [Geplande vernieuwing configureren](/power-bi/refresh-scheduled-refresh)
- [Power BI-dashboards en -rapporten delen met collega's en anderen](/power-bi/service-share-dashboards)
- [Abonneer uzelf en anderen op rapporten en dashboards in de Power BI-service](/power-bi/service-report-subscribe)
- [Een rapport downloaden van de Power BI-service naar Power BI Desktop](/power-bi/service-export-to-pbix)
- [Een rapport opslaan in Power BI-service en Power BI Desktop](/power-bi/service-report-save)
- [Een rapport maken in de Power BI-service door een gegevensset te importeren](/power-bi/service-report-create-new)