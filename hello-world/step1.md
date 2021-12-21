เริ่มต้นด้วยการติดตั้ง PostgreSQL ใน Docker container

run container ชื่อ postgres จาก postgres image (detached mode = รัน in background) default user: postgres จะถูกสร้างขึ้น 
`docker run --name postgres-experiment -e POSTGRES_PASSWORD=password -d postgres`{{execute}}

- POSTGRES_PASSWORD : required for you to use the PostgreSQL image

เปิดโปรแกรม command line (bash) ใน container postgres
`docker exec -it postgres-experiment bash`{{execute}}

`psql -U postgres`{{execute}}