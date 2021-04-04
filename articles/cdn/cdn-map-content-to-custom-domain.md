---
title: 'Zelfstudie: Een aangepast domein aan uw eindpunt toevoegen'
titleSuffix: Azure Content Delivery Network
description: Met deze zelfstudie kunt u een aangepast domein toevoegen aan een Azure Content Delivery Network-eindpunt, zodat uw domeinnaam zichtbaar is in uw URL.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: b0e8f2b14d506eb408660b939a7c925a33215cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99537743"
---
# <a name="tutorial-add-a-custom-domain-to-your-endpoint"></a>Zelfstudie: Een aangepast domein aan uw eindpunt toevoegen

Deze zelfstudie laat zien hoe u een aangepast domein toevoegt aan een Azure CDN-eindpunt (Content Delivery Network). 

De eindpuntnaam in uw CDN-profiel is een subdomein van azureedge.net. Bij het leveren van inhoud wordt het CDN-profieldomein standaard opgenomen in de URL.

Bijvoorbeeld **https://contoso.azureedge.net/photo.png** .

Azure CDN biedt de mogelijkheid om een aangepast domein te koppelen aan een CDN-eindpunt. In dat geval wordt de inhoud geleverd via een aangepast domein in uw URL in plaats van het standaarddomein.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Een CNAME DNS-record maken.
> - Het aangepaste domein koppelen aan het CDN-eindpunt.
> - Het aangepaste domein verifiëren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Voordat u de stappen in deze zelfstudie kunt voltooien, moet u eerst een CDN-profiel en ten minste één CDN-eindpunt maken. 
    * Zie voor meer informatie [Snelstartgids: Een Azure CDN-profiel en een eindpunt maken](cdn-create-new-endpoint.md).

* Als u nog geen aangepast domein hebt, koopt u er een bij een domeinprovider. 
    * Zie [Een aangepaste domeinnaam kopen](../app-service/manage-custom-dns-buy-domain.md) voor meer informatie.

* Als u Azure gebruikt om uw [DNS-domeinen](../dns/dns-overview.md) te hosten, delegeert u uw aangepaste domein aan Azure DNS. 
    * Zie [Delegate a domain to Azure DNS](../dns/dns-delegate-domain-azure-dns.md) (Een domein aan Azure DNS overdragen) voor meer informatie.

