---
title: Releases van micro soft-beveiligings code analyse
description: In dit artikel worden aanstaande releases beschreven van de uitbrei ding voor de analyse van beveiligings codes van micro soft
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 33ce2a496caa52609d8bdf8c92e29064ca4ae349
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85362037"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Release en route ring van micro soft security code Analysis

Het micro soft security code Analysis-Team in samen werking met Developer Support is trots om recente en aanstaande verbeteringen aan te kondigen aan onze MSCA-extensie.


## <a name="credential-scanner-v20-released-in-april-2020"></a>Referentie scanner v 2.0: uitgebracht in april 2020

### <a name="innovations--improvements"></a>Vernieuwt & verbeteringen

- **Kern engine**

   - Gemiddelde prestatie-upgrade van 25% met nabije lineaire uitvoerings tijden
   - Op context/bewijs gebaseerd zoeken en classificatie voor een grotere nauw keurigheid
   - Verbeteringen in algemene wachtwoord detecties en overeenkomende logica voor duidelijke tijdelijke aanduidingen (bijvoorbeeld fakePassword)

- **Dekking** : ondersteuning voor 25 + geheime typen, waaronder de volgende aangevraagde eerste.

   - Wachtwoordzin van certificaat van infrastructuur account
   - Client geheim/API-sleutel
   - HTTP-autorisatie-header
   - Amazon S3-client geheim toegangs sleutel
   - Azure Active Directory token voor client toegang
   - Azure function Master/API-sleutel
   - Toegangs sleutel Power BI
   - Wachtwoord patroon van Azure Resource Manager sjabloon

- **Uitvoerwaarden**

   - Ondersteuning voor bestands indelingen voor SARIF 2,1 en CSV-uitvoer

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v 1.6.0: uitgebracht in april 2020

### <a name="improvements"></a>Verbeteringen

- FUNCTIE: werk bij naar laatste SARIF v2 (versie 2.1.16). Met deze update kunt u de resultaten in de cache opslaan tijdens het door geven van hashes op de opdracht regel, een aanzienlijke prestatie verbetering bij het recursief analyseren van directory's met meerdere exemplaren van scan doelen.
- Probleem oplossing: type fout oplossing in BA2021. DoNotMarkWritableSectionsAsExecutable uitvoer.
- PRESTATIES: Elimineer het laden van PDB voor alle niet-gemengde modus voor beheerde assembly's, inclusief IL-bibliotheek (voor tijd gecompileerd) binaire bestanden.
- ONJUISTE negatieve correctie: Controleer of een PDB die is geplaatst naast een binair element, echt overeenkomt met het binaire bestand onder analyse
- FUNCTIE: Geef het argument--Local-Symbol-Directors op om extra (lokale, niet-symbool server) PDB-opzoek locaties op te geven
- Fout-positieve correctie: overs laan van PDB gestuurde analyses voor de gegenereerde systeem eigen Boots trap exe van .NET core (dit is geen door de gebruiker te bepalen code).

## <a name="whats-next-in-q3-cy20"></a>Wat is er nieuw in Q3 CY20?

- Hulp programma voor Java-beveiligings analyse
- Python-beveiligings analyse programma
- S pluis om TS-linten te vervangen voor type script en Java script
- Hulp programma voor het analyseren van Resource Manager-sjablonen

## <a name="tool-deprecation-notification"></a>Melding voor afschaffing van hulp programma

### <a name="microsoft-security-risk-detection-msrd-is-deprecated-on-june-26-2020"></a>Micro soft beveiligings risico detectie (MSRD) is afgeschaft op 26 2020 juni.

De afgeschafte MSRD fuzzy-service wordt vervangen door een door de open source zelf gehoste ontwikkelaar platform voor ontwikkel aars voor Azure. Dit platform wordt momenteel ontwikkeld en getest in samen werking met veel van de kern product teams van micro soft. Dit benadering van een platform integreert opschoon functies en biedt adaptieve, geavanceerde methoden voor het leren van fuzz/CD-pijp lijnen die in de loop van de tijd met software projecten groeien. De open-source release van dit platform is gepland voor de laatste helft van 2020.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [hand leiding](security-code-analysis-onboard.md)voor de installatie van micro soft voor meer informatie over het uitvoeren van een onboarding en het installeren van de analyse van beveiligings codes.

Bekijk onze [pagina met veelgestelde vragen](security-code-analysis-faq.md)als u meer vragen hebt over de uitbrei ding en de hulpprogram ma's die worden aangeboden.
