---
title: Jacht-mogelijkheden in azure-Sentinel | Microsoft Docs
description: Gebruik de ingebouwde jacht query's van Azure Sentinel om u te helpen bij het bepalen van de juiste vragen om problemen op te sporen in uw gegevens.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: yelevin
ms.openlocfilehash: b88185a09e69b1c7bd6c6c58b8e22190b4a2e394
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576761"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Zoeken naar bedreigingen met Azure Sentinel

Als u een onderzoeker bent die proactief wil zijn om te kijken naar beveiligings Risico's, worden de Azure Sentinel krachtige zoek-en query hulpprogramma's gebruikt om beveiligings Risico's te vinden in de gegevens bronnen van uw organisatie. Maar uw systemen en beveiligings apparaten genereren bergen van gegevens die moeilijk kunnen worden geparseerd en gefilterd in betekenis volle gebeurtenissen. Om beveiligings analisten proactief te bieden voor nieuwe afwijkingen die niet door uw beveiligings-apps zijn gedetecteerd, kunt u met de ingebouwde jacht-query's van Azure Sentinel de juiste vragen stellen om problemen op te sporen in de gegevens die u al in uw netwerk hebt. 

Eén ingebouwde query biedt bijvoorbeeld gegevens over de meest voorkomende processen die op uw infra structuur worden uitgevoerd. u wilt geen waarschuwing over elke keer dat ze worden uitgevoerd, maar het kan ook een goed idee zijn, maar het is aan te raden om de query bij te werken om te zien of er iets ongebruikelijk is. 



Met Azure Sentinel-jacht kunt u profiteren van de volgende mogelijkheden:

- Ingebouwde query's: om aan de slag te gaan, biedt een start pagina vooraf geladen query voorbeelden die zijn ontworpen om u op weg te helpen en u vertrouwd te raken met de tabellen en de query taal. Deze ingebouwde jacht-query's worden continu ontwikkeld door micro soft-beveiligings onderzoekers, het toevoegen van nieuwe query's en het afstemmen van bestaande query's om u een ingangs punt te bieden voor nieuwe detecties en erachter te komen waar u kunt beginnen met het starten van nieuwe aanvallen. 

- Krachtige query taal met IntelliSense: gebouwd op basis van een query taal die u de flexibiliteit biedt die u nodig hebt om naar het volgende niveau te gaan.

- Uw eigen blad wijzers maken: tijdens het jacht-proces kunt u komen over overeenkomsten of bevindingen, Dash boards of activiteiten die er vreemd of verdacht uitzien. Als u deze items wilt markeren zodat u ze in de toekomst weer kunt gebruiken, gebruikt u de bladwijzer functionaliteit. Met blad wijzers kunt u items later opslaan, zodat u deze kunt gebruiken om een incident voor onderzoek te maken. Zie [blad wijzers gebruiken in jacht](hunting.md)voor meer informatie over blad wijzers.
- Notitie blokken gebruiken om onderzoek te automatiseren: Notebooks zijn bijvoorbeeld stapsgewijze playbooks die u kunt maken om de stappen van een onderzoek en jacht te door lopen.  Notebooks bevatten alle jacht-stappen in een herbruikbare Playbook die kunnen worden gedeeld met anderen in uw organisatie. 
- Query's uitvoeren op de opgeslagen gegevens: de gegevens zijn toegankelijk in de tabellen die u wilt doorzoeken. U kunt bijvoorbeeld een query uitvoeren op proces maken, DNS-gebeurtenissen en veel andere gebeurtenis typen.

- Koppelingen naar Community: Maak gebruik van de kracht van de grotere Community om extra query's en gegevens bronnen te vinden.
 
## <a name="get-started-hunting"></a>Aan de slag met jacht

1. Klik in de Azure-Sentinel-Portal op **jacht**.
  ![Azure Sentinel start-jacht](media/tutorial-hunting/hunting-start.png)

2. Wanneer u de pagina **jacht** opent, worden alle Zoek opdrachten in één tabel weer gegeven. De tabel bevat een lijst met alle query's die zijn geschreven door het team van beveiligings analisten van micro soft, evenals de aanvullende query's die u hebt gemaakt of gewijzigd. Elke query bevat een beschrijving van de functie voor het zoeken naar en de soort gegevens waarop deze wordt uitgevoerd. Deze sjablonen worden gegroepeerd op hun verschillende tactiek-de pictogrammen aan de rechter kant categoriseren het type bedreiging, zoals initiële toegang, persistentie en exfiltration. U kunt deze jacht-query sjablonen filteren met behulp van een van de velden. U kunt elke query opslaan in uw favorieten. Als u een query opslaat in uw favorieten, wordt de query automatisch uitgevoerd telkens wanneer de pagina **jacht** wordt geopend. U kunt uw eigen zoek opdracht maken of een bestaande zoek opdracht sjabloon klonen en aanpassen. 
 
2. Klik op **query uitvoeren** op de pagina met query Details van de jacht om query's uit te voeren zonder de pagina jacht te verlaten.  Het aantal overeenkomsten wordt weer gegeven in de tabel. Bekijk de lijst met jacht-query's en de bijbehorende overeenkomsten. Bekijk in welke fase van de Kill-keten de overeenkomst is gekoppeld.

