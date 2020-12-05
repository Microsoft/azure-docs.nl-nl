---
title: Waarschuwingen voor netwerk beveiligings groepen oplossen in azure AD DS | Microsoft Docs
description: Meer informatie over het oplossen van problemen met configuratie waarschuwingen voor netwerk beveiligings groepen voor Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: d8f2e77b7225306844cec85363a2971eaac4eebd
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620253"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Bekende problemen: waarschuwingen voor netwerk configuratie in Azure Active Directory Domain Services

Om ervoor te zorgen dat toepassingen en services op de juiste wijze communiceren met een door Azure Active Directory Domain Services (Azure AD DS) beheerd domein, moeten er specifieke netwerk poorten open zijn om verkeer toe te staan. In azure beheert u de stroom van verkeer met behulp van netwerk beveiligings groepen. In de status van een beheerd domein van Azure AD DS wordt een waarschuwing weer gegeven als de vereiste regels voor de netwerk beveiligings groep niet aanwezig zijn.

Dit artikel helpt u bij het begrijpen en oplossen van veelvoorkomende waarschuwingen voor problemen met de configuratie van netwerk beveiligings groepen.

## <a name="alert-aadds104-network-error"></a>Waarschuwing AADDS104: netwerk fout

### <a name="alert-message"></a>Waarschuwings bericht

*Micro soft kan de domein controllers voor dit beheerde domein niet bereiken. Dit kan gebeuren als een netwerk beveiligings groep (NSG) die in uw virtuele netwerk is geconfigureerd, de toegang tot het beheerde domein blokkeert. Een andere mogelijke reden is als er een door de gebruiker gedefinieerde route is die inkomend verkeer van het Internet blokkeert.*

Ongeldige regels voor netwerk beveiligings groepen zijn de meest voorkomende oorzaak van netwerk fouten voor Azure AD DS. De netwerk beveiligings groep voor het virtuele netwerk moet toegang tot specifieke poorten en protocollen toestaan. Als deze poorten zijn geblokkeerd, kan het Azure-platform het beheerde domein niet bewaken of bijwerken. De synchronisatie tussen de Azure AD-Directory en Azure AD DS wordt ook beïnvloed. Zorg ervoor dat u de standaard poorten opent om te voor komen dat de service wordt onderbroken.

## <a name="default-security-rules"></a>Standaardbeveiligingsregels

De volgende standaard regels voor binnenkomend en uitgaand verkeer worden toegepast op de netwerk beveiligings groep voor een beheerd domein. Met deze regels blijven Azure AD DS beveiligd en kan het Azure-platform het beheerde domein bewaken, beheren en bijwerken.

### <a name="inbound-security-rules"></a>Inkomende beveiligingsregels

| Prioriteit | Naam | Poort | Protocol | Bron | Doel | Actie |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | Alle | Toestaan |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | Alle | Toestaan |
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | Alle | Toestaan |
| 65000    | AllVnetInBound | Alle | Alle | VirtualNetwork | VirtualNetwork | Toestaan |
| 65001    | AllowAzureLoadBalancerInBound | Alle | Alle | AzureLoadBalancer | Alle | Toestaan |
| 65500    | DenyAllInBound | Alle | Alle | Alle | Alle | Weigeren |

> [!NOTE]
> Mogelijk hebt u ook een extra regel waarmee inkomend verkeer wordt toegestaan als u [beveiligde LDAP configureert][configure-ldaps]. Deze extra regel is vereist voor de juiste LDAPS-communicatie.

### <a name="outbound-security-rules"></a>Uitgaande beveiligingsregels

| Prioriteit | Naam | Poort | Protocol | Bron | Doel | Actie |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Alle | Alle | VirtualNetwork | VirtualNetwork | Toestaan |
| 65001    | AllowAzureLoadBalancerOutBound | Alle | Alle |  Alle | Internet | Toestaan |
| 65500    | DenyAllOutBound | Alle | Alle | Alle | Alle | Weigeren |

>[!NOTE]
> Azure AD DS heeft onbeperkte uitgaande toegang nodig van het virtuele netwerk. Het is niet raadzaam extra regels te maken die de uitgaande toegang voor het virtuele netwerk beperken.

## <a name="verify-and-edit-existing-security-rules"></a>Bestaande beveiligings regels controleren en bewerken

Voer de volgende stappen uit om de bestaande beveiligings regels te controleren en te controleren of de standaard poorten open zijn:

1. In de Azure Portal zoekt en selecteert u **netwerk beveiligings groepen**.
1. Kies de netwerk beveiligings groep die is gekoppeld aan uw beheerde domein, zoals *AADDS-contoso.com-NSG*.
1. De bestaande binnenkomende en uitgaande beveiligings regels worden weer gegeven op de pagina **overzicht** .

    Controleer de regels voor binnenkomend en uitgaand verkeer en vergelijkt u met de lijst met vereiste regels in de vorige sectie. Selecteer, indien nodig, alle aangepaste regels die het vereiste verkeer blok keren. Als een van de vereiste regels ontbreekt, voegt u in de volgende sectie een regel toe.

    Nadat u regels hebt toegevoegd of verwijderd om het vereiste verkeer toe te staan, wordt de status van het beheerde domein automatisch binnen twee uur bijgewerkt en wordt de waarschuwing verwijderd.

### <a name="add-a-security-rule"></a>Een beveiligingsregel toevoegen

Als u een ontbrekende beveiligings regel wilt toevoegen, voert u de volgende stappen uit:

1. In de Azure Portal zoekt en selecteert u **netwerk beveiligings groepen**.
1. Kies de netwerk beveiligings groep die is gekoppeld aan uw beheerde domein, zoals *AADDS-contoso.com-NSG*.
1. Klik onder **instellingen** in het linkerdeel venster op *inkomende beveiligings regels* of *uitgaande beveiligings regels* , afhankelijk van de regel die u wilt toevoegen.
1. Selecteer **toevoegen** en maak vervolgens de vereiste regel op basis van de poort, het Protocol, de richting, enzovoort. Wanneer u klaar bent, selecteert u **OK**.

Het kan even duren voordat de beveiligings regel is toegevoegd en weer gegeven in de lijst.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen ondervindt, [opent u een ondersteunings aanvraag voor Azure][azure-support] voor aanvullende hulp bij het oplossen van problemen.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
