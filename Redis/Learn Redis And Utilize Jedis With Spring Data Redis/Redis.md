# Redis

Key story database. Master-slave replication. In memory or to file. Open source. Po defaultu no index. Lua support. No query language ali moze pomocu lua da se napravi.

Primena:

- User session managment. Ima auto time expire session
- Caching. Na osnovu search kriterijuma.
- Pub/Sub (Queues & Notifications). 
- Leaderboards for gaming apps. Konvencionalne baze su lose za ovo. Ali redis ima sorted set koji ovo resava
- Geospatial - Distanca izmedju dve tacke, ili u okviru radiusa

## SQL vs NoSQL

SQL cuva podatke u tabelama koja ima predefinisanu DB Schemu. Tabela je entitet.  

NoSQL - Unstructured, Dynamic Schema. Generalno NoSQL formati podataka su key-value, document databases, wide-column databases, graph databases. Redus je key-value.

- Key-value : Redis, Voldemort, Dynamo
- Document db: MongoDB, CouchDB
- Wide-Column db: Cassandra. HBase. Redovi tabele mogu da imaju razlicite kolone. two-dimensional key-value store
- Graph db: Neo4J, Infinite graph
- Full text search engines: Lucene Solr

SQL se skejluje vertikalno, bolji CPU i vise memorije. NoSQL se skejluje horizontalno, sa vise instanci i replikacijom. SQL su ACID friendly, atomacity, Consistency, Isolation transaction, Durability. Document based db je veoma slican key value db ali zahteva da podaci budu sacuvani u poznatom formatu tipa JSON, XML kako bi omogucio query podataka.



# Instalacija

Redis Desktop Manager za Windows -https://docs.redisdesktop.com/en/latest/install/

Redis Desktop Manager za Windows - https://github.com/qishibo/AnotherRedisDesktopManager/releases

## Windows

Po defaultu Redis ne nudi windows, ali postoje nezvanicne distribucije. Websajt: https://github.com/dmajkic/redis/downloads

 Moze da se instalira preko msi ili preko zip verzije. Default port je 6379. Zip verzija ima **redis-server.exe** i **redis-cli.exe**. Prvo se server pokrene pa server pa cli. 

```
ping
set mykey key
get mykey
```

Msi se instalira u program files redis. U windows start ima **sevices** i izlistace redis. Pokreni **cmd** i cd u putanju **C:/Program Files/Redis** i pokreni **redis-cli.exe**

## Linux

```
sudo apt-get install redis-server
sudo systemctl enable redis-server.service
redis-server
redis-cli
```

## Free Could Hosted Redis - Redislabs.com



# Redis Data Types

Redis je key value db. Ima 5 tipova String, Lists, Set, Sorted Sets, Hashes. Za svaki data tip postoji razlicit tip komandi. 

Dokumentacija: https://redis.io/commands

## Strings

Maximalna velicina value za key je 12MB.

### set

 SET key value [EX seconds|PX milliseconds|EXAT timestamp|PXAT milliseconds-timestamp|KEEPTTL] [NX|XX] [GET] 

- NX - set if doesnt exists, XX set if exists
- KEEPTTL retain time to live associated with key
- GET Vrati staru vrednost keya, ili nula ako je postajalo. Error ako data tip nije string

```
SET key "Hello"
SET anotherkey "will expire in minute" EX 60
GET anotherkey

SET student:1 jordan
SET student:2 bojan

keys *
key st*
EXISTS student:4

ttl student 
// vraca preostalo vreme

set testkey testvalue
append testkey " additional "
get testkey
stringlen testkey

set mykey 10
incr mykey
incrby mykey 2
decr mykey
decrby mekey 3
```

### setrange

Da uradi override deo vrednosti kao substring

```
set rangekey rangeone
setrange rangekey 5 two
get setrange
// rangetwo

getrange rangekey 5 8
// two
```

### mset mget

Setovanje i getovanje vise vrednosti

```
mset firstkey firstvalue secondkey secondvalue
get firstkey
mget firstkey secondkey
```



## List

Kolekcija elemenata, sortirana na osnovu dodavanja.

```
lpush tutorials value1 value2 value3
lrange tutorials 0 1

rpush tutorials val4

llin tutorials

lset tutorials 2 "tut2"
lrange tutorials 0 3

lpop // removes and returns first element
rpop // removes and returns last element

ltrim // da filtrira na osnovu indexa
lpush numbers 1 2 3 4
lrange numbers 0 3
ltrim number 1 2
lrange numbers 0 3 // 2 3

// to delete key
del numbers
```

## Set

Svi elementi u kolekciji su jedinstveni

```
sadd emails email1 email2 email3
smembers emails
scard emails // broj elemenata

sadd newemails email3 email4 email5
sdiff emails newemails 

sunion emails newemails

sinter emails and newemails

spop emails // returns and removes random element

srem newemails email5 // removes element from list

smembers newemails
```

