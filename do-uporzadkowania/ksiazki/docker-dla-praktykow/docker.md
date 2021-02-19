# Obrazy

```dockerfile
docker pull <NAME>					// Pobranie obrazu z oficjalnej bazy

docker images -a					// Lista obrazów
docker rmi <IMAGE>					// Usuwanie obrazu

docker save <OBRAZ> <PLIK>.tar		// Zapis obrazu do pliku

docker history <OBRAZ>				// Podgląd warstw
```

# Kontenery

Kontener nie wykorzystuje Hypervisor'a tylko server deamon i jest to niepełnoprawny system, bez kernela.

```dockerfile
docker ps -a						// Lista

docker ps -a -f name=site			// Filtrowanie wyników (te z nazwą site)

ocker container rename site site.old	// Zmiana nazwy

docker rm --force <KONTENER>			// Usuwanie

docker start <KONTENER>			// Uruchomienie
docker stop <KONTENER>			// Zatrzymanie 1
docker kill <KONTENER>			// Zatrzymanie 2

docker exec -it <KONTENER> /bin/bash		// Wejście do terminala kontenera
											// Kontener musi być uruchomiony
											// docker start <NAZWA_KONTENERA>

docker container diff <KONTENER>		// pokazuje zmiany poczynione wzgledem 
										// oryginalnego obrazu
										
docker container export -o <NAZWA>.tar <KONTENER>	// eksport kontenera do pliku

docker cp <HOST>:<KONTENER>	// Skopiowanie pliku do kontenra

exit		// Wyjście z kontenera bez zatrzymywania
```

#### Tworzenie kontenera z obrazu

```dockerfile
docker run -it <OBRAZ> bin/bash				// Stworzenie kontenera z obrazu

docker run -ti <OBRAZ-LUB-WARSTWA> /bin/bash   // Tworzenie kontenera z konkretnej warstwy
```

Flagi dla `docker run`:

```dockerfile
- it					// iteraktywny (bez tego nie można się komunikować przez terminal)
--name <NAZWA>			// nadanie nazwy
--rm					// po zatrzymaniu kontenera jest on usuwany
-d						// uruchomienie w tle
-v <HOST>:<KONTENER>	// podpięcie wolumin
-p 8080:8080			// ustawienie portu do komunikacji
```

#### Tworzenie kontenera z Dockerfile

```dockerfile
docker build -t hello <ŚCIEŻKA_DO_DOCKERFILE>	// Stworzenie obrazu z Dockerfile
```


Flagi dla `docker build`


```dockerfile
-t		// nadanie nazwy
-v		// montowanie woluminu

(domyślnie tworzy się tag latest)
```

#### Sprawdzenie IP kontenera

Tradycyjnie:

```dockerfile
docker exec -ti <KONTENER> sh
>> ip a
```

Szybciej:

```dockerfile
docker inspect <KONTENTER> --format "{{ .NetworkSettings.Network.bridge.IPAddress }}"

// LUB

docker inspect site | jq -r .[].NetworkSettings.Network.bridge.IPAddress
```

# Sieci

* `bridge` - urządzenie warstwy drugiej, które spina różne interfejsy sieciowe
* Można wybrać do którego `bridge`'a wrzucamy sieci danego kontenera

```dockerfile
docker network ls		// wyświetlenie dostępnych sieci
```

# Woluminy

```dockerfile
docker volume ls		
```

# Dockerfile

* Każdy RUN tworzy nową warstwę
* Im więcej warstw, tym większy jest obraz



```dockerfile
FROM centos
RUN echo secret > /sercret  	// WARSTWA 1
RUN ...                     	// WARSTWA 2

// można się dostać do plików w poszczególnych warstwach dlatego
// nie ma sensu podawanie hasłą w 1 warstwie i w kolejnej kasowanie
```



```dockerfile
FROM centos:7
COPY ./event-scanner /bin
COPY ../config.json /bin
```



### Zamontowanie w obrazie ścieżki z hosta

Volumes
1. Do współdzielenia danych pomiędzy kontenerami
2. Do zamontowania lokalnej ścieżki z hosta kontenerze








```docker
# Use the official image as a parent image.
FROM node:current-slim

# Set the working directory.
WORKDIR /usr/src/app

# Copy the file from your host to your current location.
# COPY package.json .

# Run the command inside your image filesystem.
RUN npm install

# Add metadata to the image to describe which port the container is listening on at runtime.
EXPOSE 8080

# Run the specified command within the container.
CMD [ "npm", "start" ]

# Copy the rest of your app's source code from your host to your image filesystem.
COPY . .
```

# Czyszczenie

```dockerfile
docker system prune		// Usuwa wszystko nieużywane (sieci, kontenery, obrazy, chache)
docker image prune		// Usuwa wszystkie nieużywane obrazy
docker container prune	// Usuwa wsystkie zastopowane kontenery
```

# Inne

* Docker bez `sudo`
  * https://docs.docker.com/engine/install/linux-postinstall/

* 