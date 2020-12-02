---
title: Visuele ontwerpen
description: Meer informatie over het gebruik van visuele ontwerpen in Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.reviewer: ''
manager: anandsub
ms.date: 09/08/2020
ms.openlocfilehash: c3204d33cac9f0944ec2ea2cf43be65c7afa46df
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500135"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Visueel ontwerpen in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de Azure Data Factory gebruikers interface-ervaring (UX) kunt u resources visueel ontwerpen en implementeren voor uw data factory zonder dat u code hoeft te schrijven. U kunt activiteiten naar een pijplijn doek slepen, test uitvoeringen uitvoeren, iteratief fouten opsporen en uw pijplijn uitvoeringen implementeren en bewaken.

Op dit moment wordt de Azure Data Factory UX alleen ondersteund in micro soft Edge en Google Chrome.

## <a name="authoring-canvas"></a>Canvas ontwerpen

Klik op het potlood pictogram om het **ontwerp canvas** te openen. 

![Canvas ontwerpen](media/author-visually/authoring-canvas.png)

Hier maakt u een ontwerp van de pijp lijnen, activiteiten, gegevens sets, gekoppelde services, gegevens stromen, triggers en Integration Runtimes waaruit uw fabriek bestaat. Zie [gegevens kopiëren met behulp van de Kopieer activiteit](tutorial-copy-data-portal.md)om aan de slag te gaan met het maken van een pijp lijn met behulp van het ontwerp doek. 

De standaard ervaring voor het ontwerpen van visuele elementen werkt rechtstreeks met de Data Factory-service. De integratie van Azure opslag plaatsen git of GitHub wordt ook ondersteund om broncode beheer en samen werking mogelijk te maken voor werk op uw data factory-pijp lijnen. Zie [broncode beheer in azure Data Factory](source-control.md)voor meer informatie over de verschillen tussen deze ontwerp ervaring.

### <a name="properties-pane"></a>Deelvenster Eigenschappen

Voor resources op het hoogste niveau, zoals pijp lijnen, gegevens sets en data stromen, kunnen eigenschappen op hoog niveau worden bewerkt in het deel venster Eigenschappen aan de rechter kant van het canvas. Het deel venster Eigenschappen bevat eigenschappen zoals naam, beschrijving, aantekeningen en andere eigenschappen op hoog niveau. Subbronnen, zoals pijplijn activiteiten en gegevens stroom transformaties, worden bewerkt met behulp van het deel venster aan de onderkant van het canvas. 

![Deelvenster Eigenschappen](media/author-visually/properties-pane.png)

Het deel venster Eigenschappen wordt alleen standaard geopend bij het maken van resources. Als u deze wilt bewerken, klikt u op het deel venster Eigenschappen in de rechter bovenhoek van het canvas.

### <a name="related-resources"></a>Gerelateerde resources

In het deel venster Eigenschappen kunt u zien welke resources afhankelijk zijn van de geselecteerde resource door het tabblad **verwant** te selecteren. Een resource die verwijst naar de huidige resource, wordt hier weer gegeven.

![Gerelateerde resources](media/author-visually/related-resources.png)

In de bovenstaande afbeelding gebruiken bijvoorbeeld één pijp lijn en twee gegevens stromen de gegevensset die momenteel is geselecteerd.

## <a name="management-hub"></a>Beheerhub

De beheer-hub, toegankelijk via het tabblad *beheren* in de Azure Data Factory UX, is een portal die de algemene beheer acties voor uw Data Factory host. Hier kunt u uw verbindingen met gegevens archieven en externe berekeningen, configuratie van broncode beheer en trigger instellingen beheren. Meer informatie over de mogelijkheden van de [Management hub](author-management-hub.md).

![Gekoppelde services beheren](media/author-management-hub/management-hub-linked-services.png)

## <a name="expressions-and-functions"></a>Expressies en functies

Expressies en functies kunnen worden gebruikt in plaats van statische waarden om veel eigenschappen in Azure Data Factory op te geven.

Als u een expressie voor een eigenschaps waarde wilt opgeven, selecteert u **dynamische inhoud toevoegen** of klikt u op **Alt + P** bij focus op het veld.

![Dynamische inhoud toevoegen](media/author-visually/dynamic-content-1.png)

Hiermee opent u de **opbouw functie voor Data Factory Expression** , waar u expressies kunt bouwen op basis van ondersteunde systeem variabelen, uitvoer van activiteiten, functies en door de gebruiker opgegeven variabelen of para meters. 

![Opbouwfunctie voor expressies](media/author-visually/dynamic-content-2.png)

Zie [expressies en functies in azure Data Factory](control-flow-expression-language-functions.md)voor meer informatie over de taal van de expressie.

## <a name="provide-feedback"></a>Feedback geven

Selecteer **feedback** om commentaar te geven over functies of om micro soft op de hoogte te stellen over problemen met het hulp programma:

![Feedback](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Volgende stappen

Zie [pijp lijnen bewaken en beheren via een programma](monitor-programmatically.md)voor meer informatie over het controleren en beheren van pijp lijnen.
