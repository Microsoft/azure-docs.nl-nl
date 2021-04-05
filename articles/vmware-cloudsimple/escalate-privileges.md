---
title: Azure VMware-oplossing door CloudSimple-CloudSimple-bevoegdheden te escaleren
description: Beschrijft hoe u CloudSimple-machtigingen kunt escaleren om beheer functies uit te voeren in de Privécloud-vCenter
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d481717a79856583d23f61107678d2ecd1af68d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895724"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>CloudSimple-bevoegdheden escaleren om beheer functies uit te voeren in de Privécloud-vCenter

De CloudSimple privileges aanpak is ontworpen om vCenter-gebruikers de benodigde bevoegdheden te geven voor het uitvoeren van normale bewerkingen. In sommige gevallen is het mogelijk dat een gebruiker aanvullende bevoegdheden nodig heeft om een bepaalde taak uit te voeren.  U kunt de bevoegdheden van een vCenter SSO-gebruiker voor een beperkte periode escaleren.

Redenen voor het escaleren van bevoegdheden kunnen het volgende omvatten:

* Configuratie van identiteits bronnen
* Gebruikersbeheer
* Verwijderen van gedistribueerde poort groep
* VCenter-oplossingen installeren (zoals back-upapps)
* Service accounts maken

> [!WARNING]
> Acties die worden uitgevoerd in de status van de escalated privilege kunnen een nadelige invloed hebben op uw systeem en kunnen ervoor zorgen dat uw systeem niet meer beschikbaar is. Voer alleen de vereiste acties uit tijdens de escalatie periode.

In de CloudSimple-Portal kunt u machtigingen voor de lokale gebruiker CloudOwner op de vCenter-SSO [escaleren](escalate-private-cloud-privileges.md) .  U kunt de bevoegdheid van de externe gebruiker alleen escaleren als er een aanvullende ID-provider is geconfigureerd op vCenter.  Voor escalatie van bevoegdheden moet u de geselecteerde gebruiker toevoegen aan de ingebouwde groep Administrators van vSphere.  Er kan slechts één gebruiker escalated privileges hebben.  Als u de bevoegdheden van een andere gebruiker wilt door verwijzen, moet u eerst de bevoegdheden van de huidige gebruikers deactiveren.

Gebruikers uit aanvullende identiteits bronnen moeten worden toegevoegd als leden van de groep CloudOwner.

> [!CAUTION]
> Nieuwe gebruikers moeten alleen worden toegevoegd aan de *Cloud-eigenaar-groep*, *Cloud-Global-cluster-admin groep*, *Cloud-Global-Storage-admin-Group*, Cloud-Global: *Network-Administrator-* Group of Cloud-Global:- *beheer groep*.  Gebruikers die zijn toegevoegd aan de groep *Administrators* , worden automatisch verwijderd.  Alleen service accounts moeten worden toegevoegd aan de groep *Administrators* en service accounts moeten worden gebruikt om u aan te melden bij de vSphere-webgebruikersinterface.

Tijdens de escalatie periode gebruikt CloudSimple geautomatiseerde controle met bijbehorende waarschuwings meldingen om onbedoelde wijzigingen in de omgeving te identificeren.
