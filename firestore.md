## Índice

1.  [Referenciando uma coleção](#collection)
2.  [Fluxo de dados da coleção](#collectiondata)
3.  [Transformar dados do firestore para a aplicação e vice-versa](#converter)
4.  [Realizando queries](#queries)

### collection

Assinatura do método:

```
collection(firestore: Firestore, path: string, ...pathSegments: string[]): CollectionReference<DocumentData>
```

Esse método cria uma referência para uma coleção, ela pode já existir ou não. O parâmetro `firestore` diz respeito a instância do banco. O parâmetro `path` diz o nome da coleção. E o parâmetro `...pathSegments` pode ser usado para indicar subcoleções dentro da coleção de `path`.

Exemplo:

```typescript
import { collectionData, Firestore } from "@angular/fire/firestore";
import { collection } from "@firebase/firestore";
@Injectable({
  providedIn: "root",
})
export class ProdutoService {
  constructor(private db: Firestore) {}

  produtos = collection(this.db, "produtos");
}
```

### collectionData

Assinatura do método:

```
collectionData(query: Query<DocumentData>, options?: { idField?: string | undefined; } | undefined): Observable<DocumentData[]>
```

Esse método retorna um fluxo de dados vindos do banco. Conforme ocorrem alterações na coleção o `Observable`emite um novo array de `DocumentData` atualizado.
O parâmetro `query` indica que pode ser passado a referência da coleção ou um objeto `Query`, o parâmetro `options` pode ser usado para indicar ao Firestore para anexar o id do documento dentro de cada objeto `DocumentData`. Esse objeto `DocumentData` é um simples `Map`, as informações podem ser acessadas como `data['propriedade']` ou pode ser realizado o cast para a interface que estiver usando para representar os dados do documento.

Exemplo:

```typescript
import { collectionData, Firestore } from "@angular/fire/firestore";
import { collection } from "@firebase/firestore";
@Injectable({
  providedIn: "root",
})
export class ProdutoService {
  constructor(private db: Firestore) {}

  produtos = collection(this.db, "produtos");

  // Devido a tipagem estrutural do Typescript,
  // e o fato da interface Produto ter o mesmo formato de
  // DocumentData, não há problemas em realizar o cast de tipos.
  getProdutos(): Observable<Produto[]> {
    return collectionData(this.produtos, { idField: "id" }) as Observable<
      Produto[]
    >;
  }
}
```

### converter

As vezes é necessário modificar campos antes e depois do Firestore. Resumidamente, realizar adaptações ou processamentos nos dados para que melhor se adequem a situação em específico. Podemos usar converter para transformar o tipo de data `Timestamp` para um `Date` do Typescript.

Exemplo:

```typescript
export interface Converter<T> {
  toFirestore(data: T): any;
  fromFirestore(snapshot: DocumentSnapshot, options: any): T;
}

export const ProdutoConverter: Converter<Produto> = {
  toFirestore: (produto) => produto,
  fromFirestore: (snapshot, options) => {
    const dataDoc = snapshot.data(options);

    return { ...dataDoc, dataPromocao: dataDoc["dataPromocao"]?.toDate() };
  },
};
```

Em seguida basta aplicar o converter em alguma coleção conforme a necessidade.

```typescript
produtos = collection(this.db).withConverter(ProdutoConverter);
```

Esse método é vantajoso pois evitará realizar o cast de tipos mostrado nos exemplos anteriores de `collectionData` !

### Realizando queries

Além de ler todos os dados em uma coleção é possível aplicar filtros, para que apenas uma parcela dos documentos sejam lidos, para isso utiliza-se `query`.
Assinatura do método:

```
query<Diario>(query: Query<Diario>, ...queryConstraints: QueryConstraint[]): Query<Diario>
```

O parâmetro `query` pode ser uma query de base ou uma referência para uma coleção. E temos as `...queryConstraints` que podem ser:

- [where](#where);
- orderBy;
- limit;
- limitToLast, startAt, startAfter, endAt e endBefore.

As três primeiras constraints são as de uso mais prático dentro da aplicação.

### where

Assinatura do método:

```
where(fieldPath: string | FieldPath, opStr: WhereFilterOp, value: unknown): QueryConstraint
```

O parâmetro `fieldPath` indica qual o campo do documento será comparado. O parâmetro `opStr` indica qual será o método de comparação ("<", "<=", "==", "<", "<=", "!="). E `value` indica o valor que será comparado.

Exemplo:

```typescript
getProdutosPromocao(): Observable<Produto[]> {
    return collectionData(
        query(this.produtos, where('emPromocao', '==', true)),
        { idField: 'id }
    ) as Observable<Produto[]>;
}
```
