---
title: HTTPS configureren voor uw aangepaste domein in een Azure front deur Standard/Premium SKU-configuratie
description: In dit artikel leert u hoe u een aangepast domein kunt voorbereiden voor de Azure front deur Standard/Premium SKU.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: c2edf11939996156c2b589b0b7876ae1b01466e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740814"
---
# <a name="configure-https-on-a-front-door-standardpremium-sku-preview-custom-domain-using-the-azure-portal"></a>Configureer HTTPS op een aangepast domein voor de voor deur standaard/Premium SKU (preview) met behulp van de Azure Portal

> [!NOTE]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

Met Azure front deur Standard/Premium kunt u standaard beveiligde TLS-levering aan uw toepassingen inschakelen wanneer een aangepast domein wordt toegevoegd. Door gebruik te maken van het HTTPS-protocol in uw aangepaste domein, zorgt u ervoor dat uw gevoelige gegevens veilig worden afgeleverd met TLS/SSL-versleuteling wanneer deze via internet wordt verzonden. Wanneer uw webbrowser is verbonden met een website via HTTPS, wordt het beveiligingscertificaat van de website gevalideerd en wordt er gecontroleerd of het certificaat is uitgegeven door een legitieme certificeringsinstantie. Via dit proces zijn uw webtoepassingen beveiligd tegen aanvallen.

