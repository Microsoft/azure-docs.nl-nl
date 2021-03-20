---
title: Beveiligde communicatie van Azure Service Fabric reverse proxy
description: Configureer omgekeerde proxy zo dat beveiligde end-to-end-communicatie mogelijk is in een Azure Service Fabric-toepassing.
ms.topic: conceptual
ms.date: 08/10/2017
ms.openlocfilehash: b01ce559b3c790164992d6618149afa9df069466
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86256132"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Verbinding maken met een beveiligde service met de omgekeerde proxy

In dit artikel wordt uitgelegd hoe u een veilige verbinding tot stand brengt tussen de omgekeerde proxy en services, waardoor een end-to-end beveiligd kanaal kan worden ingeschakeld. Zie [reverse proxy in Azure service Fabric](service-fabric-reverseproxy.md) voor meer informatie over reverse proxy

> [!IMPORTANT]
> Verbinding maken met beveiligde services wordt alleen ondersteund als omgekeerde proxy is geconfigureerd om te Luis teren op HTTPS. In dit artikel wordt ervan uitgegaan dat dit het geval is. Raadpleeg [reverse proxy instellen in Azure service Fabric](service-fabric-reverseproxy-setup.md) voor het configureren van de omgekeerde proxy in service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Beveiligde verbinding tot stand brengen tussen de omgekeerde proxy en services 

### <a name="reverse-proxy-authenticating-to-services"></a>Omgekeerde proxy verificatie bij Services:
De omgekeerde proxy identificeert zichzelf bij Services met behulp van het certificaat. Voor Azure-clusters wordt het certificaat met de eigenschap ***reverseProxyCertificate** _ opgegeven in de sectie met het [resource type](../azure-resource-manager/templates/template-syntax.md) [*micro soft. ServiceFabric/clusters* *](/azure/templates/microsoft.servicefabric/clusters) van de Resource Manager-sjabloon. Voor zelfstandige clusters wordt het certificaat opgegeven met de **eigenschap _ReverseProxyCertificate_*_ of _*_ReverseProxyCertificateCommonNames_*_ in het gedeelte _* beveiliging** van ClusterConfig.jsop. Zie [reverse proxy inschakelen op zelfstandige clusters](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters)voor meer informatie. 

Services kunnen de logica implementeren om het certificaat te verifiëren dat door de omgekeerde proxy wordt aangeboden. De services kunnen de geaccepteerde client certificaat gegevens opgeven als configuratie-instellingen in het configuratie pakket. Dit kan tijdens runtime worden gelezen en wordt gebruikt om het certificaat te valideren dat door de omgekeerde proxy wordt aangeboden. Raadpleeg [toepassings parameters beheren](service-fabric-manage-multiple-environment-app-configuration.md) om de configuratie-instellingen toe te voegen. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Omgekeerde proxy voor het controleren van de identiteit van de service via het certificaat dat door de service wordt aangeboden:
Omgekeerde proxy ondersteunt de volgende beleids regels voor het uitvoeren van server certificaat validatie van de certificaten die worden gepresenteerd door services: none, ServiceCommonNameAndIssuer en ServiceCertificateThumbprints.
Als u het beleid voor het gebruik van een reverse proxy wilt selecteren, geeft u de **ApplicationCertificateValidationPolicy** op in de sectie **toepassings Gateway/http** onder [fabricSettings](service-fabric-cluster-fabric-settings.md).

In de volgende sectie worden de configuratie Details voor elk van deze opties weer gegeven.

### <a name="service-certificate-validation-options"></a>Opties voor validatie van service certificaat 

