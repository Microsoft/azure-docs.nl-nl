---
title: Problemen met de beveiliging van LDAP in Azure AD Domain Services oplossen | Microsoft Docs
description: Meer informatie over het oplossen van problemen met secure LDAP (LDAPS) voor een Azure Active Directory Domain Services beheerd domein
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e21ec7d8fdf7a5c7a8ba7a9f22aa5709581cbae6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618396"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Problemen met de beveiliging van de LDAP-verbinding met een Azure Active Directory Domain Services beheerd domein oplossen

Toepassingen en services die gebruikmaken van Lightweight Directory Access Protocol (LDAP) om te communiceren met Azure Active Directory Domain Services (Azure AD DS) kunnen worden [geconfigureerd voor het gebruik van secure LDAP](tutorial-configure-ldaps.md). Er moet een geschikt certificaat en de vereiste netwerk poorten zijn geopend voor een goede werking van de beveiliging van LDAP.

Dit artikel helpt u bij het oplossen van problemen met beveiligde LDAP-toegang in azure AD DS.

## <a name="common-connection-issues"></a>Algemene verbindingsproblemen

Raadpleeg de volgende stappen voor probleem oplossing als u problemen ondervindt bij het maken van verbinding met een met Azure AD DS beheerd domein met behulp van secure LDAP. Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met het beheerde domein:

* De uitgevers keten van het beveiligde LDAP-certificaat moet worden vertrouwd op de client. U kunt de basis certificerings instantie (CA) toevoegen aan het vertrouwde basis certificaat archief op de client om de vertrouwens relatie tot stand te brengen.
    * Zorg ervoor dat u [het certificaat exporteert en toepast op client computers][client-cert].
* Controleer of het beveiligde LDAP-certificaat voor uw beheerde domein de DNS-naam in het *onderwerp* of het kenmerk *alternatieve namen voor onderwerp* bevat.
    * Bekijk de [vereisten voor het beveiligde LDAP-certificaat][certs-prereqs] en maak indien nodig een vervangend certificaat.
* Controleer of de LDAP-client, zoals *ldp.exe* , verbinding maakt met het beveiligde LDAP-eind punt met behulp van een DNS-naam, niet op het IP-adres.
    * Het certificaat dat wordt toegepast op het beheerde domein bevat niet de IP-adressen van de service, alleen de DNS-namen.
* Controleer de DNS-naam waarmee de LDAP-client verbinding maakt. Het moet worden omgezet in het open bare IP-adres voor beveiligde LDAP op het beheerde domein.
    * Als de DNS-naam wordt omgezet naar het interne IP-adres, werkt u de DNS-record bij om om te zetten naar het externe IP-adres.
* Voor externe connectiviteit moet de netwerk beveiligings groep een regel bevatten waarmee het verkeer naar de TCP-poort 636 van Internet wordt toegestaan.
    * Als u verbinding kunt maken met het beheerde domein met behulp van beveiligde LDAP van resources die rechtstreeks zijn verbonden met het virtuele netwerk, maar niet met externe verbindingen, moet u [een regel voor de netwerk beveiligings groep maken om beveiligd LDAP-verkeer toe te staan][ldaps-nsg].

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen ondervindt, [opent u een ondersteunings aanvraag voor Azure][azure-support] voor aanvullende hulp bij het oplossen van problemen.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
