# 模型兼容性

Composer模型预计会随着时间而改变和演变。但是，在进行模型更改时，必须谨慎和纪律，以确保现有实例对于新模型仍然有效。

如果用模型M创建的实例相对于模型M'有效，则模型M' 与模型M **兼容**。如果这些实例是有效的，那么可以使用`Serializer`反序列化它们。

本文档中使用以下术语：

- *类*：资产、参与者、交易、概念或事件的结构的声明

- *实例*：类的一个实例，例如，如果org.example.Vehicle是一种资产（类），然后org.example.Vehicle＃ABC123是的org.acme.Vehicle的一个**实例**

- *属性*：由类定义的成员（或字段），包括关系。例如，类org.example.Vehicle可能有一个`string`类型的叫做`model` 的属性。

一个类（资产SampleAsset）：
```
namespace org.acme.sample

asset SampleAsset identified by assetId {
  o String assetId
  --> SampleParticipant owner
  o String value
}
```

类的一个实例：
```
{
  "$class": "org.acme.sample.SampleAsset",
  "assetId": "assetId:6463",
  "owner": "resource:org.acme.sample.SampleParticipant#participantId:8091",
  "value": "secret plant frequently ruler"
}
```

## 命名空间的演变

一个新的类可以被添加到一个命名空间，而不会破坏与已存在实例的兼容性。

## 类的演变

本部分描述了对已存在实例的类声明及其属性的更改的影响。

### 重命名

对类重命名将破坏它的任何已存在实例，或对它关联的兼容性。

### 抽象类

如果一个非抽象类被改为抽象类，那么试图创建该类的新实例将会在运行时抛出一个错误。因此这种改变不适用于广泛分布的类。

将抽象类更改为非抽象类不会破坏与已存在实例的兼容性。

### 超类

如果一个类是它自己的超类，那么在加载时抛出一个错误。对于广泛分布的类，不建议对加载实例时导致这种循环的类层次结构进行更改。

改变类的直接超类不会破坏与已存在实例的兼容性，前提是类的所有超类没有任何属性。

如果对直接超类的更改导致任何类不再是超类，那么如果已存在实例与修改的类有关系，则可能会导致错误。对于广泛分布的类，不建议这样的更改。

### 类属性

如果属性被声明为`optional`或被分配了`default`值，那么与向已存在实例添加属性不会导致不兼容。添加既不可选也不具有默认值的新属性将会破坏与该类的任何已存在实例的兼容性。

更改属性的基数（将数组`[]`更改为非数组或反之）将会破坏与该类的任何已存在实例的兼容性。

从类中删除属性将会破坏与引用此字段的任何已存在实例的兼容性。

如果属性由已存在实例使用，则更改属性的类型可能会导致错误。

如果属性由已存在实例使用，那么更改属性的验证表达式可能会导致错误。

作为关联的属性遵循与其他类型相同的规则。

### Enums的演变

在枚举类型中添加或重新排序常量不会破坏与已存在实例的兼容性。

如果已存在实例试图访问不再存在的枚举常量，则会发生错误。因此，对于广泛分布的枚举，不推荐这样的改变。

在所有其他方面，枚举的模型演化规则与类的规则相同。
