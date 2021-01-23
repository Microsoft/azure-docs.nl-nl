---
title: TLS configureren voor een Cloud service | Microsoft Docs
description: Meer informatie over het opgeven van een HTTPS-eind punt voor een webrole en het uploaden van een TLS/SSL-certificaat om uw toepassing te beveiligen. In deze voor beelden wordt gebruikgemaakt van de Azure Portal.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 33aa088efd7768153d4a17472d82e0826f4ffa6b
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742638"
---
# <a name="configuring-tls-for-an-application-in-azure"></a>TLS configureren voor een toepassing in azure

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Transport Layer Security (TLS), voorheen bekend als SSL-versleuteling (Secure Socket Layer), is de meest gebruikte methode voor het beveiligen van gegevens die via internet worden verzonden. In deze algemene taak wordt beschreven hoe u een HTTPS-eind punt opgeeft voor een webrole en hoe u een TLS/SSL-certificaat kunt uploaden om uw toepassing te beveiligen.

> [!NOTE]
> De procedures in deze taak zijn van toepassing op Azure Cloud Services; [Zie voor](../app-service/configure-ssl-bindings.md)app Services.
>

Deze taak maakt gebruik van een productie-implementatie. Meer informatie over het gebruik van een faserings implementatie vindt u aan het einde van dit onderwerp.

Lees [Dit](cloud-services-how-to-create-deploy-portal.md) eerst als u nog geen Cloud service hebt gemaakt.

## <a name="step-1-get-a-tlsssl-certificate"></a>Stap 1: een TLS/SSL-certificaat ophalen
Als u TLS wilt configureren voor een toepassing, moet u eerst een TLS/SSL-certificaat ophalen dat is ondertekend door een certificerings instantie (CA), een vertrouwde derde partij die certificaten uitgeeft voor dit doel. Als u er nog geen hebt, moet u er een verkrijgen van een bedrijf dat TLS/SSL-certificaten verkoopt.

Het certificaat moet voldoen aan de volgende vereisten voor TLS/SSL-certificaten in Azure:

* Het certificaat moet een open bare sleutel bevatten.
* Het certificaat moet worden gemaakt voor sleutel uitwisseling, exporteerbaar naar een pfx-bestand (Personal Information Exchange).
* De onderwerpnaam van het certificaat moet overeenkomen met het domein dat wordt gebruikt voor toegang tot de Cloud service. U kunt geen TLS/SSL-certificaat verkrijgen van een certificerings instantie (CA) voor het cloudapp.net-domein. U moet een aangepaste domein naam verkrijgen die u kunt gebruiken om toegang te krijgen tot uw service. Wanneer u een certificaat van een certificerings instantie aanvraagt, moet de onderwerpnaam van het certificaat overeenkomen met de aangepaste domein naam die wordt gebruikt voor toegang tot uw toepassing. Als uw aangepaste domein naam bijvoorbeeld **contoso.com** is, vraagt u een certificaat aan bij uw CA voor **_. contoso.com_* of **www \. contoso.com**.
* Het certificaat moet mini maal 2048 bits versleuteling gebruiken.

Voor test doeleinden kunt u een zelfondertekend certificaat [maken](cloud-services-certs-create.md) en gebruiken. Een zelfondertekend certificaat wordt niet geverifieerd via een certificerings instantie en kan het domein cloudapp.net als de URL van de website gebruiken. De volgende taak maakt bijvoorbeeld gebruik van een zelfondertekend certificaat waarin de algemene naam (CN) die in het certificaat wordt gebruikt, **sslexample.cloudapp.net** is.

Vervolgens moet u informatie over het certificaat in uw service definitie en configuratie bestanden van de service toevoegen.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Stap 2: de service definitie en configuratie bestanden wijzigen
Uw toepassing moet worden geconfigureerd voor het gebruik van het certificaat en er moet een HTTPS-eind punt worden toegevoegd. Als gevolg hiervan moeten de service definitie en de service configuratie bestanden worden bijgewerkt.

1. Open in uw ontwikkel omgeving het service definitie bestand (CSDEF), voeg een sectie **certificaten** toe binnen de sectie **webrol** en neem de volgende informatie op over het certificaat (en tussenliggende certificaten):

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   De sectie **certificaten** definieert de naam van het certificaat, de locatie en de naam van het archief waar het zich bevindt.

   Permissions ( `permissionLevel` Attribute) kunnen worden ingesteld op een van de volgende waarden:

   | Machtigings waarde | Beschrijving |
   | --- | --- |
   | limitedOrElevated |**(Standaard)** Alle rollen processen hebben toegang tot de persoonlijke sleutel. |
   | verhoogde |Alleen verhoogde processen hebben toegang tot de persoonlijke sleutel. |

