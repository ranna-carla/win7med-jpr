# 🚀 Como publicar o sistema Win7 Med JPR

Tempo total: **~20 minutos.** Custo: **R$ 0.**

Você vai precisar de 3 contas (todas grátis):
- **Google** (pra Firebase) — provavelmente já tem
- **GitHub** — pra hospedar o código → https://github.com/signup
- **Netlify** — pra publicar online → https://app.netlify.com/signup (faça login com GitHub mesmo)

---

## 🔥 Passo 1 — Criar projeto no Firebase (5 min)

O Firebase é o "banco de dados" onde os contatos vão ficar salvos. Sem ele o sistema funciona offline mas os iPads não sincronizam entre si.

### 1.1 Criar o projeto

1. Acesse **https://console.firebase.google.com/**
2. Clique em **"Adicionar projeto"** (botão azul)
3. Nome: digite `win7med-jpr` → clique **Continuar**
4. **Desative o Google Analytics** (não é necessário, deixa mais simples) → clique **Criar projeto**
5. Aguarde ~30 segundos → clique **Continuar**

### 1.2 Criar o banco de dados (Firestore)

1. No menu lateral esquerdo, procure **"Build"** → clique em **"Firestore Database"**
2. Clique no botão **"Criar banco de dados"**
3. Escolha **"Iniciar no modo de teste"** (deixa válido por 30 dias, pode estender depois)
4. Em "Local do Cloud Firestore", escolha **`southamerica-east1` (São Paulo)** — é o mais próximo
5. Clique **Ativar**

### 1.3 Pegar a configuração do projeto

1. Clique no ícone de **engrenagem ⚙️** no canto superior esquerdo (ao lado de "Visão geral do projeto")
2. Clique em **"Configurações do projeto"**
3. Role a página até o final → seção **"Seus apps"**
4. Clique no ícone **`</>`** (que significa Web)
5. Em "Apelido do app" digite: `jpr-2026` → clique **Registrar app**
6. **NÃO** marque "Configurar também o Firebase Hosting"
7. Vai aparecer um código tipo isso — **COPIE TUDO ENTRE AS CHAVES `{ }` da variável `firebaseConfig`**:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSyABCDEFG...",
  authDomain: "win7med-jpr.firebaseapp.com",
  projectId: "win7med-jpr",
  storageBucket: "win7med-jpr.firebasestorage.app",
  messagingSenderId: "123456789012",
  appId: "1:123456789012:web:abcdef1234567890"
};
```

**Guarde isso num bloco de notas** — vamos usar daqui a pouco. Pode fechar o Firebase, voltamos depois.

### 1.4 Liberar permissões do banco

Antes de fechar o Firebase, ainda precisa fazer mais uma coisa importante:

1. Volte pro menu **Firestore Database**
2. Clique na aba **"Regras"** (no topo)
3. Apague tudo que estiver lá e cole exatamente isto:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /jpr_leads/{leadId} {
      allow read, create, update, delete: if true;
    }
  }
}
```

4. Clique em **"Publicar"**

> ⚠️ Essas regras são "abertas" — qualquer pessoa com a URL do sistema consegue cadastrar leads e ler. O controle de acesso ao dashboard é feito pelo PIN. Pra produção real seria bom fechar mais, mas pra ação do JPR está perfeito.

---

## ✏️ Passo 2 — Editar o HTML com sua configuração (2 min)

1. Abra o arquivo **`index.html`** num editor de texto qualquer (Bloco de Notas serve, mas recomendo VS Code, Sublime ou até o TextEdit do Mac)
2. Aperte `Ctrl+F` (ou `Cmd+F` no Mac) e procure por: `window.WIN7_CONFIG`
3. Você vai encontrar este bloco perto do final do arquivo:

```javascript
window.WIN7_CONFIG = {
  firebase: {
    apiKey: "",
    authDomain: "",
    projectId: "",
    storageBucket: "",
    messagingSenderId: "",
    appId: ""
  },
  dashboardPin: "1162",
  eventName: "JPR 2026"
};
```

