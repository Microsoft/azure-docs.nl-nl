---
title: Veelgestelde vragen over VM Insights (GA) | Microsoft Docs
description: VM Insights is een oplossing in azure met een combi natie van de status-en prestatie bewaking van het Azure VM-besturings systeem, evenals het automatisch detecteren van toepassings onderdelen en afhankelijkheden met andere resources en het toewijzen van de communicatie tussen de toepassingen. In dit artikel vindt u antwoorden op veelgestelde vragen over de GA-versie.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: fbef73bfe8058110277b200b8c4091fcde110c04
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031863"
---
# <a name="vm-insights-generally-available-ga-frequently-asked-questions"></a>VM Insights algemeen beschikbaar (GA) Veelgestelde vragen
Deze veelgestelde vragen over algemene Beschik baarheid omvatten wijzigingen die zijn aangebracht in Q4 2019 en Q1 2020 van voor bereiding op GA.

## <a name="updates-for-vm-insights"></a>Updates voor VM Insights
We hebben in januari 2020 een nieuwe versie van de VM-inzichten uitgebracht, vóór onze GA aankondiging. Klanten die VM Insights inschakelen, ontvangen nu de GA-versie, maar bestaande klanten die gebruikmaken van de versie van VM Insights van Q4 2019 en eerder, worden gevraagd om bij te werken. Deze veelgestelde vragen bieden richt lijnen voor het uitvoeren van een upgrade op schaal als u grote implementaties in meerdere werk ruimten hebt.


Met deze upgrade worden de prestatie gegevens van de VM-inzichten opgeslagen in dezelfde *InsightsMetrics* -tabel als [container Insights](../containers/container-insights-overview.md), waardoor het gemakkelijker wordt om de twee gegevens sets op te vragen. U kunt ook meer diverse gegevens sets opslaan die niet kunnen worden opgeslagen in de tabel die eerder is gebruikt. 

Onze prestatie weergaven gebruiken nu de gegevens die we opslaan in de tabel *InsightsMetrics* .  Als u nog niet hebt bijgewerkt voor het gebruik van de meest recente VMInsights-oplossing in uw werk ruimte, worden er geen gegevens meer weer gegeven in uw grafieken.  U kunt een upgrade uitvoeren van de pagina **aan de slag** zoals hieronder wordt beschreven.


## <a name="what-is-changing"></a>Wat wordt er gewijzigd?
We hebben een nieuwe oplossing met de naam VMInsights, die meer mogelijkheden bevat voor het verzamelen van gegevens, samen met een nieuwe locatie voor het opslaan van deze gegevens in uw Log Analytics-werk ruimte. 

In het verleden hebben we de ServiceMap-oplossing ingeschakeld in uw werk ruimte en de prestatie meter items in uw Log Analytics-werk ruimte om de gegevens naar de *prestatie* tabel te verzenden. Deze nieuwe oplossing verzendt de gegevens naar een tabel met de naam *InsightsMetrics* die ook wordt gebruikt door container Insights. Met dit tabel schema kunnen we meer metrische gegevens en servicegegevens sets opslaan die niet compatibel zijn met de indeling van de *prestatie* tabel.

We hebben onze prestatie grafieken bijgewerkt voor het gebruik van de gegevens die we opslaan in de *InsightsMetrics* -tabel. U kunt een upgrade uitvoeren om de tabel *InsightsMetrics* te gebruiken op de pagina **aan** de slag zoals hieronder wordt beschreven.


## <a name="how-do-i-upgrade"></a>Hoe kan ik upgrade?
Wanneer een upgrade van een Log Analytics-werk ruimte naar de nieuwste versie van Azure Monitor naar Vm's wordt uitgevoerd, wordt de afhankelijkheids agent bijgewerkt op elke virtuele machine die aan die werk ruimte is gekoppeld. Elke VM die moet worden bijgewerkt, wordt geïdentificeerd op het tabblad **aan de slag** in de Azure portal van de virtuele machine. Wanneer u ervoor kiest om een virtuele machine te upgraden, wordt de werk ruimte voor die VM bijgewerkt, samen met eventuele andere virtuele machines die aan die werk ruimte zijn gekoppeld. U kunt één virtuele machine of meerdere Vm's, resource groepen of abonnementen selecteren. 

