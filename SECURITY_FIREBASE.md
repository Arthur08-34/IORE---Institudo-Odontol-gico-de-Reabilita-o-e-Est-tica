# Segurança do Firebase e do site

## O que foi protegido neste projeto

- As avaliações enviadas pelo site agora entram como `pending`.
- O site só exibe avaliações com `status: "approved"`.
- Os comentários vindos do Firestore são renderizados com `textContent`, evitando injeção de HTML/JavaScript.
- A consulta do Firestore foi limitada a 12 avaliações aprovadas.
- `firestore.rules` bloqueia qualquer coleção que não seja `avaliacoes`.
- `firebase.json` adiciona headers de segurança quando publicado no Firebase Hosting.
- `404.html` será exibido automaticamente pelo Firebase Hosting quando uma URL não existir.

## Passos obrigatórios no Firebase

1. Publique as regras e o índice:

   ```bash
   firebase deploy --only firestore,hosting
   ```

2. Aprove depoimentos manualmente no Firestore:

   - Abra a coleção `avaliacoes`.
   - Confira o conteúdo.
   - Troque `status` de `pending` para `approved` somente quando o texto puder aparecer no site.

3. Restrinja a API key no Google Cloud Console:

   - Vá em **APIs e serviços > Credenciais**.
   - Abra a chave usada pelo app web.
   - Em **Application restrictions**, permita apenas os domínios reais do site.
   - Em **API restrictions**, deixe só as APIs Firebase/Google realmente usadas.

4. Ative o Firebase App Check para Web:

   - Use reCAPTCHA Enterprise ou reCAPTCHA v3.
   - Ative enforcement para Cloud Firestore depois de testar em modo monitoramento.
   - Depois disso, adicione a inicialização do App Check no `Index.html` com a chave pública do reCAPTCHA.

## Observação importante

Não existe como impedir que alguém veja o HTML, CSS e JavaScript enviados ao navegador. A proteção real é nunca colocar segredos no frontend e bloquear o acesso ao banco com Security Rules, App Check e restrições de domínio/API key.
