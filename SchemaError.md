# Introduction
When two consecutive version of the code, with different database schema tries to read and edit the same database record. Operations without protection can generate unexpected outcomes. This document will provide a list of common schema changes and the unexpected outcomes if no protection were added for these changes.

# Definition
**Backward Competible** (Stages)
1. Current Version of code can properly read data in previous version
2. Current Version of code can properly read data in previous version, edit the data, save data in current version. Then previous version of the code should be able to process the data as if it was modified by the previous version of code. (Require Forward and Backward Competibility Stage 1)

**Forward Competible** (Stages)
1. Previous Version of the code can properly read data in current version
2. Previous Version of the code can properly read data in current version, edit the data, save data in previous version. Then current version of code should be able to process the data as if it was modified by the current version of code. (Require Forward and Backward Competibility Stage 1)

# Cases
`This is list only provide error when no protection is added. Different protection method may cause other errors, a separated list should be generated based the method used.`
- Add Required Parameter
  - [X] Not Backward Competible\
    Stage 1 broken: Can not read previous version of data. Old data does not contain value in the new required parameter.
  - [X] Not Forward Competible\
    Stage 2 broken: Backward Competible Stage 1 Required
- Add Optional Parameter
  - [ ] Not Backward Competible
  - [X] Not Forward Competible\
    Stage 2 broken: Reading and saving data without modification will remove optional parameter values, unintentionally removing saved data.
- Change Optional Parameter to Required
  - [X] Not Backward Competible\
    Stage 1 broken: Can not read previous version of data. Old data may not contain value in the required parameter field, as it was not required.
  - [X] Not Forward Competible\
    Stage 2 broken: Backward Competible Stage 1 Required
- Change Required Parameter to Optional
  - [X] Not Backward Competible\
    Stage 2 broken: Forward Competible Stage 1 Required
  - [X] Not Forward Competible\
    Stage 1 broken: Can not read current data. Current data may not contain value in required field, as it's not required.
- Remove Required Parameter
  - [X] Not Backward Competible\
    Stage 2 broken: Forward Competible Stage 1 Required
  - [X] Not Forward Competible\
    Stage 1 broken: Can not read new data as they do not contain required parameter
- Remove Optional Parameter
  - [X] Not Backward Competible\
    Stage 2 broken: Reading old data, then saving that data without modification will remove optional parameter values, unintentionally removing saved data
  - [ ] Not Forward Competible

# Case Example
## Add Required Parameter
```ts
interface CurrentVersion {
    dataOne: string;
    dataTwo: string; // New Data
}

interface PreviousVersion {
    dataOne: string;
}
```

## Add Optional Parameter
```ts
interface CurrentVersion {
    dataOne: string;
    dataTwo?: string; // New Data
}

interface PreviousVersion {
    dataOne: string;
}
```

## Change Optional Parameter to Required
```ts
interface CurrentVersion {
    dataOne: string;
    dataTwo: string; // Data updated
}

interface PreviousVersion {
    dataOne: string;
    dataTwo?: string;
}
```

## Change Required Parameter to Optional
```ts
interface CurrentVersion {
    dataOne: string;
    dataTwo?: string; // Data updated
}

interface PreviousVersion {
    dataOne: string;
    dataTwo: string;
}
```

## Remove Required Parameter
```ts
interface CurrentVersion {
    dataOne: string;
    // DataTwo removed
}

interface PreviousVersion {
    dataOne: string;
    dataTwo: string;
}
```

## Remove Optional Parameter
```ts
interface CurrentVersion {
    dataOne: string;
}

interface PreviousVersion {
    dataOne: string;
    dataTwo?: string;
}
```