Gebruik de volgende opdracht om een werk ruimte bij te werken met Power shell:

```PowerShell
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Wat moet ik doen met de prestatie meter items in mijn werk ruimte als ik de VMInsights-oplossing Installeer?

De vorige methode voor het inschakelen van VM Insights gebruikte prestatie meter items in uw werk ruimte. In de huidige versie worden deze gegevens opgeslagen in een tabel met de naam `InsightsMetrics` . U kunt ervoor kiezen om deze prestatie meter items in uw werk ruimte uit te scha kelen als u ze niet meer nodig hebt. 

>[!NOTE]
>Als u waarschuwings regels hebt die naar deze prestatie meter items in de `Perf` tabel verwijzen, moet u deze bijwerken om te verwijzen naar nieuwe gegevens die zijn opgeslagen in de `InsightsMetrics` tabel. Raadpleeg de documentatie voor voorbeeld logboek query's die u kunt gebruiken om deze tabel te raadplegen.
>

Als u ervoor kiest om de prestatie meter items ingeschakeld te laten, wordt u gefactureerd voor de gegevens die zijn opgenomen en opgeslagen in de `Perf` tabel op basis van [log Analytics prijzen [( https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="how-will-this-change-affect-my-alert-rules"></a>Wat is de invloed van deze wijziging op mijn waarschuwings regels?

Als u [logboek waarschuwingen](../alerts/alerts-unified-log.md) hebt gemaakt die query's uitvoeren op de `Perf` tabel doel prestatie meter items die zijn ingeschakeld in de werk ruimte, moet u deze regels bijwerken om `InsightsMetrics` in plaats daarvan naar de tabel te verwijzen. Deze richt lijnen gelden ook voor zoek regels in Logboeken met `ServiceMapComputer_CL` en `ServiceMapProcess_CL` , omdat deze gegevens sets worden verplaatst naar `VMComputer` en `VMProcess` tabellen.

Deze veelgestelde vragen worden bijgewerkt en onze documentatie bevat voor beelden van waarschuwings regels voor logboek zoeken in de gegevens sets die worden verzameld.

## <a name="how-will-this-change-affect-my-bill"></a>Wat is de invloed van deze wijziging op mijn factuur?

Facturering is nog steeds gebaseerd op gegevens die zijn opgenomen en bewaard in uw Log Analytics-werk ruimte.

De prestaties van het computer niveau die we verzamelen, zijn hetzelfde als die van de gegevens die we in de tabel hebben opgeslagen `Perf` en kosten ongeveer hetzelfde bedrag.

## <a name="what-if-i-only-want-to-use-service-map"></a>Wat moet ik doen als ik alleen Servicetoewijzing wil gebruiken?

Dat is prima. U ziet de aanwijzingen in de Azure Portal bij het weer geven van VM Insights over de aanstaande update. Zodra de release is uitgebracht, wordt u gevraagd om u bij te werken naar de nieuwe versie. Als u liever de [Maps](vminsights-maps.md) -functie wilt gebruiken, kunt u ervoor kiezen om de Maps-functie niet te upgraden en door te gaan met het gebruik van de toewijzingen in VM Insights en de servicetoewijzing-oplossing die wordt geopend vanuit uw werk ruimte of dashboard tegel.

Als u ervoor hebt gekozen om de prestatie meter items in uw werk ruimte hand matig in te scha kelen, kunt u de gegevens in een aantal van de prestatie diagrammen weer geven vanuit Azure Monitor. Zodra de nieuwe oplossing is uitgebracht, worden de prestatie grafieken bijgewerkt om de gegevens op te vragen die in de tabel zijn opgeslagen `InsightsMetrics` . Als u gegevens uit die tabel in deze grafieken wilt zien, moet u een upgrade uitvoeren naar de nieuwe versie van VM Insights.

De wijzigingen voor het verplaatsen van gegevens van `ServiceMapComputer_CL` en `ServiceMapProcess_CL` zijn van invloed op zowel servicetoewijzing als virtuele machine inzichten, dus u moet deze update nog steeds plannen.

Als u ervoor hebt gekozen om geen upgrade uit te voeren naar de **VMInsights** -oplossing, zullen we oudere versies van onze prestatie werkmappen blijven leveren die verwijzen naar de gegevens in de `Perf` tabel.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Worden de Servicetoewijzing gegevens sets ook opgeslagen in InsightsMetrics?

De gegevens sets worden niet gedupliceerd als u beide oplossingen gebruikt. Beide aanbiedingen delen de gegevens sets die worden opgeslagen in `VMComputer` (voorheen ServiceMapComputer_CL), `VMProcess` (voorheen ServiceMapProcess_CL), `VMConnection` en `VMBoundPort` tabellen om de kaart gegevens sets op te slaan die we verzamelen.  

In de tabel worden de `InsightsMetrics` gegevens sets van de VM, het proces en de service opgeslagen die we verzamelen en alleen worden ingevuld als u gebruikmaakt van VM Insights en de oplossing voor de VM-inzichten. Met de Servicetoewijzing oplossing worden gegevens in de tabel niet verzameld of opgeslagen `InsightsMetrics` .

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Worden er dubbele kosten in rekening gebracht als ik de Servicetoewijzing-en VMInsights-oplossingen in mijn werk ruimte heb?

Nee, de twee oplossingen delen de kaart gegevens sets die worden opgeslagen in `VMComputer` (voorheen ServiceMapComputer_CL), `VMProcess` (voorheen ServiceMapProcess_CL), `VMConnection` en `VMBoundPort` . Als u beide oplossingen in uw werk ruimte hebt, worden deze gegevens niet dubbel in rekening gebracht.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Als ik de Servicetoewijzing-of VMInsights-oplossing Verwijder, worden mijn gegevens verwijderd?

Nee, de twee oplossingen delen de kaart gegevens sets die worden opgeslagen in `VMComputer` (voorheen ServiceMapComputer_CL), `VMProcess` (voorheen ServiceMapProcess_CL), `VMConnection` en `VMBoundPort` . Als u een van de oplossingen verwijdert, ziet u in deze gegevens sets dat er nog steeds een oplossing aanwezig is die gebruikmaakt van de gegevens en deze blijft in de werk ruimte Log Analytics. U moet beide oplossingen uit uw werk ruimte verwijderen om ervoor te zorgen dat de gegevens erin worden verwijderd.

## <a name="health-feature-is-in-limited-public-preview"></a>Health-functie heeft een beperkte open bare preview

We hebben een groot aantal fantastische feedback ontvangen van klanten over onze VM-status functie ingesteld. Er is asignificant belang stelling voor deze functie en de mogelijkheden voor het ondersteunen van bewakings werk stromen. We zijn van plan een reeks wijzigingen aan te brengen om functionaliteit toe te voegen en de ontvangen feedback te verhelpen. 

Om de gevolgen van deze wijzigingen aan nieuwe klanten te beperken, hebben we deze functie verplaatst naar een **beperkte open bare preview**. Deze update is opgetreden in oktober 2019.

We zijn van plan deze status functie opnieuw te starten in 2020, nadat VM Insights in GA is.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Hoe krijgen bestaande klanten toegang tot de status functie?

Bestaande klanten die gebruikmaken van het Health-onderdeel, hebben nog steeds toegang tot het, maar ze worden niet aangeboden aan nieuwe klanten.  

Als u de functie wilt openen, kunt u de volgende functie vlag toevoegen `feature.vmhealth=true` aan de Azure Portal URL [https://portal.azure.com](https://portal.azure.com) . Voor beeld `https://portal.azure.com/?feature.vmhealth=true` .

U kunt deze korte URL ook gebruiken, waarmee de functie vlag automatisch wordt ingesteld: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview) .

Als een bestaande klant kunt u de status functie blijven gebruiken op virtuele machines die zijn verbonden met een bestaande installatie van een werk ruimte met de status functionaliteit.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Ik gebruik nu VM-status met één omgeving en wil deze implementeren in een nieuw abonnement

Als u een bestaande klant bent die gebruikmaakt van de Health-functie en deze wilt gebruiken voor een nieuwe implementatie, neemt u contact met ons op vminsights@microsoft.com om instructies aan te vragen.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg [DEPLOY machine Insights](./vminsights-enable-overview.md)voor meer informatie over de vereisten en methoden die u helpen bij het bewaken van uw virtuele machines.
