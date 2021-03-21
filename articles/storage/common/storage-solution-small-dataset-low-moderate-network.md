---
title: Opties voor Azure-gegevens overdracht voor kleine gegevens sets met lage tot gemiddeld netwerk bandbreedte | Microsoft Docs
description: Meer informatie over het kiezen van een Azure-oplossing voor gegevens overdracht wanneer u weinig netwerk bandbreedte in uw omgeving hebt en u van plan bent om kleine gegevens sets over te brengen.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: f59d1e297ba4d7607d7abd07a78da4784f55d20f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023224"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Gegevensoverdracht voor kleine gegevenssets met weinig tot gemiddelde netwerkbandbreedte
 
Dit artikel bevat een overzicht van de oplossingen voor gegevens overdracht wanneer u weinig netwerk bandbreedte in uw omgeving hebt en u van plan bent om kleine gegevens sets over te brengen. In het artikel worden ook de aanbevolen opties voor gegevens overdracht en de bijbehorende matrix voor de belangrijkste functionaliteit voor dit scenario beschreven.

Voor een overzicht van alle beschik bare opties voor gegevens overdracht gaat u naar [een Azure-oplossing voor gegevens overdracht kiezen](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Scenariobeschrijving

Kleine gegevenssets verwijzen naar gegevensgrootten van GB's tot enkele TB's. Lage tot gemiddelde netwerkbandbreedte duidt op 45 Mbps (T3-verbinding in datacentrum) tot 1 Gbps.

- Als u slechts een aantal bestanden overbrengt en u de gegevens overdracht niet meer nodig hebt, kunt u de hulpprogram ma's overwegen met een grafische interface.
- Als u vertrouwd bent met systeembeheer, kunt u overwegen om het opdrachtregelprogramma, een programmatische hulpprogramma, of een hulpprogramma voor scripts te gebruiken.

## <a name="recommended-options"></a>Aanbevolen opties

De opties die worden aanbevolen in dit scenario zijn:

- **Grafische interface-hulpprogram ma's** zoals Azure Storage Explorer en Azure Storage in azure Portal. Deze bieden een eenvoudige manier om uw gegevens weer te geven en snel een paar bestanden over te dragen.

    - **Azure Storage Explorer** -dit hulp programma voor meerdere platformen biedt u de mogelijkheid om de inhoud van uw Azure-opslag accounts te beheren. Het stelt u in staat om blobs, bestanden, wachtrijen en tabellen, en Azure Cosmos DB-entiteiten te uploaden, downloaden en beheren. Gebruik het met Blob-opslag om blobs en mappen te beheren, en om blobs te uploaden en te downloaden tussen uw lokale bestandssysteem en Blob-opslag, of tussen opslagaccounts.
    - **Azure Portal** -Azure Storage in azure Portal biedt een webinterface om bestanden te verkennen en nieuwe bestanden een voor een te uploaden. Dit is een goede optie als u geen hulpprogramma's wilt installeren, of opdrachten wilt gebruiken om snel uw bestanden te verkennen, of om gewoon een aantal nieuwe bestanden te uploaden.

- **Scripting/programmatische hulpprogram ma's** zoals AzCopy/Power shell/Azure CLI en Azure Storage rest api's.

    - **AzCopy** : gebruik dit opdracht regel programma om eenvoudig gegevens te kopiëren van en naar Azure-blobs,-bestanden en-tabel opslag met optimale prestaties. AzCopy biedt ondersteuning voor gelijktijdigheid en parallellisme, en de mogelijkheid om kopieerbewerkingen te hervatten als deze worden onderbroken.
    - **Azure PowerShell** -voor gebruikers die vertrouwd zijn met systeem beheer, gebruikt u de module Azure Storage in azure PowerShell om gegevens over te dragen.
    - **Azure cli** : gebruik dit platformoverschrijdende hulp programma voor het beheren van Azure-Services en het uploaden van gegevens naar Azure Storage.
    - **Azure Storage rest api's/sdk's** : wanneer u een toepassing bouwt, kunt u de toepassing ontwikkelen op Azure Storage rest Api's/sdk's en de Azure-client bibliotheken gebruiken die in meerdere talen worden aangeboden.


## <a name="comparison-of-key-capabilities"></a>Vergelijking van de belangrijkste mogelijkheden

In de volgende tabel worden de verschillen tussen de belangrijkste mogelijkheden samengevat.

| Functie | Azure Storage Explorer | Azure Portal | AzCopy<br>Azure PowerShell<br>Azure CLI | Azure Storage REST Api's of Sdk's |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Beschikbaarheid | Downloaden en installeren <br>Zelfstandig hulp programma | Webgebaseerde hulpprogram ma's voor onderzoek in Azure Portal | Opdracht regel programma |Programmeer bare interfaces in .NET, Java, Python, java script, C++, go, Ruby en PHP |
| Grafische interface | Ja | Ja | Nee | Nee |
| Ondersteunde platforms | Windows, Mac, Linux | Op internet gebaseerde |Windows, Mac, Linux |Alle platforms |
| Toegestane bewerkingen voor Blob-opslag<br>voor blobs en mappen | Uploaden<br>Downloaden<br>Beheren | Uploaden<br>Downloaden<br>Beheren |Uploaden<br>Downloaden<br>Beheren | Ja, aanpasbaar |
| Data Lake gen1-opslag toegestaan<br>bewerkingen voor bestanden en mappen | Uploaden<br>Downloaden<br>Beheren | Nee |Uploaden<br>Downloaden<br>Beheren                   | Nee |
| Toegestane bewerkingen voor bestands opslag<br>voor bestanden en mappen | Uploaden<br>Downloaden<br>Beheren | Uploaden<br>Downloaden<br>Beheren   |Uploaden<br>Downloaden<br>Beheren | Ja, aanpasbaar |
| Toegestane bewerkingen voor tabel opslag<br>voor tabellen |Beheren | Nee |Tabel ondersteuning in AzCopy V7 |Ja, aanpasbaar|
| Toegestane wachtrij opslag | Beheren | Nee  |Nee | Ja, is aanpasbaar|


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [overdragen van gegevens met Azure Storage Explorer](../../machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer.md).
- [Gegevens overdragen met AzCopy](./storage-use-azcopy-v10.md)