#
# PolyGen
# src-level makefile
#
# Alvise Spano' (2002-03)
#

NAME       := polygen

VERSION    := $(shell cat <VERSION)
DATE       := $(shell cat <DATE)

# must be linked in this order
SRCS	   :=	ver.ml	        \
		prelude.ml	\
		err.ml		\
		absyn.ml	\
		env.ml		\
		check.ml	\
		pre.ml		\
		gen.ml		\
		fake.ml		\
		parser.ml	\
		lexer.ml	\
		io.ml		\
		main.ml

ifeq ($(NATIVE), false)
MLC		= ocamlc
MLCOPTS		= -unsafe
MLLDOPTS	= unix.cma
OBJS		:= $(SRCS:.ml=.cmo)
else
MLC		= ocamlopt
MLCOPTS		= -inline 23 -unsafe
MLLDOPTS	= unix.cmxa
OBJS		:= $(SRCS:.ml=.cmx)
endif

MLDEP	    = ocamldep -native
MLLEX		= ocamllex
MLYACC		= ocamlyacc



all: $(NAME)

$(NAME): $(OBJS)
	$(MLC) $(MLLDOPTS) $(OBJS) -o $@

%.cmi: %.mli
	$(MLC) -c $(MLCOPTS) $< -o $@

%.cmo: %.ml
	$(MLC) -c $(MLCOPTS) $< -o $@

%.cmx: %.ml
	$(MLC) -c $(MLCOPTS) $< -o $@

parser.mli parser.ml lexer.ml: lexer.mll parser.mly
	$(MLLEX) lexer.mll
	$(MLYACC) parser.mly
	# workaround for ocamlyacc "open" lack
	(echo "open Absyn" ; echo ; cat parser.mli) > _parser.mli
	@mv _parser.mli parser.mli

ver.ml: ver.ml.template
	sed -e "s/%VERSION%/$(VERSION)/" -e "s/%DATE%/$(DATE)/" ver.ml.template >ver.ml

clean:
	rm -f *.o *.cm* a.out *.bak *.obj *~ *.annot *.stackdump parser.ml lexer.ml parser.mli ver.ml
	cat </dev/null >.depend

distclean: clean
	rm -f $(NAME) $(NAME).exe

.depend: parser.mli
	$(MLDEP) $(SRCS) parser.mli >.depend


include .depend
