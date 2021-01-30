---
title: Bewakings-en waarschuwings functies Azure Key Vault | Microsoft Docs
description: Maak een dash board om de status van uw sleutel kluis te controleren en waarschuwingen te configureren.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: 1b447e907bca3f8d21f522d888632aa687fd0f91
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070658"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Bewaking en waarschuwingen voor Azure Key Vault

## <a name="overview"></a>Overzicht

Zodra u de sleutel kluis hebt gestart om uw productie geheimen op te slaan, is het belang rijk om de status van uw sleutel kluis te controleren om er zeker van te zijn dat uw service goed werkt. Wanneer u begint met het schalen van uw service, neemt het aantal aanvragen toe dat naar uw sleutelkluis wordt verzonden. Dit kan de latentie van uw aanvragen verhogen en er in uitzonderlijke gevallen voor zorgen dat uw aanvragen worden beperkt, waardoor de prestaties van uw service worden beïnvloed. U moet ook een waarschuwing ontvangen als uw sleutel kluis een ongebruikelijk aantal fout codes verzendt, zodat u snel kunt worden geïnformeerd over problemen met toegangs beleid of firewall configuratie. In dit document worden de volgende onderwerpen behandeld:

+ Basis Key Vault metrische gegevens die moeten worden bewaakt
+ Metrische gegevens configureren en een dash board maken
+ Waarschuwingen maken met de opgegeven drempel waarden

## <a name="basic-key-vault-metrics-to-monitor"></a>Basis Key Vault metrische gegevens die moeten worden bewaakt

+ Beschik baarheid kluis  
+ Intensiteit van de kluis 
+ API-latentie van service 
+ Totaal aantal treffers in de service-API (filteren op activiteitstype) 
+ Fout codes (filteren op status code) 

**Beschik baarheid** van de kluis: deze metriek moet altijd 100% zijn. Dit is een belang rijke metrische waarde om te bewaken, omdat u hiermee snel kunt zien of uw sleutel kluis een storing heeft ondervonden. 

**Intensiteit** van de kluis: het aantal aanvragen per seconde dat een sleutel kluis kan leveren is gebaseerd op het type bewerking dat wordt uitgevoerd. Voor sommige kluis bewerkingen gelden lagere drempel waarden per seconde. Met deze metrische waarde wordt het totale gebruik van uw sleutel kluis geaggregeerd voor alle bewerkings typen die worden opgehaald met een percentage dat het huidige gebruik van de sleutel kluis aangeeft. Raadpleeg het volgende document voor een volledige lijst met de sleutel kluis-service limieten. [Limieten van Azure Key Vault-service](service-limits.md)

**API-latentie van service** -deze metriek toont de gemiddelde latentie van aanroepen naar sleutel kluis, gemeten bij de service. Het bevat geen tijd die wordt verbruikt door de client of het netwerk tussen client en service.

**Totaal aantal API-treffers** : in deze metrische gegevens worden alle aanroepen van uw sleutel kluis weer gegeven. Dit helpt u te bepalen welke toepassingen uw sleutel kluis bellen. 

**Fout codes** : deze metrische gegevens worden weer gegeven als uw sleutel kluis een ongebruikelijke hoeveelheid fouten ondervindt. Raadpleeg het volgende document voor een volledige lijst met fout codes en richt lijnen voor probleem oplossing. [Azure Key Vault REST API fout codes](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Metrische gegevens configureren en een dash board maken

1. Meld u aan bij de Azure-portal
2. Ga naar uw Key Vault
3. **Metrische gegevens** selecteren onder **bewaking** 

> [!div class="mx-imgBorder"]
> ![Scherm opname van de optie metrische gegevens in de sectie bewaking.](../media/alert-1.png)

4. Werk de titel van de grafiek bij naar wat u op het dash board wilt weer geven. 
5. Selecteer het bereik. In dit voor beeld wordt een enkele sleutel kluis geselecteerd. 
6. Selecteer de metrische gegevens over de **gehele Beschik baarheid** en aggregatie van de kluis  
7. Werk het tijds bereik bij in de afgelopen 24 uur en werk de tijd granulatie bij naar 1 minuut. 

> [!div class="mx-imgBorder"]
> ![Scherm opname van de algemene beschikbaarheids metric van de kluis.](../media/alert-2.png)

8. Herhaal de bovenstaande stappen voor de metrische gegevens voor de kluis verzadiging en service-API-latentie. Selecteer **vastmaken aan dash board** om uw metrische gegevens in een dash board op te slaan. 

> [!IMPORTANT]
> Selecteer vastmaken aan dash board en sla elke metrische waarde op die u configureert. Als u de pagina verlaat en terugkeert zonder op te slaan, gaan de configuratie wijzigingen verloren. 

9. Als u alle typen bewerkingen op de sleutel kluis wilt bewaken, gebruikt u de totale metrische gegevens voor **Service-API-Hits** en selecteert u splitsen op **activiteitstype Toep assen**

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding met de knop splitsing Toep assen.](../media/alert-3.png)

