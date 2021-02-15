---
title: 'ML Studio (klassiek): De gegevenssets met voorbeelden gebruiken - Azure'
description: Beschrijvingen van de gegevenssets die worden gebruikt in voorbeeldmodellen in Machine Learning Studio (klassiek). U kunt deze gegevenssets met voorbeelden voor uw experimenten gebruiken.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: sample
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: 8a848f6589902c2eabf5b4e14a4d000dc4ed081e
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518433"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio-classic"></a>De gegevenssets met voorbeelden gebruiken in Azure Machine Learning Studio (klassiek)

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


[top]: #machine-learning-sample-datasets

Wanneer u in Azure Machine Learning Studio (klassiek) een nieuwe werkruimte maakt, hebt u standaard de beschikking over een aantal experimenten en gegevenssets met voorbeelden. Veel van deze gegevenssets met voorbeelden worden gebruikt in de voorbeeldmodellen in [Azure AI Gallery](https://gallery.azure.ai/). Andere zijn opgenomen als voorbeelden van verschillende typen gegevens die doorgaans in machine learning worden gebruikt.

Sommige van deze gegevenssets zijn beschikbaar in Azure Blob-opslag. De volgende tabel bevat een directe koppeling naar deze gegevenssets. U kunt deze gegevenssets in uw experimenten gebruiken met behulp van de module [Gegevens importeren][import-data].

De rest van deze gegevenssets met voorbeelden is beschikbaar in uw werkruimte onder **Opgeslagen gegevenssets**. U kunt dit vinden in het modulepalet links van het experimenteercanvas in Machine Learning Studio (klassiek).
U kunt deze gegevenssets voor uw eigen experiment gebruiken door ze naar uw experimenteercanvas te slepen.

## <a name="datasets"></a>Gegevenssets

<table>

<tr>
  <th>Naam van de gegevensset</th>
  <th>Beschrijving van gegevensset</th>
</tr>

<tr>
  <td>Gegevensset Binaire classificatie voor volwassen Census-inkomen</td>
  <td>
Een subset van de 1994 Census-database, gebruikmakend van werkende volwassenen boven de 16 jaar met een aangepaste inkomstenindex van > 100.
<p></p>
<b>Gebruik:</b> Classificeer mensen met behulp van demografie om te voorspellen of een persoon meer dan 50.000 per jaar verdient.
<p></p>
<b>Aanverwant onderzoek:</b> Kohavi, R., Becker, B., (1996). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Gegevensset met luchthavencodes</td>
  <td>
Amerikaanse luchthavencodes.
<p></p>
Deze gegevensset bevat één rij voor elke Amerikaanse luchthaven, waarin het identificatienummer en de naam van de luchthaven en tevens de plaats en staat van de locatie.
  </td>
</tr>

<tr>
  <td>Autoprijsgegevens (onbewerkt)</td>
  <td>
Informatie over auto’s per merk en model, waaronder de prijs, kenmerken zoals het aantal cilinders en MPG, evenals een verzekeringsrisicoscore.
<p></p>
De risicoscore is aanvankelijk gekoppeld aan de autoprijs. Vervolgens wordt het aangepast voor daadwerkelijk risico in een proces dat verzekeringswiskundigen symbolisering noemen. Een waarde van +3 geeft aan dat de auto risicovol is, en een waarde van -3 dat deze waarschijnlijk veilig is.
<p></p>
<b>Gebruik:</b> Voorspel de risicoscore per kenmerk met behulp van regressie of multidimensionale classificatie. 
<p></p>
<b>Aanverwant onderzoek:</b> Schlimmer, J.C. (1987). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Bike Rental UCI-gegevensset</td>
  <td>
UCI Bike Rental-gegevensset op basis van reële gegevens van het bedrijf Capital Bikeshare, dat een netwerk voor fietsverhuur in Washington D.C. verzorgt.
<p></p>
De gegevensset heeft één rij voor elk uur van elke dag in 2011 en 2012, met in totaal 17.379 rijen. Het bereik van het aantal verhuurde fietsen per uur is 1 tot en met 977.

  </td>
</tr>

<tr>
  <td>RGB-afbeelding van Bill Gates</td>
  <td>
Openbaar beschikbaar afbeeldingsbestand dat naar CSV-gegevens is geconverteerd.
<p></p>
De code voor het converteren van de afbeelding vindt u op de pagina met modeldetails <strong>Color quantization using k-Means clustering</strong> (Kwantisatie van kleuren door middel van k-Means-clustering).
  </td>
</tr>

<tr>
  <td>Bloeddonatiegegevens</td>
  <td>
Een subset met gegevens uit de database met bloeddonoren van het servicecentrum voor bloedtransfusie van Hsinchu (stad) in Taiwan.
<p></p>
Donorgegevens omvatten de maand sinds de laatste donatie, de frequentie of het totale aantal donaties, de tijd sinds de laatste donatie en de hoeveelheid gedoneerd bloed.
<p></p>
<b>Gebruik:</b> Het doel is om via classificatie te voorspellen of de donor bloed heeft gedoneerd in maart 2007, waarbij 1 voor een donor staat tijdens de doelperiode en 0 voor een niet-donor. 
<p></p>
<b>Aanverwant onderzoek:</b> Yeh, I.C., (2008). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science
<p></p>
Yeh, I-Cheng, Yang, King-Jang, and Ting, Tao-Ming, "Knowledge discovery on RFM model using Bernoulli sequence, "Expert Systems with Applications, 2008, <a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Borstkankergegevens</td>
  <td>
Een van de drie aan kanker gerelateerde gegevenssets die worden verstrekt door het oncologie-instituut dat regelmatig in de literatuur over machine learning voorkomt. Hierin worden diagnostische gegevens met kenmerken van laboratoriumanalyse van circa 300 weefselmonsters gecombineerd.
<p></p>
<b>Gebruik:</b> Classificatie van het type kanker op basis van negen kenmerken, waarvan sommige lineair en sommige categorisch zijn. 
<p></p>
<b>Aanverwant onderzoek:</b> Wohlberg, W.H., Street, W.N., & Mangasarian, O.L. (1995). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Kenmerken van borstkanker <td>
De gegevensset bevat informatie over 102.000 verdachte regio's (kandidaten) met röntgenfoto's, die elk door 117 kenmerken worden beschreven. De kenmerken zijn privé en de betekenis ervan wordt niet vrijgegeven door de makers van de gegevensset (Siemens Healthcare). 
  </td>
</tr>

<tr>
  <td>Borstkankerinfo</td>
  <td>
De gegevensset bevat aanvullende informatie voor elke verdachte regio met een röntgenfoto. Elk voorbeeld bevat informatie (bijvoorbeeld label, patiënt-id, coördinaten van de plek ten opzichte van de hele foto) over het overeenkomstige rijnummer in de gegevensset Kenmerken van borstkanker. Voor elke patiënt is er een aantal voorbeelden. Voor patiënten met kanker zijn enkele voorbeelden positief en sommige negatief. Voor patiënten die geen kanker hebben, zijn alle voorbeelden negatief. De gegevensset bevat 102.000 voorbeelden. De gegevensset is vertekend; 0,6% van de punten is positief, de rest negatief. De gegevensset is beschikbaar gesteld door Siemens Healthcare.
  </td>
</tr>

<tr>
  <td>Gedeelde labels CRM-verlangen</td>
  <td>
Labels uit de KDD Cup 2009-uitdaging Klantrelaties voorspellen (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>Gedeelde labels CRM-verloop</td>
  <td>
Labels uit de KDD Cup 2009-uitdaging Klantrelaties voorspellen (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>Gedeelde CRM-gegevensset</td>
  <td>
Deze gegevens komen uit de KDD Cup 2009-uitdaging Klantrelaties voorspellen (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
De gegevensset bevat 50.000 klanten van het Franse telecombedrijf Orange. Elke klant heeft 230 geanonimiseerde kenmerken, waarvan 190 numeriek en 40 categorisch zijn. De kenmerken zijn zeer verspreid.
  </td>
</tr>

<tr>
  <td>Gedeelde labels CRM-upselling</td>
  <td>
Labels uit de KDD Cup 2009-wedstrijd Klantrelaties voorspellen (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Regressiegegevens over energiezuinigheid</td>
  <td>
Een verzameling gesimuleerde energieprofielen op basis van twaalf verschillende gebouwvormen. De gebouwen worden op acht kenmerken onderscheiden. Deze zijn onder meer het glasoppervlak, de verdeling en de oriëntatie van het glas.
<p></p>
<b>Gebruik:</b> Gebruik regressie of classificatie om de energiezuinigheid te voorspellen op basis van een of twee reële responsewaarden. Voor classificaties met meerdere klassen is rond de responsevariabele tot op het dichtstbijzijnde gehele getal. 
<p></p>
<b>Aanverwant onderzoek:</b> Xifara, A. & Tsanas, A. (2012). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Gegevens over vluchtvertragingen</td>
  <td>
Gegevens over tijdige passagiersvluchten opgehaald uit de TranStats-gegevensverzameling van de U.S. Department of Transportation (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">On-Time</a>).
<p></p>
De gegevensset dekt de tijdsperiode april t/m oktober 2013. Voordat de gegevensset naar Azure Machine Learning Studio (klassiek) wordt geüpload, is deze als volgt verwerkt:
<ul>
  <li>De gegevensset is gefilterd zodat slechts de zeventig drukste luchthavens op het vasteland van de VS zijn meegenomen</li>
  <li>Geannuleerde vluchten zijn gelabeld met meer dan vijftien minuten vertraging</li>
  <li>Omgeleide vluchten zijn uitgefilterd</li>
  <li>De volgende kolommen zijn geselecteerd: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled</li>
</ul>
</td>
</tr>

<tr>
  <td>Vlucht op tijd (onbewerkt)</td>
  <td>
Records van aankomst en vertrek van vliegtuigen binnen de Verenigde Staten vanaf oktober 2011.
<p></p>
<b>Gebruik:</b> Vertragingen voorspellen. 
<p></p>
<b>Aanverwant onderzoek:</b> Van het United States Department of Transportation <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td>Bosbrandgegevens</td>
  <td>
Bevat weersgegevens, zoals temperatuurs- en vochtigheidsindices en windsnelheid. De gegevens zijn afkomstig van een gebied in het noordoosten van Portugal, gecombineerd met records van bosbranden.
<p></p>
<b>Gebruik:</b> Dit is een lastige regressietaak. Het doel is om het verbrande deel na een bosbrand te voorspellen. 
<p></p>
<b>Aanverwant onderzoek:</b> Cortez, P., & Morais, A. (2008). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science
<p></p>
[Cortez and Morais, 2007] P. Cortez and A. Morais. A Data Mining Approach to Predict Forest Fires using Meteorological Data. In J. Neves, M. F. Santos and J. Machado Eds., New Trends in Artificial Intelligence, Proceedings of the 13th EPIA 2007 - Portuguese Conference on Artificial Intelligence, December, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Beschikbaar op: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr>
  <td>Gegevensset UCI German Credit Card</td>
  <td>
De gegevensset UCI Statlog (German Credit Card) (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data</a>), die het german.data-bestand gebruikt.
<p></p>
De gegevensset classificeert personen, beschreven door een set kenmerken, als laag of hoog kredietrisico. Elk voorbeeld vertegenwoordigt een persoon. Er zijn 20 kenmerken, zowel numeriek als categorisch, en een binair label (de kredietrisicowaarde). Vermeldingen van een hoog kredietrisico hebben label = 2, vermeldingen van een laag kredietrisico hebben label = 1. De kosten van het verkeerd classificeren van een voorbeeld met laag risico als hoog is 1, en de kosten van het verkeerd classificeren van een voorbeeld met hoog risico als laag is 5.
  </td>
</tr>

<tr>
  <td>IMDB-filmtitels</td>
  <td>
De gegevensset bevat informatie over films die zijn beoordeeld in Twitter-tweets: IMDB-film-ID, filmnaam, genre en productiejaar. De gegevensset bevat 17.000 films. De gegevensset werd geïntroduceerd in het rapport "S. Dooms, T. De Pessemier en L. Martens. MovieTweetings: a Movie Rating Dataset Collected From Twitter. Workshop on Crowdsourcing and Human Computation for Recommender Systems, CrowdRec at RecSys 2013."
  </td>
</tr>

<tr>
  <td>Iris-gegevens in twee klassen</td>
  <td>
Dit is wellicht de bekendste database uit de literatuur over patroonherkenning. De gegevensset is relatief klein, met vijftig voorbeelden elk van de maten van de kroonbladen van irisvarianten.
<p></p>
<b>Gebruik:</b> Het iristype voorspellen op basis van de metingen.  
<p></p>
<b>Aanverwant onderzoek:</b> Fisher, R.A. (1988). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Filmtweets</td>
  <td>
Deze gegevensset is een uitgebreide versie van de gegevensset MovieTweetings. De gegevensset bevat 170.000 beoordelingen van films, geëxtraheerd uit goed gestructureerde tweets op Twitter. Elk exemplaar vertegenwoordigt een tweet en is een tuple: gebruikers-ID, IMDB-film-ID, beoordeling, tijdstempel, aantal vind-ik-leuks van deze tweet en aantal retweets van deze tweet. De gegevensset werd beschikbaar gesteld door A. Said, S. Dooms, B. Loni en D. Tikk voor Recommender Systems Challenge 2014.
  </td>
</tr>

<tr>
  <td>Gegevens over brandstofverbruik van diverse auto's</td>
  <td>
Deze gegevensset is een enigszins gewijzigde versie van de gegevensset die door de StatLib-bibliotheek van Carnegie Mellon University wordt verstrekt. De gegevensset is gebruikt in de uiteenzetting van de American Statistical Association uit 1983.
<p></p>
In de lijst staat het brandstofverbruik van verschillende auto's in mijlen per gallon vermeld. Het bevat ook informatie als het aantal cilinders, de cilinderinhoud, het vermogen, het totale gewicht en de acceleratie.
<p></p>
<b>Gebruik:</b> Voorspellen van brandstofverbruik op basis van drie meerwaardige, discrete kenmerken en vijf continue kenmerken. 
<p></p>
<b>Aanverwant onderzoek:</b> StatLib, Carnegie Mellon University, (1993). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Gegevensset voor binaire classificatie van diabetes bij Pima-indianen</td>
  <td>
Een gegevenssubset uit de database van het National Institute of Diabetes and Digestive and Kidney Diseases. De gegevensset is gefilterd om de nadruk te leggen op vrouwelijke Pima-patiënten. De gegevens omvatten medische gegevens, zoals glucose- en insulinespiegels, en levensstijlfactoren.
<p></p>
<b>Gebruik:</b> Voorspellen of de patiënt diabetes heeft (binaire classificatie). 
<p></p>
<b>Aanverwant onderzoek:</b> Sigillito, V. (1990). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml"</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Gegevens van bezoekers van restaurants</td>
  <td>
Een set metagegevens over klanten, inclusief demografische informatie en voorkeuren.
<p></p>
<b>Gebruik:</b> Gebruik deze gegevensset, in combinatie met de andere twee gegevenssets van het restaurant, om een aanbevolen systeem te trainen en te testen. 
<p></p>
<b>Aanverwant onderzoek:</b> Bache, K. and Lichman, M. (2013). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Gegevens over restaurantkenmerken</td>
  <td>
Een set metagegevens over restaurants en hun kenmerken, zoals type gerechten, beoogd publiek en locatie.
<p></p>
<b>Gebruik:</b> Gebruik deze gegevensset, in combinatie met de andere twee gegevenssets van het restaurant, om een aanbevolen systeem te trainen en te testen. 
<p></p>
<b>Aanverwant onderzoek:</b> Bache, K. and Lichman, M. (2013). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Restaurantbeoordelingen</td>
  <td>
Bevat beoordelingen van gebruikers over restaurants op een schaal van 0 tot 2.
<p></p>
<b>Gebruik:</b> Gebruik deze gegevensset, in combinatie met de andere twee gegevenssets van het restaurant, om een aanbevolen systeem te trainen en te testen. 
<p></p>
<b>Aanverwant onderzoek:</b> Bache, K. and Lichman, M. (2013). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Gegevensset van meerdere klassen over gloeien van staal</td>
  <td>
Deze gegevensset bevat een reeks records uit experimenten over het gloeien van staal. Het bevat de fysieke kenmerken (breedte, dikte, type (rol, blad, enzovoort) van de resulterende staaltypen.
<p></p>
<b>Gebruik:</b> Voorspellen van twee numerieke klassekenmerken; hardheid of sterkte. U kunt ook correlaties tussen kenmerken analyseren.
<p></p>
Staalkwaliteiten volgen een vaste standaard, gedefinieerd door SAE en andere organisaties. U zoekt een specifieke 'kwaliteit' (de klassevariabele) en u wilt meer inzicht krijgen in de benodigde waarden. 
<p></p>
<b>Aanverwant onderzoek:</b> Sterling, D. & Buntine, W. (N.v.t.) UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science
<p></p>
Een nuttige handleiding voor staalkwaliteiten vindt u hier: <a href="https://www.steamforum.com/pictures/Outokumpu-steel-grades-properties-global-standards.pdf">https://www.steamforum.com/pictures/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Telescoopgegevens</td>
  <td>
Record van hoogenergetische uitbarstingen van gammadeeltjes in combinatie met achtergrondruis, beide gesimuleerd met behulp van een Monte Carlo-methode.
<p></p>
Het doel van de simulatie is om de nauwkeurigheid te verbeteren van telescopen op de grond voor de detectie van atmosferische gammastraling. Dit gebeurt aan de hand van statistische methoden om onderscheid te maken tussen het gewenste signaal (Cherenkovstraling) en achtergrondruis (deeltjesstroom van hadronen die door kosmische straling in de bovenste lagen van de atmosfeer worden opgewekt).
<p></p>
De gegevens zijn voorbewerkt om een verlengd cluster te maken waarbij de lange as op het centrale deel van de camera is gericht. De kenmerken van deze ellips (ook wel Hillasparameters genoemd) zijn parameters van de afbeelding die ter onderscheiding kunnen worden gebruikt.
<p></p>
<b>Gebruik:</b> Voorspellen of een afbeelding van deeltjesstroom een signaal of achtergrondruis representeert.
<p></p>
<b>Opmerkingen:</b> Gewone classificatienauwkeurigheid is voor deze gegevens niet zinvol, omdat het classificeren van een achtergrondgebeurtenis als signaal erger is dan het classificeren van een signaal als achtergrond. Voor het vergelijken van verschillende classificatoren moet de ROC-curve worden gebruikt. De kans dat een achtergrondgebeurtenis als signaal wordt geaccepteerd, moet lager zijn dan een van de volgende drempelwaarden: 0,01, 0,02, 0,05, 0,1 of 0,2.
<p></p>
Houd er ook rekening mee dat het aantal achtergrondgebeurtenissen (h, voor hadronische deeltjesstroom) wordt onderschat. In echte metingen vertegenwoordigt de h of ruisklasse het merendeel van de gebeurtenissen. 
<p></p>
<b>Aanverwant onderzoek:</b> Bock, R.K. (1995). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information </td>
</tr>

<tr>
  <td>Gegevensset Weer</td>
  <td>
Weerobservaties op het land per uur van NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">samengevoegde gegevens van 201304 tot 201310</a>).
<p></p>
De weersgegevens komen van observaties uit weerstations op luchthavens en dekken de tijdsperiode april t/m oktober 2013. Voordat de gegevensset naar Azure Machine Learning Studio (klassiek) werd geüpload, werd deze als volgt verwerkt:
<ul>
  <li>Weerstation-id's werden toegewezen aan overeenkomstige luchthaven-id's</li>
  <li>Weerstations die niet waren gekoppeld aan de zeventig drukste luchthavens, werden eruit gefilterd</li>
  <li>De kolom Date werd in aparte kolommen gesplitst: Year, Month en Day</li>
  <li>De volgende kolommen werden geselecteerd: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Gegevensset Wikipedia SP 500</td>
  <td>
Gegevens zijn afkomstig van Wikipedia (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>) op basis van artikelen van elk S&P 500-bedrijf, opgeslagen als XML-gegevens.
<p></p>
Voordat de gegevensset naar Azure Machine Learning Studio (klassiek) werd geüpload, werd deze als volgt verwerkt:
<ul>
  <li>Voor elk specifieke bedrijf werd tekstinhoud geëxtraheerd</li>
  <li>Wiki-opmaak werd verwijderd</li>
  <li>Niet-alfanumerieke tekens werden verwijderd</li>
  <li>Alle tekst werd omgezet in kleine letters</li>
  <li>Bekende bedrijfscategorieën werden toegevoegd</li>
</ul>
<p></p>
Voor sommige bedrijven kon er geen artikel worden gevonden, waardoor het aantal records minder dan 500 is.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
De gegevensset bevat klantgegevens en indicaties over hun reactie op een direct-mailingcampagne. Elke rij stelt een klant voor. De gegevensset bevat negen functies voor demografische gegevens van gebruikers en gedrag in het verleden, en drie labelkolommen (bezoek, conversie en uitgaven).  Bezoek is een binaire kolom die aangeeft dat een klant na de marketingcampagne een bezoek heeft gebracht. Conversie geeft aan dat een klant iets heeft gekocht. Uitgaven is het bedrag dat is uitgegeven.  De gegevensset is beschikbaar gesteld door Kevin Hillstrom voor MineThatData E-Mail Analytics And Data Mining Challenge.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Kenmerken van testvoorbeelden in de RCV1-V2-gegevensset met nieuwsberichten van Reuters. De gegevensset bevat 781.000 nieuwsartikelen samen met hun id (eerste kolom van de gegevensset). Elk artikel wordt gesplitst in afzonderlijke woorden (tokenized), ontdaan van woorden met weinig inhoud (stopworded) en ontdaan van voor- en achtervoegsels met weinig of geen inhoud (stemmed). De gegevensset is beschikbaar gesteld door David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Kenmerken van trainingsvoorbeelden in de RCV1-V2-gegevensset met nieuwsberichten van Reuters. De gegevensset bevat 23.000 nieuwsartikelen samen met hun id (eerste kolom van de gegevensset). Elk artikel wordt gesplitst in afzonderlijke woorden (tokenized), ontdaan van woorden met weinig inhoud (stopworded) en ontdaan van voor- en achtervoegsels met weinig of geen inhoud (stemmed). De gegevensset is beschikbaar gesteld door David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Gegevensset van de KDD Cup 1999: wedstrijd kennisdetectie en hulpmiddelen voor datamining (<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99. html</a>).
<p></p>
De gegevensset is gedownload en opgeslagen in Azure Blob Storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) en bevat gegevenssets over trainingen en tests. De trainingsgegevensset bevat ongeveer 126.000 rijen en 43 kolommen, met inbegrip van de labels. Van de informatie op de labels maken drie kolommen deel uit. 40 kolommen, bestaande uit numerieke en tekenreeks-/categorische functies, zijn beschikbaar om het model te trainen. De testgegevens bevatten circa 22.500 testvoorbeelden, met dezelfde 43 kolommen als in de trainingsgegevens.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Thematoewijzingen voor nieuwsartikelen in de RCV1-V2-gegevensset met nieuwsberichten van Reuters. Een nieuwsartikel kan aan verschillende thema's worden toegewezen. De indeling van elke rij is &lt;naam thema&gt; &lt;document-id&gt; 1. De gegevensset bevat 2.600.000 thematoewijzingen. De gegevensset is beschikbaar gesteld door David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Deze gegevens zijn afkomstig uit de KDD Cup 2010: wedstrijd evaluatie van prestaties van studenten (<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">evaluatie van prestaties van studenten</a>). De gebruikte gegevens bestaan uit de Algebra_2008_2009-trainingsset (Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010). Algebra I 2008-2009. Wedstrijdgegevensset van de KDD Cup 2010: wedstrijd datamining voor onderwijsdoeleinden. U vindt het op <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
De gegevensset is gedownload en opgeslagen in Azure Blob Storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) en bevat logboekbestanden uit een tutoring-systeem voor studenten. De verstrekte kenmerken omvatten de probleem-id en een korte beschrijving, student-id, tijdstempel en het aantal pogingen dat de student heeft gedaan voordat het probleem op de juiste manier werd opgelost. De oorspronkelijke gegevensset bevat 8.900.000 records; deze gegevensset is gereduceerd tot de eerste 100.000 rijen. De gegevensset heeft 23 door tabs gescheiden kolommen van verschillende typen: numeriek, categorisch en tijdstempel.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zet uw experimenten in gang met voorbeelden](sample-experiments.md)

<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data