# Clinic System

**Sistema de Gestão Financeira e Operacional para Clínicas de Psicologia e Psiquiatria**

Plataforma completa para gerenciar finanças, pacientes, agenda profissional e relatórios de uma clínica.

## 🚀 Stack

- **Backend**: Nest.js + Prisma + PostgreSQL
- **Frontend**: Next.js + TypeScript + Tailwind CSS
- **Infraestrutura**: Railway (Banco + API) + Vercel (Frontend)
- **Autenticação**: JWT

## 📋 Requir...

## 📋 Requisitos

- Node.js 18+
- npm ou yarn
- PostgreSQL (ou cria no Railway)
- Conta GitHub
- Conta Railway
- Conta Vercel

## 🚀 Setup Completo (Copy & Paste)

### 1. Clone e Estrutura Base

```bash
# Clone o repo
git clone https://github.com/Danieltradergo/clinic-system.git
cd clinic-system

# Crie estrutura monorepo
mkdir -p apps/{api,web}
mkdir -p apps/api/{src,prisma}
mkdir -p apps/web/src
```

### 2. Setup do Backend (Nest.js)

```bash
cd apps/api

# Crie package.json
cat > package.json << 'EOF'
{
  "name": "@clinic-system/api",
  "version": "1.0.0",
  "description": "Clinic System API",
  "main": "dist/main.js",
  "scripts": {
    "build": "nest build",
    "start": "node dist/main",
    "start:dev": "nest start --watch",
    "prisma:generate": "prisma generate",
    "prisma:migrate": "prisma migrate dev",
    "prisma:seed": "ts-node prisma/seed.ts"
  },
  "dependencies": {
    "@nestjs/common": "^10.0.0",
    "@nestjs/core": "^10.0.0",
    "@nestjs/jwt": "^11.0.0",
    "@nestjs/passport": "^10.0.0",
    "@nestjs/platform-express": "^10.0.0",
    "@prisma/client": "^5.0.0",
    "bcrypt": "^5.1.1",
    "passport": "^0.7.0",
    "passport-jwt": "^4.0.1",
    "reflect-metadata": "^0.1.13",
    "rxjs": "^7.8.1"
  },
  "devDependencies": {
    "@nestjs/cli": "^10.0.0",
    "@types/node": "^20.0.0",
    "@types/bcrypt": "^5.0.2",
    "prisma": "^5.0.0",
    "ts-node": "^10.9.1",
    "typescript": "^5.1.3"
  }
}
EOF

# Instale dependências
npm install
```

### 3. Banco de Dados

**Opção A: PostgreSQL Local**
```bash
# Crie .env
echo 'DATABASE_URL="postgresql://postgres:postgres@localhost:5432/clinic_dev"' > .env
echo 'JWT_SECRET="sua-chave-secreta-aqui"' >> .env
echo 'PORT=3001' >> .env
```

**Opção B: Railway (Recomendado)**
- Vá em https://railway.app
- Crie projeto novo
- Adicione PostgreSQL
- Copie DATABASE_URL para .env

### 4. Deploy no Railway

```bash
# De volta na raiz (clinic-system)
cd ../..

# Crie railway.json
cat > railway.json << 'EOF'
{
  "$schema": "https://railway.app/railway.schema.json",
  "build": {
    "builder": "nixpacks",
    "buildCommand": "cd apps/api && npm install && npm run build"
  },
  "deploy": {
    "startCommand": "cd apps/api && npm start",
    "restartPolicyType": "on_failure",
    "restartPolicyMaxRetries": 5
  }
}
EOF
```

### 5. Frontend (Next.js)

```bash
cd apps/web

# Crie package.json
cat > package.json << 'EOF'
{
  "name": "@clinic-system/web",
  "version": "1.0.0",
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  },
  "dependencies": {
    "next": "^14.0.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "typescript": "^5.2.0"
  }
}
EOF

npm install
```

## 🚀 Rodando Localmente

```bash
# Backend
cd apps/api
npm run start:dev

# Frontend (outro terminal)
cd apps/web
npm run dev
```

Backend: http://localhost:3001
Frontend: http://localhost:3000

## 🚀 Deploy

1. **Backend Railway**: Push no GitHub → Railway puxa automaticamente
2. **Frontend Vercel**: Connect repo → Deploy automático

## 📊 Estrutura de Dados

Veja `SCHEMA.md` para detalhes completos do banco.

## 🚘 Status

- [x] Estrutura base criada
- [ ] Autenticação JWT completa
- [ ] CRUD de pacientes, profissionais, convênios
- [ ] Lançamentos financeiros
- [ ] Relatórios

## 📋 Licença

MIT
