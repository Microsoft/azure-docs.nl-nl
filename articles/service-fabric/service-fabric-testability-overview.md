---
title: Overzicht van fout analyse service
description: In dit artikel wordt de fout analyse service in Service Fabric beschreven om fouten te voor komen en test scenario's uit te voeren op basis van uw services.
ms.topic: conceptual
ms.date: 06/15/2017
ms.openlocfilehash: 4e879b0b39d58f115b4661d47cf2ae564ba14e5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91531374"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Inleiding tot de fout analyse service
De fout analyse service is ontworpen voor het testen van services die zijn gebouwd op Microsoft Azure Service Fabric. Met de fout analyse service kunt u zinvolle fouten tegen komen en volledige test scenario's uitvoeren voor uw toepassingen. Deze fouten en scenario's oefenen en valideren de talrijke statussen en overgangen die gedurende de levens duur van een service worden ervaren, op een gecontroleerde, veilige en consistente manier.

Acties zijn de afzonderlijke fouten die gericht zijn op een service om deze te testen. Een service ontwikkelaar kan deze gebruiken als bouw stenen voor het schrijven van gecompliceerde scenario's. Bijvoorbeeld:

* Start een knoop punt opnieuw voor het simuleren van een wille keurig aantal situaties waarbij een machine of VM opnieuw wordt opgestart.
* Verplaats een replica van uw stateful service om taak verdeling, failover of toepassings upgrade te simuleren.
* Aanroepen van quorum verlies op een stateful service voor het maken van een situatie waarin schrijf bewerkingen niet kunnen worden voortgezet omdat er onvoldoende ' back-up '-of ' secundaire ' replica's zijn om nieuwe gegevens te accepteren.
* Gegevens verlies op een stateful service aanroepen om een situatie te maken waarin de status van alle in-Memory volledig is gewist.

Scenario's zijn complexe bewerkingen die bestaan uit een of meer acties. De fout analyse service biedt twee ingebouwde volledige scenario's:

* Chaos-scenario
* Failover-scenario

## <a name="testing-as-a-service"></a>Testen als een service
De fout analyse service is een Service Fabric systeem service die automatisch wordt gestart met een Service Fabric cluster. Deze service fungeert als host voor fout injectie, uitvoering van test scenario en status analyse. 

![Fout analyse service][0]

Wanneer een fout actie of test scenario wordt gestart, wordt een opdracht verzonden naar de fout analyse service om de fout actie of het test scenario uit te voeren. De fout analyse service is stateful, zodat er op betrouw bare wijze fouten en scenario's kunnen worden uitgevoerd en de resultaten worden gevalideerd. Een langlopend test scenario kan bijvoorbeeld betrouwbaar worden uitgevoerd door de fout analyse service. En omdat tests in het cluster worden uitgevoerd, kan de service de status van het cluster en uw services controleren om meer gedetailleerde informatie over fouten te bieden.

## <a name="testing-distributed-systems"></a>Gedistribueerde systemen testen
Service Fabric maakt het eenvoudiger om gedistribueerde schaal bare toepassingen te schrijven en te beheren. Met de fout analyse service wordt een gedistribueerde toepassing op dezelfde manier getest. Er zijn drie belang rijke problemen die moeten worden opgelost tijdens het testen:

1. Simuleren/genereren van fouten die kunnen optreden in praktijk scenario's: een van de belang rijke aspecten van Service Fabric is dat gedistribueerde toepassingen kunnen worden hersteld van verschillende fouten. Als u echter wilt testen of de toepassing van deze fouten kan worden hersteld, moet u een mechanisme hebben voor het simuleren/genereren van deze praktijk fouten in een gecontroleerde test omgeving.
1. De mogelijkheid om gecorreleerde fouten te genereren: basis fouten in het systeem, zoals netwerk fouten en computer fouten, kunnen eenvoudig afzonderlijk worden geproduceerd. Het genereren van een groot aantal scenario's die zich in de praktijk kunnen voordoen als gevolg van de interactie van deze afzonderlijke fouten is niet-trivial.
1. Geïntegreerde ervaring op verschillende niveaus van ontwikkeling en implementatie: er zijn veel fout injectie systemen waarop verschillende soorten fouten kunnen worden uitgevoerd. De ervaring in al deze situaties is echter slecht wanneer u overstapt van ontwikkel scenario's met één box, om dezelfde tests uit te voeren in grote test omgevingen, om ze te gebruiken voor tests in de productie omgeving.

Hoewel er veel mechanismen zijn om deze problemen op te lossen, een systeem dat hetzelfde doet als de vereiste garanties: alle van een ontwikkel omgeving met één vak om te testen in productie clusters, ontbreekt. De fout analyse service helpt de ontwikkel aars van toepassingen te concentreren op het testen van hun bedrijfs logica. De fout analyse service biedt alle mogelijkheden die nodig zijn om de interactie van de service met het onderliggende gedistribueerde systeem te testen.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Scenario's voor praktijk fouten simuleren/genereren
Als u de robuustheid van een gedistribueerd systeem wilt testen op fouten, moet er een mechanisme worden gegenereerd voor het genereren van fouten. In theorie is het genereren van een storing, zoals een knoop punt, gemakkelijk te vinden, maar op dezelfde reeks consistentie problemen die Service Fabric probeert op te lossen. Als we bijvoorbeeld een knoop punt willen afsluiten, is de vereiste werk stroom het volgende:

