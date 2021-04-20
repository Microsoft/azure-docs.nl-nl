---
title: Een sleutelkluis verplaatsen naar een andere regio - Azure Key Vault | Microsoft Docs
description: Dit artikel bevat richtlijnen voor het verplaatsen van een sleutelkluis naar een andere regio.
services: key-vault
author: msmbaldwin
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: ac2f6347776c2f5d230065b80b1c0336e21e181c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751792"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Een Azure-sleutelkluis tussen regio's verplaatsen

Azure Key Vault biedt geen ondersteuning voor een bewerking voor het verplaatsen van resources, zodat een sleutelkluis van de ene regio naar de andere kan worden verplaatst. In dit artikel worden tijdelijke oplossingen beschreven voor organisaties die een bedrijf nodig hebben om een sleutelkluis naar een andere regio te verplaatsen. Elke tijdelijke oplossing heeft beperkingen. Het is essentieel om inzicht te krijgen in de implicaties van deze tijdelijke oplossingen voordat u ze in een productieomgeving probeert toe te passen.

Als u een sleutelkluis naar een andere regio wilt verplaatsen, maakt u een sleutelkluis in die andere regio en kopieert u vervolgens handmatig elk afzonderlijk geheim van uw bestaande sleutelkluis naar de nieuwe sleutelkluis. U kunt dit doen met behulp van een van de volgende twee opties.

## <a name="design-considerations"></a>Overwegingen bij het ontwerpen

Voordat u begint, moet u rekening houden met de volgende concepten:

* Namen van sleutelkluizen zijn globaal uniek. U kunt de naam van een kluis niet opnieuw gebruiken.
* U moet uw toegangsbeleid en netwerkconfiguratie-instellingen opnieuw configureren in de nieuwe sleutelkluis.
* U moet de beveiliging voor het verwijderen en opsluizen van de nieuwe sleutelkluis opnieuw configureren.
* Met de back-up- en herstelbewerking blijven uw instellingen voor automatische herstelbewerkingen niet behouden. Mogelijk moet u de instellingen opnieuw configureren.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>Optie 1: de opdrachten voor back-up en herstel van de sleutelkluis gebruiken

U kunt een back-up maken van elk afzonderlijk geheim, elke sleutel en elk certificaat in uw kluis met behulp van de back-upopdracht. Uw geheimen worden gedownload als een versleutelde blob. Vervolgens kunt u de blob herstellen in uw nieuwe sleutelkluis. Zie voor een lijst met opdrachten [Azure Key Vault opdrachten.](/powershell/module/azurerm.keyvault#key_vault)

Het gebruik van de back-up- en herstelopdrachten heeft twee beperkingen:

* U kunt geen back-up maken van een sleutelkluis in één geografie en deze herstellen in een andere geografie. Zie Azure-geografieën [voor meer informatie.](https://azure.microsoft.com/global-infrastructure/geographies/)

* Met de back-upopdracht maakt u een back-up van alle versies van elk geheim. Als u een geheim hebt met een groot aantal vorige versies (meer dan 10), kan de aanvraag de maximaal toegestane grootte overschrijden en kan de bewerking mislukken.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>Optie 2: De sleutelkluisgeheimen handmatig downloaden en uploaden

U kunt bepaalde geheime typen handmatig downloaden. U kunt bijvoorbeeld certificaten downloaden als pfx-bestand. Met deze optie worden de geografische beperkingen voor sommige geheime typen, zoals certificaten, geëlimineerd. U kunt de PFX-bestanden uploaden naar elke sleutelkluis in elke regio. De geheimen worden gedownload in een indeling die niet is beveiligd met een wachtwoord. U bent verantwoordelijk voor het beveiligen van uw geheimen tijdens de overstap.