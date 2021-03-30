---
title: Capaciteits planning voor Service Fabric-apps
description: Hierin wordt beschreven hoe u het aantal reken knooppunten kunt identificeren dat vereist is voor een Service Fabric toepassing
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cd5a5c55ff873e4891ac63361d0c4a0b56d70109
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75377205"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Capaciteits planning voor Service Fabric toepassingen
In dit document leert u hoe u de hoeveelheid resources (Cpu's, RAM, schijf opslag) kunt schatten die u nodig hebt om uw Azure Service Fabric-toepassingen uit te voeren. Het is gebruikelijk dat uw resource vereisten na verloop van tijd veranderen. Normaal gesp roken hebt u weinig resources nodig bij het ontwikkelen/testen van uw service, en hebt u meer bronnen nodig tijdens de productie en wordt uw toepassing uitgebreid in populariteit. Wanneer u uw toepassing ontwerpt, bedenkt u door de vereisten voor de lange termijn en maakt u keuzen waarmee uw service kan worden geschaald om te voldoen aan de vraag naar een hoge klant.

 Wanneer u een Service Fabric cluster maakt, bepaalt u welke soorten virtuele machines (Vm's) het cluster vormen. Elke VM wordt geleverd met een beperkte hoeveelheid resources in de vorm van Cpu's (kernen en snelheid), netwerk bandbreedte, RAM-geheugen en schijf opslag. Naarmate uw service in de loop van de tijd toeneemt, kunt u een upgrade uitvoeren naar Vm's die meer resources bieden en/of meer Vm's toevoegen aan uw cluster. Om dit te doen, moet u uw service in eerste instantie ontwerpen, zodat deze kan profiteren van nieuwe Vm's die dynamisch worden toegevoegd aan het cluster.

Sommige services beheren weinig op geen gegevens op de virtuele machines zelf. Daarom moet de capaciteits planning voor deze services voornamelijk gericht zijn op de prestaties, wat betekent dat u de juiste Cpu's (kernen en snelheid) van de virtuele machines selecteert. Daarnaast moet u rekening houden met de band breedte van het netwerk, met inbegrip van hoe vaak er netwerk overdrachten plaatsvinden en hoeveel gegevens er worden overgedragen. Als uw service goed moet worden uitgevoerd als het service verbruik toeneemt, kunt u meer Vm's toevoegen aan het cluster en de netwerk aanvragen verdelen over alle virtuele machines.

Voor services die grote hoeveel heden gegevens op de Vm's beheren, moet de capaciteits planning voornamelijk op grootte worden gericht. Daarom moet u zorgvuldig rekening houden met de capaciteit van het RAM-geheugen en de schijf opslag van de virtuele machine. Het beheer systeem voor virtueel geheugen in Windows maakt schijf ruimte te zien als RAM-geheugen voor toepassings code. Daarnaast kunt u met de Service Fabric-runtime alleen dynamische gegevens in het geheugen houden en de koude gegevens naar schijf verplaatsen. Toepassingen kunnen dus meer geheugen gebruiken dan fysiek beschikbaar is op de virtuele machine. Als u meer RAM-geheugen hebt, neemt de prestaties toe, omdat de VM meer schijf ruimte in het RAM-geheugen kan bewaren. De virtuele machine die u selecteert, moet een schijf hebben die groot genoeg is voor de opslag van de gegevens die u op de virtuele machine wilt opslaan. Op dezelfde manier moet de virtuele machine voldoende RAM-geheugen hebben om u de gewenste prestaties te bieden. Als de gegevens van uw service na verloop van tijd toeneemt, kunt u meer Vm's toevoegen aan het cluster en de gegevens op alle Vm's partitioneren.

## <a name="determine-how-many-nodes-you-need"></a>Bepalen hoeveel knoop punten u nodig hebt
Door uw service te partitioneren, kunt u de gegevens van uw service schalen. Zie [partitioneren service Fabric](service-fabric-concepts-partitioning.md)voor meer informatie over partitioneren. Elke partitie moet binnen één virtuele machine passen, maar er kunnen meerdere (kleine) partities op één virtuele machine worden geplaatst. Als u meer kleine partities hebt, beschikt u dus over meer flexibiliteit dan met een paar grotere partities. Als de trans actie veel partities heeft, neemt Service Fabric overhead toe en kunt u geen trans acties uitvoeren tussen partities. Er is ook meer potentieel netwerk verkeer als uw service code regel matig toegang nodig heeft tot delen van gegevens die zich in verschillende partities bevinden. Bij het ontwerpen van uw service moet u deze voor-en nadelen aandachtig door nemen om te komen aan een efficiënte partitie strategie.

We gaan ervan uit dat uw toepassing één stateful service heeft met een archief grootte die u verwacht te groeien tot DB_Size GB in een jaar. U bereidt meer toepassingen (en partities) toe als u de groei buiten dat jaar hebt.  De replicatie factor (RF) waarmee het aantal replica's voor uw service wordt bepaald, is van invloed op de totale DB_Size. De totale DB_Size in alle replica's is de replicatie factor vermenigvuldigd met DB_Size.  Node_Size staat voor de schijf ruimte/het RAM per knoop punt dat u wilt gebruiken voor uw service. Voor de beste prestaties moet de DB_Size in het geheugen passen in het cluster en moet er een Node_Size dat rond het RAM-geheugen van de virtuele machine ligt, worden gekozen. Door een Node_Size toe te wijzen dat groter is dan de RAM-capaciteit, vertrouwt u op het wissel bestand dat wordt verschaft door de Service Fabric-runtime. Het is dus mogelijk dat uw prestaties niet optimaal zijn als uw volledige gegevens worden beschouwd als dynamisch (sinds de gegevens worden in-of uitgewisseld). Voor veel services waarbij slechts een fractie van de gegevens heet, is het echter rendabeler.

Het aantal knoop punten dat is vereist voor de maximale prestaties kan als volgt worden berekend:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Account voor groei
U kunt het aantal knoop punten berekenen op basis van de DB_Size waarvan u verwacht dat uw service groeit, naast de DB_Size waarmee u begon. Breid vervolgens het aantal knoop punten uit naarmate uw service groeit, zodat u niet meer dan het aantal knoop punten hoeft in te richten. Het aantal partities moet echter worden gebaseerd op het aantal knoop punten dat nodig is wanneer u uw service uitvoert met maximale groei.

Het is een goed idee om een aantal extra machines op elk gewenst moment beschikbaar te stellen, zodat u onverwachte pieken of storingen kunt afhandelen (bijvoorbeeld als een aantal Vm's omlaag gaat).  Hoewel de extra capaciteit moet worden bepaald door gebruik te maken van de verwachte pieken, is een begin punt het reserveren van een aantal extra Vm's (5-10 procent extra).

In de vorige stap wordt ervan uitgegaan dat één stateful service. Als u meer dan één stateful service hebt, moet u de DB_Size die aan de andere services zijn gekoppeld, toevoegen aan de vergelijking. U kunt ook het aantal knoop punten afzonderlijk voor elke stateful service berekenen.  Uw service heeft mogelijk replica's of partities die niet zijn gebalanceerd. Houd er wel van uit dat partities ook meer gegevens kunnen bevatten dan andere. Zie voor meer informatie over partitionering het [artikel partitioneren over best practices](service-fabric-concepts-partitioning.md). De voor gaande vergelijking is echter partitie en replica neutraal, omdat Service Fabric ervoor zorgt dat de replica's op een geoptimaliseerde manier worden verdeeld over de knoop punten.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Een werk blad gebruiken voor de kosten berekening
We gaan nu echte getallen in de formule opnemen. Een [voor beeld van een werk blad](https://github.com/Azure/service-fabric/raw/master/docs_resources/SF_VM_Cost_calculator-NEW.xlsx) laat zien hoe de capaciteit van een toepassing met drie typen gegevens objecten kan worden gepland. Voor elk object zijn de grootte en het aantal objecten waarnaar wordt verwacht. Daarnaast selecteren we hoeveel replica's van elk object type er moeten zijn. Het werk blad berekent de totale hoeveelheid geheugen die in het cluster moet worden opgeslagen.

Vervolgens voeren we een VM-grootte en maandelijkse kosten in. Op basis van de grootte van de virtuele machine vertelt het werk blad het minimale aantal partities dat u moet gebruiken om uw gegevens te splitsen zodat deze fysiek passen op de knoop punten. U kunt een groter aantal partities hebben om te voldoen aan de specifieke berekenings-en netwerk verkeer vereisten van uw toepassing. In het werk blad ziet u het aantal partities dat het beheer van de gebruikers profiel objecten beheert, verhoogd van een tot zes.

Op basis van deze informatie geeft het werk blad nu aan dat u alle gegevens fysiek kunt ophalen met de gewenste partities en replica's in een cluster van 26 knoop punten. Dit cluster zou echter zeer groot worden verpakt, dus u wilt misschien dat sommige extra knoop punten voor knooppunt fouten en upgrades kunnen worden aangepast. In het werk blad wordt ook weer gegeven dat meer dan 57 knoop punten een extra waarde hebben, omdat u lege knoop punten zou hebben. Het kan ook zijn dat u de bovenstaande 57-knoop punten wilt bezoeken om knooppunt fouten en-upgrades te kunnen verwerken. U kunt het werk blad aanpassen aan de specifieke behoeften van uw toepassing.   

![Spread sheet voor kosten berekening][Image1]

## <a name="next-steps"></a>Volgende stappen
Bekijk het [partitioneren van service Fabric Services][10] voor meer informatie over het partitioneren van uw service.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
