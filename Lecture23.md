# CS 246, Lecture 23
###### 2022 July 19

## Recall
```
class C{...};
unique_ptr<c> p{ new C{...}};
unique_ptr<c> q = p; // Error
```
class `unique_ptr<T>` has no copy operations, it's meant to be unique, so a shallow copy of the pointer is disallowed. They can be moved from, since that signifies the transfer of ownership.
```
unique_ptr<C> a{new C{...}};
unique_ptr<C> b{make_unique<C>{&a}};
int *p = new int{5};
if(...) {
    unique_ptr<int> q{p};
}
*p = 10; //Dangling
```
```
int *p = nullptr;
if(...) {
    unique_ptr<int> q = make_unique<int>(5);
    p = q.get();
}
*p = 10; // Dangling
```
If however, there is true shared ownership - i.e. any of several pointers may need to free the data, you can use `std::shared_ptr`.
```
{
    //shared_ptr<MyClass>
    auto p1 = std::make_shared<MyClass>();
    // allocates space for MyClass object, p1 points at it
    if(...) {
        auto p2 = p1; // copy constructor
    } // p2 is popped, it doesn't free the memory it points at but it decrements the reference count
    auto pe = std::make_shared<MyClass>(*p1);
}
```
`shared_ptr`s maintain a reference count, a count of all `shared_ptr`s pointing at the same object. The memory is freed when the reference count reaches zero.
```
{
    int *p = new int{5};
    shared_ptr<int> sp1{p};
    if(...) {
        shared_ptr<int> sp2{p};
    }
} // double free
```
Use the pointer type that accurately reflects the ownership role. Dramatically fewer opportunities for leaks. 

There are three levels of exception safety for some function `f`,
- Basic Guarantee: if this function throws or propagates an exception, the program will be in a valid but unspecified state. Nothing is leaked, and class invariants are maintained.
- Strong Guarantee: if this function throws or propagates an exception, the program will be as if the function was never called.
- No-throw Guarantee: this function will never throw an exception and will always complete its task.

```
class A {...}; // A::g offers strong guarantee
class B {...}; // B::h offers strong guarantee
class C {
    A a;
    B b;
  public:
    void f() {
        a.g();
        b.h();
    }
};
```
Is `C::f` exception-safe? 
- If `a.g()` throws, nothing happened yet, so we're OK
- If `b.h()` throws, the effects of `a.g()` must be undone to offer the strong guarantee. This can be very hard or impossible if `a.g()` has non-local side effects (e.g. if it calls `cout`).

If `A::g` and `B::h` do not have non-local side effects, we can use the copy-and-swap idiom:
```
class C{
    A a; B b;
  public:
    void F() {
        A atemp{a};
        B btemp{b};
        atemp.g();
        btemp.h();
        // If either of these throw, then nothing has changed.
        a = atemp;
        b = btemp;
    }
};
```
Because copy assignment could throw, we don't yet have exception safety. It would be better if we could guarantee that the "swap" part was a no-throw operation - a non-throwing swap operation is at the heart of writing exception-safe code.

Key observation: copying pointers can't throw.

Solution: use the PImpl idiom. 
```
class CImpl{
    A a;
    B b;
};
class C{
    unique_ptr<CImpl> pImpl;
  public:
    void f() {
        auto temp = make_unique<CImpl>(*pImpl);
        temp->a.g();
        temp->b.h();
        std::swap(pImpl, temp); //no-throw
    }
}
```
On the other hand, if either `A::g` or `B::h` offer no exception safety guarantee, then in general, neither can `C::f`. Similarly, if they have non-local side effects, it is much harder to offer a guarantee for `C::f`.

## Exception Safety and STL Vectors
Vectors
- Encapsulate a heap-allocated array
- Follow RAII: when a stack-allocated vector goese out of scope, the internal heap-allocated array is freed.
```
void f() {
    vector<myClass> v;
} 
// v goes out of scope, its array is freed, so the myClass objects stored in that array are destroyed and their destructors are run.
```
But
```
void g() {
    vector<myClass *> v;
    v.emplace_back(new myClass());
}
// v's destructor frees its array, the pointers in it are now gone without being freed, making a memory leak.
```
So if you needed to free those pointers, you would need to do it yourself. e.g. 
```
for(auto &p: v) delete p;
```
Alternatively
```
void h() {
    vector<unique_ptr<myclass>> v;
    ...
}
// Array is freed, unique_ptrs within destroyed and their destrcutors free the myclass objects.
```
Consider how `vector<T>::emplace_back` (or `push_back`) works:
- Offers the strong guarantee
- If the array is full (i.e. size == cap),
    - Allocates new larger array
        - Adds the new element to this array
        - Copys the objects over from old (copy constructor)
            - If the copy constructor throws
                - Destroy new larger array
                - Old array still intact
            - Strong guarantee
        - Delete old array
BUT - copying is expensive if the old array is just going to be thrown away. Furthermore, how can we create a vector of `unique_ptr`s if they can't be copied?

Wouldn't moving objects from the array to the new array be more efficient?
- Allocate new larger array
- Place new object in it
- Move the objects over using the move constructor
- Delete the old array

The problem if we move: if the move constructor throws, then `vector<T>::emplace_back` can no longer offer the strong guarantee, because the old array is no longer intact. But, `emplace_back` promises the strong guarantee.

Therefore, if the move constructor offers the no-throw guarantee, `emplace_back` will use the move constructor. Otherwise, it will use the copy constructor which may be slower.

So, your move operations should provide the no-throw guarantee if possible, and you should indicate that they do. (If your function is marked as `noexcept` and throws an exception, the program immediately terminates)
```
class MyClass {
  public:
    MyClass(MyClass && other) noexcept {...}
    MyClass &operator=(MyClass && other) noexcept {...}
};
```
If you know a function will never throw or propogate an exception, declare it `noexcept`. This facilitates optimization. At a minimum your class's swap and move operations should be non-throwing. 
