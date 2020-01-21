# How to create a test environment for Response 10.x
**Pull down the MSSQL docker image**

$`docker pull mcr.microsoft.com/mssql/server`

**Then run the container and attach a volume**

$ `docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=asdf1234$$$$' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2017-CU8-ubuntu`

**username:** `sa`

**password:** `asdf1234$$$$`

While that's building go get yourself an SQL editor that works with MSSQL DBeaver is open source and cross platform. 
https://dbeaver.io/download/

**List your containers to find the container ID**

$`docker ps`

    warren@asus:~/Sites/olive$ docker ps
    CONTAINER ID        IMAGE
    93c0c07c9edd        mcr.microsoft.com/mssql/server:2017-CU8-ubuntu

**Next copy your Database from your host machine to the container**

$`docker cp dump.sql 93c0:/var/opt/mssql`

**shell into the container**

$ `docker exec -it <mycontainer> bash`

## Example:

$ `docker exec -it 93c0 bash`

**cd into the volume location we specified when we ran the container initially and make sure your database is there**

$ `docker exec -it 93c0 bash`

    root@93c0c07c9edd:/# ls
    bin   core  etc   install.sh  lib64  mnt  proc  run   srv  tmp  var
    boot  dev   home  lib         media  opt  root  sbin  sys  usr
    root@93c0c07c9edd:/# cd /var/opt/mssql
    root@93c0c07c9edd:/var/opt/mssql# ls
    data  dump.sql  log  secrets

**Import the Database**

    /opt/mssql-tools/bin/sqlcmd -S localhost -d test -U sa -P 'asdf1234$$$$' -i dump.sql -o results.txt

If this succeeds you should be able to make a connection with DBeaver and check your database now.

Or check the database from the container using MSSQL

$ `docker exec -it 93c0/opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P 'asdf1234$$$$'`

    1> use test
    2> go
    Changed database context to 'test'.
    1> SELECT Distinct TABLE_NAME FROM information_schema.TABLES
    2> go
    TABLE_NAME                                                                                                                      
    --------------------------------------------------------------------------------------------------------------------------------
    CATEGINV                                                                                                                        
    CATEGORY                                                                                                                        
    CUSTDIV                                                                                                                         
    FINDER                                                                                                                          
    INVENTOR                                                                                                                        
    LINEITEM                                                                                                                        
    MASMEDIA                                                                                                                        
    ORD_D                                                                                                                           
    PRODCLAS                                                                                                                        
    RELITEM                                                                                                                         
    SERVICE                                                                                                                         
    SHIPTO                                                                                                                          
    SYSCUST                                                                                                                         
    SYSOENT                                                                                                                         
    
    (14 rows affected)



