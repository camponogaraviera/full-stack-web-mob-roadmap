<div align='center'>
  <h1>MongoDB</h1>
</div>

# Table of Contents

- About
- Mongoose
- Installing MongoDB
- Installing mongoose
- Installing MongoDB Compass

# About

MongoDB is a general-purpose, document-based distributed NoSQL (non-relational) database used to store unstructured or semi-structured data, such as texts in a BSON (Binary JSON) format, and references to multimedia files (image, audio, video). It is not used as the primary storage solution for multimedia files. Services like `AWS S3` are more suited for storing large multimedia files, while MongoDB is used to store metadata and references to these files.

Being a NoSQL database, it is schema-less, i.e., it doesn't enforce a rigid schema like traditional relational databases. It provides security best practices from the get-go, including authentication and authorization. It also provides support for [multi-document ACID transactions](https://www.mongodb.com/blog/post/mongodb-multi-document-acid-transactions-general-availability), enabling use cases such as financial transactions that require rollbacks.

Use cases include storage of customer details, product catalogs, social media posts, emails, log files, etc.

# Mongoose 

Mongoose is a JavaScript Object Data Modeling (ODM) library for MongoDB. It is used to impart a schema for the documents within a MongoDB collection, as MongoDB is a schema-less database.

 <!-- ################################################################ -->

# Installing MongoDB

- First, install brew:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

```bash
brew install mongodb
```

- Create a DB directory:

```bash
sudo mkdir -p /data/db && sudo chown - R `id -un` /data/db
```

- Run the mongo daemon:

```bash
mongod
```

# Installing mongoose

```bash
npm i mongoose
```

# Installing MongoDB Compass

- [Install MongoDB Compass GUI](https://www.mongodb.com/try/download/shell):

```bash
sudo dpkg -i package.deb
```