## SortedSets

```
 zadd names 10 ten 11 eleven 12 twelfe
 zrange names 0 -1 // istampa sve values
 zrange names 0 -1 withscores // stampa sve value pa key
 zcount names -inf +inf // da izbroji broj elemenata
 
 zrank names eleven // index ove vrednosti 1
 
 zrem names ten
 zrange names 0 -1
```

## Hash

```
hmset student name bojan surname djulbic gender male grade A
hmget student name gender and grade // bojan male nil A
hgetall student
hexists student
hexists student name
hvals student
hkeys student
hdel student grade
hkeys student
hlen student
```

# Redis Advanced 

How to persist data

- no persistence, dataset in memory
- RDB redis database file
- AOF append Only File
- Hybrod (RDB & AOF) for recovery

RDB - je pravljenje snapshota u odredjenim intervalima. u redis.windows.conf ili redis.conf u redis instalacion folderu. RDB je dobar za recovery jer se cuva kao jedan fajl koji se moze enkriptovati i poslati to remote. Postoji mogucnost da se izgube podaci izmedju dva backupa

```
save 900 1 // sacuvaj svakih 900s ako je jedan key promenjen
save 300 10
save 60 10000
```

AOF - loguje svaku operaciju. i bice procitana kada se server pokrene. Moze da fsync svake secunde i svakog querija. Append only je siguran od data korupcije. Velicina AOF fajla je veca i za svaku operaciju pise na disku pa je sporije.

```
appendonly yes
```

Hibrid - Ovo se preporucuje. redis.io/topics/persistence

## How to backup and restore

```
set backup beforebackup
get backup // izlistace sve bekaupe
```

U redis folderu postoji dump.rdb file. Sacuvaj sa strane i obrisi redis instalaciju folder. I onda get backup

## Redis sentinental

Ako imas jednu instancu redis i to kreshuje, cela aplikacija ce da kreshuje. Da se resi problejm mozes da imas slajvove i da oni budu aktivni dok se master ne vrati. Za to postoji i sistem Sentinental - koji radi monitoring, notifikaciju, automatic failover koji automatski unapredi slejv u master. Uloga sentitentala je da zna ko je master i kad ga klijenti pitaju sentinental iz prosledjuje.

Predlaze da imaju 3 instance sentinentala. Ne mora da bude na drugom serveru. Java klijent koji se koristi treba da podrzava sentinental. Sentinental API nudi info o instancama

```
SENTINEL masters
SENTINEL slaves <master name>
```

### Da postane slave

u redis.conf promeni se port. uncoment slaveof **127.0.0.1 6379** i onda se pokrene. rename redis-slave.conf. Otvori redis sentinental

sentinel down-after-miliseconds mymaster 10000.

sentinental monitor mymaster 127.0.0.1 6379 2 // 2 is the amount of sentintent that have to agree

port 26380. napravi dva sentinteal fajla

pokrece se preko redis-server /putanja/do/redis-slave.conf

redis-sentintel /putanja/do/sentintenal

redis-cli -p 26379



# Redis client

Treba klijent za redis, ko drajver za bazu. Ima Jedis, Lettuce, Redisson. Na off strani imaju klijentu za gomilu jezika. Lettuce je dobar za reactive usage i multithread. Redisson za distribuciju i skajluje. Jedis instance is not thread safe but its easy to use. Jedis ima threadsafe varijantu Jedis pool object. Pooling ima odredjen broj komunikaciju otvoreno i kad god neko zatrazi vrati se neki od postojecih veza.

## Start up

init: https://start.spring.io/ dep: Web, Redis

```
POM.XML
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-data-redis</artifactId>
	</dependency>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-data-redis-reactive</artifactId>
	</dependency>
	<dependency>
		<groupId>redis.clients</groupId>
		<artifactId>jedis</artifactId>
		<version>3.6.1</version>
	</dependency>
	<dependency>
		<groupId>org.apache.commons</groupId>
		<artifactId>commons-pool2</artifactId>
		<version>2.0</version>
	</dependency>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
	</dependency>

	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-test</artifactId>
		<scope>test</scope>
	</dependency>
	<dependency>
		<groupId>io.projectreactor</groupId>
		<artifactId>reactor-test</artifactId>
		<scope>test</scope>
	</dependency>
</dependencies>
```

Za komunikaciju sa redisom koristi se Redis Template. U zavisnosti od tipa podatka koriste se razlicite operacije. Podaci u redisu se cuvaju kao bajtovi i potrebni su serijalizajeri da bi se konvertovali u string, json... Postoje default serijalizeri tipa GenericToStringSerializer, JacksonJsonRedisSerializer (default), JdkSerializationRedisSerializer, OxmSerializer, StringRedisSerializer (UTF-8 def).



i cli 
monitor // da logguje sta se desava u redi