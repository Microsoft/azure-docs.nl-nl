---
title: Toegang tot Key Vault achter een firewall - Azure Key Vault | Microsoft Docs
description: Informatie over welke poorten, hosts en IP-adressen moeten worden geopend om een sleutelkluis-clienttoepassing achter een firewall toegang te geven tot de sleutelkluis.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: ca5842fb268c20f8ae58eb5f683229c4ae3919f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93289160"
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>Toegang tot Azure Key Vault achter een firewall

## <a name="what-ports-hosts-or-ip-addresses-should-i-open-to-enable-my-key-vault-client-application-behind-a-firewall-to-access-key-vault"></a>Welke poorten, hosts en IP-adressen moet ik openen om mijn sleutelkluis-clienttoepassing achter een firewall toegang te geven tot de sleutelkluis?

Als u toegang wilt krijgen tot een key vault, moet de clienttoepassing voor de key vault toegang hebben tot meerdere eindpunten voor verschillende functies:

* Verificatie via Azure Active Directory (Azure AD).
* Beheer van Azure Key Vault. Dit omvat het maken, lezen, bijwerken, verwijderen en instellen van toegangsbeleid via Azure Resource Manager.
* Toegang tot en beheer van objecten (sleutels en geheimen) die zijn opgeslagen in de Key Vault zelf die via het Key Vault-specifieke eindpunt lopen (bijvoorbeeld `https://yourvaultname.vault.azure.net`).  

Er zijn enkele verschillen, afhankelijk van uw configuratie en omgeving.

## <a name="ports"></a>Poorten

Al het verkeer naar de key vault voor de drie functies (verificatie, beheer en toegang tot gegevenslaag) gaat via HTTPS: poort 443. Voor CRL gaat het verkeer soms echter via HTTP (poort 80). Clients die ondersteuning bieden voor OCSP, mogen CRL niet bereiken, maar kunnen soms [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl) bereiken.  

## <a name="authentication"></a>Verificatie

Key vault-clienttoepassingen moet toegang hebben tot Azure Active Directory-eindpunten voor verificatie. Welk eindpunt wordt gebruikt, hangt af van de configuratie van de Azure AD-tenant en het type principal (gebruikers-principal of service-principal) en het type account, bijvoorbeeld een Microsoft-account of een werk- of schoolaccount.  

| Type principal | Eindpunt:poort |
| --- | --- |
| Gebruiker gebruikt Microsoft-account<br> (bijvoorbeeld, user@hotmail.com) |**Wereldwijd:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure van de Amerikaanse overheid:**<br> login.microsoftonline.us:443<br><br>**Azure Duitsland:**<br> login.microsoftonline.de:443<br><br> en <br>login.live.com:443 |
| Gebruiker of service-principal gebruikt een werk- of schoolaccount met Microsoft Azure Active Directory (bijvoorbeeld user@contoso.com) |**Wereldwijd:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure van de Amerikaanse overheid:**<br> login.microsoftonline.us:443<br><br>**Azure Duitsland:**<br> login.microsoftonline.de:443 |
| Gebruiker of service-principal gebruikt een werk- of schoolaccount, plus Active Directory Federation Services (AD FS) of andere federatief eindpunt (bijvoorbeeld user@contoso.com) |Alle eindpunten voor een werk- of schoolaccount, plus AD FS- of andere federatieve eindpunten |

Er zijn andere, mogelijk complexe scenario's. Raadpleeg [Azure Active Directory-verificatiestroom](../../active-directory/develop/authentication-vs-authorization.md), [Toepassingen integreren met Azure Active Directory](../../active-directory/develop/active-directory-how-to-integrate.md) en [Active Directory-verificatieprotocollen](/previous-versions/azure/dn151124(v=azure.100)) voor meer informatie.  

## <a name="key-vault-management"></a>Key Vault-beheer

Voor Key Vault-beheer (CRUD en toegangsbeleid instellen), moet de clienttoepassing voor de key vault toegang hebben tot het Azure Resource Manager-eindpunt.  

| Type bewerking | Eindpunt:poort |
| --- | --- |
| Bewerkingen voor de controlelaag van Key Vault<br> via Azure Resource Manager |**Wereldwijd:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure van de Amerikaanse overheid:**<br> management.usgovcloudapi.net:443<br><br> **Azure Duitsland:**<br> management.microsoftazure.de:443 |
| Microsoft Graph API |**Wereldwijd:**<br> graph.microsoft.com:443<br><br> **Azure China:**<br> graph.chinacloudapi.cn:443<br><br> **Azure van de Amerikaanse overheid:**<br> graph.microsoft.com:443<br><br> **Azure Duitsland:**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Key Vault-bewerkingen

Voor alle beheer- en cryptografiebewerkingen van het key vault-object (sleutels en geheimen) moet de key vault-client toegang hebben tot het eindpunt van de key vault. Het DNS-achtervoegsel van het eindpunt verschilt afhankelijk van de locatie van de key vault. Het eindpunt van de key vault heeft de indeling *kluisnaam*.*regiospecifiek-dns-achtervoegsel*, zoals beschreven in de onderstaande tabel.  

| Type bewerking | Eindpunt:poort |
| --- | --- |
| Bewerkingen waaronder cryptografiebewerkingen voor sleutels, het maken, lezen, bijwerken en verwijderen van sleutels en geheimen, instellen of ophalen van tags en andere kenmerken voor key vault-objecten (sleutels of geheimen) |**Wereldwijd:**<br> &lt;kluisnaam&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;kluisnaam&gt;.vault.azure.cn:443<br><br> **Azure van de Amerikaanse overheid:**<br> &lt;kluisnaam&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Duitsland:**<br> &lt;kluisnaam&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>IP-adresbereiken

De Key Vault-service maakt gebruik van andere Azure-bronnen zoals de PaaS-infrastructuur. Daarom is het niet mogelijk om een specifiek bereik van IP-adressen op te geven die key vault-eindpunten op een bepaald moment hebben. Als uw firewall alleen IP-adresbereiken ondersteunt, raadpleeg dan de documenten over Microsoft Azure Datacenter IP Ranges die beschikbaar zijn op:
* [Openbaar](https://www.microsoft.com/en-us/download/details.aspx?id=56519)
* [US Gov](https://www.microsoft.com/en-us/download/details.aspx?id=57063)
* [Duitsland](https://www.microsoft.com/en-us/download/details.aspx?id=57064)
* [China](https://www.microsoft.com/en-us/download/details.aspx?id=57062)

Authentication and Identity (Azure Active Directory) is een wereldwijde service waarmee failover naar andere regio's kan worden uitgevoerd of verkeer kan worden verplaatst zonder dit te melden. In dit scenario moeten alle IP-adresbereiken bij [IP-adressen voor Authentication and Identity](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity_ip) worden toegevoegd aan de firewall.

## <a name="next-steps"></a>Volgende stappen

Als u vragen hebt over Key Vault, ga dan naar de [Microsoft-pagina met vragen en antwoorden voor Azure Key Vault](/answers/topics/azure-key-vault.html).