---
title: Certificaten configureren voor toepassingen op Linux
description: Certificaten voor uw app configureren met de Service Fabric runtime op een Linux-cluster
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: pepogors
ms.openlocfilehash: 70f9cc38d84681f68c10882889214648a4dd2624
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785563"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certificaten en beveiliging op Linux-clusters

Dit artikel bevat informatie over het configureren van X. 509-certificaten op Linux-clusters.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Locatie en indeling van X. 509-certificaten op Linux-knoop punten

Service Fabric verwacht in het algemeen dat X. 509-certificaten aanwezig zijn in de */var/lib/sfcerts* -Directory op Linux-cluster knooppunten. Dit geldt voor cluster certificaten, client certificaten, enzovoort. In sommige gevallen kunt u een andere locatie dan de map *var/lib/sfcerts* voor certificaten opgeven. Als u bijvoorbeeld Reliable Services hebt gemaakt met de Service Fabric Java SDK, kunt u een andere locatie opgeven via het configuratie pakket (Settings.xml) voor sommige toepassingsspecifieke certificaten. Zie [certificaten waarnaar wordt verwezen in het configuratie pakket (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml)voor meer informatie.

Voor Linux-clusters wordt Service Fabric verwacht dat certificaten aanwezig zijn als een. pem-bestand dat zowel het certificaat als de persoonlijke sleutel bevat, of als een. crt-bestand dat het certificaat en een. key-bestand bevat dat de persoonlijke sleutel bevat. Alle bestanden moeten de indeling PEM hebben. 

Als u uw certificaat van Azure Key Vault installeert met behulp van een [Resource Manager-sjabloon](./service-fabric-cluster-creation-create-template.md) of [Power shell](/powershell/module/az.servicefabric/) -opdrachten, wordt het certificaat geïnstalleerd in de juiste indeling in de map */var/lib/sfcerts* op elk knoop punt. Als u een certificaat installeert via een andere methode, moet u ervoor zorgen dat het certificaat correct is geïnstalleerd op cluster knooppunten.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certificaten waarnaar wordt verwezen in het toepassings manifest

Certificaten die zijn opgegeven in het manifest van de toepassing, bijvoorbeeld via de elementen [**SecretsCertificate**](./service-fabric-service-model-schema-elements.md#secretscertificate-element) of [**EndpointCertificate**](./service-fabric-service-model-schema-elements.md#endpointcertificate-element) , moeten aanwezig zijn in de map */var/lib/sfcerts* . De elementen die worden gebruikt om certificaten in het manifest van de toepassing op te geven, hebben geen kenmerk path, dus de certificaten moeten aanwezig zijn in de standaard directory. Deze elementen hebben een optioneel **X509StoreName** -kenmerk. De standaard waarde is ' My ', die verwijst naar de map */var/lib/sfcerts* op Linux-knoop punten. Een andere waarde is niet gedefinieerd in een Linux-cluster. Het is raadzaam dat u het kenmerk **X509StoreName** weglaat voor apps die worden uitgevoerd op Linux-clusters. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certificaten waarnaar wordt verwezen in het configuratie pakket (Settings.xml)

Voor sommige services kunt u X. 509-certificaten configureren in de [ConfigPackage](./service-fabric-application-and-service-manifests.md) (standaard Settings.xml). Dit is bijvoorbeeld het geval bij het declareren van certificaten die worden gebruikt voor het beveiligen van RPC-kanalen voor Reliable Services services die zijn gebouwd met de Service Fabric .NET core-of Java-Sdk's. Er zijn twee manieren om te verwijzen naar certificaten in het configuratie pakket. Ondersteuning is afhankelijk van de .NET core-en Java-Sdk's.

### <a name="using-x509-securitycredentialstype"></a>X509-SecurityCredentialsType gebruiken

Met de .NET-of Java-Sdk's kunt u **x509** opgeven voor de **SecurityCredentialsType**. Dit komt overeen met het `X509Credentials` ([.net](/previous-versions/azure/reference/mt124925(v=azure.100)) / [Java](/java/api/system.fabric.x509credentials))-type `SecurityCredentials` ([](/previous-versions/azure/reference/mt124894(v=azure.100)) / [](/java/api/system.fabric.securitycredentials).net java).

De **x509** -verwijzing zoekt naar het certificaat in een certificaat archief. Het volgende XML-bestand toont de para meters die worden gebruikt om de locatie van het certificaat op te geven:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Voor een service die op Linux wordt uitgevoerd, **LocalMachine** / **mijn** wijst naar de standaard locatie voor certificaten, de */var/lib/sfcerts* -map. Voor Linux zijn alle andere Combi Naties van **CertificateStoreLocation** en **naam certificaat archief** niet gedefinieerd. 

Geef altijd **LocalMachine** op voor de para meter **CertificateStoreLocation** . U hoeft de para meter **naam certificaat archief** niet op te geven omdat deze standaard is ingesteld op My. Met een **x509** -verwijzing moeten de certificaat bestanden zich in de map */var/lib/sfcerts* op het cluster knooppunt bevinden.  

In het volgende XML-bestand wordt een **TransportSettings** -sectie weer gegeven op basis van deze stijl:

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x509_2-securitycredentialstype"></a>X509_2 SecurityCredentialsType gebruiken

Met de Java-SDK kunt u **X509_2** opgeven voor de **SecurityCredentialsType**. Dit komt overeen met het `X509Credentials2` ([Java](/java/api/system.fabric.x509credentials2))-type (Java) `SecurityCredentials` .[](/java/api/system.fabric.securitycredentials) 

Met een **X509_2** referentie geeft u een pad para meter op, zodat u het certificaat in een andere map dan */var/lib/sfcerts* kunt vinden.  Het volgende XML-bestand toont de para meters die worden gebruikt om de locatie van het certificaat op te geven: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

In het volgende XML-bestand wordt een **TransportSettings** -sectie weer gegeven op basis van deze stijl.

```xml
<!--Section name should always end with "TransportSettings".-->
<!--Here we are using a prefix "HelloWorldStateless".-->
<Section Name="HelloWorldStatelessTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509_2" />
    <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
    <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
</Section>
```

> [!NOTE]
> Het certificaat wordt opgegeven als een. crt-bestand in de voor gaande XML. Dit betekent dat er ook een. key-bestand is met de persoonlijke sleutel op dezelfde locatie.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Een Reliable Services-app configureren om uit te voeren op Linux-clusters

Met de Service Fabric Sdk's kunt u communiceren met de Service Fabric runtime-Api's om gebruik te maken van het platform. Wanneer u een toepassing uitvoert die gebruikmaakt van deze functionaliteit op beveiligde Linux-clusters, moet u uw toepassing configureren met een certificaat dat kan worden gebruikt om te valideren met de Service Fabric runtime. Toepassingen die Service Fabric betrouw bare service services die zijn geschreven met de .NET core-of Java-Sdk's, hebben deze configuratie nodig. 

Als u een toepassing wilt configureren, voegt u een [**SecretsCertificate**](./service-fabric-service-model-schema-elements.md#secretscertificate-element) -element toe onder het label **certificaten** . Deze bevindt zich onder de tag **ApplicationManifest** in het *ApplicationManifest.xml* -bestand. De volgende XML-code bevat een certificaat waarnaar wordt verwezen door de vinger afdruk: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

U kunt verwijzen naar het cluster certificaat of een certificaat dat u op elk cluster knooppunt installeert. In Linux moeten de certificaat bestanden aanwezig zijn in de */var/lib/sfcerts* -map. Zie [locatie en indeling van X. 509-certificaten op Linux-knoop punten](#location-and-format-of-x509-certificates-on-linux-nodes)voor meer informatie.
