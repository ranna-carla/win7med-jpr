# Win7 Med — Sistema JPR 2026

Sistema único e auto-contido para captação ativa de leads no JPR (Jornada Paulista de Radiologia). Inclui apresentação interativa, formulário de captura e dashboard com métricas em tempo real.

## ✨ O que o sistema faz

### 1. Apresentação interativa
- 6 slides navegáveis (capa, diferenciais, modalidades, tecnologia, case CEICO, CTA)
- Animações sutis de contadores
- Mock do dashboard real da Win7 Med
- Controles via teclado (← → para navegar, ESC para sair)
- CTA final que leva direto ao formulário

### 2. Captura de leads
- Identificação do SDR (uma vez por dispositivo)
- Formulário com nome, clínica, cidade, WhatsApp (com máscara), e-mail
- Seleção de modalidades coloridas (TC, RM, US, RX, MMG, DEXA)
- Observações livres
- Validação em tempo real
- Modo offline: salva localmente quando sem internet, sincroniza depois

### 3. Dashboard (acesso por PIN)
- Métricas principais: total de leads, hoje, última hora, taxa de conversão
- Ranking de SDRs (quem captou mais)
- Distribuição por modalidade de interesse
- Distribuição por status (novo, contatado, agendado, fechado, perdido)
- Gráfico de captação por hora do dia
- Lista completa com busca, filtros e exportação CSV
- Detalhe de cada lead com botão direto de abrir WhatsApp

---

## 🚀 Setup completo (15-20 minutos)

### Passo 1: Criar projeto Firebase (5 min)

1. Acesse https://console.firebase.google.com/
2. Clique em **"Adicionar projeto"** → nome: `win7med-jpr` → continue
3. Pode **desativar** o Google Analytics (não é necessário)
4. Após criação, no menu lateral clique em **Build → Firestore Database**
5. Clique em **"Criar banco de dados"** → escolha **modo de teste** (válido por 30 dias) → região: `southamerica-east1` (São Paulo)
6. Vá em **Configurações do projeto** (ícone de engrenagem no topo) → role até **"Seus apps"**
7. Clique no ícone **`</>`** (Web) → registre app com apelido `win7-jpr-web` → **NÃO** marque Firebase Hosting
8. **Copie o objeto `firebaseConfig`** que aparece — vai parecer assim:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSyXXXXXXXXXXXXXXXXXXXXXXXXX",
  authDomain: "win7med-jpr.firebaseapp.com",
  projectId: "win7med-jpr",
  storageBucket: "win7med-jpr.appspot.com",
  messagingSenderId: "123456789012",
  appId: "1:123456789012:web:abcdef123456"
};
```

### Passo 2: Configurar regras de segurança do Firestore (2 min)

No Firebase Console → **Firestore Database → Regras**, cole isto e publique:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /jpr_leads/{leadId} {
      // Permite criar (formulário público) e ler (dashboard com PIN)
      allow read, create: if true;
      // Update e delete: também liberado (controle pelo PIN do dashboard)
      allow update, delete: if true;
    }
  }
}
```

> ⚠️ **Importante:** essas regras são abertas porque o controle de acesso é feito pelo PIN no dashboard. Para produção real, considere migrar para Firebase Authentication.

### Passo 3: Editar o `index.html` (2 min)

Abra `index.html` e localize a seção `window.WIN7_CONFIG` (próximo ao final do arquivo). Cole sua config do Firebase:

```javascript
window.WIN7_CONFIG = {
  firebase: {
    apiKey: "AIzaSyXXXXXXXXXXXXXXXXXXXXXXXXX",
    authDomain: "win7med-jpr.firebaseapp.com",
    projectId: "win7med-jpr",
    storageBucket: "win7med-jpr.appspot.com",
    messagingSenderId: "123456789012",
    appId: "1:123456789012:web:abcdef123456"
  },
  dashboardPin: "1162",  // 👈 PIN de 4 dígitos do dashboard
  eventName: "JPR 2026"
};
```

**Personalize:**
- `dashboardPin`: 4 dígitos. Distribua para Ranna e SDRs autorizados.
- `eventName`: nome do evento (aparece no rodapé).

### Passo 4: Subir para o GitHub (3 min)

```bash
# No terminal, dentro da pasta do projeto:
git init
git add .
git commit -m "Sistema Win7 Med JPR 2026"

# Crie um repositório novo no GitHub: https://github.com/new
# Depois:
git remote add origin https://github.com/SEU-USUARIO/win7med-jpr.git
git branch -M main
git push -u origin main
```

### Passo 5: Deploy no Netlify (3 min)

