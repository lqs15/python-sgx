NAME=attestation
SO_NAME=_$(NAME)_swig.so

all: $(SO_NAME)

PROJECT_HOME = ../../..

include $(PROJECT_HOME)/Makefile.config

UTILS = $(PROJECT_HOME)/utils
SWIG_LIB = $(PROJECT_HOME)/lib/swig

INCLUDES = -I$(SDK)/include -I$(SDK_GIT_DIR)/common/inc -I$(SDK_GIT_DIR)/common/inc/internal -I$(SDK_GIT_DIR)/external/crypto_px/include -I$(SDK_GIT_DIR)/sdk/trts/linux -I$(SDK_GIT_DIR)/external/rdrand -I$(SDK_GIT_DIR)/sdk/tseal

$(NAME).i:
	echo "// Generated from $(NAME).i.template\n" > $(NAME).i
	sed -e "s:\$$(NAME):$(NAME):g" $(NAME).i.template >> $(NAME).i
	chmod 444 $(NAME).i

$(NAME)_wrap.c: $(NAME).i
	swig -Wall -python -I$(SDK)/include -I$(SWIG_LIB) $<

$(NAME)_wrap.o: $(NAME)_wrap.c
	gcc -Wall -Werror -fPIC -c $^ -I$(SDK)/include -I$(SWIG_LIB) -I/usr/include/python3.5 -ldl

$(NAME).o: $(NAME).c
	gcc -Wall -Werror -fPIC -c $^ $(INCLUDES) -I/usr/include/python3.5

$(SO_NAME): $(NAME).o $(NAME)_wrap.o
	gcc -Wall -Werror -shared *.o -lsgx_uae_service -o $@ -Wl,--trace-symbol,sgx_rijndael128GCM_encrypt

exec: $(NAME).o
	gcc -g -Wall -Werror $^ \
	    -lsgx_uae_service \
	    -lpython3.5m



.PHONY: clean
clean:
	rm -rf $(NAME).i $(NAME)_wrap.c $(SO_NAME) $(NAME)_swig.py *.o
