SQL และ Relational Database เบื้องต้น 
===

ระบบจัดการฐานข้อมูลมีหลากหลาย ที่ได้รับความนิยมคือ PostgreSQL และ MySQL สองตัวนี้จัดอยู่ในประเภท Relational Database Management System (RDBMS) ไม่ว่าจะเป็นสองตัวนี้หรือระบบอื่นจะยึดหลักการและปฏิบัติตามข้อกำหนดของ SQL ที่เป็นมาตรฐานของภาษา

ในแบบฝึกปฏิบัตินี้จะใช้ PostgreSQL (pronounced "post-gress-Q-L") โดยจะเริ่มต้นด้วยรัน PostgreSQL บน Docker container

# รัน PostgreSQL บน Docker
Run container ชื่อ postgres จาก postgres image (detached mode = รัน in background) default user: postgres จะถูกสร้างขึ้น 
`docker run --name postgres-experiment -e POSTGRES_PASSWORD=password -d postgres`{{execute}}

- POSTGRES_PASSWORD : required for you to use the PostgreSQL image

เปิดโปรแกรม command line (bash) ใน container postgres
`docker exec -it postgres-experiment bash`{{execute}}

เข้าใช้ database ผ่านโปรแกรม psql ที่เป็น interface ให้เราติดต่อสื่อสารกับ database ใช้ user account: postgres
`psql -U postgres`{{execute}}

psql จะเริ่มทำงาน เพื่อรับคำสั่งจากผู้ใช้ โดยรับคำสั่งที่เป็นคำสั่งของโปรแกรม psql ที่ขึ้นต้นด้วย backlash `\` และ คำสั่งในภาษา SQL


# SQL และ Relational database
**SQL** (Structured Query Language) คือ ภาษาที่ใช้ในการเลือกข้อมูล (query) ปรับเปลี่ยน (trasform) ข้อมูลในฐานข้อมูลเชิงสัมพันธ์ (relational databases)

ตัวอย่างการใช้คำสั่งในภาษา SQL
https://www.sqltutorial.org/sql-cheat-sheet/

ให้สังเกตว่า คำสั่ง keyword ต่างๆ โดยธรรมเนียมปฏิบัติจะใช้ตัวหนังสือตัวใหญ่ และจบคำสั่งด้วยเซมิโคลอน `;`

ฐานข้อมูลเชิงสัมพันธ์ประกอบด้วย กลุ่มตารางข้อมูลย่อยๆ ที่มีความสัมพันธ์กัน
แนวคิดของตารางข้อมูลจะคล้ายกับโปรแกรม spreadsheet เช่น ในโปรแกรม Excel ที่มีจำนวนคอลัมน์ที่คงที่ แต่ละคอลัมน์มีชื่อเรียก (attributes of the table) และมีการบันทึกข้อมูลหลายๆ แถว (row)

**สร้าง database**
เริ่มต้นเราจะสร้าง database สมมติที่ ชื่อว่า iot โดยใช้คำสั่งในภาษา SQL `CREATE`
`CREATE DATABASE iot;`{{execute}}

**เชื่อมต่อ database**
เชื่อมต่อกับฐานข้อมูล ใช้คำสั่งโปรแกรม psql `\connect DATABASE_NAME` หรือย่อๆ `\c DATABASE_NAME`
ในที่นี้เราต้องการเชื่อมต่อกับ database ที่ชื่อ 'iot'
`\c iot`{{execute}}

**สร้างข้อมูลสมมติ**
การสร้างตารางขึ้นมาต้องมีการบอกโครงสร้างข้อมูล (Schema) ว่าข้อมูลที่จะถูกจัดเก็บมีชนิดข้อมูลอะไรบ้าง 

ในแบบฝึกหัดนี้จะสร้างข้อมูลสมมติ เพื่อใช้ทดลองคำสั่งพื้นฐานต่างๆ ของ SQL
https://sqlbolt.com/lesson/creating_tables

`CREATE TABLE sensors(id SERIAL PRIMARY KEY, type VARCHAR(50), location VARCHAR(50), reading decimal);`{{execute}}

ข้างบนเป็นการใช้คำสั่งสร้างตารางชื่อว่า sensors ประกอบด้วยข้อมูลย่อย (คอลัมน์) id, type, location
โดย id เป็น ข้อมูลหลัก (primary key) ที่ทำให้ข้อมูลในแต่ละแถว unique
type และ location เป็นข้อมูลที่เก็บได้มากสุด 50 characters

แสดง table ที่มีอยู่ใน database
`\dt`{{execute}}

แสดง schema ของ table `sensors` 
`\d sensors`{{execute}}

ใส่ข้อมูลเพื่อทำการทดสอบ โดยใช้ คำสั่ง SQL ชื่อว่า `INSERT`
https://sqlbolt.com/lesson/inserting_rows

`INSERT INTO sensors(type, location, reading) VALUES ('temperature', 'S1', 22.0),('temperature', 'S1', 23.0),('temperature', 'S2', 24.0),('temperature', 'S2',  25.0),('temperature', NULL, 20.0),('humidity', 'S1', 0.6),('humidity', 'S2', 0.7),('humidity', NULL, 0.4);`{{execute}}


`SELECT * FROM sensors`{{execute}}

`CREATE TABLE buildings(building_id VARCHAR(50) PRIMARY KEY, address VARCHAR(50));`{{execute}}

`INSERT INTO buildings VALUES ('S1', '98 Moo 8 Doi Saket'),('S2', '98 Moo 8 Doi Saket');`{{execute}}

# Simple select queries
https://sqlbolt.com/lesson/select_queries_review

ดูข้อมูล buildings ทุกคอลัมน์ (ทุก attributes ใช้ *)
`SELECT * FROM buildings;`{{execute}}

ดูข้อมูล buildings เฉพาะคอลัมน์ (ตาม attributes ที่กำหนด)
`SELECT building_id FROM buildings;`{{execute}}


# Multi-table queries with JOINs
https://sqlbolt.com/lesson/select_queries_with_joins

การเชื่อม table เข้าด้วยกัน
`SELECT * FROM sensors INNER JOIN buildings ON sensors.location = buildings.building_id;`{{execute}}

# Select queries with expression
https://sqlbolt.com/lesson/select_queries_with_expressions

ดูข้อมูล sensor เฉพาะตัวที่กำหนด
`SELECT * FROM sensors WHERE id=3;`{{execute}}

`SELECT * FROM sensors WHERE type='humidity' AND reading>0.6;`{{execute}}

ดูว่า sensor ตัวไหนยังไม่ได้ถูกติดตั้งในในอาคาร
`SELECT * FROM sensors WHERE location IS NULL;`{{execute}}

# การจัดลำดับ (ORDER) และจำกัดการแสดงผล (LIMIT)
https://sqlbolt.com/lesson/filtering_sorting_query_results

แสดงข้อมูล temperature sensors เรียงตามค่าที่อ่านได้จากน้อยไปมาก
`SELECT * FROM sensors WHERE type='temperature' ORDER BY reading ASC;`{{execute}}

แสดงข้อมูล temperature sensors เรียงตามค่าที่อ่านได้จากน้อยไปมาก สามค่าแรก
`SELECT * FROM sensors WHERE type='temperature' ORDER BY reading ASC LIMIT 3;`{{execute}}

# Select queries with aggregate
https://sqlbolt.com/lesson/select_queries_with_aggregates

หาค่าเฉลี่ย temperature ของแต่ละตึก
`SELECT location, AVG(reading) AS average_temp FROM sensors WHERE type='temperature' AND location IS NOT NULL GROUP BY location;`{{execute}}
