test:test.o
	g++ -o test test.o `pkg-config --cflags --libs opencv`
test.o:test.cpp
	g++ -c -o test.o test.cpp
clean:
	g++ -f test test.o	
