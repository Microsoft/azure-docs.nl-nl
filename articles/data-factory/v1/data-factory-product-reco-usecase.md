---
title: Gebruiks voorbeeld-product aanbevelingen van Data Factory
description: Meer informatie over een gebruiks voorbeeld dat is geïmplementeerd met behulp van Azure Data Factory samen met andere services.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 44c66f5fa89e7293667c930bfd1720d72dc26bf4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785067"
---
# <a name="use-case---product-recommendations"></a>Gebruiks voorbeeld-product aanbevelingen
Azure Data Factory is een van de vele services die worden gebruikt voor het implementeren van de Cortana Intelligence Suite oplossings versnellers.  Zie [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) -pagina voor meer informatie over deze suite. In dit document beschrijven we een algemene use-case die Azure-gebruikers al hebben opgelost en geïmplementeerd met Azure Data Factory en andere Cortana Intelligence-onderdeel Services.

## <a name="scenario"></a>Scenario
Online detail handelaren willen hun klanten meestal overhalen om producten te kopen door ze te presen teren aan producten waarvan de kans groot is dat ze waarschijnlijk geïnteresseerd zijn in, en daarom kunnen we het beste kopen. Om dit te bereiken, moeten online detail handelaren hun online-ervaring van de gebruiker aanpassen door gebruik te maken van aangepaste product aanbevelingen voor die specifieke gebruiker. Deze gepersonaliseerde aanbevelingen moeten worden gemaakt op basis van hun huidige en historische informatie over het gedrag, de product informatie, de nieuw geïntroduceerde merken en de segmentatie gegevens van producten en klanten.  Daarnaast kunnen ze de aanbevelingen voor gebruikers producten opgeven op basis van de analyse van het algehele gebruiks gedrag van alle gebruikers gecombineerd.

Het doel van deze detail handelaren is te optimaliseren voor de conversies van klikken op verkoop van gebruikers en verdienen hogere omzet omzet.  Deze conversie wordt gerealiseerd door contextuele, op gedrag gebaseerde product aanbevelingen te leveren op basis van interesses en acties van klanten. Voor deze use-case gebruiken we online detail handelaren als voor beeld van bedrijven die willen optimaliseren voor hun klanten. Deze principes zijn echter van toepassing op elk bedrijf dat haar klanten wil betrekken over zijn goederen en services en de koop ervaring van klanten met aangepaste product aanbevelingen moet verbeteren.

## <a name="challenges"></a>Uitdagingen
Er zijn veel uitdagingen die online winkels bieden bij het implementeren van dit type use-case. 

Ten eerste moeten gegevens van verschillende groottes en vormen worden opgenomen vanuit meerdere gegevens bronnen, zowel on-premises als in de Cloud. Deze gegevens omvatten product gegevens, historische gedrags gegevens van de klant en gebruikers gegevens wanneer de gebruiker door de online retail site bladert. 

Ten tweede moeten aangepaste product aanbevelingen redelijkerwijs en correct worden berekend en gedicteerd. Naast het gedrag en de browser gegevens van het product, het merk en de klant, moeten online detail handelaren ook feedback van klanten over eerdere aankopen opneemt in het bepalen van de beste product aanbevelingen voor de gebruiker. 

Ten derde moeten de aanbevelingen onmiddellijk van toepassing zijn op de gebruiker om een naadloze Browse-en aankoop ervaring te bieden en de meest recente en relevante aanbevelingen te bieden. 

Ten slotte moeten de detail handel de effectiviteit van hun aanpak meten door de verkoop successen voor het verkopen en door verkopen en cross-sellen te volgen.

