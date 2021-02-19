


----
sprawdzenie co nasluchuje
ps aux 


Mozna sie komunikowac np poprzez Curl
----

DOCKER zmienia firewalla' i dopisuje przekierowania
iptables -t nat -L -n









---
docker run -u 9999:9999 -ti --name aaa --rm <NAZWA_KONT> sh


dobra praktka:
uruchamaiwac kontenre read-only


docker run --read-only -ti --name aaa --rm alpine sh



najczesciej caly katalog jest read only, ale volumin do zmian


-----




---



----


podglad procesow, uzycia procesora itp

docker stats <KONTENRE>



albo

docker top <KONTENER>


----





--------------

# Przygotowanie aplikacji do uruchomienia w kontenerze

portainer.sandbox.dev-http.com/#/stacks



1. Przygotowanie Dockerfile

![Screenshot from 2020-10-20 12-56-15](/home/kawapa/Pictures/Screenshot from 2020-10-20 12-56-15.png)



USER root:root potrzebne do wykonania stepów



CMD - kontener uruchomiony z opcjami -g i daemon ff



2. Commit
3. Push
4. Musi być .gitlab.ci.yml

![Screenshot from 2020-10-20 12-58-50](/home/kawapa/Pictures/Screenshot from 2020-10-20 12-58-50.png)

FAZA BUILD:

* Zbudowano obraz

* Wypchano go do harboura (harbor.dev.int/harbor/sign-in)



FAZA RELEASE:

* pobranie tokena
* wyswietlanie tokena
* za pomoca curla i tokena, majac idenyfikator swarma, posilkujac sie plikiem stack.yml z repo -> robimy deploy stacka Test
  * Stack.yml to konfiguracja usług, stacku aplikacji kontenerowych 
  * Stack ma zapisaną konfirację, jakie usługi są uruchomione
* Stack.yml
  * Labels zawiera m.in pod jaka domena ta apka ma byc dostepna (rule=Host)
  * Pojedyncza usulga site
  * Secrets  

![Screenshot from 2020-10-20 13-06-20](/home/kawapa/Pictures/Screenshot from 2020-10-20 13-06-20.png)



FAZA DESTROY:

* Usunięcie aplikacji (stacka w portainerze)

* Krok manualny, trzeba go wywołać ręcznie
* Niszczenie środowiska z poziomu GitLaba



WYMAGANE:

Zeby wszystko działało GitLab musi potrafić sie komunikować z harbourem i portainerem:

* Hasła do portainera i harboura

* Settings -> CI / CD





---

C++



1. Tylko podczas budowania potrzebne sa zaleznosci
2. Binarka kopiowana jest do kolejnego stage'a
3. ... ktory nie ma wszystkich zaleznosci (goly kontener)



Przekazywanie plikóœ konfiguracyjnych: (config)



```dockerfile
secrets:
	- source ...
	  target: app/report_service/config/config.yaml
```



traefik obsluguje komunikacje





----



docker compose to tak naprawde to konfiguracja stacka w portainerze







kaninko - narzędzie do wysyłania do harboura







