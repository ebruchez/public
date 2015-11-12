I found myself recently looking at the following code which I wrote when I was getting started with Scala:

```scala
def getModelState(modelPrefixedId: String) =
  modelStates.get(modelPrefixedId) match {
    case Some(modelState) ⇒ modelState
    case None ⇒
      val modelState = new ModelState(modelPrefixedId)
      modelStates += modelPrefixedId → modelState
      modelState
  }
```
This is typically logic you would write in Java, and it looks great in some ways: it uses pattern matching, the tuple arrow (`→`), etc. But it turns out that Scala collections already provide the `getOrElseUpdate` method on mutable maps. The 8 lines above translate simply into:

```scala
def getModelState(modelPrefixedId: String) =
  modelStates.getOrElseUpdate(modelPrefixedId, new ModelState(modelPrefixedId))
```

Morality: know your collections!