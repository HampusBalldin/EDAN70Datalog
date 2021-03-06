Datalog Implementation for Project in Computer Science at LTH (EDAN70)
========

## Datalog
[Datalog](https://en.wikipedia.org/wiki/Datalog) is a declarative query language. It enables the derivation of relations with derivation rules expressed as Horn-Clauses.
For an introduction to Datalog see e.g. [Slides EDA045F](http://fileadmin.cs.lth.se/cs/Education/EDA045F/2018/web/slides-06.pdf), or the project report (listed below).

## JFlex
[JFlex](http://jflex.de/) is a lexical analyzer generator and is used to generate the lexical analyzer.

## Beaver
[Beaver](http://beaver.sourceforge.net/) is a LALR(1) parser generator. The parser descriptions are written in EBNF-form and a parser is generated.

## JastAdd
[JastAdd](http://jastadd.org/web/) is a meta-compilation system that supports Reference Attribute Grammars (RAGs). It uses the parser generated from Beaver. In addition it takes an AST-description file as input. The AST-description is used to generate and populate the corresponding classes that represent the dynamic AST.

## JUnit
[JUnit](https://junit.org/junit5/) is used for unit testing.

## Building
[Gradle](https://gradle.org/) is used as a build tool. There is additionally a Makefile to summarize certain common build sequences. 
* Package: ***make jar***
* Test: ***make test***
* Eval: ***make eval EVAL=\<Input File\>***
* Cross-compile to Souffle: ***make souffle EVAL=\<Input File\>***

Enable Debug Printouts by exporting: export DebugMode=true

## Final Release
Current supported Meta-Literals:
* EDB : PredRef x String -- What predicates to input from external database
* OUTPUT : PredRef -- What predicates to output
* ATOM : PredRef   -- All atoms
* PREDS : PredRef  -- All predicates
* TYPEOF : PrefRef x List(Type) -- Declare Types

Additional Supported features:
* NEG -- Stratified Negation
* EQ, NEQ, LT, GT, ... -- Filtering
* BIND -- Bind a variable to the result of an expression (Object Creation)
* Expressions for filtering
* Static type system: Integer, String, PredRef, List
* Left-to-Right Type Inference

The evaluation method is a naive bottom-up iterative fix-point algorithm.

Extended Example: 
```
OUTPUT(x) :- ATOM(x), NEQ('Vars, x).        # Output all atoms except Vars
OUTPUT('OUTPUT).                            # Also output the predicates to output
EDB('Vars, "Vars.csv").                     # Load Var relation from Vars.csv

A(x, y) :- Vars(x), Vars(y). 
Filter(x, y) :- A(x, y),
                LT(x, y).                   # x less than y
B(x, y) :- A(x, y), 
           NOT(Filter(x, y)),               # Remove all tuples in filter
           EQ(x * 2 + y, (7 + 2) * 2 - 11). # Keep tuples that satisfy the equality

C(x, y, z) :- B(x, y),
              BIND(z, 2 * x + y).           # z <- 2 * x + y
```

Finite Automaton Example:
```
# Input Sequence
EDB('Inputs, "Inputs.csv"),         # Input Sequence
EDB('Transition, "Transition.csv"). # Finite State Automaton Schema

# Output all 
OUTPUT(x) :- ATOM(x).

# Declare Types
TYPEOF('Inputs, [Integer # Time of Input
                 String  # Input
                ]).

TYPEOF('Transition, [String # Tail State
                     String # Input
                     String # Output
                     String # Head State
                    ]).

# Start State
State(0, "s1").

# Transition Rule
State(t, s1),
State(t2, s2),
Outputs(t, out) :- Transition(s1, in, out, s2),
                   Inputs(t, in),
                   BIND(t2, t + 1).
```

## Project Report
Included in report/ folder as a .tex-file.


## License
This repository is covered by the license BSD 2-clause, see file LICENSE.md.