10. Als u wilt controleren op fout codes in de sleutel kluis, gebruikt u de totale metrische gegevens van de **Service-API-resultaten** en selecteert u vervolgens **op type splitsing Toep assen op activiteitstype**

> [!div class="mx-imgBorder"]
> ![Scherm opname van het geselecteerde totale aantal metrische Service-API-resultaten.](../media/alert-4.png)

U hebt nu een dash board dat er als volgt uitziet. U kunt in de rechter bovenhoek van elke tegel klikken op de drie punten en u kunt de tegels aanpassen en het formaat ervan wijzigen. 

Zodra u het dash board opslaat en publiceert, wordt er een nieuwe resource in uw Azure-abonnement gemaakt. U kunt deze op elk gewenst moment weer geven door te zoeken naar "gedeeld dash board". 

> [!div class="mx-imgBorder"]
> ![Scherm opname waarin het gepubliceerde dash board wordt weer gegeven.](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Waarschuwingen op uw Key Vault configureren 

In deze sectie wordt uitgelegd hoe u waarschuwingen kunt configureren voor uw sleutel kluis, zodat u een waarschuwing krijgt dat uw team onmiddellijk actie moet ondernemen als uw sleutel kluis een slechte status heeft. U kunt waarschuwingen configureren waarmee een e-mail bericht wordt verzonden, bij voor keur aan een team DL, een event grid-melding of een telefoon nummer of tekst oproep of SMS-bericht. U kunt ook statische waarschuwingen kiezen op basis van een vaste waarde, of een dynamische waarschuwing waarmee u wordt gewaarschuwd als een gecontroleerde metriek de gemiddelde limiet van uw sleutel kluis een bepaald aantal keer binnen een bepaald tijds bereik overschrijdt. 

> [!IMPORTANT]
> Het kan Maxi maal tien minuten duren voordat zojuist geconfigureerde meldingen worden verzonden. 

### <a name="configure-an-action-group"></a>Een actie groep configureren 

Een actie groep is een Configureer bare lijst van meldingen en eigenschappen.

1. Meld u aan bij de Azure-portal
2. **Waarschuwingen** zoeken in het zoekvak
3. **Acties beheren** selecteren

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding die de knop acties beheren markeert.](../media/alert-6.png)

4. Selecteer **+ actie groep toevoegen**

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding met de knop + actie groep toevoegen.](../media/alert-7.png)

5. Kies het **actie type** voor uw actie groep. In dit voor beeld maken we een e-mail waarschuwing.

> [!div class="mx-imgBorder"]
> ![Scherm opname van de velden die nodig zijn om een actie groep toe te voegen.](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Scherm opname van wat er is vereist voor het toevoegen van een e-mail of SMS-bericht waarschuwing.](../media/alert-9.png)

6. Klik onder aan de pagina op **OK**. U hebt een actie groep gemaakt. 

Nu u een actie groep hebt geconfigureerd, worden de waarschuwings drempels voor de sleutel kluis geconfigureerd. 

### <a name="configure-alert-thresholds"></a>Waarschuwings drempels configureren 

1. Selecteer uw sleutel kluis resource in het Azure Portal en selecteer **waarschuwingen** onder **bewaking**

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding met de menu optie waarschuwingen in de sectie bewaking.](../media/alert-10.png)

2. **Nieuwe waarschuwings regel** selecteren

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding met de knop + nieuwe waarschuwings regel.](../media/alert-11.png)

3. Selecteer het bereik van uw waarschuwings regel. U kunt één kluis of meerdere selecteren. 

> [!IMPORTANT]
> Wanneer u meerdere kluizen selecteert voor het bereik van uw waarschuwingen, moeten alle geselecteerde kluizen zich in dezelfde regio bevinden. U moet afzonderlijke waarschuwings regels configureren voor kluizen in verschillende regio's. 

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding die laat zien hoe u een kluis kunt selecteren.](../media/alert-12.png)

