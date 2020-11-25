---
title: StorSimple Apparaatbeheer-opslag accounts, abonnementen migreren
description: Meer informatie over het migreren van abonnementen, opslag accounts voor uw StorSimple-Apparaatbeheer service.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: a2d4aa8845472812cf1874b380c6cbf1e91e2149
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014819"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migratie van abonnementen en opslagaccounts die zijn gekoppeld aan de StorSimple Device Manager-service

Mogelijk moet u de StorSimple-service verplaatsen naar een nieuwe inschrijving of naar een nieuw abonnement. Deze migratie scenario's zijn account wijzigingen of wijzigingen in data centers. Gebruik de volgende tabel om te begrijpen welke van deze scenario's worden ondersteund, met inbegrip van de gedetailleerde stappen om te verplaatsen.

## <a name="account-changes"></a>Account wijzigingen

| Kunt u verplaatsen...| Ondersteund| Downtime| Ondersteunings proces voor Azure| Methode|
|-----|-----|-----|-----|-----|
| Een volledig abonnement (inclusief StorSimple-service-en opslag accounts) bij een andere inschrijving? | Ja       | Nee       | **Overdracht van inschrijving**<br>Gebruiken<li>Wanneer u een nieuwe Azure-toezeg ging aanschaft onder een nieuwe overeenkomst.</li><li>U wilt alle accounts en abonnementen migreren van de oude inschrijving naar het nieuwe. Dit omvat alle Azure-Services onder het oude abonnement.</li> | **Stap 1: Open een ondersteunings ticket voor Azure Enter prise-bewerkingen.**<li>Ga naar [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).</li><li> Selecteer **registratie beheer** en selecteer vervolgens **overzetten van de ene inschrijving naar een nieuwe inschrijving**.<br>**Stap 2: Geef de gevraagde gegevens op**<br>Behoort<li>registratie nummer van de bron</li><li> nummer van de doel registratie</li><li>ingangs datum van overdracht|
| StorSimple-service van een bestaand account naar een nieuwe inschrijving?    | Ja       | Nee       | **Account overdracht**<br>Gebruiken<li>Wanneer u geen volledige overdracht wilt uitvoeren.</li><li>U wilt specifieke accounts alleen naar een nieuwe inschrijving verplaatsen.</li>| **Stap 1: Open een ondersteunings ticket voor Azure Enter prise-bewerkingen.**<li>Ga naar [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).</li><li>Selecteer **registratie beheer** en selecteer vervolgens **een EA-account naar een nieuwe registratie overdragen**.<br>**Stap 2: Geef de gevraagde gegevens op**<br>Behoort<li>registratie nummer van de bron</li><li> nummer van de doel registratie</li><li>ingangs datum van overdracht|
| StorSimple-service van een abonnement op een ander abonnement?      | Nee        |    Ja         | Geen, hand matig proces|<li>Migreer gegevens van het StorSimple-apparaat.</li><li>De fabrieks instellingen van het apparaat worden opnieuw ingesteld. Hierdoor worden alle lokale gegevens op het apparaat verwijderd.</li><li>Registreer het apparaat bij het nieuwe abonnement bij een StorSimple-Apparaatbeheer service.</li><li>Migreer de gegevens terug naar het apparaat.|
|Kan ik het eigendom van een Azure-abonnement overdragen naar een andere map? | Ja       | Nee       | Een bestaand abonnement koppelen aan uw Azure AD-adres lijst | Raadpleeg [een bestaand abonnement koppelen aan uw Azure AD-adres lijst](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Het kan maximaal 10 minuten duren voordat alles goed wordt weergegeven.|
| StorSimple-apparaat van een StorSimple Apparaatbeheer service naar een andere service in een andere regio?      | Nee        | Ja            | Geen, hand matig proces |Hetzelfde als hierboven.|
| Opslag account naar een nieuw abonnement of een nieuwe resource groep?     | Ja        | Nee             |Het opslag account verplaatsen naar een ander abonnement of een andere resource groep |Als de toegangs sleutels van het opslag account na de verplaatsing zijn bijgewerkt, moet de gebruiker de toegangs sleutels hand matig configureren voor het gemigreerde opslag account via de StorSimple-Apparaatbeheer service.|
| Klassiek opslag account naar een Azure Resource Manager Storage-account      | Ja        | Nee             |Migreren van een klassieke implementatie naar Azure Resource Manager |<li>Ga naar [een klassiek opslag account migreren](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account)voor gedetailleerde instructies voor het migreren van een opslag account van klassiek naar Azure Resource Manager.</li><li> Als de toegangs sleutels van het opslag account na de migratie worden bijgewerkt, moet de gebruiker de toegangs sleutels voor het gemigreerde opslag account synchroniseren via de StorSimple-Apparaatbeheer service. Zo zorgt u ervoor dat de StorSimple-apparaten normaal blijven functioneren en de primaire/back-upgegevens kunnen laag maken naar Azure. Ga naar de [werk stroom voor rotatie](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)voor gedetailleerde instructies voor het synchroniseren van toegangs sleutels.</li><li> In het geval van een StorSimple Cloud Appliance, als het klassieke opslag account is gemigreerd, maar de onderliggende virtuele machine nog steeds in het klassieke systeem blijft, moet het apparaat goed functioneren. Als de onderliggende virtuele machine voor het Cloud apparaat wordt gemigreerd, zal de functionaliteit voor deactiveren en verwijderen niet werken.</li><li> U moet een nieuw StorSimple-Cloud toestel maken in de Azure Portal en vervolgens een failover uitvoeren van de oudere Cloud apparaten. U kunt geen StorSimple Cloud Appliance maken in de nieuwe Azure Portal met behulp van een klassiek opslag account, ze moeten een Azure Resource Manager-opslag account hebben. Ga voor meer informatie naar [implementeren en beheren van een StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).</li>|

## <a name="datacenter-changes"></a>Wijzigingen in data centers

| Kunt u verplaatsen...| Ondersteund|Downtime| Ondersteunings proces voor Azure| Methode|
|-----|-----|-----|-----|-----|
| Een StorSimple-service van een Azure-Data Center naar een andere? | Nee | Ja |Geen, hand matig proces  |<li>Migreer gegevens van het StorSimple-apparaat.</li><li>De fabrieks instellingen van het apparaat worden opnieuw ingesteld. Hierdoor worden alle lokale gegevens op het apparaat verwijderd.</li><li>Registreer het apparaat bij het nieuwe abonnement bij een nieuwe StorSimple-Apparaatbeheer service.</li><li>Migreer de gegevens terug naar het apparaat.|
| Een opslag account van een Azure-Data Center naar een andere? | Nee |Ja  |Geen, hand matig proces  | Hetzelfde als hierboven.|

## <a name="next-steps"></a>Volgende stappen

* [StorSimple Apparaatbeheer-service implementeren](storsimple-8000-manage-service.md)
* [StorSimple 8000 Series-apparaat implementeren in Azure Portal](storsimple-8000-deployment-walkthrough-u2.md)
