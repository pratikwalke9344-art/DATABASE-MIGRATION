# DATABASE-MIGRATION
*COMPANY :* CODETECH IT SOLUTION

*NAME :* WALKE PRATIK BABU

*INTERN ID :* CTIS4099

*DOMAIN :* SQL

*DURATION :* 4 WEEEKS

*MENTOR :* NEELA SANTHOSH KUMAR

This task demonstrates how to migrate data between two relational database systems — specifically from MySQL to PostgreSQL — while ensuring schema compatibility and data integrity. Migration is a critical process in real‑world projects, often required when organizations switch database platforms for performance, scalability, or licensing reasons. The challenge lies in handling differences in SQL dialects, data types, and constraints while preserving the accuracy of the data.

1. Export Schema and Data from MySQL
The first step is to export both the schema and the data from the source MySQL database. Using mysqldump, we can generate two separate files:

Schema only: bash

     mysqldump -u root -p --no-data prtkintern > mysql_export_schema.sql
   
Data only: bash

    mysqldump -u root -p --no-create-info prtkintern > mysql_export_data.sql
   
This separation allows us to adapt the schema for PostgreSQL while keeping the raw data intact.

2. Adapt Schema for PostgreSQL
MySQL and PostgreSQL differ in certain syntax and features. For example:
MySQL uses AUTO_INCREMENT for primary keys, while PostgreSQL uses SERIAL.
MySQL includes engine and charset clauses (ENGINE=InnoDB, DEFAULT CHARSET=utf8mb4), which are unnecessary in PostgreSQL.
Foreign key constraints must be explicitly defined in PostgreSQL.
PostgreSQL schema example:

sql

    CREATE TABLE job_seekers (
        seeker_id SERIAL PRIMARY KEY,
        name VARCHAR(100),
        email VARCHAR(100)
    );

    CREATE TABLE companies (
        company_id SERIAL PRIMARY KEY,
        company_name VARCHAR(100),
        industry VARCHAR(50)
    );
    
    CREATE TABLE jobs (
        job_id SERIAL PRIMARY KEY,
        company_id INT REFERENCES companies(company_id),
        title VARCHAR(100)
    );

    CREATE TABLE applications (
        application_id SERIAL PRIMARY KEY,
        seeker_id INT REFERENCES job_seekers(seeker_id),
        job_id INT REFERENCES jobs(job_id),
        status VARCHAR(20)
    );

3. Import into PostgreSQL
Once the schema is adapted, create the target database and import both schema and data:

       psql -U postgres -c "CREATE DATABASE prtkintern;"
       psql -U postgres -d prtkintern -f postgres_schema.sql
       psql -U postgres -d prtkintern -f mysql_export_data.sql
   
5. Verify Data Integrity
After migration, it is essential to confirm that the data matches the source. This can be done by comparing row counts and checking relationships.

Row count verification:
sql

     SELECT COUNT(*) FROM job_seekers;
     SELECT COUNT(*) FROM companies;
     SELECT COUNT(*) FROM jobs;
     SELECT COUNT(*) FROM applications;
   
Relationship verification:
sql

     SELECT a.application_id, js.name, j.title
     FROM applications a
     JOIN job_seekers js ON a.seeker_id = js.seeker_id
     JOIN jobs j ON a.job_id = j.job_id;
     
If counts and relationships match the MySQL source, integrity is preserved.
