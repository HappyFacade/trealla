Trealla ProLog
==============

A compact, efficient Prolog interpreter with ISO compliant aspirations.
Written in plain-old C.

	Integers are 64-bit (optionally 128-bit)
	Reals are double
	Rationals are a native type
	Atoms are UTF-8 of unlimited length
	Arity limit 255
	Full-term indexing
	Peristence to per-module database
	REPL with history
	MIT licensed


Building
========

	git clone https://github.com/trealla-prolog/trealla.git
	cd trealla
	make
	make test

There are no dependencies except OpenSSL, which can removed by:

	make nossl

Run...

	make debug
	make valgrind

to do the tests under valgrind.

A 'make debug' build compiles in 0.17s with TCC, 1.8s with CLANG & 2.1s
with GCC. Should build on any Unix-like system with a C99 compiler.
Tested on Manjaro, Ubuntu 20.04, Raspbian & FreeBSD 12.1 systems.


Usage
=====

	tpl [options] [-l file] [-g goal]

where options can be:

	-O0, --noopt       - no optimization
	-t, --trace        - trace
	-q, --quiet        - quiet mode (no banner)
	-v, --version      - version
	-h, --help         - help
	-d, --daemonize    - daemonize
	-w, --watchdog     - create watchdog
	--stats            - print stats
	--iso-only         - ISO-only mode
	--consult          - consult from STDIN

For example:

	./tpl -l samples/sieve -g test2,halt

Invocation without any goal presents the REPL.


What's missing?
===============

	there may be missing or incomplete ISO predicates
	database is immediate update view, fix
	modules need more work
	missing directives?


GNU-Prolog & SWI-Prolog
=======================

	between/3
	forall/2
	msort/2
	read_term_from_atom/3
	format/1-3
	predicate_property/2
	numbervars/1,3-4
	e/0
	sleep/1
	name/2
	maplist/1-4
	tab/1-2
	random/1                # random(-float) float [0.0,<1.0]
	random/1                # random(+integer) function returning integer [0,<integer]


GNU-Prolog
==========

	write_term_to_atom/3


SWI-Prolog
==========

	setup_call_cleanup/3
	findall/4
	term_to_atom/2
	atom_number/2
	atomic_concat/3
	format(atom(A),...)
	var_number/2
	ignore/1
	is_list/1
	term_hash/2
	writeln/1
	time/1
	inf/0
	nan/0
	\uXXXX and \UXXXXXXXX quoted character escapes
	volatile/1
	rational/1
	rationalize/1
	rdiv/2
	char_type/2
	code_type/2
	string_upper/2
	string_lower/2
	getenv/2
	setenv/2
	unsetenv/1
	uuid/1                       # generates non-standard UUID string

	delete_file/1
	exists_file/1
	rename_file/2
	time_file/2
	exists_directory/1
	make_directory/1
	working_directory/2
	chdir/1

	current_key/1
	recorda/2-3
	recordz/2-3
	recorded/2-3
	instance/2
	asserta/2
	assertz/2
	clause/3
	erase/1

	http_get/3
	http_post/4
	http_put/4
	http_delete/3
	http_open/3


Others
======

	atom_hex/2              # unify atom (in hex) with number
	atom_octal/2            # unify atom (in octal) with number
	log10/1                 # function returning log10 of arg
	now/0                   # function returning C-time in secs as integer
	now/1                   # now (-integer) C-time in secs as integer
	get_time/1              # get_time(-var) C-time in secs as float
	srandom/1               # seed(+integer) seed random number generator
	rand/0                  # function returning integer [0,RAND_MAX]
	rand/1                  # integer(-integer) integer [0,RAND_MAX]
	delay/1                 # delay(+integer) sleep for ms
	loadfile/2              # loadfile(+filename,-atom)
	savefile/2              # savefile(+filename,+atom)
	getfile/2               # getfile(+filename,-list)
	getline/1               # getline(-atom)
	getline/2               # getline(+stream,-atom)
	bread/3                 # bread(+stream,?len,-blob)
	bwrite/2                # bwrite(+stream,+blob)
	replace/4               # replace(+atom,+old,+new,-atom)
	split/3                 # split(+atom,+sep,?list)
	split/4                 # split(+atom,+sep,?left,?right)
	sys_queue/1             # push (append) term to queue
	sys_list/1              # pop list of all queued items
	base64/2                # base64(?decoded,?encoded)
	urlenc/2                # urlenc(?decoded,?encoded)
	sha1/2                  # sha1(?atom,?hash)        NEEDS OPENSSL
	sha256/2                # sha256(?atom,?hash)      NEEDS OPENSSL
	sha512/2                # sha512(?atom,?hash)      NEEDS OPENSSL

	persist/1               # directive 'persist funct/arity'

	dict:set/4              # set(+dict,+name,+value,-dict)
	dict:del/3              # del(+dict,+name,-dict)
	dict:get/3              # get(+dict,+name,-value)
	dict:get/4              # defget(+dict,+name,-value,+default)


Networking          ##EXPERIMENTAL##
==========

	server/3                # server(+host,-stream)
	server/3                # server(+host,-stream,+list)
	accept/2                # accept(+stream,-stream)
	client/4                # client(+url,-host,-path,-stream)
	client/5                # client(+url,-host,-path,-stream,+list)

The options list can include *udp(bool)* (default is false), *nodelay(bool)* (default
is true) and *ssl(bool)* (default is false).

The optional schemes 'http://' (the default) and 'https://' can be provided in the client URL.

With *bread/3* the 'len' arg can be an integer > 0 meaning return that many
bytes, = 0 meaning return what is there (if non-blocking) or a var meaning
return all bytes until end end of file,


