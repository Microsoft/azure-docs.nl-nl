---
title: Wat is op groepen gebaseerde licentie verlening-Azure Active Directory | Microsoft Docs
description: Meer informatie over het Azure Active Directory op groepen gebaseerde licentie verlening, met inbegrip van de werking en aanbevolen procedures.
services: active-directory
keywords: Azure AD-licenties
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0490334c759da6ef7ba7ff2535f5f561cdb7a9bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92369808"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Wat is op een groep gebaseerde licentie verlening in Azure Active Directory?

Betaalde micro soft-Cloud Services, zoals Microsoft 365, Enterprise Mobility + Security, Dynamics 365 en andere vergelijk bare producten, vereisen licenties. Deze licenties worden toegewezen aan elke gebruiker die toegang tot deze services nodig heeft. Om licenties te beheren gebruiken beheerders een van de beheerportals (Office of Azure) en PowerShell-cmdlets. Azure AD (Azure Active Directory) is de onderliggende infrastructuur die ondersteuning biedt voor identiteitsbeheer in alle Microsoft-cloudservices. In Azure AD wordt informatie opgeslagen over de statussen van licentietoewijzingen voor gebruikers.

Tot nu toe konden licenties alleen worden toegewezen op het niveau van de individuele gebruiker, wat grootschalig beheer moeilijk maakt. Als u bijvoorbeeld gebruikerslicenties wilt toevoegen of verwijderen op basis van wijzigingen in de organisatie, zoals wanneer gebruikers toetreden tot de organisatie of tot een afdeling of deze verlaten, moet een beheerder vaak een complex PowerShell-script schrijven. Met dit script worden afzonderlijke aanroepen naar de cloudservice gemaakt.

Azure AD bevat nu licenties op basis van groepen om met dergelijke uitdagingen om te gaan. U kunt een of meer productlicenties toewijzen aan een groep. De licenties worden in Azure AD toegewezen aan alle leden van de groep. Wanneer er nieuwe gebruikers lid worden van de groep, worden aan hen de juiste licenties toegewezen. Wanneer zij de groep weer verlaten, worden deze licenties verwijderd. Dit licentie beheer elimineert de nood zaak voor het automatiseren van licentie beheer via Power shell om wijzigingen in de organisatie-en afdelings structuur per gebruiker weer te geven.

## <a name="licensing-requirements"></a>Licentievereisten
U moet een van de volgende licenties hebben voor het gebruik van op groepen gebaseerde licentie verlening:

- Betaalde of proef abonnement voor Azure AD Premium P1 en hoger

- Betaalde of proef versie van Office 365 Enter prise E3 of Office 365 a3 of Office 365 GCC G3 of Office 365 E3 voor GCCH of Office 365 E3 voor DOD en hoger

### <a name="required-number-of-licenses"></a>Vereist aantal licenties
Voor elke groep waaraan een licentie is toegewezen, moet u ook een licentie voor elk uniek lid hebben. Hoewel u niet elk lid van de groep een licentie hoeft toe te wijzen, moet u ten minste voldoende licenties hebben om alle leden op te nemen. Als u bijvoorbeeld 1.000 unieke leden hebt die deel uitmaken van gelicentieerde groepen in uw Tenant, moet u ten minste 1.000 licenties hebben om te voldoen aan de licentie overeenkomst.

## <a name="features"></a>Functies

Dit zijn de belangrijkste functies van licenties op basis van groepen:

- Licenties kunnen worden toegewezen aan elke beveiligingsgroep in Azure AD. Beveiligings groepen kunnen worden gesynchroniseerd vanuit on-premises met behulp van Azure AD Connect. U kunt beveiligingsgroepen ook rechtstreeks in Azure AD maken (ook wel groepen met het kenmerk Alleen-cloud genoemd), of automatisch via de functie voor dynamische Azure AD-groepen.

- Wanneer een productlicentie is toegewezen aan een groep, kan de beheerder een of meer serviceplannen in het product uitschakelen. Normaal gesp roken wordt deze toewijzing uitgevoerd wanneer de organisatie nog niet klaar is om te beginnen met het gebruik van een service die is opgenomen in een product. De beheerder kan bijvoorbeeld Microsoft 365 toewijzen aan een afdeling, maar de Yammer-service tijdelijk uitschakelen.

- Alle Microsoft-cloudservices waarvoor licenties op gebruikersniveau zijn vereist, worden ondersteund. Deze ondersteuning omvat alle Microsoft 365 producten, Enterprise Mobility + Security en Dynamics 365.

- Op groep gebaseerde licentie verlening is momenteel alleen beschikbaar via de [Azure Portal](https://portal.azure.com). Als u voornamelijk andere beheer portals gebruikt voor gebruikers-en groeps beheer, zoals het [Microsoft 365-beheer centrum](https://admin.microsoft.com), kunt u dit gewoon voortzetten. U moet Azure Portal wel gebruiken om licenties op groepsniveau te beheren.

- Wijzigingen in licenties die het resultaat zijn van wijzigingen in groepslidmaatschappen, worden automatisch beheerd in Azure AD. Wijzigingen in licenties worden meestal binnen enkele minuten na een lidmaatschapswijziging van kracht.

- Een gebruiker kan lid zijn van meerdere groepen waarvoor licentiebeleid is opgesteld. Een gebruiker kan ook een aantal licenties hebben die rechtstreeks zijn toegewezen, buiten een groep. De resulterende gebruikersstatus is een combinatie van alle toegewezen product- en servicelicenties. Als een gebruiker dezelfde licentie uit meerdere bronnen heeft toegewezen, wordt de licentie slechts één keer gebruikt.

- In sommige gevallen kunnen er geen licenties worden toegewezen aan een gebruiker. Bijvoorbeeld wanneer er niet voldoende licenties beschikbaar zijn in de tenant, of wanneer tegelijkertijd conflicterende services zijn toegewezen. Beheerders hebben toegang tot informatie over gebruikers voor wie de groepslicenties niet volledig zijn verwerkt in Azure AD. Ze kunnen vervolgens een herstelbewerking uitvoeren op basis van deze informatie.

## <a name="your-feedback-is-welcome"></a>We stellen uw feedback op prijs!

Als u feedback of functie aanvragen hebt, kunt u deze met ons delen met behulp van [het Azure AD-beheer forum](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over scenario’s voor licentiebeheer via licenties op basis van groepen, raadpleegt u:

* [Licenties toewijzen aan een groep in Azure Active Directory](../enterprise-users/licensing-groups-assign.md)
* [Licentieproblemen voor een groep vaststellen en oplossen in Azure Active Directory](../enterprise-users/licensing-groups-resolve-problems.md)
* [Gebruikers met een afzonderlijke licentie migreren naar licenties op basis van groepen in Azure Active Directory](../enterprise-users/licensing-groups-migrate-users.md)
* [Gebruikers tussen product licenties migreren met op groepen gebaseerde licentie verlening in Azure Active Directory](../enterprise-users/licensing-groups-change-licenses.md)
* [Aanvullende scenario’s voor Azure Active Directory-licenties op basis van groepen](../enterprise-users/licensing-group-advanced.md)
* [Power shell-voor beelden voor op groep gebaseerde licentie verlening in Azure Active Directory](../enterprise-users/licensing-ps-examples.md)
