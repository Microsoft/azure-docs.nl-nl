---
title: 'Zelf studie: een bestaand aangepast domein toewijzen aan Azure lente-Cloud'
description: Een bestaande naam van een aangepaste gedistribueerde naamservice (DNS) toewijzen aan een Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 7aa1982fc880ac5733cc4453808c18956969572f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627010"
---
# <a name="tutorial-map-an-existing-custom-domain-to-azure-spring-cloud"></a>Zelf studie: een bestaand aangepast domein toewijzen aan Azure lente-Cloud

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

Domain Name Service (DNS) is een techniek voor het opslaan van namen van netwerkknooppunten in een netwerk. In deze zelfstudie wordt een domein, zoals www.contoso.com, gekoppeld met behulp van een CNAME-record. Het aangepaste domein wordt beveiligd met een certificaat en laat zien hoe u Transport Layer Security (TLS) afdwingt, ook wel bekend als Secure Sockets Layer (SSL). 

Certificaten versleutelen webverkeer. Deze TLS/SSL-certificaten kunnen worden opgeslagen in Azure Key Vault. 

## <a name="prerequisites"></a>Vereisten
* Een toepassing die is geïmplementeerd in Azure Spring Cloud (Zie [Snelstart: Start een bestaande Azure Spring Cloud-toepassing met behulp van het Azure- portal](spring-cloud-quickstart.md)of gebruik een bestaande app).
* Een domeinnaam met toegang hebt tot het DNS-register voor uw domeinprovider zoals GoDaddy.
* Een persoonlijk certificaat (dat wil zeggen uw zelfondertekende certificaat) van een externe provider. Het certificaat moet overeenkomen met het domein.
* Een geïmplementeerd exemplaar van [Azure Key Vault](../key-vault/general/overview.md)

## <a name="keyvault-private-link-considerations"></a>Aandachtspunten voor de persoonlijke koppeling van de sleutel kluis

De Azure veer Cloud beheer Ip's maken geen deel uit van de vertrouwde micro soft-services van Azure. Daarom moet u de volgende Ip's toevoegen aan Azure Key Vault firewall om ervoor te zorgen dat Azure lente-Cloud certificaten laadt van een Key Vault die zijn beveiligd met een privé-eindpunt verbinding:

```
20.53.123.160 52.143.241.210 40.65.234.114 52.142.20.14 20.54.40.121 40.80.210.49 52.253.84.152 20.49.137.168 40.74.8.134 51.143.48.243
```

