Piotr Plewka 
Grupa 6.7 nr indeksu 97707
Sprawozdanie
Programowanie aplikacji w chmurze obliczeniowej
Laboratorium 10

Utworzenie karty sieciowej mostkowanej
docker network create --driver=bridge --subnet=192.168.10.0/24 lab10net

Zbudowanie obrazu 
docker build -t lab10 -f Dockerfile .            

Uruchomienie 3 kontenerów

docker run -dt --name web1 --network lab10net -p 8001:80 -v /Users/piotrek/Desktop/aplikacjeChmurowe/lab10/web1/index.html:/usr/share/nginx/html/index.html:ro -v /Users/piotrek/Desktop/aplikacjeChmurowe/lab10/web1/logs:/var/log/nginx lab10

docker run -dt --name web2 --network lab10net -p 8002:80 -v /Users/piotrek/Desktop/aplikacjeChmurowe/lab10/web2/index.html:/usr/share/nginx/html/index.html:ro -v /Users/piotrek/Desktop/aplikacjeChmurowe/lab10/web2/logs:/var/log/nginx lab10

docker run -dt --name web3 --network lab10net -p 8003:80 -v /Users/piotrek/Desktop/aplikacjeChmurowe/lab10/web3/index.html:/usr/share/nginx/html/index.html:ro -v /Users/piotrek/Desktop/aplikacjeChmurowe/lab10/web3/logs:/var/log/nginx lab10

Przykładowy odczyt logów serwera
piotrek@netpanel-87-246-221-159 lab10 % cat ./web1/logs/access.log
192.168.65.1 - - [04/Jun/2024:12:03:03 +0000] "GET / HTTP/1.1" 200 297 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36" "-"
192.168.65.1 - - [04/Jun/2024:12:03:04 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8001/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36" "-"
piotrek@netpanel-87-246-221-159 lab10 % cat ./web1/logs/error.log 
2024/06/04 12:01:18 [notice] 1#1: using the "epoll" event method
2024/06/04 12:01:18 [notice] 1#1: nginx/1.25.4
2024/06/04 12:01:18 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
2024/06/04 12:01:18 [notice] 1#1: OS: Linux 6.6.26-linuxkit
2024/06/04 12:01:18 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2024/06/04 12:01:18 [notice] 1#1: start worker processes
2024/06/04 12:01:18 [notice] 1#1: start worker process 29
2024/06/04 12:01:18 [notice] 1#1: start worker process 30
2024/06/04 12:01:18 [notice] 1#1: start worker process 31
2024/06/04 12:01:18 [notice] 1#1: start worker process 32
2024/06/04 12:01:18 [notice] 1#1: start worker process 33
2024/06/04 12:01:18 [notice] 1#1: start worker process 34
2024/06/04 12:01:18 [notice] 1#1: start worker process 35
2024/06/04 12:01:18 [notice] 1#1: start worker process 36
2024/06/04 12:03:04 [error] 29#29: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 192.168.65.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8001", referrer: "http://localhost:8001/"

Inspekcja utworzonych kontenerów

docker network inspect lab10net | jq '.[].Containers'
{
  "2726223086e65e5fcc605f6815285551295726a3ed0cb9fecb9ad1f9d92283ba": {
    "Name": "web3",
    "EndpointID": "51b2177b84d61effc3e91125d9615b9bc11531d104316dc635ac08429a73cf14",
    "MacAddress": "02:42:c0:a8:0a:04",
    "IPv4Address": "192.168.10.4/24",
    "IPv6Address": ""
  },
  "527b927fdd870d55b8091f69018798bdaa085de2148c129f7ff850921c580db0": {
    "Name": "web1",
    "EndpointID": "3cca6cba0d8fff83e49b909edb454d87e9a4ae33669ca4e1b279d5a2ff228b4c",
    "MacAddress": "02:42:c0:a8:0a:02",
    "IPv4Address": "192.168.10.2/24",
    "IPv6Address": ""
  },
  "ce048507aa55198c6658ac501019f7ae1564145d6f1c8c68307bb50789bdbe42": {
    "Name": "web2",
    "EndpointID": "ca3e0f01d42f638c0cb9c3cc25646ecc3eb54100457c499ea7f830c56aac533f",
    "MacAddress": "02:42:c0:a8:0a:03",
    "IPv4Address": "192.168.10.3/24",
    "IPv6Address": ""
  }
}