1. Geef vanuit de client een aanvraag voor het afsluiten van het knoop punt op.
1. Verzend de aanvraag naar het juiste knoop punt.
   
    a. Als het knoop punt niet wordt gevonden, zou het mislukken moeten zijn.
   
    b. Als het knoop punt wordt gevonden, moet dit alleen worden geretourneerd als het knoop punt is afgesloten.

Als u de fout van een test perspectief wilt controleren, moet de test er zeker van zijn dat de fout daad werkelijk optreedt wanneer deze fout optreedt. De garantie dat Service Fabric biedt, is dat het knoop punt wordt uitgeschakeld of al was ingedrukt toen de opdracht het knoop punt bereikte. In beide gevallen moet de test op de juiste manier in staat zijn om de status te controleren en goed te kunnen slagen of mislukken tijdens de validatie. Een systeem dat buiten Service Fabric wordt geïmplementeerd om dezelfde set fouten uit te voeren, zou kunnen oplopen over veel netwerk-, hardware-en software problemen, waardoor het niet mogelijk is om de voor gaande garanties te bieden. In aanwezigheid van de problemen die eerder zijn vermeld, configureert Service Fabric de cluster status opnieuw om de problemen te omzeilen, waardoor de fout analyse service nog steeds de juiste set garanties kan bieden.

### <a name="generating-required-events-and-scenarios"></a>Vereiste gebeurtenissen en scenario's genereren
Hoewel het simuleren van een Real-World-fout consequent moeilijk is om te beginnen met, is de mogelijkheid om gecorreleerde fouten te genereren, zelfs lastiger. Zo vindt er een gegevens verlies plaats in een stateful persistente service wanneer de volgende zaken zich voordoen:

1. Er wordt alleen een schrijf quorum van de replica's gedetecteerd bij replicatie. Alle secundaire replica's hebben een vertraging achter de primaire replica.
1. Het schrijf quorum gaat omlaag vanwege de replica's (als gevolg van een code pakket of een knoop punt).
1. Er kan geen back-up worden gemaakt van het schrijf quorum, omdat de gegevens voor de replica's verloren zijn gegaan (vanwege een beschadigde schijf of het opnieuw maken van een machine).

Deze gecorreleerde fouten doen zich voor in de echte wereld, maar niet zo vaak als afzonderlijke fouten. De mogelijkheid om deze scenario's te testen voordat deze in de productie omgeving plaatsvindt, is essentieel. Nog belang rijker is dat u deze scenario's kunt simuleren met productie werkbelastingen in gecontroleerde omstandigheden (in het midden van de dag met alle technici op het dek). Dat is veel beter dan het voor de eerste keer in de productie om 2:00 uur.

### <a name="unified-experience-across-different-environments"></a>Geïntegreerde ervaring in verschillende omgevingen
Het is gebruikelijk om drie verschillende sets ervaringen te maken, één voor de ontwikkel omgeving, één voor testen en één voor productie. Het model is:

1. In de ontwikkel omgeving produceert u status overgangen die de eenheids tests van afzonderlijke methoden toestaan.
1. In de test omgeving worden er fouten gegenereerd om end-to-end-tests toe te staan die verschillende scenario's voor storingen uitoefenen.
1. Bewaar de productie omgeving pristine om onnatuurlijke storingen te voor komen en ervoor te zorgen dat er zeer snelle menselijke reacties op fouten zijn.

In Service Fabric wordt de fout analyse service Voorst Ellen om dit te omzeilen en dezelfde methodologie te gebruiken van de ontwikkelaars omgeving tot de productie. Er zijn twee manieren om dit te doen:

1. Gebruik de fout analyse service-Api's van een omgeving met één doos voor het produceren van gecontroleerde storingen.
1. Om het cluster een koorts te geven dat de automatische inductie van fouten veroorzaakt, gebruikt u de fout analyse service om automatische fouten te genereren. Als u de frequentie van fouten via de configuratie beheert, kan dezelfde service in verschillende omgevingen anders worden getest.

Met Service Fabric, hoewel de schaal van storingen in de verschillende omgevingen anders zou zijn, zijn de daad werkelijke mechanismen identiek. Dit biedt een veel snellere pijp lijn voor het implementeren van code en de mogelijkheid om de services te testen onder praktijk belasting.

## <a name="using-the-fault-analysis-service"></a>De fout analyse service gebruiken
**C#**

De functies van de fout analyse service bevinden zich in de naam ruimte System. fabric in het NuGet-pakket van micro soft. ServiceFabric. Als u de fout Analysis Service-functies wilt gebruiken, neemt u het nuget-pakket op als referentie in uw project.

**PowerShell**

Als u Power shell wilt gebruiken, moet u de Service Fabric SDK installeren. Nadat de SDK is geïnstalleerd, wordt de Power shell-module ServiceFabric automatisch geladen zodat u deze kunt gebruiken.

## <a name="next-steps"></a>Volgende stappen
Voor het maken van echt Cloud-Scale-Services is het van essentieel belang om ervoor te zorgen dat de services voor en na de implementatie een grote situatie in de praktijk kunnen opvangen. In de wereld van Services is de mogelijkheid om snel te innoveren en code naar productie te verplaatsen, heel belang rijk. Met de service fout analyse kunnen service ontwikkelaars nauw keurig dat doen.

Begin met het testen van uw toepassingen en services met behulp van de ingebouwde [test scenario's](service-fabric-testability-scenarios.md)of maak uw eigen test scenario's met behulp van de [fout acties](service-fabric-testability-actions.md) van de fout analyse service.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
