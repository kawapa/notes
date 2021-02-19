compile: *.cpp
	g++ server.cpp -o server -std=c++17 -lzmq
	g++ client.cpp -o client -std=c++17 -lzmq

clean:
	rm server
	rm client
# 
# .PHONY: clean