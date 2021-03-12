---
title: Beveiligings beleid voor inhoud configureren met behulp van de Azure Portal | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de Azure Portal gebruikt voor het configureren van beveiligings beleid voor inhoud. In dit artikel wordt ook beschreven hoe u dynamische versleuteling voor uw assets inschakelt.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: c6620158965271ba99f80c2fbd36504fdbea7ecf
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010776"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Beveiligings beleid voor inhoud configureren met behulp van de Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.   > er geen nieuwe functies of functionaliteit aan Media Services v2 worden toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)
>

 Met Azure Media Services kunt u uw media beveiligen vanaf het moment dat de computer de opslag, verwerking en levering verlaat. U kunt Media Services gebruiken om uw inhoud dynamisch te versleutelen met de Advanced Encryption Standard (AES) met behulp van 128-bits versleutelings sleutels. U kunt dit ook gebruiken met common Encryption (CENC) met behulp van PlayReady en/of Widevine digital rights management (DRM) en Apple FairPlay. 

Media Services levert een service voor het leveren van DRM-licenties en AES-sleutels aan geautoriseerde clients. U kunt de Azure Portal gebruiken om één sleutel/licentie autorisatie beleid te maken voor alle typen versleuteling.

In dit artikel wordt beschreven hoe u een beveiligings beleid voor inhoud configureert met behulp van de portal. In dit artikel wordt ook beschreven hoe u dynamische versleuteling toepast op uw activa.

## <a name="start-to-configure-content-protection"></a>Beginnen met het configureren van inhouds beveiliging
Voer de volgende stappen uit om de portal te gebruiken voor het configureren van globale inhouds beveiliging met behulp van uw Media Services-account:

1. Selecteer uw Media Services-account in de [Portal](https://portal.azure.com/).

1. Selecteer **instellingen**  >  **Content Protection**.

    ![Inhoudsbeveiliging](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Autorisatie beleid voor sleutels/licenties
Media Services ondersteunt meerdere manieren om gebruikers te verifiëren die sleutel-of licentie aanvragen maken. U moet het autorisatiebeleid voor de inhoudssleutel configureren. De client moet voldoen aan het beleid voordat de sleutel/licentie kan worden geleverd. Het autorisatiebeleid voor inhoudssleutels kan een of meer autorisatiebeperkingen hebben: beperking voor openen of tokenbeperkingen.

U kunt de portal gebruiken om één sleutel/licentie autorisatie beleid te maken voor alle typen versleuteling.

### <a name="open-authorization"></a>Autorisatie openen
Beperking openen betekent dat het systeem de sleutel levert aan iedereen die een belang rijke aanvraag doet. Deze beperking kan handig zijn voor test doeleinden. 

### <a name="token-authorization"></a>Token autorisatie
Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door een beveiligingstokenservice (STS). Media Services ondersteunt tokens in de indelingen Simple Web Tokens (SWT) en JSON Web Token (JWT). Media Services biedt geen STS. U kunt een aangepaste STS maken of Azure Access Control Service gebruiken om tokens te verlenen. De STS moet worden geconfigureerd om een token te maken dat is ondertekend met de opgegeven sleutel en claims uitgeven die u hebt opgegeven in de configuratie van de token beperking. Als het token geldig is en de claims in het token overeenkomen met die zijn geconfigureerd voor de sleutel (of licentie), retourneert de Media Services key delivery service de aangevraagde sleutel (of licentie) naar de client.

Wanneer u het token beperkt beleid configureert, moet u de para meters voor de primaire verificatie sleutel, verlener en doel groep opgeven. De primaire verificatie sleutel bevat de sleutel waarmee het token is ondertekend. De uitgever is de beveiligde token service waarmee het token wordt uitgegeven. De doel groep (ook wel bereik genoemd) beschrijft de bedoeling van het token of de bron waarvan het token toegang verleent. Met de Media Services key delivery service wordt gecontroleerd of deze waarden in het token overeenkomen met de waarden in de sjabloon.

![Autorisatie beleid voor sleutels/licenties](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady-licentiesjabloon
De PlayReady-licentie sjabloon stelt de functionaliteit in die is ingeschakeld voor uw PlayReady-licentie. Voor meer informatie over de PlayReady-licentie sjabloon raadpleegt u het overzicht van de [Media Services playready-licentie sjabloon](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Permanent
Als u een licentie als niet-persistent configureert, wordt deze alleen in het geheugen bewaard terwijl de speler de licentie gebruikt.  

![Niet-permanente inhouds beveiliging](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Permanent
Als u een licentie als permanent configureert, wordt deze opgeslagen in de permanente opslag op de client.

![Permanente inhouds beveiliging](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine-licentiesjabloon
De Widevine-licentie sjabloon stelt de functionaliteit in die is ingeschakeld voor uw Widevine-licenties.

### <a name="basic"></a>Basic
Wanneer u **basis** selecteert, wordt de sjabloon gemaakt met alle standaard waarden.

### <a name="advanced"></a>Geavanceerd
Zie het overzicht van de [Widevine-licentie sjablonen](media-services-widevine-license-template-overview.md)voor meer informatie over de Widevine rechten sjabloon.

![Geavanceerde inhouds beveiliging](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay-configuratie
Als u FairPlay-versleuteling wilt inschakelen, selecteert u **Fairplay-configuratie**. Selecteer vervolgens het **app-certificaat** en voer de sleutel van het **toepassings geheim** in. Zie [uw HLS-inhoud beveiligen met Apple FairPlay of micro soft PlayReady](media-services-protect-hls-with-FairPlay.md)voor meer informatie over Fairplay-configuratie en-vereisten.

![FairPlay-configuratie](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Dynamische versleuteling Toep assen op uw asset
Als u gebruik wilt maken van dynamische versleuteling, moet u het bron bestand coderen in een set adaptieve-bitrate MP4-bestanden.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Een activum selecteren dat u wilt versleutelen
Als u al uw assets wilt zien, selecteert u **instellingen**  >  **activa**.

![Activa optie](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Versleutelen met AES of DRM
Wanneer u **versleutelen** voor een activum selecteert, ziet u twee opties: **AES** of **DRM**. 

#### <a name="aes"></a>AES
AES Clear Key-versleuteling is ingeschakeld op alle streaming-protocollen: Smooth Streaming, HLS en MPEG-DASH.

![Versleutelings configuratie](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>Digital
1. Nadat u **DRM** hebt geselecteerd, ziet u verschillende beleids regels voor de beveiliging van inhoud (die moeten worden geconfigureerd door dit punt) en een set streaming-protocollen:

    a. **Playready en Widevine met MPEG-Dash** worden uw MPEG-Dash stream dynamisch versleuteld met Playready en Widevine DRMs.

    b. **Playready en Widevine met MPEG-Dash + Fairplay met HLS** worden uw MPEG-Dash stream dynamisch versleuteld met PlayReady en Widevine DRMs. Deze optie versleutelt ook uw HLS-streams met FairPlay.

    c. **PlayReady alleen met Smooth streaming, HLS en MPEG-Dash** versleutelt dynamisch Smooth streaming, HLS en MPEG-Dash-streams met PlayReady DRM.

    d. **Widevine alleen met MPEG-Dash** worden uw MPEG-Dash met Widevine DRM dynamisch versleuteld.
    
    e. **Fairplay alleen met HLS** worden uw HLS-stroom dynamisch versleuteld met Fairplay.

1. Als u FairPlay-versleuteling wilt inschakelen, selecteert u **Fairplay-configuratie** op de Blade **globale instellingen Content Protection** . Selecteer vervolgens het **app-certificaat** en voer de **geheime sleutel** van de toepassing in.

    ![Versleutelings type](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Nadat u de versleutelings selectie hebt gemaakt, selecteert u **Toep assen**.

>[!NOTE] 
>Als u van plan bent een AES-versleutelde HLS te spelen in Safari, raadpleegt u het blog bericht [versleutelde HLS in Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