2. Voeg in het bestand met de service definitie een **InputEndpoint** -element toe binnen de sectie **endpoints** om https in te scha kelen:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. Voeg in het service definitie bestand een **binding** element toe binnen de sectie **sites** . Dit element voegt een HTTPS-binding toe om het eind punt toe te wijzen aan uw site:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   Alle vereiste wijzigingen in het service definitie bestand zijn voltooid. maar u moet de certificaat gegevens nog steeds toevoegen aan het service configuratie bestand.
4. Voeg in uw service configuratie bestand (CSCFG), ServiceConfiguration. Cloud. CSCFG, een **certificaten** waarde toe aan het certificaat. Het volgende code voorbeeld bevat details van de sectie **certificaten** , met uitzonde ring van de waarde van de vinger afdruk.

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(In dit voor beeld wordt **SHA1** gebruikt voor het vinger afdruk algoritme. Geef de juiste waarde op voor het vinger afdruk algoritme van uw certificaat.)

Nu de service definitie en de service configuratie bestanden zijn bijgewerkt, pakt u uw implementatie in voor uploaden naar Azure. Als u **cspack** gebruikt, gebruikt u de vlag **/generateConfigurationFile** niet, aangezien de certificaat gegevens die u zojuist hebt ingevoegd, worden overschreven.

## <a name="step-3-upload-a-certificate"></a>Stap 3: een certificaat uploaden
Verbinding maken met de Azure Portal en...

1. Selecteer in de sectie **alle resources** van de portal uw Cloud service.

    ![Uw Cloud service publiceren](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Klik op **Certificaten**.

    ![Klik op het pictogram certificaten](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Klik op **uploaden** boven aan het gebied certificaten.

    ![Klik op het menu-item uploaden](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Geef het **bestand**, het **wacht woord** en klik op **uploaden** onder aan het gebied voor gegevens invoer.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Stap 4: verbinding maken met de rolinstantie met behulp van HTTPS
Nu uw implementatie in azure wordt uitgevoerd, kunt u er verbinding mee maken met behulp van HTTPS.

1. Klik op de **site-URL** om de webbrowser te openen.

   ![Klik op de site-URL](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. Wijzig in uw webbrowser de koppeling voor gebruik van **https** in plaats van **http** en ga vervolgens naar de pagina.

   > [!NOTE]
   > Als u een zelfondertekend certificaat gebruikt en u bladert naar een HTTPS-eind punt dat is gekoppeld aan het zelfondertekende certificaat, ziet u mogelijk een certificaat fout in de browser. Het gebruik van een certificaat dat is ondertekend door een vertrouwde certificerings instantie elimineert dit probleem. Ondertussen kunt u de fout negeren. (Een andere optie is het toevoegen van het zelfondertekende certificaat aan het certificaat archief van de vertrouwde certificerings instantie van de gebruiker.)
   >
   >

   ![Site preview](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Als u TLS wilt gebruiken voor een staging-implementatie in plaats van een productie-implementatie, moet u eerst de URL bepalen die wordt gebruikt voor de faserings implementatie. Zodra uw Cloud service is geïmplementeerd, wordt de URL naar de faserings omgeving bepaald door de GUID van de **implementatie-id** in deze indeling: `https://deployment-id.cloudapp.net/`  
   >
   > Maak een certificaat met de common name (CN) dat gelijk is aan de GUID-gebaseerde URL (bijvoorbeeld **328187776e774ceda8fc57609d404462.cloudapp.net**). Gebruik de portal om het certificaat toe te voegen aan uw gefaseerde Cloud service. Voeg vervolgens de certificaat gegevens toe aan uw CSDEF-en CSCFG-bestanden, verpakt uw toepassing opnieuw en werk uw gefaseerde implementatie bij om het nieuwe pakket te gebruiken.
   >

## <a name="next-steps"></a>Volgende stappen
* [Algemene configuratie van uw Cloud service](cloud-services-how-to-configure-portal.md).
* Meer informatie over het [implementeren van een Cloud service](cloud-services-how-to-create-deploy-portal.md).
* Een [aangepaste domein naam](cloud-services-custom-domain-name-portal.md)configureren.
* [Uw Cloud service beheren](cloud-services-how-to-manage-portal.md).



