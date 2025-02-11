# Repositório do evento NLW Unite Nodejs

# pass.in

O pass.in é uma aplicação de **gestão de participantes em eventos presenciais**.

A ferramenta permite que o organizador cadastre um evento e abra uma página pública de inscrição.

Os participantes inscritos podem emitir uma credencial para check-in no dia do evento.

O sistema fará um scan da credencial do participante para permitir a entrada no evento.

## Requisitos

### Requisitos funcionais

- [ ] O organizador deve poder cadastrar um novo evento;
- [ ] O organizador deve poder visualizar dados de um evento;
- [ ] O organizador deve poser visualizar a lista de participantes;
- [ ] O participante deve poder se inscrever em um evento;
- [ ] O participante deve poder visualizar seu crachá de inscrição;
- [ ] O participante deve poder realizar check-in no evento;

### Regras de negócio

- [ ] O participante só pode se inscrever em um evento uma única vez;
- [ ] O participante só pode se inscrever em eventos com vagas disponíveis;
- [ ] O participante só pode realizar check-in em um evento uma única vez;

### Requisitos não-funcionais

- [ ] O check-in no evento será realizado através de um QRCode;

## Documentação da API (Swagger)

Para documentação da API, acesse o link:

## Banco de dados

Nessa aplicação vamos utilizar banco de dados relacional (SQL). Para ambiente de desenvolvimento seguiremos com o SQLite pela facilidade do ambiente.

### Diagrama ERD

<img src=".github/erd.svg" width="600" alt="Diagrama ERD do banco de dados" />

### Estrutura do banco (SQL)

```sql
-- CreateTable
CREATE TABLE "events" (
    "id" TEXT NOT NULL PRIMARY KEY,
    "title" TEXT NOT NULL,
    "details" TEXT,
    "slug" TEXT NOT NULL,
    "maximum_attendees" INTEGER
);

-- CreateTable
CREATE TABLE "attendees" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "name" TEXT NOT NULL,
    "email" TEXT NOT NULL,
    "event_id" TEXT NOT NULL,
    "created_at" DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT "attendees_event_id_fkey" FOREIGN KEY ("event_id") REFERENCES "events" ("id") ON DELETE RESTRICT ON UPDATE CASCADE
);

-- CreateTable
CREATE TABLE "check_ins" (
    "id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
    "created_at" DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    "attendeeId" INTEGER NOT NULL,
    CONSTRAINT "check_ins_attendeeId_fkey" FOREIGN KEY ("attendeeId") REFERENCES "attendees" ("id") ON DELETE RESTRICT ON UPDATE CASCADE
);

-- CreateIndex
CREATE UNIQUE INDEX "events_slug_key" ON "events"("slug");

-- CreateIndex
CREATE UNIQUE INDEX "attendees_event_id_email_key" ON "attendees"("event_id", "email");

-- CreateIndex
CREATE UNIQUE INDEX "check_ins_attendeeId_key" ON "check_ins"("attendeeId");
```

### Configurando projeto:

- Gera o package.json: `$ npm init -y`
- Instala as dependências do typescript e da tipagem em modo de desenvolvimento: `$ npm i typescript @types/node -D`
- Gera o tsconfig.json: `$ npx tsc --init`
- Converte os arquivos typescript em javascript: `$ npx tsc`
- Obs: Necessário incluir no final do tsconfig.json a linha: "include": ["src"]
- Instala o tsx como dependência de desenvolvimento para facilitar o processo de conversão dos arquivos .ts para .js: `$ npm i tsx -D`
- Executa o arquivo: `$ npx tsx src/server.ts`
- Executa o arquivo porém observando alterações: `$ npx tsx watch src/server.ts`
- Criado script dev no package.json:

```json
"scripts": {
    "dev": "tsx watch src/server.ts"
}
```

- Rodando o script criado: `$ npm run dev`
- Instala a dependência fastify: `$ npm i fastify`
- Instala o ORM prisma como dependência de desenvolvimento: `$ npm i prisma -D`
- Executando o prisma utilizando o bd SQLite: `$ npx prisma init --datasource-provider SQLite`

- Modificado script no package.json para o nodejs ler o arquivo .env:

```json
"scripts": {
    "dev": "tsx watch --env-file .env src/server.ts"
}
```

- Executa o processo de criação dos campos definidos no schema.prisma`$ npx prisma migrate dev`
- Defino o nome para a migração:
  ? Enter a name for the new migration: › create table events

- Abrindo a ferramenta do prisma para visualização dos dados do bd: `$npx prisma studio`

- Instala a biblioteca zod de validação: `$ npm i zod`