1. Acesse https://app.netlify.com/
2. Login com GitHub
3. **"Add new site" → "Import an existing project" → GitHub → selecione o repositório**
4. Configurações de build:
   - Build command: deixe **vazio**
   - Publish directory: `.` (ponto)
5. Clique em **"Deploy"**
6. Em ~30 segundos seu site estará online em uma URL tipo `https://magnificent-pony-12345.netlify.app/`
7. (Opcional) **Site settings → Change site name** para algo memorável: `win7med-jpr`
8. (Opcional avançado) **Domain settings** para usar domínio próprio tipo `jpr.win7med.com.br`

### Passo 6: Testar tudo

**Teste o fluxo completo:**

1. Abra a URL no iPad/celular
2. **Modo Apresentar** — navegue pelos 6 slides
3. **Capturar Lead** — digite seu nome, preencha um lead-teste, salve
4. **Dashboard** — digite o PIN, veja o lead aparecer na lista
5. Clique no lead → mude o status → confirme que sumiu da contagem "Novo"
6. Clique em "Exportar CSV" → confirme o download

**Teste com 2 dispositivos simultâneos** — iPad cadastra um lead, computador da Ranna no dashboard vê o lead aparecer em **menos de 2 segundos**.

---

## 📱 Dicas de uso no estande

### Para o SDR
- **Use um iPad (ou tablet) em modo retrato** — interface foi desenhada pra isso
- **Adicione à tela inicial:** Safari → ícone de compartilhar → "Adicionar à Tela de Início" → o site vira um "app" sem barra de navegação
- **Bookmark o atalho `/`** (modo entrada) e configure o iPad pra abrir nele
- **Não desconecte o WiFi do JPR** — sistema funciona offline mas sincroniza só quando volta

### Para a Ranna (gestão)
- Abra o **Dashboard** no notebook ou celular durante o JPR
- Atualização é em tempo real — você vê os leads chegando ao vivo
- Use os filtros pra ver leads por SDR específico ou por status
- Ao final de cada dia, exporte o CSV pra distribuir os contatos pro time fazer follow-up

### Backup e segurança
- Os dados ficam no Firebase do Google — **infraestrutura mais robusta que qualquer planilha**
- Cada lead tem timestamp + autoria registrados
- O CSV exportado é compatível com Excel, Google Sheets, qualquer CRM

---

## 🎨 Identidade visual

O sistema usa a paleta da marca Win7:
- **Amarelo** `#F2B707`
- **Vermelho** `#DD2C2C`
- **Verde** `#1FB75A`
- **Azul** `#2A7BC4`
- Mais o **dourado de destaque** `#F59E0B`

Mantém a mesma linguagem visual do flyer/PDF institucional para que o lead reconheça a marca em qualquer ponto de contato.

---

## 🛠 Stack técnica

- **Frontend:** HTML + Tailwind CSS (CDN) + JavaScript vanilla
- **Database:** Firebase Firestore (real-time sync)
- **Charts:** Chart.js
- **Hospedagem:** Netlify (CDN global, HTTPS automático)
- **Custo total:** R$ 0 dentro dos limites gratuitos (50k leituras/dia no Firebase)

---

## 🐛 Troubleshooting

**"Modo demo: configure o Firebase..."**
Significa que o `firebaseConfig` está vazio ou inválido. Revise o Passo 3.

**Leads não aparecem no dashboard de outro dispositivo**
Confirme que o `projectId` é o mesmo nos dois dispositivos. Veja o console do navegador (F12) — deve aparecer um erro específico.

**"Modo offline ativado"**
A internet caiu ou o Firebase está bloqueado. O sistema continua funcionando localmente — os leads salvos vão sincronizar quando a conexão voltar.

**PIN incorreto repetidamente**
Verifique no `index.html` o valor de `dashboardPin`. É case-sensitive e deve ter exatamente 4 dígitos.

---

## 📝 Estrutura dos dados (referência)

Cada lead salvo no Firestore tem:

```javascript
{
  name: "Dr. João Silva",
  clinic: "Clínica Imagem Paulista",
  city: "São Paulo / SP",
  whatsapp: "(11) 91234-5678",
  email: "joao@clinica.com.br",
  modalities: ["TC", "RM", "MMG"],
  notes: "Tem 2 ressonâncias paradas. Faturamento ~80k/mês.",
  capturedBy: "Ranna Carla",
  status: "new",  // new | contacted | scheduled | closed | lost
  createdAt: <timestamp>,
  statusUpdatedAt: <timestamp opcional>,
  statusUpdatedBy: "Nome do SDR que mudou"
}
```

---

**Win7 Med · Diagnóstico por Imagem · JPR 2026**
