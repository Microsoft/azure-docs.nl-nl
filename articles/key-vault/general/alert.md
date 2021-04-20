---
title: Azure Key Vault bewaking en waarschuwingen | Microsoft Docs
description: Maak een dashboard om de status van uw sleutelkluis te bewaken en waarschuwingen te configureren.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: f8f9dd6d51b974ebd31804daf0402ca5535ffc92
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751576"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Bewaking en waarschuwingen voor Azure Key Vault

## <a name="overview"></a>Overzicht

Zodra u de sleutelkluis hebt gebruikt om uw productiegeheimen op te slaan, is het belangrijk om de status van uw sleutelkluis te controleren om ervoor te zorgen dat uw service werkt zoals bedoeld. Wanneer u begint met het schalen van uw service, neemt het aantal aanvragen toe dat naar uw sleutelkluis wordt verzonden. Dit kan de latentie van uw aanvragen verhogen en er in uitzonderlijke gevallen voor zorgen dat uw aanvragen worden beperkt, waardoor de prestaties van uw service worden beïnvloed. U moet ook worden gewaarschuwd als uw sleutelkluis een ongebruikelijk aantal foutcodes verstuurt, zodat u snel op de hoogte kunt worden gesteld van problemen met toegangsbeleid of firewallconfiguratie. In dit document komen de volgende onderwerpen aan bod:

+ Metrische Key Vault basisgegevens die moeten worden bewaakt
+ Metrische gegevens configureren en een dashboard maken
+ Waarschuwingen maken bij opgegeven drempelwaarden

