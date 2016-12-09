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

SessionStateTests.swift
```
override func setUp(){
  super.setUp()
}

override func tearDown(){
  super.tearDown()
}

func testConcurrentAccess(){
  let asyncQueue = DispatchQueue(label:"asyncQueue", attributes:.concurrent,target:nil)
  let expect = expectation(description:"")
  let MaxIndex =100
  for index in 0..MaxIndex{
    asyncQueue.async{
      SessionState.shared.set(index, forKey:String(index))
    }
  }
  while SessionState.shared.object(forKey: String(MaxIndex)) as? Int != MaxIndex{}
  expect.fulfill()
  waitForExpectations(timeout:10){(error) in
  }
}
```


###3 Thread-safety with GCD
```
//let asyncQueue = DispatchQueue(label:"asyncQueue", attributes:.concurrent,target:nil)
  private let syncQueue = DispatchQueue(label:"serializationQueue")

  public func set( _ value:Any,forKey key:String){
    syncQueue.sync{
      storage[key] = value
    }
  }
  
  public func object( forKey key:String)->Any?{
    syncQueue.sync{
      result =  storage[key] ?? nil
    }
    return result
  }


```


##4. Prototype
###1 Overview
####01:05 Prototype
The prototype pattern creates new objects by copying a prototype object. Use this pattern if initializing an object is expensive.


###2 Cloning Value types
MemoryUtil.swift
```
public struct MemoryUtil{
  public static func address(_ o:UnsafeRawPointer)-> String{
    let address = unsafeBitCast(o, to:Int.self)
    return String(format:"%p")
  }
}
```


prototypewithValuetype.swift
```
public struct Contact:CustomStringConvertible{
  public var firstName:String
  public vat lastName:String
}
var contactPrototype = Contact(firstName:"",lastName:"")
//new var
var contactCopy = contactPrototype
print(MemoryUtil.address(&contactPrototype))
print(MemoryUtil.address(&contactCopy))
```

####02:36
```
public struct Contact:CustomStringConvertible{
  public var firstName:String
  public vat lastName:String
  public var description:String{
    return "\(firstName),\(lastName)"
  }
}
extension Contact:Equatable{
  public static func ==(rhs:Contact,lhs:Contact)->Bool{
    return rhs.lastName == lhs.lastName && rhs.firstName == lhs.firstName
  }
}
```
###3 Copy-on-Write Optimization
```
var numbers = Array<Int>=[12,3]
numbrersCopy = numbers  //same memory
numbersCopy.append(2)  //not same
```

###4 Cloning Reference Types
```
public class Contact{
  public var firstName:String
  public vat lastName:String
  public init(firstName:String,lastName:String){
    self.firstName = firstName
    self.lastName = lastName
  }
}
var contactPrototype = Contact(firstName:"",lastName:"")
//new var
var contactCopy = contactPrototype
//here the memory address are same
print(MemoryUtil.address(&contactPrototype))
print(MemoryUtil.address(&contactCopy))
```
