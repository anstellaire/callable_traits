# Callable Traits

Add `callable_traits` info helpers that allow querying various information about any callable object:

- `enn::callable_traits<T>::type` - type of the callable
- `enn::callable_traits<T>::arg_types` - tuple of arguments
- `enn::callable_traits<T>::arg_num` - number of arguments
- `enn::callable_traits<T>::arg<N>::type` - type of the N-th argument
- `enn::callable_traits<T>::return_type` - type of the return value

Examples with callable functor or lambda:

```c++
struct callable_const_cls {
    void operator()(int) const {}
};

struct callable_cls {
    void operator()(int) {}
};

int main() {
    auto lambda = [](int x) -> float { return x; };
    auto lambda_mut = [](int x) mutable -> float { return x; };

    static_assert(
        enn::callable_traits<callable_const_cls>::type
        == enn::callable_type::CONST_CALLABLE_OBJECT, "FAIL"); // PASS
    static_assert(
        enn::callable_traits<callable_cls>::type
        == enn::callable_type::CALLABLE_OBJECT, "FAIL"); // PASS
    static_assert(
        enn::callable_traits<decltype(lambda)>::type
        == enn::callable_type::CONST_CALLABLE_OBJECT, "FAIL"); // PASS
    static_assert(
        enn::callable_traits<decltype(lambda_mut)>::type
        == enn::callable_type::CALLABLE_OBJECT, "FAIL"); // PASS
}
```

Examples with class methods:

```c++
struct noncallable_cls {
    void foo_const() const {}
    void foo() {}

    static void foo_static() {}
};

int main() {
    static_assert(
        enn::callable_traits<decltype(&noncallable_cls::foo_const)>::type
        == enn::callable_type::CONST_METHOD_POINTER, "FAIL"); // PASS
    static_assert(
        enn::callable_traits<decltype(&noncallable_cls::foo)>::type
        == enn::callable_type::METHOD_POINTER, "FAIL"); // PASS

    static_assert(
        enn::callable_traits<decltype(&noncallable_cls::foo_static)>::type
        == enn::callable_type::FUNCTION_POINTER, "FAIL"); // PASS
    static_assert(
        enn::callable_traits<decltype(noncallable_cls::foo_static)>::type
        == enn::callable_type::FUNCTION, "FAIL"); // PASS
}
```

Examples with plain functions:

```c++
void foo(int) { }

int main() {
    static_assert(
        enn::callable_traits<decltype(&foo)>::type
        == enn::callable_type::FUNCTION_POINTER, "FAIL"); // PASS
    static_assert(
        enn::callable_traits<decltype(foo)>::type
        == enn::callable_type::FUNCTION, "FAIL"); // PASS
}
```
