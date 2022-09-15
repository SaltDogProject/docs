# SaltDog Types

## 文献库

### IDirList
```typescript
interface IDirList {
    meta: {
        parentDirID: number;
        libraryID: number;
        isRoot: boolean;
    };
    dirs: Array<IDirListDir>;
    items: Array<IDirListItems>;
}
```
### IDirListDir
```typescript
interface IDirListDir {
    dirID: number;
    name: string;
    dateAdded: string;
    dateModified: string;
    localKey: string;
}
```
### IDirListItems
```typescript
interface IDirListItems {
    itemID: number;
    name: string;
    itemType: string;
    itemTypeID: number;
    dateAdded: string;
    dateModified: string;
    localKey: string;
}
```