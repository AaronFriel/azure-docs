---
title: BCP327
description: Error/warning - The provided value (which will always be greater than or equal to <value>) is too large to assign to a target for which the maximum allowable value is <max-value>.
ms.topic: reference
ms.custom: devx-track-bicep
ms.date: 07/15/2024
---

# Bicep error/warning code - BCP327

This error/warning occurs when you assign a value that is greater than the allowable value.

## Error/warning description

`The provided value (which will always be greater than or equal to <value>) is too large to assign to a target for which the maximum allowable value is <max-value>.`

## Solution

Assign a value that falls within the permitted range.

## Examples

The following example raises the error because `13` is greater than maximum allowable value:

```bicep
@minValue(1)
@maxValue(12)
param month int = 13

```

You can fix the error by assigning a value within the permitted range:

```bicep
@minValue(1)
@maxValue(12)
param month int = 12

```

## Next steps

For more information about Bicep error and warning codes, see [Bicep warnings and errors](./bicep-error-codes.md).