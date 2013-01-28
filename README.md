gremlin-scala
=============

![logo](https://github.com/mpollmeier/gremlin-scala/raw/master/doc/images/gremlin-scala-logo.png)

Gremlin is a domain specific language for traversing a number of graph databases including:
* Graph database connectivity:
** "TinkerGraph":https://github.com/tinkerpop/blueprints/wiki/TinkerGraph in-memory graph
** "Neo4j":http://neo4j.org/ graph database
** "OrientDB":http://www.orientechnologies.com/ graph database
** "DEX":http://www.sparsity-technologies.com/dex graph database
** "InfiniteGraph":http://www.infinitegraph.com/ graph database
** "Titan":http://thinkaurelius.github.com/titan/ graph database
** "Rexster":http://rexster.tinkerpop.com graph server
** "Sesame 2.0":http://www.openrdf.org compliant RDF stores
* Supporting extensions:
** "GraphML Reader/Writer":http://graphml.graphdrawing.org/ library
** "GraphSON Reader/Writer":http://www.json.org/ library
** "GML Reader/Writer":http://en.wikipedia.org/wiki/Graph_Modelling_Language library
** "Java Universal/Graph":http://jung.sourceforge.net/ framework

Gremlin-Scala is just a thin wrapper for Gremlin so that it feels natural in Scala:
```scala
  // this is an executable ScalaTest specification - see SampleUsageTest.scala for full setup
  describe("Gremlin-Scala") {
    val graph = TinkerGraphFactory.createTinkerGraph
    def vertices = graph.V
  
    it("finds all vertices") {
      vertices.count should be(6)
      vertices.map.toList.toString should be(
        "[{name=lop, lang=java}, {name=vadas, age=27}, {name=marko, age=29}, " +
          "{name=peter, age=35}, {name=ripple, lang=java}, {name=josh, age=32}]"
      )
    }

    it("can get a specific vertex by id and get it's properties") {
      val marko = graph.v(1)
      marko("name") should be("marko")
      marko("age") should be(29)
    }

    it("finds everybody who is over 30 years old") {
      vertices.filter { v: Vertex ⇒
        v.as[Int]("age") match {
          case Some(age) if age > 30 ⇒ true
          case _                     ⇒ false
        }
      }.map.toList.toString should be(
        "[{name=peter, age=35}, {name=josh, age=32}]")
    }

    it("finds who marko knows") {
      val marko = graph.v(1)
      marko.out("knows")(_("name"))
        .toList.toString should be("[vadas, josh]")
    }

    it("finds who marko knows if a given edge property `weight` is > 0.8") {
      val marko = graph.v(1)
      marko.outE("knows").filter { e: Edge ⇒
        e.as[Float]("weight") match {
          case Some(weight) if weight > 0.8 ⇒ true
          case _                            ⇒ false
        }
      }.inV.map.toList.toString should be("[{name=josh, age=32}]")
    }
  }
```

Getting started
=============
```shell
git clone https://github.com/mpollmeier/gremlin-scala.git
mvn test      #run all tests - should find all dependencies and run the tests fine
mvn install   #install into your local maven repository so that you can use it (groupId=com.tinkerpop.gremlin,
artifactId=gremlin-scala
```

Known Issues
=============
** Console doesn't seem to work at the moment. Just use it in a test (see SampleUSageTest for some examples)

For details about Gremlin see the excellent "wiki":https://github.com/tinkerpop/gremlin/wiki

Kudos go to [Zach Cox](http://theza.ch) for starting this project and the whole [Tinkerpop](http://www.tinkerpop.com) team for creating a whole awesome stack around graph databases.