- **Geen**: omgekeerde proxy slaat de verificatie van het certificaat van de proxy service over en brengt de beveiligde verbinding tot stand. Dit is de standaardinstelling.
Geef de **ApplicationCertificateValidationPolicy** op met de waarde **none** in de sectie [**toepassings Gateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "None"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCommonNameAndIssuer**: omgekeerde proxy verifieert het certificaat dat door de service wordt gepresenteerd op basis van de algemene naam van het certificaat en de vinger afdruk van de verlener: Geef de **ApplicationCertificateValidationPolicy** met de waarde **ServiceCommonNameAndIssuer** op in de sectie [**toepassings Gateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCommonNameAndIssuer"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Als u de lijst met algemene en uitgevers van services wilt opgeven, voegt u een sectie [**toepassings Gateway/http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) onder **fabricSettings** toe, zoals hieronder wordt weer gegeven. Er kunnen meerdere algemene naam-en verleners vingerafdruk paren worden toegevoegd in de **parameter** matrix. 

   Als de reverse-proxy van het eind punt verbinding maakt met een certificaat dat de algemene naam en de verleners-vinger afdruk overeenkomt met een van de waarden die hier zijn opgegeven, wordt er een TLS-kanaal tot stand gebracht.
   Als de certificaat details niet overeenkomen, mislukt de aanvraag van de client met de status code 502 (ongeldige gateway). De HTTP-status regel bevat ook de zin ' ongeldig SSL-certificaat '. 

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
               "parameters": [
                 {
                   "name": "WinFabric-Test-Certificate-CN1",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
                 },
                 {
                   "name": "WinFabric-Test-Certificate-CN2",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCertificateThumbprints**: bij een omgekeerde proxy wordt het certificaat van de proxy service gecontroleerd op basis van de vinger afdruk. U kunt ervoor kiezen om deze route te gaan wanneer de services worden geconfigureerd met zelfondertekende certificaten: Geef de **ApplicationCertificateValidationPolicy** op met de waarde **ServiceCertificateThumbprints** in het gedeelte [**toepassings Gateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCertificateThumbprints"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Geef ook de vinger afdrukken op met een **ServiceCertificateThumbprints** -vermelding in de sectie **toepassings Gateway/http** . Meerdere vinger afdrukken kunnen worden opgegeven als een door komma's gescheiden lijst in het waardeveld, zoals hieronder wordt weer gegeven:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                   ...
                 {
                   "name": "ServiceCertificateThumbprints",
                   "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Als de vinger afdruk van het server certificaat wordt weer gegeven in deze configuratie vermelding, slaagt de TLS-verbinding met de omgekeerde proxy. Anders wordt de verbinding beëindigd en mislukt de aanvraag van de client met een 502 (ongeldige gateway). De HTTP-status regel bevat ook de zin ' ongeldig SSL-certificaat '.

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Een eindpunt selectie logica wanneer Services beveiligde en onbeveiligde eind punten weer geven
Service Fabric ondersteunt het configureren van meerdere eind punten voor een service. Zie [resources opgeven in een service manifest](service-fabric-service-manifest-resources.md)voor meer informatie.

Omgekeerde proxy selecteert een van de eind punten om de aanvraag door te sturen op basis van de **ListenerName** -query parameter in de [service-URI](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Als de para meter **ListenerName** niet is opgegeven, kan de omgekeerde proxy een wille keurig eind punt uit de lijst met eind punten kiezen. Afhankelijk van de eind punten die voor de service zijn geconfigureerd, kan het geselecteerde eind punt een HTTP-of HTTPS-eind punt zijn. Er zijn mogelijk scenario's of vereisten waar u wilt dat de omgekeerde proxy in de modus ' alleen beveiligd ' wordt uitgevoerd. dat wil zeggen dat u niet wilt dat de beveiligde omgekeerde proxy aanvragen doorstuurt naar niet-beveiligde eind punten. Als u omgekeerde proxy wilt instellen op de modus alleen beveiligd, geeft u de **SecureOnlyMode** -configuratie vermelding met de waarde **True** op in de sectie [**toepassings Gateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> [!NOTE]
> Als een client een **ListenerName** heeft opgegeven die overeenkomt met een http-eind punt (niet-beveiligd), mislukt de reverse-proxy op de aanvraag met een 404 (niet gevonden) http-status code in **SecureOnlyMode**.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Verificatie van client certificaten instellen via de omgekeerde proxy
TLS-beëindiging gebeurt bij de omgekeerde proxy en alle gegevens van het client certificaat gaan verloren. Geef voor de services verificatie van client certificaten op de **ForwardClientCertificate** -instelling in de sectie [**toepassings Gateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) op.

1. Wanneer **ForwardClientCertificate** is ingesteld op **False**, vraagt reverse proxy het client certificaat niet aan tijdens de TLS-Handshake van de client.
Dit is de standaardinstelling.

2. Wanneer **ForwardClientCertificate** is ingesteld op **True**, vraagt reverse proxy het client certificaat aan tijdens de TLS-Handshake met de client.
De client certificaat gegevens worden vervolgens doorgestuurd naar een aangepaste HTTP-header met de naam **X-client-certificaat**. De waarde van de header is de base64-gecodeerde PEM-indelings teken reeks van het certificaat van de client. De service kan de aanvraag slagen of afwijzen met de juiste status code na het controleren van de certificaat gegevens.
Als de client geen certificaat presenteert, stuurt de omgekeerde proxy een lege header door en kan de service de aanvraag afhandelen.

> [!NOTE]
> Omgekeerde proxy fungeert alleen als een doorstuur service. Er wordt geen validatie uitgevoerd van het certificaat van de client.


## <a name="next-steps"></a>Volgende stappen
* [Stel een omgekeerde proxy in op een cluster en configureer](service-fabric-reverseproxy-setup.md)deze.
* Raadpleeg [reverse proxy configureren om verbinding te maken met beveiligde services](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* Bekijk een voor beeld van HTTP-communicatie tussen services in een [voorbeeld project op github](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Externe procedure aanroepen met Reliable Services externe toegang](service-fabric-reliable-services-communication-remoting.md)
* [Web-API die gebruikmaakt van OWIN in Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Clustercertificaten beheren](service-fabric-cluster-security-update-certs-azure.md)
