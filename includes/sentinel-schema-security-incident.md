---
title: bestand opnemen
description: bestand opnemen
services: azure-sentinel
author: yelevin
ms.service: azure-sentinel
ms.topic: include
ms.date: 06/28/2020
ms.author: yelevin
ms.custom: include file
ms.openlocfilehash: 63cb53dc60a718892d4bf86140e7fd51303bd61c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88761713"
---
### <a name="the-data-model-of-the-schema"></a>Het gegevens model van het schema

| Veld | Gegevenstype | Beschrijving |
| ---- | ---- | ---- |
| **AdditionalData** | dynamisch | Aantal waarschuwingen, aantal blad wijzers, aantal opmerkingen, product namen en tactieken voor waarschuwingen |
| **AlertIds** | dynamisch | Waarschuwingen waaruit het incident is gemaakt |
| **BookmarkIds** | dynamisch | Entiteiten met blad wijzer |
| **Classificatie** | tekenreeks | Classificatie incident sluiten |
| **ClassificationComment** | tekenreeks | Opmerking over het sluiten van incidenten |
| **ClassificationReason** | tekenreeks | Reden classificatie incident sluiten |
| **ClosedTime** | datum/tijd | Tijds tempel (UTC) van de laatste keer dat het incident is gesloten |
| **Opmerkingen** | dynamisch | Reacties op incidenten |
| **CreatedTime** | datum/tijd | Tijds tempel (UTC) van wanneer het incident is gemaakt |
| **Beschrijving** | tekenreeks | Beschrijving van het incident |
| **FirstActivityTime** | datum/tijd | Tijd van de eerste gebeurtenis |
| **FirstModifiedTime** | datum/tijd | Tijds tempel (UTC) van het tijdstip waarop het incident voor het eerst is gewijzigd |
| **IncidentName** | tekenreeks | Interne GUID |
| **IncidentNumber** | int |  |
| **IncidentUrl** | tekenreeks | Koppeling naar incident |
| **Labels** | dynamisch | Tags |
| **LastActivityTime** | datum/tijd | Tijd van de laatste gebeurtenis |
| **LastModifiedTime** | datum/tijd | Tijds tempel (UTC) van het tijdstip waarop het incident voor het laatst is gewijzigd <br>(de wijziging die wordt beschreven door de huidige record) |
| **ModifiedBy** | tekenreeks | Gebruiker of systeem dat het incident heeft gewijzigd |
| **Eigenaar** | dynamisch |  |
| **RelatedAnalyticRuleIds** | dynamisch | Regels van waaruit de waarschuwingen voor incidenten zijn geactiveerd |
| **Ernst** | tekenreeks | Ernst van het incident (hoog/gemiddeld/laag/informatief) |
| **SourceSystem** | tekenreeks | Constante (' Azure ') |
| **Status** | tekenreeks |  |
| **Tenant-ID** | tekenreeks |  |
| **TimeGenerated** | datum/tijd | Tijds tempel (UTC) van wanneer de huidige record is gemaakt <br>(na wijziging van het incident) |
| **Titel** | tekenreeks | 
| **Type** | tekenreeks | Constante (' SecurityIncident ') |
|
