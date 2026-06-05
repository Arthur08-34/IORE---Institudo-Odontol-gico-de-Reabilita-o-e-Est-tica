# Seguranca do Firebase e do site

## Protecoes aplicadas no projeto

- Avaliacoes enviadas pelo site entram como `status: "pending"`.
- O site consulta e exibe somente avaliacoes com `status: "approved"`.
- As regras do Firestore bloqueiam leitura de avaliacoes pendentes.
- As regras do Firestore bloqueiam `update` e `delete` publicos.
- As regras validam campos, tipos, tamanhos e notas antes de aceitar uma avaliacao.
- Comentarios vindos do Firestore sao renderizados com `textContent`, evitando injecao de HTML.
- Links externos com `target="_blank"` usam `rel="noopener noreferrer"`.
- `firebase.json` envia headers de seguranca pelo Firebase Hosting.
- `/.well-known/security.txt` foi adicionado para contato responsavel.
- `404.html` sera exibido automaticamente quando uma URL nao existir.

## Fluxo correto de moderacao

1. O paciente envia a avaliacao pelo site.
2. O documento entra na colecao `avaliacoes` como `pending`.
3. Um responsavel entra no Firebase Console e confere o texto.
4. Se estiver tudo certo, altera `status` de `pending` para `approved`.
5. Somente depois disso a avaliacao aparece no site.

## Deploy recomendado

Use este comando para publicar site, regras e indices:

```powershell
cd "C:\Users\arthu\OneDrive\Área de Trabalho\Códigos\IORE"
& "$env:APPDATA\npm\firebase.cmd" deploy --only hosting,firestore
```

## Passos obrigatorios fora do codigo

### 1. Restringir a API key

No Google Cloud Console:

- Abra **APIs e servicos > Credenciais**.
- Selecione a chave usada pelo app web.
- Em **Application restrictions**, use **HTTP referrers**.
- Permita somente os dominios reais, por exemplo:
  - `https://iore.com.br/*`
  - `https://www.iore.com.br/*`
  - `https://iore-d7e47.web.app/*`
  - `https://iore-d7e47.firebaseapp.com/*`
- Em **API restrictions**, permita apenas as APIs usadas pelo Firebase/Firestore.

### 2. Ativar Firebase App Check

No Firebase Console:

- Abra **App Check**.
- Registre o app Web.
- Use reCAPTCHA Enterprise ou reCAPTCHA v3.
- Comece em modo de monitoramento.
- Depois que tudo estiver funcionando, ative enforcement para Cloud Firestore.

### 3. Aprovar avaliacoes manualmente

No Firestore:

- Abra a colecao `avaliacoes`.
- Leia o conteudo enviado.
- Altere `status` para `approved` apenas quando puder aparecer publicamente.

## Observacao importante

Nao existe como esconder o HTML, CSS e JavaScript enviados ao navegador. A seguranca real vem de nao colocar segredos no frontend, validar tudo no Firestore Rules, ativar App Check, restringir a API key por dominio e monitorar o uso do projeto.
