## Índice
 1. [Criar conta de usuário com email e senha](#createuserwithemailandpassword)

### createUserWithEmailAndPassword
Assinatura do método:
```
createUserWithEmailAndPassword(auth: Auth, email: string, password: string): Promise<UserCredential>
```
No contexto do Angular, geralmente trabalhamos com `Observable`, portanto usa-se o operador `from` para transformar a `Promise` em `Observable`.
```typescript
import { createUserWithEmailAndPassword } from '@firebase/auth';
import { Auth } from '@angular/fire/auth';

@Injectable({
  providedIn: 'root',
})
export class AuthService {
  constructor(private auth: Auth) {}
  
  signupWithEmailAndPassword(email: string, password: string): Observable<UserCredential> {
    return from(createUserWithEmailAndPassword(this.auth, email, password));
  }
}
```
A chamada para o método seria dessa forma dentro de um componente ou outro serviço:
```typescript
this.authService.signupWithEmailAndPassword("example@gmail.com", "123456789").subscribe();
```
Utiliza-se o `subscribe()` no final para realizar o processo de criação. E dentro do subscribe também é possível obter informações da operação de criação caso haja falha ou sucesso:
```typescript
this.authService.signupWithEmailAndPassword("example@gmail.com", "123456789")
.subscribe({
  next: (credentials) => console.log(`Usuário logado: ${creds.user.uid}`),
  error: (err) => console.log(`Um erro ocorreu no login: ${err}`),
});
```
A partir deste comportamento é possível repassar feedbacks para o usuário do ocorrido, e até mesmo mostrar com mais exatidão qual erro ocorreu no processo de cadastro.
