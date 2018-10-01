# What is scilla

Scilla, short for Smart Contract Intermediate-Level Language, is an intermediate-level smart contract language being developed for Zilliqa. Scilla has been designed as a principled language with smart contract safety in mind.
‍

## Scilla by Example

Here is a code snippet of Scilla smart contract:

```
(***************************************************)
(*               Associated library                *)
(***************************************************)
library HelloWorld

let one_msg = 
  fun (msg : Message) => 
  let nil_msg = Nil {Message} in
  Cons {Message} msg nil_msg

let not_owner_code = Int32 1
let set_hello_code = Int32 2

(***************************************************)
(*             The contract definition             *)
(***************************************************)

contract HelloWorld
(owner: ByStr20)

field welcome_msg : String = ""

transition setHello (msg : String)
  is_owner = builtin eq owner _sender;
  match is_owner with
  | False =>
    msg = {_tag : "Main"; _recipient : _sender; _amount : Uint128 0; code : not_owner_code};
    msgs = one_msg msg;
    send msgs
  | True =>
    welcome_msg := msg;
    msg = {_tag : "Main"; _recipient : _sender; _amount : Uint128 0; code : set_hello_code};
    msgs = one_msg msg;
    send msgs
  end
end


transition getHello ()
    r <- welcome_msg;
    msg = {_tag : "Main"; _recipient : _sender; _amount : Uint128 0; msg : r};
    msgs = one_msg msg;
    send msgs
end
```
### Library

A contract may start with some helper libraries that declare purely functional (with no state manipulation) components of a contract. A library is declared in the preamble of a contract using the keyword library followed by the name of the library.

```js
library HelloWorld

let one_msg = 
  fun (msg : Message) => 
  let nil_msg = Nil {Message} in
  Cons {Message} msg nil_msg

let not_owner_code = Int32 1
let set_hello_code = Int32 2
```

In this example, it declares a library named HelloWorld including of the definition of the error codes and a utility function.

Definition of the error codes is given via using standard `let x = y` construct.
```js
let not_owner_code = Int32 1
let set_hello_code = Int32 2
```
- `let x = f` : Give f the name x in the contract. The binding of x to f is global and extends to the end of the contract which means it will be a global variable.

Utility function is declared with `fun (x : T) => e`.
```js
let one_msg = 
  fun (msg : Message) => 
  let nil_msg = Nil {Message} in
  Cons {Message} msg nil_msg
```
- `fun (x : T) => e` : A function that takes an input x of type T and returns the value to which expression e evaluates.

- `msg`: Scilla defines a special type Message for outgoing messages.

- `let x = f in e` : Give f the name x within expression e. The binding of x to f within e here is local and hence limited to e. The above example concates an empty list of entries of type `Message` with the input argument `msg`:
	- `Nil` creates an empty List. It takes the following form: Nil {'A}, and creates an empty list of entries of type 'A.

	- `Cons` adds an element to an existing list. It takes the following form: Cons {'A} h l, where 'A is a type variable that can be instantiated with any type and h is an element of type 'A that is inserted at the head of list l (of type List 'A).


### Defining Contract and its (Im)Mutable Fields

A contract is declared using the contract keyword that starts the scope of the contract. The keyword is followed by the name of the contract which will be HelloWorld in our example. So, the following code fragment declares a HelloWorld contract.

```
contract HelloWorld
```

A contract declaration is followed by the declaration of its `immutable` variables, the scope of which is defined by (). Each immutable variable is declared in the following way: vname: vtype, where vname is the variable name and vtype is the variable type. 

```js
(owner: ByStr20)
```

The above code snippet declares an immutable variable named owner and its type is ByStr20 which means  hexadecimal Byte String of 20 bytes (40 hexadecimal characters) prefixed with 0x.

You can declare many immutable variables through separated declarations by `,` within brackets.
```js
(vname1 : type1,
 vname2 : type2,
  ...  )
```

Here is a brief introduction of Scilla primitive data types:

- `IntX/UintX` : Signed/unsigned integer types of X bytes where X can be `32`, `64` and `128`.
- `String` :  expressed with a sequence of characters enclosed in `double quotes`.
- `ByStr32` : A `hash` in Scilla is declared using the data type `ByStr32`. A ByStr32 represents a hexadecimal Byte String of `32` bytes (64 hexadecimal characters) prefixed with 0x.
- `ByStr20` : `Addresses` are declared using the data type ByStr20 data type.
- `Map` : it provides key-value store. Keys can have types `IntX`, `UintX`, `String`, `ByStr32` or `ByStr20`. Values can be of any type.
- `BNum` : It's  a dedicated type of `Block numbers` in Scilla. A `BNum` literal is a sequence of digits with the keyword `block` prefixed (example `block 101`).

### Mutable Fields

Mutable variables in a contract are declared through keyword `field`. Each mutable variable is declared in the following way: `field vname : vtype = init_val`, where vname is the variable name, vtype is its type and init_val is the initial value.

```js
field welcome_msg : String = ""
```
The above example declares a mutable variable named welcome_msg of type `String` initialized to `""`.

### Defining Interfaces aka Transitions

Interfaces like `setHello` are referred to as transitions in Scilla. Transitions are similar to functions or methods in other languages.
A transition is declared using the keyword `transition`. The end of a transition scope is declared using the keyword `end`. The transition keyword is followed by the transition name, then follows the input parameters separated by `,` within `()`.

```js
transition setHello (msg : String)
  is_owner = builtin eq owner _sender;
  match is_owner with
  | False =>
    msg = {_tag : "Main"; _recipient : _sender; _amount : Uint128 0; code : not_owner_code};
    msgs = one_msg msg;
    send msgs
  | True =>
    welcome_msg := msg;
    msg = {_tag : "Main"; _recipient : _sender; _amount : Uint128 0; code : set_hello_code};
    msgs = one_msg msg;
    send msgs
  end
end
```
- `_sender` : Scilla internally defines some variables that have special semantics. These special variables are often prefixed by `_`. `_sender` in Scilla refers to the account address that called the current contract.
- `builtin f x` : Apply the builtin function f on x. `builtin eq owner _sender` means the caller of the transition is checked against the owner, it will return a boolean value `True/False`.
- `match` : Depending on the output of the comparison, the transition takes a different path declared via pattern matching. 
	```js
	match expr with
	| x => expr_1
	| y => expr_2
	end
	```
	The above code checks whether expr evaluates to x or y. If expr evaluates to x, then the next expression to be evaluated will be expr_1, else if it evaluates to y, then, the next expression to be evaluated will be expr_2. 
- `send` : Sending a message out is done using the `send` instruction.
- `one_msg` : it's a utility function we just mentioned above.

```js
transition getHello ()
    r <- welcome_msg;
    msg = {_tag : "Main"; _recipient : _sender; _amount : Uint128 0; msg : r};
    msgs = one_msg msg;
    send msgs
end
```
- `<-` : Reading from a mutable variable is done via the operator `<-`. In our example, this translates to r <- welcome_msg.

## Reference
[Scilla-doc](https://scilla.readthedocs.io/en/latest/index.html)





