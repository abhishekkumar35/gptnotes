

**simple Model**
```javascipt
model PdfFile {
  id       Int    @id @default(autoincrement())
  fileName String
  fileData Bytes
  name String
  email String
}```


Prisma is an advanced Object-Relational Mapping (ORM) tool that streamlines database interactions in Node.js and TypeScript applications. It offers a robust and type-safe API for performing Create, Read, Update, and Delete (CRUD) operations. Below is a comprehensive cheatsheet detailing Prisma's CRUD methods and their subcomponents.

**1. Create Operations**

- **`create`**: Inserts a single record into the database.

  
```javascript
  const newUser = await prisma.user.create({
    data: {
      email: 'elsa@prisma.io',
      name: 'Elsa Prisma',
    },
  });
  ```


- **`createMany`**: Inserts multiple records simultaneously.

  
```javascript
  const newUsers = await prisma.user.createMany({
    data: [
      { email: 'anna@prisma.io', name: 'Anna Prisma' },
      { email: 'kristoff@prisma.io', name: 'Kristoff Bjorgman' },
    ],
  });
  ```


**2. Read Operations**

- **`findUnique`**: Retrieves a single record that matches a unique identifier.

  
```javascript
  const user = await prisma.user.findUnique({
    where: {
      email: 'elsa@prisma.io',
    },
  });
  ```


- **`findFirst`**: Fetches the first record that matches the specified criteria.

  
```javascript
  const user = await prisma.user.findFirst({
    where: {
      name: 'Elsa Prisma',
    },
  });
  ```


- **`findMany`**: Retrieves all records that match the given criteria.

  
```javascript
  const users = await prisma.user.findMany({
    where: {
      name: {
        contains: 'Prisma',
      },
    },
  });
  ```


**3. Update Operations**

- **`update`**: Modifies a single record identified by a unique field.

  
```javascript
  const updatedUser = await prisma.user.update({
    where: {
      email: 'elsa@prisma.io',
    },
    data: {
      name: 'Elsa Frozen',
    },
  });
  ```


- **`updateMany`**: Updates multiple records that meet the specified criteria.

  
```javascript
  const updatedUsers = await prisma.user.updateMany({
    where: {
      name: {
        contains: 'Prisma',
      },
    },
    data: {
      isActive: false,
    },
  });
  ```


**4. Delete Operations**

- **`delete`**: Removes a single record identified by a unique field.

  
```javascript
  const deletedUser = await prisma.user.delete({
    where: {
      email: 'elsa@prisma.io',
    },
  });
  ```


- **`deleteMany`**: Deletes multiple records that match the specified criteria.

  
```javascript
  const deletedUsers = await prisma.user.deleteMany({
    where: {
      isActive: false,
    },
  });
  ```


**5. Additional Operations**

- **`upsert`**: Performs an update if the record exists; otherwise, it creates a new record.

  
```javascript
  const upsertedUser = await prisma.user.upsert({
    where: {
      email: 'elsa@prisma.io',
    },
    update: {
      name: 'Elsa Updated',
    },
    create: {
      email: 'elsa@prisma.io',
      name: 'Elsa Created',
    },
  });
  ```


- **`count`**: Returns the number of records that match the specified criteria.

  
```javascript
  const userCount = await prisma.user.count({
    where: {
      isActive: true,
    },
  });
  ```


- **`aggregate`**: Performs aggregation operations like sum, average, min, and max on specified fields.

  
```javascript
  const aggregateResult = await prisma.user.aggregate({
    _avg: {
      age: true,
    },
    _max: {
      age: true,
    },
    _min: {
      age: true,
    },
    _sum: {
      age: true,
    },
    where: {
      isActive: true,
    },
  });
  ```


- **`groupBy`**: Groups records by specified fields and allows aggregation on grouped data.

  
```javascript
  const groupedUsers = await prisma.user.groupBy({
    by: ['role'],
    _count: {
      _all: true,
    },
    _avg: {
      age: true,
    },
    _sum: {
      age: true,
    },
    where: {
      isActive: true,
    },
  });
  ```


This cheatsheet serves as a quick reference to Prisma's CRUD methods and their subcomponents, facilitating efficient and type-safe database operations in your applications. For more detailed information, refer to the [Prisma Documentation](https://www.prisma.io/docs/orm/prisma-client/queries/crud). 
