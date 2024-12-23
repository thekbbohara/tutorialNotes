# Prisma 101 baby.
## What is prisma ?
Prisma is an ORM (Object Relational Mapper) that simplifies database interactions for Node.js and TypeScript applications.
_**ORM**  is a software tool that connects programming code to database structures._
Don't worry if you don't get it.

### Why use prisma?
1. Automatically generates TypeScript types which gives a lots of benefits like: 
- **Error Prevention**
```ts
const user = await prisma.user.findUnique({
  where: { id: "123" }, // Error: `id` expects a number, not a string!
});
```
- **Auto-Completion**
- **Seamless Refactoring**: If you update your database schema , Prisma updates the generated types and your IDE will highlight where you need changes in your code.
- **Strongly-Typed Relationships**: Prisma understands relationships between tables and generates types accordingly.
2. Thats all I care about for prisma 101.
## How to setup prisma?
1. Install the Prisma CLI as a dev dependency in the project.
```bash
pnpm add prisma -d
```
2. Now initialize  prisma in your project.
```bash
npx prisma init --datasource-provider mysql # depends on what you use.
```
This creates a `prisma` dir with a `schema.prisma` file and configures mysql as your database. 
And that's all you have to do. How easy pg.
## schema.prisma
In this file you define model/schema. 
```prisma
model Gf {  
id Int @id @default(autoincrement())  
name String @db.Varchar(255)
Chats Chat[] @relation("GfToChats")
}

model Chat{
id Int @id @default(autoincrement())
msg String @db.Text
gfId Int
gf Gf @relation("GfToChats", fields:[gfId] references:[id])
}
```
But Before we move into next step let me tell a little about pre-existed code.
```prisma
// Generator block: Specifies what Prisma should generate after running 
`prisma generate`.
generator client {
  provider = "prisma-client-js" // Generates the Prisma Client for JavaScript/TypeScript.
}
```

```prisma

// Datasource block: Defines the connection to your database.
datasource db {
  provider = "mysql" // Specifies the type of database you're using (MySQL in this case).
  url = env("DATABASE_URL")
}
```
Now what next?
## Migration
At this point, you have a Prisma schema but no database yet. Run the following command in your terminal to create the mysql database and the `Gf` and `Chat` tables represented by your models:
```prisma
npx prisma migrate dev --name init
```
This command did three things:

1. It created a new SQL migration file for this migration in the `prisma/migrations` dir.
2. It executed the SQL migration file against the database.
3. It ran `prisma generate` under the hood (which installed the `@prisma/client` package and generated a tailored Prisma Client API based on your models). 
## `prisma generate`
`npx prisma generate` is a command within the Prisma CLI that reads your Prisma schema file and generates a tailored database client called "Prisma Client" which allows you to interact with your database using type-safe queries, essentially creating the necessary code to perform operations like creating, reading, updating, and deleting data based on your defined data models in the schema; you need to run this command whenever you make changes to your Prisma schema to update the generated client code.
You can say it generates a strongly-typed functions to  interact with db. "That how I imagine it."

## `prisma studio`
`npx prisma studio` it gives a web gui to interact with you db.
## Prisma Client
Now that the Prisma Client has been generated, you can start using it to interact with your database.
```ts
// for not serverless application
import { PrismaClient } from '@prisma/client';
const prisma = new PrismaClient();
export default prisma;
```

```ts
// for serverless
import { PrismaClient } from "@prisma/client";

const prismaClientSingleton = () => {
  return new PrismaClient();
};

declare const globalThis: {
  prismaGlobal: ReturnType<typeof prismaClientSingleton>;
} & typeof global;

const prisma = globalThis.prismaGlobal ?? prismaClientSingleton();

export default prisma;

if (process.env.NODE_ENV !== "production") globalThis.prismaGlobal = prisma;

```
## Interact with db
Now you get into action and create Gf or chat.
```ts
// Create a new Gf
import prisma from @/lib/db
const create Gf = async () =>{
  const gf = await prisma.Gf.create({
	data:{
	  name:"rishi",
	  email:"mujrawala@gfmail.com",
	  Chats: { create: { msg: "Hello" } }
	}
  });
  console.log("Created a new Gg:",gf.name);
}
```

```ts
// breakup
const breakup= async() => {
  const ex = await prisma.Gf.delete({ where: { name: "jainam" }});   
  console.log("Broke up with:", ex.name);
}
```
## Handle Disconnection
Don’t forget to disconnect the Prisma Client when you’re done using it to avoid memory leaks or database connection issues.
```ts
try{
  // Try creating new gf, break up or patch up 
}catch{
  // Cant even make a gf, f**king looser
}finally{
  await prisma.$disconnect();
}
```
## Seed Script
A seed script is **a script that generates dummy data**
```ts
// prisma/seed.ts
import { PrismaClient } from '@prisma/client';
const prisma = new PrismaClient();

async function main() {
  await prisma.gf.create({
    data: {
      name: "raashed",
      email:"raaseed@gfmail.com"
    },
  });
}

main().catch(e => {
  throw e;
}).finally(async () => {
  await prisma.$disconnect();
});
```
## More on Migrations
Prisma migrations allow you to manage database schema changes over time, making it easy to apply, track, and rollback changes to your database.
`prisma migrate dev` vs `prisma migrate deploy`
- **`npx prisma migrate dev`**: This command is used during development. It automatically applies new migrations to your database and also updates your Prisma Client based on schema changes. It is ideal when you are actively working on your schema and want to quickly apply changes.
- **`npx prisma migrate deploy`**: This command is used for deploying migrations to a production database. Unlike `dev`, it does not generate new migrations. Instead, it applies all pending migrations created during development.
## `prisma db push`
If you're not using migrations (e.g., in simpler projects or prototypes), you can use `npx prisma db push` to push your Prisma schema directly to the database. This will create tables based on your Prisma schema but without the tracking and rollback features of migrations.
This is useful for quick changes but is not recommended for production environments.
