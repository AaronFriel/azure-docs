---
title: BCP040
description: Error/warning - String interpolation is not supported for keys on objects of type <type-definition>.
ms.topic: reference
ms.custom: devx-track-bicep
ms.date: 07/15/2024
---

# Bicep error/warning code - BCP040

This error/warning occurs when the Bicep compiler cannot determine the exact value of an interpolated string key.

## Error/warning description

`String interpolation is not supported for keys on objects of type <type-definition>.`

## Solution

Remove string interpolation.

## Examples

The following example raises the warning because string interpolation is used for specifying the key `sku1`:

```bicep
var name = 'sku'

type storageAccountConfigType = {
  name: string
  sku1: string
}

param foo storageAccountConfigType = {
  name: 'myStorage'
  '${name}1': 'Standard_LRS' 
}
```

You can fix the issue by adding the missing properties:

```bicep
var name = 'sku'

type storageAccountConfigType = {
  name: string
  sku1: string
}

param foo storageAccountConfigType = {
  name: 'myStorage'
  sku1: 'Standard_LRS' 
}
```

## Next steps

For more information about Bicep error and warning codes, see [Bicep warnings and errors](./bicep-error-codes.md).
