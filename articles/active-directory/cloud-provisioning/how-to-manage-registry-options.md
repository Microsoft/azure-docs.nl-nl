---
title: 'Azure AD Connect Cloud-inrichtings agent: Register opties beheren | Microsoft Docs'
description: In dit artikel wordt beschreven hoe u Register opties in de Azure AD Connect Cloud inrichtings agent beheert.
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad3bd938355d138e660958e34d046d7af03e75c7
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371103"
---
# <a name="manage-agent-registry-options"></a>Agent Register opties beheren

In deze sectie worden de Register opties beschreven die u kunt instellen om het gedrag van runtime-verwerking van de Azure AD Connect inrichtings agent te beheren. 

## <a name="configure-ldap-connection-timeout"></a>Time-out voor LDAP-verbinding configureren
Bij het uitvoeren van LDAP-bewerkingen op geconfigureerde Active Directory domein controllers, gebruikt de inrichtings agent standaard de time-outwaarde voor de verbinding van 30 seconden. Als uw domein controller meer tijd nodig heeft om te reageren, ziet u mogelijk het volgende fout bericht in het logboek bestand van de agent: 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

LDAP-Zoek bewerkingen kunnen langer duren als het zoek kenmerk niet is geïndexeerd. Als eerste stap, als u de bovenstaande fout ontvangt, controleert u eerst of het kenmerk Search/lookup is [geïndexeerd](https://docs.microsoft.com/windows/win32/ad/indexed-attributes). Als de zoek kenmerken worden geïndexeerd en de fout blijft bestaan, kunt u de time-out voor LDAP-verbindingen verhogen met behulp van de volgende stappen: 

1. Meld u als Administrator aan op de Windows-Server waarop de Azure AD Connect inrichtings agent wordt uitgevoerd.
1. Gebruik de menu opdracht *uitvoeren* om de REGI ster-editor (regedit.exe) te openen. 
1. Zoek de sleutel map **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Klik met de rechter muisknop en selecteer ' nieuwe-> teken reeks waarde '
1. Geef de naam op: `LdapConnectionTimeoutInMilliseconds`
1. Dubbel klik op de naam van de **waarde** en voer de waardegegevens in op `60000` milliseconden.
    > [!div class="mx-imgBorder"]
    > ![Time-out LDAP-verbinding](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. Start de Azure AD Connect Provisioning-Service opnieuw vanuit de *Services* -console.
1. Als u meerdere inrichtings agenten hebt geïmplementeerd, moet u deze register wijziging Toep assen op alle agents voor consistentie. 

## <a name="configure-referral-chasing"></a>Referentie Chasing configureren
Standaard worden [verwijzingen](https://docs.microsoft.com/windows/win32/ad/referrals)niet door de Azure AD Connect-inrichtings agent opsporen. Het is raadzaam om referentie Chasing in te scha kelen, ter ondersteuning van bepaalde HR-inrichtings scenario's voor inkomend verkeer, zoals: 
* De uniekheid van de UPN in meerdere domeinen controleren
* Verwijzingen naar Kruis domein beheer oplossen

Gebruik de volgende stappen om referentie Chasing in te scha kelen:

1. Meld u als Administrator aan op de Windows-Server waarop de Azure AD Connect inrichtings agent wordt uitgevoerd.
1. Gebruik de menu opdracht *uitvoeren* om de REGI ster-editor (regedit.exe) te openen. 
1. Zoek de sleutel map **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Klik met de rechter muisknop en selecteer ' nieuwe-> teken reeks waarde '
1. Geef de naam op: `ReferralChasingOptions`
1. Dubbel klik op de naam van de **waarde** en voer de waardegegevens in als `96` . Deze waarde komt overeen met de constante waarde voor `ReferralChasingOptions.All` en geeft aan dat zowel de substructuur als de verwijzing naar het basis niveau worden gevolgd door de agent. 
    > [!div class="mx-imgBorder"]
    > ![Verwijzing Chasing](media/how-to-manage-registry-options/referral-chasing.png)
1. Start de Azure AD Connect Provisioning-Service opnieuw vanuit de *Services* -console.
1. Als u meerdere inrichtings agenten hebt geïmplementeerd, moet u deze register wijziging Toep assen op alle agents voor consistentie.

> [!NOTE]
> U kunt controleren of de Register opties zijn ingesteld door [uitgebreide logboek registratie](how-to-troubleshoot.md#log-files)in te scha kelen. De logboeken die tijdens het opstarten van de agent worden gegenereerd, worden weer gegeven in de configuratie waarden die zijn opgenomen in het REGI ster. 

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)

