# psdesnpattswiftcreational
##2. Introduction and Prerequisites
###7 UML diagrams
halo diamond: aggregation (has-a) an instrucor has a course
filled diamond: composistion(part-of) a chassis is a part of car. If car is destroyed, chassis is destroyed as well

###8 Sequence Diagrams
rectangle: execution occurrence

####01:12
dash line + arrow

####message to self


##3. Singleton
###2 SessionState: A Naive Approach
(swift guarantees that lazily initialized globals or static properties are thread-safe
```
public class SessionState{
  private var storage = [String:Any]()
  private init(){}
  public static let shared: SessionState = {
    let instance = SessionState()
    return instance
  }()
  
  public func set( _ value:Any,forKey key:String){
    storage[key] = value
  }
  
  public func object( forKey key:String)->Any?{
    return storage[key] ?? nil
  }
}
```
