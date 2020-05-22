There's a lesson here:
http://www.ilikebigbits.com/2014_04_29_myth_of_ram_3.html

What does this imply for our implementation?  
Is `getByte buffer 0` a random access or is this memory location known?
Aren't we already in trouble when we iterate a buffer by writing `getByte buffer n` for each n?  
As opposed to  ptr = getByte buffer 0  and iterate the ptr? And accessing the memory locations ourselves.  
This bears investigating.



Some thoughts on Bytes representation, I'm very inclined to just move len to Int at this point since the advantage of having a statically providied NonEmpty is unclear.  
```
-- Our Bytes type, a Ptr to a `block` of memory, its size in bytes,
-- and the current 0-based offset into that memory.
-- Nat is technically too big here, it'd be better to have some bounded type.
-- Perhaps it would be best to use Int and provide a NonEmpty in terms of
-- believe_me or So (which seems a little tedious) since I really only use the
-- Nat to prove NonEmpty. The problem is that the compiler isn't going to help
-- us supply NonEmpty then. It'd be really awful to have to `with` anywhere I
-- want NonEmpty to be inferred. Especially since it's just for a few
-- operations. Adding inconvience for a convenience feature isn't choice.
public export
data Bytes : Type where
     MkB : (b : Buffer) -> (pos : Int) -> (len : Nat) -> Bytes

-- Performance: For the purposes of proving NonEmpty, if Nat is compiled to Int
-- then this is as performant as if we didn't use Nat but we gain static size
-- computing. However if it's compiled to Integer then we didn't gain anything
-- over simply having two constructors for Bytes, empty and not. The default
-- for Nat to compile to is Integer but realistically Int is safe since you'll
-- run out of memory before you fill up an Int. That's especially the case once
-- we have Word (uint) types if we compile Nat to those, which is only
-- reasonable since Nat can't be negative anyway.
```