## <a name="import-certificate"></a>Certificaat importeren
### <a name="prepare-your-certificate-file-in-pfx-optional"></a>U certificaatbestand voorbereiden in PFX (optioneel)
Azure Key Vault-ondersteuning voor het importeren van een privécertificaat in de PEM- en PFX-indeling. Als het PEM-bestand dat u hebt verkregen van de certificaatprovider, niet werkt in onderstaande sectie: [Sla het certificaat op in Key Vault](#save-certificate-in-key-vault), volg de stappen hier om een PFX voor Azure Key Vault te genereren.

#### <a name="merge-intermediate-certificates"></a>Tussenliggende certificaten samenvoegen

Als uw certificeringsinstantie meerdere certificaten in de certificaatketen geeft, moet u de certificaten in de juiste volgorde samenvoegen.

U doet dit door elk certificaat dat u hebt ontvangen in een teksteditor te openen.

Maak een bestand voor het samengevoegde certificaat met de naam _mergedcertificate.crt_. Kopieer de inhoud van elk certificaat in dit bestand in een teksteditor. De volgorde van uw certificaten moet de volgorde in de certificaatketen volgen, beginnend met uw certificaat en eindigend met het hoofdcertificaat. Het lijkt op het volgende voorbeeld:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

#### <a name="export-certificate-to-pfx"></a>Certificaat naar PFX exporteren

Exporteer uw samengevoegde TLS/SSL-certificaat met de persoonlijke sleutel die met uw certificaataanvraag is gegenereerd.

Als u de certificaataanvraag met OpenSSL hebt gegenereerd, hebt u een bestand met een persoonlijke sleutel gemaakt. Voer de volgende opdracht uit om uw certificaat naar PFX te exporteren. Vervang de tijdelijke aanduidingen _&lt;private-key-file>_ en _&lt;merged-certificate-file>_ door de paden naar uw persoonlijke sleutel en uw bestand met samengevoegde certificaten.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Wanneer u daarom wordt gevraagd, geeft u een wachtwoord voor export op. U gebruikt dit wachtwoord later wanneer u uw TLS/SSL-certificaat uploadt naar Azure Key Vault.

Als u IIS of _Certreq.exe_ hebt gebruikt voor het genereren van uw certificaataanvraag, installeert u het certificaat op uw lokale computer en [exporteert u het certificaat naar PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="save-certificate-in-key-vault"></a>Certificaat opslaan in Key Vault
Voor de procedure voor het importeren van een certificaat moet het PEM-of PFX-bestand op een schijf staan en moet u de persoonlijke sleutel hebben. 
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Uw certificaat uploaden naar de sleutelkluis:
1. Ga naar uw sleutelkluis-exemplaar.
1. Klik op **Certificaten** in het linkernavigatievenster.
1. Klik in het bovenste menu op **Genereren/importeren**.
1. Selecteer in het dialoogvenster **Een certificaat maken** onder **Methode voor het maken van certificaten**`Import`.
1. Ga in **Certificaatbestand uploaden** naar de locatie van het certificaat en selecteer dit.
1. Voer onder **Wachtwoord** de persoonlijke sleutel voor uw certificaat in.
1. Klik op **Create**.

    ![Certificaat 1 importeren](./media/custom-dns-tutorial/import-certificate-a.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```azurecli
az keyvault certificate import --file <path to .pfx file> --name <certificate name> --vault-name <key vault name> --password <export password>
```
---

### <a name="grant-azure-spring-cloud-access-to-your-key-vault"></a>Azure Spring Cloud toegang verlenen tot uw sleutelkluis

U moet Azure Spring Cloud toegang verlenen tot uw sleutelkluis voordat u het certificaat importeert:
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Ga naar uw sleutelkluis-exemplaar.
1. Klik op **Toegangsbeleid** in het linkernavigatievenster.
1. Klik in het bovenste menu op **Toegangsbeleid toevoegen**.
1. Vul de informatie in en klik op de knop **Toevoegen** en toegangsbeleid **Opslaan**.

| Geheime machtiging | Certificaatmachtiging | Principal selecteren |
|--|--|--|
| Ophalen, Lijst | Ophalen, Lijst | Domeinbeheer van Azure Spring Cloud |

![Certificaat 2 importeren](./media/custom-dns-tutorial/import-certificate-b.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Verleen Azure Spring Cloud toegang tot de sleutelkluis, vervang `<key vault resource group>` en `<key vault name>` in de volgende opdracht.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list --secret-permissions get list
``` 
---

### <a name="import-certificate-to-azure-spring-cloud"></a>Certificaat importeren in Azure Spring Cloud
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Ga naar uw service-exemplaar. 
1. Selecteer **TLS/SSL-instellingen** vanuit het navigatiedeelvenster aan de linkerkant van uw app.
1. Klik vervolgens op **Key Vault-certificaat importeren**.

    ![Certificaat importeren](./media/custom-dns-tutorial/import-certificate.png)

1. Wanneer u uw certificaat hebt geïmporteerd, ziet u dit in de lijst met **Persoonlijke sleutelcertificaten**.

    ![Certificaat met een persoonlijke sleutel](./media/custom-dns-tutorial/key-certificates.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

Een lijst met geïmporteerde certificaten weergeven:

```
az spring-cloud certificate list --resource-group <resource group name> --service <service name>
```
---

> [!IMPORTANT] 
> Als u een aangepast domein met dit certificaat wilt beveiligen, moet u het certificaat nog steeds binden aan een specifiek domein. Volg de stappen in deze sectie: [SSL-binding toevoegen](#add-ssl-binding).

## <a name="add-custom-domain"></a>Aangepast domein toevoegen
U kunt een CNAME-record gebruiken om een aangepaste DNS-naam toe te wijzen aan Azure Spring Cloud. 

> [!NOTE] 
> De A-record wordt niet ondersteund. 

### <a name="create-the-cname-record"></a>Het CNAME-record maken
Ga naar uw DNS-provider en voeg een CNAME-record toe om uw domein toe te wijzen aan de <service_name>.azuremicroservices.io. Hier is <service_name> de naam van uw Azure Spring Cloud-exemplaar. We ondersteunen het Wildcard-domein en -subdomein. Nadat u de CNAME toevoegt, lijkt de pagina DNS-records op het volgende voorbeeld: 

![Pagina met DNS-records](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Uw aangepaste domein toewijzen aan Azure Spring Cloud-app
Als u geen toepassing in Azure Spring Cloud hebt, volgt u de instructies in [Snelstart: Start een bestaande Azure Spring Cloud-toepassing met behulp van het Azure- portal](./spring-cloud-quickstart.md).

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Ga naar de toepassingspagina.

1. Selecteer **Aangepast domein**.
2. Vervolgens **Aangepast domein toevoegen**. 

    ![Aangepast domein](./media/custom-dns-tutorial/custom-domain.png)

3. Typ de volledig gekwalificeerde domeinnaam in waarvoor u een CNAME-record zoals www.contoso.com heeft toegevoegd. Zorg ervoor dat het Hostnaam-recordtype is ingesteld op CNAME (<service_name>.azuremicroservices.io)
4. Klik op **Valideren** om de knop **Toevoegen** in te schakelen.
5. Klik op **Add**.

    ![Aangepast domein toevoegen](./media/custom-dns-tutorial/add-custom-domain.png)

Eén app kan meerdere domeinen hebben, maar één domein kan slechts aan één app worden toegewezen. Wanneer u uw aangepaste domein hebt toegewezen aan de app, ziet u dit in de tabel aangepast domein.

![Aangepast domeintabel](./media/custom-dns-tutorial/custom-domain-table.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> --resource-group <resource group name> --service <service name>
```

De lijst met aangepaste domeinen weergeven:
```
az spring-cloud app custom-domain list --app <app name> --resource-group <resource group name> --service <service name>
```
---

> [!NOTE]
> Een **Niet beveiligd** label voor uw aangepaste domein betekent dat het nog niet is gebonden aan een SSL-certificaat. Een HTTPS-aanvraag van een browser naar uw aangepaste domein krijgt een fout of waarschuwing.

## <a name="add-ssl-binding"></a>SSL-binding toevoegen

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Selecteer in de tabel aangepast domein **SSL-binding toevoegen** zoals in de vorige afbeelding wordt weergegeven.  
1. Selecteer uw **Certificaat** of importeer het.
1. Klik op **Opslaan**.

    ![SSL-binding toevoegen 1](./media/custom-dns-tutorial/add-ssl-binding.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```
---

Nadat u een SSL-binding hebt toegevoegd, is de status van het domein beveiligd: **In orde**. 

![SSL-binding toevoegen 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>HTTPS afdwingen
Standaard kan iedereen nog steeds toegang krijgen tot uw app met behulp van HTTP, maar u kunt alle HTTP-aanvragen omleiden naar de HTTPS-poort.
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Selecteer in het linkernavigatievenster van de app-pagina **Aangepaste domein**. Stel vervolgens op **Alleen HTTPS**, in op *Waar*.

![SSL-binding toevoegen 3](./media/custom-dns-tutorial/enforce-http.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app update -n <app name> --resource-group <resource group name> --service <service name> --https-only
```
---
Wanneer de bewerking is voltooid, gaat u naar een van de HTTPS-URL's die naar uw app verwijzen. Houd er rekening mee dat HTTP-URL's niet werken.

## <a name="see-also"></a>Zie ook
* [Wat is Azure Key Vault?](../key-vault/general/overview.md)
* [Certificaat importeren](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)
* [Uw Spring Cloud-app starten met behulp van de Azure CLI](./spring-cloud-quickstart.md)
