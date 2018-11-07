TARGET  := accesshttp.exe
INCS = -I ../network -I ../utils
LIBS = -L ../utils -lutils -L ../network -lnetwork -Wl,-Bstatic -lev -ljansson -lmpdec -lrdkafka -lhiredis -Wl,-Bdynamic -lm -lz -lssl -lcrypto -lpthread -ldl
include ../makefile.inc
