## type 支持

支持类型：对象、函数两者都适用，但是 type 可以用于基础类型、联合类型、元祖

计算属性：type 支持，interface 不支持

```typescript
type Keys = "firstname" | "surname"

type DudeType = {
    [key in Keys]: string
}

const test: DudeType = {
    firstname: "Pawel",
    surname: "Grzybek"
}
```

## interface 支持

同名合并：interface 支持，type 不支持