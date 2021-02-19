### Podział klient / serwer

* Nie ma znaczenia kto robi `bind()` (z kilkoma wyjątkami)

### Format przesyłanych wiadomości

* 0MQ nic nie wie na temat formatu przesyłanych wiadomości **oprócz jej rozmiaru w bajtach**
    * Programista musi zadbać żeby odpowiednio przetworzyć to co klient
* Za każdym razem trzeba rezerwować nowy bufor na wiadomość
* Wiadomości są wysyłane bez `null`-a na końcu (nie jest konieczny bo znany jest ich rozmiar) 

### Typy połączeń

#### 1. Request & Reply

* Klient
  * W pętli: `zmq-send()` oraz `zmq_recv()` - **w tej kolejności**
    * Każdy inny setup, na przykład wysłanie dwóch wiadomości albo rozpoczęcie od `zmq_recv()` zakończy się kodem `-1`
* Serwer
  * W pętli: `zmq_recv()` oraz `zmq-send()` - **w tej kolejności**

* Zarówno `zmq::recv()` jak i `zmq::send()` jest blokujące (program nie przejdzie dalej)
* **Wyjście CTRL + C z serwera i ponowne uruchomienie nie połączy z powrotem klienta**

#### 2. Publisher - Subscriber

* Nie wiadomo dokładnie w którym momencie klient rozpoczyna otrzymać wiadomości
  * **Jeśli uruchomimy najpierw klienta, a później serwer, klient straci "kilka" pierwszych wiadomości** ("handshake")
* Istnieje mechanizm wstrzymania publikowania dopóki wszyscy klienci nie są połączeni (poza oczywistym uśpieniem na początku publishera)


* **Jeśli sieć jest powolna, wiadomości będą się kolejkować u publishera**

* Komunikacja w jedną stronę (server -> client)
* Klient
  * Subskrybuuje jednego lub wielu publisherów `setsockopt(ZMQ_SUBSCRIBE, "", 0)
    * Wiadomości od kilku publisherów będą przychodziły naprzemiennie przez co nie ma możliwości, że jeden zagłuszy pozostałych (za wyjątkiem `epgm` gdzie jest to u klienta)
  * Może zrezygnować z subskrypcji
    *  JAK!?
* Serwer
  * Wywouje tylko `zmq::send()`
  * NIE DA SIĘ wywołać `zmq::recv()`

#### 3. Pipeline

* Setup kiedy np. jeden węzeł rozdziela pomiędzy wiele innych, a te wszystkie przesyłają do jednego



##### Przykłady

### Działający `zmq::poll`

```cpp
#include <iostream>
#include <chrono>
#include <zmq.hpp>
#include <thread>

int main() {
    zmq::context_t context{1};

    zmq::socket_t socket{context, ZMQ_SUB};
    socket.connect("tcp://10.110.0.153:61998");
    socket.setsockopt(ZMQ_SUBSCRIBE, "", 0);

    zmq::pollitem_t poll {static_cast<void*>(socket), 0, ZMQ_POLLIN, 0};

    while (1) {
        zmq::message_t message;
        zmq::poll(&poll, 1, std::chrono::seconds(3));

        if (poll.revents & ZMQ_POLLIN) {
            socket.recv(message, zmq::recv_flags::none);
            auto json = std::string(static_cast<char*>(message.data()), message.size());
            std::cout << json << std::endl;
        }
        else {
            std::cout << "No INFO\n";
        }
    }

    return 0;
}
```