4. **Cole os valores** que você copiou do Firebase (item 1.3) entre as aspas
5. **Mude o `dashboardPin`** pra um número de 4 dígitos que SÓ Luís, Ranna e os SDRs autorizados saberão. O padrão é `1162` — pode trocar pra outro qualquer.
6. **Salve o arquivo**

---

## 📤 Passo 3 — Subir para o GitHub (5 min)

### 3.1 Criar o repositório

1. Acesse **https://github.com/new**
2. **Repository name:** `win7med-jpr`
3. Marque **"Public"** (precisa ser público pra Netlify gratuito funcionar)
4. **NÃO** marque nenhuma das outras opções (README, .gitignore, license)
5. Clique **"Create repository"**

### 3.2 Subir os arquivos (jeito mais fácil — pelo navegador)

Você nem precisa instalar Git. Faz assim:

1. Na tela do repositório novo (logo após criar), clique em **"uploading an existing file"** (link azul no meio da tela)
2. Arraste para a área tracejada estes 3 arquivos:
   - `index.html` (já com sua config do Firebase!)
   - `netlify.toml`
   - `.gitignore`
3. Embaixo da página, em "Commit changes", deixe tudo padrão e clique **"Commit changes"**
4. Pronto — seu código está no GitHub.

---

## 🌐 Passo 4 — Publicar no Netlify (3 min)

### 4.1 Conectar GitHub ao Netlify

1. Acesse **https://app.netlify.com/**
2. Login com GitHub (clique no botão "Log in with GitHub")
3. Autorize o Netlify a acessar seus repositórios

### 4.2 Importar o projeto

1. Na tela inicial do Netlify, clique **"Add new site"** → **"Import an existing project"**
2. Clique **"Deploy with GitHub"**
3. Procure pelo repositório **`win7med-jpr`** e clique nele
4. Em "Build settings":
   - **Branch to deploy:** `main` (ou `master`, depende do nome)
   - **Build command:** deixe **VAZIO**
   - **Publish directory:** digite `.` (apenas um ponto)
5. Clique **"Deploy site"** ou **"Deploy win7med-jpr"**

### 4.3 Aguardar deploy

Em ~30 segundos seu site estará no ar com uma URL automática tipo:
```
https://magnificent-pony-12345.netlify.app
```

### 4.4 Personalizar a URL (opcional, recomendado)

1. No painel do site no Netlify, clique em **"Site settings"** → **"Change site name"**
2. Digite algo memorável tipo: `win7med-jpr` ou `jpr-win7`
3. Clique **Save**
4. Sua URL final fica: `https://win7med-jpr.netlify.app`

### 4.5 Domínio próprio (opcional, mais profissional)

Se vocês quiserem usar `jpr.win7.com.br`:

1. Em **"Domain management"** clique em **"Add custom domain"**
2. Digite o domínio desejado (ex: `jpr.win7.com.br`)
3. O Netlify vai pedir pra criar um registro DNS no provedor onde vocês compraram o domínio
4. Tipo: `CNAME` | Nome: `jpr` | Aponta para: `[seu-site].netlify.app`
5. Em até 1 hora começa a funcionar com HTTPS automático grátis

---

## ✅ Passo 5 — Testar tudo (5 min)

**Cheque essas 5 coisas:**

### Teste 1 — Apresentação
1. Abra a URL no celular ou tablet
2. Clique em **"Apresentar"**
3. Navegue pelos 7 slides
4. No slide 4 ("Plataforma"), clique em **"Ver painel ao vivo"** → tem que abrir o sistema RAC
5. No slide 5 ("Busca ao vivo"), digite uma cidade, clique numa modalidade → tem que abrir o Google numa nova aba
6. No slide 6 ("Case CEICO"), clique em **"Ver ROI auditável"** → tem que abrir o ROI da Ceico

### Teste 2 — Captura
1. Volta na tela inicial → **"Capturar Lead"**
2. Digite seu nome (vai ficar salvo no dispositivo)
3. Preencha um lead-teste com seu próprio número de WhatsApp
4. Clique **"Quero agendar conversa"**
5. Tela de "Tudo certo!" tem que aparecer

