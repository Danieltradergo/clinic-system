# Clinic System - Guia de Implementacao Completo

Este documento fornece um guia passo a passo para implementar todos os componentes do Clinic System.

## Arquivos Disponiveis no Gist

Todos os arquivos de codigo estao disponibilizados em:
**https://gist.github.com/Danieltradergo/36d8e5b1cc20871f73f8918f5e287ea0**

Arquivos criados:
- `setup.sh` - Script de setup inicial
- `schema.prisma` - Schema completo do banco de dados
- `seed.ts` - Script de seed (dados iniciais)

## Passo 1: Setup Local (5 minutos)

```bash
# Clone
git clone https://github.com/Danieltradergo/clinic-system.git
cd clinic-system

# Estrutura
mkdir -p apps/{api,web}
mkdir -p apps/api/{src,prisma}
mkdir -p apps/web/src

# Backend
cd apps/api
npm install
```

## Passo 2: Copiar Arquivos do Gist

### 2.1 Schema Prisma
1. Acesse: https://gist.github.com/Danieltradergo/36d8e5b1cc20871f73f8918f5e287ea0
2. Clique em `schema.prisma`
3. Copie todo conteudo
4. Cole em `apps/api/prisma/schema.prisma`
5. Copie tambem em `apps/api/.env`:
```
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/clinic_dev"
JWT_SECRET="super-secret-jwt-key-change-in-production"
PORT=3001
```

### 2.2 Arquivo de Seed
1. No Gist, clique em `seed.ts`
2. Copie todo conteudo
3. Cole em `apps/api/prisma/seed.ts`

## Passo 3: Configurar Banco de Dados

### Opcao A: PostgreSQL Local
```bash
# Mac com Homebrew
brew install postgresql@15
brew services start postgresql@15

# Criar banco
createdb clinic_dev
```

### Opcao B: Railway (Recomendado)
1. Acesse https://railway.app
2. Crie novo projeto
3. Adicione PostgreSQL
4. Copie DATABASE_URL
5. Cole em `.env`

## Passo 4: Rodar Migracao e Seed

```bash
cd apps/api

# Gerar cliente Prisma
npx prisma generate

# Criar tabelas
npx prisma migrate dev --name init

# Popular com dados iniciais
npm run prisma:seed
```

**Credenciais de teste criadas:**
- Email: `admin@clinica.com`
- Email: `financeiro@clinica.com`
- Senha: `Admin@123`

## Passo 5: Frontend (Next.js)

```bash
cd apps/web
npm install
npm run dev
```

## Passo 6: Rodar Tudo

**Terminal 1 - Backend:**
```bash
cd apps/api
npm run start:dev
```

**Terminal 2 - Frontend:**
```bash
cd apps/web
npm run dev
```

**URLs:**
- Frontend: http://localhost:3000
- Backend: http://localhost:3001

## Proximas Etapas de Implementacao

Os seguintes modulos precisam ser criados:

### 1. Modulo de Autenticacao (JWT)
- [ ] `src/auth/auth.service.ts` - Logica de login
- [ ] `src/auth/auth.controller.ts` - Rotas de auth
- [ ] `src/auth/jwt.strategy.ts` - Estrategia JWT
- [ ] `src/auth/auth.module.ts` - Modulo auth

### 2. Servicos do Backend
- [ ] `src/users/users.service.ts`
- [ ] `src/patients/patients.service.ts`
- [ ] `src/professionals/professionals.service.ts`
- [ ] `src/insurances/insurances.service.ts`
- [ ] `src/services/service-types.service.ts`
- [ ] `src/categories/categories.service.ts`
- [ ] `src/accounts/cash-accounts.service.ts`
- [ ] `src/attendances/attendances.service.ts`
- [ ] `src/receivables/receivables.service.ts`
- [ ] `src/payables/payables.service.ts`
- [ ] `src/reports/reports.service.ts`

### 3. Controllers (Rotas REST)
- [ ] CRUD para cada entidade
- [ ] Endpoints de relatorio
- [ ] Endpoints de dashboard

### 4. Frontend (Next.js)
- [ ] Pagina de login
- [ ] Dashboard principal
- [ ] Tabelas de CRUD (pacientes, profissionais, etc)
- [ ] Relatorios e graficos

## Deploy no Railway

```bash
# Adicionar remoto Railway
git remote add railway <seu-url-railway>

# Push para fazer deploy
git push railway main
```

## Troubleshooting

**Erro de conexao com banco:**
- Verifique DATABASE_URL em `.env`
- Certifique que PostgreSQL esta rodando
- Railway: Conecte novo servíor

**Erro de dependencias:**
```bash
rm -rf node_modules package-lock.json
npm install
```

**Porta 3001 ou 3000 ja em uso:**
```bash
# Kill proceso
lsof -i :3001
kill -9 <PID>
```

## Suporte

Gist com todos os arquivos: https://gist.github.com/Danieltradergo/36d8e5b1cc20871f73f8918f5e287ea0
Repositorio: https://github.com/Danieltradergo/clinic-system
