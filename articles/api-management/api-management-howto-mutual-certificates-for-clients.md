---
title: Api's beveiligen met verificatie op basis van client certificaten in API Management
titleSuffix: Azure API Management
description: Meer informatie over het beveiligen van de toegang tot Api's met behulp van client certificaten. U kunt beleids expressies gebruiken om binnenkomende certificaten te valideren.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 553b4527796db3e5d0f430afd6c5e614626187e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988888"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API's beveiligen met behulp van verificatie via clientcertificaten in API Management

API Management biedt de mogelijkheid om de toegang tot Api's (client naar API Management) te beveiligen met behulp van client certificaten. U kunt het inkomende certificaat valideren en certificaat eigenschappen controleren op basis van de gewenste waarden met behulp van beleids expressies.

Voor informatie over het beveiligen van de toegang tot de back-end-service van een API met behulp van client certificaten (API Management naar back-end), raadpleegt u [back-end-services beveiligen met verificatie op basis van client certificaten](./api-management-howto-mutual-certificates.md)

> [!IMPORTANT]
> Als u client certificaten wilt ontvangen en controleren via HTTP/2 in de lagen ontwikkelaar, Basic, Standard of Premium, moet u de instelling ' onderhandelen over client certificaat ' op de Blade ' aangepaste domeinen ' inschakelen, zoals hieronder wordt weer gegeven.

![Onderhandelen over client certificaat](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Als u client certificaten wilt ontvangen en verifiëren in de laag verbruik, moet u de instelling client certificaat aanvragen inschakelen op de Blade aangepaste domeinen, zoals hieronder wordt weer gegeven.

![Client certificaat aanvragen](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>De uitgever en het onderwerp controleren

Onder beleids regels kunnen worden geconfigureerd om de uitgever en het onderwerp van een client certificaat te controleren:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Als u het controleren van de lijst met ingetrokken certificaten wilt uitschakelen, gebruikt u `context.Request.Certificate.VerifyNoRevocation()` in plaats van `context.Request.Certificate.Verify()` .
> Als het client certificaat zelfondertekend is, moeten de basis-(of tussenliggende) CA-certificaten worden [geüpload](api-management-howto-ca-certificates.md) naar API Management for `context.Request.Certificate.Verify()` en `context.Request.Certificate.VerifyNoRevocation()` to work.

## <a name="checking-the-thumbprint"></a>De vinger afdruk controleren

Onder beleids regels kunnen worden geconfigureerd om de vinger afdruk van een client certificaat te controleren:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Als u het controleren van de lijst met ingetrokken certificaten wilt uitschakelen, gebruikt u `context.Request.Certificate.VerifyNoRevocation()` in plaats van `context.Request.Certificate.Verify()` .
> Als het client certificaat zelfondertekend is, moeten de basis-(of tussenliggende) CA-certificaten worden [geüpload](api-management-howto-ca-certificates.md) naar API Management for `context.Request.Certificate.Verify()` en `context.Request.Certificate.VerifyNoRevocation()` to work.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Een vinger afdruk controleren op certificaten die zijn geüpload naar API Management

In het volgende voor beeld ziet u hoe u de vinger afdruk van een client certificaat controleert op certificaten die zijn geüpload naar API Management:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Als u het controleren van de lijst met ingetrokken certificaten wilt uitschakelen, gebruikt u `context.Request.Certificate.VerifyNoRevocation()` in plaats van `context.Request.Certificate.Verify()` .
> Als het client certificaat zelfondertekend is, moeten de basis-(of tussenliggende) CA-certificaten worden [geüpload](api-management-howto-ca-certificates.md) naar API Management for `context.Request.Certificate.Verify()` en `context.Request.Certificate.VerifyNoRevocation()` to work.

> [!TIP]
> Probleem met het publiceren van het client certificaat dat in dit [artikel](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) wordt beschreven, kan zich op verschillende manieren manifesteren, zoals het blok keren van aanvragen, aanvragen met de `403 Forbidden` status code na een time-out `context.Request.Certificate` `null` . Dit probleem is doorgaans `POST` van invloed op en `PUT` aanvragen met een inhouds lengte van ongeveer 60KB of hoger.
> Ga als volgt te werk om te voor komen dat dit probleem optreedt bij het inschakelen van de instelling onderhandelend client certificaat voor gewenste hostnamen op de Blade Custom Domains, zoals wordt weer gegeven in de eerste afbeelding van dit document. Deze functie is niet beschikbaar in de laag verbruik.

## <a name="certificate-validation-in-self-hosted-gateway"></a>Certificaat validatie in een zelf-hostende gateway

De standaard [-API Management zelf-hostende gateway](self-hosted-gateway-overview.md) installatie kopie biedt geen ondersteuning voor het valideren van server-en client certificaten met behulp van [CA-basis certificaten](api-management-howto-ca-certificates.md) die zijn geüpload naar een API Management-exemplaar. Clients die een aangepast certificaat aan de zelf-hostende gateway aanbieden, kunnen trage reacties ondervinden, omdat validatie van de intrekkings lijst (CRL) lang kan duren op de gateway. 

Als tijdelijke oplossing voor het uitvoeren van de gateway kunt u het IP-adres van de PKI zo configureren dat dit verwijst naar het localhost-adres (127.0.0.1) in plaats van het API Management-exemplaar. Dit zorgt ervoor dat de CRL-validatie snel mislukt wanneer de gateway het client certificaat probeert te valideren. Als u de gateway wilt configureren, voegt u een DNS-vermelding voor het API Management-exemplaar toe om te leiden naar de localhost in het `/etc/hosts` bestand in de container. U kunt deze vermelding toevoegen tijdens de implementatie van de gateway:
 
* Voor docker-implementatie: Voeg de `--add-host <hostname>:127.0.0.1` para meter toe aan de `docker run` opdracht. Zie [vermeldingen toevoegen aan container bestand hosts](https://docs.docker.com/engine/reference/commandline/run/#add-entries-to-container-hosts-file---add-host) voor meer informatie.
 
* Voor de implementatie van Kubernetes-een `hostAliases` specificatie toevoegen aan het `myGateway.yaml` configuratie bestand. Zie [vermeldingen toevoegen aan pod bestand/etc/hosts met Host-aliassen](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)voor meer informatie.




## <a name="next-steps"></a>Volgende stappen

-   [Back-end-services beveiligen met verificatie op basis van client certificaten](./api-management-howto-mutual-certificates.md)
-   [Certificaten uploaden](./api-management-howto-mutual-certificates.md)
