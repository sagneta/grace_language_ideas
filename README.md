# grace_language_ideas
Ideas concerning the design of the language of Grace


## Basic Outline
First shot at an outline of the language. The fundamental concepts sans approach. The latter I don't know about.
Fundamental concepts of the Grace language:
* Authentication
* Authorization
* Provide Integrity, Confidentiality, obfuscation and Non-repudiation. (Don't just toss an encryption library at the developer.)
* KeyManagement (Every successful security standard has key management. Conversely, every unsuccessful security standard does not have key management.)
* Immutability be default (You have to explicitly make things writable. No longer unique concept among languages but worth pointing out.)
* Error Handling (All the arguments against non-checked exceptions ignore the fact that developers can't handle non-checked exceptions. Not sure what the answer is here.)
* Taint (We have to do something. Immutability may help a bit.)
* Security Scanner Friendly (The compiler needs to produce something to allow scanners to do their jobs.)
* Library Dependency Management (The newest languages do this but from a security perspective it is now vital.)
* Incorporate HTTP2 and HTTP REST into the language directly. We probably have to do this anyways for Authentication/Authorization etc.
* JSON is the standard serialization mechanism
Secondary concepts:
* Everything else is secondary and negotiable to the above fundamental concepts which is primarily security centric. For once, it's Security first.
* I'm also going against the grain in that compilers can be relatively slow. Honestly if a compiler isn't working hard to produce the code then something is wrong. Quick compilation is as much problem as solution. Doing stuff fast and wrong isn't a great paradigm. As Rand used to say, if you logon fast to a system it's not doing authentication correctly.
* A functional approach is fine and one I prefer, but mostly it doesn't really affect code quality much in terms of security, so we will need to ease off. I don't care about monads and currying.
* Prefer an interface approach rather than classic OOP. Ease off here as well. Simpler is better. I think GO, even Java,  might have this correct in a sense of direction. No inheritance just multiple interface implementations.
* I would like some sort of operator overloading for analytics work. Math exists. We can and probably should restrict it somewhat, but you can't ignore numbers. Looking at you Java...
* Fixed Point (BCD or something) built in. Don't go the BigInteger library route crap like Java. Java is terrible to work with for analytics.
That's all my thoughts for now. I almost think of this language like the SmallTalk 80 approach. When they developed the language they incorporated all sorts of features (A Graphic User Interface! The language of GUI!) etc that wasn't considered before in a language.

## Language Mock-up

```java


// So a class that must be authenticated and requires a number of 
// permissions. The language just works on permissions and not roles. Roles
// are just a list of permissions. That really just something the Idp knows
// about and care. In theory 'authenticated' isn't required because
// permissions assumes authentication

public authenticated class BigMondoClass iintegritymplements IList, ISomethingElse requires permission1, permission2 {
    
    // All methods requires permission1, permission2
    public void method1()...
    public void method2()...
}

public class Another implements ISOmething requires permission1 {
    
    public void method1() requires permission2 // Addition to permission 1 or overrides?
    {}
}

public class Another implements ISomethingelse
{
    public void method1() requires permission1, permission2
}

// Constraints & taint

public class Another implements ISomething
{
    public void method1(int i, string j)
    {
        // Must be the first lines after a method invocation
        constraint i > 0 && i < 32768;
        constraint j not null || not empty;
        constraint j contains "joe";
        
        // stuff
        ....
    }
    
    public void method2 (tainted string j)
    {
        // Wonder what we do with this knowledge
        // Or maybe when we have gRPC HTTP/2 & HTTP1 built-in we
        // Simply can mark these variables (really immutables) 
        // as tainted. 
        
        if is tainted j {}
        
        // Thus, the compiler will prevent j from being passed to
        // methods/functions unless that specifically allowed tainted
        // strings. Thus, we need a process to sanitize strings like a cast.
            
        string x = fubar(j);
    }
    
    public string fubar (tainted string badstring) {
        sanitize {
            // do something to badstring in here 
            return x;
        }
    }
    
    public void method2 (string j)
    {
        confidential string x = j; 
        integral confidential string y = j;
        
        y = "another string"; // Nope immutable 
        
        if check x; // must be confidential
        if check y; // must be confidential and integral
        
        if is confidential x {}
        if is integral y {}
        
        if is authenticated {}
    }
    
    // variables and optional parameters.
    public void method3(int y = 80 ) {
        var int x = 1;
        
        x = 2;
    }
    
}

interface ISomething {
 ... // Like Java/C#
}

permission ReadPortfolio
{
        equivalent "READ_PORTFOLIO";
        equivalent "ReadPortfolio";

// Many default methods like name(), hash(), boolean is(string equivalent)
}

// Error Exceptions at least for constraints and requirements

At the end of each method we have some sort of location area for managing the exceptions should they occur. They are checked in the sense that the compiler warns us if we have no handled all the exceptions. 


public class Another implements ISomethingelse
{
    public void method1(float i) requires permission1, permission2
    {
        constraint i > 0.0
        // stuff
        .....
        .....
        .....
        
        handle permission1, permission2 {integrity
            // exception is the default variable name containing the
        // exception information. If you need to do something different for permissions then use separate handle closures. Probably should be closures? I don't know.
    }
    
    handle constraints {
            // Exception contains the information. Not sure about this one.
        // Which constraint? Do I care? Should I be allowed to do anything?
    }
        
        
    }
}


public class YetAnother implements ISomething {
    
    public void method5() {
        
        requires permission3{
            ....
            ....
            
        }
    }
}



```
