[![Build Status](https://travis-ci.org/SpoonLabs/gumtree-spoon-ast-diff.svg?branch=master)](https://travis-ci.org/SpoonLabs/gumtree-spoon-ast-diff)

Gumtree Spoon AST Diff
======================

Computes the AST difference between two Spoon abstract syntax trees using the Gumtree algorithm.

If you use this, please cite:

[Fine-grained and Accurate Source Code Differencing][paper] (Jean-Rémy Falleri, Floréal Morandat, Xavier Blanc, Matias Martinez, Martin Monperrus), In Proceedings of the International Conference on Automated Software Engineering, 2014.

Difference between plain gumtreediff and gumtree-spoon-ast-diff
----------------------------------------------------------------

What is the main difference between gumtree-spoon-ast-diff and [gumtreediff](https://github.com/GumTreeDiff/gumtree)?

* The tree of gumtree-spoon-ast-diff is carefully designed to provide better AST diffs for Java as opposed to vanilla gumtreediff. Simply compare the AST diffs on your own data.
* The nodes involved in the diff can be pretty-printed through the pointer to the original Spoon nodes (see method `changedNode`, `commonAncestor`, `getSrcNode`)
* The nodes involved in the diff can be further analyzed with [Spoon](https://github.com/INRIA/spoon/)'s API

Usage
-----

The main class is used this way:

```bash
gumtree.spoon.AstComparator <file_1> <file_2>
```

Testing AST differencing
------------------------

gumtree-spoon-ast-diff is heavily tested. The testing of AST tree differencing is quite interesting.

There are cases where the oracle is pretty clear, for instance for the deletion of a node.

```java
// there is only one deletion at line 442
assertEquals(operations.size(), 1);
assertTrue(diff.containsOperation(operations, OperationKind.Delete, "Literal", "\"UTF-8\""));
assertEquals(442, result.changedNode().getPosition().getLine());

```

or for the addition of a single node

```java
assertEquals(operations.size(), 1);
assertTrue(diff.containsOperation(operations, OperationKind.Insert, "Invocation", "append"));

```

However, with the presence of moves, the answer is less clear. For instance, an insert+delete instead of a move is correct, although not optimal. However, in this case, there are still definitive oracles: for instance, you are sure that the change happened within a certain node

```java
// the change happened in System.out.println() at line 334
CtElement ancestor = result.commonAncestor();
assertTrue(ancestor instanceof CtInvocation);
assertEquals("println", ((CtInvocation)ancestor).getExecutable().getSimpleName());
assertEquals(344,ancestor.getPosition().getLine());

``` 

To conclude, for testing AST differencing, there is not always a unique and complete, it is a blend of assertions on:

* the number of changes
* the presence of certain changes
* the location of the change (node type and content)
* the location of the change (line number)

Download
--------

Build your own version (`mvn install`) or download [the latest stable JAR][jar] on [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22gumtree-spoon-ast-diff%22).

Deploy
------

To deploy a new version on Maven Central:

* Deploy from Travis directly (easier, all keys configured in .travis.yml):
  * Update version number (if it ends with "-SNAPSHOT" it is pushed as Snapshot otherwise as release)
  * Commit to branch "deploy" (only this branch deploys to Maven Central)
  * Push to branch "deploy"  (you have to force push)
* Deploy from localhost:
  * Update version number
  * `mvn -Prelease verify deploy:deploy`
  * Commit and push

License
-------

    Copyright 2016 Matias Martinez

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.


 [paper]: http://hal.archives-ouvertes.fr/hal-01054552
 [jar]: https://search.maven.org/remote_content?g=fr.inria.gforge.spoon.labs&a=gumtree-spoon-ast-diff&v=LATEST
