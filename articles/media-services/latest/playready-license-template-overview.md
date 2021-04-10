---
title: Media Services micro soft PlayReady-licentie sjabloon
description: Meer informatie over Azure Media Services V3 met de PlayReady-licentie sjabloon en het configureren van PlayReady-licenties.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 23b0f92a4beca6e1c986993a42cc8ac66e619177
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455430"
---
# <a name="media-services-v3-with-playready-license-template"></a>Media Services V3 met PlayReady-licentie sjabloon

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Met Azure Media Services kunt u uw inhoud versleutelen met **micro soft PlayReady**. Media Services biedt ook een service voor het leveren van PlayReady-licenties. U kunt Media Services Api's gebruiken om PlayReady-licenties te configureren. Wanneer een speler uw met PlayReady beveiligde inhoud probeert af te spelen, wordt een aanvraag verzonden naar de service voor levering van licenties om een licentie te verkrijgen. Als de licentie service de aanvraag goedkeurt, wordt de licentie die naar de client wordt verzonden, uitgegeven en wordt de opgegeven inhoud gedecodeerd en afgespeeld.

PlayReady-licenties bevatten de rechten en beperkingen die u wilt dat de PlayReady digital rights management (DRM)-runtime wordt afgedwongen wanneer een gebruiker beveiligde inhoud wil afspelen. Hier volgen enkele voor beelden van PlayReady-licentie beperkingen die u kunt opgeven:

