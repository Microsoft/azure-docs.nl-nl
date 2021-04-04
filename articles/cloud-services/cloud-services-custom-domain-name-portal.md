---
title: Een aangepaste domein naam configureren in Cloud Services (klassiek) | Microsoft Docs
description: Meer informatie over hoe u uw Azure-toepassing of-gegevens beschikbaar maakt op internet in een aangepast domein door DNS-instellingen te configureren.  In deze voor beelden wordt gebruikgemaakt van de Azure Portal.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: bced2345473dbcbb5b9adf0269de0bef0549e862
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742366"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service-classic"></a>Een aangepaste domein naam configureren voor een Azure-Cloud service (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Wanneer u een Cloud service maakt, wijst Azure deze toe aan een subdomein van **cloudapp.net**. Als uw Cloud service bijvoorbeeld de naam Contoso heeft, hebben uw gebruikers toegang tot uw toepassing op een URL, zoals `http://contoso.cloudapp.net` . Azure wijst ook een virtueel IP-adres toe.

U kunt uw toepassing echter ook beschikbaar maken op uw eigen domein naam, zoals **contoso.com**. In dit artikel wordt uitgelegd hoe u een aangepaste domein naam voor Cloud service-webrollen kunt reserveren of configureren.

Weet u al wat CNAME en A records? [Ga na de uitleg](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> De procedures in deze taak zijn van toepassing op Azure Cloud Services. Zie [een bestaande aangepaste DNS-naam toewijzen aan Azure web apps](../app-service/app-service-web-tutorial-custom-domain.md)voor app Services. Zie [een aangepaste domein naam configureren voor het eind punt van uw Azure Blob-opslag](../storage/blobs/storage-custom-domain-name.md)voor opslag accounts.
> 
> 

<p/>

> [!TIP]
> Ga snel aan de slag: gebruik de nieuwe Azure- [geleide walkthrough](https://support.microsoft.com/kb/2990804).  Hiermee wordt het koppelen van een aangepaste domein naam en het beveiligen van communicatie (TLS) met Azure Cloud Services of Azure websites een magneet.
> 
> 

## <a name="understand-cname-and-a-records"></a>Informatie over CNAME en A-records
Met CNAME-(of alias records) en een record kunt u een domein naam koppelen aan een specifieke server (of service in dit geval), maar ze werken echter anders. Er zijn ook enkele specifieke overwegingen bij het gebruik van een record met Azure Cloud Services die u moet overwegen voordat u besluit om te gebruiken.

### <a name="cname-or-alias-record"></a>CNAME-of alias record
Een CNAME-record wijst een *specifiek* domein, zoals **contoso.com** of **www \. contoso.com**, toe aan een canonieke domein naam. In dit geval is de canonieke domein naam de domein naam **[Mijntoep]. cloudapp. net** van uw door Azure gehoste toepassing. Zodra de CNAME is gemaakt, wordt er een alias gemaakt voor de **[MyApp]. cloudapp. net**. De CNAME-vermelding wordt automatisch omgezet in het IP-adres van uw **[MyApp]. cloudapp. net** -service, dus als het IP-adres van de Cloud service wordt gewijzigd, hoeft u geen actie te ondernemen.

> [!NOTE]
> Bij sommige domein registraties kunt u alleen subdomeinen toewijzen wanneer u een CNAME-record gebruikt, zoals www- \. contoso.com en geen hoofd namen, zoals contoso.com. Voor meer informatie over CNAME-records raadpleegt u de documentatie van uw registratie service, [de Wikipedia-vermelding in de CNAME-record](https://en.wikipedia.org/wiki/CNAME_record)of het [IETF-domein namen-implementatie en specificatie](https://tools.ietf.org/html/rfc1035) document.

### <a name="a-record"></a>A-record
Een *a* -record wijst een domein, zoals **contoso.com** of **www \. contoso.com**, *of een Joker teken domein* , zoals **\* . contoso.com**, toe aan een IP-adres. In het geval van een Azure-Cloud service is dit het virtuele IP-adres van de service. Het belangrijkste voor deel van een A-record over een CNAME-record is dat u één vermelding kunt hebben die gebruikmaakt van een Joker teken, zoals \* *_. contoso.com_*, waarmee aanvragen voor meerdere subdomeinen, zoals **mail.contoso.com**, **login.contoso.com** of **www \. contso.com**, worden afgehandeld.

> [!NOTE]
> Omdat een A-record wordt toegewezen aan een statisch IP-adres, kan de wijziging niet automatisch worden omgezet in het IP-adres van uw Cloud service. Het IP-adres dat wordt gebruikt door uw Cloud service wordt de eerste keer dat u implementeert in een lege sleuf (productie of fase ring) toegewezen. Als u de implementatie voor de sleuf verwijdert, wordt het IP-adres vrijgegeven door Azure en kunnen toekomstige implementaties naar de sleuf een nieuw IP-adres krijgen.
> 
> Het IP-adres van een bepaalde implementatie sleuf (productie of fase ring) wordt bij het wisselen tussen fase ring en productie-implementaties of het uitvoeren van een in-place upgrade van een bestaande implementatie bewaard. Zie [Cloud Services beheren](cloud-services-how-to-manage-portal.md)voor meer informatie over het uitvoeren van deze acties.
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Een CNAME-record voor uw aangepaste domein toevoegen
Als u een CNAME-record wilt maken, moet u een nieuwe vermelding toevoegen aan de DNS-tabel voor uw aangepaste domein met behulp van de hulpprogram ma's van uw registratie service. Elke registratie service heeft een vergelijk bare, maar iets andere methode om een CNAME-record op te geven, maar de concepten zijn hetzelfde.

1. Gebruik een van deze methoden om de domein naam **. cloudapp.net** te vinden die is toegewezen aan uw Cloud service.

   * Meld u aan bij de [Azure Portal], selecteer uw Cloud service, Bekijk de sectie **overzicht** en zoek vervolgens de **site-URL** -vermelding.

       ![sectie quickis waarin de site-URL wordt weer gegeven][csurl]

       **OF**
   * Installeer en configureer [Azure Power shell](/powershell/azure/)en gebruik vervolgens de volgende opdracht:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Sla de domein naam op die wordt gebruikt in de URL die wordt geretourneerd door een van beide methoden, zoals u deze nodig hebt bij het maken van een CNAME-record.
2. Meld u aan bij de website van uw DNS-domein registratie en ga naar de pagina voor het beheren van DNS. Zoek naar koppelingen of gebieden van de site met het label **domein naam**, **DNS** of **naam server beheer**.
3. Nu kunt u de locatie van een CNAME selecteren of invoeren. Mogelijk moet u het record type selecteren in een vervolg keuzelijst of naar een pagina met geavanceerde instellingen gaan. U moet de woorden **CNAME**, **alias** of **subdomeins** zoeken.
4. U moet ook de alias van het domein of subdomein voor de CNAME opgeven, zoals **www** als u een alias voor www- **\. customdomain.com** wilt maken. Als u een alias voor het hoofd domein wilt maken, wordt deze mogelijk vermeld als het symbool ' **\@** ' in de DNS-hulpprogram ma's van uw registratie service.
5. Vervolgens moet u een canonieke hostnaam opgeven. Dit is het **cloudapp.net** -domein van uw toepassing in dit geval.

De volgende CNAME-record stuurt bijvoorbeeld alle verkeer van **www \. contoso.com** naar **contoso.cloudapp.net**, de aangepaste domein naam van uw geïmplementeerde toepassing:

| Alias/hostnaam/subdomein | Canonieke domein |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> Een bezoeker van **www- \. contoso.com** ziet nooit de echte host (contoso.cloudapp.net), dus het doorstuur proces is niet zichtbaar voor de eind gebruiker.
> 
> Bovenstaand voor beeld is alleen van toepassing op verkeer in het subdomein **www** . Aangezien u geen joker tekens met CNAME-records kunt gebruiken, moet u voor elk domein/subdomein één CNAME maken. Als u verkeer van subdomeinen, zoals *. contoso.com, wilt omleiden naar uw cloudapp.net-adres, kunt u een **URL-omleiding** of **URL-doorstuur** vermelding in uw DNS-instellingen configureren, of een a-record maken.

## <a name="add-an-a-record-for-your-custom-domain"></a>Een A-record voor uw aangepaste domein toevoegen
Als u een A-record wilt maken, moet u eerst het virtuele IP-adres van uw Cloud service vinden. Voeg vervolgens een nieuw item toe aan de DNS-tabel voor uw aangepaste domein met behulp van de hulpprogram ma's van uw registratie service. Elke registratie service heeft een vergelijk bare, maar iets andere methode om een record op te geven, maar de concepten zijn hetzelfde.

1. Gebruik een van de volgende methoden om het IP-adres van uw Cloud service op te halen.

   * Meld u aan bij de [Azure Portal], selecteer uw Cloud service, Bekijk de sectie **overzicht** en zoek vervolgens de vermelding **open bare IP-adressen** .

       ![sectie quickis waarin het VIP wordt weer gegeven][vip]

       **OF**
   * Installeer en configureer [Azure Power shell](/powershell/azure/)en gebruik vervolgens de volgende opdracht:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Sla het IP-adres op, zoals u het nodig hebt bij het maken van een A-record.
2. Meld u aan bij de website van uw DNS-domein registratie en ga naar de pagina voor het beheren van DNS. Zoek naar koppelingen of gebieden van de site met het label **domein naam**, **DNS** of **naam server beheer**.
3. Nu kunt u een record selecteren of invoeren. Mogelijk moet u het record type selecteren in een vervolg keuzelijst of naar een pagina met geavanceerde instellingen gaan.
4. Selecteer of typ het domein of subdomein dat deze record gaat gebruiken. Selecteer bijvoorbeeld **www** als u een alias wilt maken voor **www- \. customdomain.com**. Als u een vermelding met Joker tekens wilt maken voor alle subdomeinen, voert u ' * * * * * ' in. Dit geldt voor alle subdomeinen, zoals **mail.customdomain.com**, **login.customdomain.com** en **www \. customdomain.com**.

    Als u een A-record wilt maken voor het hoofd domein, wordt deze mogelijk vermeld als het **\@** symbool ' ' in de DNS-hulpprogram ma's van uw domein registratie.
5. Voer het IP-adres van uw Cloud service in het opgegeven veld in. Hiermee wordt de domein vermelding die wordt gebruikt in de A-record gekoppeld aan het IP-adres van de implementatie van de Cloud service.

De volgende A-record stuurt bijvoorbeeld alle verkeer van **contoso.com** naar **137.135.70.239**, het IP-adres van uw geïmplementeerde toepassing:

| Hostnaam/subdomein | IP-adres |
| --- | --- |
| \@ |137.135.70.239 |

In dit voor beeld ziet u hoe u een A-record maakt voor het hoofd domein. Als u een invoer van joker tekens wilt maken voor alle subdomeinen, voert u ' * * * * * * ' in als het subdomein.

> [!WARNING]
> IP-adressen in azure zijn standaard dynamisch. U wilt waarschijnlijk een [gereserveerd IP-adres](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) gebruiken om ervoor te zorgen dat uw IP-adres niet wordt gewijzigd.
> 
> 

## <a name="next-steps"></a>Volgende stappen
* [Cloud Services beheren](cloud-services-how-to-manage-portal.md)
* [CDN-inhoud toewijzen aan een aangepast domein](../cdn/cdn-map-content-to-custom-domain.md)
* [Algemene configuratie van uw Cloud service](cloud-services-how-to-configure-portal.md).
* Meer informatie over het [implementeren van een Cloud service](cloud-services-how-to-create-deploy-portal.md).
* [TLS/SSL-certificaten](cloud-services-configure-ssl-certificate-portal.md)configureren.

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure-portal]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png