# Docker containers for develop on PHP, Postgre SQL, Oracle

1. Create directory structure

```
# project
#        |
#        +--- docker
#        |
#        +--- src
#
mkdir project
mkdir project/docker
mkdir project/src
```

2. Copy project source into directory *src*

3. Go to directory *project*

```bash
cd project
```

4. Clone repository

```bash
git clone https://github.com/musdevs/docker-php-oracle.git docker
```

5. Initialize environment variables:

```bash
cp .env.example .env
```

6. Start containers:

```bash
docker-compose up -d
```