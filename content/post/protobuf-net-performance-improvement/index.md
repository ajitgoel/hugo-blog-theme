---
title: Protobuf.Net performance improvement in .Net application
date: 2021-09-30T01:35:43.949Z
draft: false
featured: false
tags:
  - .Net
  - C#
  - Performance-Improvement
categories:
  - .Net
  - C#
  - Performance-Improvement
image:
  filename: ""
  focal_point: Smart
  preview_only: false
---
[protobuf-net](https://github.com/protobuf-net/protobuf-net) is a contract based serializer for .NET code created by the famous [Marc Gravell](https://github.com/mgravell), that writes data in the "protocol buffers" serialization format engineered by Google. The API is broadly comparable, in usage, to `XmlSerializer`, `DataContractSerializer`, etc. \
To use protobuf.net in your .Net applications, you need to:

**1. Decorate your classes**\
Unlike XmlSerializer, the member-names are identified by an integer to identify each member. 

{{<hl>}}[ProtoContract]{{</hl>}}
class Person {
    {{<hl>}}[ProtoMember(1)]{{</hl>}}
    public int Id {get;set;}
    [ProtoMember(2)]
    public string Name {get;set;}
    [ProtoMember(3)]
    public Address Address {get;set;}
}
[ProtoContract]
class Address {
    [ProtoMember(1)]
    public string Line1 {get;set;}
    [ProtoMember(2)]
    public string Line2 {get;set;}
}

2 **Serialize your data:** This writes a 32 byte file to "person.bin"

```
var person = new Person {
    Id = 12345, Name = "Fred",
    Address = new Address {
        Line1 = "Flat 1",
        Line2 = "The Meadows"
    }
};
using (var file = File.Create("person.bin")) {
    Serializer.Serialize(file, person);
}
```

3 **Deserialize your data:** This reads the data back from "person.bin" :

```
Person newPerson;
using (var file = File.OpenRead("person.bin")) {
    newPerson = Serializer.Deserialize<Person>(file);
}
```

**4. Inheritance:** Inheritance must be explicitly declared via \[ProtoInclude(...)] on each type with known sub-types.

[ProtoContract]
{{<hl>}}[ProtoInclude(7, typeof(SomeDerivedType))]{{</hl>}}
class SomeBaseType {...}

[ProtoContract]
class SomeDerivedType {...}

We used protobuf.net in one of our very heavily used applications. This application used to write static and expensive to retrieve data into xml files(using xml serialization), that was then used by our applications(using xml deserialization).  Needless to say, xml serialization and de-serialization was one of the biggest performance bottleneck identified in our application. \
We changed the application to use protobuf.net instead of xml serialization. This helped improve the performance of the application by leaps and bounds, as you can see in the chart below(i.e before April release and after April release). 

![Protobuf.Net-performance improvement](protobuf.net-performanceimprovement2.png "Protobuf.Net-performance improvement")

![Protobuf.Net-Performance improvement](protobuf.net-performanceimprovement.png "Protobuf.Net-Performance improvement")
