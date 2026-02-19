# 🚀 Loja Digital - Deploy no Vercel

Estrutura completa de e-commerce com integração Stripe para pagamentos.

## 📁 Arquivos

```
vercel-store/
├── index.html          # Página principal (loja)
├── sucesso.html        # Página de confirmação de pagamento
├── api/
│   └── checkout.js     # API de checkout Stripe (serverless)
├── package.json        # Dependências
└── vercel.json         # Configuração do Vercel
```

## ⚡ Deploy Rápido

### 1. Preparar arquivos
- Faça download de todos os arquivos desta pasta
- Compacte em um arquivo `.zip`

### 2. Criar conta Stripe (obrigatório para receber pagamentos)
1. Acesse [stripe.com](https://stripe.com/br)
2. Crie uma conta (pode usar modo teste inicialmente)
3. Vá em Developers > API Keys
4. Copie:
   - **Publishable key** (pk_test_...)
   - **Secret key** (sk_test_...)

### 3. Deploy no Vercel

#### Opção A: Via Dashboard (Mais fácil)
1. Acesse [vercel.com](https://vercel.com)
2. Crie conta (pode usar GitHub)
3. Clique em "Add New..." > "Project"
4. Selecione "Import Git Repository" ou faça upload do ZIP
5. No momento do deploy, adicione as Environment Variables:
   - `STRIPE_SECRET_KEY` = sua chave secreta (sk_test_...)
   - `STRIPE_PUBLISHABLE_KEY` = sua chave pública (pk_test_...)
6. Clique em "Deploy"

#### Opção B: Via CLI
```bash
# Instalar CLI
npm i -g vercel

# Login
vercel login

# Deploy
vercel

# Configurar secrets
vercel env add STRIPE_SECRET_KEY
vercel env add STRIPE_PUBLISHABLE_KEY
```

### 4. Configurar Stripe (importante!)

#### Webhook (para entrega automática)
1. No Dashboard Stripe, vá em Developers > Webhooks
2. Clique "Add endpoint"
3. URL: `https://seu-site.vercel.app/api/webhook`
4. Selecione eventos:
   - `checkout.session.completed`
   - `payment_intent.succeeded`
5. Copie o "Signing secret" e adicione como `STRIPE_WEBHOOK_SECRET` no Vercel

#### Produtos
Você pode criar produtos diretamente no código (como está feito) ou no Dashboard Stripe:
1. Products > Add product
2. Defina nome, descrição, preço
3. Use o Price ID no código

## 💳 Como funciona o pagamento

1. Cliente clica "Comprar agora" → Produto vai para o carrinho
2. Cliente clica "Finalizar Compra" → Chama API `/api/checkout`
3. API cria sessão Stripe → Redireciona para página de pagamento Stripe
4. Cliente paga com cartão, Pix ou boleto
5. Stripe redireciona para `sucesso.html`
6. Webhook dispara e envia e-mail com o produto (você precisa configurar)

## 🔧 Personalizações

### Alterar produtos
Edite o array `products` no `index.html`:
```javascript
const products = [
    {
        id: 1,
        name: 'Nome do Produto',
        description: 'Descrição',
        price: 47.90,  // Preço em reais
        image: 'URL da imagem',
        emoji: '💳',
        category: 'novidades' // ou 'trampos'
    }
];
```

### Alterar cores
No `<script>` do Tailwind:
```javascript
colors: {
    primary: '#7c3aed',  // Altere para sua cor
    dark: '#0a0a0a',     // Cor de fundo
}
```

### Adicionar entrega automática por e-mail
Crie o arquivo `api/webhook.js`:
```javascript
// Envia e-mail quando pagamento é confirmado
const stripe = require('stripe')(process.env.STRIPE_SECRET_KEY);
// Use SendGrid, AWS SES ou Nodemailer para enviar e-mails
```

## 🛡️ Segurança

- ✅ Stripe cuida de toda a parte de PCI compliance
- ✅ Dados de cartão nunca tocam seu servidor
- ✅ HTTPS obrigatório (Vercel fornece)
- ✅ Webhook verifica assinatura Stripe

## 📱 Funcionalidades incluídas

- [x] Carrinho de compras persistente (localStorage)
- [x] Checkout seguro via Stripe
- [x] Design responsivo (mobile-first)
- [x] Animações e micro-interações
- [x] Página de sucesso com confete
- [x] Sistema de avaliações
- [x] FAQ interativo
- [x] Menu mobile

## 🆘 Suporte

Problemas comuns:

**"Erro ao processar pagamento"**
→ Verifique se as chaves Stripe estão configuradas no Vercel

**"CORS error"**
→ A API já está configurada com headers CORS

**"Webhook não funciona"**
→ Verifique se adicionou o endpoint correto no Stripe

---

💰 **Pronto para vender!** Após o deploy, seu site estará online com pagamentos funcionando.
