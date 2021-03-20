---
title: Acties in de standaard regels-engine voor Azure CDN | Microsoft Docs
description: Referentie documentatie voor acties in de Standard Rules engine voor Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 051737a9f5e0d4092cda26a3f7ce3df1d7f535ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87760121"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Acties in de standaard regels-engine voor Azure CDN

In de [standaard regels-engine](cdn-standard-rules-engine.md) voor Azure Content Delivery Network (Azure CDN) bestaat een regel uit een of meer match-voor waarden en een actie. In dit artikel vindt u gedetailleerde beschrijvingen van de acties die u kunt gebruiken in de Standard Rules engine voor Azure CDN.

Het tweede deel van een regel is een actie. Een actie definieert het gedrag dat wordt toegepast op het aanvraagtype dat overeenkomt met de voorwaarde of een set voorwaarden van overeenkomst.

## <a name="actions"></a>Acties

De volgende acties zijn beschikbaar voor gebruik in de standaard regels-engine voor Azure CDN. 

### <a name="cache-expiration"></a>Verval datum van cache

Gebruik deze actie om de TTL-waarde (time to Live) van het eind punt te overschrijven voor aanvragen die voldoen aan de regels voor waarden.

#### <a name="required-fields"></a>Vereiste velden

Cachegedrag |  Beschrijving              
---------------|----------------
Cache overs Laan | Als deze optie is geselecteerd en de regel overeenkomt, wordt de inhoud niet in de cache opgeslagen.
Onderdrukken | Als deze optie is geselecteerd en de regel overeenkomt, wordt de TTL-waarde die wordt geretourneerd door uw oorsprong overschreven door de waarde die is opgegeven in de actie. Dit gedrag wordt alleen toegepast als het antwoord in de cache kan worden opgeslagen. Voor de reactie header van het cache-besturings element met de waarden ' no-cache ', ' private ', ' No-Store ', is de actie niet van toepassing.
Instellen als ontbreekt | Als deze optie is geselecteerd en de regel overeenkomt, wordt de TTL ingesteld op de waarde die is opgegeven in de actie als er geen TTL-waarde is geretourneerd van uw oorsprong. Dit gedrag wordt alleen toegepast als het antwoord in de cache kan worden opgeslagen. Voor de reactie header van het cache-besturings element met de waarden ' no-cache ', ' private ', ' No-Store ', is de actie niet van toepassing.

#### <a name="additional-fields"></a>Aanvullende velden

Dagen | Tijden | Minuten | Seconden
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>Query reeks voor de cache sleutel

Gebruik deze actie om de cache sleutel te wijzigen op basis van query teken reeksen.

#### <a name="required-fields"></a>Vereiste velden

Gedrag | Beschrijving
---------|------------
Opnemen | Als deze optie is geselecteerd en de regel overeenkomt, worden in de para meters opgegeven query reeksen opgenomen wanneer de cache sleutel wordt gegenereerd. 
Elke unieke URL in de cache opslaan | Als deze optie is geselecteerd en de regel overeenkomt, heeft elke unieke URL een eigen cache sleutel. 
Exclude | Als deze optie is geselecteerd en de regel overeenkomt, worden query reeksen die zijn opgegeven in de para meters uitgesloten wanneer de cache sleutel wordt gegenereerd.
Queryreeksen negeren | Als deze optie is geselecteerd en de regel overeenkomt, worden query reeksen niet meegenomen wanneer de cache sleutel wordt gegenereerd. 

### <a name="modify-request-header"></a>Aanvraagheader wijzigen

Gebruik deze actie om headers te wijzigen die aanwezig zijn in aanvragen die naar uw herkomst worden verzonden.

#### <a name="required-fields"></a>Vereiste velden

