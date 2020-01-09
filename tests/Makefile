promise-test: ../promise.hpp async-test/include/*
	g++ -std=c++11 -O0 -g -fsanitize=address -I.. -Iasync-test/include promise-test.cpp -o ./promise-test
all: promise-test
clean:
	rm -f ./promise-test
run: promise-test
	./promise-test