* De datum en tijd waarop de licentie geldig is.
* De waarde voor datum/tijd wanneer de licentie verloopt. 
* De licentie wordt opgeslagen in de permanente opslag op de client. Permanente licenties worden meestal gebruikt om het offline afspelen van de inhoud toe te staan.
* Het minimale beveiligings niveau dat een speler nodig heeft om uw inhoud af te spelen. 
* Het beveiligings niveau voor uitvoer voor de uitvoer besturings elementen voor audio\video-inhoud. 
* Zie voor meer informatie de sectie "uitvoer besturings elementen" (3,5) in het document met [nalevings regels voor PlayReady](https://www.microsoft.com/playready/licensing/compliance/) .

> [!NOTE]
> Op dit moment kunt u alleen de PlayRight van de PlayReady-licentie configureren. Dit recht is vereist. De PlayRight biedt de client de mogelijkheid om de inhoud af te spelen. U kunt ook de PlayRight gebruiken om beperkingen te configureren die specifiek zijn voor afspelen. 
> 

In dit onderwerp wordt beschreven hoe u PlayReady-licenties configureert met Media Services.

## <a name="basic-streaming-license-example"></a>Voor beeld van Basic streaming-licentie

In het volgende voor beeld ziet u de meest eenvoudige (en meest voorkomende) sjabloon waarmee een basis-streaming-licentie wordt geconfigureerd. Met deze licentie kunnen uw clients uw inhoud die is beveiligd met PlayReady, afspelen. 

De XML voldoet aan het XML-schema voor de PlayReady-licentie sjabloon dat is gedefinieerd in de sectie [XML-schema van playready-licentie sjabloon](#schema) .

```xml
<?xml version="1.0" encoding="utf-8"?>
<PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" 
                                  xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
    <LicenseTemplates>
    <PlayReadyLicenseTemplate>
        <ContentKey i:type="ContentEncryptionKeyFromHeader" />
        <PlayRight />
    </PlayReadyLicenseTemplate>
    </LicenseTemplates>
</PlayReadyLicenseResponseTemplate>
```

## <a name="use-media-services-apis-to-configure-license-templates"></a><a id="classes"></a>Media Services-Api's gebruiken om licentie sjablonen te configureren

Media Services biedt typen die u kunt gebruiken voor het configureren van een PlayReady-licentie sjabloon. 

In het volgende fragment wordt gebruikgemaakt van Media Services .NET-klassen voor het configureren van de PlayReady-licentie sjabloon. De klassen worden gedefinieerd in de naam ruimte [micro soft. Azure. Management. media. model](/dotnet/api/microsoft.azure.management.media.models) . Met het fragment wordt de PlayRight van de PlayReady-licentie geconfigureerd. PlayRight verleent de gebruiker de mogelijkheid om de inhoud af te spelen, afhankelijk van eventuele beperkingen die zijn geconfigureerd in de licentie en op de PlayRight zelf (voor een specifiek beleid). Veel van het beleid voor een PlayRight heeft betrekking op uitvoer beperkingen die de typen uitvoer bepalen waarmee de inhoud kan worden afgespeeld. Het bevat ook alle beperkingen die moeten worden ingevoerd wanneer een bepaalde uitvoer wordt gebruikt. Als DigitalVideoOnlyContentRestriction bijvoorbeeld is ingeschakeld, staat de DRM-runtime alleen toe dat de video wordt weer gegeven via digitale uitvoer. (Analoge video-uitvoer zijn niet toegestaan om de inhoud door te geven.)

> [!IMPORTANT]
> PlayReady-licentie heeft beperkingen die krachtig zijn. Als de uitvoer beveiliging te beperkend is, is de inhoud mogelijk niet kan worden afgespeeld op sommige clients. Zie voor meer informatie de [nalevings regels voor PlayReady](https://www.microsoft.com/playready/licensing/compliance/).

### <a name="configure-playready-license-template-with-net"></a>PlayReady-licentie sjabloon configureren met .NET

```csharp
ContentKeyPolicyPlayReadyLicense objContentKeyPolicyPlayReadyLicense;
objContentKeyPolicyPlayReadyLicense = new ContentKeyPolicyPlayReadyLicense
{
    AllowTestDevices = true,
    BeginDate = new DateTime(2016, 1, 1),
    ContentKeyLocation = new ContentKeyPolicyPlayReadyContentEncryptionKeyFromHeader(),
    ContentType = ContentKeyPolicyPlayReadyContentType.UltraVioletStreaming,
    LicenseType = drmSettings.EnableOfflineMode ? ContentKeyPolicyPlayReadyLicenseType.Persistent : ContentKeyPolicyPlayReadyLicenseType.NonPersistent,
    PlayRight = new ContentKeyPolicyPlayReadyPlayRight
    {
        ImageConstraintForAnalogComponentVideoRestriction = true,
        ExplicitAnalogTelevisionOutputRestriction = new ContentKeyPolicyPlayReadyExplicitAnalogTelevisionRestriction(true, 2),
        AllowPassingVideoContentToUnknownOutput = ContentKeyPolicyPlayReadyUnknownOutputPassingOption.Allowed,
        FirstPlayExpiration = TimeSpan.FromSeconds(20.0),
    }
};
```

## <a name="playready-license-template-xml-schema"></a><a id="schema"></a>XML-schema voor PlayReady-licentie sjablonen

```xml
<?xml version="1.0" encoding="utf-8"?>
<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:ser="http://schemas.microsoft.com/2003/10/Serialization/" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
    <xs:import namespace="http://schemas.microsoft.com/2003/10/Serialization/" />
    <xs:complexType name="AgcAndColorStripeRestriction">
        <xs:sequence>
            <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
    </xs:complexType>
    <xs:element name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction" />
    <xs:simpleType name="ContentType">
        <xs:restriction base="xs:string">
            <xs:enumeration value="Unspecified" />
            <xs:enumeration value="UltravioletDownload" />
            <xs:enumeration value="UltravioletStreaming" />
        </xs:restriction>
    </xs:simpleType>
    <xs:element name="ContentType" nillable="true" type="tns:ContentType" />
    <xs:complexType name="ExplicitAnalogTelevisionRestriction">
        <xs:sequence>
            <xs:element minOccurs="0" name="BestEffort" type="xs:boolean" />
            <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
    </xs:complexType>
    <xs:element name="ExplicitAnalogTelevisionRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction" />
    <xs:complexType name="PlayReadyContentKey">
        <xs:sequence />
    </xs:complexType>
    <xs:element name="PlayReadyContentKey" nillable="true" type="tns:PlayReadyContentKey" />
    <xs:complexType name="ContentEncryptionKeyFromHeader">
        <xs:complexContent mixed="false">
            <xs:extension base="tns:PlayReadyContentKey">
                <xs:sequence />
            </xs:extension>
        </xs:complexContent>
    </xs:complexType>
    <xs:element name="ContentEncryptionKeyFromHeader" nillable="true" type="tns:ContentEncryptionKeyFromHeader" />
    <xs:complexType name="ContentEncryptionKeyFromKeyIdentifier">
        <xs:complexContent mixed="false">
            <xs:extension base="tns:PlayReadyContentKey">
                <xs:sequence>
                    <xs:element minOccurs="0" name="KeyIdentifier" type="ser:guid" />
                </xs:sequence>
            </xs:extension>
        </xs:complexContent>
    </xs:complexType>
    <xs:element name="ContentEncryptionKeyFromKeyIdentifier" nillable="true" type="tns:ContentEncryptionKeyFromKeyIdentifier" />
    <xs:complexType name="PlayReadyLicenseResponseTemplate">
        <xs:sequence>
            <xs:element name="LicenseTemplates" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
            <xs:element minOccurs="0" name="ResponseCustomData" nillable="true" type="xs:string">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
        </xs:sequence>
    </xs:complexType>
    <xs:element name="PlayReadyLicenseResponseTemplate" nillable="true" type="tns:PlayReadyLicenseResponseTemplate" />
    <xs:complexType name="ArrayOfPlayReadyLicenseTemplate">
        <xs:sequence>
            <xs:element minOccurs="0" maxOccurs="unbounded" name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
        </xs:sequence>
    </xs:complexType>
    <xs:element name="ArrayOfPlayReadyLicenseTemplate" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
    <xs:complexType name="PlayReadyLicenseTemplate">
        <xs:sequence>
            <xs:element minOccurs="0" name="AllowTestDevices" type="xs:boolean" />
            <xs:element minOccurs="0" name="BeginDate" nillable="true" type="xs:dateTime">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element name="ContentKey" nillable="true" type="tns:PlayReadyContentKey" />
            <xs:element minOccurs="0" name="ContentType" type="tns:ContentType">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="ExpirationDate" nillable="true" type="xs:dateTime">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="GracePeriod" nillable="true" type="ser:duration">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="LicenseType" type="tns:PlayReadyLicenseType" />
            <xs:element minOccurs="0" name="PlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
        </xs:sequence>
    </xs:complexType>
    <xs:element name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
    <xs:simpleType name="PlayReadyLicenseType">
        <xs:restriction base="xs:string">
            <xs:enumeration value="Nonpersistent" />
            <xs:enumeration value="Persistent" />
        </xs:restriction>
    </xs:simpleType>
    <xs:element name="PlayReadyLicenseType" nillable="true" type="tns:PlayReadyLicenseType" />
    <xs:complexType name="PlayReadyPlayRight">
        <xs:sequence>
            <xs:element minOccurs="0" name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="AllowPassingVideoContentToUnknownOutput" type="tns:UnknownOutputPassingOption">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="AnalogVideoOpl" nillable="true" type="xs:int">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="CompressedDigitalAudioOpl" nillable="true" type="xs:int">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="CompressedDigitalVideoOpl" nillable="true" type="xs:int">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="DigitalVideoOnlyContentRestriction" type="xs:boolean">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="ExplicitAnalogTelevisionOutputRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="FirstPlayExpiration" nillable="true" type="ser:duration">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="ImageConstraintForAnalogComponentVideoRestriction" type="xs:boolean">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="ImageConstraintForAnalogComputerMonitorRestriction" type="xs:boolean">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="UncompressedDigitalAudioOpl" nillable="true" type="xs:int">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
            <xs:element minOccurs="0" name="UncompressedDigitalVideoOpl" nillable="true" type="xs:int">
                <xs:annotation>
                    <xs:appinfo>
                        <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
                    </xs:appinfo>
                </xs:annotation>
            </xs:element>
        </xs:sequence>
    </xs:complexType>
    <xs:element name="PlayReadyPlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
    <xs:simpleType name="UnknownOutputPassingOption">
        <xs:restriction base="xs:string">
            <xs:enumeration value="NotAllowed" />
            <xs:enumeration value="Allowed" />
            <xs:enumeration value="AllowedWithVideoConstriction" />
        </xs:restriction>
    </xs:simpleType>
    <xs:element name="UnknownOutputPassingOption" nillable="true" type="tns:UnknownOutputPassingOption" />
    <xs:complexType name="ScmsRestriction">
        <xs:sequence>
            <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
        </xs:sequence>
    </xs:complexType>
    <xs:element name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction" />
</xs:schema>
```

## <a name="next-steps"></a>Volgende stappen

Lees hoe u kunt [beveiligen met DRM](protect-with-drm.md)
