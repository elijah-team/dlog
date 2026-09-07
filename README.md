# Datalog for Java

Maybe the smallest recursive Datalog system available in Java (jar file size < 150kB).

Does not cover:
- stratificatation, negation
- magic sets or any other advanced improvements

# Usage

Build using [Maven](https://maven.apache.org/): e.g., `mvn clean package`

Maven coordinate:
```
<dependency>
  <groupId>io.github.elijah-team</groupId>
  <artifactId>dlog</artifactId>
  <version>elijah-SNAPSHOT</version>
</dependency>
```

The following program code illustrates the usage of the parser and evaluator. Considering a datalog program, the parser allows to differentiate facts and rules, which are then used to parameterize one of the evaluators (e.g., rules -> NaiveRecursiveEvaluator) and then evaluate the rules for the facts or edbRelations. 

```
  final String program = "tc(X,Y) :- edge(X,Y)." + "tc(X,Y) :- edge(X,Z), tc(Z,Y)." 
  	+ "edge(\"a\",\"b\")." + "edge(\"b\",\"c\")."
	+ "edge(\"c\",\"b\")." + "edge(\"c\",\"d\").";
  final DlogParser parser = new DlogParser();
  parser.parse(program);

  final List<IRule> rules = parser.getRules();
  final Collection<IFacts> edbRelations = parser.getFacts();

  final IEvaluator evaluator = new NaiveRecursiveEvaluator(rules);
  final Collection<IFacts> idbRelations = evaluator.eval(edbRelations);
```

The log output will print the actual datalog plan that is executed, which helps to understand, how the rules are evaluated. For the example datalog program, the following plan is constructed:

```
UnionAll {
  Projection {
    0 1 
    Table {
      edge(_tc0, _tc1)
    }
  }
  Projection {
    0 3 
    Join {
      Z_1 = Z_2 
      Table {
        edge(_tc0, Z_1)
      }
      Table {
        tc(Z_2, _tc1)
      }
    }
  }
}
```

## License

Apache 2.0 license [here](https://github.com/dritter-hd/dlog/blob/master/LICENSE)

old version with upstream stuff: https://github.com/elijah-team/dlog/blob/c7eb515167244a11b71db428234d6148be2bfd0a/README.md