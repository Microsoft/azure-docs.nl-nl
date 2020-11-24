---
title: Een statische website integreren met Azure CDN-Azure Storage
description: Meer informatie over het opslaan van statische website-inhoud vanuit een Azure Storage-account met behulp van Azure Content Delivery Network (CDN).
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: e458f98d82c910ec845ebf951b7f6470b6aba10d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95527323"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Een statische website integreren met Azure CDN

U kunt [CDN (Azure Content Delivery Network)](../../cdn/cdn-overview.md) inschakelen voor het opslaan van inhoud van een [statische website](storage-blob-static-website.md) die wordt gehost in een Azure-opslag account. U kunt Azure CDN gebruiken om het aangepaste domein eindpunt te configureren voor uw statische website, aangepaste TLS/SSL-certificaten in te richten en aangepaste regels voor herschrijven te configureren. Het configureren van Azure CDN resulteert in extra kosten, maar biedt consistente lage latenties naar uw website vanaf elke locatie ter wereld. Azure CDN biedt ook TLS-versleuteling met uw eigen certificaat. 

Zie [Azure CDN-prijzen](https://azure.microsoft.com/pricing/details/cdn/) voor meer informatie over prijzen voor Azure CDN.

## <a name="enable-azure-cdn-for-your-static-website"></a>Azure CDN inschakelen voor uw statische website

U kunt Azure CDN rechtstreeks vanuit uw opslag account inschakelen voor uw statische website. Als u voor uw CDN-eindpunt geavanceerde configuratie-instellingen wilt opgeven, zoals [ optimalisatie voor het downloaden van grote bestanden](../../cdn/cdn-optimization-overview.md#large-file-download), kunt u in plaats daarvan de [Azure CDN-extensie](../../cdn/cdn-create-new-endpoint.md) gebruiken om een CDN-profiel en -eindpunt te maken.

1. Zoek uw opslag account in de Azure Portal en geef het account overzicht weer.

1. Selecteer in het menu **BLOB** -service **Azure CDN** om de pagina **Azure CDN** te openen:

    ![Een CDN-eindpunt maken](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. Geef in de sectie **CDN-profiel** op of er een nieuw CDN-profiel moet worden gemaakt of dat er al een wordt gebruikt. Een CDN-profiel is een verzameling CDN-eind punten die een prijs categorie en provider delen. Voer vervolgens een naam in voor de CDN die uniek is binnen uw abonnement.

1. Geef een prijs categorie op voor het CDN-eind punt. Zie [Content Delivery Network prijzen](https://azure.microsoft.com/pricing/details/cdn/)voor meer informatie over prijzen. Zie [Azure CDN product functies vergelijken](../../cdn/cdn-features.md)voor meer informatie over de beschik bare functies voor elke laag.

1. Geef in het veld **naam van CDN-eind punt** een naam op voor uw CDN-eind punt. Het CDN-eind punt moet uniek zijn binnen Azure en biedt het eerste deel van de eind punt-URL. In het formulier wordt gevalideerd of de naam van het eind punt uniek is.

1. Geef het eind punt van de statische website op in het veld **hostnaam van oorsprong** . 

   Om het eind punt van een statische website te vinden, gaat u naar de **statische website** -instellingen voor uw opslag account.  Kopieer het primaire eind punt en plak het in de CDN-configuratie.

   > [!IMPORTANT]
   > Zorg ervoor dat u de protocol-id (*bijvoorbeeld* https) en de afsluitende slash in de URL verwijdert. Als het eind punt van de statische website bijvoorbeeld is `https://mystorageaccount.z5.web.core.windows.net/` , geeft u `mystorageaccount.z5.web.core.windows.net` in het veld **hostnaam van oorsprong** op.

   In de volgende afbeelding ziet u een voor beeld van een eindpunt configuratie:

   ![Scherm opname van voor beeld van CDN-eindpunt configuratie](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Selecteer **maken** en wacht tot het CDN is ingericht. Zodra het eindpunt is gemaakt, wordt dit weergegeven in de lijst met eindpunten. (Als het formulier fouten bevat, wordt er een uitroep teken naast dat veld weer gegeven.)

1. Als u wilt controleren of het CDN-eind punt correct is geconfigureerd, klikt u op het eind punt om naar de instellingen te navigeren. Zoek in het CDN-overzicht voor uw opslag account de hostnaam van het eind punt en ga naar het eind punt, zoals wordt weer gegeven in de volgende afbeelding. De indeling van uw CDN-eind punt is vergelijkbaar met `https://staticwebsitesamples.azureedge.net` .

    ![Scherm afbeelding met overzicht van het CDN-eind punt](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. Zodra het CDN-eind punt is ingericht, wordt in de navigatie naar het CDN-eind punt de inhoud weer gegeven van het index.html-bestand dat u eerder hebt geüpload naar uw statische website.

1. Als u de oorspronkelijke instellingen voor het CDN-eind punt wilt controleren, gaat u naar **oorsprong** in het gedeelte **instellingen** van het CDN-eind punt. U ziet dat het veld van het **type oorsprong** is ingesteld op *aangepaste oorsprong* en dat het veld **hostnaam van oorsprong** het eind punt van de statische website weergeeft.

    ![Scherm opname met de oorspronkelijke instellingen voor het CDN-eind punt](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Inhoud verwijderen uit Azure CDN

Als u niet langer een object in de cache in Azure CDN nodig hebt, kunt u een van de volgende stappen uitvoeren:

* Maak de container persoonlijk in plaats van openbaar. Zie [Anonieme leestoegang tot containers en blobs beheren](./anonymous-read-access-configure.md) voor meer informatie.
* Schakel het CDN-eindpunt uit of verwijder het met behulp van Azure Portal.
* Wijzig de gehoste service zodat deze niet meer reageert op aanvragen voor het object.

Een object dat al in de cache in Azure CDN is geplaatst, blijft in de cache totdat de Time-to-Live-periode voor het object is verstreken of totdat het eindpunt is [opgeschoond](../../cdn/cdn-purge-endpoint.md). Als de Time-to-Live-periode is verstreken, bepaalt Azure CDN of het CDN-eindpunt nog geldig is en het object nog steeds anoniem toegankelijk is. Als dat niet het geval is, wordt het object niet langer in de cache bewaard.

## <a name="next-steps"></a>Volgende stappen

Beschrijving Een aangepast domein toevoegen aan uw Azure CDN-eind punt. Zie [zelf studie: een aangepast domein toevoegen aan uw Azure CDN-eind punt](../../cdn/cdn-map-content-to-custom-domain.md).