---
title: Een actieve DNS-naam migreren
description: Meer informatie over het migreren van een aangepaste DNS-domein naam die al aan een live-site is toegewezen, Azure App Service zonder uitval tijd.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: e1b50675bef0f883ff617b3098a742d3491b3c13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89484292"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Een actieve DNS-naam migreren naar Azure App Service

In dit artikel wordt beschreven hoe u een actieve DNS-naam migreert naar [Azure app service](../app-service/overview.md) zonder uitval tijd.

Wanneer u een live-site en de DNS-domein naam naar App Service migreert, is die DNS-naam al bezig met live verkeer. U kunt voor komen dat de DNS-omzetting tijdens de migratie wordt versneld door de actieve DNS-naam te koppelen aan uw App Service app preventief.

Zie [een bestaande aangepaste DNS-naam toewijzen aan Azure app service](app-service-web-tutorial-custom-domain.md)als u zich niet zorgen maakt over uitval tijd in DNS-omzetting.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze procedure:

- [Zorg ervoor dat uw app service-app zich niet in de laag gratis bevindt](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Bind de domein naam preventief

Wanneer u een aangepast domein preventief bindt, voert u de volgende handelingen uit voordat u wijzigingen aanbrengt in uw bestaande DNS-records:

- Domeineigendom controleren
- De domein naam voor uw app inschakelen

Wanneer u ten slotte uw aangepaste DNS-naam migreert van de oude-site naar de App App Service, wordt er geen downtime in DNS-omzetting.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="get-domain-verification-id"></a>Verificatie-id van domein ophalen

De domein verificatie-ID voor uw app ophalen door de stappen te volgen in de [domein verificatie-id ophalen](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id).

### <a name="create-domain-verification-record"></a>Domein verificatie record maken

Voeg een TXT-record toe voor domein verificatie om het domein eigendom te controleren. De hostnaam voor de TXT-record is afhankelijk van het type DNS-record type dat u wilt toewijzen. Zie de volgende tabel ( `@` Dit is meestal het hoofd domein):

| Voor beeld van DNS-record | TXT-host | TXT-waarde |
| - | - | - |
| \@ basis | _asuid_ | [Domein verificatie-ID voor uw app](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| www (sub) | _asuid. www_ | [Domein verificatie-ID voor uw app](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| \* vervanging | _asuid_ | [Domein verificatie-ID voor uw app](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |

Noteer op de pagina DNS-records het record type van de DNS-naam die u wilt migreren. App Service ondersteunt toewijzingen van CNAME-en A-records.

> [!NOTE]
> `*`Met Joker records worden subdomeinen met een bestaande CNAME-record niet gevalideerd. U moet mogelijk expliciet een TXT-record maken voor elk subdomein.

### <a name="enable-the-domain-for-your-app"></a>Het domein voor uw app inschakelen

1. Selecteer in de [Azure Portal](https://portal.azure.com), in de linkernavigatiebalk van de app-pagina, de optie **aangepaste domeinen**. 

    ![Menu voor aangepaste domeinen](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Selecteer op de pagina **aangepaste domeinen** de optie **aangepast domein toevoegen**.

    ![Hostnaam toevoegen](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Typ de Fully Qualified Domain Name die u wilt migreren, die overeenkomt met de TXT-record die u maakt, zoals `contoso.com` , `www.contoso.com` of `*.contoso.com` . Selecteer **Valideren**.

    De knop **Aangepast domein toevoegen** wordt geactiveerd. 

1. Zorg ervoor dat het **hostnaam-record type** is ingesteld op het DNS-record type dat u wilt migreren. Selecteer **Hostnaam toevoegen**.

    ![DNS-naam toevoegen aan de app](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Het kan even duren voor de nieuwe hostnaam wordt weergegeven op de pagina **Aangepaste domeinen** van de app. Vernieuw de browser voor om de gegevens bij te werken.

    ![CNAME-record toegevoegd](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    Uw aangepaste DNS-naam is nu ingeschakeld in uw Azure-app. 

## <a name="remap-the-active-dns-name"></a>De actieve DNS-naam opnieuw toewijzen

Het enige wat u nog moet doen, is het opnieuw toewijzen van uw actieve DNS-record om naar App Service te verwijzen. Op dit moment wijst het nog steeds naar uw oude site.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Het IP-adres van de app kopiëren (alleen een record)

Als u een CNAME-record opnieuw wilt toewijzen, kunt u deze sectie overs Laan. 

Als u een A-record opnieuw wilt toewijzen, hebt u het externe IP-adres van de App Service-app nodig. dit wordt weer gegeven op de pagina **aangepaste domeinen** .

Op de pagina **Aangepaste domeinen** kopieert u het IP-adres van de app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>De DNS-record bijwerken

Selecteer op de pagina DNS-records van uw domein provider de DNS-record die u opnieuw wilt toewijzen.

Voor het `contoso.com` voor beeld van het hoofd domein wijst u de A-of CNAME-record toe zoals de voor beelden in de volgende tabel: 

| FQDN-voor beeld | Recordtype | Host | Waarde |
| - | - | - | - |
| contoso.com (root) | A | `@` | IP-adres uit [Het IP-adres van de app kopiëren](#info) |
| www \. -contoso.com (sub) | CNAME | `www` | _&lt;appName>. azurewebsites.net_ |
| \*. contoso.com (Joker teken) | CNAME | _\*_ | _&lt;appName>. azurewebsites.net_ |

Sla uw wijzigingen op.

DNS-query's moeten meteen beginnen met het oplossen van problemen met de App Service-app nadat de DNS-doorgifte is uitgevoerd.

## <a name="migrate-domain-from-another-app"></a>Domein migreren vanuit een andere app

U kunt een actief aangepast domein migreren in azure, tussen abonnementen of binnen hetzelfde abonnement. Een dergelijke migratie zonder downtime vereist echter dat de bron-app en de doel-app op een bepaald moment hetzelfde aangepaste domein toewijzen. Daarom moet u ervoor zorgen dat de twee apps niet zijn geïmplementeerd in dezelfde implementatie-eenheid (intern bekend als een webruimte). Een domein naam kan slechts aan één app in elke implementatie-eenheid worden toegewezen.

U kunt de implementatie-eenheid voor uw app vinden door te kijken naar de domein naam van de FTP/S-URL `<deployment-unit>.ftp.azurewebsites.windows.net` . Controleer of de implementatie-eenheid verschilt van de bron-app en de doel-app. De implementatie-eenheid van een app wordt bepaald door het [app service plan](overview-hosting-plans.md) waarin het zich bevindt. Het wordt wille keurig door Azure geselecteerd wanneer u het plan maakt en kan niet worden gewijzigd. Azure zorgt er alleen voor dat twee plannen zich in dezelfde implementatie-eenheid bevinden wanneer u [ze in dezelfde resource groep *en* dezelfde regio maakt](app-service-plan-manage.md#create-an-app-service-plan), maar er is geen logica om ervoor te zorgen dat plannen zich in verschillende implementatie-eenheden bevinden. De enige manier om een plan in een andere implementatie-eenheid te maken, is door te blijven maken van een plan in een nieuwe resource groep of regio, totdat u een andere implementatie-eenheid krijgt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het koppelen van een aangepast TLS/SSL-certificaat aan App Service.

> [!div class="nextstepaction"]
> [Een aangepaste DNS-naam beveiligen met een TLS-binding in Azure App Service](configure-ssl-bindings.md)