Bewerking | HTTP-headernaam | Waarde
-------|------------------|------
Toevoegen | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in **headernaam** aan de aanvraag toegevoegd met de opgegeven waarde. Als er al een header aanwezig is, wordt de waarde toegevoegd aan de bestaande waarde. | Tekenreeks
Overschrijven | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in **headernaam** aan de aanvraag toegevoegd met de opgegeven waarde. Als er al een header aanwezig is, overschrijft de opgegeven waarde de bestaande waarde. | Tekenreeks
Verwijderen | Als deze optie is geselecteerd, de regel overeenkomt en de opgegeven header aanwezig is, wordt de header uit de aanvraag verwijderd. | Tekenreeks

### <a name="modify-response-header"></a>Antwoordheader wijzigen

Gebruik deze actie om headers te wijzigen die aanwezig zijn in antwoorden die aan uw clients worden geretourneerd.

#### <a name="required-fields"></a>Vereiste velden

Bewerking | HTTP-headernaam | Waarde
-------|------------------|------
Toevoegen | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in **headernaam** aan het antwoord toegevoegd met de opgegeven **waarde**. Als de header al aanwezig is, wordt de **waarde** toegevoegd aan de bestaande waarde. | Tekenreeks
Overschrijven | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in **headernaam** aan het antwoord toegevoegd met de opgegeven **waarde**. Als de header al aanwezig is, overschrijft de **waarde** de bestaande waarde overschrijft. | Tekenreeks
Verwijderen | Als deze optie is geselecteerd, de regel overeenkomt en de opgegeven header aanwezig is, wordt de header uit het antwoord verwijderd. | Tekenreeks

### <a name="url-redirect"></a>URL-omleiding

Gebruik deze actie om clients om te leiden naar een nieuwe URL. 

#### <a name="required-fields"></a>Vereiste velden

Veld | Beschrijving 
------|------------
Type | Selecteer het antwoordtype dat u wilt retourneren aan de aanvrager: Gevonden (302), Verplaatst (301), Tijdelijke omleiding (307) en Permanente omleiding (308).
Protocol | Match-aanvraag, HTTP, HTTPS.
Hostnaam | Selecteer de hostnaam waarnaar u de aanvraag wilt omleiden. Laat het veld leeg om de binnenkomende host te behouden.
Pad | Geef het pad op dat in de omleiding moet worden gebruikt. Laat het veld leeg als u het binnenkomende pad wilt behouden.  
Queryreeksen | Geef de queryreeks op die in de omleiding wordt gebruikt. Laat het veld leeg om de binnenkomende queryreeks te behouden. 
Fragment | Geef het fragment op dat in de omleiding moet worden gebruikt. Laat het veld leeg om het binnenkomende fragment te behouden. 

We raden u ten zeerste aan een absolute URL te gebruiken. Het gebruik van een relatieve URL kan Azure CDN Url's omleiden naar een ongeldig pad. 

### <a name="url-rewrite"></a>URL opnieuw genereren

Gebruik deze actie om het pad van een aanvraag die naar uw oorsprong wordt doorgestuurd, opnieuw te genereren.

#### <a name="required-fields"></a>Vereiste velden

Veld | Beschrijving 
------|------------
Bron patroon | Definieer het bron patroon in het URL-pad dat moet worden vervangen. Op dit moment gebruikt het bron patroon een overeenkomst op basis van voor voegsels. Als u wilt dat alle URL-paden overeenkomen, gebruikt u een slash ( **/** ) als bron patroon waarde.
Doel | Definieer het doelpad dat moet worden gebruikt in de herschrijf bewerking. Het doelpad overschrijft het bron patroon.
Niet-overeenkomend pad behouden | Als deze instelling is ingesteld op **Ja**, wordt het resterende pad na het bron patroon toegevoegd aan het nieuwe doelpad. 

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure CDN](cdn-overview.md)
- [Naslaginformatie over standaardregelengine](cdn-standard-rules-engine-reference.md)
- [Voldoen aan de voor waarden in de standaard regels-engine](cdn-standard-rules-engine-match-conditions.md)
- [HTTPS afdwingen met de standaardregelengine](cdn-standard-rules-engine.md)
