## Índice
 1. [Authentication](./authentication.html)
 2. [Firestore](./firestore.html)
 3. [Storage](./storage.html)

### Instalação e configuração
```
ng add @angular/fire
```

## Considerações
Os exemplos desta documentação são todos feitos orientados ao uso do RxJS pois é uma biblioteca totalmente integrada com o ecossistema do Angular, para tal, as funções que retornam `Promise` foram convertidas para trabalhar com `Observable` com o uso do operador `from`. No entanto, isto não é regra para se trabalhar com a biblioteca do @angular/fire, basta então adaptar os exemplos mostrados para usar `Promise` normalmente.
