---
title: Veelgestelde vragen over Internet analyse | Microsoft Docs
description: De veelgestelde vragen over Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74184257"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Veelgestelde vragen over Azure Internet Analyzer (preview-versie)

Dit is de veelgestelde vragen voor Azure Internet Analyzer: als u meer vragen hebt, gaat u naar het [Feedback forum](https://aka.ms/internetAnalyzerFeedbackForum) en plaatst u uw vraag. Wanneer een vraag regel matig wordt gesteld, voegen we deze toe aan dit artikel zodat het snel en eenvoudig kan worden gevonden.

## <a name="how-do-i-participate-in-the-preview"></a>Hoe kan ik deel nemen aan de preview?

De preview-versie is beschikbaar om klanten te selecteren. Ga als volgt te werk als u wilt deel nemen aan de preview-versie:

1. Meld u aan bij [Azure Portal](https://ms.portal.azure.com).
2. Navigeer naar de pagina **abonnementen** .
3. Klik op het Azure-abonnement dat u wilt gebruiken Internet Analyzer met.
4. Ga naar de instellingen van de **resource providers** voor het abonnement.
5. Zoek naar **micro soft. Network** en klik op de knop **registreren** (of **opnieuw registreren**).
![toegangsaanvraag](./media/ia-faq/request-preview-access.png)

6. [Goed keuring aanvragen](https://aka.ms/internetAnalyzerContact) door ons uw e-mail adres en de id van het Azure-abonnement op te geven dat is gebruikt om de toegangs aanvraag te maken.
7. Zodra uw aanvraag is goedgekeurd, ontvangt u een e-mail bevestiging en kunt u Internet Analyzer-resources maken/bijwerken of wijzigen op basis van het zojuist toegestane Azure-abonnement.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>Moet ik de client insluiten om een test uit te voeren?

Ja, de Internet Analyzer-client moet zijn geïnstalleerd in uw toepassing voor het verzamelen van metrische gegevens die specifiek zijn voor uw gebruikers. [Meer informatie over het installeren van de client.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Worden er kosten in rekening gebracht voor deelname aan de preview-versie?
Nee, Azure Internet Analyzer is gratis voor gebruik in de preview-versie. Er is geen service overeenkomst beschikbaar tijdens de preview-versie.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Welke scenario's is Internet Analyzer ontworpen om te verhelpen?

Internet Analyzer is ontworpen om u netwerk prestatie inzichten te geven op basis van uw gebruikers populatie. Internet Analyzer vergelijkt de prestaties van twee Internet-eind punten met behulp van uw afzonderlijke gebruikers populatie om de beste prestatie beslissingen te nemen voor uw gebruikers. Hoewel Internet Analyzer een groot aantal vragen kan beantwoorden, zijn enkele van de meest voorkomende:

* Wat is de invloed op prestaties van migratie naar de cloud? 
    * *Voorgestelde test: Aangepast (uw huidige on-premises infrastructuur) versus Azure (elk vooraf geconfigureerd eindpunt)*
* Wat is de waarde van het plaatsen van mijn gegevens aan de rand versus in een datacentrum? 
    *  *Voorgestelde test: Azure versus Azure Front Door, Azure versus Azure CDN van Microsoft*
* Wat is het prestatievoordeel van Azure Front Door?
    *  *Voorgestelde test: Aangepast/Azure/CDN versus Azure Front Door*
* Wat is het prestatievoordeel van Azure CDN van Microsoft? 
    *  *Voorgestelde test: Custom/ Azure/ AFD versus Azure CDN van Microsoft*
* Hoe verloopt Azure CDN van Microsoft? 
    *  *Voorgestelde test: Aangepast (ander CDN-eindpunt) versus Azure CDN van Microsoft*
* Wat is de beste cloud voor uw populatie eindgebruikers in elke regio? 
    *  *Voorgestelde test: Aangepast (andere cloudservice) versus Azure (elk vooraf geconfigureerd eindpunt)*

## <a name="which-tests-can-i-run-in-preview"></a>Welke tests kan ik uitvoeren in Preview?

Elke test die u in Internet Analyzer maakt, bestaat uit twee eind punten: eind punt A en eind punt B. Een van de volgende combi Naties kan worden uitgevoerd als tests:  
* Twee vooraf geconfigureerde eind punten,
* Een aangepast en één vooraf geconfigureerd eind punt of
* Twee [aangepaste eind punten](internet-analyzer-custom-endpoint.md) (een aangepast eind punt moet zich in azure bevinden).

De volgende vooraf geconfigureerde eind punten zijn beschikbaar tijdens de preview-versie:
* **Azure-regio's**
    * Brazil South
    * India - centraal
    * Central US
    * Azië - oost
    * VS - oost
    * Japan - west
    * Europa - noord
    * Zuid-Afrika - noord
    * Azië - zuidoost
    * VAE - noord
    * Verenigd Koninkrijk West  
    * Europa -west
    * VS - west
    * VS - west 2
* **Meerdere combinaties van Azure-regio's**
    * US - oost, Brazilië - zuid
    * US - oost, Azië - oost
    * Europa - west, Brazilië - zuid
    * Europa - west, Azië - zuidoost
    * Europa - west, AE - noord
    * US - west, US - oost
    * US - west, Europa - west
    * US - west, AE - noord
    * Europa - west, AE - noord, Azië - zuidoost
    * US - west, Europa - west, Azië - oost
    * US - west, Europa - noord, Azië - zuidoost, AE - noord, Zuid-Afrika - noord 
* **Azure + Azure Front Door** - geïmplementeerd op een of meer combinaties van Azure-regio's die hierboven worden vermeld
* **Azure + Azure CDN van Microsoft** - geïmplementeerd op één combinatie van Azure-regio's die hierboven worden vermeld
* **Azure + Azure Traffic Manager** - geïmplementeerd op meerdere combinaties van Azure-regio's die hierboven worden vermeld

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Hoe wijkt Internet Analyzer af van andere bewakings services die door Azure worden ondersteund?

Internet Analyzer helpt u inzicht te krijgen in de prestaties van uw eind gebruikers en helpt bij het nemen van beslissingen om de prestaties te verbeteren. Terwijl andere Azure-bewakings Programma's inzicht bieden in uw Azure-Services, is Internet Analyzer gericht op het meten van end-to-end Internet prestaties voor uw gebruikers.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Hoe worden meet gegevens verwerkt door Internet Analyzer?

Azure heeft [sterke beveiligings processen en voldoet aan een breed scala aan nalevings standaarden](https://azure.microsoft.com/support/trust-center/). Alleen u en uw specifieke team hebben toegang tot uw gegevens. Micro soft-mede werkers kunnen beperkte toegang hebben tot alleen onder specifieke beperkte omstandigheden met uw kennis. Het is versleuteld onderweg en in rust.

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van Internet Analyzer](internet-analyzer-overview.md)voor meer informatie.
