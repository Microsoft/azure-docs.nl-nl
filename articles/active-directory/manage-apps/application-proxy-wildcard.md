---
title: Joker tekens voor toepassingen in de Azure AD-toepassingsproxy
description: Meer informatie over het gebruik van Joker toepassingen in de Azure Active Directory toepassings proxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/06/2018
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a532ae9485efa9571137130d32ba0827728e8094
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166867"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Joker tekens voor toepassingen in de Azure Active Directory toepassings proxy

In Azure Active Directory (Azure AD) kan het configureren van een groot aantal on-premises toepassingen snel onbeheerbaar worden en worden er onnodige Risico's voor configuratie fouten geïntroduceerd als veel van de verschillende gebruikers dezelfde instellingen nodig hebben. Met [Azure AD-toepassingsproxy](application-proxy.md)kunt u dit probleem oplossen met behulp van de functie voor het publiceren van joker tekens voor het publiceren en beheren van veel toepassingen tegelijk. Dit is een oplossing waarmee u het volgende kunt doen:

- Vereenvoudig uw administratieve overhead
- Verminder het aantal mogelijke configuratie fouten
- Uw gebruikers veilig toegang bieden tot meer bronnen

In dit artikel vindt u de informatie die u nodig hebt voor het configureren van de toepassing voor het publiceren van joker tekens in uw omgeving.

## <a name="create-a-wildcard-application"></a>Een Joker toepassing maken

U kunt een Joker teken toepassing (*) maken als u een groep toepassingen met dezelfde configuratie hebt. Potentiële kandidaten voor een Joker toepassing zijn toepassingen die de volgende instellingen delen:

- De groep gebruikers die toegang hebben
- De SSO-methode
- Het toegangs Protocol (http, https)

U kunt toepassingen publiceren met Joker tekens als de interne en externe Url's de volgende indeling hebben:

> http (s)://*.\<domain\>

Bijvoorbeeld: `http(s)://*.adventure-works.com`.

Hoewel de interne en externe Url's verschillende domeinen als best practice kunnen gebruiken, moeten ze hetzelfde zijn. Wanneer u de toepassing publiceert, ziet u een fout melding als een van de Url's geen joker teken heeft.

Het maken van een Joker teken toepassing is gebaseerd op dezelfde [toepassings publicatie stroom](application-proxy-add-on-premises-application.md) die beschikbaar is voor alle andere toepassingen. Het enige verschil is dat u een Joker teken opneemt in de Url's en mogelijk de SSO-configuratie.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan deze vereisten hebt voldaan om aan de slag te gaan.

### <a name="custom-domains"></a>Aangepaste domeinen

Hoewel [aangepaste domeinen](application-proxy-configure-custom-domain.md) optioneel zijn voor alle andere toepassingen, zijn ze een vereiste voor Joker teken toepassingen. Voor het maken van aangepaste domeinen hebt u het volgende nodig:

1. Een geverifieerd domein maken in Azure.
1. Upload een TLS/SSL-certificaat in de PFX-indeling naar uw toepassings proxy.

Overweeg het gebruik van een certificaat met Joker tekens voor de toepassing die u wilt maken. 

Uit veiligheids overwegingen is dit een hard vereiste en worden geen joker tekens ondersteund voor toepassingen die geen aangepast domein voor de externe URL kunnen gebruiken.

### <a name="dns-updates"></a>DNS-updates

Wanneer u aangepaste domeinen gebruikt, moet u een DNS-vermelding maken met een CNAME-record voor de externe URL (bijvoorbeeld  `*.adventure-works.com` ) die verwijst naar de externe URL van het eind punt van de toepassings proxy. Voor Joker teken toepassingen moet de CNAME-record verwijzen naar de relevante externe URL:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Als u wilt controleren of u de CNAME juist hebt geconfigureerd, kunt u [nslookup](/windows-server/administration/windows-commands/nslookup) gebruiken op een van de doel eindpunten, bijvoorbeeld `expenses.adventure-works.com` .  Uw antwoord moet de reeds vermelde alias ( `<yourAADTenantId>.tenant.runtime.msappproxy.net` ) bevatten.

### <a name="using-connector-groups-assigned-to-an-app-proxy-cloud-service-region-other-than-the-default-region"></a>Connector groepen gebruiken die zijn toegewezen aan de Cloud service regio van een app-proxy, behalve de standaard regio
Als u connectors hebt geïnstalleerd in regio's die verschillen van de standaard-Tenant regio, kan het nuttig zijn om te wijzigen in welke regio uw connector groep is geoptimaliseerd voor het verbeteren van de prestaties die toegang hebben tot deze toepassingen. Zie voor meer informatie [connector groepen optimaliseren voor het gebruik van de dichtstbijzijnde Cloud service van de toepassings proxy](application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview).
 