## <a name="solution-overview"></a>Overzicht van de oplossing
Dit voor beeld is het gebruik van cases opgelost en geïmplementeerd door echte Azure-gebruikers met behulp van Azure Data Factory en andere Cortana Intelligence onderdeel Services, waaronder [HDInsight](https://azure.microsoft.com/services/hdinsight/) en [Power bi](https://powerbi.microsoft.com/).

De online winkel maakt gebruik van een Azure Blob Store, een on-premises SQL Server, Azure SQL Database en een relationele data Mart als gegevens opslag opties in de werk stroom.  Het BLOB-archief bevat klant gegevens, gedrags gegevens van klant en gegevens over product gegevens. De gegevens van product informatie bevatten product informatie over het merk en een product catalogus die on-premises is opgeslagen in een Azure Synapse-analyse. 

Alle gegevens worden gecombineerd en gevoederd in een systeem voor product aanbevelingen voor het leveren van persoonlijke aanbevelingen op basis van interesses en acties van klanten, terwijl de gebruiker door producten in de catalogus op de website bladert. De klanten zien ook producten die verwant zijn aan het product dat ze bekijken op basis van de algemene gebruiks patronen van de website die niet zijn gerelateerd aan een gebruiker.

![use-case diagram](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabytes aan onbewerkte weblogboek bestanden worden dagelijks gegenereerd op basis van de website van de online winkel als semi-gestructureerde bestanden. De onbewerkte weblogboek bestanden en de gegevens van de klant en de product catalogus worden regel matig opgenomen in een Azure Blob-opslag met behulp van de wereld wijd geïmplementeerde gegevens verplaatsing van Data Factory als een service. De onbewerkte logboek bestanden voor de dag worden gepartitioneerd (op jaar en maand) in Blob Storage voor lange termijn opslag.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) wordt gebruikt voor het partitioneren van de onbewerkte logboek bestanden in de Blob-opslag en het verwerken van de opgenomen logboeken op schaal met behulp van de Hive-en Pig-scripts. De gepartitioneerde Weblogboeken gegevens worden vervolgens verwerkt om de benodigde invoer te extra heren voor een machine learning aanbevelings systeem om de aangepaste product aanbevelingen te genereren.

Het aanbevelings systeem dat wordt gebruikt voor de machine learning in dit voor beeld is een open source machine learning recommending platform van [Apache mahout](https://mahout.apache.org/).  Een [Azure machine learning](https://azure.microsoft.com/services/machine-learning/) of aangepast model kan worden toegepast op het scenario.  Het mahout-model wordt gebruikt voor het voors pellen van de overeenkomst tussen items op de website op basis van algemene gebruiks patronen en voor het genereren van de persoonlijke aanbevelingen op basis van de afzonderlijke gebruiker.

Ten slotte wordt de resultatenset met aangepaste product aanbevelingen verplaatst naar een relationele datamart voor gebruik door de website van de detail handelaar.  De resultatenset kan ook rechtstreeks vanuit Blob Storage worden geopend door een andere toepassing of worden verplaatst naar extra winkels voor andere consumenten en use cases.

## <a name="benefits"></a>Voordelen
Door hun productaanbevelings strategie te optimaliseren en deze te centreren met zakelijke doel stellingen, heeft de oplossing voldaan aan de handels doelstellingen van de online winkel. Daarnaast konden ze de werk stroom voor product aanbevelingen op een efficiënte, betrouw bare en kosteneffectieve manier operationeel makenen en beheren. De aanpak maakt het eenvoudig om hun model bij te werken en de effectiviteit ervan te verfijnen op basis van de maat regelen van de verkoop van klik-en-klare pogingen. Door gebruik te maken van Azure Data Factory konden ze hun tijdrovende en dure hand matige Cloud resource beheer afbreken en verplaatsen naar on-demand Cloud resource beheer. Daarom konden ze tijd en geld besparen en hun tijd beperken om de oplossing te implementeren. Gegevens afkomst weer gaven en operationele service status zijn eenvoudig te visualiseren en problemen op te lossen met de intuïtieve Data Factory bewaking en beheer GEBRUIKERSINTERFACE die beschikbaar zijn via de Azure Portal. De oplossing kan nu worden gepland en beheerd, zodat voltooide gegevens betrouwbaar worden geproduceerd en geleverd aan gebruikers, en gegevens-en verwerkings afhankelijkheden automatisch worden beheerd zonder menselijke tussen komst.

Door deze persoonlijke winkel ervaring te bieden, heeft de online winkeler een meer concurrerende en aantrekkelijke klant ervaring gemaakt en daarom wordt de verkoop en de algehele klant tevredenheid verbeterd.

