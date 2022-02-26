# Prisma Inconsistent Casing in Migrations Issue

## Env
- Microsoft Windows 10
- MariaDB: `10.4.11-MariaDB - mariadb.org binary distribution`
- NodeJS: `v17.4.0`
- Prisma: `v3.10.0`

## How it was setup
The initial (and committed) migration was created with `npx prisma migrate dev`

## How to trigger the bug
Rename a field in the schema (e.g. `name` to `myNameUpdated` in the `User` model).

Then, run `npx prisma migrate dev`.

This creates the following migration file:

```SQL
/*
  Warnings:

  - You are about to drop the column `name` on the `user` table. All the data in the column will be lost.
  - Added the required column `myNameUpdated` to the `User` table without a default value. This is not possible if the table is not empty.

*/
-- AlterTable
ALTER TABLE `user` DROP COLUMN `name`,
    ADD COLUMN `myNameUpdated` VARCHAR(191) NOT NULL;
```

Notice the inconsistent casing. The first warning says `user` table and the second one `User` table.

The SQL Statement uses `user` table which causes the migration to fail on Linux (as the table names use PascalCase there).