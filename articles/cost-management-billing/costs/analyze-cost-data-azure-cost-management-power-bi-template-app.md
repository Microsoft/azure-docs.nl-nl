---
title: Azure-kosten analyseren met de Power BI-app
description: In dit artikel wordt uitgelegd hoe de Azure Cost Management Power BI-app wordt geïnstalleerd en gebruikt.
author: bandersmsft
ms.author: banders
ms.date: 1/29/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: d3439f5a86c445898d95191a32496b5f0c86be0b
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070154"
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
2. Selecteer **Nu downloaden** op de pagina Power BI AppSource.
3. Selecteer **Doorgaan** om de gebruiksvoorwaarden en het privacybeleid te accepteren.
4. Selecteer in het vak **Deze Power BI-app installeren** de optie **Installeren**.
5. Maak zo nodig een werkruimte en selecteer **Doorgaan**.
6. Wanneer de installatie is voltooid, wordt er een melding weergegeven dat de nieuwe app gereed is.
7. Selecteer **Ga naar app**.
8. Selecteer in **Aan de slag met uw nieuwe app-** , onder **Verbinding maken met uw gegevens** de optie **Verbinding maken**.  
  ![Aan de slag met uw nieuwe app - Verbinding maken](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-data2.png)
9. Voer in het weergegeven dialoogvenster uw EA-inschrijvingsnummer in voor **BillingProfileIdOrEnrollmentNumber**. Geef het aantal maanden aan gegevens op dat moet worden opgehaald. Behoud de standaardwaarde voor **Bereik** van **Inschrijvingsnummer** en selecteer **Volgende**.  
  ![Informatie over EA-inschrijving invoeren](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  
10. Het volgende dialoogvenster maakt verbinding met Azure en haalt gegevens op die nodig zijn voor gereserveerde instantieaanbevelingen. *Laat de standaardwaarden zoals ze geconfigureerd zijn* en selecteer **Aanmelden**.  
  ![Schermopname van het dialoogvenster Verbinding maken met Azure Cost Management met standaardwaarden.](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png)  
11. De laatste installatiestap maakt verbinding met uw EA-inschrijving en vereist een [Enterprise Administrator](../manage/understand-ea-roles.md)-account. Selecteer **Aanmelden** voor verificatie met uw EA-inschrijving. Met deze stap wordt ook een actie voor gegevensvernieuwing in Power BI gestart.  
  ![Verbinding maken met EA-inschrijving](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png)  
    > [!NOTE]
    > Het proces voor gegevensvernieuwing kan enige tijd duren. De duur is afhankelijk van het aantal opgegeven maanden en de hoeveelheid gegevens die nodig is om te synchroniseren.
12. Als u de status van het vernieuwen van gegevens wilt controleren, selecteert u het tabblad **Gegevenssets** in de werkruimte. Kijk naast het vernieuwde tijdstempel. Als deze nog steeds wordt bijgewerkt, geeft een indicator aan dat het vernieuwen wordt uitgevoerd.  
  ![Gegevens vernieuwen](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/data-refresh2.png)

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

![Volledig rapport Azure Hybrid Benefits](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png)

Het rapport identificeert ook Windows-VM's waarvoor Hybrid Benefit is **ingeschakeld**, maar wanneer er _minder dan_ 8 vCPU's zijn. Het rapport laat ook zien waar Hybrid Benefit **niet is ingeschakeld** met 8 _of meer_ vcPU's. Deze informatie helpt u om volledig gebruik te kunnen maken van Hybrid Benefit. Pas het voordeel toe op uw duurste virtuele machines voor een maximale besparing.

![Azure Hybrid Benefit: minder dan 8 vCPU's en vCPU's niet ingeschakeld](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png)

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

![Rapport over VM-RI-dekking – inzoomen selecteren](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png)

Selecteer de regio die u wilt analyseren. Selecteer vervolgens de groep voor flexibiliteit van instantiegrootte, enzovoort.

Voor elk inzoomniveau worden de volgende filters toegepast op het rapport:

- De dekkingsgegevens aan de rechterkant fungeren als het filter dat laat zien hoeveel gebruik in rekening wordt gebracht bij gebruik van het tarief op aanvraag vergeleken met de hoeveelheid die door de reservering wordt gedekt.
- Aanbevelingen worden eveneens gefilterd.

De tabel met aanbevelingen bevat aanbevelingen voor de aankoop van de reservering, op basis van de gebruikte VM-grootten.

Met de _Genormaliseerde grootte_ en de waarden voor _Aanbevolen genormaliseerd aantal_ kunt u de aankoop normaliseren tot de kleinste grootte voor de flexibiliteitsgroep van de instantiegrootte. De informatie is handig als u van plan bent om slechts één reservering te kopen voor alle grootten in de flexibiliteitsgroep van de instantiegrootte.

![Aanbevelingen voor gereserveerde instanties](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recomendations.png)

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

![Informatie over EA-inschrijving invoeren](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  

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

Deze fout treedt op als u de verificatiemethode voor de AutoFitComboMeter-blobverbinding wijzigt.

#### <a name="solution"></a>Oplossing

1. Maak verbinding met uw gegevens.
1. Nadat u uw EA-registratie en aantal maanden invoert, moet u ervoor zorgen dat u de standaardwaarden **Anoniem** voor Verificatiemethode en **Geen** voor Privacyniveau behoudt.  
  ![Schermopname van het dialoogvenster Verbinding maken met Azure Cost Management met de ingevoerde waarden Anoniem en Geen.](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png)  
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