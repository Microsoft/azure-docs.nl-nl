---
title: Beschikbaarheid en redundantie in Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Meer informatie over beschikbaarheid en redundantie in Azure Key Vault.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 3c5afc92044fcb109bedd38298b0b027ebeb437d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749686"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Beschikbaarheid en redundantie in Azure Key Vault

Azure Key Vault bevat meerdere lagen van redundantie om ervoor te zorgen dat uw sleutels en geheimen beschikbaar blijven voor uw toepassing, zelfs als afzonderlijke onderdelen van de service niet werken.

> [!NOTE]
> Deze informatie is van toepassing op kluizen. Voor beheerde HSM-groepen wordt een ander model voor hoge beschikbaarheid en herstel na noodgevallen gebruikt. Raadpleeg de [documentatie over herstel na noodgevallen voor beheerde HSM's](../managed-hsm/disaster-recovery-guide.md) voor meer informatie.

De inhoud van uw sleutelkluis wordt gerepliceerd binnen de regio en naar een secundaire regio op minstens 150 mijl afstand, maar binnen dezelfde geografische locatie voor het behoud van de hoge duurzaamheid van uw sleutels en geheimen. Raadpleeg [Azure-gekoppelde regio's](../../best-practices-availability-paired-regions.md) voor meer informatie over specifieke regioparen. De uitzondering op het model voor gekoppelde regio's is Brazilië - zuid, waarmee alleen de optie voor het behoud van gegevens in Brazilië - zuid wordt toegestaan. Brazilië - zuid maakt gebruik van ZRS (zone-redundante opslag) om uw gegevens drie keer te repliceren binnen één locatie/regio. Voor AKV Premium worden slechts 2 van de 3 regio's gebruikt om gegevens van de HSM's te repliceren.  

Als afzonderlijke onderdelen van de sleutelkluis-service niet meer werken, nemen alternatieve onderdelen in de regio het over om uw aanvraag te verwerken om ervoor te zorgen dat de functionaliteit niet wordt verminderd. U hoeft geen actie te ondernemen om dit proces te starten. Dit gebeurt automatisch en is transparant voor u.

In het zeldzame geval dat een volledige Azure-regio niet beschikbaar is, worden de aanvragen die u bij Azure Key Vault in die regio hebt gemaakt, automatisch doorgestuurd (*failed over*) naar een secundaire regio, behalve in het geval van de regio Brazilië - zuid. Wanneer de primaire regio weer beschikbaar is, worden de aanvragen teruggestuurd (*failed back*) naar de primaire regio. Nogmaals: u hoeft geen actie te ondernemen, omdat dit automatisch gebeurt.

In de regio Brazilië - zuid moet u een herstelprocedure voor uw Azure-sleutelkluizen plannen voor een scenario met een regionale storing. Als u een back-up wilt maken van uw Azure-sleutelkluis en deze wilt herstellen naar een regio van uw keuze, voert u de stappen uit die worden beschreven in [Back-up voor Azure Key Vault](backup.md). 

Dankzij dit ontwerp met hoge beschikbaarheid heeft Azure Key Vault geen downtime nodig voor onderhoudsactiviteiten.

U moet rekening houden met enkele waarschuwingen:

* In het geval van een failover van een regio kan het enkele minuten duren voordat een failover wordt uitgevoerd voor de service. Aanvragen die gedurende deze tijd worden ingediend vóór de failover, kunnen mislukken.
* Als u een privékoppeling gebruikt om verbinding te maken met uw sleutelkluis, kan het tot 20 minuten duren voordat de verbinding opnieuw tot stand wordt gebracht in het geval van een failover. 
* Tijdens de failover staat uw sleutelkluis in de modus alleen-lezen. Aanvragen die in deze modus worden ondersteund, zijn:
  * Certificaten vermelden
  * Certificaten ophalen
  * Geheimen vermelden
  * Geheimen ophalen
  * Sleutels vermelden
  * (Eigenschappen van) sleutels ophalen
  * Versleutelen
  * Ontsleutelen
  * Wrap
  * Unwrap
  * Verifiëren
  * Teken
  * Backup

* Tijdens de failover kunt u geen wijzigingen aanbrengen aan eigenschappen van de sleutelkluis. U kunt de configuraties en instellingen van het toegangsbeleid of de firewall niet wijzigen.

* Nadat een failback is uitgevoerd voor een failover, zijn alle aanvraagtypen (inclusief lees- *en* schrijfaanvragen) beschikbaar.