Als de connector groep die is toegewezen aan de toepassing voor joker tekens een **andere regio dan uw standaard regio** gebruikt, moet u de CNAME-record bijwerken zodat deze verwijst naar een regionale specifieke externe URL. Gebruik de volgende tabel om de relevante URL te bepalen:

| Toegewezen regio connector | Externe URL |
| ---   | ---         |
| Azië | `<yourAADTenantId>.asia.tenant.runtime.msappproxy.net`|
| Australië  | `<yourAADTenantId>.aus.tenant.runtime.msappproxy.net` |
| Europa  | `<yourAADTenantId>.eur.tenant.runtime.msappproxy.net`|
| Noord-Amerika  | `<yourAADTenantId>.nam.tenant.runtime.msappproxy.net` |

## <a name="considerations"></a>Overwegingen

Hier volgen enkele overwegingen waar u rekening mee moet houden voor Joker teken toepassingen.

### <a name="accepted-formats"></a>Geaccepteerde indelingen

Voor Joker teken toepassingen moet de **interne URL** worden opgemaakt als `http(s)://*.<domain>` .

![Gebruik voor interne URL de notatie http (s)://*. \< domein>](./media/application-proxy-wildcard/22.png)

Wanneer u een **externe URL** configureert, moet u de volgende indeling gebruiken: `https://*.<custom domain>`

![Gebruik voor externe URL de notatie https://*. \< aangepast domein>](./media/application-proxy-wildcard/21.png)

Andere posities van het Joker teken, meerdere joker tekens of andere regex-reeksen worden niet ondersteund en veroorzaken fouten.

### <a name="excluding-applications-from-the-wildcard"></a>Toepassingen uitsluiten van het Joker teken

U kunt een toepassing uitsluiten van de toepassing voor joker tekens door

- De uitzonderings toepassing als gewone toepassing publiceren
- Het inschakelen van het Joker teken voor specifieke toepassingen via uw DNS-instellingen

Het publiceren van een toepassing als gewone toepassing is de voorkeurs methode om een toepassing uit te sluiten van een Joker teken. U moet de uitgesloten toepassingen vóór de Joker teken toepassingen publiceren om ervoor te zorgen dat uw uitzonde ringen vanaf het begin worden afgedwongen. De meest specifieke toepassing heeft altijd voor rang: een toepassing die is gepubliceerd `budgets.finance.adventure-works.com` , heeft voor rang op de toepassing `*.finance.adventure-works.com` , die op zijn beurt voor rang heeft op de toepassing `*.adventure-works.com` .

U kunt het Joker teken ook beperken om te werken voor specifieke toepassingen via uw DNS-beheer. Als best practice moet u een CNAME-vermelding maken die een Joker teken bevat en overeenkomt met de indeling van de externe URL die u hebt geconfigureerd. U kunt echter in plaats daarvan specifieke Url's voor de toepassing verwijzen naar de joker tekens. Bijvoorbeeld in plaats van `*.adventure-works.com` , punt `hr.adventure-works.com` `expenses.adventure-works.com` en `travel.adventure-works.com individually` naar `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` .

Als u deze optie gebruikt, moet u ook een andere CNAME-vermelding voor de waarde hebben, `AppId.domain` bijvoorbeeld, `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com` naar dezelfde locatie verwijzen. U kunt de **AppId** vinden op de pagina eigenschappen van toepassing van de toepassing voor joker tekens:

![Zoek de toepassings-ID op de eigenschappen pagina van de app](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>De URL van de start pagina instellen voor het deel venster MyApps

De Joker toepassing wordt weer gegeven met slechts één tegel in het [deel venster MyApps](https://myapps.microsoft.com). Deze tegel is standaard verborgen. De tegel weer geven en het land van de gebruiker op een specifieke pagina laten staan:

1. Volg de richt lijnen voor het [instellen van een start pagina-URL](application-proxy-configure-custom-home-page.md).
1. Stel **toepassing weer geven** op **waar** in op de eigenschappen pagina van de toepassing.

### <a name="kerberos-constrained-delegation"></a>Beperkte Kerberos-delegering

Voor toepassingen die gebruikmaken van [Kerberos-beperkte delegering (KCD) als SSO-methode](application-proxy-configure-single-sign-on-with-kcd.md), kan de SPN die wordt vermeld voor de SSO-methode ook een Joker teken hebben. De SPN kan bijvoorbeeld: `HTTP/*.adventure-works.com` . U moet nog steeds de afzonderlijke Spn's configureren op uw back-endservers (bijvoorbeeld `HTTP/expenses.adventure-works.com and HTTP/travel.adventure-works.com` ).

## <a name="scenario-1-general-wildcard-application"></a>Scenario 1: algemene Joker toepassing

In dit scenario hebt u drie verschillende toepassingen die u wilt publiceren:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Alle drie de toepassingen:

- Door al uw gebruikers worden gebruikt
- *Geïntegreerde Windows-verificatie* gebruiken
- Dezelfde eigenschappen hebben

U kunt de toepassing voor joker tekens publiceren met behulp van de stappen die worden beschreven in [toepassingen publiceren met behulp van Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md). In dit scenario wordt ervan uitgegaan:

- Een Tenant met de volgende ID: `000aa000-11b1-2ccc-d333-4444eee4444e`
- Er is een geverifieerd domein met de naam `adventure-works.com` geconfigureerd.
- Een **CNAME** -vermelding waarnaar wordt verwezen, `*.adventure-works.com` `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` is gemaakt.

Als u de [gedocumenteerde stappen](application-proxy-add-on-premises-application.md)volgt, maakt u een nieuwe toepassings proxy toepassing in uw Tenant. In dit voor beeld bevindt het Joker teken zich in de volgende velden:

- Interne URL:

    ![Voor beeld: Joker teken in interne URL](./media/application-proxy-wildcard/42.png)

- Externe URL:

    ![Voor beeld: Joker teken in externe URL](./media/application-proxy-wildcard/43.png)

- Interne toepassings-SPN:

    ![Voor beeld: Joker teken in SPN-configuratie](./media/application-proxy-wildcard/44.png)

Als u de toepassing voor joker tekens publiceert, kunt u toegang krijgen tot uw drie toepassingen door te navigeren naar de Url's die u gebruikt (bijvoorbeeld `travel.adventure-works.com` ).

De configuratie implementeert de volgende structuur:

![Toont de structuur die door de voorbeeld configuratie is geïmplementeerd](./media/application-proxy-wildcard/05.png)

| Kleur | Description |
| ---   | ---         |
| Blue  | Toepassingen worden expliciet gepubliceerd en zichtbaar in de Azure Portal. |
| Grijs  | Toepassingen die u via de bovenliggende toepassing kunt openen. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scenario 2: algemene Joker toepassing met uitzonde ring

In dit scenario hebt u naast de drie algemene toepassingen een andere toepassing, `finance.adventure-works.com` , die alleen toegankelijk moet zijn voor de financiële afdeling. Met de huidige toepassings structuur zou uw financiële toepassing toegankelijk zijn via de toepassing voor joker tekens en door alle werk nemers. Als u dit wilt wijzigen, sluit u uw toepassing uit van uw Joker teken door financiën te configureren als een afzonderlijke toepassing met meer beperkende machtigingen.

U moet ervoor zorgen dat er een CNAME-records bestaan die `finance.adventure-works.com` naar het specifieke eind punt van de toepassing verwijzen, opgegeven op de toepassings proxy pagina voor de toepassing. Voor dit scenario `finance.adventure-works.com` verwijst naar `https://finance-awcycles.msappproxy.net/` .

Aan de hand van de [gedocumenteerde stappen](application-proxy-add-on-premises-application.md)zijn voor dit scenario de volgende instellingen vereist:

- In de **interne URL** stelt u **Financiën** in in plaats van een Joker teken.

    ![Voor beeld: Finance instellen in plaats van een Joker teken in interne URL](./media/application-proxy-wildcard/52.png)

- In de **externe URL** stelt u **Financiën** in in plaats van een Joker teken.

    ![Voor beeld: Finance instellen in plaats van een Joker teken in externe URL](./media/application-proxy-wildcard/53.png)

- Interne SPN van de toepassing u stelt **Financiën** in in plaats van een Joker teken.

    ![Voor beeld: Finance instellen in plaats van een Joker teken in SPN-configuratie](./media/application-proxy-wildcard/54.png)

Deze configuratie implementeert het volgende scenario:

![Toont de configuratie die wordt geïmplementeerd door het voorbeeld scenario](./media/application-proxy-wildcard/09.png)

Omdat `finance.adventure-works.com` het een meer specifieke URL is dan `*.adventure-works.com` , heeft dit een hogere prioriteit. Gebruikers navigeren met `finance.adventure-works.com` de ervaring die is opgegeven in de toepassing Financiën resources. In dit geval kunnen alleen werk nemers met Financiën toegang krijgen `finance.adventure-works.com` .

Als u meerdere toepassingen hebt gepubliceerd voor financiering en u `finance.adventure-works.com` als een geverifieerd domein hebt, kunt u een andere joker toepassing publiceren `*.finance.adventure-works.com` . Omdat dit specifiekere is dan de algemene `*.adventure-works.com` , heeft dit een prioriteit als een gebruiker toegang heeft tot een toepassing in het domein Finance.

## <a name="next-steps"></a>Volgende stappen

- Zie [werken met aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md)voor meer informatie over **aangepaste domeinen**.
- Zie [toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md) voor meer informatie over het **publiceren van toepassingen**
