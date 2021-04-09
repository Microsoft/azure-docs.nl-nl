---
title: Betrouw bare verzameling van object-serialisatie
description: Meer informatie over de serialisatie van Azure Service Fabric reliable Collections-objecten, met inbegrip van de standaard strategie en het definiëren van aangepaste serialisatie.
ms.topic: conceptual
ms.date: 5/8/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 29bb9a2dfb028d223d63559b35735e78d7e6bcf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784356"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Betrouw bare serialisatie van verzamelings objecten in azure Service Fabric
Betrouw bare verzamelingen repliceren en blijven hun items om ervoor te zorgen dat ze duurzaam zijn in de machine storingen en stroom uitval.
Voor het repliceren en persistent maken van items moeten betrouw bare verzamelingen worden geserialiseerd.

Betrouw bare verzamelingen ' Haal de juiste serialisatiefunctie op voor een bepaald type vanuit betrouw bare status Manager.
Betrouw bare status beheerder bevat ingebouwde serialisatiefuncties en kan aangepaste serialisatiefuncties registreren voor een bepaald type.

## <a name="built-in-serializers"></a>Ingebouwde Serialisatiefunctie

Betrouw bare status Manager bevat een ingebouwde serialisatiefunctie voor een aantal algemene typen, zodat deze standaard efficiënt kunnen worden geserialiseerd. Voor andere typen is betrouw bare status beheer terugvallen op het gebruik van de [DataContractSerializer](/dotnet/api/system.runtime.serialization.datacontractserializer).
Ingebouwde serialisatiefuncties zijn efficiënter omdat ze weten dat hun typen niet kunnen worden gewijzigd en ze hoeven geen informatie over het type op te nemen zoals de type naam ervan.

Betrouw bare status Manager heeft ingebouwde serialisatiefunctie voor de volgende typen: 
- Guid
- booleaans
- DBCS
- sbyte
- byte []
- char
- tekenreeks
- decimal
- double
- float
- int
- uint
- long
- ULONG
- korte
- USHORT

## <a name="custom-serialization"></a>Aangepaste serialisatie

Aangepaste serialisatiefunctie worden vaak gebruikt om de prestaties te verbeteren of om de gegevens te versleutelen via de kabel en op schijf. Aangepaste serialisatiefunctieen zijn over het algemeen efficiënter dan generieke serialisatiefunctie, omdat ze geen informatie over het type hoeven te serialiseren. 

[IReliableStateManager. TryAddStateSerializer \<T> ](/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer) wordt gebruikt voor het registreren van een aangepaste serialisatiefunctie voor het opgegeven type T. Deze registratie moet plaatsvinden in de bouw van de StatefulServiceBase om ervoor te zorgen dat voordat het herstel wordt gestart, alle betrouw bare verzamelingen toegang hebben tot de relevante serialisatiefunctie om hun persistente gegevens te lezen.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> Aangepaste serializers krijgen voor rang op ingebouwde serialisatiefunctie. Wanneer bijvoorbeeld een aangepaste serialisatiefunctie voor int wordt geregistreerd, wordt deze gebruikt voor het serialiseren van gehele getallen in plaats van de ingebouwde serialisatiefunctie voor int.

### <a name="how-to-implement-a-custom-serializer"></a>Een aangepaste serialisatiefunctie implementeren

Een aangepaste serialisatiefunctie moet de [IStateSerializer \<T> ](/dotnet/api/microsoft.servicefabric.data.istateserializer-1) -interface implementeren.

> [!NOTE]
> IStateSerializer \<T> bevat een overbelasting voor schrijven en lezen die een extra T wordt genoemd. Deze API is voor differentiële serialisatie. Momenteel wordt de functie voor differentiële serialisatie niet weer gegeven. Deze twee Overloads worden daarom pas aangeroepen als differentiële serialisatie wordt weer gegeven en ingeschakeld.

Hieronder ziet u een voor beeld van een aangepast type met de naam OrderKey dat vier eigenschappen bevat

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

Hier volgt een voor beeld van de implementatie van IStateSerializer \<OrderKey> .
Houd er rekening mee dat de overbelasting van lees-en schrijf bewerkingen die in baseValue worden uitgevoerd, hun respectieve overbelasting aanroept voor voorwaartse compatibiliteit.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Upgradatie
Bij een [upgrade van een rolling toepassing](service-fabric-application-upgrade.md)wordt de upgrade toegepast op een subset knoop punten, één upgrade domein per keer. Tijdens dit proces zullen sommige upgrade domeinen zich op de nieuwere versie van uw toepassing bekomen. sommige upgrade domeinen bezullen de oudere versie van uw toepassing. Tijdens de implementatie moet de nieuwe versie van uw toepassing de oude versie van uw gegevens kunnen lezen, en de oude versie van uw toepassing moet de nieuwe versie van uw gegevens kunnen lezen. Als de gegevens indeling niet voorwaarts en achterwaarts compatibel is, kan de upgrade mislukken of kunnen de gegevens verloren gaan of beschadigd raken.

Als u ingebouwde serialisatiefunctie gebruikt, hoeft u zich geen zorgen te maken over de compatibiliteit.
Als u echter een aangepaste serialisatiefunctie of de DataContractSerializer gebruikt, moeten de gegevens oneindig achterwaarts zijn en compatibel worden doorgestuurd.
Met andere woorden, elke versie van serialisatiefunctie moet een wille keurige versie van het type kunnen serialiseren en deserialiseren.

Gebruikers van het gegevens contract moeten de goed gedefinieerde versie regels volgen om velden toe te voegen, te verwijderen en te wijzigen. Data contract biedt ook ondersteuning voor het afhandelen van onbekende velden, het koppelen van het serialisatie proces en het afhandelen van klasse-overname. Zie [using data contract](/dotnet/framework/wcf/feature-details/using-data-contracts)(Engelstalig) voor meer informatie.

Aangepaste serialisatiefunctie-gebruikers moeten voldoen aan de richt lijnen van de serialisatiefunctie die ze gebruiken om ervoor te zorgen dat deze achterwaarts en doorgestuurd compatibel zijn.
Gang bare methode voor het ondersteunen van alle versies is het toevoegen van grootte-informatie aan het begin en alleen het toevoegen van optionele eigenschappen.
Op deze manier kan elke versie zoveel mogelijk worden gelezen en over het resterende deel van de stroom heen gaan.

## <a name="next-steps"></a>Volgende stappen
  * [Serialisatie en upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Naslag informatie voor ontwikkel aars voor betrouw bare verzamelingen](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
  * Als u een [upgrade uitvoert van uw toepassing met behulp van Visual Studio](service-fabric-application-upgrade-tutorial.md) , wordt u begeleid bij een toepassings upgrade met Visual Studio.
  * Als u uw toepassing bijwerkt [met Power shell](service-fabric-application-upgrade-tutorial-powershell.md) , kunt u een toepassings upgrade uitvoeren met behulp van Power shell.
  * Bepalen hoe uw toepassing wordt bijgewerkt met behulp van [upgrade parameters](service-fabric-application-upgrade-parameters.md).
  * Meer informatie over het gebruik van geavanceerde functionaliteit bij het upgraden van uw toepassing door te verwijzen naar [Geavanceerde onderwerpen](service-fabric-application-upgrade-advanced.md).
  * Corrigeer veelvoorkomende problemen in toepassings upgrades door te verwijzen naar de stappen in [Troubleshooting Application upgrades](service-fabric-application-upgrade-troubleshooting.md).
