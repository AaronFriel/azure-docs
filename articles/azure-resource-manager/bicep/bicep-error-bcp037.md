---
title: BCP037
description: Warning - The property <property-name> is not allowed on objects of type <type-definition>.
ms.topic: reference
ms.custom: devx-track-bicep
ms.date: 07/15/2024
---

# Bicep warning code - BCP037

This warning occurs when you specify a property that isn't defined in a resource type.

## Warning description

`The property <property-name> is not allowed on objects of type <type-defintion>.`

## Solution

Remove the undefined property.

## Examples

The following example raises the warning because `bar` isn't defined in `storageAccountType`:

```bicep
type storageAccountConfigType = {
  name: string
  sku: string
}

param foo storageAccountConfigType = {
  name: 'myStorage'
  sku: 'Standard_LRS' 
  bar: 'myBar'
}
```

You can fix the issue by removing the property:

```bicep
type storageAccountConfigType = {
  name: string
  sku: string
}

param foo storageAccountConfigType = {
  name: 'myStorage'
  sku: 'Standard_LRS' 
}
```

The following example raises the error because `obj` is a sealed type and doesn't define a `baz` property.

```bicep
@sealed()
type obj = {
  foo: string
  bar: string
}

param p obj = {
  foo: 'foo'
  bar: 'bar'
  baz: 'baz' 
}
```

You can fix the issue by removing the property:

```bicep
@sealed()
type obj = {
  foo: string
  bar: string
}

param p obj = {
  foo: 'foo'
  bar: 'bar'
}
```

## Next steps

For more information about Bicep error and warning codes, see [Bicep warnings and errors](./bicep-error-codes.md).