Azure Monitor voor Key Vault logboeken en metrische gegevens gecombineerd om een globale bewakingsoplossing te bieden. [Meer informatie over Azure Monitor voor Key Vault hier](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview#introduction-to-azure-monitor-for-key-vault)

## <a name="basic-key-vault-metrics-to-monitor"></a>Metrische Key Vault basisgegevens die moeten worden bewaakt

+ Beschikbaarheid van kluis  
+ Verzadiging van kluis 
+ Latentie van service-API 
+ Totaal aantal treffers service-API (filteren op activiteitstype) 
+ Foutcodes (filteren op statuscode) 

**Beschikbaarheid van** kluis: deze metrische gegevens moeten altijd 100% zijn. Dit is een belangrijke metriek die u moet bewaken, omdat u hiermee snel kunt zien of er een storing is in uw sleutelkluis. 

**Verzadiging van** kluis: het aantal aanvragen per seconde dat een sleutelkluis kan verwerken, is gebaseerd op het type bewerking dat wordt uitgevoerd. Sommige kluisbewerkingen hebben een lagere drempelwaarde voor aanvragen per seconde. Met deze metrische waarde wordt het totale gebruik van uw sleutelkluis voor alle bewerkingstypen geaggregeerd, zodat er een percentagewaarde wordt gebruikt dat uw huidige sleutelkluisgebruik aangeeft. Zie het volgende document voor een volledige lijst met servicelimieten voor key vault. [Limieten van Azure Key Vault-service](service-limits.md)

**Service-API-latentie:** deze metrische waarde toont de gemiddelde latentie van aanroepen naar de sleutelkluis, gemeten in de service. Dit omvat geen tijd die wordt verbruikt door de client of door het netwerk tussen client en service.

**Totaal aantal API-treffers:** deze metrische waarde toont alle aanroepen naar uw sleutelkluis. Hiermee kunt u bepalen welke toepassingen uw sleutelkluis aanroepen. 

**Foutcodes:** deze metrische gegevens geven aan of uw sleutelkluis te maken heeft met een ongebruikelijk aantal fouten. Zie het volgende document voor een volledige lijst met foutcodes en richtlijnen voor probleemoplossing. [Azure Key Vault REST API foutcodes](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Metrische gegevens configureren en een dashboard maken

1. Meld u aan bij de Azure-portal
2. Navigeer naar uw Key Vault
3. Selecteer **Metrische** gegevens onder **Bewaking** 

> [!div class="mx-imgBorder"]
> ![Schermopname met de optie Metrische gegevens onder de sectie Bewaking.](../media/alert-1.png)

4. Werk de titel van de grafiek bij naar wat u op uw dashboard wilt zien. 
5. Selecteer het bereik. In dit voorbeeld selecteren we één sleutelkluis. 
6. Selecteer de gem. **beschikbaarheid en**  aggregatie van de metrische gegevens 
7. Werk het tijdsbereik bij naar de afgelopen 24 uur en werk de tijdgranulatie bij naar 1 minuut. 

> [!div class="mx-imgBorder"]
> ![Schermopname van de metrische gegevens Over beschikbaarheid van de algehele kluis.](../media/alert-2.png)

8. Herhaal de bovenstaande stappen voor de metrische gegevens Over verzadiging van de kluis en service-API-latentie. Selecteer **Vastmaken aan dashboard om** uw metrische gegevens op te slaan in een dashboard. 

> [!IMPORTANT]
> Selecteer Vastmaken aan dashboard en sla alle metrische gegevens op die u configureert. Als u de pagina verlaat en terugkeert zonder op te slaan, gaan uw configuratiewijzigingen verloren. 

9. Als u alle typen bewerkingen in de sleutelkluis wilt bewaken, gebruikt u de metriek Total **Service API Hits** en selecteert u Splitsing toepassen op **activiteitstype**

> [!div class="mx-imgBorder"]
> ![Schermopname van de knop Splitsen toepassen.](../media/alert-3.png)

10. Als u wilt controleren op foutcodes in de sleutelkluis, gebruikt u de metrische gegevens voor total **service-API-resultaten** en selecteert u **Splitsen toepassen op activiteitstype**

> [!div class="mx-imgBorder"]
> ![Schermopname met de geselecteerde metrische gegevens voor total service-API-resultaten.](../media/alert-4.png)

U hebt nu een dashboard dat er als dit uitziet. U kunt op de drie puntjes rechtsboven in elke tegel klikken en de tegels naar behoefte opnieuw rangschikken en het ize van de tegels aan te geven. 

Zodra u het dashboard hebt op slaan en publiceren, maakt het een nieuwe resource in uw Azure-abonnement. U kunt dit op elk gewenst moment zien door te zoeken naar 'gedeeld dashboard'. 

> [!div class="mx-imgBorder"]
> ![Schermopname van het gepubliceerde dashboard.](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Waarschuwingen configureren op uw Key Vault 

In deze sectie ziet u hoe u waarschuwingen voor uw sleutelkluis configureert, zodat u uw team kunt waarschuwen dat er onmiddellijk actie moet worden ondernomen als uw sleutelkluis een slechte status heeft. U kunt waarschuwingen configureren die een e-mailbericht verzenden, bij voorkeur naar een team-DL, een Event Grid-melding verzenden of een telefoonnummer bellen of sms-berichten verzenden. U kunt ook statische waarschuwingen kiezen op basis van een vaste waarde of een dynamische waarschuwing die u waarschuwt als een bewaakte metrische waarde de gemiddelde limiet van uw sleutelkluis een bepaald aantal keer binnen een gedefinieerd tijdsbereik overschrijdt. 

> [!IMPORTANT]
> Houd er rekening mee dat het maximaal 10 minuten kan duren voordat nieuw geconfigureerde waarschuwingen meldingen gaan verzenden. 

### <a name="configure-an-action-group"></a>Een actiegroep configureren 

Een actiegroep is een configureerbare lijst met meldingen en eigenschappen.

1. Meld u aan bij de Azure-portal
2. Zoeken naar **waarschuwingen** in het zoekvak
3. Acties **beheren selecteren**

> [!div class="mx-imgBorder"]
> ![Schermopname met de knop Acties beheren.](../media/alert-6.png)

4. Selecteer **+ Actiegroep toevoegen**

> [!div class="mx-imgBorder"]
> ![Schermopname met de knop + Actiegroep toevoegen.](../media/alert-7.png)

5. Kies het **Actietype** voor uw actiegroep. In dit voorbeeld maken we een e-mailwaarschuwing.

> [!div class="mx-imgBorder"]
> ![Schermopname met de velden die nodig zijn om een actiegroep toe te voegen.](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Schermopname die laat zien wat er nodig is om een e-mail- of sms-berichtwaarschuwing toe te voegen.](../media/alert-9.png)

6. Klik onder aan de pagina op **OK**. U hebt een actiegroep gemaakt. 

Nu u een actiegroep hebt geconfigureerd, configureren we de waarschuwingsdrempels voor de sleutelkluis. 

### <a name="configure-alert-thresholds"></a>Waarschuwingsdrempels configureren 

1. Selecteer uw sleutelkluisresource in Azure Portal en selecteer **Waarschuwingen** onder **Bewaking**

> [!div class="mx-imgBorder"]
> ![Schermopname van de menuoptie Waarschuwingen onder de sectie Bewaking.](../media/alert-10.png)

2. Selecteer **Nieuwe waarschuwingsregel**

> [!div class="mx-imgBorder"]
> ![Schermopname van de knop + Nieuwe waarschuwingsregel.](../media/alert-11.png)

3. Selecteer het bereik van de waarschuwingsregel. U kunt één of meerdere kluizen selecteren. 

> [!IMPORTANT]
> Houd er rekening mee dat wanneer u meerdere kluizen selecteert voor het bereik van uw waarschuwingen, alle geselecteerde kluizen zich in dezelfde regio moeten hebben. U moet afzonderlijke waarschuwingsregels configureren voor kluizen in verschillende regio's. 

> [!div class="mx-imgBorder"]
> ![Schermopname die laat zien hoe u een kluis kunt selecteren.](../media/alert-12.png)

4. Selecteer de voorwaarden voor uw waarschuwingen. U kunt een van de volgende signalen kiezen en uw logica voor waarschuwingen definiëren. Het Key Vault team raadt aan de volgende waarschuwingsdrempels te configureren. 

    + Key Vault beschikbaarheid is lager dan 100% (statische drempelwaarde)
    + Key Vault latentie groter is dan 500 ms (statische drempelwaarde) 
    + De algehele verzadiging van de kluis is groter dan 75% (statische drempelwaarde) 
    + Algehele verzadiging van kluis overschrijdt het gemiddelde (dynamische drempelwaarde)
    + Totaal aantal foutcodes hoger dan gemiddeld (dynamische drempelwaarde) 

> [!div class="mx-imgBorder"]
> ![Schermopname die laat zien waar u voorwaarden voor waarschuwingen selecteert.](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Voorbeeld 1: Een statische waarschuwingsdrempel voor latentie configureren

Selecteer **Algemene service-API-latentie** als signaalnaam
> [!div class="mx-imgBorder"]
> ![Schermopname van de signaalnaam voor de algehele service-API-latentie.](../media/alert-14.png)

Raadpleeg de volgende configuratieparameters.

+ Stel de drempelwaarde in op **Statisch** 
+ Stel de operator in op **Groter dan**
+ Stel aggregatietype in op **Gemiddeld**
+ Stel de drempelwaarde in op **500**
+ Aggregatieperiode instellen op **5 minuten**
+ Stel de evaluatiefrequentie in op **1 minuut**
+ Selecteer **Gereed**  

> [!div class="mx-imgBorder"]
> ![Schermopname met de geconfigureerde waarschuwingslogica.](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Voorbeeld 2: Een dynamische waarschuwingsdrempel configureren voor kluisverzadiging 

Wanneer u een dynamische waarschuwing gebruikt, kunt u historische gegevens bekijken van de sleutelkluis die u hebt geselecteerd. Het blauwe gebied geeft het gemiddelde gebruik van uw sleutelkluis aan. Het rode gebied toont pieken die een waarschuwing zouden hebben geactiveerd, mits aan andere criteria in de waarschuwingsconfiguratie wordt voldaan. De rode punten geven exemplaren van schendingen weer waaraan is voldaan aan de criteria voor de waarschuwing tijdens het geaggregeerde tijdvenster. U kunt binnen een bepaalde tijd een waarschuwing instellen om te worden ge fireed na een bepaald aantal schendingen. Als u geen eerdere gegevens wilt opnemen, is er een optie om oude gegevens hieronder uit te sluiten in geavanceerde instellingen. 

> [!div class="mx-imgBorder"]
> ![Schermopname van een grafiek van de algehele verzadiging van de kluis.](../media/alert-16.png)

Raadpleeg de volgende configuratieparameters.

+ De drempelwaarde instellen op **Dynamisch** 
+ Stel operator in op **Groter dan**
+ Stel aggregatietype in op **Gemiddeld**
+ Stel Drempelgevoeligheid in op **Gemiddeld**
+ Aggregatieperiode instellen op **5 minuten**
+ De evaluatiefrequentie instellen op **1 minuut**
+ **Optioneel** Geavanceerde instellingen configureren 
+ Selecteer **Gereed**

> [!div class="mx-imgBorder"]
> ![Schermopname van Azure Portal](../media/alert-17.png)

5. De actiegroep toevoegen die u hebt geconfigureerd

> [!div class="mx-imgBorder"]
> ![Schermopname die laat zien hoe u een actiegroep toevoegt.](../media/alert-18.png)

6. De waarschuwing inschakelen en een ernst toewijzen

> [!div class="mx-imgBorder"]
> ![Schermopname die laat zien waar u de waarschuwing kunt inschakelen en een ernst kunt toewijzen.](../media/alert-19.png)

7. De waarschuwing maken 

### <a name="example-email-alert"></a>Voorbeeld van e-mailwaarschuwing 

> [!div class="mx-imgBorder"]
> ![Schermopname met de informatie die nodig is om een e-mailwaarschuwing te configureren.](../media/alert-20.png)

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt nu een bewakingsdashboard gemaakt en waarschuwingen geconfigureerd voor uw sleutelkluis. Nadat u alle bovenstaande stappen hebt gevolgd, ontvangt u e-mailwaarschuwingen wanneer uw sleutelkluis voldoet aan de waarschuwingscriteria die u hebt geconfigureerd. Hieronder kunt u een voorbeeld bekijken. Gebruik de hulpprogramma's die u in dit artikel hebt ingesteld om de status van uw sleutelkluis actief te bewaken. 


