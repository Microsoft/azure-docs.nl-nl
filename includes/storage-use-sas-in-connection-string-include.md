---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176253"
---
Als u beschikt over een SAS-URL (Shared Access Signature) waarmee u toegang krijgt tot resources in een opslag account, kunt u de SAS gebruiken in een connection string. Omdat de SAS de informatie bevat die nodig is om de aanvraag te verifiëren, levert een connection string met een SAS het Protocol, het service-eind punt en de benodigde referenties voor toegang tot de bron.

Als u een connection string wilt maken dat een Shared Access Signature bevat, geeft u de teken reeks op in de volgende indeling:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Elk service-eind punt is optioneel, hoewel het connection string ten minste één moet bevatten.

> [!NOTE]
> Het gebruik van HTTPS met een SAS wordt aanbevolen als best practice.
>
> Als u een SAS opgeeft in een connection string in een configuratie bestand, moet u mogelijk speciale tekens in de URL coderen.
>
>

### <a name="service-sas-example"></a>Voor beeld van service-SAS
Hier volgt een voor beeld van een connection string die een service-SAS voor Blob Storage bevat:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

En hier volgt een voor beeld van hetzelfde connection string met code ring van speciale tekens:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Voor beeld van SAS-account
Hier volgt een voor beeld van een connection string die een account-SAS bevat voor Blob en file storage. Houd er rekening mee dat eind punten voor beide services worden opgegeven:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

En hier volgt een voor beeld van hetzelfde connection string met URL-code ring:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