Azure front deur Standard/Premium ondersteunt zowel door Azure beheerd certificaat als door de klant beheerde certificaten. Azure front deur maakt standaard automatisch HTTPS voor al uw aangepaste domeinen met behulp van door Azure beheerde certificaten. Er zijn geen extra stappen vereist voor het ophalen van een door Azure beheerd certificaat. Er wordt een certificaat gemaakt tijdens het domein validatie proces. U kunt ook uw eigen certificaat gebruiken door Azure front-deur standaard/Premium te integreren met uw Key Vault.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [**Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Voordat u HTTPS voor uw aangepaste domein kunt configureren, moet u eerst een Azure front deur Standard/Premium-profiel maken. Zie [Quick Start: een Azure front deur Standard/Premium-profiel maken](create-front-door-portal.md)voor meer informatie.

* Als u nog geen aangepast domein hebt, moet u er eerst een aanschaffen bij een domein provider. Zie bijvoorbeeld [Een aangepaste domeinnaam kopen](../../app-service/manage-custom-dns-buy-domain.md).

* Als u Azure gebruikt voor het hosten van uw [DNS-domeinen](../../dns/dns-overview.md), moet u de DNS-naam (Domain Name System) van de domein provider overdragen aan een Azure DNS. Zie [Delegate a domain to Azure DNS](../../dns/dns-delegate-domain-azure-dns.md) (Een domein aan Azure DNS overdragen) voor meer informatie. Als u echter een domein provider gebruikt voor het verwerken van uw DNS-domein, moet u het domein hand matig valideren door desgevraagd DNS TXT-records in te voeren.

## <a name="azure-managed-certificates"></a>Door Azure beheerde certificaten

1. Onder instellingen voor het profiel voor Azure front-deur Standard/Premium selecteert u **domeinen** en selecteert u vervolgens **+ toevoegen** om een nieuw domein toe te voegen.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-new-custom-domain.png" alt-text="Scherm opname van de pagina voor de land instellingen van de domein configuratie.":::

1. Selecteer op de pagina **een domein toevoegen** voor *DNS-beheer* de optie door **Azure beheerde DNS** . 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-domain-azure-managed.png" alt-text="Scherm opname van een domein pagina toevoegen met door Azure beheerde DNS geselecteerd.":::

1. Valideer en koppel het aangepaste domein aan een eind punt door de stappen in het inschakelen van een [aangepast domein](how-to-add-custom-domain.md)te volgen.

1. Zodra het aangepaste domein aan het eind punt is gekoppeld, wordt een door Azure beheerd certificaat geïmplementeerd naar de voor deur. Het kan enkele minuten duren voordat dit proces is voltooid.

## <a name="using-your-own-certificate"></a>Uw eigen certificaat gebruiken

U kunt er ook voor kiezen om uw eigen TLS-certificaat te gebruiken. Dit certificaat moet in een Azure Key Vault worden geïmporteerd voordat u het kunt gebruiken met de standaard/Premium-functie van Azure voor de voor deur. Zie [een certificaat importeren](../../key-vault/certificates/tutorial-import-certificate.md) in azure Key Vault. 

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Voorbereiden van uw Azure Key Vault-account en -certificaat
 
1. U moet beschikken over een actief Azure Key Vault account onder hetzelfde abonnement als uw Azure front-deur Standard/Premium waarvoor u aangepaste HTTPS wilt inschakelen. Maak een Azure Key Vault-account als u er nog geen hebt.

    > [!WARNING]
    > Azure Front Door ondersteunt momenteel alleen Key Vault-accounts in hetzelfde abonnement als de Front Door-configuratie. Als u een Key Vault selecteert onder een ander abonnement dan voor de standaard/Premium van de Azure-deur, treedt er een fout op.

1. Als u al een certificaat hebt, kunt u het rechtstreeks naar uw Azure Key Vault-account uploaden. Als dat niet het geval is, maakt u een nieuw certificaat rechtstreeks via Azure Key Vault van een van de partner certificerings instanties die Azure Key Vault integreert met. Upload uw certificaat als een **certificaat**-object in plaats van een **geheim**.

    > [!NOTE]
    > Voor uw eigen TLS/SSL-certificaat biedt Front Door geen ondersteuning voor certificaten met EC-cryptografie-algoritmen.

#### <a name="register-azure-front-door"></a>Azure Front Door registreren

Registreer de service-principal voor Azure Front Door als een app in uw Azure Active Directory via PowerShell.

> [!NOTE]
> Voor deze actie zijn globale beheerdersmachtigingen vereist en de actie moet maar **eenmaal** per tenant worden uitgevoerd.

1. Installeer zo nodig [Azure PowerShell](/powershell/azure/install-az-ps) in PowerShell op uw lokale computer.

1. Voer in PowerShell de volgende opdracht uit:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Azure Front Door toegang verlenen tot uw sleutelkluis
 
Geef Azure Front Door toegang tot de certificaten in uw Azure Key Vault-account.

1. Selecteer in uw sleutel kluis account onder instellingen de optie **toegangs beleid**. Selecteer vervolgens **Nieuw toevoegen** om een nieuw beleid te maken.

1. In **Select Principal zoekt u** naar **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** en kiest u * * micro soft. AzureFrontDoor-CDN * *. Klik op **Selecteren**.

1. Selecteer in **Geheime machtigingen** de optie **Ophalen** om Front Door het certificaat te laten ophalen.

1. Selecteer in **Certificaatmachtigingen** de optie **Ophalen** om Front Door het certificaat te laten ophalen.

1. Selecteer **OK**. 

#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Het certificaat voor implementatie door Azure Front Door selecteren
 
1. Ga terug naar de standaard/Premium van Azure voor de voor deur in de portal.

1. Navigeer naar **geheimen** onder *instellingen* en selecteer **certificaat toevoegen**.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate.png" alt-text="Scherm opname van geheime landings pagina van de Azure front-deur.":::

1. Schakel op de pagina **certificaat toevoegen** het selectie vakje in voor het certificaat dat u wilt toevoegen aan de standaard/Premium van Azure voor de voor deur. Wijzig de versie selectie als ' meest recent ' en selecteer **toevoegen**. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate-page.png" alt-text="Scherm opname van de pagina certificaat toevoegen.":::

1. Zodra het certificaat is ingericht, kunt u het gebruiken wanneer u een nieuw aangepast domein toevoegt.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/successful-certificate-provisioned.png" alt-text="Scherm opname van het certificaat is toegevoegd aan geheimen.":::

1. Navigeer naar **domeinen** onder *instelling* en selecteer **+ toevoegen** om een nieuw aangepast domein toe te voegen. Kies op de pagina **een domein toevoegen** de optie uw eigen certificaat (BYOC) meenemen voor *https*. Selecteer bij *geheim* het certificaat dat u wilt gebruiken in de vervolg keuzelijst. 

    > [!NOTE]
    > Het geselecteerde certificaat moet een algemene naam (CN) hebben, hetzelfde als het aangepaste domein dat wordt toegevoegd.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-custom-domain-https.png" alt-text="Scherm afbeelding van de pagina een aangepast domein toevoegen met HTTPS.":::

1. Volg de stappen op het scherm om het certificaat te valideren. Koppel vervolgens het zojuist gemaakte aangepaste domein aan een eind punt zoals beschreven in [een aangepaste domein handleiding maken](how-to-add-custom-domain.md) .

#### <a name="change-from-azure-managed-to-bring-your-own-certificate-byoc"></a>Wijzigen van Azure die wordt beheerd om uw eigen certificaat te maken (BYOC)

1. U kunt een bestaand door Azure beheerd certificaat wijzigen in een door een gebruiker beheerd certificaat door de status van het certificaat te selecteren om de pagina **certificaat Details** te openen.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/domain-certificate.png" alt-text="Scherm afbeelding van de status van het certificaat op de domeinen landings pagina." lightbox="../media/how-to-configure-https-custom-domain/domain-certificate-expanded.png":::

1. Op de pagina **certificaat Details** kunt u overschakelen van ' Azure Managed ' in de optie ' uw eigen certificaat (BYOC) meenemen '. Volg dezelfde stappen als eerder om een certificaat te kiezen. Selecteer **bijwerken** als u het gekoppelde certificaat wilt wijzigen in een domein.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/certificate-details-page.png" alt-text="Scherm afbeelding van de pagina met certificaat details.":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [caching met de voor deur Standard/Premium van Azure](concept-caching.md).
