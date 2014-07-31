While I was porting some of my existing code to Swift (only for learning) I ran into the issue of waiting for closures to call. After a while I figured it out and this is the result.

My case was this:
- Set up a callback block
- Inside that callback block, expose a block in caller scope
- Run the exposed block when you're done to notify the caller

```Swift
class AsyncThing {
    // set up a optional callback variable that takes a closure with a return of Void
    // that closure takes another (non-optional) closure named "done" which returns Void
    var callback:((done: () -> Void) -> Void)?
    
    // just a dummy method to execute the blocks
    func run() {
        // set up a constant block, this is just for readability
        // it returns Void. this is the inner closure.
        let doneClosure: () -> Void = {
            println("done was called")
        }
        // call our callback with our closure in it
        callback!(doneClosure)
    }
    
}
 
let t = AsyncThing()
 
// setup callback, it takes a closure as argument
t.callback = {
    done in // extract the closure, this can also be typed as "done: () -> Void in".
            // try alt-click on this and see what the compiler gives you!
    println("executing something") // run some dummy code
    done() // call our block when finished
}
 
// run the method that executes the blocks
t.run()
```