4. Selecteer de voor waarden voor uw waarschuwingen. U kunt een van de volgende signalen kiezen en uw logica definiëren voor waarschuwingen. Het Key Vault Team raadt aan om de volgende waarschuwings drempels te configureren. 

    + Key Vault Beschik baarheid daalt onder 100% (statische drempel waarde)
    + Key Vault latentie is groter dan 500ms (statische drempel waarde) 
    + De algehele intensiteit van de kluis is groter dan 75% (statische drempel waarde) 
    + De totale kluis verzadiging is groter dan gemiddeld (dynamische drempel waarde)
    + Totaal aantal fout codes hoger dan gemiddeld (dynamische drempel waarde) 

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding die laat zien waar u de voor waarden voor waarschuwingen selecteert.](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Voor beeld 1: een statische waarschuwings drempel configureren voor latentie

Selecteer de **algehele latentie** van de service-API als de signaal naam
> [!div class="mx-imgBorder"]
> ![Scherm opname van de algemene service API-latentie signaal naam.](../media/alert-14.png)

Raadpleeg de volgende configuratie parameters.

+ De drempel waarde instellen op **statisch** 
+ Stel de operator in op **groter dan**
+ Stel het aggregatie type in op **gemiddeld**
+ Stel de drempel waarde in op **500**
+ Aggregatie periode instellen op **5 minuten**
+ Stel de evaluatie frequentie in op **1 minuut**
+ Selecteer **Gereed**  

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding die de geconfigureerde waarschuwings logica markeert.](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Voor beeld 2: een dynamische waarschuwings drempel configureren voor de verzadiging van de kluis 

Wanneer u een dynamische waarschuwing gebruikt, kunt u historische gegevens bekijken van de sleutel kluis die u hebt geselecteerd. Het blauwe gebied vertegenwoordigt het gemiddelde gebruik van uw sleutel kluis. Het rode gebied toont pieken die een waarschuwing zouden hebben geactiveerd, terwijl aan de waarschuwings configuratie andere criteria worden gegeven. De rode stippen tonen instanties van schendingen waarbij aan de criteria voor de waarschuwing is voldaan tijdens het samengevoegde tijd venster. U kunt instellen dat een waarschuwing wordt geactiveerd na een bepaald aantal schendingen binnen een ingestelde periode. Als u geen eerdere gegevens wilt opnemen, is er een optie voor het uitsluiten van oude gegevens hieronder in geavanceerde instellingen. 

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding met een grafiek van de algehele intensiteit van de kluis.](../media/alert-16.png)

Raadpleeg de volgende configuratie parameters.

+ De drempel waarde instellen op **dynamisch** 
+ Stel de operator in op **groter dan**
+ Stel het aggregatie type in op **gemiddeld**
+ De gevoeligheid van de drempel waarde instellen op **gemiddeld**
+ Aggregatie periode instellen op **5 minuten**
+ Stel de evaluatie frequentie in op **1 minuut**
+ **Optioneel** Geavanceerde instellingen configureren 
+ Selecteer **Gereed**

> [!div class="mx-imgBorder"]
> ![Scherm opname van Azure Portal](../media/alert-17.png)

5. De door u geconfigureerde actie groep toevoegen

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding die laat zien hoe u een actie groep kunt toevoegen.](../media/alert-18.png)

6. De waarschuwing inschakelen en een Ernst toewijzen

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding die laat zien waar de waarschuwing moet worden ingeschakeld en een ernst kan worden toegewezen.](../media/alert-19.png)

7. De waarschuwing maken 

### <a name="example-email-alert"></a>Voor beeld van een e-mail waarschuwing 

> [!div class="mx-imgBorder"]
> ![Scherm opname van de informatie die nodig is voor het configureren van een e-mail waarschuwing.](../media/alert-20.png)

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt nu een bewakings dashboard gemaakt en er zijn waarschuwingen geconfigureerd voor uw sleutel kluis. Zodra u alle bovenstaande stappen hebt gevolgd, ontvangt u e-mail waarschuwingen wanneer uw sleutel kluis voldoet aan de waarschuwings criteria die u hebt geconfigureerd. Hieronder kunt u een voorbeeld bekijken. Gebruik de hulpprogram ma's die u in dit artikel hebt ingesteld om de status van uw sleutel kluis actief te controleren. 