Persistence			##EXPERIMENTAL##
===========

Declaring something dynamic with the *persist* directive causes that
clause to be saved to a per-module database on update (assert/retract).


Coroutines          ##EXPERIMENTAL##
==========

Trealla is single-threaded but cooperative multitasking is available
in the form of coroutines that run until they yield control, either
explicitly or implicitly when waiting on input or a timer...

	fork/0                  # parent fails, child continues
	spawn/1                 # spawn(+callable)
	spawn/2-3               # spawn(+callable,+term,...)
	yield/0                 # voluntarily yield control
	wait/0                  # parent should wait for children to finish
	await/0                 # parent should wait for a message
	send/1                  # send(+term) send term to parent queue
	recv/1                  # recv(-term) pop term from queue

Note: *send/1*, *sleep/1* and *delay/1* do implied yields. As does *getline/2*,
*bread/3*, *bwrite/2* and *accept/2*.

Note: *spawn/1-2* are the concurrent forms of *call/1-2*.

An example:

	geturl(Url) :-
		http_get(Url,_Data,[status_code(Code),final_url(Location)]),
		format('Job [~w] ~w ==> ~w done~n',[Url,Code,Location]).

	% Fetch each URL in list sequentially...

	test54 :-
		L = ['www.google.com','www.bing.com','www.duckduckgo.com'],
		maplist(geturl,L),
		writeln('Finished').

	% Fetch each URL in list concurrently...

	test55 :-
		L = ['www.google.com','www.bing.com','duckduckgo.com'],
		maplist(spawn(geturl),L),
		wait, writeln('Finished').

	$ ./tpl -l samples/test -g "time(test54),halt"
	Job [www.google.com] 200 ==> www.google.com done
	Job [www.bing.com] 200 ==> www.bing.com done
	Job [www.duckduckgo.com] 200 ==> https://duckduckgo.com done
	Finished
	Time elapsed 0.702 secs
	$ ./tpl -l samples/test -g "time(test55),halt"
	Job [www.duckduckgo.com] 200 ==> https://duckduckgo.com done
	Job [www.bing.com] 200 ==> www.bing.com done
	Job [www.google.com] 200 ==> www.google.com done
	Finished
	Time elapsed 0.229 secs


Rationals
=========

Rationals are a native type, with integers just a special case where
the denominator happens to be 1. Rationals can be specified using
the *rdiv/2* operator or by *1r3* style notation:

	?- X is 1 / 7.
	X = 0.1428571428571428
	yes
	?- X is 1 / 7, Y is rationalize(X).
	X = 0.1428571428571428
	Y = 1r7
	yes
	yes
	?- X = 1 rdiv 7.
	X = 1 rdiv 7.
	?- X is 1 rdiv 7.
	X = 1r7
	?- X is 1 rdiv 1.
	X = 1
	?-


Performance
===========

Elapsed time in seconds, smaller is better. Indicative only...

	------------|---------|-----------|-----------|---------|----------
	            |   tpl   |   swipl   |  gprolog  |   yap   |  scryer
	            |         |   8.2.1   |    1.45   |   6.5   |  0.8.127
	------------|---------|-----------|-----------|---------|----------
	sieve(5)    |   0.38  |   0.27    |   0.52    |   0.31  |   7.63
	fib(30)     |   0.57  |   0.30    |   0.56    |   0.79  |   6.74
	hanoiq(22)  |   1.19  |   0.39    |   1.18    |   0.85  |   9.9
	queens11    |   1.33  |   0.88    |   1.40    |   1.46  |  10.9
	puzzle      |   0.57  |   0.17    |   0.28    |   0.22  |
	chess       |  11.7   |   4.95    |   4.95    |   4.88  |
	------------|---------|-----------|-----------|---------|----------
	testindex1a |   1.36  |   1.01    |   0.70    |   4.94  |
	testindex1b |   1.56  |   1.03    |   >300    |   5.10  |
	testindex5  |  10.6   |   7.7     |   4.23    |  49.7   |
	------------|---------|-----------|-----------|---------|----------

	tpl -l samples/sieve.pro -g "time(test5),halt"
	tpl -l samples/fib.pro -g "time(test),halt"
	tpl -l samples/hanoi.pro -g "time(hanoiq(22)),halt"
	tpl -l samples/queens11.pro -g "time(testq),halt"
	tpl -l samples/puzzle.pro -g "time(main),halt"
	tpl -l samples/chess.pro -g "time(main),halt"
	tpl -l samples/testindex.pro -g "time(test1a),halt"
	tpl -l samples/testindex.pro -g "time(test1b),halt"
	tpl -l samples/testindex.pro -g "time(test5),halt"

	swipl -l samples/sieve.pro -g "time(test5),halt"
	etc

	yap -l samples/sieve.pro -g "time(test5),halt" -s128000
	etc

	export setenv LOCALSZ=256000
	export setenv GLOBALSZ=128000
	time gprolog --consult-file samples/sieve.pro --query-goal "test5,halt"
	etc

	time scryer-prolog samples/sieve.pro -g "test5,halt"
	etc

Times for gprolog & scryer were done using the unix *time* command and
thus include setup time, whereas the others were done with the predicate
*time(+Goal)*.

Also, gprolog only seems to implement 1st argument indexing (hence very
slow *testindex1b* result) Also 2 internal stacks needed to be boosted.

Yap came from *git clone https://github.com/vscosta/yap-6.3* and needs
*cmake* installed.

Scryer came from *cargo install scryer-prolog* (it takes a long time)
and needs *m4* installed. It seems puzzle won't compile, and chess
needs name/2 (at least). Also *testindex* needs between/3 so won't
load, is it in a module?