### Teste 3 — Dashboard ao vivo
1. Volta na tela inicial → **"Dashboard"**
2. Digite seu PIN (que você definiu no Passo 2)
3. Você tem que ver o lead-teste que acabou de cadastrar aparecer na lista

### Teste 4 — Sincronização entre dispositivos
1. Abra a mesma URL em **outro celular** ou **no notebook ao mesmo tempo**
2. Cadastre um lead num dispositivo
3. **Em até 2 segundos** ele aparece no dashboard do outro dispositivo
4. Se aparecer = está tudo funcionando ✅

### Teste 5 — Exportar CSV
1. No dashboard, clique no botão **"CSV"** no canto superior direito
2. Um arquivo `win7med_jpr_2026-XX-XX.csv` vai baixar
3. Abra no Excel ou Google Sheets pra confirmar que tem todas as colunas

---

## 📱 Como usar no JPR

### Pros SDRs (Luís e equipe)
- **Adicionar à tela inicial do iPad/celular:** Safari → ícone de compartilhar (caixinha com seta) → "Adicionar à Tela de Início" → o site vira tipo um app
- O SDR digita o nome dele **uma vez** no primeiro acesso, depois só capturar leads
- Cada lead capturado fica marcado com nome do SDR (atribuição automática)

### Pra Ranna acompanhar pipeline
- Abre o **Dashboard** do notebook ou celular
- Atualização em tempo real — vê os leads chegando ao vivo durante o JPR
- Filtra por status, por SDR, por tipo (cliente/parceiro)
- No final de cada dia, exporta CSV pra distribuir o follow-up

---

## 🆘 Deu problema?

**"Modo demo: configure o Firebase..."** aparece embaixo da tela
→ Volta no Passo 2, confirme que colou os valores do Firebase corretamente. Salvou o arquivo? Subiu o arquivo novo no GitHub? O Netlify republicou?

**Cadastrei um lead mas não aparece no dashboard de outro dispositivo**
→ Confirme que os dois dispositivos estão na mesma URL. Aperte F12 (no notebook) pra ver erros do console. Se aparecer "permission-denied", refaça o Passo 1.4 (regras do Firestore).

**PIN incorreto**
→ Confirme o valor de `dashboardPin` no `index.html` que você subiu. É case-sensitive. Tem que ser exatamente 4 dígitos.

**Esqueci o PIN**
→ Edite o `index.html` no GitHub direto (basta clicar no arquivo lá → ícone de lápis → editar → commit), o Netlify republica em 30 segundos.

**Quero mudar algo no sistema (texto, cor, etc.)**
→ Edita o arquivo no GitHub, salva (commit), e em ~30 segundos o Netlify atualiza automaticamente o site no ar. **Não precisa fazer deploy de novo.**

---

## 📊 Estrutura dos dados (referência técnica)

Cada lead salvo no Firestore tem:

```javascript
{
  // Identificação
  name: "Dr. João Silva",
  role: "Sócio-diretor",
  clinic: "Clínica Imagem Paulista",
  city: "São Paulo / SP",

  // Tipo + qualificação (só pra cliente, não pra parceiro)
  leadType: "cliente",  // ou "parceiro"
  revenue: "150-500k",  // ate-50k | 50-150k | 150-500k | 500k+ | prefiro-nao-dizer
  hasPartner: "socio",  // sozinho | socio | grupo

  // Contato
  whatsapp: "(11) 91234-5678",
  email: "joao@clinica.com.br",

  // Interesse
  modalities: ["TC", "RM", "MMG"],
  notes: "Tem 2 ressonâncias paradas...",

  // Atribuição & status
  capturedBy: "Luís Guilherme",
  status: "new",  // new | contacted | scheduled | closed | lost
  createdAt: <timestamp>
}
```

---

**Win7 Med · JPR 2026 · Boa sorte na captação!** 🎯