3. Voer een snelle controle uit van de onderliggende query in het detail venster van de query of klik op **query resultaten weer geven** om de query te openen in log Analytics. Controleer aan de hand van de overeenkomsten voor de query.

4.    Klik op de rij en selecteer **blad wijzer toevoegen** om de rijen toe te voegen die u wilt onderzoeken. u kunt dit doen voor alles wat er verdacht uitziet. 

5. Ga vervolgens terug naar de hoofd pagina van de **jacht** en klik op het tabblad **blad wijzers** om alle verdachte activiteiten weer te geven. 

6. Selecteer een blad wijzer en klik vervolgens op **onderzoeken** om de ervaring van het onderzoek te openen. U kunt de blad wijzers filteren. Als u bijvoorbeeld een campagne onderzoekt, kunt u een tag voor de campagne maken en vervolgens alle blad wijzers filteren op basis van de campagne.

1. Nadat u hebt ontdekt welke zoek opdracht hoge waarde-inzichten biedt in mogelijke aanvallen, kunt u ook aangepaste detectie regels maken op basis van uw query en die inzichten als waarschuwingen voor uw beveiligings incident responders.

 

## <a name="query-language"></a>Querytaal 

Jacht in azure Sentinel is gebaseerd op de Kusto-query taal. Zie [Query Language Reference (Engelstalig](../azure-monitor/logs/get-started-queries.md)) voor meer informatie over de query taal en ondersteunde Opera tors.

## <a name="public-hunting-query-github-repository"></a>GitHub-opslag plaats voor open bare jacht-query's

Bekijk de [Zoek opslagplaats](https://github.com/Azure/Orion)van de query. Deel bijdragen en gebruik voorbeeld query's die worden gedeeld door onze klanten.

 

## <a name="sample-query"></a>Voorbeeldquery

Een typische query begint met een tabel naam gevolgd door een reeks Opera tors gescheiden door \| .

In het bovenstaande voor beeld begint u met de tabel naam SecurityEvent en voegt u waar nodig gepipede elementen toe.

1. Definieer een tijd filter om alleen records van de afgelopen zeven dagen te controleren.

2. Een filter toevoegen in de query om alleen gebeurtenis-ID 4688 weer te geven.

3. Voeg in de query op de CommandLine een filter toe dat alleen exemplaren van cscript.exe bevat.

4. Projecteer alleen de kolommen die u wilt verkennen en beperk de resultaten tot 1000 en klik op **query uitvoeren**.
5. Klik op het groene drie hoekje en voer de query uit. U kunt de query testen en uitvoeren om te zoeken naar afwijkend gedrag.

## <a name="useful-operators"></a>Nuttige operatoren

De query taal is krachtig en er zijn veel beschik bare Opera tors. sommige nuttige Opera tors worden hier weer gegeven:

**where** : een tabel filteren op de subset rijen die voldoen aan een predikaat.

**samenvatten** : een tabel genereren die de inhoud van de invoer tabel samenvoegt.

**lid worden** van de rijen van twee tabellen samen voegen om een nieuwe tabel te maken op basis van de waarden van de opgegeven kolom (men) in elke tabel.

**Count** : retourneert het aantal records in de invoer Recordset.

**Top** : retourneert de eerste N records, gesorteerd op de opgegeven kolommen.

**Limit** : retourneert naar het opgegeven aantal rijen.

**project** : Selecteer de kolommen die u wilt opnemen, de naam ervan wijzigen of verwijderen, en nieuwe berekende kolommen invoegen.

**uitbreiden** : berekende kolommen maken en toevoegen aan de resultatenset.

**makenset** : retourneert een dynamische (JSON)-matrix van de set afzonderlijke waarden die de expressie in de groep neemt

**Zoek** rijen die overeenkomen met een predikaat in een set tabellen.

## <a name="save-a-query"></a>Een query opslaan

U kunt een query maken of wijzigen en deze opslaan als uw eigen query of deze delen met gebruikers die zich in dezelfde Tenant bevinden.

   ![Query opslaan](./media/tutorial-hunting/save-query.png)

Een nieuwe zoek opdracht maken:

1. Klik op **nieuwe query** en selecteer **Opslaan**.
2. Vul alle lege velden in en selecteer **Opslaan**.

   ![Nieuwe query](./media/tutorial-hunting/new-query.png)

Een bestaande zoek opdracht klonen en wijzigen:

1. Selecteer de zoek opdracht in de tabel die u wilt wijzigen.
2. Selecteer het beletsel teken (...) in de regel van de query die u wilt wijzigen en selecteer **kloon query**.

   ![query klonen](./media/tutorial-hunting/clone-query.png)
 

3. Wijzig de query en selecteer **maken**.

   ![aangepaste query](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u een jacht onderzoek kunt uitvoeren met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:


- [Notitie blokken gebruiken voor het uitvoeren van geautomatiseerde jacht-campagnes](notebooks.md)
- [Gebruik blad wijzers voor het opslaan van interessante informatie tijdens het jacht](bookmarks.md)