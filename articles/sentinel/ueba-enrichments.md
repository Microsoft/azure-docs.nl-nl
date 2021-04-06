---
title: Naslag informatie over de Azure Sentinel UEBA-verrijkingen | Microsoft Docs
description: In dit artikel worden de entiteits verrijkingen weer gegeven die zijn gegenereerd door de entiteit gedrags analyse van Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.author: yelevin
ms.openlocfilehash: daba8fc1f645b51dc8668c806be63744b6ae0842
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97901725"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Naslag informatie over verrijkingen van Azure Sentinel UEBA

Deze tabellen bevatten een lijst en beschrijven entiteits verrijkingen die kunnen worden gebruikt om het onderzoek van beveiligings incidenten te richten en te verscherpen.

De eerste twee tabellen, **gebruikers inzichten** en **device Insights**, bevatten entiteits gegevens van Active Directory/Azure AD en micro soft Threat Intelligence-bronnen.

<a name="baseline-explained"></a>De rest van de tabellen, onder **Activity Insights-tabellen**, bevatten entiteits gegevens op basis van de gedrags profielen die zijn gebouwd door de analyse van het entiteit gedrag van Azure Sentinel. De activiteiten worden geanalyseerd volgens een basis lijn die dynamisch wordt gecompileerd telkens wanneer deze wordt gebruikt. Voor elke activiteit is de gedefinieerde lookback-periode van waaruit deze dynamische basis lijn is afgeleid. Deze periode is opgegeven in de kolom [**basislijn**](#activity-insights-tables) in deze tabel.

> [!NOTE] 
> In het veld **verrijkings naam** in alle drie de tabellen worden twee rijen met gegevens weer gegeven. De eerste, **vetgedrukt**, is de beschrijvende naam van de verrijking. De tweede *(cursief en haakjes)* is de veld naam van de verrijking zoals opgeslagen in de [**analyse tabel**](identify-threats-with-entity-behavior-analytics.md#data-schema)van het gedrag.

## <a name="user-insights-table"></a>Tabel met gebruikers inzichten

| Verrijkings naam | Beschrijving | Voorbeeldwaarde |
| --- | --- | --- | --- |
| **Weergave naam account**<br>*(AccountDisplayName)* | De weergave naam van de account van de gebruiker. | Beheerder, Hayden Cook |
| **Accountdomein**<br>*(AccountDomain)* | De domein naam van het account van de gebruiker. |  |
| **Object-ID van account**<br>*(AccountObjectID)* | De object-ID van de account van de gebruiker. | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **Versterking van RADIUS**<br>*(BlastRadius)* | De hoogoven straal wordt berekend op basis van verschillende factoren: de positie van de gebruiker in de organisatie structuur en de Azure Active Directory rollen en machtigingen van de gebruiker. | Laag, gemiddeld, hoog |
| **Is een niet-actieve account**<br>*(IsDormantAccount)* | Het account is afgelopen 180 dagen niet gebruikt. | Waar, onwaar |
| **Is lokale beheerder**<br>*(IsLocalAdmin)* | Het account heeft lokale Administrator bevoegdheden. | Waar, onwaar |
| **Is een nieuw account**<br>*(IsNewAccount)* | Het account is in de afgelopen 30 dagen gemaakt. | Waar, onwaar |
| **On-premises SID**<br>*(OnPremisesSID)* | De on-premises SID van de gebruiker met betrekking tot de actie. | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

## <a name="device-insights-table"></a>Tabel met Device Insights

| Verrijkings naam | Beschrijving | Voorbeeldwaarde |
| --- | --- | --- | --- |
| **Browser**<br>*Bekijkt* | De browser die wordt gebruikt in de actie. | Edge, Chrome |
| **Apparaatfamilie**<br>*(DeviceFamily)* | Het apparaat dat wordt gebruikt in de actie. | Windows |
| **Apparaattype**<br>*DeviceType* | Het type client apparaat dat wordt gebruikt in de actie | Desktop |
| **OPNEMEN**<br>*OPNEMEN* | De Internet serviceprovider die wordt gebruikt in de actie. |  |
| **Besturingssysteem**<br>*Geheugen* | Het besturings systeem dat wordt gebruikt voor de actie. | Windows 10 |
| **Beschrijving van bedreigings-Intel-indicator**<br>*(ThreatIntelIndicatorDescription)* | Beschrijving van de waargenomen bedreigings indicator die is opgelost met het IP-adres dat wordt gebruikt in de actie. | Host is lid van botnet: azorult |
| **Type bedreiging Intel-indicator**<br>*(ThreatIntelIndicatorType)* | Het type bedreigings indicator dat is omgezet van het IP-adres dat wordt gebruikt in de actie. | Botnet, C2, CryptoMining, Darknet, DDoS, MaliciousUrl, malware, phishing, proxy, PUA'S geblokkeerd, watch list |
| **Gebruikers agent**<br>*User agent* | De gebruikers agent die wordt gebruikt voor de actie. | Microsoft Azure Graph-client bibliotheek 1,0,<br>Swagger-CodeGen/1.4.0.0/csharp,<br>EvoSTS |
| **Gebruikers agent-familie**<br>*(UserAgentFamily)* | De groep gebruikers agent die wordt gebruikt in de actie. | Chrome, Edge, Firefox |
|

## <a name="activity-insights-tables"></a>Activiteiten Insights-tabellen

#### <a name="action-performed"></a>Uitgevoerde actie

| Verrijkings naam | [Basis lijn](#baseline-explained) (dagen) | Beschrijving | Voorbeeldwaarde |
| --- | --- | --- | --- |
| **Eerste keer dat de gebruiker de actie heeft uitgevoerd**<br>*(FirstTimeUserPerformedAction)* | 180 | De actie is voor de eerste keer uitgevoerd door de gebruiker. | Waar, onwaar |
| **Actie ongebruikelijk uitgevoerd door gebruiker**<br>*(ActionUncommonlyPerformedByUser)* | 10 | De actie wordt niet vaak uitgevoerd door de gebruiker. | Waar, onwaar |
| **Actie die ongebruikelijk wordt uitgevoerd tussen peers**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | De actie wordt niet vaak uitgevoerd op de peers van de gebruiker. | Waar, onwaar |
| **Eerste keer dat er een bewerking wordt uitgevoerd in de Tenant**<br>*(FirstTimeActionPerformedInTenant)* | 180 | De actie is voor de eerste keer uitgevoerd door iedereen in de organisatie. | Waar, onwaar |
| **Actie ongebruikelijk uitgevoerd in Tenant**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | De actie wordt niet vaak uitgevoerd in de organisatie. | Waar, onwaar |
|

#### <a name="app-used"></a>App gebruikt

| Verrijkings naam | [Basis lijn](#baseline-explained) (dagen) | Beschrijving | Voorbeeldwaarde |
| --- | --- | --- | --- |
| **De eerste keer dat de gebruiker de app gebruikt**<br>*(FirstTimeUserUsedApp)* | 180 | De app is voor de eerste keer door de gebruiker gebruikt. | Waar, onwaar |
| **De app wordt ongebruikelijk gebruikt door de gebruiker**<br>*(AppUncommonlyUsedByUser)* | 10 | De app wordt niet vaak door de gebruiker gebruikt. | Waar, onwaar |
| **App wordt ongebruikelijk gebruikt tussen peers**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | De app wordt niet vaak gebruikt op de peers van de gebruiker. | Waar, onwaar |
| **Eerste keer dat de app wordt waargenomen in de Tenant**<br>*(FirstTimeAppObservedInTenant)* | 180 | De app is voor de eerste keer in de organisatie geobserveerd. | Waar, onwaar |
| **De app wordt niet gebruikelijk gebruikt in de Tenant**<br>*(AppUncommonlyUsedInTenant)* | 180 | De app wordt niet vaak gebruikt in de organisatie. | Waar, onwaar |
| 

#### <a name="browser-used"></a>Gebruikte browser

| Verrijkings naam | [Basis lijn](#baseline-explained) (dagen) | Beschrijving | Voorbeeldwaarde |
| --- | --- | --- | --- |
| **Eerste keer dat de gebruiker verbinding heeft via de browser**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | De browser is voor de eerste keer door de gebruiker geobserveerd. | Waar, onwaar |
| **Browser ongebruikelijk gebruikt door gebruiker**<br>*(BrowserUncommonlyUsedByUser)* | 10 | De browser wordt niet vaak gebruikt door de gebruiker. | Waar, onwaar |
| **Browser ongebruikelijk gebruikt onder peers**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | De browser wordt niet vaak gebruikt op de peers van de gebruiker. | Waar, onwaar |
| **Eerste keer dat de browser wordt waargenomen in de Tenant**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | De browser is voor de eerste keer in de organisatie geobserveerd. | Waar, onwaar |
| **Browser ongebruikelijk gebruikt in Tenant**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | De browser wordt niet vaak gebruikt in de organisatie. | Waar, onwaar |
| 

#### <a name="country-connected-from"></a>Land verbonden vanaf

| Verrijkings naam | [Basis lijn](#baseline-explained) (dagen) | Beschrijving | Voorbeeldwaarde |
| --- | --- | --- | --- |
| **Eerste keer dat de gebruiker verbinding heeft met het land**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | De geografische locatie, zoals omgezet van het IP-adres, is voor de eerste keer door de gebruiker verbonden. | Waar, onwaar |
| **Het land is ongebruikelijk verbonden door de gebruiker**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | De geografische locatie, zoals die is omgezet van het IP-adres, is niet vaak verbonden door de gebruiker. | Waar, onwaar |
| **Het land is ongebruikelijk verbonden tussen peers**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | De geo-locatie, zoals omgezet van het IP-adres, is niet vaak verbonden vanuit de peers van de gebruiker. | Waar, onwaar |
| **Eerste keer verbinding van land dat in de Tenant is waargenomen**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | Het land is van de eerste keer verbonden door iemand in de organisatie. | Waar, onwaar |
| **Het land is ongebruikelijk verbonden vanuit de Tenant**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | De geo-locatie, zoals omgezet van het IP-adres, is niet vaak verbonden vanuit de organisatie. | Waar, onwaar |
| 

#### <a name="device-used-to-connect"></a>Apparaat dat wordt gebruikt om verbinding te maken

| Verrijkings naam | [Basis lijn](#baseline-explained) (dagen) | Beschrijving | Voorbeeldwaarde |
| --- | --- | --- | --- |
| **Eerste keer dat de gebruiker verbinding heeft met het apparaat**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | Het bron apparaat is voor de eerste keer verbonden door de gebruiker. | Waar, onwaar |
| **Het apparaat wordt ongebruikelijk gebruikt door de gebruiker**<br>*(DeviceUncommonlyUsedByUser)* | 10 | Het apparaat wordt niet vaak gebruikt door de gebruiker. | Waar, onwaar |
| **Apparaat wordt ongebruikelijk gebruikt tussen peers**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | Het apparaat wordt niet vaak gebruikt op de peers van de gebruiker. | Waar, onwaar |
| **Eerste keer dat het apparaat wordt waargenomen in de Tenant**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | Het apparaat is voor de eerste keer in de organisatie geobserveerd. | Waar, onwaar |
| **Het apparaat wordt ongebruikelijk gebruikt in de Tenant**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | Het apparaat wordt niet vaak gebruikt in de organisatie. | Waar, onwaar |
| 

#### <a name="other-device-related"></a>Ander apparaat-gerelateerd

| Verrijkings naam | [Basis lijn](#baseline-explained) (dagen) | Beschrijving | Voorbeeldwaarde |
| --- | --- | --- | --- |
| **Eerste keer dat de gebruiker is aangemeld bij het apparaat**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | Het doel apparaat is voor de eerste keer verbonden door de gebruiker. | Waar, onwaar |
| **Apparaten familie ongebruikelijk gebruikt in Tenant**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | De apparaten familie wordt niet vaak gebruikt in de organisatie. | Waar, onwaar |
| 

#### <a name="internet-service-provider-used-to-connect"></a>Internet serviceprovider die wordt gebruikt om verbinding te maken

| Verrijkings naam | [Basis lijn](#baseline-explained) (dagen) | Beschrijving | Voorbeeldwaarde |
| --- | --- | --- | --- |
| **Eerste keer dat de gebruiker verbinding heeft via de Internet provider**<br>*(FirstTimeUserConnectedViaISP)* | 30 | De Internet provider is voor de eerste keer door de gebruiker geobserveerd. | Waar, onwaar |
| **ISP ongebruikelijk gebruikt door gebruiker**<br>*(ISPUncommonlyUsedByUser)* | 10 | De Internet provider wordt niet vaak gebruikt door de gebruiker. | Waar, onwaar |
| **Internet provider wordt ongebruikelijk gebruikt tussen peers**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | De Internet provider wordt niet vaak gebruikt voor de peers van de gebruiker. | Waar, onwaar |
| **Eerste keer verbinding via Internet provider in Tenant**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | De Internet provider is voor de eerste keer in de organisatie geobserveerd. | Waar, onwaar |
| **ISP ongebruikelijk gebruikt in Tenant**<br>*(ISPUncommonlyUsedInTenant)* | 30 | De Internet provider wordt niet vaak gebruikt in de organisatie. | Waar, onwaar |
| 

#### <a name="resource-accessed"></a>Toegang tot bronnen

| Verrijkings naam | [Basis lijn](#baseline-explained) (dagen) | Beschrijving | Voorbeeldwaarde |
| --- | --- | --- | --- |
| **Eerste keer dat gebruikers toegang krijgen tot de resource**<br>*(FirstTimeUserAccessedResource)* | 180 | De resource is voor de eerste keer door de gebruiker gebruikt. | Waar, onwaar |
| **De resource is ongebruikelijk toegankelijk door de gebruiker**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | De resource wordt niet vaak gebruikt door de gebruiker. | Waar, onwaar |
| **Bron ongebruikelijk toegankelijk tussen peers**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | De resource is niet vaak toegankelijk via de peers van de gebruiker. | Waar, onwaar |
| **De eerste keer dat de resource wordt geopend in de Tenant**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | De resource is voor het eerst gebruikt door iedereen in de organisatie. | Waar, onwaar |
| **De resource is ongebruikelijk toegankelijk in de Tenant**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | De resource wordt niet vaak gebruikt in de organisatie. | Waar, onwaar |
| 

#### <a name="miscellaneous"></a>Diversen

| Verrijkings naam | [Basis lijn](#baseline-explained) (dagen) | Beschrijving | Voorbeeldwaarde |
| --- | --- | --- | --- |
| **Laatste keer dat de gebruiker de actie heeft uitgevoerd**<br>*(LastTimeUserPerformedAction)* | 180 | De laatste keer dat de gebruiker dezelfde actie heeft uitgevoerd. | <Timestamp> |
| **Een soort gelijke actie is niet uitgevoerd in het verleden**<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | Er is geen actie uitgevoerd in dezelfde resource provider door de gebruiker. | Waar, onwaar |
| **Bron-IP-locatie**<br>*(SourceIPLocation)* | *N.v.t.* | Het land dat is omgezet van het bron-IP-adres van de actie. | [Surrey, England] |
| **Ongebruikelijk hoog volume van bewerkingen**<br>*(UncommonHighVolumeOfOperations)* | 7 | Een gebruiker heeft een burst van vergelijk bare bewerkingen uitgevoerd binnen dezelfde provider | Waar, onwaar |
| **Ongebruikelijk aantal voorwaardelijke toegangs fouten van Azure AD**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | Een ongebruikelijk aantal gebruikers dat niet kan worden geverifieerd vanwege voorwaardelijke toegang | Waar, onwaar |
| **Ongebruikelijk aantal apparaten toegevoegd**<br>*(UnusualNumberOfDevicesAdded)* | 5 | Een gebruiker heeft een ongebruikelijk aantal apparaten toegevoegd. | Waar, onwaar |
| **Ongebruikelijk aantal verwijderde apparaten**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | Een gebruiker heeft een ongebruikelijk aantal apparaten verwijderd. | Waar, onwaar |
| **Ongebruikelijk aantal gebruikers dat is toegevoegd aan de groep**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | Een gebruiker heeft een ongebruikelijk aantal gebruikers toegevoegd aan een groep. | Waar, onwaar |
|