---
title: Een sleutel kluis verplaatsen naar een andere regio-Azure Key Vault | Microsoft Docs
description: Dit artikel bevat richt lijnen voor het verplaatsen van een sleutel kluis naar een andere regio.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: 1961d86e55f59233b1dc168e2de48b6b42b0cff0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728110"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Een Azure-sleutel kluis verplaatsen tussen regio's

Azure Key Vault biedt geen ondersteuning voor het verplaatsen van resources, waardoor het verplaatsen van een sleutel kluis van de ene regio naar een andere kan worden toegestaan. In dit artikel worden tijdelijke oplossingen behandeld voor organisaties die een bedrijf nodig hebben om een sleutel kluis naar een andere regio te verplaatsen. Voor elke oplossing geldt een beperking. Het is belang rijk om te begrijpen wat de gevolgen van deze tijdelijke oplossingen zijn voordat u ze in een productie omgeving probeert toe te passen.

Als u een sleutel kluis naar een andere regio wilt verplaatsen, maakt u een sleutel kluis in die andere regio en kopieert u elk afzonderlijk geheim hand matig van uw bestaande sleutel kluis naar de nieuwe sleutel kluis. U kunt dit doen door een van de volgende twee opties te gebruiken.

## <a name="design-considerations"></a>Overwegingen bij het ontwerpen

Houd rekening met de volgende concepten voordat u begint:

* Namen van sleutelkluizen zijn globaal uniek. U kunt een kluis naam niet opnieuw gebruiken.
* U moet uw toegangs beleid en netwerk configuratie-instellingen opnieuw configureren in de nieuwe sleutel kluis.
* U moet zacht verwijderen opnieuw configureren en beveiliging opschonen in de nieuwe sleutel kluis.
* Met de back-up-en herstel bewerking blijven uw instellingen voor het door draaien van gegevens niet behouden. Mogelijk moet u de instellingen opnieuw configureren.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>Optie 1: de back-up-en herstel opdrachten van de sleutel kluis gebruiken

U kunt met behulp van de back-upopdracht back-ups maken van elk afzonderlijk geheim, dezelfde sleutel en elk certificaat in uw kluis. Uw geheimen worden gedownload als een versleutelde blob. U kunt de BLOB vervolgens herstellen in de nieuwe sleutel kluis. Zie [Azure Key Vault-opdrachten](/powershell/module/azurerm.keyvault#key_vault)voor een lijst met opdrachten.

Het gebruik van de opdrachten backup en Restore heeft twee beperkingen:

* U kunt geen back-up maken van een sleutel kluis in één Geografie en deze herstellen in een andere geografie. Zie [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/)(Engelstalig) voor meer informatie.

* Met de back-upopdracht maakt u een back-up van alle versies van elk geheim. Als u een geheim hebt met een groot aantal vorige versies (meer dan 10), kan de aanvraag de maximaal toegestane grootte overschrijden en kan de bewerking mislukken.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>Optie 2: de sleutel kluis geheimen hand matig downloaden en uploaden

U kunt bepaalde geheime typen hand matig downloaden. U kunt certificaten bijvoorbeeld downloaden als een PFX-bestand. Met deze optie worden de geografische beperkingen voor sommige geheime typen, zoals certificaten, geëlimineerd. U kunt de PFX-bestanden uploaden naar elke sleutel kluis in elke gewenste regio. De geheimen worden gedownload in een indeling die niet met een wacht woord is beveiligd. U bent zelf verantwoordelijk voor het beveiligen van uw geheimen tijdens het verplaatsen.