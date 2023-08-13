
/**************************************************************************************************************
 ****************************** REVIEW KOTLIN *****************************************************************
 **************************************************************************************************************/

/**
 * 1. Nullability: In kotlin, object are not allow to carry NULL value, it need to be declare with ? to allow to be null
 * In Kotlin, every data type is non-nullable by default, meaning it cannot hold a null value.
 */
// Shorthand to check null
fun userName(u: Any?): String {
    u?.let {
        println("User name is $u")
        return "User name is $u"
    }
    return "DEFAULT_NAME"
}

/**
 * 2. Function is first class citizen:
 */
// It can pass around to another function
fun add(a: Int, b: Int): Int {
    return a + b
}
fun operateOnNumbers(a: Int, b: Int, operation: (Int, Int) -> Int): Int {
    return operation(a, b)
}
fun mainX() {
    val sumFunction = ::add
    val result = operateOnNumbers(10, 5, sumFunction)
    println("Result: $result") // Output: Result: 15
}

// Inline function, when declare as inline, the compiler replace the function call with actual function body content, so in runtime
// It does not cause additional call stack
inline fun doubleValue(value: Int): Int {
    return value * 2
}

// It kotlin we can extend behaviour of object without have to inherit it by using extend function
fun Int.isEven(): Boolean {
    return this % 2 == 0
}

// Function in kotlin can also have name argument and default value so that we can decrease number of overload

/**
 * Scope function: with the purpose of apply some code to object
 * - let
 * - apply
 * - with
 * - run
 */

// Let is shorthand for check null
val nullableValue: String? = "Hello, World!"
var result = nullableValue?.let {
    it.length
} ?: 0

// [Run] is shorthand for check null and access the object
val resultX = DataPoint2D(1, 2).run {
    this.dist += 5 // Accessing and modifying the 'dist' property directly
    this // this is require
}

// [With] is similar to Run but just require the object reference
val resultZ = with(nullableValue) {
    println("Do some thing")
}

// [Apply] use to apply setter to object
class Configuration {
    var timeout: Int = 5000
    var maxRetries: Int = 3
    var logLevel: String = "INFO"
}
val config = Configuration().apply {
    timeout = 10000
    maxRetries = 5
    logLevel = "DEBUG"
}

// [Also] similar to run but do not modify the object 
val also = resultX.also {
    println("Print some logging")
}

// 
// xmpp chat 
// 

/**
 * 3. Class in kotlin
 */
// They are final by default, we have to declare "open" to inherit them
// Declare class as Data automatically generate equal, hashCode, toString, ....
// Sealed Classes, restrict only a class from the same file can extend seal class
// Extend function: class behaviour can be extend without inherit it
// Properties can be associate with adjacent set and get function
// Smart casting 
fun smartCast(a: MyDate) {
    if (a !is MyDate) return
    println(a.year) // In here a is guarantee is MyDate
}
// Accessor contain of internal, private, public, protected
// Inline class: a special class that wrap around primitive, and add extra overhead
// Define an inline class representing Length in meters
inline class Length(val value: Double) {
    // Additional methods or properties can be added to the inline class
    fun toFeet(): Double {
        return value * 3.28084
    }
}

/**
 * 4. Primitive in kotlin
 */
// Primitive can be null if we declare ?
// There is no Boxing object in Kotlin, they behave the same


/**
 * 5. Collection
 */

// Kotlin support range
// Kotlin support sequence where the collection is lazy load, suitable for operation like find first, ... and don't add additional overhead.
// Kotlin's collection have some new operation:
// associate: return a map,
// zip, return compress pair,
// window, return a nested array
// groupingBy, return map and value is a list
// partitionTo, return two list


/**
 * 6. ASYNC in Kotlin
 */

// Co routine using Continuation Passing Style (CPS)
// When a suspend function run and encounter a Suspension Points, it suspends its execution, free the thread,
// pass the context to the children and notify when that operations are completed

fun fetchSomeData() = runBlocking {
    val result = withContext(Dispatchers.IO) {
        "Data fetched from the network"
    }

    println("Result: $result")
}

fun main() {
    fetchSomeData()
}

/**
 * 7. Generic in out where
 */

// this function produce only T and its supertype (similar to super in java)
interface Source<out T> {
    fun nextT(): T
}
fun demo(strs: Source<String>) {
    val objects: Source<Any> = strs
}


// this function consume T and its subclass (similar to extend in java)
interface Comparable<in T> {
    operator fun compareTo(other: T): Int
}
fun demo(x: Comparable<Number>) {
    x.compareTo(1.0)
    val y: Comparable<Double> = x // OK!
}

/**
 * 8. MISC
 */

// Properties can be lazy in classes
// Classes can implement invoke to be called
// Destructuring declarations
// Type aliases


// PRACTICE:
// JSON and XML Ser and Der in Kotlin with strategy pattern.