* Als u een domeinprovider gebruikt voor het afhandelen van uw DNS-domein, gaat u verder met [Een CNAME DNS-record maken](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Een CNAME DNS-record maken

Voordat u een aangepast domein met een Azure CDN-eindpunt kunt gebruiken, moet u eerst een CNAME-record (canonieke naam) maken bij Azure DNS of uw DNS-provider om te verwijzen naar uw CDN-eindpunt. 

Een CNAME-record is een DNS-record die een brondomeinnaam toewijst aan een doeldomeinnaam. 

De domeinnaam van de bron is uw aangepaste domeinnaam voor Azure CDN en de domeinnaam van het doel is de hostnaam van uw CDN-eindpunt. 

Na verificatie van de CNAME-record routeert Azure CDN verkeer dat is geadresseerd aan het aangepaste brondomein naar de bestemming met de hostnaam van het CDN-eindpunt.

Een aangepast domein en het subdomein kunnen aan één eindpunt tegelijk worden gekoppeld. 

Gebruik meerdere CNAME-records voor verschillende subdomeinen uit hetzelfde aangepaste domein voor verschillende Azure-services.

U kunt een aangepast domein met verschillende subdomeinen toewijzen aan hetzelfde CDN-eindpunt.

> [!NOTE]
> In deze zelfstudie wordt het CNAME-recordtype gebruikt. Als u A- of AAAA-recordtypen gebruikt, volgt u de onderstaande stappen en vervangt u CNAME door het recordtype van uw keuze.

---
# <a name="azure-dns"></a>[**Azure DNS**](#tab/azure-dns)

Azure DNS maakt gebruik van aliasrecords voor Azure-resources in hetzelfde abonnement.

Ga als volgt te werk om een aliasrecord toe te voegen voor uw Azure CDN-eindpunt:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer **Alle resources** in het linkermenu en selecteer vervolgens de Azure DNS-zone voor uw aangepaste domein.

3. Selecteer **+ Recordset** in de DNS-zone voor uw aangepaste domein.

4. Typ of selecteer de volgende informatie in **Recordset toevoegen**:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam  | Voer de alias in die u wilt gebruiken voor uw CDN-eindpunt. Bijvoorbeeld **www**. |
    | Type  | Selecteer **CNAME**. |
    | Recordset van alias | Selecteer **Ja**. |
    | Aliastype | Selecteer **Azure-resource**. |
    | Kies een abonnement | Selecteer uw abonnement. |
    | Azure-resource | Selecteer uw Azure CDN-eindpunt. |

5. Wijzig de **TTL** voor de record in uw waarde.

6. Selecteer **OK**.

# <a name="dns-provider"></a>[**DNS-provider**](#tab/dns-provider)

## <a name="map-the-temporary-cdnverify-subdomain"></a>Tijdelijk cdnverify subdomein toewijzen

Wanneer u een bestaand domein toewijst dat in productie is genomen, zijn er speciale overwegingen. 

Er kan een korte uitvaltijd voor het domein optreden wanneer u uw aangepaste domein in de Azure-portal registreert.

Om te voorkomen dat het webverkeer wordt onderbroken, wijst u uw aangepaste domein toe aan de hostnaam van uw CDN-eindpunt met het Azure-subdomein **cdnverify**. Met deze procedure wordt een tijdelijke CNAME-toewijzing gemaakt. 

Met deze methode kunnen gebruikers zonder onderbreking toegang krijgen tot uw domein terwijl de DNS-toewijzing wordt uitgevoerd. 

Als uitvaltijd geen probleem voor u is, kunt u uw aangepaste domein rechtstreeks toewijzen aan uw CDN-eindpunt. Ga door naar [Permanent aangepast domein toewijzen](#map-the-permanent-custom-domain).

Om een CNAME-record te maken met het subdomein cdnverify:

1. Meld u aan bij de website van de DNS-provider voor uw aangepaste domein.

2. Maak een CNAME-record vermelding voor uw aangepaste domein en vul de velden in, zoals wordt weergegeven in de volgende tabel (veldnamen kunnen verschillen):

    | Bron                    | Type  | Doel                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Bron: Voer de naam van uw aangepaste domein in, inclusief het subdomein cdnverify, in de volgende indeling: **cdnverify.\<custom-domain-name>**
        - Bijvoorbeeld: **cdnverify. www.contoso.com**

    - Type: Typ of selecteer **CNAME**.

    - Bestemming: Voer uw CDN-eindpunthostnaam in, inclusief het subdomein cdnverify, in de volgende indeling: **cdnverify.\<endpoint-name>.azureedge.net**. 
        - Bijvoorbeeld: **cdnverify.contoso.azureedge.net**

3. Sla uw wijzigingen op.

## <a name="map-the-permanent-custom-domain"></a>Permanent aangepast domein toewijzen

In deze sectie wijst u het permanente aangepaste domein toe aan het CDN-eindpunt. 

Maken van een CNAME-record voor uw aangepaste domein:

1. Meld u aan bij de website van de domeinprovider voor uw aangepaste domein.

2. Maak een CNAME-record vermelding voor uw aangepaste domein en vul de velden in, zoals wordt weergegeven in de volgende tabel (veldnamen kunnen verschillen):

    | Bron          | Type  | Doel           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Bron: Voer de naam van uw aangepaste domein in.
        - Bijvoorbeeld: **www.contoso.com**

    - Type: Typ of selecteer **CNAME**.

    - Bestemming: Voer uw CDN-eindpunthostnaam in de volgende indeling in: **\<endpoint-name>.azureedge.net**. 
        - Bijvoorbeeld: **contoso.azureedge.net**

3. Sla uw wijzigingen op.

4. Als u eerder een tijdelijk cdnverify-subdomein CNAME-record hebt gemaakt, moet u dit verwijderen. 

5. Als u dit aangepaste domein voor het eerst in de productieomgeving gebruikt, volg dan de stappen voor [Het aangepaste domein koppelen aan uw CDN-eindpunt](#associate-the-custom-domain-with-your-cdn-endpoint) en [Het aangepaste domein verifiëren](#verify-the-custom-domain).

---
## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Het aangepaste domein koppelen aan uw CDN-eindpunt

Nadat u uw aangepaste domein hebt geregistreerd, kunt u dit toevoegen aan uw CDN-eindpunt. 


---
# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal)

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) en blader naar het CDN-profiel met het eindpunt dat u wilt toewijzen aan een aangepast domein.
    
2. Op de **CDN-profiel** pagina, selecteert u het CDN-eindpunt dat u wilt koppelen aan het aangepaste domein.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-endpoint-selection.png" alt-text="CDN-eindpunt selecteren" border="true":::
    
3. Selecteer **+ Aangepast domein**. 

   :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png" alt-text="Knop Aangepast domein toevoegen" border="true":::

4. In **Een aangepast domein toevoegen** is **Hostnaam van het eindpunt** vooraf ingevuld en afgeleid van de URL van uw CDN-eindpunt: **\<endpoint-hostname>** .azureedge.net. De naam kan niet worden gewijzigd.

5. Bij **Aangepaste hostnaam** voert u de naam van uw aangepaste domein in, inclusief het subdomein, om dit te gebruiken als het brondomein van uw CNAME-record. 
    1. Bijvoorbeeld **www.contoso.com** of **cdn.contoso.com**. **Gebruik niet de naam van het cdnverify-subdomein**.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png" alt-text="Aangepast domein toevoegen" border="true":::

6. Selecteer **Toevoegen**.

   Azure controleert of het CNAME-record bestaat dat u voor de domeinnaam hebt ingevoerd. Als de CNAME juist is, wordt uw aangepaste domein gevalideerd. 

   Het kan even duren voordat de nieuwe instellingen van het aangepaste domein zijn doorgegeven aan alle CDN-edge-knooppunten: 
    - Voor profielen van **Azure CDN Standard van Microsoft** is het doorgeven gewoonlijk binnen 10 minuten voltooid. 
    - Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
    - Profielen van **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** worden normaal gesproken binnen 10 minuten doorgegeven.   

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

1. Meld u aan bij Azure PowerShell:

```azurepowershell-interactive
    Connect-AzAccount

```
2. Gebruik [New-AzCdnCustomDomain](/powershell/module/az.cdn/new-azcdncustomdomain) om het aangepaste domein toe te wijzen aan uw CDN-eind punt. 

    * Vervang **myendpoint8675.azureedge.net** door uw eind punt-URL.
    * Vervang **myendpoint8675** door de naam van uw CDN-eind punt.
    * Vervang **www.contoso.com** door de naam van uw aangepaste domein.
    * Vervang **myCDN** door de naam van uw CDN-profiel.
    * Vervang **myResourceGroupCDN** door de naam van de resource groep.

```azurepowershell-interactive
    $parameters = @{
        Hostname = 'myendpoint8675.azureedge.net'
        EndPointName = 'myendpoint8675'
        CustomDomainName = 'www.contoso.com'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    New-AzCdnCustomDomain @parameters
```

Azure controleert of het CNAME-record bestaat dat u voor de domeinnaam hebt ingevoerd. Als de CNAME juist is, wordt uw aangepaste domein gevalideerd. 

   Het kan even duren voordat de nieuwe instellingen van het aangepaste domein zijn doorgegeven aan alle CDN-edge-knooppunten: 

- Voor profielen van **Azure CDN Standard van Microsoft** is het doorgeven gewoonlijk binnen 10 minuten voltooid. 
- Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
- Profielen van **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** worden normaal gesproken binnen 10 minuten doorgegeven.   


---
## <a name="verify-the-custom-domain"></a>Het aangepaste domein verifiëren

Nadat u de registratie van uw aangepaste domein hebt voltooid, verifieert u dat het aangepaste domein verwijst naar uw CDN-eindpunt.
 
1. Zorg ervoor dat u op het eindpunt openbare inhoud hebt die in cache is opgeslagen. Bijvoorbeeld, als uw CDN-eindpunt is gekoppeld aan een opslagaccount, zal Azure CDN de inhoud in cache opslaan in een openbare container. Stel uw container in voor openbare toegang en zorg dat deze ten minste één bestand bevat om het aangepaste domein te testen.

2. Ga naar het adres van het bestand met behulp van het aangepaste domein in uw browser. Als de naam van uw aangepaste domein bijvoorbeeld `www.contoso.com` is, ziet de URL van het in de cache opgeslagen bestand er ongeveer als volgt uit: `http://www.contoso.com/my-public-container/my-file.jpg`. Verifieer dat het resultaat overeenkomt met het resultaat dat u ziet wanneer u het CDN-eindpunt rechtstreeks benadert op **\<endpoint-hostname>** .azureedge.net.

## <a name="clean-up-resources"></a>Resources opschonen

---
# <a name="azure-portal"></a>[**Azure Portal**](#tab/azure-portal-cleanup)

Als u uw eindpunt niet langer aan een aangepast domein wilt koppelen, verwijdert u het aangepaste domein door de volgende stappen uit te voeren:
 
1. Selecteer het eindpunt met het aangepaste domein dat u wilt verwijderen in uw CDN-profiel.

2. Klik vanuit de **Eindpunt**-pagina onder Aangepaste domeinen met de rechtermuisknop op het aangepaste domein dat u wilt verwijderen en selecteer vervolgens **Verwijderen** in het contextmenu. Selecteer **Ja**.

   Het aangepaste domein is ontkoppeld van het eindpunt.

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell-cleanup)

Als u uw eindpunt niet langer aan een aangepast domein wilt koppelen, verwijdert u het aangepaste domein door de volgende stappen uit te voeren:

1. Gebruik [Remove-AzCdnCustomDomain](/powershell/module/az.cdn/remove-azcdncustomdomain) om het aangepaste domein te verwijderen uit het eind punt:

    * Vervang **myendpoint8675** door de naam van uw CDN-eind punt.
    * Vervang **www.contoso.com** door de naam van uw aangepaste domein.
    * Vervang **myCDN** door de naam van uw CDN-profiel.
    * Vervang **myResourceGroupCDN** door de naam van de resource groep.


```azurepowershell-interactive
    $parameters = @{
        CustomDomainName = 'www.contoso.com'
        EndPointName = 'myendpoint8675'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    Remove-AzCdnCustomDomain @parameters
```

---
## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> - Een CNAME DNS-record maken.
> - Het aangepaste domein koppelen aan het CDN-eindpunt.
> - Het aangepaste domein verifiëren.

Ga naar de volgende zelfstudie voor meer informatie over hoe u HTTPS configureert op een aangepast Azure CDN-domein.

> [!div class="nextstepaction"]
> [Zelfstudie: HTTPS configureren op een aangepast Azure CDN-domein](cdn-custom-ssl.md)